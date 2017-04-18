<properties
    pageTitle="Diretrizes de contas e de subscrição | Microsoft Azure"
    description="Saiba mais sobre as diretrizes de estrutura e implementação chaves para contas no Azure e subscrições."
    documentationCenter=""
    services="virtual-machines-linux"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="subscription-and-accounts-guidelines"></a>Diretrizes de subscrição e contas

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)] 

Este artigo foca Noções básicas sobre como aproximar gestão de subscrição e conta, como o seu ambiente e cresce de base de utilizador.


## <a name="implementation-guidelines-for-subscriptions-and-accounts"></a>Diretrizes de implementação para contas e subscrições

Decisões:

- O que conjunto de subscrições e não de contas que precisa para alojar o seu carga de trabalho ou infraestrutura?
- Como divide a hierarquia para se ajustar a sua organização?

Tarefas:

- Defina a hierarquia lógica organização que pretender para gerir a partir de um nível de subscrição.
- Corresponder a esta hierarquia lógica, pode defina as contas obrigatórias e subscrições em cada conta.
- Crie o conjunto de contas utilizando Convenção de nomenclatura e subscrições.


## <a name="subscriptions-and-accounts"></a>Subscrições e contas

Para trabalhar com Azure, tem uma ou mais subscrições Azure. Recursos como máquinas virtuais (VMs) ou redes virtuais existirem desses subscrições.

- Os clientes empresariais normalmente têm um inscrição de empresa, que é um recurso mais início na hierarquia e está associado a uma ou mais contas.
- Para os consumidores e clientes sem uma inscrição Enterprise, o recurso de início mais está a conta.
- Subscrições estão associadas a contas e, podem existir uma ou mais subscrições por conta. Registos Azure informação ao nível de subscrição de faturação.

Devido ao limite de dois níveis da hierarquia na relação de conta/subscrição, é importante alinhar a Convenção de nomenclatura de contas e subscrições para as necessidades de faturaçãohttps. Por exemplo, se uma empresa global utiliza Azure, podem optar por ter uma conta por região e tem subscrições geridos ao nível de região:

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub01.png)

Por exemplo, poderá utilizar a seguinte estrutura:

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub02.png)

Se decidir uma região tenha mais do que uma subscrição associada a um grupo específico, a Convenção de nomenclatura deve incorporar uma forma de codificar os dados extra da conta ou o nome da subscrição. Este organização o permitir massaging dados de faturaçãohttps para gerar novos níveis da hierarquia durante relatórios de faturaçãohttps:

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub03.png)

A organização pode ter o seguinte aspeto:

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub04.png)

Fornecemos faturação detalhada através de um ficheiro transferível para uma única conta ou para todas as contas num acordo empresarial.


## <a name="next-steps"></a>Próximos passos

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)] 