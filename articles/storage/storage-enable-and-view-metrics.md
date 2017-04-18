<properties
    pageTitle="Ativar métricas de armazenamento no Portal do Azure | Microsoft Azure"
    description="Como ativar métricas de armazenamento para os serviços de BLOBs, fila de espera, tabela e ficheiro"
    services="storage"
    documentationCenter=""
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>

# <a name="enabling-azure-storage-metrics-and-viewing-metrics-data"></a>Ativar métricas de armazenamento do Azure e visualizar dados de métricas

[AZURE.INCLUDE [storage-selector-portal-enable-and-view-metrics](../../includes/storage-selector-portal-enable-and-view-metrics.md)]

## <a name="overview"></a>Descrição geral

Por predefinição, métricas de armazenamento não estiver ativada para os seus serviços de armazenamento. Pode ativar a monitorização de através do [Portal do Azure](https://portal.azure.com) ou o Windows PowerShell ou através de programação através da biblioteca de armazenamento do cliente.

Quando ativa métricas de armazenamento, tem de escolher um período de retenção para os dados: este período de determina para quanto tempo o armazenamento de serviço mantém as métricas e taxas que para o espaço necessários para armazená-los. Normalmente, deve utilizar um endereço mais curto período de retenção para minutos métricas de métricas de comunicação devido o significativo espaço extra necessário para o minutos métricas. Deverá escolher um período de retenção assim que tiver tempo suficiente para analisar os dados e transferir qualquer métricas que pretende manter para análise offline ou efeitos de relatórios. Lembre-se de que que também irão ser faturada para transferir dados de métricas de uma conta de armazenamento.

## <a name="how-to-enable-metrics-using-the-azure-portal"></a>Como ativar métricas utilizando o Portal do Azure

Siga estes passos para ativar métricas no [Portal do Azure](https://portal.azure.com):

1. Navegue até à sua conta de armazenamento.
1. Abra o pá **Definições** e selecione **diagnóstico**.
1. Certifique-se de que o **Estado** está definido como **On**.
1. Selecione as métricas para os serviços que pretende monitorizar.
2. Especifique uma política de retenção para indicar quanto tempo para reter métricas e registar os dados.

Tenha em atenção que o [Portal do Azure](https://portal.azure.com) não atualmente permitem-lhe configurar métricas minutos na sua conta de armazenamento; tem de ativar métricas minutos através do PowerShell ou através de programação.

## <a name="how-to-enable-metrics-using-powershell"></a>Como ativar métricas através do PowerShell

Pode utilizar o PowerShell no seu computador local para configurar métricas de armazenamento na sua conta de armazenamento, utilizando o cmdlet do PowerShell do Azure Get-AzureStorageServiceMetricsProperty para obter as definições atuais e o cmdlet Set-AzureStorageServiceMetricsProperty para alterar as definições atuais.

Os cmdlets que controlam métricas de armazenamento, utilize os seguintes parâmetros:

- MetricsType: os valores possíveis são horas e minutos.

- TipoServiço: os valores possíveis são Blob, fila de espera e tabela.

- MetricsLevel: valores possíveis são nenhuma, o serviço e ServiceAndApi.

Por exemplo, o seguinte comando muda no minutos métricas para o serviço de BLOBs na sua conta de armazenamento predefinida com retenção período definido para cinco dias:

`Set-AzureStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5`

O comando seguinte obtém os atuais horários métricas nível e retenção dias para o serviço de BLOBs na sua conta de armazenamento predefinido:

`Get-AzureStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob`

Para obter informações sobre como configurar os cmdlets do Azure PowerShell para trabalhar com a sua subscrição Azure e como selecionar a conta de armazenamento predefinida para utilizar, consulte o artigo: [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

## <a name="how-to-enable-storage-metrics-programmatically"></a>Como ativar métricas de armazenamento através de programação

O fragmento de c# seguinte mostra como ativar o registo para o serviço de BLOBs utilizando a biblioteca do cliente de armazenamento para .NET e métricas:

    //Parse the connection string for the storage account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

    // Create service client for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Enable Storage Analytics logging and set retention policy to 10 days.
    ServiceProperties properties = new ServiceProperties();
    properties.Logging.LoggingOperations = LoggingOperations.All;
    properties.Logging.RetentionDays = 10;
    properties.Logging.Version = "1.0";

    // Configure service properties for metrics. Both metrics and logging must be set at the same time.
    properties.HourMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
    properties.HourMetrics.RetentionDays = 10;
    properties.HourMetrics.Version = "1.0";

    properties.MinuteMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
    properties.MinuteMetrics.RetentionDays = 10;
    properties.MinuteMetrics.Version = "1.0";

    // Set the default service version to be used for anonymous requests.
    properties.DefaultServiceVersion = "2015-04-05";

    // Set the service properties.
    blobClient.SetServiceProperties(properties);


## <a name="viewing-storage-metrics"></a>Métricas de armazenamento de visualização

Depois de configurar métricas de armazenamento Analytics para monitorizar a sua conta de armazenamento, análises de armazenamento de registos métricas num conjunto de famoso tabelas na sua conta de armazenamento. Pode configurar gráficos para ver o preço por hora métricas no [Azure Portal](https://portal.azure.com):

1. Navegue até à sua conta de armazenamento no [Portal do Azure](https://portal.azure.com).
2. Na secção de **monitorização** , clique em **Adicionar mosaicos** para adicionar um novo gráfico. Na **Galeria de mosaico**, selecione a métrica do que pretende ver e arraste-o para a secção de **monitorização** .
3. Para editar quais métricas são apresentadas num gráfico, clique em **Editar** ligação. Pode adicionar ou remover métricas individuais ao selecionar ou desmarcá-los.
4. Clique em **Guardar** quando tiver concluído a edição métricas.

Se pretender para transferir as métricas de armazenamento a longo prazo ou analisá-los localmente, terá:

- Utilize uma ferramenta em mente estas tabelas e que permite-lhe ver e transferi-los.
- Escreva uma aplicação personalizada ou script para ler e armazenar as tabelas.

Ferramentas de armazenamento de navegação de terceiros muitos estiverem cientes destas tabelas e permitem-lhe para visualizá-los diretamente.
Consulte o artigo [Azure armazenamento Explorador](storage-explorers.md) para obter uma lista de ferramentas disponíveis.

> [AZURE.NOTE] Começando versão 0.8.0 [Microsoft Azure armazenamento do Explorador de] (http://storageexplorer.com/), agora poderão ver e transferir as tabelas de métricas de análise.

Para poder aceder às tabelas de análise através de programação, tenha em atenção que as tabelas da análise não aparecem se listar todas as tabelas na sua conta de armazenamento. Pode aceder-lhes diretamente pelo nome ou utilizar a [CloudAnalyticsClient API](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.analytics.cloudanalyticsclient.aspx) na biblioteca de cliente do .NET para os nomes das tabelas de consulta.

### <a name="hourly-metrics"></a>Métricas de comunicação
- $MetricsHourPrimaryTransactionsBlob
- $MetricsHourPrimaryTransactionsTable
- $MetricsHourPrimaryTransactionsQueue

### <a name="minute-metrics"></a>Métricas minutos
- $MetricsMinutePrimaryTransactionsBlob
- $MetricsMinutePrimaryTransactionsTable
- $MetricsMinutePrimaryTransactionsQueue

### <a name="capacity"></a>Capacidade
- $MetricsCapacityBlob

Pode localizar detalhes na totalidade dos esquemas para estas tabelas de [Esquema de tabela de métricas de análise de armazenamento](https://msdn.microsoft.com/library/azure/hh343264.aspx). As linhas de exemplo abaixo mostram apenas um subconjunto de colunas disponíveis, mas ilustram algumas funcionalidades da forma como métricas de armazenamento guarda nestas métricas importantes:

| PartitionKey  |       RowKey       |                    Data/hora | TotalRequests | TotalBillableRequests | TotalIngress | TotalEgress | Disponibilidade | AverageE2ELatency | AverageServerLatency | PercentSuccess |
|---------------|:------------------:|-----------------------------:|---------------|-----------------------|--------------|-------------|--------------|-------------------|----------------------|----------------|
| 20140522T1100 |      utilizador; Todos os      | 2014-05-22T11:01:16.7650250Z | 7             | 7                     | 4003         | 46801       | 100          | 104.4286          | 6.857143             | 100            |
| 20140522T1100 | utilizador; QueryEntities | 2014-05-22T11:01:16.7640250Z | 5             | 5                     | 2694         | 45951       | 100          | 143.8             | 7,8                  | 100            |
| 20140522T1100 |  utilizador; QueryEntity  | 2014-05-22T11:01:16.7650250Z | 1             | 1                     | 538          | 633         | 100          | 3                 | 3                    | 100            |
| 20140522T1100 | utilizador; UpdateEntity  | 2014-05-22T11:01:16.7650250Z | 1             | 1                     | 771          | 217         | 100          | 9                 | 6                    | 100               |

Nos dados de métricas minuto neste exemplo, a tecla partição utiliza o tempo na resolução minuto. A chave de linha identifica o tipo de informação que está armazenado na linha e esta é composto por duas partes de informações, o tipo de acesso e o tipo de pedido:

- O tipo de acesso é o utilizador ou sistema, onde o utilizador que se refere a todos os pedidos de utilizador para o serviço de armazenamento e sistema refere-se para pedidos de efetuadas pela análise de armazenamento.

- O tipo de pedido é tudo na qual caso é uma linha de resumo, ou identifique API específico como QueryEntity ou UpdateEntity.


Os dados de exemplo acima mostram todos os registos de um minuto única (começando na 11:00 AM), para que o número de pedidos de QueryEntities mais o número de QueryEntity os pedidos de adição o número de UpdateEntity pedidos adicione até sete, que é o total apresentado na linha de utilizador: All. Da mesma forma, pode deriva a latência de ponto a ponto 104.4286 média a linha de utilizador: todas, calculando ((143.8 * 5) + 3 + 9) / 7.

Deverá pondere definir alertas no [Portal do Azure](https://portal.azure.com) na página Monitor de modo a que métricas de armazenamento pode automaticamente notificá-lo de quaisquer alterações importantes no comportamento dos seus serviços de armazenamento. Se utilizar uma ferramenta de Explorador de armazenamento para transferir estes dados de métricas num formato delimitado, pode utilizar o Microsoft Excel para analisar os dados. Consulte a mensagem no blogue [Explorador de armazenamento do Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx) para obter uma lista das ferramentas do Explorador de armazenamento disponível.



## <a name="accessing-metrics-data-programmatically"></a>Aceder a dados de métricas através de programação

Na lista seguinte mostra o exemplo c# código que acede métricas minutos para um intervalo de minutos e apresenta os resultados na janela de consola. Utiliza a biblioteca de armazenamento do Azure versão 4, que inclui a classe de CloudAnalyticsClient que simplifica o acesso às tabelas de métricas de armazenamento.

    private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)
    {
    // Convert the dates to the format used in the PartitionKey
    var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");
    var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");

    var services = Enum.GetValues(typeof(StorageService));
    foreach (StorageService service in services)
    {
    Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);
    var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);
    var t = analyticsClient.GetMinuteMetricsTable(service);
    var opContext = new OperationContext();
    var query =
    from entity in metricsQuery
    // Note, you can't filter using the entity properties Time, AccessType, or TransactionType
    // because they are calculated fields in the MetricsEntity class.
    // The PartitionKey identifies the DataTime of the metrics.
    where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0
    select entity;

    // Filter on "user" transactions after fetching the metrics from Table Storage.
    // (StartsWith is not supported using LINQ with Azure table storage)
    var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));
    var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();
    Console.WriteLine(resultString);
    }
    }

    private static string MetricsString(MetricsEntity entity, OperationContext opContext)
    {
    var entityProperties = entity.WriteEntity(opContext);
    var entityString =
    string.Format("Time: {0}, ", entity.Time) +
    string.Format("AccessType: {0}, ", entity.AccessType) +
    string.Format("TransactionType: {0}, ", entity.TransactionType) +
    string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));
    return entityString;

    }




## <a name="what-charges-do-you-incur-when-you-enable-storage-metrics"></a>O que dos encargos que implicam quando ativa métricas de armazenamento?

Escreva pedidos para criar entidades de tabela para métricas são cobrados as taxas fixas aplicáveis a todas as operações de armazenamento do Windows Azure.

Ler e eliminar os pedidos por um cliente para dados métricas também são cobrar taxas padrão. Se tiver configurado uma política de retenção de dados, não são cobrados quando o armazenamento do Windows Azure elimina os dados de métricas antigos. No entanto, se eliminar dados de análise, a sua conta é cobrada para as operações de eliminação.

A capacidade utilizada pelas tabelas de métricas também é cobrar: pode utilizar o seguinte procedimento para estimar a quantidade de capacidade utilizada para armazenar dados métricas:

- Se a cada hora cada API em cada serviço utiliza a um serviço, aproximadamente 148KB de dados é armazenado cada hora nas tabelas da transação métricas se ativou o serviço e resumo de nível de API.

- Se a cada hora cada API em cada serviço utiliza a um serviço, aproximadamente 12KB de dados é armazenado cada hora nas tabelas da transação métricas se ativou apenas nível de serviço resumo.

- A tabela de capacidade para blobs tem duas linhas adicionadas cada dia (desde o utilizador tiver optado por participar para registos): Isto significa que todos os dias o tamanho desta tabela aumenta por até aproximadamente 300 bytes.

## <a name="next-steps"></a>Passo seguinte:
[Ativar o registo e aceder a dados do registo de armazenamento](https://msdn.microsoft.com/library/dn782840.aspx)
