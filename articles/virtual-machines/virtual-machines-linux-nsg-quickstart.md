<properties
   pageTitle="Abrir portas e os pontos finais para uma VM Linux | Microsoft Azure"
   description="Saiba como abrir uma porta / crie um ponto final para sua VM Linux utilizando o modelo de implementação do Gestor de recursos Azure e o clip do Azure"
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
   ms.workload="infrastructure-services"
   ms.date="10/27/2016"
   ms.author="iainfou"/>

# <a name="opening-ports-and-endpoints-to-a-linux-vm-in-azure"></a>Posso abrir portas e os pontos finais para uma VM Linux no Azure
Abrir uma porta ou criar um ponto final, a uma máquina de virtual (VM) no Azure através da criação de um filtro de rede num sub-rede ou interface da rede VM. Coloque estes filtros, que controlam o tráfego de entrada e saído, no grupo de segurança de rede ligada ao recurso que recebe o tráfego. Vamos utilizar um exemplo comum de tráfego da web no porta 80.

## <a name="quick-commands"></a>Comandos rápidos
Para criar um grupo de segurança de rede e regras tem [O clip do Azure](../xplat-cli-install.md) instalado e utilizar o modo de Gestor de recursos:

```bash
azure config mode arm
```

Nos exemplos seguintes, substitua os nomes dos parâmetros de exemplo com os seus próprios valores. Exemplo os nomes dos parâmetros incluídos `myResourceGroup`, `myNetworkSecurityGroup`, e `myVnet`.

Crie o grupo de segurança de rede, introduzir o seu próprio nomes e a localização corretamente. O exemplo seguinte cria um grupo de segurança de rede com o nome `myNetworkSecurityGroup` na `WestUS` localização:

```
azure network nsg create --resource-group myResourceGroup --location westus \
    --name myNetworkSecurityGroup
```

Adicione uma regra para permitir o tráfego para o servidor Web HTTP (ou ajustar para o seu próprio cenário, tal como SSH conectividade de acesso ou base de dados). O exemplo seguinte cria uma regra com o nome `myNetworkSecurityGroupRule` para permitir o tráfego TCP na porta 80:

```
azure network nsg rule create --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRule \
    --protocol tcp --direction inbound --priority 1000 \
    --destination-port-range 80 --access allow
```

Associe o grupo de segurança de rede a interface de rede da sua VM (NIC). O exemplo seguinte associa um NIC existente com o nome `myNic` com o grupo de segurança de rede com o nome `myNetworkSecurityGroup`:

```
azure network nic set --resource-group myResourceGroup \
    --network-security-group-name myNetworkSecurityGroup --name myNic
```

Em alternativa, pode associar o seu grupo de segurança de rede com sub-rede de uma rede virtual em vez de apenas para a interface de rede uma única VM. O exemplo seguinte associa uma sub-rede existente com o nome `mySubnet` na `myVnet` rede virtual com o grupo de segurança de rede com o nome `myNetworkSecurityGroup`:

```
azure network vnet subnet set --resource-group myResourceGroup \
    --network-security-group-name myNetworkSecurityGroup \
    --vnet-name myVnet --name mySubnet
```

## <a name="more-information-on-network-security-groups"></a>Obter mais informações sobre os grupos de segurança de rede
Os comandos rápidos aqui permitem-lhe começar a trabalhar rapidamente com o tráfego a fluir para sua VM. Grupos de segurança de rede fornecem muitas funcionalidades excelentes e granularidade para controlar o acesso aos seus recursos. Pode ler mais sobre a [criação de um grupo de segurança de rede e as regras ACL aqui](../virtual-network/virtual-networks-create-nsg-arm-cli.md).

Pode definir grupos de segurança de rede e as regras ACL como parte do Gestor de recursos do Azure modelos. Leia mais sobre a [criação de grupos de segurança de rede com modelos](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Se precisar de utilizar o reencaminhamento de portas para mapear uma porta externa exclusiva para uma porta interna no seu VM, utilize um balanceador de carga e as regras de tradução de endereços da rede (NAT). Por exemplo, poderá querer para expor TCP porta 8080 externamente e ter o tráfego direcionado para a porta TCP 80 uma VM. Pode obter informações sobre a [criação de um balanceador de carga de acesso à Internet](../load-balancer/load-balancer-get-started-internet-arm-cli.md).

## <a name="next-steps"></a>Próximos passos
Neste exemplo, criado uma regra para permitir o tráfego HTTP simples. Pode encontrar informações sobre como criar ambientes mais detalhadas nos seguintes artigos:

- [Descrição geral do Gestor de recursos Azure](../azure-resource-manager/resource-group-overview.md)
- [O que é um grupo de segurança de rede (NSG)?](../virtual-network/virtual-networks-nsg.md)
- [Descrição geral do Gestor de recursos do Azure para balanceadores de carga](../load-balancer2    /load-balancer-arm.md)