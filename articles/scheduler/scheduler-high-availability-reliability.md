<properties
 pageTitle="Scheduler alta disponibilidade e fiabilidade"
 description="Scheduler alta disponibilidade e fiabilidade"
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
 ms.date="08/16/2016"
 ms.author="deli"/>


# <a name="scheduler-high-availability-and-reliability"></a>Scheduler alta disponibilidade e fiabilidade

## <a name="azure-scheduler-high-availability"></a>Azure Scheduler alta disponibilidade

Como um serviço de plataforma Azure elementos estruturais, Azure Scheduler é altamente disponível e funcionalidades de implementação do serviço geo redundantes e replicação geo regionais tarefa.

### <a name="geo-redundant-service-deployment"></a>Implementação de serviço geo redundantes

Azure programador está disponível através de IU na região quase todas geo que está no Azure hoje. A lista de regiões que está disponível no Azure programador está [listado aqui](https://azure.microsoft.com/regions/#services). Se um centro de dados numa região alojado é composto indisponível, as capacidades de activação pós-falha do Scheduler do Azure estão assim que o serviço está disponível a partir do Centro de dados do outro.

### <a name="geo-regional-job-replication"></a>Replicação de tarefa geo regionais

Não só é o Scheduler do Azure front-end disponíveis para pedidos de gestão, mas a sua tarefa também são replicada geo. Quando há uma falha numa região, o Scheduler do Azure Falha ao longo do e garante que a tarefa é executada a partir do Centro de dados noutro na região geográfica par.

Por exemplo, se tiver criado uma tarefa no Sul Central-nos, o Scheduler do Azure replica automaticamente essa tarefa na América do Norte Central-nos. Quando há uma falha na Sul Central-nos, o Scheduler do Azure garante que a tarefa é executada a partir da América do Norte Central-nos. 

![][1]

Como resultado, o Scheduler do Azure assegura que os seus dados permanecem dentro da mesma região geográfica mais amplo em caso de uma falha do Azure. Como resultado, não precisa de duplicar o trabalho apenas para adicionar elevada disponibilidade – Azure Scheduler automaticamente fornece capacidades de alta disponibilidade para as tarefas.

## <a name="azure-scheduler-reliability"></a>Fiabilidade de programador do Azure

Azure Scheduler garante a sua própria alta disponibilidade e leva uma abordagem diferente para tarefas criados pelo utilizador. Por exemplo, a tarefa pode invocar um ponto final HTTP que não está disponível. Azure Scheduler assim tenta executar a sua tarefa com êxito, atribuindo opções alternativas para lidar com falha. Azure Scheduler faz isto de duas maneiras:

### <a name="configurable-retry-policy-via-retrypolicy"></a>Política de repetir configuráveis através de "retryPolicy"

Azure Scheduler permite-lhe configurar uma política de repetir. Por predefinição, se uma tarefa falhar, Scheduler tentará a tarefa novamente quatro mais vezes em intervalos de 30 segundos. Pode configurar novamente esta política de repetir a ser mais agressivas (por exemplo, dez vezes, em intervalos de 30 segundos) ou quanto (por exemplo, duas vezes, intervalos diárias.)

Como um exemplo de quando isto pode ajudar, pode criar uma tarefa que é executado vez por semana e invoca um ponto final HTTP. Se o ponto final de HTTP destina-se para baixo algumas horas quando o seu trabalho é executado, não poderá querer de esperar uma semana mais para a tarefa de executar novamente uma vez que o mesmo a política predefinida para repetir irá falhar. Nestes casos, poderá reconfigurar a política de repetir padrão para repetir a cada três horas (por exemplo) em vez de cada 30 segundos.

Para saber como configurar uma política de repetir, consulte [retryPolicy](scheduler-concepts-terms.md#retrypolicy).

### <a name="alternate-endpoint-configurability-via-erroraction"></a>Alternativo de ponto final de configurabilidade através de "errorAction"

Se o ponto final de destino para a tarefa de programador do Azure permanecer inacessível, Azure Scheduler reverte para o ponto final de processamento de erros alternativo depois de seguir sua política de repetir. Se estiver configurado um extremo de processamento de erros alternativo, o Scheduler do Azure chama-lo. Com um ponto final alternativo, suas próprias tarefas estão altamente disponíveis no falha.

Por exemplo, no diagrama abaixo, o Scheduler do Azure segue sua política de repetir para acertar um serviço web de Nova Iorque. Depois do número de tentativas falha, verifica se existe uma alternativa. Em seguida, acede com antecedência e inicia a fazer pedidos para a alternativa com a mesma política de repetir.

![][2]

Tenha em atenção que a mesma política de repetir, aplica-se para a ação original e a ação de erro alternativo. Também é possível ter o tipo de ação a ação de erro alternativo ser diferente do tipo de ação a ação principal. Por exemplo, enquanto a ação principal pode ser invocar um ponto final HTTP, a ação de erro em vez disso, poderá ser uma fila de armazenamento, fila de bus serviço ou serviço bus tópico ação que é que o registo de erros.

Para saber como configurar um ponto final alternativo, consulte [errorAction](scheduler-concepts-terms.md#action-and-erroraction).

## <a name="see-also"></a>Consulte também

 [O que é o Scheduler?](scheduler-intro.md)

 [Azure Scheduler conceitos, terminologia e hierarquia entidade](scheduler-concepts-terms.md)

 [Começar a utilizar o Scheduler no portal do Azure](scheduler-get-started-portal.md)

 [Planos e faturação no Azure Programador de tarefas](scheduler-plans-billing.md)

 [Como construir complexas agendas e periodicidade avançada com o programador do Azure](scheduler-advanced-complexity.md)

 [Referência de programador REST API Azure](https://msdn.microsoft.com/library/mt629143)

 [Azure cmdlets do PowerShell de Programador de referência](scheduler-powershell-reference.md)

 [Limites de programador Azure, predefinições e códigos de erro](scheduler-limits-defaults-errors.md)

 [Azure autenticação de saída de programador](scheduler-outbound-authentication.md)


[1]: ./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png

[2]: ./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png
