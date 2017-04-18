<properties
    pageTitle="Criar em farms do SharePoint server | Microsoft Azure"
    description="Crie rapidamente uma nova farm do SharePoint 2013 ou 2016 do SharePoint no Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="JoeDavies-MSFT"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="josephd"/>

# <a name="create-sharepoint-server-farms"></a>Criar em farms do SharePoint server

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo clássico.

## <a name="sharepoint-2013-farms"></a>Farms do SharePoint 2013

Com o portal de Microsoft Azure marketplace, pode criar rapidamente pré-configurada farms do SharePoint Server 2013. Isto modo, pode poupar tempo muitas quando precisar de um farm do SharePoint básico ou alta disponibilidade para um ambiente do Dev Center/testar ou se está a avaliar o SharePoint Server 2013 como uma solução de colaboração para a sua organização.

> [AZURE.NOTE] O item do **Farm do SharePoint** no Azure Marketplace do Azure portal foi removido. Foi substituído com os itens **Que não sejam-HA Farm do SharePoint 2013** e **HA Farm do SharePoint 2013** .

O farm do SharePoint básico é constituída por três máquinas virtuais nesta configuração.

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/Non-HAFarm.png)

Pode utilizar esta configuração do farm para simplificar a configuração para o desenvolvimento de aplicação do SharePoint ou a avaliação pela primeira vez do SharePoint 2013.

Para criar o farm do SharePoint (servidor de três) básico:

1. Clique [aqui](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/).
2. Clique em **Implementar**.
3. No painel de **Que não sejam-HA Farm do SharePoint 2013** , clique em **Criar**.
4. Especificar as definições sobre os passos do painel **Criar não-HA Farm do SharePoint 2013** e, em seguida, clique em **Criar**.

O farm do SharePoint alta disponibilidade consiste em máquinas virtuais nove nesta configuração.

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/HAFarm.png)

Pode utilizar esta configuração do farm para testar a superiores cargas de cliente, elevada disponibilidade do site do SharePoint externo e grupos de disponibilidade do SQL Server AlwaysOn de um farm do SharePoint. Também pode utilizar esta configuração para SharePoint programação da aplicações num ambiente de alta disponibilidade.

Para criar o farm do SharePoint (servidor de nove) como sendo de alta disponibilidade:

1. Clique [aqui](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/).
2. Clique em **Implementar**.
3. No painel **HA Farm do SharePoint 2013** , clique em **Criar**.
4. Especificar as definições sobre os sete passos do painel **Criar 2013 HA Farm do SharePoint** e, em seguida, clique em **Criar**.

> [AZURE.NOTE] Não é possível criar o **Farm do SharePoint 2013 HA** ou **Não-HA Farm do SharePoint 2013** com uma versão de avaliação gratuita do Azure.

Portal do Azure cria ambos estes farms numa rede virtual apenas na nuvem com uma presença na web acesso à Internet. Não existe ligação VPN ou ExpressRoute site para o site voltar à rede da organização.

> [AZURE.NOTE] Quando criar o basic ou alta disponibilidade em farms do SharePoint através do portal Azure, não é possível especificar um grupo de recursos existente. Para resolver esta limitação, crie estes farms com PowerShell do Azure. Para mais informações, consulte o artigo [Criar SharePoint 2013 Dev Center/testar farms com PowerShell do Azure](https://technet.microsoft.com/library/mt743093.aspx#powershell).

## <a name="sharepoint-2016-farms"></a>Farms do SharePoint 2016

Consulte [Este artigo](https://technet.microsoft.com/library/mt723354.aspx) para obter as instruções criar o seguinte único-2016 de servidor do SharePoint farm.

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/SP2016Farm.png)

## <a name="managing-the-sharepoint-farms"></a>Gerir os farms do SharePoint

Pode administrar os servidores destes farms através de ligações de ambiente de trabalho remoto. Para mais informações, consulte o artigo [Iniciar sessão para a máquina virtual](virtual-machines-windows-hero-tutorial.md#log-on-to-the-virtual-machine).

A partir do site do SharePoint de Administração Central, pode configurar os meus sites, as aplicações do SharePoint e outras funcionalidades. Para mais informações, consulte o artigo [Configurar o SharePoint](http://technet.microsoft.com/library/ee836142.aspx).

## <a name="next-steps"></a>Próximos passos

- Descubra adicionais [configurações do SharePoint](https://technet.microsoft.com/library/dn635309.aspx) nos serviços de infraestrutura Azure.
