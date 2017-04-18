<properties
   pageTitle="Adicionar relatórios de estado de funcionamento de serviço ferro personalizados | Microsoft Azure"
   description="Descreve como enviar relatórios de estado de funcionamento personalizado para entidades de estado de funcionamento do Azure Service ferro. Dá recomendações para estruturar e implementar relatórios de estado de funcionamento de qualidade."
   services="service-fabric"
   documentationCenter=".net"
   authors="oanapl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/28/2016"
   ms.author="oanapl"/>

# <a name="add-custom-service-fabric-health-reports"></a>Adicionar relatórios de estado de funcionamento de serviço ferro personalizados
Azure Service ferro apresenta um [modelo de estado de funcionamento](service-fabric-health-introduction.md) concebido para sinalizar cluster danificado e condições de aplicação no entidades específicas. O modelo de estado de funcionamento utiliza **sede de estado de funcionamento** (componentes do sistema e fiscais). O objetivo é fácil e rápido de diagnóstico e reparação. Precisa de pensar processo decorra sobre o estado de funcionamento do escritores do serviço. Uma condição que pode afetar o estado de funcionamento deve ser comunicada, sobretudo se o pode ajudar a problemas de sinalizador perto da raiz. As informações de estado de funcionamento podem guardar muito tempo e esforço sobre depuração e inquérito uma vez, o serviço está a trabalhar na escala na nuvem (privado ou Azure).

O monitor de sede serviço ferro identificado condições de interesse. Estes relatórios sobre essas condições baseadas na sua vista local. O [Estado de funcionamento do armazenar](service-fabric-health-introduction.md#health-store) agrega dados de estado de funcionamento que foi enviados por todos os sede para determinar se entidades são globalmente saudáveis. O modelo se destina a ser formatado, flexíveis e fáceis de utilizar. A qualidade dos relatórios de estado de funcionamento determina a precisão da vista de estado de funcionamento do cluster. Falsos indevidamente mostram problemas danificados podem um impacto negativo actualizações ou outros serviços que utilizem dados de estado de funcionamento. Exemplos desses serviços são mecanismos alerta e serviços de reparação. Por conseguinte, algumas pensamento é necessário para fornecer relatórios que capturar condições de interesse da melhor forma possíveis.

Para estruturar e implementar a comunicar o estado de funcionamento, fiscais e tem de componentes do sistema:

- Defina a condição que lhe interessam, a forma como é monitorizada e o impacto da funcionalidade cluster ou aplicação. Com base nesta informação, decida na propriedade de relatório de estado de funcionamento e estado de funcionamento.

- Determine a [entidade](service-fabric-health-introduction.md#health-entities-and-hierarchy) que o relatório que se aplica a.

- Determinar onde o reporte é concluído, a partir de dentro do serviço ou a partir de um watchdog interna e externa.

- Defina uma origem de utilizado para identificar o autor.

- Selecione uma estratégia de elaboração de relatórios, periodicamente ou no transições. O caminho recomendado é periodicamente, tal como requer código mais simples e está menos sujeito às erros.

- Determine quanto tempo o relatório para condições danificados deve manter-se na loja do Estado de funcionamento e como deve ser desmarcada. Com esta informação, decida o tempo de comunicar para live e o comportamento de remover no expiração.

Tal como mencionado, elaboração de relatórios pode ser feita:

- Monitorizada réplica de serviço do serviço ferro.

- Fiscais internos implementados como um serviço de ferro de serviço (por exemplo, um serviço ferro sem estado serviço que monitoriza condições e relatórios de problemas). Podem ser os fiscais implementado um todos os nós ou pode ser affinitized para o serviço monitorizado.

- Fiscais internos que executar em nós ferro de serviço, mas *não* implementada como serviços ferro de serviço.

- Fiscais externos que sondagem o recurso de *fora* do serviço ferro cluster (por exemplo, serviço de monitorização como Gomez).

> [AZURE.NOTE] Terminar a caixa de, cluster é povoado com relatórios de estado de funcionamento que foi enviados pelos componentes do sistema. Leia mais em [relatórios de estado de funcionamento do sistema utilizar para resolver o problema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md). Os relatórios de utilizador devem ser enviados em [entidades de estado de funcionamento](service-fabric-health-introduction.md#health-entities-and-hierarchy) que já foram criadas pelo sistema.

Uma vez o estado de funcionamento do relatório de estrutura está desmarcada, relatórios de estado de funcionamento podem ser enviados facilmente. Pode utilizar [FabricClient](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.aspx) para o estado de funcionamento do relatório, se o cluster não estiver [seguro](service-fabric-cluster-security.md) ou, se o cliente ferro tenha privilégios de administrador. Isto pode ser feito através da API utilizando [FabricClient.HealthManager.ReportHealth](https://msdn.microsoft.com/library/system.fabric.fabricclient.healthclient.reporthealth.aspx), através do PowerShell ou através de descanso. Botões de configuração batch relatórios de desempenho melhorado.

> [AZURE.NOTE] Estado de funcionamento do relatório é síncrono e representa o trabalho de validação do lado do cliente. O facto de que o relatório é aceite pelo cliente do Estado de funcionamento ou o `Partition` ou `CodePackageActivationContext` objetos não significa que esta seja aplicada na loja. É enviado modo assíncrono e possivelmente enviadas em batches com outros relatórios. Ainda poderá falhar processamento no servidor: o número de sequência dever obsoleto, a entidade no qual o relatório terá de ser aplicado foi eliminado, etc.

## <a name="health-client"></a>Cliente do Estado de funcionamento
Os relatórios de estado de funcionamento são enviados para o arquivo de estado de funcionamento através de um cliente de estado de funcionamento que encontra-se no interior do cliente de estação de ferro. O cliente de estado de funcionamento pode ser configurado com o seguinte procedimento:

- **HealthReportSendInterval**: O atraso entre o tempo que o relatório é adicionado ao cliente e o tempo ter sido enviada para o arquivo de estado de funcionamento. Utilizado para lote relatórios para uma única mensagem, em vez de enviar uma mensagem para cada relatório. O processo de lotes melhora o desempenho. Predefinição: 30 segundos.

- **HealthReportRetrySendInterval**: O intervalo no qual o cliente de estado de funcionamento reenvios do Estado de funcionamento acumulado relatórios para o arquivo de estado de funcionamento. Predefinição: 30 segundos.

- **HealthOperationTimeout**: período de tempo limite para uma mensagem de relatório enviada para o arquivo de estado de funcionamento. Se uma mensagem de tempo, o cliente de estado de funcionamento repetições-lo até o arquivo de estado de funcionamento confirma que o relatório foi processado. Predefinição: dois minutos.

> [AZURE.NOTE] Quando os relatórios são enviadas em batches, o cliente de ferro deve ser mantido vivo pelo menos HealthReportSendInterval para se certificar de que são enviadas. Se a mensagem é perdida ou o arquivo de estado de funcionamento não é possível aplicá-los devido a erros breves, o cliente de ferro deve ser mantido vivo já para lhe dar a oportunidade de repetir.

A utilização da memória intermédia no cliente leva a exclusividade dos relatórios em consideração. Por exemplo, se um determinado reporter incorretas é a identificação de relatórios de 100 por segundo sobre a mesma propriedade da mesma entidade, os relatórios são substituídos com a última versão. Apenas uma essa existe um relatório na fila de cliente no máximo. Se estiver configurado de lotes, o número de relatórios enviadas para o arquivo de estado de funcionamento é apenas um por intervalo de enviar. Este relatório é o último relatório adicionado, que reflita o estado da entidade mais recente.
Todos os parâmetros de configuração podem ser especificado quando `FabricClient` é criado pela prisma [FabricClientSettings](https://msdn.microsoft.com/library/azure/system.fabric.fabricclientsettings.aspx) com os valores pretendidos para entradas relacionadas com o estado de funcionamento.

A seguinte cria um cliente ferro e especifica que os relatórios devem ser enviados quando são adicionados. No tempos limite e erros que podem ser repetidos, número de tentativas ocorrer em segundos 40.

```csharp
var clientSettings = new FabricClientSettings()
{
    HealthOperationTimeout = TimeSpan.FromSeconds(120),
    HealthReportSendInterval = TimeSpan.FromSeconds(0),
    HealthReportRetrySendInterval = TimeSpan.FromSeconds(40),
};
var fabricClient = new FabricClient(clientSettings);
```

Parâmetros mesmo podem ser especificados quando é criada uma ligação a um cluster através do PowerShell. A seguinte inicia uma ligação a um cluster local:

```powershell
PS C:\> Connect-ServiceFabricCluster -HealthOperationTimeoutInSec 120 -HealthReportSendIntervalInSec 0 -HealthReportRetrySendIntervalInSec 40
True

ConnectionEndpoint   :
FabricClientSettings : {
                       ClientFriendlyName                   : PowerShell-1944858a-4c6d-465f-89c7-9021c12ac0bb
                       PartitionLocationCacheLimit          : 100000
                       PartitionLocationCacheBucketCount    : 1024
                       ServiceChangePollInterval            : 00:02:00
                       ConnectionInitializationTimeout      : 00:00:02
                       KeepAliveInterval                    : 00:00:20
                       HealthOperationTimeout               : 00:02:00
                       HealthReportSendInterval             : 00:00:00
                       HealthReportRetrySendInterval        : 00:00:40
                       NotificationGatewayConnectionTimeout : 00:00:00
                       NotificationCacheUpdateTimeout       : 00:00:00
                       }
GatewayInformation   : {
                       NodeAddress                          : localhost:19000
                       NodeId                               : 1880ec88a3187766a6da323399721f53
                       NodeInstanceId                       : 130729063464981219
                       NodeName                             : Node.1
                       }
```

> [AZURE.NOTE] Para se certificar de que não autorizados serviços não é possível reportar saúde contra as entidades no cluster, o servidor pode ser configurado para aceitar pedidos apenas a partir de clientes seguros. O `FabricClient` utilizado para elaboração de relatórios também deve ter segurança activado possam comunicar com o cluster (por exemplo, com autenticação Kerberos ou certificado). Leia mais sobre a [segurança de cluster](service-fabric-cluster-security.md).

## <a name="report-from-within-low-privilege-services"></a>A partir de um relatório nos serviços de baixa privilégio
A partir de dentro de serviços de ferro de serviço que não têm acesso do administrador para cluster, pode comunicar do Estado de funcionamento em entidades a partir do contexto atual através de `Partition` ou `CodePackageActivationContext`.

- Para os serviços sem estado, utilize [IStatelessServicePartition.ReportInstanceHealth](https://msdn.microsoft.com/library/system.fabric.istatelessservicepartition.reportinstancehealth.aspx) reportar a instância atual do serviço.

- Para os serviços com estado, utilize [IStatefulServicePartition.ReportReplicaHealth](https://msdn.microsoft.com/library/system.fabric.istatefulservicepartition.reportreplicahealth.aspx) reportar réplica atual.

- Utilize [IServicePartition.ReportPartitionHealth](https://msdn.microsoft.com//library/system.fabric.iservicepartition.reportpartitionhealth.aspx) reportar a entidade de partição atual.

- Utilize [CodePackageActivationContext.ReportApplicationHealth](https://msdn.microsoft.com/library/system.fabric.codepackageactivationcontext.reportapplicationhealth.aspx) para elaborar relatórios sobre a aplicação atual.

- Utilizar [CodePackageActivationContext.ReportDeployedApplicationHealth](https://msdn.microsoft.com/library/system.fabric.codepackageactivationcontext.reportdeployedapplicationhealth.aspx) reportar a aplicação atual implementada no nó actual.

- Utilizar [CodePackageActivationContext.ReportDeployedServicePackageHealth](https://msdn.microsoft.com/library/system.fabric.codepackageactivationcontext.reportdeployedservicepackagehealth.aspx) reportar um pacote de serviço para a aplicação atual implementado no nó actual.

> [AZURE.NOTE] Internamente, o `Partition` e o `CodePackageActivationContext` mantenha um cliente de estado de funcionamento que é configurado com as predefinições. Explicação das mesmas considerações para o [cliente de estado de funcionamento](service-fabric-report-health.md#health-client) aplicar - os relatórios são enviadas em batches e enviados de um temporizador, para que os objetos devem ser mantidos em vivos para que a oportunidade de enviar o relatório.

## <a name="design-health-reporting"></a>Estruturar relatórios de estado de funcionamento
O primeiro passo no gerar relatórios de alta qualidade é identificar as condições que podem ter impacto o estado de funcionamento do serviço. Qualquer condição que o pode ajudar a problemas de sinalizador no serviço ou cluster quando inicia – ou melhor ainda, antes de um problema acontece – pode potencialmente guardar milhares de euros. Os benefícios incluem menor vez, menos da noite horas despendidas investigar e reparar problemas e mais elevada satisfação do cliente.

Assim que estão identificadas as condições, escritores watchdog têm descobrir a melhor forma de monitorizá-los para equilíbrio entre gerais e utilidade. Por exemplo, considere um serviço que é que os cálculos complexos que utilizam alguns ficheiros temporários numa partilha. Um watchdog poderia monitorizar partilhar para se certificar de que o espaço suficiente está disponível. Poderá ouvir a para as notificações das alterações de ficheiro ou do diretório. -Poderia report um aviso se um processo decorra limiar for atingido e o report um erro se a opção partilhar está completa. Num aviso, um sistema de reparação conseguiu iniciar limpar os ficheiros mais antigos na partilha. Um erro, um sistema de reparação foi possível mover a réplica do serviço para outro nó. Nota a forma como os Estados de condição são descritos em termos de estado de funcionamento: o estado da condição que pode ser considerado saudável ou danificado (aviso ou erro).

Depois de definir os detalhes de monitorização, necessita de um gravador de watchdog descobrir como implementar o watchdog. Se as condições podem ser determinadas a partir de dentro do serviço, o watchdog pode fazer parte do serviço monitorizado própria. Por exemplo, o código de serviço pode verificar a utilização de partilhar e, em seguida, um relatório utilizando um cliente local ferro sempre tentar escrever um ficheiro. A vantagem desta abordagem é relatórios simples. Cuidados devem ser tomados para impedir que afetam a funcionalidade do serviço, apesar de watchdog erros.

Relatórios a partir do serviço monitorizado nem sempre é uma opção. Um watchdog dentro do serviço poderá não conseguir detetar as condições. Poderá não ter a lógica ou os dados para tornar a imagem. O overhead da monitorização as condições pode ser elevado. As condições também poderão não ser específicas para um serviço, mas em vez disso afeta interações entre os serviços. Outra opção é ter fiscais no cluster como processos separados. Os fiscais simplesmente monitorizam as condições e o relatório, sem afetar os serviços principais de qualquer forma. Por exemplo, estes fiscais podem ser implementadas como sem estado serviços na aplicação do mesmo, implementado em todos os nós ou em nós do mesmo, como o serviço.

Por vezes, um watchdog executadas no cluster não é uma opção quer. Se a condição monitorizada for a disponibilidade ou a funcionalidade do serviço de como os utilizadores veem-lo, é melhor ter os fiscais no mesmo local, como os clientes de utilizador. Não existem, estes podem testar as operações da mesma forma que os utilizadores chamam-los. Por exemplo, pode ter uma watchdog que encontra-se fora do cluster e problemas de pedidos de serviço e, em seguida, verifica a latência e regularidade do resultado. (Para um serviço de calculadora, por exemplo, 2 + 2 devolvem 4 num período de tempo razoável?)

Assim que tiverem sido finalizados os detalhes de watchdog, deverá optar por um ID de origem que identifica-lo. Se vários fiscais do mesmo tipo são vivem no cluster, quer tem um relatório em entidades diferentes ou, se estes relatório sobre a mesma entidade, tem de garantir que o ID de origem ou a propriedade é diferente. Desta forma, podem coexistência seus relatórios. A propriedade de relatório de estado de funcionamento deverá capturar a condição monitorizada. (Por exemplo acima, a propriedade pode ser **ShareSize**.) Se aplicam vários relatórios para a mesma condição, a propriedade deverá conter algumas informações dinâmicas, que permite que os relatórios a coexistência. Por exemplo, se precisar de múltiplas partilhas ser monitorizadas, o nome da propriedade pode ser **ShareSize partilha**.

> [AZURE.NOTE] Arquivo de estado de funcionamento deve *não* ser utilizados para manter as informações de estado. Apenas as informações relacionadas com o estado de funcionamento devem ser comunicadas como do Estado de funcionamento, tal como estas informações impactos da avaliação de estado de funcionamento de uma entidade. Arquivo de estado de funcionamento não foi concebido como um arquivo de uso geral. Utiliza lógica de avaliação de estado de funcionamento para agregar todos os dados para o estado de funcionamento. Enviar informações não relacionadas com o estado de funcionamento (por exemplo, comunicar o estado com um Estado de funcionamento da OK) não irá afetar o estado de funcionamento agregado, mas negativa-pode afetar o desempenho da loja do Estado de funcionamento.

O ponto de decisão seguinte é que entidade ao relatório no. A maior parte das vezes, este é óbvia, com base na condição. Que deverá escolher a entidade com melhor granularidade possível. Se uma condição impactos todas as réplicas numa partição, um relatório na partição, não no serviço. Existem onde pensamento mais for necessário, apesar de casos de canto. Se a condição impactos uma entidade, tal como uma réplica, mas o vontade deve ter a condição sinalizados para a mais do que a duração de vida de réplica, em seguida, deve ser comunicado na partição. Caso contrário, quando a réplica é eliminada, vai ser limpo todos os relatórios associados a partir da loja. Isto significa que escritores watchdog também tem em consideração as durações da entidade e o relatório. Tem de ser limpar quando deve ser limpo um relatório a partir de uma loja (por exemplo, quando já não se aplica um erro comunicado numa entidade).

Vamos ver um exemplo que junta os pontos que posso descrito. Considere uma aplicação de serviço ferro composto por um serviço de persistente com estado principal e secundários serviços sem estado implementados em todos os nós (um tipo de serviço secundário para cada tipo de tarefa). O modelo global de tem uma fila de processamento que contém os comandos que será executada pelo secundários. Os secundários executar os pedidos recebidos e enviar sinais de confirmação anterior. Uma condição que pode ser monitorizada for o comprimento da fila de processamento principal. Se o comprimento da fila mestra atingir um determinado limiar, um aviso é comunicado. O aviso indica que o secundários não consegue processar a carregar. Se a fila de atinge o tamanho máximo e comandos são ignorados, um erro é comunicado, tal como o serviço não é possível recuperar. Os relatórios podem ser na propriedade **QueueStatus**. O watchdog encontra-se no interior do serviço e ter sido enviada periodicamente na réplica primária principal. A time to live é de dois minutos e ter sido enviada periodicamente a cada 30 segundos. Se a página principal vai para baixo, o relatório é desorganizado; automaticamente a partir da loja. Se estiver a réplica do serviço para cima, mas está bloqueada ou outros problemas, o relatório de expira na loja do Estado de funcionamento. Neste caso, a entidade é avaliada no erro.

Outra condição que pode ser monitorizada é o tempo de execução de tarefa. O modelo global de distribui tarefas para secundários consoante o tipo de tarefa. Dependendo da concepção, o modelo global de poderia consultar secundários para o estado da tarefa. Também-poderia aguardar secundários enviar sinais de volta confirmação quando estes terminado. No segundo caso, deve ser cuidado para detetar situações onde são perdidas dado secundários ou de mensagens. Uma das opções é para o modelo global de enviar um pedido de ping para o mesmo secundário, que envia novamente o respetivo estado. Se for recebido sem estado, o modelo global de considera uma falha de e reagendamentos a tarefa. Este comportamento assume que as tarefas são idempotent.

É possível traduzir a condição monitorizada como um aviso se a tarefa não é realizada num determinado período de tempo (**t1**, por exemplo 10 minutos). Se a tarefa não for concluída no tempo (**t2**, por exemplo, 20 minutos), é possível traduzir a condição monitorizada como um erro. Este relatório pode ser feito de várias formas:

- A réplica primária principal relatórios sobre si próprio periodicamente. Pode ter uma propriedade de todas as tarefas pendentes na fila de espera. Se pelo menos uma tarefa demorar mais tempo, o estado de relatório na propriedade **PendingTasks** é um aviso ou um erro, conforme adequado. Se não existem tarefas pendentes ou todas as tarefas iniciado execução, o estado de relatório é OK. As tarefas são persistentes. Se a página principal vai para baixo, a página principal recentemente promovida pode continuar a comunicar corretamente.

- Outro processo watchdog (na nuvem ou externo) verifica se as tarefas (a partir de fora da, com base no resultado de tarefa pretendida) para ver se são concluídas. Se não são respeitar os limiares, um relatório é enviado o serviço de modelo global. Um relatório também é enviado em cada tarefa que inclua o identificador de tarefa, como **PendingTask + taskId**. Relatórios devem ser enviados apenas nos Estados Unidos danificados. Definir a hora de ao vivo para alguns minutos e marque todos os relatórios para ser removido quando expiram para se certificar de limpeza.

- Secundária que está a executar uma tarefa relatórios quando demora mais tempo do que o esperado executá-la. -Relatórios sobre a instância do serviço na propriedade **PendingTasks**. O relatório pinpoints a instância do serviço que tem problemas, mas não de captura a situação onde a instância falecer. Os relatórios são desorganizados; em seguida. Pode comunicar o serviço de secundário. Se secundária conclui a tarefa, a instância secundária limpa o relatório a partir da loja. O relatório não captura a situação onde perde-se a mensagem de confirmação e a tarefa não estiver concluída a partir do ponto de vista do modelo global.

No entanto, o relatório de concluir o processo nos casos descritos acima, os relatórios são captados no estado de funcionamento da aplicação quando o estado de funcionamento é avaliado.

## <a name="report-periodically-vs-on-transition"></a>Relatório periodicamente vs. de transição
Ao utilizar o modelo de relatório de estado de funcionamento, fiscais podem enviar relatórios periodicamente ou no transições. O caminho recomendado para elaboração de relatórios watchdog é periodicamente, porque o código é muito mais simples e menor sujeito a erros. Os fiscais esforça devem ser tão simples como possíveis evitar erros acionam relatórios incorretos. Incorretos *danificado* relatórios impacto avaliações de estado de funcionamento e cenários com base no estado de funcionamento, incluindo atualiza os. Relatórios incorretos *Saudável* ocultar problemas no cluster não pretendido.

Para criar relatórios periódicas, o watchdog pode ser implementada com um temporizador. Numa chamada de retorno do temporizador, o watchdog pode verificar o estado e enviar um relatório de acordo com o estado atual. Não é necessário para ver qual o relatório foi enviado anteriormente ou efetuar quaisquer otimizações em termos de mensagens. O cliente de estado de funcionamento tem lotes lógica para o ajudar com o desempenho. Enquanto o cliente de estado de funcionamento é mantido vivo, internamente tentativas até que o relatório é confirmado pelo arquivo de estado de funcionamento ou o watchdog gera um relatório mais recente com o mesmo entidade, propriedade e origem.

Criar relatórios sobre transições requer o cuidado de processamento de estado. O watchdog monitoriza algumas condições e quando as condições alterar apenas cria relatórios. Pernas desta abordagem são que os relatórios menos são necessárias. A desvantagem é que a lógica da watchdog é complexa. O watchdog tem de manter as condições ou em relatórios, para que possam ser controlados para determinar as alterações de estado. No falha na ligação, tem de ser cuidado quando é enviado um relatório que poderão ainda não foram enviados anteriormente (em espera, mas ainda não foram enviadas para o arquivo de estado de funcionamento). O número de sequência tem de ser crescente. Caso contrário, os relatórios rejeitados como obsoletas. Nos casos raros onde é suportados com o perda de dados, pode ser necessárias sincronização entre o estado do autor e o estado da loja do Estado de funcionamento.

Criar relatórios sobre transições sentido para serviços de comunicação de informações sobre si, através de `Partition` ou `CodePackageActivationContext`. Quando o objeto local (réplica ou pacote de serviço implementado / implementado aplicação) é removida, todos os seus relatórios também são removidos. Este limpeza automática reduz a necessidade de sincronização entre o autor, bem como arquivo de estado de funcionamento. Se o relatório que partição principal ou aplicação principal, devem ser tomados cuidados no activação pós-falha para evitar obsoletos relatórios na loja do Estado de funcionamento. Lógica deve ser adicionada para manter o estado correto e desmarque o relatório a partir da loja quando já não é necessário.

## <a name="implement-health-reporting"></a>Implementar o relatório de estado de funcionamento
Assim que os detalhes do relatório e entidade são claros, enviar relatórios de estado de funcionamento pode ser feito através da API, PowerShell ou restantes.

### <a name="api"></a>API
Para comunicar através da API, é necessário criar um relatório de estado de funcionamento específicas para o tipo de entidade que pretendem incluir no relatório. Atribua ao relatório a um cliente do Estado de funcionamento. Em alternativa, criar um Estado de funcionamento de informações e passam-lo para corrigir os métodos de comunicação no `Partition` ou `CodePackageActivationContext` reportar entidades atuais.

O exemplo seguinte mostra periódico elaboração de relatórios a partir de um watchdog dentro do cluster. O watchdog verifica se um recurso externo pode ser acedido a partir de um nó. O recurso é necessária, por um manifesto dentro da aplicação de serviço. Se o recurso não estiver disponível, os outros serviços dentro da aplicação ainda podem funcionar corretamente. Por conseguinte, o relatório é enviado na entidade de pacote do serviço implementado cada 30 segundos.

```csharp
private static Uri ApplicationName = new Uri("fabric:/WordCount");
private static string ServiceManifestName = "WordCount.Service";
private static string NodeName = FabricRuntime.GetNodeContext().NodeName;
private static Timer ReportTimer = new Timer(new TimerCallback(SendReport), null, 30 * 1000, 30 * 1000);
private static FabricClient Client = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });

public static void SendReport(object obj)
{
    // Test whether the resource can be accessed from the node
    HealthState healthState = this.TestConnectivityToExternalResource();

    // Send report on deployed service package, as the connectivity is needed by the specific service manifest
    // and can be different on different nodes
    var deployedServicePackageHealthReport = new DeployedServicePackageHealthReport(
        ApplicationName,
        ServiceManifestName,
        NodeName,
        new HealthInformation("ExternalSourceWatcher", "Connectivity", healthState));

    // TODO: handle exception. Code omitted for snippet brevity.
    // Possible exceptions: FabricException with error codes
    // FabricHealthStaleReport (non-retryable, the report is already queued on the health client),
    // FabricHealthMaxReportsReached (retryable; user should retry with exponential delay until the report is accepted).
    Client.HealthManager.ReportHealth(deployedServicePackageHealthReport);
}
```

### <a name="powershell"></a>PowerShell
Envie relatórios de estado de funcionamento com * *Enviar ServiceFabric*EntityType*HealthReport * *.

O exemplo seguinte mostra periódico relatórios em valores de CPU num nó. Os relatórios devem ser enviados a cada 30 segundos e que tenham uma hora para a live de dois minutos. Se expirem, o autor tem problemas, de modo que o nó é avaliado no erro. Quando estiver a CPU acima de um determinado limiar, o relatório tem um Estado de funcionamento de aviso. Quando a CPU permanece acima de um determinado limiar para mais do que a hora configurada, é comunicado como um erro. Caso contrário, o autor envia um Estado de funcionamento da OK.

```powershell
PS C:\> Send-ServiceFabricNodeHealthReport -NodeName Node.1 -HealthState Warning -SourceId PowershellWatcher -HealthProperty CPU -Description "CPU is above 80% threshold" -TimeToLiveSec 120

PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='CPU', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 5
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : CPU
                        HealthState           : Warning
                        SequenceNumber        : 130741236814913394
                        SentAt                : 4/21/2015 9:01:21 PM
                        ReceivedAt            : 4/21/2015 9:01:21 PM
                        TTL                   : 00:02:00
                        Description           : CPU is above 80% threshold
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:01:21 PM
```

O exemplo seguinte relatórios um aviso breves uma réplica. -La pela primeira vez obtém o ID da partição e, em seguida, o ID de réplica no serviço que está interessado em. Em seguida, envia um relatório a partir do **PowershellWatcher** na propriedade **ResourceDependency**. O relatório é de interesse para apenas dois minutos e é removida automaticamente a partir da loja.

```powershell
PS C:\> $partitionId = (Get-ServiceFabricPartition -ServiceName fabric:/WordCount/WordCount.Service).PartitionId

PS C:\> $replicaId = (Get-ServiceFabricReplica -PartitionId $partitionId | where {$_.ReplicaRole -eq "Primary"}).ReplicaId

PS C:\> Send-ServiceFabricReplicaHealthReport -PartitionId $partitionId -ReplicaId $replicaId -HealthState Warning -SourceId PowershellWatcher -HealthProperty ResourceDependency -Description "The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes." -TimeToLiveSec 120 -RemoveWhenExpired

PS C:\> Get-ServiceFabricReplicaHealth  -PartitionId $partitionId -ReplicaOrInstanceId $replicaId


PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
ReplicaId             : 130740415594605869
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='ResourceDependency', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130740768777734943
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : ResourceDependency
                        HealthState           : Warning
                        SequenceNumber        : 130741243777723555
                        SentAt                : 4/21/2015 9:12:57 PM
                        ReceivedAt            : 4/21/2015 9:12:57 PM
                        TTL                   : 00:02:00
                        Description           : The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:12:32 PM
```

### <a name="rest"></a>RESTO
Envie relatórios de estado de funcionamento utilizando o resto com pedidos de mensagem que aceda à entidade pretendida e que têm no corpo a descrição do relatório de estado de funcionamento. Por exemplo, consulte o artigo como enviar resto [relatórios de estado de funcionamento de cluster](https://msdn.microsoft.com/library/azure/dn707640.aspx) ou [relatórios de estado de funcionamento do serviço](https://msdn.microsoft.com/library/azure/dn707640.aspx). Todas as entidades são suportadas.

## <a name="next-steps"></a>Próximos passos

Escritores do serviço e administradores de cluster/application com base nos dados do Estado de funcionamento, poderá pensar de formas de consumir as informações. Por exemplo, eles podem configurar alertas com base no estado de funcionamento capturas extremas problemas antes de estes provocar de corrente. Os administradores também podem configurar os sistemas de reparação para corrigir automaticamente problemas.

[Introdução ao estado de funcionamento do serviço ferro Monitoring](service-fabric-health-introduction.md)

[Ver relatórios de estado de funcionamento do serviço ferro](service-fabric-view-entities-aggregated-health.md)

[Como um relatório e verificar o estado de funcionamento do serviço](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Utilizar relatórios de estado de funcionamento do sistema para resolução de problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Monitorizar e diagnosticar serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Atualização da aplicação de serviço ferro](service-fabric-application-upgrade.md)
