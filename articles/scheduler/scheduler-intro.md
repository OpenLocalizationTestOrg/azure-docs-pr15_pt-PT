<properties
 pageTitle="O que é o Scheduler do Azure? | Microsoft Azure"
 description="Azure Scheduler permite-lhe descrever forma declarativa ações para executar na nuvem. -Lo, em seguida, agenda e executa automaticamente essas ações."
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
 ms.topic="hero-article"
 ms.date="08/18/2016"
 ms.author="deli"/>

# <a name="what-is-azure-scheduler"></a>O que é o Scheduler do Azure?

Azure Scheduler permite-lhe descrever forma declarativa ações para executar na nuvem. -Lo, em seguida, agenda e executa automaticamente essas ações.  Scheduler faz isto utilizando [o portal do Azure](scheduler-get-started-portal.md), código, [REST API](https://msdn.microsoft.com/library/mt629143.aspx)ou Azure PowerShell.

Scheduler cria, mantém e invoca o trabalho agendado.  Scheduler não alojar qualquer das cargas de trabalho ou executar qualquer código. IT apenas código _invocar_ alojado noutro local — no Azure, no local, ou com outro fornecedor. Chama-lo através de HTTP, HTTPS, uma fila de armazenamento, uma fila de bus serviço ou um tópico de bus de serviço.

Programador de [tarefas](scheduler-concepts-terms.md)de agenda, mantém um histórico de resultados de execução de tarefa uma pode rever e deterministically e fiável agenda das cargas de trabalho a ser executado. Azure WebJobs (parte da funcionalidade Web Apps num serviço de aplicação do Azure) e outros Azure capacidades de agendamento utilizam o programador em segundo plano. O [Scheduler REST API](https://msdn.microsoft.com/library/mt629143.aspx) ajuda a gerir a comunicação para estas ações. Como tal, Scheduler suporta [complexas agendas e avançada periodicidade](scheduler-advanced-complexity.md) facilmente.

Existem várias cenários que conduzem a utilização do Programador de. Por exemplo:

+ _Periódico ações de aplicações:_ Periodicamente recolha de dados a partir do Twitter para um feed.
+ _Manutenção diária:_ Diária eliminação dos registos, efetuar cópias de segurança e outras tarefas de manutenção. Por exemplo, um administrador poderá optar por cópia de segurança da base de dados às 1:00 todos os dias para os seguintes nove meses.

Scheduler permite-lhe criar, atualizar, eliminar, ver e gerir tarefas e [colecções de tarefa](scheduler-concepts-terms.md) através de programação, utilizando scripts e no portal.

## <a name="see-also"></a>Consulte também

 [Azure Scheduler conceitos, terminologia e hierarquia entidade](scheduler-concepts-terms.md)

 [Começar a utilizar o Scheduler no portal do Azure](scheduler-get-started-portal.md)

 [Planos e faturação no Azure Programador de tarefas](scheduler-plans-billing.md)

 [Como construir complexas agendas e periodicidade avançada com o programador do Azure](scheduler-advanced-complexity.md)

 [Referência de programador REST API Azure](https://msdn.microsoft.com/library/mt629143)

 [Azure cmdlets do PowerShell de Programador de referência](scheduler-powershell-reference.md)

 [Azure Scheduler alta disponibilidade e fiabilidade](scheduler-high-availability-reliability.md)

 [Limites de programador Azure, predefinições e códigos de erro](scheduler-limits-defaults-errors.md)

 [Azure autenticação de saída de programador](scheduler-outbound-authentication.md)
