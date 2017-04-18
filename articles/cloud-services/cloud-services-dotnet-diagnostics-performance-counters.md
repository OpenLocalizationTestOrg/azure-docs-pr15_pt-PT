<properties
   pageTitle="Utilize contadores de desempenho no Azure diagnóstico | Microsoft Azure"
   description="Utilize contadores de desempenho no serviços em nuvem Azure ou máquina virtual para localizar congestionamentos e o desempenho."
   services="cloud-services"
   documentationCenter=".net"
   authors="rboucher"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/29/2016"
   ms.author="robb" />

# <a name="create-and-use-performance-counters-in-an-azure-application"></a>Criar e utilizar contadores de desempenho numa aplicação do Azure

Este artigo descreve os benefícios da e como colocar contadores de desempenho para as aplicações do Azure. Pode utilizá-los para recolher dados, localizar congestionamentos e o desempenho do sistema e de aplicações.

Também pode ser recolhidos e utilizado para determinar a saúde dos seus funções Azure web, funções de trabalho e máquinas virtuais desempenho dos contadores disponíveis para o Windows Server, IIS e ASP.NET. Também pode criar e utilizar contadores de desempenho personalizado.  

Pode analisar dados de contador de desempenho
1. Diretamente no anfitrião do aplicação com a ferramenta de Monitor de desempenho aceder às mesmas através do ambiente de trabalho remoto
2. Com o Gestor de operações do Centro de sistema que o pacote de gestão do Azure
3. Com outras ferramentas de monitorização aceder aos dados diagnóstico transferidos para o armazenamento Azure. Para mais informações, consulte [armazenar e visualizar dados diagnóstico armazenamento do Windows Azure](https://msdn.microsoft.com/library/azure/hh411534.aspx) .  

Para mais informações sobre como monitorizar o desempenho da sua aplicação no [portal clássica Azure](http://manage.azure.com/), consulte o artigo [como aos serviços em nuvem Monitor](https://www.azure.com/manage/services/cloud-services/how-to-monitor-a-cloud-service/).

Para adicional orientação detalhada sobre a criação de um registo e rastreio estratégia e através de diagnóstico e outras técnicas para resolver problemas e otimizar Azure aplicações, consulte o artigo [Resolução de problemas de melhores práticas para desenvolver aplicações Azure](https://msdn.microsoft.com/library/azure/hh771389.aspx).


## <a name="enable-performance-counter-monitoring"></a>Ativar a monitorização de contador de desempenho

Contadores de desempenho não estão ativados por predefinição. A aplicação ou uma tarefa de arranque tem de modificar a configuração do agente de diagnóstico predefinido para incluir os contadores de desempenho específicos que pretende monitorizar para cada instância de função.

### <a name="performance-counters-available-for-microsoft-azure"></a>Contadores de desempenho disponíveis para Microsoft Azure

Azure fornece um subconjunto dos contadores de desempenho disponíveis para o Windows Server, IIS e na pilha ASP.NET. A tabela seguinte lista algumas dos contadores de desempenho de interesse específico para aplicações do Azure.

|Categoria de contador: Objeto (instância)|Nome do contador      |Referência|
|---|---|---|
|.NET CLR exceções (_Global_)|# Excepções iniciadas / seg   |Contadores de desempenho de exceção|
|Memória de .NET CLR (_Global_)    |% De tempo no GC            |Contadores de desempenho de memória|
|ASP.NET                      |Aplicação reinicia    |Desempenho contadores do ASP.NET|
|ASP.NET                      |Tempo de execução do pedido  |Desempenho contadores do ASP.NET|
|ASP.NET                      |Pedidos de perder a ligação   |Desempenho contadores do ASP.NET|
|ASP.NET                      |Trabalhador processo reinicia |Desempenho contadores do ASP.NET|
|Aplicações do ASP.NET (__Total__)|Total de pedidos        |Desempenho contadores do ASP.NET|
|Aplicações do ASP.NET (__Total__)|Pedidos de seg          |Desempenho contadores do ASP.NET|
|V4.0.30319 do ASP.NET           |Tempo de execução do pedido  |Desempenho contadores do ASP.NET|
|V4.0.30319 do ASP.NET           |Pedido de tempo de espera       |Desempenho contadores do ASP.NET|
|V4.0.30319 do ASP.NET           |Pedidos atual        |Desempenho contadores do ASP.NET|
|V4.0.30319 do ASP.NET           |Pedidos em fila         |Desempenho contadores do ASP.NET|
|V4.0.30319 do ASP.NET           |Pedidos rejeitados       |Desempenho contadores do ASP.NET|
|Memória                       |MB disponíveis        |Contadores de desempenho de memória|
|Memória                       |Consolidada Bytes         |Contadores de desempenho de memória|
|( Total)            |Processador % de tempo        |Desempenho contadores do ASP.NET|
|TCPv4                        |Falhas de ligação     |Objeto TCP|
|TCPv4                        |Ligações estabelecidas |Objeto TCP|
|TCPv4                        |Repor ligações       |Objeto TCP|
|TCPv4                        |Segmentos enviados/seg.       |Objeto TCP|
|Interface(*) de rede         |Bytes recebidos/seg.      |Objecto de Interface de rede|
|Interface(*) de rede         |Bytes enviados/seg.          |Objecto de Interface de rede|
|Interface de rede (Microsoft Máquina Virtual Bus rede adaptador -2)|Bytes recebidos/seg.|Objecto de Interface de rede|
|Interface de rede (Microsoft Máquina Virtual Bus rede adaptador -2)|Bytes enviados/seg.|Objecto de Interface de rede|
|Interface de rede (Microsoft Máquina Virtual Bus rede adaptador -2)|Total de bytes/seg|Objecto de Interface de rede|

## <a name="create-and-add-custom-performance-counters-to-your-application"></a>Criar e adicionar contadores de desempenho personalizado à aplicação

Azure tem suporte para criar e modificar contadores de desempenho personalizado para web funções e funções de trabalho. Os contadores podem ser utilizados para registar e monitorizar o comportamento de específicos da aplicação. Pode criar e eliminar categorias de contador de desempenho personalizadas e especificadores de uma tarefa de arranque, função web ou função de trabalho com permissões elevadas.

>[AZURE.NOTE] Código que faz com que as alterações aos contadores de desempenho personalizado tem de ter permissões para executar elevadas. Se o código for numa função de web ou a função de trabalho, a função de tem de incluir a etiqueta <Runtime executionContext="elevated" /> no ficheiro ServiceDefinition.csdef para a função a inicialização corretamente.

Pode enviar dados de contador de desempenho personalizado ao armazenamento Azure utilizando o agente diagnóstico.

Os dados de contador de desempenho padrão são gerados pelos processos Azure. Dados de contador de desempenho personalizado devem ser criados pela aplicação web do papel ou trabalhador função. Consulte o artigo [Tipos de contador de desempenho](https://msdn.microsoft.com/library/z573042h.aspx) para obter informações sobre os tipos de dados que podem ser armazenados em contadores de desempenho personalizado. Consulte o artigo [PerformanceCounters exemplo](http://code.msdn.microsoft.com/azure/) para obter um exemplo que cria e conjuntos de dados do contador de desempenho personalizada numa função de web.

## <a name="store-and-view-performance-counter-data"></a>Armazenar e vista de dados de contador de desempenho

Azure coloca em cache dados de contador de desempenho com outras informações de diagnóstico. Estes dados estão disponíveis para monitorização remota enquanto a instância de função estiver em execução com acesso ao ambiente de trabalho remoto para ver as ferramentas, como o Monitor de desempenho. Para manter os dados fora da instância de função, o agente de diagnóstico tem de transferir os dados ao armazenamento Azure. O limite de tamanho de em cache contador de dados de desempenho pode ser configurado no agente do diagnóstico ou este pode ser configurado para fazer parte de um limite partilhado para todos os dados de diagnóstico. Para mais informações sobre como definir o tamanho do intervalo de tempo, consulte o artigo [OverallQuotaInMB](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitorconfiguration.overallquotainmb.aspx) e [DirectoriesBufferConfiguration](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.directoriesbufferconfiguration.aspx). Consulte o artigo [arquivo e visualizar dados diagnóstico no Azure armazenamento](https://msdn.microsoft.com/library/azure/hh411534.aspx) para uma descrição geral de como configurar o agente de diagnóstico para transferir dados para uma conta de armazenamento.

Cada instância do contador de desempenho configurado estiver registada com uma taxa de amostragem especificado e os dados recolhidos são transferidos para a conta de armazenamento por um pedido de transferência agendada ou um pedido de transferência a pedido. Transferências automáticas podem ser agendadas tão frequentemente uma vez por minuto. Dados de contador de desempenho transferidos pelo agente do diagnóstico são armazenados numa tabela, WADPerformanceCountersTable, na conta de armazenamento. Esta tabela pode ser acedida e consultada com métodos de armazenamento Azure padrão API. Consulte o artigo [Microsoft Azure PerformanceCounters exemplo](http://code.msdn.microsoft.com/Windows-Azure-PerformanceCo-7d80ebf9) de um exemplo de consulta e apresentar dados de contador de desempenho a partir da tabela WADPerformanceCountersTable.

>[AZURE.NOTE] Dependendo de frequência de transferência do agente diagnóstico e de latência fila de espera, os dados mais recentes do contador de desempenho na conta de armazenamento poderão ter alguns minutos desatualizada.

## <a name="enable-performance-counters-using-diagnostics-configuration-file"></a>Ativar contadores de desempenho utilizando o ficheiro de configuração de diagnóstico

Utilize o seguinte procedimento para ativar contadores de desempenho na sua aplicação Azure.

## <a name="prerequisites"></a>Pré-requisitos

Esta secção assume que tiver importado o monitor de diagnóstico para a aplicação e adicionado o ficheiro de configuração de diagnóstico a sua solução do Visual Studio (diagnostics.wadcfg na SDK 2.4 e abaixo ou diagnostics.wadcfgx na SDK 2,5 e acima). Consulte os passos 1 e 2 na [Ativação diagnóstico no Azure serviços em nuvem e máquinas virtuais](./cloud-services-dotnet-diagnostics.md)) para obter mais informações.

## <a name="step-1-collect-and-store-data-from-performance-counters"></a>Passo 1: Recolher e armazenar dados a partir de contadores de desempenho

Depois de ter adicionado o ficheiro do diagnóstico a sua solução do Visual Studio pode configurar a recolha e armazenamento dos dados de contador de desempenho numa aplicação do Azure. Isto é feito adicionando contadores de desempenho para o ficheiro de diagnóstico. Dados de diagnóstico, incluindo contadores de desempenho, são recolhidos pela primeira vez na instância. Os dados, em seguida, são mantidos à tabela WADPerformanceCountersTable no serviço de tabela do Azure, pelo que também será necessário especificar a conta de armazenamento na sua aplicação. Se estiver a testar a aplicação localmente emulador de calcular, também pode armazenar dados da diagnóstico localmente no emulador de armazenamento. Antes de armazenar dados diagnósticos tem de aceder ao [portal clássica Azure](http://manage.windowsazure.com/) e criar uma conta de armazenamento. É aconselhável localizar a sua conta de armazenamento na mesma geo-localização como as aplicações do Azure para evitar pagamentos custos de largura de banda externa e para reduzir a latência.

### <a name="add-performance-counters-to-the-diagnostics-file"></a>Adicionar contadores de desempenho para o ficheiro de diagnóstico

Existem muitos contadores que pode utilizar. O exemplo seguinte mostra várias contadores de desempenho que lhe foram recomendados para a web e monitorização da função de trabalho.

Abra o ficheiro de diagnóstico (diagnostics.wadcfg na SDK 2.4 e abaixo ou diagnostics.wadcfgx na SDK 2,5 e acima) e adicione o seguinte para o elemento DiagnosticMonitorConfiguration:

```
    <PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
       <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT30S" />

    <!-- Use the Process(w3wp) category counters in a web role -->

       <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\% Processor Time" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Private Bytes" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Thread Count" sampleRate="PT30S" />

    <!-- Use the Process(WaWorkerHost) category counters in a worker role.
       <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\% Processor Time" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Private Bytes" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Thread Count" sampleRate="PT30S" />
    -->

       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Interop(_Global_)\# of marshalling" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Loading(_Global_)\% Time Loading" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR LocksAndThreads(_Global_)\Contention Rate / sec" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Memory(_Global_)\# Bytes in all Heaps" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Networking(_Global_)\Connections Established" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Remoting(_Global_)\Remote Calls/sec" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Jit(_Global_)\% Time in Jit" sampleRate="PT30S" />
    </PerformanceCounters>
```

O atributo bufferQuotaInMB, que especifica a quantidade máxima de armazenamento de sistema de ficheiros que está disponível para o tipo de recolha de dados (registos Azure, registos do IIS, etc.). A predefinição é 0. Quando a quota de é atingida, os dados mais antigo são eliminados conforme são adicionados novos dados. A soma de todas as propriedades de bufferQuotaInMB tem de ser maior que o valor do atributo OverallQuotaInMB. Para obter informações mais detalhadas de determinar a quantidade de armazenamento irão ser necessário para a recolha de dados do diagnóstico, consulte a secção WAD de configuração de [Resolução de problemas de melhores práticas para desenvolver aplicações Azure](https://msdn.microsoft.com/library/windowsazure/hh771389.aspx).

O atributo scheduledTransferPeriod, que especifica o intervalo entre as transferências de dados agendadas, arredondado por excesso para o minuto mais próximo. Nos exemplos seguintes está definido para PT30M (30 minutos). Definir o período de transferência para um valor de pequenas, tal como 1 minuto, será um impacto adverso na desempenho da sua aplicação de produção mas podem ser úteis para diagnóstico está a ver rapidamente a trabalhar quando estiver a testar. O período de transferência agendada deve ser pequeno o suficiente para se certificar de que os dados de diagnóstico não são substituídos na instância, mas grande o suficiente para que não afectar o desempenho da sua aplicação.

O atributo counterSpecifier Especifica o contador de desempenho para recolher. O atributo sampleRate Especifica a taxa no qual o contador de desempenho deve colher, neste caso 30 segundos.

Depois de adicionar os contadores de desempenho que pretende recolher, guarde as alterações ao ficheiro diagnóstico. Em seguida, tem de especificar a conta de armazenamento serão mantidos os dados diagnóstico.

### <a name="specify-the-storage-account"></a>Especificar a conta de armazenamento

Para manter as suas informações de diagnóstico para a sua conta de armazenamento do Windows Azure, tem de especificar uma cadeia de ligação no ficheiro de configuração (ServiceConfiguration.cscfg) de serviço.

Para Azure SDK 2,5 pode ser especificada a conta de armazenamento no ficheiro diagnostics.wadcfgx.

>[AZURE.NOTE] Estas instruções aplicam-se apenas e Azure SDK 2,4 e abaixo. Para Azure SDK 2,5 pode ser especificada a conta de armazenamento no ficheiro diagnostics.wadcfgx.

Para definir as cadeias de ligação:

1. Abra o ficheiro de ServiceConfiguration.Cloud.cscfg utilizando o editor de texto Favoritos e defina a cadeia de ligação para o seu armazenamento. Os valores *AccountName* e *AccountKey* encontram-se no portal do Azure clássico no dashboard de conta de armazenamento, em Gerir chaves.

    ```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>"/>
    </ConfigurationSettings>
    ```
2. Guarde o ficheiro ServiceConfiguration.Cloud.cscfg.

3. Abra o ficheiro ServiceConfiguration.Local.cscfg e certifique-se de que UseDevelopmentStorage está definido como verdadeiro.

    ```
    <ConfigurationSettings>
      <Settingname="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true"/>
    </ConfigurationSettings>
    ```
Agora que estão definidas as cadeias de ligação, a sua aplicação vai permanecer dados diagnósticos à sua conta de armazenamento quando a aplicação é implementada.
4. Guardar e criar o seu projeto e, em seguida, implementar a aplicação.

## <a name="step-2-optional-create-custom-performance-counters"></a>Passo 2: Contadores de desempenho personalizada criar (opcional)

Além de contadores desempenho predefinidos, pode adicionar seus próprio contadores de desempenho personalizado para monitorizar a funções web ou de trabalho. Contadores de desempenho personalizados podem ser utilizados para registar e monitorizar o comportamento de específicos da aplicação e podem ser criados ou eliminados numa tarefa de arranque, função web ou função de trabalho com permissões elevadas.

O agente de diagnóstico Azure atualiza a desempenho contador configuração do ficheiro .wadcfg após o início de um minuto.  Se criar contadores de desempenho personalizado no método OnStart e as tarefas de arranque demoram mais de um minuto executar, seu contadores de desempenho personalizado serão não ter sido criados quando o agente de diagnósticos do Azure tenta carregá-los.  Neste cenário verá que diagnósticos do Azure corretamente para capturar a todos os dados do diagnóstico exceto os contadores de desempenho personalizado.  Para resolver este problema, crie os contadores de desempenho numa tarefa de arranque ou mover alguns da sua tarefa de arranque funcionam para o método de OnStart depois de criar os contadores de desempenho.

Execute os seguintes passos para criar um desempenho personalizado simple contador com o nome "\MyCustomCounterCategory\MyButton1Counter":

1. Abra o ficheiro de definição de serviço (CSDEF) para a sua aplicação.
2. Adicione o elemento de Runtime para o WebRole ou elemento WorkerRole para permitir a execução com privilégios elevados:

    ```
    <runtime executioncontext="elevated"/>
    ```
3. Guarde o ficheiro.
4. Abra o ficheiro de diagnóstico (diagnostics.wadcfg na SDK 2.4 e abaixo ou diagnostics.wadcfgx na SDK 2,5 e acima) e adicione o seguinte para o DiagnosticMonitorConfiguration 

    ```
    <PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
     <PerformanceCounterConfiguration counterSpecifier="\MyCustomCounterCategory\MyButton1Counter" sampleRate="PT30S"/>
    </PerformanceCounters>
    ```
5. Guarde o ficheiro.
6. Crie a categoria de contador de desempenho personalizada no método de OnStart do seu perfil, antes de invocar base. OnStart. O exemplo seguinte c# cria uma categoria personalizada, se ainda não existir:

    ```
    public override bool OnStart()
    {
    if (!PerformanceCounterCategory.Exists("MyCustomCounterCategory"))
    {
       CounterCreationDataCollection counterCollection = new CounterCreationDataCollection();

       // add a counter tracking user button1 clicks
       CounterCreationData operationTotal1 = new CounterCreationData();
       operationTotal1.CounterName = "MyButton1Counter";
       operationTotal1.CounterHelp = "My Custom Counter for Button1";
       operationTotal1.CounterType = PerformanceCounterType.NumberOfItems32;
       counterCollection.Add(operationTotal1);

       PerformanceCounterCategory.Create(
         "MyCustomCounterCategory",
         "My Custom Counter Category",
         PerformanceCounterCategoryType.SingleInstance, counterCollection);

       Trace.WriteLine("Custom counter category created.");
    }
    else{
       Trace.WriteLine("Custom counter category already exists.");
    }

    return base.OnStart();
    }
    ```
7. Atualize os contadores dentro da aplicação. O exemplo seguinte atualiza um contador de desempenho personalizado Button1_Click eventos:

    ```
    protected void Button1_Click(object sender, EventArgs e)
        {
         PerformanceCounter button1Counter = new PerformanceCounter(
           "MyCustomCounterCategory",
           "MyButton1Counter",
           string.Empty,
           false);
         button1Counter.Increment();
        this.Button1.Text = "Button 1 count: " +
           button1Counter.RawValue.ToString();
        }
    ```
8. Guarde o ficheiro.  

Dados de contador de desempenho personalizado agora serão recolhidos pelo monitor de diagnóstico Azure.

## <a name="step-3-query-performance-counter-data"></a>Passo 3: Consultar dados de contador de desempenho

Assim que a sua aplicação é implementada e a executar o monitor do diagnóstico começará a recolher contadores de desempenho e persistência esses dados ao armazenamento Azure. Utilize ferramentas como Server Explorer no Visual Studio, [Azure armazenamento Explorer](http://azurestorageexplorer.codeplex.com/)ou [Azure diagnósticos do Gestor](http://www.cerebrata.com/Products/AzureDiagnosticsManager/Default.aspx) ao Cerebrata para ver os dados de contadores desempenho na tabela WADPerformanceCountersTable. Pode também forma programática consultar o serviço de tabela utilizando [c#](../storage/storage-dotnet-how-to-use-tables.d), [Java](../storage/storage-java-how-to-use-table-storage.md), [Node.js](../storage/storage-nodejs-how-to-use-table-storage.md), [Python](../storage/storage-python-how-to-use-table-storage.md), [Rubi](../storage/storage-ruby-how-to-use-table-storage.md)ou [PHP](../storage/storage-php-how-to-use-table-storage.md).

O exemplo seguinte c# apresenta uma consulta simple contra a tabela WADPerformanceCountersTable e guarda os dados de diagnóstico para um ficheiro CSV. Assim que são guardados os contadores de desempenho para um ficheiro CSV pode utilizar as funcionalidades de gráficos no Microsoft Excel ou outra ferramenta de algumas para visualizar os dados. Certifique-se de que adicione uma referência a Microsoft.WindowsAzure.Storage.dll, que é incluído no SDK Azure para .NET Outubro de 2012 e versões posteriores. A assemblagem está instalada no directório de programa Files%\Microsoft SDKs\Microsoft Azure.NET SDK\version num\ref\ %.

```
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ...

    // Get the connection string. When using Microsoft Azure Cloud Services, it is recommended
    // you store your connection string using the Microsoft Azure service configuration
    // system (*.csdef and *.cscfg files). You can you use the CloudConfigurationManager type
    // to retrieve your storage connection string.  If you're not using Cloud Services, it's
    // recommended that you store the connection string in your web.config or app.config file.
    // Use the ConfigurationManager type to retrieve your storage connection string.

    string connectionString = Microsoft.WindowsAzure.CloudConfigurationManager.GetSetting("StorageConnectionString");
    //string connectionString = System.Configuration.ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString;

    // Get a reference to the storage account using the connection string.  You can also use the development
    // storage account (Storage Emulator) for local debugging.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
    //CloudStorageAccount storageAccount = CloudStorageAccount.DevelopmentStorageAccount;

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "WADPerformanceCountersTable" table.
    CloudTable table = tableClient.GetTableReference("WADPerformanceCountersTable");

    // Create the table query, filter on a specific CounterName, DeploymentId and RoleInstance.
    TableQuery<PerformanceCountersEntity> query = new TableQuery<PerformanceCountersEntity>()
       .Where(
          TableQuery.CombineFilters(
          TableQuery.GenerateFilterCondition("CounterName", QueryComparisons.Equal, @"\Processor(_Total)\% Processor Time"),
          TableOperators.And,
          TableQuery.CombineFilters(
          TableQuery.GenerateFilterCondition("DeploymentId", QueryComparisons.Equal, "ec26b7a1720447e1bcdeefc41c4892a3"),
          TableOperators.And,
          TableQuery.GenerateFilterCondition("RoleInstance", QueryComparisons.Equal, "WebRole1_IN_0")
          )
       )
    );

    // Execute the table query.
    IEnumerable<PerformanceCountersEntity> result = table.ExecuteQuery(query);

    // Process the query results and build a CSV file.
    StringBuilder sb = new StringBuilder("TimeStamp,EventTickCount,DeploymentId,Role,RoleInstance,CounterName,CounterValue\n");

    foreach (PerformanceCountersEntity entity in result)
    {
       sb.Append(entity.Timestamp + "," + entity.EventTickCount + "," + entity.DeploymentId + ","
          + entity.Role + "," + entity.RoleInstance + "," + entity.CounterName + "," + entity.CounterValue+"\n");
    }

    StreamWriter sw = File.CreateText(@"C:\temp\PerfCounters.csv");
    sw.Write(sb.ToString());
    sw.Close();
```

Mapa de entidades para c# objetos através de uma classe personalizada derivada de **TableEntity**. O código seguinte define uma classe entidade que representa um contador de desempenho na tabela **WADPerformanceCountersTable** .


    public class PerformanceCountersEntity : TableEntity
    {
       public long EventTickCount { get; set; }
       public string DeploymentId { get; set; }
       public string Role { get; set; }
       public string RoleInstance { get; set; }
       public string CounterName { get; set; }
       public double CounterValue { get; set; }
    }


## <a name="next-steps"></a>Próximos passos
[Artigos adicionais para os ver no Azure diagnósticos] (.. / azure diagnostics.md)
