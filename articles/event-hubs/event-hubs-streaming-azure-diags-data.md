<properties
    pageTitle="Transmissão de dados de diagnósticos do Azure no caminho de quente utilizando o evento concentradores | Microsoft Azure"
    description="Ilustra como configurar diagnósticos do Azure com concentradores evento a partir do fim para fim, incluindo a seta de quatro pontas cenários comuns."
    services="event-hubs"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags
    ms.service="event-hubs"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="07/14/2016"
    ms.author="sethm" />

# <a name="streaming-azure-diagnostics-data-in-the-hot-path-by-using-event-hubs"></a>Transmissão de dados de diagnósticos do Azure no caminho quente utilizando concentradores de evento

Diagnósticos do Azure fornece formas flexíveis para recolher registos e métricas de cloud services virtual máquinas (VMs) e transferir os resultados para o armazenamento do Windows Azure. Iniciar no período de tempo de Março de 2016 (SDK 2.9), pode receber diagnósticos a origens de dados personalizada completamente e transferir dados de caminho quente em segundos, utilizando o [Azure evento concentradores](https://azure.microsoft.com/services/event-hubs/).

Tipos de dados suportados incluem:

- Eventos de rastreio para Windows (ETW) do evento
- Contadores de desempenho
- Registos de eventos do Windows
- Registos de aplicação
- Registos de infraestrutura de diagnósticos Azure

Este artigo mostra-lhe como configurar diagnósticos do Azure com concentradores evento a partir do fim para fim. Orientações também são fornecida para os seguintes cenários comuns:

- Como personalizar registos e métricas obter sinked a concentradores de evento
- Como alterar as configurações de cada ambiente
- Como ver dados em sequência de concentradores de evento
- Como resolver problemas com a ligação  

## <a name="prerequisites"></a>Pré-requisitos

Evento concentradores perfuração no Azure diagnósticos é suportada no serviços em nuvem, VMs, conjuntos de escala de Máquina Virtual e serviço ferro inicial na 2.9 SDK Azure e as ferramentas de Azure correspondente para o Visual Studio.

- Extensão de diagnóstico Azure 1.6 ([SDK Azure para .NET 2.9 ou posterior](https://azure.microsoft.com/downloads/) destina-se isto por predefinição)
- [Visual Studio 2013 ou posterior](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
- Configurações de existentes dos diagnósticos do Azure numa aplicação com um ficheiro de *.wadcfgx* e um dos seguintes métodos:
    - [Configurar diagnósticos para serviços em nuvem Azure e máquinas virtuais](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md) do Visual Studio:
    - Windows PowerShell: [activar diagnósticos no Azure serviços em nuvem através do PowerShell](../cloud-services/cloud-services-diagnostics-powershell.md)
- Espaço de nomes do concentradores evento aprovisionado pelo artigo [Introdução ao evento concentradores](./event-hubs-csharp-ephcs-getstarted.md)

## <a name="connect-azure-diagnostics-to-event-hubs-sink"></a>Ligar diagnósticos do Azure a sink concentradores de evento

Diagnósticos do Azure afunde sempre registos de início e de métricas, por predefinição, a uma conta de armazenamento do Windows Azure. Uma aplicação para além disso poderá receber a concentradores evento ao adicionar uma nova secção **sumidouros** para o elemento **WadCfg** na secção **PublicConfig** do ficheiro *.wadcfgx* . No Visual Studio, o ficheiro de *.wadcfgx* está armazenado no seguinte caminho: **Projecto de serviço de nuvem** > **funções** >  **(nome da função)** > **diagnostics.wadcfgx** ficheiro.

```
<SinksConfig>
  <Sink name="HotPath">
    <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" />
  </Sink>
</SinksConfig>
```

Neste exemplo, o URL do concentrador de evento está definido para o espaço de nomes completamente qualificado do concentrador de evento: espaço de nomes do evento concentradores + "/" + nome do concentrador de evento.  

O URL do concentrador de evento é apresentado no [portal do Azure](http://go.microsoft.com/fwlink/?LinkID=213885) no dashboard de evento concentradores.  

O nome de **receber** pode ser definido para qualquer cadeia válida desde que o mesmo valor é utilizado de forma consistente ao longo do ficheiro de configuração.

> [AZURE.NOTE]  Poderão existir sumidouros adicionais, tal como *applicationInsights* configurado nesta secção. Diagnósticos do Azure permite uma ou mais sumidouros ser definidos se cada sink também for declarado na secção **PrivateConfig** .  

O evento concentradores sink também pode ser declarado e definido na secção **PrivateConfig** do ficheiro de configuração de *.wadcfgx* .

```
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <StorageAccount name="" key="" endpoint="" />
  <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="9B3SwghJOGEUvXigc6zHPInl2iYxrgsKHZoy4nm9CUI=" />
</PrivateConfig>
```

O `SharedAccessKeyName` valor tem de corresponder uma política que tenha sido definida no **Evento concentradores** espaço de nomes e chave assinatura partilhadas do Access (SA). Navegue até ao dashboard de evento concentradores no [portal do Azure](https://manage.windowsazure.com), clique no separador **Configurar** e configurar uma política com nome (por exemplo, "SendRule") que possua permissões de *Enviar* . O **StorageAccount** também é declarado na **PrivateConfig**. Não é necessário para alterar os valores aqui se estiverem a trabalhar. Neste exemplo, vamos deixe os valores vazio, que é um sinal de que um activo descendentes vai definir os valores. Por exemplo, o ficheiro de configuração de ambiente de *ServiceConfiguration.Cloud.cscfg* define as teclas e ambiente adequados nomes.  

> [AZURE.WARNING] A chave de evento concentradores SA é armazenada em texto simples no ficheiro *.wadcfgx* . Muitas vezes, esta tecla der entrada para controlo de código fonte ou está disponível como um activo no seu servidor de compilação, pelo que deverá protegê-lo conforme adequado. Recomendamos que utilize uma chave de SA aqui com permissões de *Enviar apenas* para que um utilizador malicioso pode escrever no centro do evento, mas não ouvi-la ou geri-lo.

## <a name="configure-azure-diagnostics-logs-and-metrics-to-sink-with-event-hubs"></a>Configurar os registos de diagnóstico do Azure e métricas receber com concentradores de evento

Como debatido anteriormente, todos os dados de diagnóstico personalizados e predefinidos, ou seja, métricas e registos, é automaticamente sinked ao armazenamento do Azure nos intervalos configurados. Com o evento concentradores e qualquer sink adicional, pode especificar qualquer nó raiz ou folha na hierarquia de ser sinked com o concentrador de evento. Isto inclui os eventos ETW, contadores de desempenho, registos de eventos do Windows e registos da aplicação.   

É importante que considere quantos pontos de dados realmente devem ser transferidos para o evento concentradores. Normalmente, os programadores transferir baixa latência-path dados que tem de ser consumidos e interpretados rapidamente. Sistemas monitorizar alertas ou autoscale regras são exemplos. Um programador também poderá configurar um arquivo de análise alternativo ou procurar a loja – por exemplo, a análise da cadeia de Azure, Elasticsearch, um sistema de monitorização personalizado ou um sistema de monitorização favorito a partir de outras pessoas.

Seguem-se algumas configurações de exemplo.

```
<PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
  <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
</PerformanceCounters>
```

No exemplo seguinte, o sink é aplicado a nó **PerformanceCounters** principal da hierarquia, o que significa que todos os subordinados **PerformanceCounters** serão enviadas para o evento concentradores.  

```
<PerformanceCounters scheduledTransferPeriod="PT1M">
  <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" sinks="HotPath" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" sinks="HotPath"/>
  <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" sinks="HotPath"/>
</PerformanceCounters>
```

No exemplo anterior, o sink é aplicado a apenas três contadores: **Pedidos na fila**, **Rejeitadas pedidos**e o **tempo de processador %**.  

O exemplo seguinte mostra como um programador pode limitar a quantidade de dados enviados para ser métricas críticas que são usadas para o estado de funcionamento deste serviço.  

```
<Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
```

Neste exemplo, o sink é aplicado aos registos e é filtrado apenas para o rastreio de nível do erro.

## <a name="deploy-and-update-a-cloud-services-application-and-diagnostics-config"></a>Implementar e atualizar a configuração de aplicação e diagnósticos de serviços em nuvem

O Visual Studio fornece o caminho mais fácil para implementar a aplicação e configuração do evento concentradores sink. Para ver e editar o ficheiro, abra o ficheiro de *.wadcfgx* no Visual Studio, editá-lo e guardá-lo. O caminho é **Projecto de serviço de nuvem** > **funções** > **(nome da função)** > **diagnostics.wadcfgx**.  

Neste momento, todos os implementação e implementação ações no Visual Studio, Visual Studio Team System e todos os comandos ou scripts que são baseados no MSBuild e a utilização de atualizar o **/t: publicar** destino incluir o *.wadcfgx* no processo de embalagem. Além disso, implementações e atualizações implementar o ficheiro Azure utilizando a extensão do agente de diagnósticos do Azure adequada no seu VMs.

Depois de implementar a aplicação e a configuração de diagnósticos do Azure, irá ver imediatamente atividade no dashboard do centro do evento. Isto indica que está pronto para avançar para ver os dados de caminho quente na ferramenta de cliente ou de análise de escuta da sua escolha.  

Na figura seguinte, o dashboard do evento concentradores mostra enviar saudável de dados de diagnóstico para o concentrador de evento com início algures após 11 PM. Que é quando a aplicação foi implementada com um ficheiro atualizado *.wadcfgx* e o sink tiver sido configurado corretamente.

![][0]  

> [AZURE.NOTE] Quando efetuar atualizações para o ficheiro de configuração do Azure diagnósticos (.wadcfgx), recomenda-se que emissão as atualizações para a aplicação completa, bem como a configuração através de publicação do Visual Studio ou um script do Windows PowerShell.  

## <a name="view-hot-path-data"></a>Ver dados de-path

Tal como é abordado anteriormente, existem muitos casos de utilização para ouvir e processar dados de evento concentradores.

Uma abordagem simple é criar uma aplicação de consola de teste pequenas para ouvir o concentrador de evento e imprimir na sequência de saída. Pode colocar o código seguinte, o que é explicado mais detalhadamente na [Introdução ao evento concentradores](./event-hubs-csharp-ephcs-getstarted.md)), numa aplicação de consola.  

Tenha em atenção que a aplicação da consola tem de incluir o [pacote de Nuget de anfitrião de processador de eventos](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/).  

Lembre-se substituir os valores entre parênteses em ângulo na função **principal** com valores para os recursos.   

```
//Console application code for EventHub test client
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace EventHubListener
{
    class SimpleEventProcessor : IEventProcessor
    {
        Stopwatch checkpointStopWatch;

        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
            if (reason == CloseReason.Shutdown)
            {
                await context.CheckpointAsync();
            }
        }

        Task IEventProcessor.OpenAsync(PartitionContext context)
        {
            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
            this.checkpointStopWatch = new Stopwatch();
            this.checkpointStopWatch.Start();
            return Task.FromResult<object>(null);
        }

        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (EventData eventData in messages)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                    Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                        context.Lease.PartitionId, data));

                foreach (var x in eventData.Properties)
                {
                    Console.WriteLine(string.Format("    {0} = {1}", x.Key, x.Value));
                }
            }

            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string eventHubConnectionString = "Endpoint= <your connection string>”
            string eventHubName = "<Event Hub name>";
            string storageAccountName = "<Storage account name>";
            string storageAccountKey = "<Storage account key>”;
            string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);

            string eventProcessorHostName = Guid.NewGuid().ToString();
            EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
            Console.WriteLine("Registering EventProcessor...");
            var options = new EventProcessorOptions();
            options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
            eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();

            Console.WriteLine("Receiving. Press enter key to stop worker.");
            Console.ReadLine();
            eventProcessorHost.UnregisterEventProcessorAsync().Wait();
        }
    }
}
```

## <a name="troubleshoot-event-hubs-sink"></a>Resolver problemas de evento concentradores sink

- Centro do evento não mostrar a atividade de evento recebidas ou enviadas, tal como é esperado.

    Verifique que o seu centro de evento com êxito está aprovisionado. Todas as informações de ligação na secção de **PrivateConfig** da *.wadcfgx* tem de corresponder os valores do seu recurso de conforme visto no portal. Certifique-se de que tem uma política de SA definida ("SendRule" no exemplo) no portal e que é concedida permissão *Enviar* .  

- Após uma atualização, ao centro do evento já não mostra atividade de evento recebidas ou enviadas.

    Em primeiro lugar, certifique-se de que as informações do concentrador de evento e a configuração estão correta conforme explicado anteriormente. Por vezes o **PrivateConfig** é reposta numa actualização de implementação. É recomendado corrigir efetuar todas as alterações à *.wadcfgx* no projeto e, em seguida, emissão uma atualização de aplicação completo. Se não for possível, certifique-se de que a atualização de diagnóstico emite concluída **PrivateConfig** que inclui a chave de SA.  

- Tentei as sugestões e centro do evento ainda não está a funcionar.

    Experimente está à procura da tabela de armazenamento do Windows Azure que contém registos de início e de erros para diagnósticos do Azure propriamente dito: **WADDiagnosticInfrastructureLogsTable**. Uma das opções é utilizar uma ferramenta de como o [Explorador de armazenamento do Azure](http://www.storageexplorer.com) para ligar a esta conta de armazenamento, ver esta tabela e adicione uma consulta para data/hora na últimas 24 horas. Pode utilizar a ferramenta para exportar um ficheiro. csv e abra-o numa aplicação como o Microsoft Excel. Excel torna mais fácil procurar cadeias de cartão de crédito telefónico, tal como **EventHubs**, para ver qual o erro é comunicado.  

## <a name="next-steps"></a>Próximos passos

• [Saber mais sobre o evento concentradores](https://azure.microsoft.com/services/event-hubs/)

## <a name="appendix-complete-azure-diagnostics-configuration-file-wadcfgx-example"></a>Anexo: Concluir o exemplo de ficheiro (.wadcfgx) de configuração de diagnósticos do Azure

```
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
        <Directories scheduledTransferPeriod="PT1M">
          <IISLogs containerName="wad-iis-logfiles" />
          <FailedRequestLogs containerName="wad-failedrequestlogs" />
        </Directories>
        <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
        </PerformanceCounters>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*" />
        </WindowsEventLog>
        <CrashDumps>
          <CrashDumpConfiguration processName="WaIISHost.exe" />
          <CrashDumpConfiguration processName="WaWorkerHost.exe" />
          <CrashDumpConfiguration processName="w3wp.exe" />
        </CrashDumps>
        <Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
      </DiagnosticMonitorConfiguration>
      <SinksConfig>
        <Sink name="HotPath">
          <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" />
        </Sink>
        <Sink name="applicationInsights">
          <ApplicationInsights />
          <Channels>
            <Channel logLevel="Error" name="errors" />
          </Channels>
        </Sink>
      </SinksConfig>
    </WadCfg>
    <StorageAccount />
  </PublicConfig>
  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <StorageAccount name="" key="" endpoint="" />
    <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" SharedAccessKey="YOUR_KEY_HERE" />
  </PrivateConfig>
  <IsEnabled>true</IsEnabled>
</DiagnosticsConfiguration>
```

O complementar *ServiceConfiguration.Cloud.cscfg* para este exemplo tem o seguinte aspeto.

```
<?xml version="1.0" encoding="utf-8"?>
<ServiceConfiguration serviceName="MyFixItCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2015-04.2.6">
  <Role name="MyFixIt.WorkerRole">
    <Instances count="1" />
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="YOUR_CONNECTION_STRING" />
    </ConfigurationSettings>
  </Role>
</ServiceConfiguration>
```

<!-- Images. -->
[0]: ./media/event-hubs-streaming-azure-diags-data/dashboard.png
