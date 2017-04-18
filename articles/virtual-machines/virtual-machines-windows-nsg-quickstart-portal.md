<properties
   pageTitle="Abra portas para uma VM através do portal Azure | Microsoft Azure"
   description="Saiba como abrir uma porta / crie um ponto final para sua VM Windows utilizando o modelo de implementação do Gestor de recursos no Portal do Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/27/2016"
   ms.author="iainfou"/>

# <a name="opening-ports-to-a-vm-in-azure-using-the-azure-portal"></a>Abertura de portas para uma VM no Azure através do portal Azure
[AZURE.INCLUDE [virtual-machines-common-nsg-quickstart](../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Comandos rápidos
Também pode [executar estes passos através do Azure PowerShell](virtual-machines-windows-nsg-quickstart-powershell.md).

Em primeiro lugar, crie o grupo de segurança de rede. Selecione um grupo de recursos no portal do, clique em **Adicionar**, em seguida, procure e selecione o grupo de segurança de rede:

![Adicionar um grupo de segurança de rede](./media/virtual-machines-windows-nsg-quickstart-portal/add-nsg.png)

Introduza um nome para o seu grupo de segurança de rede, selecione ou criar um grupo de recursos e selecione uma localização. Clique em **Criar** quando tiver terminado:

![Criar um grupo de segurança de rede](./media/virtual-machines-windows-nsg-quickstart-portal/create-nsg.png)

Selecione o novo grupo de segurança de rede. Selecione 'regras de segurança entrada' e, em seguida, clique no botão **Adicionar** para criar uma regra:

![Adicionar uma regra de entrada](./media/virtual-machines-windows-nsg-quickstart-portal/add-inbound-rule.png)

Forneça um nome para a sua nova regra. Portas 80 já são introduzida por predefinição. Este pá é onde quiser alterar de origem, protocolo e destino ao adicionar regras adicionais ao seu grupo de segurança de rede. Clique em **OK** para criar a regra:

![Criar uma regra de entrada](./media/virtual-machines-windows-nsg-quickstart-portal/create-inbound-rule.png)

O passo final é associar o seu grupo de segurança de rede uma sub-rede ou uma interface de rede específico. Vamos associar o grupo de segurança de rede uma sub-rede. Selecione 'Sub-redes' e, em seguida, clique em **associar**:

![Associar um grupo de segurança de rede uma sub-rede](./media/virtual-machines-windows-nsg-quickstart-portal/associate-subnet.png)

Selecione a sua rede virtual e, em seguida, selecione sub-rede apropriado:

![Associar um grupo de segurança de rede com a rede virtual](./media/virtual-machines-windows-nsg-quickstart-portal/select-vnet-subnet.png)

Agora ter criado um grupo de segurança de rede, criado uma regra de entrada que permite que o tráfego na porta 80 e associada a uma sub-rede. Qualquer VMs que se liga nessa sub-rede estão acessíveis na porta 80.


## <a name="more-information-on-network-security-groups"></a>Obter mais informações sobre os grupos de segurança de rede
Os comandos rápidos aqui permitem-lhe começar a trabalhar rapidamente com o tráfego a fluir para sua VM. Grupos de segurança de rede fornecem muitas funcionalidades excelentes e granularidade para controlar o acesso aos seus recursos. Pode ler mais sobre a [criação de um grupo de segurança de rede e as regras ACL aqui](../virtual-network/virtual-networks-create-nsg-arm-ps.md).

Pode definir grupos de segurança de rede e as regras ACL como parte do Gestor de recursos do Azure modelos. Leia mais sobre a [criação de grupos de segurança de rede com modelos](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Se precisar de utilizar o reencaminhamento de portas para mapear uma porta externa exclusiva para uma porta interna no seu VM, utilize um balanceador de carga e as regras de tradução de endereços da rede (NAT). Por exemplo, poderá querer para expor TCP porta 8080 externamente e ter o tráfego direcionado para a porta TCP 80 uma VM. Pode obter informações sobre a [criação de um balanceador de carga de acesso à Internet](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="next-steps"></a>Próximos passos
Neste exemplo, criado uma regra para permitir o tráfego HTTP simples. Pode encontrar informações sobre como criar ambientes mais detalhadas nos seguintes artigos:

- [Descrição geral do Gestor de recursos Azure](../azure-resource-manager/resource-group-overview.md)
- [O que é um grupo de segurança de rede (NSG)?](../virtual-network/virtual-networks-nsg.md)
- [Descrição geral do Gestor de recursos do Azure para balanceadores de carga](../load-balancer/load-balancer-arm.md)
