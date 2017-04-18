<properties 
    pageTitle="Ativar métricas de armazenamento no portal do Azure | Microsoft Azure" 
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

# <a name="enabling-storage-metrics-and-viewing-metrics-data"></a>Ativar métricas de armazenamento e a visualização de dados de métricas

[AZURE.INCLUDE [storage-selector-portal-enable-and-view-metrics](../../includes/storage-selector-portal-enable-and-view-metrics.md)]

## <a name="overview"></a>Descrição geral

Por predefinição, métricas de armazenamento não estiver ativada para os seus serviços de armazenamento. Pode ativar a monitorização utilizando um dos do [Portal clássica Azure](https://manage.windowsazure.com), Windows PowerShell, ou através de programação através de um armazenamento API.

Quando ativa métricas de armazenamento, tem de escolher um período de retenção para os dados: este período de determina para quanto tempo o armazenamento de serviço mantém as métricas e taxas que para o espaço necessários para armazená-los. Normalmente, deve utilizar um endereço mais curto período de retenção para minutos métricas de métricas de comunicação devido o significativo espaço extra necessário para o minutos métricas. Deverá escolher um período de retenção assim que tiver tempo suficiente para analisar os dados e transferir qualquer métricas que pretende manter para análise offline ou efeitos de relatórios. Lembre-se de que que também irão ser faturada para transferir dados de métricas de uma conta de armazenamento.

## <a name="how-to-enable-storage-metrics-using-the-azure-classic-portal"></a>Como ativar métricas de armazenamento utilizando o Portal de clássica do Azure

No [Portal clássica Azure](https://manage.windowsazure.com), utilize a página configurar para uma conta de armazenamento para controlo de métricas de armazenamento. Para monitorizar, pode definir um nível e um período de retenção em dias para cada um dos blobs, tabelas e filas. Em cada um dos casos, o nível é um dos seguintes procedimentos:

- Desativar — Sem métricas são recolhidas.

- Mínimo — Métricas de armazenamento recolhe um conjunto básico de métricas de penetração/saída, disponibilidade, latência e percentagens de sucesso, que são agregadas para os serviços de BLOBs, tabela e fila de espera.

- Verboso — Métricas de armazenamento recolhe um conjunto completo de métricas que inclui a mesma métrica para cada operação API, para além de métricas de nível de serviço de armazenamento. Métricas verbosas ativar análise mais perto dos problemas que ocorram durante operações de aplicações.

Tenha em atenção que Portal clássica do Azure não atualmente permitem-lhe configurar métricas minutos na sua conta de armazenamento; tem de ativar métricas minutos através do PowerShell ou através de programação.


## <a name="how-to-enable-storage-metrics-using-powershell"></a>Como ativar métricas de armazenamento através do PowerShell

Pode utilizar o PowerShell no seu computador local para configurar métricas de armazenamento na sua conta de armazenamento, utilizando o cmdlet do PowerShell do Azure Get-AzureStorageServiceMetricsProperty para obter as definições atuais e o cmdlet Set-AzureStorageServiceMetricsProperty para alterar as definições atuais.

Os cmdlets que controlam métricas de armazenamento, utilize os seguintes parâmetros:

- Os valores possíveis MetricsType são horas e minutos.

- Os valores possíveis TipoServiço são Blob, fila de espera e tabela.

- Os valores possíveis de MetricsLevel são nenhuma (equivalente para desligado no Portal clássica do Azure), o serviço (equivalente a mínimas no Portal clássica do Azure) e ServiceAndApi (equivalente a verboso no Portal clássica do Azure).

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

Quando tiver configurado métricas de armazenamento para monitorizar a sua conta de armazenamento, registos de métricas num conjunto de famoso tabelas na sua conta de armazenamento. Pode utilizar a página Monitor para a sua conta de armazenamento no Portal clássica do Azure para ver as preço por hora métricas à medida que ficam disponíveis num gráfico. Nesta página no Portal clássica do Azure, pode:

- Selecione quais métricas representar no gráfico (a escolha de métricas disponíveis serão dependem de se optar por verboso ou mínimas monitorização do serviço na página Configurar).


- Selecione o intervalo de tempo para métricas apresentadas no gráfico.


- Escolha uma escala de absoluta ou relativa para desenhar as métricas.


- Configure correio eletrónico alertas para notificá-lo quando uma métrica específica atinge um determinado valor.


Se pretender para transferir as métricas de armazenamento a longo prazo ou analisá-los localmente, terá de utilizar uma ferramenta ou escrever algum código para ler as tabelas. Tem de transferir as métricas minutos para uma análise. As tabelas não aparecem se listar todas as tabelas na sua conta de armazenamento, mas pode aceder aos mesmos diretamente pelo nome. Ferramentas de armazenamento de navegação de terceiros muitos esteja cientes destas tabelas e permite-lhe para visualizá-los diretamente (consulte a mensagem no blogue [Explorador de armazenamento do Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx) para obter uma lista de ferramentas disponíveis).

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

Deverá pondere definir alertas no Portal clássica do Azure na página Monitor de modo a que métricas de armazenamento pode automaticamente notificá-lo de quaisquer alterações importantes no comportamento dos seus serviços de armazenamento. Se utilizar uma ferramenta de Explorador de armazenamento para transferir estes dados de métricas num formato delimitado, pode utilizar o Microsoft Excel para analisar os dados. Consulte a mensagem no blogue [Explorador de armazenamento do Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx) para obter uma lista das ferramentas do Explorador de armazenamento disponível.



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

## <a name="next-steps"></a>Passos seguintes:
[Ativar o registo e aceder a dados do registo a análise de armazenamento](https://msdn.microsoft.com/library/dn782840.aspx)
 