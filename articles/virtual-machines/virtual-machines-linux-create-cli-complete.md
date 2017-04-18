
<properties
   pageTitle="Criar um ambiente completo do Linux utilizando o clip do Azure | Microsoft Azure"
   description="Crie armazenamento, uma VM Linux, uma rede virtual e sub-rede, um balanceador de carga, uma imagem, um endereço IP público e um grupo de segurança de rede, tudo a partir de aprofundadamente utilizando o clip do Azure."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="iainfoulds"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="10/24/2016"
   ms.author="iainfou"/>

# <a name="create-a-complete-linux-environment-by-using-the-azure-cli"></a>Criar um ambiente completo do Linux utilizando o clip do Azure

Neste artigo, criamos uma rede simples com um balanceador de carga e um par de VMs são úteis para o desenvolvimento e a computação simples. Iremos guiá através do processo ao comando command, até ter duas VMs de Linux útil, segura ao qual pode ligar a partir de qualquer lugar na Internet. Em seguida, pode mover as redes e ambientes mais complexos.

Ao longo da forma, pode obter informações sobre a hierarquia de dependência que o modelo de implementação do Gestor de recursos fornece-lhe e fornece informações sobre a quantidade de energia-lo. Quando vir a forma como o sistema é criado, pode reconstruir-muito mais rapidamente utilizando o [Gestor de recursos do Azure modelos](../resource-group-authoring-templates.md). Além disso, depois de obter informações sobre como as partes do seu ambiente ajustarem de criação de modelos para automatizá-los torna-se mais fácil.

Contém o ambiente:

- Duas VMs dentro de um conjunto de disponibilidade.
- Um balanceador de carga com uma regra de balanceamento de carga na porta 80.
- As regras de grupo (NSG) de segurança para proteger a sua VM de tráfego indesejado de rede.

![Descrição geral do ambiente básico](./media/virtual-machines-linux-create-cli-complete/environment_overview.png)

Para criar este ambiente personalizado, terá do mais recente [Azure clip](../xplat-cli-install.md) no modo de Gestor de recursos (`azure config mode arm`). Também precisa de uma ferramenta de análise de JSON. Este exemplo utiliza [jq](https://stedolan.github.io/jq/).

## <a name="quick-commands"></a>Comandos rápidos
Se precisa de realizar rapidamente a tarefa, os seguintes detalhes de secção a base para os comandos para carregar uma VM para Azure. Mais informações e contexto detalhados para cada passo pode ser encontrado do resto do documento, inicial [aqui](#detailed-walkthrough).

Certifique-se de que tem [O clip do Azure](../xplat-cli-install.md) sessão iniciada e utilizar o modo de Gestor de recursos:

```bash
azure config mode arm
```

Nos exemplos seguintes, substitua os nomes dos parâmetros de exemplo com os seus próprios valores. Os nomes dos parâmetros de exemplo incluem `myResourceGroup`, `mystorageaccount`, e `myVM`.

Crie o grupo de recursos. O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` na `westeurope` localização:

```bash
azure group create -n myResourceGroup -l westeurope
```

Verifique se o grupo de recursos ao utilizar o analisador JSON:

```bash
azure group show myResourceGroup --json | jq '.'
```

Crie a conta de armazenamento. O exemplo seguinte cria uma conta de armazenamento denominada `mystorageaccount` (o nome da conta de armazenamento tem de ser exclusivo, por isso, forneça o seu próprio nome exclusivo):

```bash
azure storage account create -g myResourceGroup -l westeurope \
  --kind Storage --sku-name GRS mystorageaccount
```

Verifique se a conta de armazenamento ao utilizar o analisador JSON:

```bash
azure storage account show -g myResourceGroup mystorageaccount --json | jq '.'
```

Crie a rede virtual. O exemplo seguinte cria uma rede virtual denominada `myVnet`:

```bash
azure network vnet create -g myResourceGroup -l westeurope\
  -n myVnet -a 192.168.0.0/16
```

Crie uma sub-rede. O exemplo seguinte cria uma sub-rede denominada `mySubnet`"

```bash
azure network vnet subnet create -g myResourceGroup \
  -e myVnet -n mySubnet -a 192.168.1.0/24
```

Verifique se a rede virtual e sub-rede ao utilizar o analisador JSON:


```bash
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Crie um endereço IP público. O exemplo seguinte cria um endereço IP público com o nome `myPublicIP` com o nome de DNS `mypublicdns` (o nome de DNS tem de ser exclusivo, por isso, forneça o seu próprio nome exclusivo):

```bash
azure network public-ip create -g myResourceGroup -l westeurope \
  -n myPublicIP  -d mypublicdns -a static -i 4
```

Crie o Balanceador de carga. O exemplo seguinte cria um balanceador de carga denominado `myLoadBalancer`:

```bash
azure network lb create -g myResourceGroup -l westeurope -n myLoadBalancer
```

Criar um conjunto IP Front-end para o Balanceador de carga e associar o endereço IP público. O exemplo seguinte cria um conjunto IP Front-end denominado `mySubnetPool`:

```bash
azure network lb frontend-ip create -g myResourceGroup -l myLoadBalancer \
  -i myPublicIP -n myFrontEndPool 
```

Crie o conjunto de IP back-end para o Balanceador de carga. O exemplo seguinte cria um conjunto IP de back-end denominado `myBackEndPool`:

```bash
azure network lb address-pool create -g myResourceGroup -l myLoadBalancer \
  -n myBackEndPool
```

Crie regras de tradução (NAT) de endereço para o Balanceador de carga de rede de entrada SSH. O exemplo seguinte cria duas regras de Balanceador de carga, `myLoadBalancerRuleSSH1` e `myLoadBalancerRuleSSH2`:

```bash
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH1 -p tcp -f 4222 -b 22
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH2 -p tcp -f 4223 -b 22
```

Criar web NAT regras para o Balanceador de carga de entrada. O exemplo seguinte cria uma regra de Balanceador de carga com nome`myLoadBalancerRuleWeb`

```bash
azure network lb rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleWeb -p tcp -f 80 -b 80 \
  -t myFrontEndPool -o myBackEndPool
```

Crie a sonda de estado de funcionamento do Balanceador de carga. O exemplo seguinte cria uma sonda TCP denominada `myHealthProbe`:

```bash
azure network lb probe create -g myResourceGroup -l myLoadBalancer \
  -n myHealthProbe -p "tcp" -i 15 -c 4
```

Verifique se o Balanceador de carga, conjuntos de IP e regras de NAT ao utilizar o analisador JSON:

```bash
azure network lb show -g myResourceGroup -n myLoadBalancer --json | jq '.'
```

Crie o primeiro cartão de interface de rede (NIC). Substituir o `#####-###-###` secções com o seu próprio ID Azure subscrição. A sua subscrição ID é indicado na saída do `jq` quando examinar os recursos que está a criar. Também pode ver o seu ID da subscrição com `azure account list`. 

O exemplo seguinte cria uma NIC denominada `myNic1`:

```bash
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic1 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
```

Criar a segunda NIC. O exemplo seguinte cria uma NIC denominada `myNic2`:

```bash
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic2 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
```

Verifique se as duas NIC ao utilizar o analisador JSON:

```bash
azure network nic show myResourceGroup myNic1 --json | jq '.'
azure network nic show myResourceGroup myNic2 --json | jq '.'
```

Crie o grupo de segurança de rede. O exemplo seguinte cria um grupo de segurança de rede com o nome `myNetworkSecurityGroup`:

```bash
azure network nsg create -g myResourceGroup -l westeurope \
  -n myNetworkSecurityGroup
```

Adicione regras de entrada duas para o grupo de segurança de rede. O exemplo seguinte cria duas regras, `myNetworkSecurityGroupRuleSSH` e `myNetworkSecurityGroupRuleHTTP`:

```bash
azure network nsg rule create -p tcp -r inbound -y 1000 -u 22 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleSSH
azure network nsg rule create -p tcp -r inbound -y 1001 -u 80 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleHTTP
```

Verifique se o grupo de segurança de rede e as regras de entrada ao utilizar o analisador JSON:

```bash
azure network nsg show -g myResourceGroup -n myNetworkSecurityGroup --json | jq '.'
```

Vincular o grupo de segurança de rede para duas NIC:

```bash
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic1
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic2
```

Crie o conjunto de disponibilidade. O exemplo seguinte cria uma disponibilidade conjunto nomeada `myAvailabilitySet`:

```bash
azure availset create -g myResourceGroup -l westeurope -n myAvailabilitySet
```

Crie a primeira VM de Linux. O exemplo seguinte cria uma VM denominada `myVM1`:

```bash
azure vm create \
    --resource-group myResourceGroup \
    --name myVM1 \
    --location westeurope \
    --os-type linux \
    --availset-name myAvailabilitySet \
    --nic-name myNic1 \
    --vnet-name myVnet \
    --vnet-subnet-name mySubnet \
    --storage-account-name mystorageaccount \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

Crie a segunda VM de Linux. O exemplo seguinte cria uma VM denominada `myVM2`:

```bash
azure vm create \
    --resource-group myResourceGroup \
    --name myVM2 \
    --location westeurope \
    --os-type linux \
    --availset-name myAvailabilitySet \
    --nic-name myNic2 \
    --vnet-name myVnet \
    --vnet-subnet-name mySubnet \
    --storage-account-name mystorageaccount \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

Utilize o analisador JSON para confirmar que tudo o que foi criado:

```bash
azure vm show -g myResourceGroup -n myVM1 --json | jq '.'
azure vm show -g myResourceGroup -n myVM2 --json | jq '.'
```

Exporte o seu novo ambiente a um modelo para rapidamente criar novas instâncias:

```bash
azure group export myResourceGroup
```

## <a name="detailed-walkthrough"></a>Obter instruções detalhadas sobre
Os passos detalhados que se seguem explicam o que cada comando está a fazer como construir o seu ambiente. Estes conceitos são úteis quando criar o seus próprio ambientes personalizados para o desenvolvimento ou de produção.

Certifique-se de que tem [O clip do Azure](../xplat-cli-install.md) sessão iniciada e utilizar o modo de Gestor de recursos:

```bash
azure config mode arm
```

Nos exemplos seguintes, substitua os nomes dos parâmetros de exemplo com os seus próprios valores. Os nomes dos parâmetros de exemplo incluem `myResourceGroup`, `mystorageaccount`, e `myVM`.

## <a name="create-resource-groups-and-choose-deployment-locations"></a>Criar grupos de recursos e selecione localizações de implementação

Grupos de recursos Azure são entidades de implementação de lógica que contêm as informações de configuração e metadados para ativar a gestão de lógica de implementações do recurso. O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` na `westeurope` localização:

```bash
azure group create --name myResourceGroup --location westeurope
```

Resultado:

```bash                        
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westeurope
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Precisa de contas de armazenamento para os seus discos VM e para discos quaisquer dados adicionais que pretende adicionar. Criar contas de armazenamento quase imediatamente depois de criar grupos de recursos.

Aqui utilizamos a `azure storage account create` comando, prisma a localização da conta, o grupo de recursos que controla e o tipo de suporte de armazenamento que pretende. O exemplo seguinte cria uma conta de armazenamento denominada `mystorageaccount`:

```bash
azure storage account create \  
  --location westeurope \
  --resource-group myResourceGroup \
  --kind Storage --sku-name GRS \
  mystorageaccount
```

Resultado:

```bash
info:    Executing command storage account create
+ Creating storage account
info:    storage account create command OK
```

Examinar a nossa grupo de recursos utilizando o `azure group show` comando, vamos utilizar a ferramenta de [jq](https://stedolan.github.io/jq/) juntamente com o `--json` opção clip Azure. (Pode utilizar **jsawk** ou de biblioteca de idioma que preferir para analisar o JSON.)

```bash
azure group show myResourceGroup --json | jq '.'
```

Resultado:

```bash
{
  "tags": {},
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "name": "myResourceGroup",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
      "name": "mystorageaccount",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

Para saber qual a conta de armazenamento utilizando o clip, tem primeiro de definir os nomes de contas e chaves. Substitua o nome da conta de armazenamento no seguinte exemplo com um nome que escolher:

```
AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show mystorageaccount --resource-group myResourceGroup --json | jq -r '.string')"
```

Em seguida, é possível visualizar facilmente as informações de armazenamento:

```bash
azure storage container list
```

Resultado:

```bash
info:    Executing command storage container list
+ Getting storage containers
data:    Name  Public-Access  Last-Modified
data:    ----  -------------  -----------------------------
data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
info:    storage container list command OK
```

## <a name="create-a-virtual-network-and-subnet"></a>Criar uma rede virtual e sub-rede

Seguinte que vai necessárias para criar uma rede virtual em execução no Azure e uma sub-rede na qual pode criar o seu VMs. O exemplo seguinte cria uma rede virtual denominada `myVnet` com o `192.168.0.0/16` prefixo de endereço:

```bash
azure network vnet create --resource-group myResourceGroup --location westeurope \
  --name myVnet --address-prefixes 192.168.0.0/16 
```

Resultado:

```bash
info:    Executing command network vnet create
+ Looking up virtual network "myVnet"
+ Creating virtual network "myVnet"
+ Loading virtual network state
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet
data:    Name                            : myVnet
data:    Type                            : Microsoft.Network/virtualNetworks
data:    Location                        : westeurope
data:    ProvisioningState               : Succeeded
data:    Address prefixes:
data:      192.168.0.0/16
info:    network vnet create command OK
```

Novamente, vamos utilizar a opção – json de `azure group show` e **jq** para ver como podemos estiver a compilar nossos recursos. Agora temos uma `storageAccounts` recurso e um `virtualNetworks` recurso.  

```bash
azure group show myResourceGroup --json | jq '.'
```

Resultado:

```bash
{
  "tags": {},
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "name": "myResourceGroup",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
      "name": "myVnet",
      "type": "virtualNetworks",
      "location": "westeurope",
      "tags": null
    },
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
      "name": "mystorageaccount",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

Agora vamos criar uma sub-rede na `myVnet` rede virtual no qual os VMs são implementadas. Utilizamos os `azure network vnet subnet create` comando, juntamente com os recursos já criámos: o `myResourceGroup` grupo de recursos e o `myVnet` rede virtual. No seguinte exemplo, vamos adicionar sub-rede denominada `mySubnet` com o prefixo de endereço sub-rede `192.168.1.0/24`:

```bash
azure network vnet subnet create --resource-group myResourceGroup \
  --vnet-name myVnet --name mySubnet --address-prefix 192.168.1.0/24
```

Resultado:

```bash
info:    Executing command network vnet subnet create
+ Looking up the subnet "mySubnet"
+ Creating subnet "mySubnet"
+ Looking up the subnet "mySubnet"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet
data:    Type                            : Microsoft.Network/virtualNetworks/subnets
data:    ProvisioningState               : Succeeded
data:    Name                            : mySubnet
data:    Address prefix                  : 192.168.1.0/24
data:
info:    network vnet subnet create command OK
```

Uma vez que a sub-rede logicamente dentro da rede virtual, podemos procure as informações de sub-rede com um comando ligeiramente diferente. O comando utilizamos está `azure network vnet show`, mas podemos continuar examinar a saída JSON utilizando **jq**.

```bash
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Resultado:

```bash
{
  "subnets": [
    {
      "ipConfigurations": [],
      "addressPrefix": "192.168.1.0/24",
      "provisioningState": "Succeeded",
      "name": "mySubnet",
      "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
    }
  ],
  "tags": {},
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "provisioningState": "Succeeded",
  "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "name": "myVnet",
  "location": "westeurope"
}
```

## <a name="create-a-public-ip-address-pip"></a>Criar um endereço IP público (PIP)

Agora vamos criar o endereço IP público (PIP) podemos atribuir a sua Balanceador de carga. Permite-lhe para se ligar à sua VMs da Internet utilizando o `azure network public-ip create` comando. Uma vez que o endereço predefinido é dinâmico, criamos uma entrada de DNS com nome no domínio de **cloudapp.azure.com** utilizando o `--domain-name-label` opção. O exemplo seguinte cria um endereço IP público com o nome `myPublicIP` com o nome de DNS `mypublicdns`. Como o nome de DNS tem de ser exclusivo, forneça o seu próprio nome DNS exclusivo:

```bash
azure network public-ip create --resource-group myResourceGroup \
  --location westeurope --name myPublicIP --domain-name-label mypublicdns
```

Resultado:

```bash
info:    Executing command network public-ip create
+ Looking up the public ip "myPublicIP"
+ Creating public ip address "myPublicIP"
+ Looking up the public ip "myPublicIP"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP
data:    Name                            : myPublicIP
data:    Type                            : Microsoft.Network/publicIPAddresses
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Allocation method               : Dynamic
data:    Idle timeout                    : 4
data:    Domain name label               : mypublicdns
data:    FQDN                            : mypublicdns.westeurope.cloudapp.azure.com
info:    network public-ip create command OK
```

O endereço IP público também é um recurso de nível superior, para que possa ver-os com `azure group show`.

```bash
azure group show myResourceGroup --json | jq '.'
```

Resultado:

```bash
{
"tags": {},
"id": "/subscriptions/guid/resourceGroups/myResourceGroup",
"name": "myResourceGroup",
"provisioningState": "Succeeded",
"location": "westeurope",
"properties": {
    "provisioningState": "Succeeded"
},
"resources": [
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
    "name": "myPublicIP",
    "type": "publicIPAddresses",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
    "name": "myVnet",
    "type": "virtualNetworks",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "name": "mystorageaccount",
    "type": "storageAccounts",
    "location": "westeurope",
    "tags": null
    }
],
"permissions": [
    {
    "actions": [
        "*"
    ],
    "notActions": []
    }
]
}
```

Pode investigar obter mais detalhes de recursos, incluindo o nome de domínio completamente qualificado (FQDN) do subdomínio, utilizando o concluído `azure network public-ip show` comando. O recurso de endereço IP público lhe tenha sido atribuído logicamente, mas ainda não foram atribuído um endereço específico. Para obter um endereço IP, que vai precisar de um balanceador de carga, podemos ainda não tiver criado.

```bash
azure network public-ip show myResourceGroup myPublicIP --json | jq '.'
```

Resultado:

```bash
{
"tags": {},
"publicIpAllocationMethod": "Dynamic",
"dnsSettings": {
    "domainNameLabel": "mypublicdns",
    "fqdn": "mypublicdns.westeurope.cloudapp.azure.com"
},
"idleTimeoutInMinutes": 4,
"provisioningState": "Succeeded",
"etag": "W/\"c63154b3-1130-49b9-a887-877d74d5ebc5\"",
"id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
"name": "myPublicIP",
"location": "westeurope"
}
```

## <a name="create-a-load-balancer-and-ip-pools"></a>Criar um balanceador de carga e conjuntos de IP
Quando cria um balanceador de carga, permite-lhe distribuir o tráfego entre vários VMs. Também fornece redundância à aplicação ao executar várias VMs que respondem a pedidos de utilizador em caso de manutenção ou cargas muito carregadas. O exemplo seguinte cria um balanceador de carga denominado `myLoadBalancer`:

```bash
azure network lb create --resource-group myResourceGroup --location westeurope \
  --name myLoadBalancer
```

Resultado:

```bash
info:    Executing command network lb create
+ Looking up the load balancer "myLoadBalancer"
+ Creating load balancer "myLoadBalancer"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer
data:    Name                            : myLoadBalancer
data:    Type                            : Microsoft.Network/loadBalancers
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
info:    network lb create command OK
```

Os nossos Balanceador de carga é bastante vazio, por isso, vamos criar algumas conjuntos de IP. Pretendemos criar dois conjuntos IP para os nossos Balanceador de carga - um para o front-end e outra para o back-end. O conjunto de IP Front-end está visível publicamente. Também é a localização para o qual podemos atribuir o PIP que criámos anterior. Em seguida, utilizamos o agrupamento de back-end como uma localização para os nossos VMs para ligar a. Desta forma, o tráfego pode fluir através do Balanceador de carga para os VMs.

Primeiro, vamos criar nosso conjunto IP Front-end. O exemplo seguinte cria um conjunto de dados front-end denominado `myFrontEndPool`:

```bash
azure network lb frontend-ip create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --public-ip-name myPublicIP \
  --name myFrontEndPool 
```

Resultado:

```bash
info:    Executing command network lb frontend-ip create
+ Looking up the load balancer "myLoadBalancer"
+ Looking up the public ip "myPublicIP"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myFrontEndPool
data:    Provisioning state              : Succeeded
data:    Private IP allocation method    : Dynamic
data:    Public IP address id            : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP
info:    network lb mySubnet-ip create command OK
```

Tenha em atenção como utilizámos o `--public-ip-name` mudar para a passar o `myPublicIP` que criámos anterior. Atribuir o endereço IP público para o Balanceador de carga permite-lhe alcançar os VMs pela Internet.

Em seguida, vamos criar nosso segundo conjunto IP, desta vez para o nosso tráfego de back-end. O exemplo seguinte cria um conjunto de dados back-end denominado `myBackEndPool`:

```bash
azure network lb address-pool create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myBackEndPool
```

Resultado:

```bash
info:    Executing command network lb address-pool create
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myBackEndPool
data:    Provisioning state              : Succeeded
info:    network lb address-pool create command OK
```

É possível ver como os nossos Balanceador de carga está a fazer procurando com `azure network lb show` e examinar a saída JSON:

```bash
azure network lb show myResourceGroup myLoadBalancer --json | jq '.'
```

Resultado:

```bash
{
  "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "name": "myLoadBalancer",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "mySubnetIPConfigurations": [
    {
      "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
      "name": "myFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
      "name": "myBackEndPool",
      "provisioningState": "Succeeded",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
    }
  ],
  "loadBalancingRules": [],
  "probes": []
}
```

## <a name="create-load-balancer-nat-rules"></a>Criar Balanceador de carga NAT regras
Para obter o tráfego a fluir através da nossa Balanceador de carga, precisamos de criar regras de tradução (NAT) que especifiquem as ações de entrada ou saídas de endereço de rede. Pode especificar o protocolo a utilizar, em seguida, mapear portas externas para portas internas conforme pretender. Para o nosso ambiente, vamos criar algumas regras que permitam SSH através da nossa Balanceador de carga a nossa VMs. Vamos configurar as portas TCP 4222 e 4223 para direcionar à porta TCP 22 no nossos VMs (o que mais tarde criamos). O exemplo seguinte cria uma regra com o nome `myLoadBalancerRuleSSH1` para mapear portas TCP 4222 à porta 22:

```bash
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH1 \
  --protocol tcp --frontend-port 4222 --backend-port 22
```

Resultado:

```bash
info:    Executing command network lb inbound-nat-rule create
+ Looking up the load balancer "myLoadBalancer"
warn:    Using default enable floating ip: false
warn:    Using default idle timeout: 4
warn:    Using default mySubnet IP configuration "myFrontEndPool"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myLoadBalancerRuleSSH1
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    mySubnet port                   : 4222
data:    Backend port                    : 22
data:    Enable floating IP              : false
data:    Idle timeout in minutes         : 4
data:    mySubnet IP configuration id    : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool
info:    network lb inbound-nat-rule create command OK
```

Repita o procedimento para a sua segunda regra NAT para SSH. O exemplo seguinte cria uma regra com o nome `myLoadBalancerRuleSSH2` para mapear portas TCP 4223 à porta 22:

```bash
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22
```

Vamos também prosseguir e criar uma regra NAT para porta TCP 80 para o tráfego da web, ao ligar a regra por excesso para o nossos conjuntos de IP. Se podemos ligar a regra ao agrupamento IP, em vez de ao ligar a regra para os nossos VMs individualmente, podemos pode adicionar ou remover VMs do conjunto de IP. O Balanceador de carga é ajustado automaticamente o fluxo de tráfego. O exemplo seguinte cria uma regra com o nome `myLoadBalancerRuleWeb` para mapear porta TCP 80 à porta 80:

```bash
azure network lb rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleWeb --protocol tcp \
  --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEndPool \
  --backend-address-pool-name myBackEndPool
```

Resultado:

```bash
info:    Executing command network lb rule create
+ Looking up the load balancer "myLoadBalancer"
warn:    Using default idle timeout: 4
warn:    Using default enable floating ip: false
warn:    Using default load distribution: Default
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myLoadBalancerRuleWeb
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    mySubnet port                   : 80
data:    Backend port                    : 80
data:    Enable floating IP              : false
data:    Load distribution               : Default
data:    Idle timeout in minutes         : 4
data:    mySubnet IP configuration id    : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool
data:    Backend address pool id         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool
info:    network lb rule create command OK
```

## <a name="create-a-load-balancer-health-probe"></a>Criar uma sonda de estado de funcionamento do Balanceador de carga

Um Estado de funcionamento periodicamente sonda verificações nas VMs que estão por detrás nosso Balanceador de carga para se certificar de que são operativo e responder a pedidos, tal como foi definido. Caso contrário, está a ser removidos de operação para garantir que os utilizadores não estão a ser direccionados aos mesmos. Pode definir verificações personalizadas para a sonda de estado de funcionamento, juntamente com intervalos e valores de tempo limite. Para mais informações sobre o estado de funcionamento sondas, consulte o artigo [Balanceador de carga sondas](../load-balancer/load-balancer-custom-probe-overview.md). O exemplo seguinte cria uma TCP estado de funcionamento do pesquisada com nome `myHealthProbe`:

```bash
azure network lb probe create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myHealthProbe --protocol "tcp" \
  --interval 15 --count 4
```

Resultado:

```bash
info:    Executing command network lb probe create
warn:    Using default probe port: 80
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myHealthProbe
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    Port                            : 80
data:    Interval in seconds             : 15
data:    Number of probes                : 4
info:    network lb probe create command OK
```

Aqui, podemos especificado um intervalo de 15 segundos para os nossos verificações do Estado de funcionamento. Vamos pode perder um máximo de quatro sondas (um minuto) antes do Balanceador de carga considera que o anfitrião já não está a funcionar.

## <a name="verify-the-load-balancer"></a>Verifique se o Balanceador de carga
Agora a configuração do Balanceador de carga está concluída. Eis os passos que seguiu:

1. Criada pela primeira vez um balanceador de carga.
2. Em seguida, criou um conjunto IP Front-end e atribuído um endereço IP público à mesma.
3. Seguinte que criou um conjunto IP de back-end VMs podem ligar a.
4. Após esta ação, o utilizador criou regras NAT que permitam SSH para VMs para gestão de, juntamente com uma regra que permita porta TCP 80 para o nosso web app.
5. Por fim adicionou uma sonda de estado de funcionamento para verificar periodicamente as VMs. Este sonda de estado de funcionamento assegura que os utilizadores não tentarem aceder a uma VM que já não está a funcionar ou conteúdo de funcionar.

Analisemos o seu Balanceador de carga aspeto agora:

```bash
azure network lb show --resource-group myResourceGroup \
  --name myLoadBalancer --json | jq '.'
```

Resultado:

```bash
{
  "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleSSH1",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "protocol": "Tcp",
      "mySubnetPort": 4222,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    },
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleSSH2",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "protocol": "Tcp",
      "mySubnetPort": 4223,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    }
  ],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "name": "myLoadBalancer",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "mySubnetIPConfigurations": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb"
        }
      ],
      "inboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
        },
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myBackEndPool",
      "provisioningState": "Succeeded",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
    }
  ],
  "loadBalancingRules": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleWeb",
      "provisioningState": "Succeeded",
      "enableFloatingIP": false,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "backendAddressPool": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
      },
      "protocol": "Tcp",
      "loadDistribution": "Default",
      "mySubnetPort": 80,
      "backendPort": 80,
      "idleTimeoutInMinutes": 4
    }
  ],
  "probes": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myHealthProbe",
      "provisioningState": "Succeeded",
      "numberOfProbes": 4,
      "intervalInSeconds": 15,
      "port": 80,
      "protocol": "Tcp",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/probes/myHealthProbe"
    }
  ]
}
```

## <a name="create-an-nic-to-use-with-the-linux-vm"></a>Criar uma imagem para utilizar com a VM Linux

NIC está disponível através de programação uma vez que pode aplicar regras a sua utilização. Também pode ter mais do que um. Nas seguintes `azure network nic create` comando, ver NIC ao conjunto de IP carga back-end e associá-lo a regra NAT para permitir o tráfego SSH.
 
Substituir o `#####-###-###` secções com o seu próprio ID Azure subscrição. A sua subscrição ID é indicado na saída do `jq` quando examinar os recursos que está a criar. Também pode ver o seu ID da subscrição com `azure account list`. 

O exemplo seguinte cria uma NIC denominada `myNic1`:

```bash
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic1 \
  --lb-address-pool-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
```

Resultado:

```bash
info:    Executing command network nic create
+ Looking up the subnet "mySubnet"
+ Looking up the network interface "myNic1"
+ Creating network interface "myNic1"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1
data:    Name                            : myNic1
data:    Type                            : Microsoft.Network/networkInterfaces
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Enable IP forwarding            : false
data:    IP configurations:
data:      Name                          : Nic-IP-config
data:      Provisioning state            : Succeeded
data:      Private IP address            : 192.168.1.4
data:      Private IP allocation method  : Dynamic
data:      Subnet                        : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet
data:      Load balancer backend address pools:
data:        Id                          : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool
data:      Load balancer inbound NAT rules:
data:        Id                          : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
data:
info:    network nic create command OK
```

Pode ver os detalhes por examinar diretamente ao recurso. Examinar o recurso utilizando o `azure network nic show` comando:

```bash
azure network nic show myResourceGroup myNic1 --json | jq '.'
```

Resultado:

```bash
{
  "etag": "W/\"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a\"",
  "provisioningState": "Succeeded",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1",
  "name": "myNic1",
  "type": "Microsoft.Network/networkInterfaces",
  "location": "westeurope",
  "ipConfigurations": [
    {
      "etag": "W/\"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1/ipConfigurations/Nic-IP-config",
      "loadBalancerBackendAddressPools": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
        }
      ],
      "loadBalancerInboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
        }
      ],
      "privateIPAddress": "192.168.1.4",
      "privateIPAllocationMethod": "Dynamic",
      "subnet": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
      },
      "provisioningState": "Succeeded",
      "name": "Nic-IP-config"
    }
  ],
  "dnsSettings": {
    "appliedDnsServers": [],
    "dnsServers": []
  },
  "enableIPForwarding": false,
  "resourceGuid": "a20258b8-6361-45f6-b1b4-27ffed28798c"
}
```

Agora, vamos criar a segunda NIC, ao ligar novamente no nosso conjunto IP back-end. Esta regra NAT tempo a segunda permite SSH tráfego. O exemplo seguinte cria uma NIC denominada `myNic2`:

```bash
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic2 \
  --lb-address-pool-ids  /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2
```

## <a name="create-a-network-security-group-and-rules"></a>Criar um grupo de segurança de rede e de regras

Agora Recomendamos criar um grupo de segurança de rede e as regras de entrada que regem o acesso à NIC. Um grupo de segurança de rede podem ser aplicado a uma imagem ou sub-rede. Pode definir regras para controlar o fluxo de tráfego e terminar a sua VMs. O exemplo seguinte cria um grupo de segurança de rede com o nome `myNetworkSecurityGroup`:

```bash
azure network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

Vamos adicionar a regra de entrada para NSG permitir que as ligações de entrada na porta 22 (para suportar SSH). O exemplo seguinte cria uma regra com o nome `myNetworkSecurityGroupRuleSSH` para permitir a porta TCP 22:

```bash
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1000 --destination-port-range 22 --access allow \
  --name myNetworkSecurityGroupRuleSSH
```

Agora vamos adicionar a regra de entrada para NSG permitir que as ligações de entrada na porta 80 (para suportar o tráfego de web). O exemplo seguinte cria uma regra com o nome `myNetworkSecurityGroupRuleHTTP` para permitir que porta TCP 80:

```bash
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1001 --destination-port-range 80 --access allow \
  --name myNetworkSecurityGroupRuleHTTP
```

> [AZURE.NOTE] A regra de entrada é um filtro para ligações de rede de entrada. Neste exemplo, vamos vincular o NSG NIC virtual VMs, que significa que qualquer pedido à porta 22 lhe é transmitido através de NIC no nosso VM. Esta regra entrada é sobre uma ligação de rede e não sobre um ponto final, que é o que seria sobre implementações clássica. Para abrir uma porta, tem de deixar o `--source-port-range` definida como '\*' (o valor predefinido) para aceitar pedidos de entrada a partir de **qualquer** pedir porta. Portas são normalmente dinâmicas.

## <a name="bind-to-the-nic"></a>Vincular ao NIC

Vincular o NSG para o NIC. É necessário para se ligar a nossa NIC a nossa grupo de segurança de rede. Execute ambos os comandos, para ligar ambos os nossos NIC:

```bash
azure network nic set --resource-group myResourceGroup --name myNic1 \
  --network-security-group-name myNetworkSecurityGroup
```

```bash
azure network nic set --resource-group myResourceGroup --name myNic2 \
  --network-security-group-name myNetworkSecurityGroup
```

## <a name="create-an-availability-set"></a>Criar um conjunto de disponibilidade
Disponibilidade define os VMs propagação de ajuda ao longo de falhas domínios e atualização. Vamos criar uma disponibilidade configurar para a sua VMs. O exemplo seguinte cria uma disponibilidade conjunto nomeada `myAvailabilitySet`:

```bash
azure availset create --resource-group myResourceGroup --location westeurope
  --name myAvailabilitySet
```

Domínios falhas definem um agrupamento de máquinas virtuais que partilham um parâmetro de origem e de rede power comuns. Por predefinição, as máquinas virtuais que estão configuradas no seu conjunto de disponibilidade são separadas em vários domínios de falhas até três. A ideia é que um problema de hardware num dos domínios falhas não afeta a cada VM que está a executar a sua aplicação. Azure distribui automaticamente VMs ao longo dos domínios falhas quando colocá-los num conjunto de disponibilidade.

Domínios atualização indicam grupos de máquinas virtuais e hardware físico subjacente que pode ser reiniciado ao mesmo tempo. A ordem na qual são reiniciados domínios atualização poderão não estar sequencial durante a manutenção planeada, mas apenas uma atualização for reiniciada uma vez. Novamente, Azure automaticamente distribui seu VMs na atualização domínios quando colocá-las num site do disponibilidade.

Leia mais sobre como [Gerir a disponibilidade de VMs](./virtual-machines-linux-manage-availability.md).

## <a name="create-the-linux-vms"></a>Criar as VMs Linux

Criou os recursos de armazenamento e de rede para suportar VMs Internet acessíveis. Agora vamos criar aqueles VMs e protegê-los com uma chave SSH que não tem uma palavra-passe. Neste caso, vamos criar uma Ubuntu VM com base em LTS a mais recente. Vamos localizar essas informações de imagem utilizando `azure vm image list`, conforme descrito na [Localizar Azure VM imagens](virtual-machines-linux-cli-ps-findimage.md).

Vamos selecionada uma imagem utilizando o comando `azure vm image list westeurope canonical | grep LTS`. Neste caso, utilizamos `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`. Para o último campo, passar `latest` para que no futuro, podemos aceder sempre a compilação mais recente. (A cadeia utilizamos é `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`).

Este passo seguinte é familiar para qualquer pessoa que já criou um ssh rsa públicas e privadas chave emparelhar no Linux ou Mac utilizando **ssh keygen-t rsa -b 2048**. Se não tiver qualquer pares de chave do certificado seu `~/.ssh` diretório, pode criá-las:

- Automaticamente, utilizando o `azure vm create --generate-ssh-keys` opção.
- Manualmente, utilizando [as instruções para criar sozinho](virtual-machines-linux-mac-create-ssh-keys.md).

Em alternativa, pode utilizar o `--admin-password` método para autenticar suas ligações SSH depois da VM é criada. Este método é normalmente menos seguro.

Vamos criar a VM colocando todos os nossos recursos e informações em conjunto com o `azure vm create` comando:

```bash
azure vm create \
  --resource-group myResourceGroup \
  --name myVM1 \
  --location westeurope \
  --os-type linux \
  --availset-name myAvailabilitySet \
  --nic-name myNic1 \
  --vnet-name myVnet \
  --vnet-subnet-name mySubnet \
  --storage-account-name mystorageaccount \
  --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username ops
```

Resultado:

```bash
info:    Executing command vm create
+ Looking up the VM "myVM1"
info:    Verifying the public key SSH file: /home/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account mystorageaccount
+ Looking up the availability set "myAvailabilitySet"
info:    Found an Availability set "myAvailabilitySet"
+ Looking up the NIC "myNic1"
info:    Found an existing NIC "myNic1"
info:    Found an IP configuration with virtual network subnet id "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet" in the NIC "myNic1"
info:    This is an NIC without publicIP configured
info:    The storage URI 'https://mystorageaccount.blob.core.windows.net/' will be used for boot diagnostics settings, and it can be overwritten by the parameter input of '--boot-diagnostics-storage-uri'.
info:    vm create command OK
```

Pode ligar ao seu VM imediatamente ao utilizar as teclas SSH predefinido. Certifique-se de que especifique a porta apropriada uma vez que está a podemos prisma Balanceador de carga. (Para os nossos VM primeiro, vamos configurar a NAT regra para reencaminhar porta 4222 para os nossos VM):

```bash
 ssh ops@mypublicdns.westeurope.cloudapp.azure.com -p 4222
```

Resultado:

```bash
The authenticity of host '[mypublicdns.westeurope.cloudapp.azure.com]:4222 ([xx.xx.xx.xx]:4222)' can't be established.
ECDSA key fingerprint is 94:2d:d0:ce:6b:fb:7f:ad:5b:3c:78:93:75:82:12:f9.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[mypublicdns.westeurope.cloudapp.azure.com]:4222,[xx.xx.xx.xx]:4222' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.1 LTS (GNU/Linux 4.4.0-34-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

ops@myVM1:~$
```

Ir para a frente e crie a segunda VM da mesma maneira:

```bash
azure vm create \
  --resource-group myResourceGroup \
  --name myVM2 \
  --location westeurope \
  --os-type linux \
  --availset-name myAvailabilitySet \
  --nic-name myNic2 \
  --vnet-name myVnet \
  --vnet-subnet-name mySubnet \
  --storage-account-name mystorageaccount \
  --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username ops
```

E agora pode usar o `azure vm show myResourceGroup myVM1` comando examinar o que criou. Neste momento, está a executar o VMs Ubuntu atrás de um balanceador de carga no Azure pode iniciar sessão para apenas com o seu par de chaves SSH (uma vez que as palavras-passe estão desativadas). Pode instalar o nginx ou httpd, implementar uma aplicação web e ver o tráfego fluxo através do Balanceador de carga para ambas as VMs.

```bash
azure vm show --resource-group myResourceGroup --name myVM1
```

Resultado:

```bash
info:    Executing command vm show
+ Looking up the VM "TestVM1"
+ Looking up the NIC "myNic1"
data:    Id                              :/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM1
data:    ProvisioningState               :Succeeded
data:    Name                            :myVM1
data:    Location                        :westeurope
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :16.04.0-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clib45a8b650f4428a1-os-1471973896525
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://mystorageaccount.blob.core.windows.net/vhds/clib45a8b650f4428a1-os-1471973896525.vhd
data:
data:    OS Profile:
data:      Computer Name                 :myVM1
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-24-D4-AA
data:          Provisioning State        :Succeeded
data:          Name                      :LmyNic1
data:          Location                  :westeurope
data:
data:    AvailabilitySet:
data:      Id                            :/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/availabilitySets/myAvailabilitySet
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://mystorageaccount.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm show command OK

```


## <a name="export-the-environment-as-a-template"></a>Exportar o ambiente como um modelo
Agora que já tiver criado saída este ambiente, o que acontece se pretende criar um ambiente de desenvolvimento adicionais com os mesmos parâmetros ou num ambiente de produção que corresponde ao-lo? Gestor de recursos utiliza modelos JSON que definem todos os parâmetros para o seu ambiente. Construir ambientes inteiras ao referenciar este modelo JSON. Pode [criar modelos JSON manualmente](../resource-group-authoring-templates.md) ou exportar um ambiente existente para criar o modelo JSON para si:

```bash
azure group export --name myResourceGroup
```

Este comando cria o `myResourceGroup.json` ficheiro no seu diretório de trabalho atual. Quando cria um ambiente deste modelo, lhe for pedido para todos os nomes de recursos, incluindo os nomes de Balanceador de carga, interfaces de rede ou VMs. Pode preencher estes nomes no seu ficheiro de modelo ao adicionar o `-p` ou `--includeParameterDefaultValue` parâmetro para a `azure group export` comando que foi apresentado anterior. Edite o seu modelo JSON para especificar os nomes de recursos ou [criar um ficheiro de parameters.json](../resource-group-authoring-templates.md#parameters) que especifica os nomes dos recursos.

Para criar um ambiente do seu modelo:

```bash
azure group deployment create --resource-group myNewResourceGroup \
  --template-file myResourceGroup.json
```

Poderá pretender [obter mais informações sobre como implementar a partir de modelos](../resource-group-template-deploy-cli.md)de leitura. Saiba mais sobre como atualizar ambientes incrementada, utilize o ficheiro de parâmetros e aceder a modelos de uma localização de armazenamento única.

## <a name="next-steps"></a>Próximos passos

Agora está pronto para começar a trabalhar com vários componentes de rede e VMs. Pode utilizar este ambiente de exemplo para construir a sua aplicação utilizando os componentes principais introduzidos aqui.
