<properties
   pageTitle="Criar uma VM Linux com vários NIC | Microsoft Azure"
   description="Saiba como criar uma VM Linux com vários NIC anexada utilizando os modelos de Azure clip ou Gestor de recursos."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="10/27/2016"
   ms.author="iainfou"/>

# <a name="creating-a-linux-vm-with-multiple-nics"></a>Criar uma VM Linux com vários NIC
Pode criar uma máquina de virtual (VM) no Azure que tenha várias interfaces de rede virtual (NIC) anexadas ao mesmo. Um cenário comum seria ter sub-redes diferentes para conectividade front-end e back-end ou uma rede dedicada a uma solução de cópia de segurança ou monitorização. Este artigo fornece comandos rápidos para criar uma VM com vários NIC anexada. Para obter informações detalhadas, incluindo como criar várias NIC dentro de scripts sua própria festa, saiba mais sobre como [Implementar multi-NIC VMs](../virtual-network/virtual-network-deploy-multinic-arm-cli.md). Diferentes [tamanhos VM](virtual-machines-linux-sizes.md) suportar um número variado de NIC, por isso, o tamanho do seu VM em conformidade.

>[AZURE.WARNING] Tem de anexar NIC vários quando cria uma VM - não é possível adicionar NIC para uma VM existente. Pode [criar uma VM com base em de discos virtual original](virtual-machines-linux-copy-vm.md) e crie várias NIC como implementar a VM.

## <a name="quick-commands"></a>Comandos rápidos
Certifique-se de que tem o [Clip do Azure](../xplat-cli-install.md) sessão iniciada e utilizar o modo de Gestor de recursos:

```bash
azure config mode arm
```

Nos exemplos seguintes, substitua os nomes dos parâmetros de exemplo com os seus próprios valores. Exemplo os nomes dos parâmetros incluídos `myResourceGroup`, `mystorageaccount`, e `myVM`.

Primeiro, crie um grupo de recursos. O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` na `WestUS` localização:

```bash
azure group create myResourceGroup -l WestUS
```

Crie uma conta de armazenamento para sua VMs colocar em espera. O exemplo seguinte cria uma conta de armazenamento denominada `mystorageaccount`:

```bash
azure storage account create mystorageaccount -g myResourceGroup \
    -l WestUS --kind Storage --sku-name PLRS
```

Crie uma rede virtual para ligar o seu VMs para. O exemplo seguinte cria uma rede virtual denominada `myVnet` com um prefixo de endereço `192.168.0.0/16`:

```bash
azure network vnet create -g myResourceGroup -l WestUS \
    -n myVnet -a 192.168.0.0/16
```

Criar duas sub-redes de rede virtual - um para o tráfego front-end e outra para o tráfego de back-end. O exemplo seguinte cria duas sub-redes, com o nome `mySubnetFrontEnd` e `mySubnetBackEnd`:

```bash
azure network vnet subnet create -g myResourceGroup -e myVnet \
    -n mySubnetFrontEnd -a 192.168.1.0/24
azure network vnet subnet create -g myResourceGroup -e myVnet \
    -n mySubnetBackEnd -a 192.168.2.0/24
```

Crie duas NIC, como anexar um NIC à sub-rede front-end e um NIC à sub-rede back-end. O exemplo seguinte cria duas NIC, com o nome `myNic1` e `myNic2`e anexa-los à sua sub-redes:

```bash
azure network nic create -g myResourceGroup -l WestUS \
    -n myNic1 -m myVnet -k mySubnetFrontEnd
azure network nic create -g myResourceGroup -l WestUS \
    -n myNic2 -m myVnet -k mySubnetBackEnd
```

Por fim, crie a VM, como anexar duas NIC que criou anteriormente. O exemplo seguinte cria uma VM denominada `myVM`:

```bash
azure vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location WestUS \
    --os-type linux \
    --nic-names myNic1,myNic2 \
    --vm-size Standard_DS2_v2 \
    --storage-account-name mystorageaccount \
    --image-urn UbuntuLTS \
    --admin-username ops \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
```

## <a name="creating-multiple-nics-using-azure-cli"></a>Criar múltiplas NIC utilizando o clip do Azure
Se tiver criado anteriormente uma VM utilizando o clip Azure, os comandos rápidos devem estar familiarizados. O processo é o mesmo para criar um NIC ou NIC múltiplos. Pode obter mais detalhes sobre como [Implementar NIC vários utilizando o clip do Azure](../virtual-network/virtual-network-deploy-multinic-arm-cli.md), incluindo scripting o processo de ciclo através de para criar todos os a NIC.

O exemplo seguinte cria duas NIC, com o nome `myNic1` e `myNic2`, com um NIC estabelecer ligação ao cada sub-rede:

```bash
azure network nic create --resource-group myResourceGroup --location WestUS \
    -n myNic1 --subnet-vnet-name myVnet --subnet-name mySubnetFrontEnd
azure network nic create --resource-group myResourceGroup --location WestUS \
    -n myNic2 --subnet-vnet-name myVnet --subnet-name mySubnetBackEnd
```

Normalmente, também pode criar um [Grupo de segurança de rede](../virtual-network/virtual-networks-nsg.md) ou [Balanceador de carga](../load-balancer/load-balancer-overview.md) para ajudar a gerir e distribuir o tráfego pelos seus VMs. Novamente, os comandos são os mesmos quando trabalha com vários NIC. O exemplo seguinte cria um grupo de segurança de rede com o nome `myNetworkSecurityGroup`:

```bash
azure network nsg create --resource-group myResourceGroup --location WestUS \
    --name myNetworkSecurityGroup
```

Vincular o NIC para o grupo de segurança de rede utilizando `azure network nic set`. O exemplo seguinte vincula automaticamente `myNic1` e `myNic2` com `myNetworkSecurityGroup`:

```bashazure 
azure network nic set --resource-group myResourceGroup --name myNic1 \
    --network-security-group-name myNetworkSecurityGroup
azure network nic set --resource-group myResourceGroup --name myNic2 \
    --network-security-group-name myNetworkSecurityGroup
```

Ao criar a VM, agora especificar várias NIC. Prefere utilizar `--nic-name` para fornecer uma única NIC, em vez disso utilize `--nic-names` e forneça uma lista separados por vírgulas de NIC. Também precisa de encarregam-se ao selecionar o tamanho da memória virtual. Existem limites para o número total de NIC que pode adicionar um VM. Leia mais sobre os [tamanhos Linux VM](virtual-machines-linux-sizes.md). O exemplo seguinte mostra como especificar várias NIC e, em seguida, um tamanho da memória virtual que suporta a utilização de vários NIC (`Standard_DS2_v2`):

```bash
azure vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location WestUS \
    --os-type linux \
    --nic-names myNic1,myNic2 \
    --vm-size Standard_DS2_v2 \
    --storage-account-name mystorageaccount \
    --image-urn UbuntuLTS \
    --admin-username ops \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
```

## <a name="creating-multiple-nics-using-resource-manager-templates"></a>Criar múltiplas NIC utilizar modelos de Gestor de recursos
Modelos de Gestor de recursos Azure utilizam declarativos JSON ficheiros para definir o seu ambiente. Pode obter uma [Descrição geral do Gestor de recursos do Azure](../azure-resource-manager/resource-group-overview.md). Modelos de Gestor de recursos fornecem uma maneira de criar várias instâncias de um recurso durante a implementação, tal como criar várias NIC. Utilize a *funcionalidade copiar* para especificar o número de instâncias para criar:

```bash
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Leia mais sobre como [criar várias instâncias utilizando a *função Copiar*](../resource-group-create-multiple.md). 

Também pode utilizar uma `copyIndex()` acrescentar, em seguida, um número para um nome de recurso, que permite-lhe criar `myNic1`, `myNic2`, etc. A imagem seguinte mostra um exemplo de acrescentar o valor de índice:

```bash
"name": "[concat('myNic', copyIndex())]", 
```

Pode obter um exemplo completo da [criação de vários NIC utilizar modelos de Gestor de recursos](../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## <a name="next-steps"></a>Próximos passos
Certifique-se rever [Linux VM tamanhos](virtual-machines-linux-sizes.md) ao tentar criar uma VM com vários NIC. Paga a atenção para o número máximo de NIC cada tamanho da memória virtual suporta. 

Lembre-se de que não é possível adicionar NIC adicional para uma VM existente, terá de criar todos os a NIC quando implementar a VM. Tomar cuidado quando planear as implementações para se certificar de que tem todos os a conectividade de rede necessários desde o início.