<properties
 pageTitle="Limites de programador e predefinições"
 description="Limites de programador e predefinições"
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="08/18/2016"
 ms.author="deli"/>

# <a name="scheduler-limits-and-defaults"></a>Limites de programador e predefinições

## <a name="scheduler-quotas-limits-defaults-and-throttles"></a>As Quotas de programador, limites, predefinições e limitações

[AZURE.INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="the-x-ms-request-id-header"></a>O cabeçalho x-ms-pedido-id

Todos os pedidos de efetuadas contra o serviço de programador devolve um cabeçalho de resposta com o nome**x-ms-pedido-id**. Este cabeçalho contém um valor opaco que identifica o pedido.

Se um pedido de forma consistente está a falhar e verificou que o pedido é elaborado corretamente, poderá utilizar este valor para comunicar o erro à Microsoft. No seu relatório, inclua o valor de x-ms-pedido-id, o tempo aproximado que o pedido foi feito, o identificador da subscrição, coleção de tarefa, e/ou tarefa e o tipo de operação que tentou o pedido.

## <a name="see-also"></a>Consulte também


 [O que é o Scheduler?](scheduler-intro.md)

 [Azure Scheduler conceitos, terminologia e hierarquia entidade](scheduler-concepts-terms.md)

 [Começar a utilizar o Scheduler no portal do Azure](scheduler-get-started-portal.md)

 [Planos e faturação no Azure Programador de tarefas](scheduler-plans-billing.md)

 [Referência de programador REST API Azure](https://msdn.microsoft.com/library/mt629143)

 [Azure cmdlets do PowerShell de Programador de referência](scheduler-powershell-reference.md)

 [Azure Scheduler alta disponibilidade e fiabilidade](scheduler-high-availability-reliability.md)

 [Azure autenticação de saída de programador](scheduler-outbound-authentication.md)
