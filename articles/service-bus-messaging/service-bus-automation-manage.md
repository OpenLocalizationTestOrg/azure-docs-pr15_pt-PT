<properties
    pageTitle="Gerir Azure Service Bus utilizando a automatização Azure | Microsoft Azure"
    description="Saiba como utilizar o serviço de automatização do Azure para gerir Azure Service Bus."
    services="service-bus, automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/29/2016"
    ms.author="magoedte;csand"/>

# <a name="managing-azure-service-bus-using-azure-automation"></a>Gerir Azure Service Bus utilizando a automatização do Azure

Este guia vai introduzi-lo para o serviço de automatização do Azure e como pode ser utilizado para simplificar a gestão do Azure Service Bus.

## <a name="what-is-azure-automation"></a>O que é o Azure automatização?

[Azure automatização](../automation/automation-intro.md) é um serviço Azure para simplificar a gestão de nuvem através de automatização dos processos e configuração de estado pretendido. Utilizar a automatização Azure, manual, repetida, tarefas de execução longa e passíveis de erro podem ser automatizadas para aumentar a fiabilidade, eficiência e a hora a valor para a sua organização.

Automatização Azure fornece um motor de execução do fluxo de trabalho altamente fiáveis, altamente disponível que se adapta para corresponder às suas necessidades. No Azure automatização, processos podem ser pontapés manualmente, pelos sistemas de 3 terceiros ou em intervalos agendados para que as tarefas deverá acontecer exatamente quando necessário.

Reduzir o overhead operacional e libertar IT e funcionários DevOps concentrar-se no trabalho que soma o valor de negócio ao mover as tarefas de gestão de na nuvem para ser executada automaticamente por Azure automatização.

## <a name="how-can-azure-automation-help-manage-azure-service-bus"></a>Como Azure automatização ajudam a gerir Azure Service Bus?

Pode gerir o serviço Bus com a automatização Azure utilizando o [Serviço Bus REST API](https://msdn.microsoft.com/library/azure/mt639375.aspx). Dentro de automatização do Azure pode executar scripts de PowerShell para efetuar muitas das suas tarefas de serviço Bus utilizar a API REST. Também pode emparelhar estas chamadas de REST API no Azure automatização com os cmdlets para outros serviços do Azure, para automatizar tarefas complexas através de serviços do Azure e 3º sistemas de terceiros.

Eis alguns exemplos de utilizar o PowerShell para gerir Azure Service Bus:

* [Personalizado os cmdlets do PowerShell para gerir filas Bus de serviço do Azure](https://blogs.technet.microsoft.com/uktechnet/2014/12/04/sample-of-custom-powershell-cmdlets-to-manage-azure-servicebus-queues)
* [Como criar serviço Bus filas, tópicos e subscrições utilizando um script PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [Criar através do PowerShell Azure espaços de nomes de Bus de serviço](http://buildazure.com/2015/09/24/create-azure-service-bus-namespaces-using-powershell-and-x-plat-cli/)

O módulo do PowerShell para trabalhar com bus Azure service no automatização runbooks pode ser transferido a partir da [Galeria do PowerShell](https://www.powershellgallery.com/packages/AzureServiceBusCreation/1.0).


## <a name="next-steps"></a>Próximos passos

Agora que aprendeu os princípios básicos de automatização do Azure e como pode ser utilizado para gerir Azure Service Bus, siga estas ligações para mais informações sobre o Azure automatização.

* Ver a automatização Azure [Introdução Tutorial](../automation/automation-first-runbook-graphical.md)
* Consulte o artigo como [Gerir o serviço Bus com PowerShell](service-bus-powershell-how-to-provision.md)
