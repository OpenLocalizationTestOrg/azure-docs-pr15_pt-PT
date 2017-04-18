<properties 
    pageTitle="Dimensionamento no Azure DocumentDB e criação de partições | Microsoft Azure"      
    description="Saiba mais sobre como a partições funciona no Azure DocumentDB, como configurar a partições e partição teclas e como escolher a chave de partição direita para a sua aplicação."         
    services="documentdb" 
    authors="arramac" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/> 

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/20/2016" 
    ms.author="arramac"/> 

# <a name="partitioning-and-scaling-in-azure-documentdb"></a>Dimensionamento no Azure DocumentDB e criação de partições
[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) destina-se para o ajudar a obter um desempenho e previsível e escala de forma totalmente integrada juntamente com a sua aplicação à medida que aumenta. Este artigo fornece uma descrição geral de como a partições funciona no DocumentDB e descreve como pode configurar DocumentDB coleções de sites para dimensionar eficazmente suas aplicações.

Depois de ler este artigo, será capaz de responder às seguintes questões:   

- Como funciona a partições funcionam no Azure DocumentDB?
- Como configurar a partições no DocumentDB
- O que são chaves de partição e como escolher a chave de partição direita para a minha aplicação?

Para começar a utilizar com o código, transfira o projeto a partir de [DocumentDB desempenho testes controlador de amostra](https://github.com/Azure/azure-documentdb-dotnet/tree/a2d61ddb53f8ab2a23d3ce323c77afcf5a608f52/samples/documentdb-benchmark). 

## <a name="partitioning-in-documentdb"></a>Criar a partições no DocumentDB

No DocumentDB, pode armazenar e consultar esquema menos JSON documentos com os tempos de resposta da ordem de milissegundos em qualquer escala. DocumentDB fornece contentores para armazenar dados chamados **coleções de sites**. Coleções de sites são recursos lógicos e podem abranger um ou mais partições físicas ou servidores. O número de partições é determinado pela DocumentDB baseada o tamanho de armazenamento e o débito aprovisionado da coleção de. Todas as partições no DocumentDB tem um valor fixo de armazenamento de cópias SSD associado e são replicada para elevada disponibilidade. Gestão de partição totalmente é gerido pelo Azure DocumentDB e que não tem de escrever código complexo ou gerir as partições. Coleções de DocumentDB são **praticamente ilimitado** em termos de armazenamento e débito. 

Criação de partições é completamente transparente para a sua aplicação. DocumentDB suporta lê rápida e escritas, consultas SQL e LINQ, JavaScript, com base lógica transaccional, níveis de consistência e controlo de acesso extensivamente através de REST API chamadas a um recurso de única colecção. O serviço processa dados distribuição ao longo das partições e encaminhamento de pedidos de consulta para a direita partição. 

Como é que isto funciona? Quando cria uma coleção de no DocumentDB, notará que existe um valor de configuração de **propriedade da chave partição** que pode especificar. Este é o propriedade JSON (ou o caminho) dentro dos seus documentos que podem ser utilizados pelo DocumentDB para distribuir os seus dados entre vários servidores ou a partições. DocumentDB irá hash o valor de chave partição e utilizar o resultado hash para determinar a partição na qual o documento JSON será armazenado. Todos os documentos com a mesma chave partição serão armazenados na mesma partição. 

Por exemplo, considere uma aplicação que armazena dados sobre empregados e dos seus serviços em DocumentDB. Vamos escolher `"department"` como a propriedade da chave partição, para poder Dimensionar dados por departamento. Todos os documentos na DocumentDB tem de conter um obrigatório `"id"` propriedade que tem de ser exclusiva para cada documento com o mesmo partição chave valor, por exemplo, `"Marketing`". Todos os documentos armazenados numa coleção de tem de ter uma combinação exclusiva de chave partição e id, por exemplo, `{ "Department": "Marketing", "id": "0001" }`, `{ "Department": "Marketing", "id": "0002" }`, e `{ "Department": "Sales", "id": "0001" }`. Por outras palavras, a propriedade composta do (id da chave, partição) é a chave primária para a coleção.

### <a name="partition-keys"></a>Chaves de partição
A opção da chave de partição é uma decisão importante que terá de fazer altura de estrutura. Tem de escolher um nome de propriedade JSON que tem uma vasta gama de valores e é provável que tenham uniformemente distribuídos padrões de acesso. A chave de partição especificada como um caminho JSON, por exemplo, `/department` representa o departamento de propriedade. 

A tabela seguinte mostra exemplos de definições de chave partição e os valores JSON correspondente a cada uma.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Caminho da chave partição</strong></p></td>
            <td valign="top"><p><strong>Descrição</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>/Department</p></td>
            <td valign="top"><p>Corresponde ao valor JSON de doc.department onde o documento é o documento.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>nome/propriedades /</p></td>
            <td valign="top"><p>Corresponde ao valor JSON de doc.properties.name onde o documento é o documento (propriedade aninhada).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/ID</p></td>
            <td valign="top"><p>Corresponde ao valor JSON de doc.id (id e partição chave são a mesma propriedade).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/ "nome do departamento"</p></td>
            <td valign="top"><p>Corresponde ao valor JSON de documento ["nome de departamento"] onde o documento é o documento.</p></td>
        </tr>
    </tbody>
</table>

> [AZURE.NOTE] A sintaxe para o caminho da chave partição é semelhante a especificação de caminho para a indexação caminhos de política com a diferença de chave que o caminho corresponde à propriedade em vez do valor, ou seja, existe nenhum cartão silvestres no final. Por exemplo, que seria especificar/departamento /? Para indexar os valores em departamento, mas especificar /department como a definição de chave partição. O caminho da chave partição implicitamente é indexado e não pode ser excluído da indexação utilizando indexação substituições de política.

Vamos olhar como a escolha de chave partição impactos o desempenho da sua aplicação.

### <a name="partitioning-and-provisioned-throughput"></a>Criação de partições e aprovisionado débito
DocumentDB destina-se para obter um desempenho previsível. Quando cria uma coleção de, é reservar débito em termos de ** [unidades pedido](documentdb-request-units.md) (RU) por segundo**. Cada pedido é atribuído um encargo de unidade de pedido é proporcional à quantidade de recursos do sistema, como CPU e IO média consumida pela operação. Uma operação de leitura de um documento de 1 kB com consistência sessão consome 1 unidade pedido. Uma operação de leitura é 1 RU independentemente o número de itens armazenados ou o número de pedidos em simultâneo a executar ao mesmo. Documentos maiores exigem mais elevadas unidades pedido dependendo do tamanho. Se souber o tamanho da sua entidades e o número de lê que necessários para suportar para a sua aplicação, pode aprovisionar o montante exacto de débito necessário para a sua aplicação do ler necessidades. 

Quando DocumentDB armazena documentos, que distribui-los uniformemente entre a partições com base no valor de chave partição. O débito também é distribuído uniformemente entre disponíveis partições, ou seja, o débito por partição = (débito total por coleção) / (número de partições). 

>[AZURE.NOTE] Para poder alcançar o débito completo da coleção de, tem de escolher uma chave de partição permite-lhe distribuir uniformemente pedidos entre um número de valores chave distinct partição.

## <a name="single-partition-and-partitioned-collections"></a>Única partição e colecções com partições
DocumentDB suporta a criação de uma única partição tanto com partições coleções de sites. 

- **Partitioned coleções de sites** pode abranger múltiplas partições e muito grandes quantidades de armazenamento e débito de suporte. Tem de especificar uma chave de partição para a coleção.
- **As coleções de única partição** ter inferiores opções de preços e a capacidade de consulta e executar as transações através de todos os dados de coleções de sites. Possuem escalabilidade e armazenamento dos limites de uma única partição. Não tem de especificar uma chave de partição para estes coleções de sites. 

![Coleções com partições no DocumentDB][2] 

Cenários que não necessita de grandes volumes de armazenamento ou débito, coleções de única partição são uma boa opção. Note que única partição coleções de ter a escalabilidade e limites de armazenamento de uma única partição, ou seja até 10 GB de armazenamento e a até 10.000 unidades pedido por segundo. 

Coleções de sites com partições podem suportar muito grandes quantidades de armazenamento e débito. No entanto as ofertas predefinido estão configuradas para armazenar até 250 GB de armazenamento e dimensionar até 250.000 unidades pedido por segundo. Se precisar de mais elevados armazenamento ou débito por coleção, contacte o [Suporte do Azure](documentdb-increase-limits.md) ter um aumento para a sua conta.

A tabela seguinte apresenta as diferenças de colecções com partições e trabalhar com uma única partição:

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>Única partição coleções de sites</strong></p></td>
            <td valign="top"><p><strong>Coleções de sites com partições</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Tecla de partição</p></td>
            <td valign="top"><p>Nenhum</p></td>
            <td valign="top"><p>Obrigatório</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Chave primária para documento</p></td>
            <td valign="top"><p>"id"</p></td>
            <td valign="top"><p>chave composta &lt;chave partição&gt; e "id"</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Armazenamento mínima</p></td>
            <td valign="top"><p>0 GB</p></td>
            <td valign="top"><p>0 GB</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Máximo de armazenamento</p></td>
            <td valign="top"><p>10 GB</p></td>
            <td valign="top"><p>Ilimitado (250 GB por predefinição)</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Débito mínimo</p></td>
            <td valign="top"><p>400 pedido as unidades por segundo</p></td>
            <td valign="top"><p>10.000 unidades pedido por segundo</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Débito máximo</p></td>
            <td valign="top"><p>10.000 unidades pedido por segundo</p></td>
            <td valign="top"><p>Ilimitado (unidades 250.000 pedido por segundo por predefinição)</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Versões de API</p></td>
            <td valign="top"><p>Todos os</p></td>
            <td valign="top"><p>API 2015-12-16 e mais recente</p></td>
        </tr>
    </tbody>
</table>

## <a name="working-with-the-sdks"></a>Trabalhar com os SDK

Azure DocumentDB adicionado suporte para criar a partições automáticas com a [API REST versão 2015-12-16](https://msdn.microsoft.com/library/azure/dn781481.aspx). Para poder criar coleções de sites com partições, tem de transferir versões SDK 1.6.0 ou mais recente de uma das plataformas suportadas SDK (.NET, Node.js, Java, Python). 

### <a name="creating-partitioned-collections"></a>Criação de colecções com partições

O exemplo seguinte mostra um fragmento de .NET para criar uma colecção para armazenar dados de telemetria do dispositivo de 20.000 unidades pedido por segundo de débito. O SDK define o valor de OfferThroughput (que por sua vez define o `x-ms-offer-throughput` cabeçalho pedido na REST API). Aqui vamos definir o `/deviceId` como chave partição. A escolha de chave partição é guardada juntamente com o resto dos metadados coleções de sites, como o nome e a política de indexação.

Neste exemplo, escolher `deviceId` desde que sabe (um) uma vez que existem um grande número de dispositivos, escritas podem ser distribuídas ao longo a partições uniformemente e permitir-nos para dimensionar a base de dados ingerir esta última grandes volumes de dados e (b) muitos dos pedidos de como a obtenção de mais recente leitura para um dispositivo estão limitadas para uma única deviceId e podem ser obtidos a partir de uma única partição.

    DocumentClient client = new DocumentClient(new Uri(endpoint), authKey);
    await client.CreateDatabaseAsync(new Database { Id = "db" });

    // Collection for device telemetry. Here the JSON property deviceId will be used as the partition key to 
    // spread across partitions. Configured for 10K RU/s throughput and an indexing policy that supports 
    // sorting against any number or string property.
    DocumentCollection myCollection = new DocumentCollection();
    myCollection.Id = "coll";
    myCollection.PartitionKey.Paths.Add("/deviceId");

    await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri("db"),
        myCollection,
        new RequestOptions { OfferThroughput = 20000 });
        

> [AZURE.NOTE] Para poder criar coleções de sites com partições, tem de especificar um valor de débito de unidades de pedido de > 10.000 por segundo. Uma vez que o débito múltiplos de 100, este tem de ser 10,100 ou superior.

Este método permite uma REST API a chamada para DocumentDB e o serviço de aprovisionamento de um número de partições com base nas débito pedido. Pode alterar o débito de uma coleção de que o desempenho do seu necessidades evoluir. Consulte o artigo [Níveis de desempenho](documentdb-performance-levels.md) para obter mais detalhes.

### <a name="reading-and-writing-documents"></a>Leitura e escrita documentos

Agora, vamos inserir dados DocumentDB. Eis uma classe de exemplo que contém um dispositivo de leitura e uma chamada para CreateDocumentAsync para inserir um novo dispositivo para uma coleção de leitura.

    public class DeviceReading
    {
        [JsonProperty("id")]
        public string Id;

        [JsonProperty("deviceId")]
        public string DeviceId;

        [JsonConverter(typeof(IsoDateTimeConverter))]
        [JsonProperty("readingTime")]
        public DateTime ReadingTime;

        [JsonProperty("metricType")]
        public string MetricType;

        [JsonProperty("unit")]
        public string Unit;

        [JsonProperty("metricValue")]
        public double MetricValue;
      }

    // Create a document. Here the partition key is extracted as "XMS-0001" based on the collection definition
    await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri("db", "coll"),
        new DeviceReading
        {
            Id = "XMS-001-FE24C",
            DeviceId = "XMS-0001",
            MetricType = "Temperature",
            MetricValue = 105.00,
            Unit = "Fahrenheit",
            ReadingTime = DateTime.UtcNow
        });


Vamos ler o documento ao chave do partição e id, atualize-la e eliminá-la, em seguida, como um passo final, chave partição e id. Tenha em atenção que o lê incluir um valor de PartitionKey (correspondente para a `x-ms-documentdb-partitionkey` cabeçalho pedido na REST API).

    // Read document. Needs the partition key and the ID to be specified
    Document result = await client.ReadDocumentAsync(
      UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
      new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });

    DeviceReading reading = (DeviceReading)(dynamic)result;

    // Update the document. Partition key is not required, again extracted from the document
    reading.MetricValue = 104;
    reading.ReadingTime = DateTime.UtcNow;

    await client.ReplaceDocumentAsync(
      UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
      reading);

    // Delete document. Needs partition key
    await client.DeleteDocumentAsync(
      UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
      new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });



### <a name="querying-partitioned-collections"></a>Consultar colecções com partições

Quando consultar dados coleções com partições, DocumentDB encaminha automaticamente a consulta para as partições correspondente para os valores da chave partição especificados no filtro (se existirem). Por exemplo, esta consulta é encaminhada para partição que contém a chave de partição "XMS 0001".

    // Query using partition key
    IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
        UriFactory.CreateDocumentCollectionUri("db", "coll"))
        .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");

A seguinte consulta não tem um filtro na chave partição (DeviceId) e é fanned a todas as partições onde é executada de índice a partição. Nota que tem de especificar o EnableCrossPartitionQuery (`x-ms-documentdb-query-enablecrosspartition` na REST API) para que o SDK para executar uma consulta ao longo a partições.

    // Query across partition keys
    IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
        UriFactory.CreateDocumentCollectionUri("db", "coll"), 
        new FeedOptions { EnableCrossPartitionQuery = true })
        .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);

### <a name="parallel-query-execution"></a>Execução da consulta paralelas

O SDK DocumentDB 1.9.0 e acima de opções de execução do suporte consulta paralela, que permitem-lhe executar consultas de latência baixa contra colecções com partições, mesmo quando precisa de um grande número de partições de toque. Por exemplo, a seguinte consulta está configurada para executar paralelamente em várias partições.

    // Cross-partition Order By Queries
    IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
        UriFactory.CreateDocumentCollectionUri("db", "coll"), 
        new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
        .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
        .OrderBy(m => m.MetricValue);

Pode gerir a execução da consulta paralelas pelos seguintes parâmetros de sintonização:

- Ao definir `MaxDegreeOfParallelism`, pode controlar o grau de paralelismo ou seja, o número máximo de ligações de rede em simultâneo a partições a coleção. Se definir este para -1, o grau de paralelismo é gerido pelo SDK. Se o `MaxDegreeOfParallelism` não é especificado ou definido para 0, o que é o valor predefinido, será uma ligação de rede única a partições a coleção.
- Ao definir `MaxBufferedItemCount`, pode comércio desativar consulta latência e o cliente lado utilização de memória. Se omitir este parâmetro ou defini-lo para -1, o número de itens na memória intermédia durante a execução de consulta paralelas é gerido pelo SDK.

Dado o mesmo Estado da coleção de, paralela consulta irá devolver resultados na mesma ordem vistos execução série. Quando executa uma consulta cruzada partição que inclui a ordenação (ORDER BY e/ou superior), o SDK DocumentDB problemas a consulta em paralelo ao longo a partições e intercala parcialmente ordenados resultados do lado do cliente para produzir resultados globalmente ordenados.

### <a name="executing-stored-procedures"></a>Procedimentos armazenados em execução

Também pode executar operações atómica contra documentos com o mesmo ID de dispositivo, por exemplo, se estiver a fazer a manutenção agregados ou o estado de mais recente de um dispositivo num único documento. 

    await client.ExecuteStoredProcedureAsync<DeviceReading>(
        UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
        new RequestOptions { PartitionKey = new PartitionKey("XMS-001") }, 
        "XMS-001-FE24C");

Na secção seguinte, podemos veja como pode mover para coleções de sites com partições de coleções de partição única.

<a name="migrating-from-single-partition"></a>
### <a name="migrating-from-single-partition-to-partitioned-collections"></a>Migrar do única partição para coleções de sites com partições
Quando uma aplicação utilizando uma coleção de única partição necessita de débito superior (> 10.000 RU/s) ou o armazenamento de dados maior (> 10GB), pode utilizar a [Ferramenta de migração de dados DocumentDB](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d) para migrar os dados de coleção de partição única para uma coleção de com partições. 

Para migrar a partir de uma coleção de partição única para uma coleção de com partições

1. Exporte dados da coleção de partição única para JSON. Para detalhes adicionais, consulte a [Exportar para ficheiro JSON](documentdb-import-data.md#export-to-json-file) .
2. Importe os dados para uma coleção de com partições criada com uma definição de chave partição e superior a 10.000 pedido unidades por segundo débito, conforme mostrado no exemplo abaixo. Para detalhes adicionais, consulte [Importar para DocumentDB](documentdb-import-data.md#DocumentDBSeqTarget) .

![Migrar dados para uma colecção de Partitioned no DocumentDB][3]  

>[AZURE.TIP] Para importar mais rápidos, considere aumentar o número de paralelas pedidos para 100 ou superior para tirar partido do débito mais elevado disponível para coleções de sites com partições. 

Agora que recomendamos concluiu os princípios básicos, vamos ver algumas considerações de estrutura importantes ao trabalhar com as teclas de partição nas DocumentDB.

## <a name="designing-for-partitioning"></a>Estruturar para criar partições
A opção da chave de partição é uma decisão importante que terá de fazer altura de estrutura. Esta secção descreve alguns das responsabilidades envolvidas na selecionar uma chave de partição para a coleção.

### <a name="partition-key-as-the-transaction-boundary"></a>Tecla de partição como o limite da transação
Sua escolha da chave partição deve calcular o saldo a necessidade de ativar a utilização das transações contra o requisito de registo para distribuir as entidades por vários partição chaves para garantir uma solução dimensionável. Por um lado, pode definir a mesma chave partição para todos os seus documentos, mas isto pode limitar a escalabilidade da sua solução. Por outro lado, poderá atribuir uma chave de partição exclusivo para cada documento, que seria altamente dimensionáveis, mas que iria impedi-lo de utilizar as transações de documento cruzada através de procedimentos armazenados e accionadores. Uma tecla partição ideal é um que permite-lhe utilizar consultas eficientes e que tenha cardinalidade suficiente para garantir a que sua solução seja dimensionável. 

### <a name="avoiding-storage-and-performance-bottlenecks"></a>Evitar constrangimento de armazenamento e desempenho 
Também é importante escolher uma propriedade que lhe permite escritas para ser distribuído ao longo de um número de valores distintos. Os pedidos para a mesma tecla partição não podem exceder o débito de uma única partição e irão estar limitados. Por isso, é importante escolher uma chave de partição não resultar em **"pontos activos"** na sua aplicação. O armazenamento total para documentos com a mesma chave partição também não pode exceder 10 GB de armazenamento. 

### <a name="examples-of-good-partition-keys"></a>Exemplos de chaves de boa partição
Eis alguns exemplos de como escolher a chave de partição para a sua aplicação:

* Se está a implementar um back-end de perfil de utilizador, o ID de utilizador é uma boa escolha para chave partição.
* Se está a armazenar dados IoT, por exemplo, estado do dispositivo, um ID de dispositivo é uma boa escolha para chave partição.
* Se estiver a utilizar DocumentDB o registo de dados de séries de tempo, o ID do nome do anfitrião ou processo é uma boa escolha para chave partição.
* Se tiver uma arquitetura de inquilino com várias, o ID de inquilino é uma boa escolha para chave partição.

Note que em alguns casos de utilização (por exemplo, os perfis de utilizador e IoT descritos acima), a tecla partição poderá ser a mesma como o seu id (tecla de documento). Em outras pessoas, como os dados da série de tempo, poderá ter uma chave de partição é diferente do id.

### <a name="partitioning-and-loggingtime-series-data"></a>Criação de partições e registo/tempo-séries de dados
Um dos casos de utilização mais comuns de DocumentDB concebido para o registo e telemetria. É importante escolher uma chave de boa partição uma vez que poderá ter de leitura/escrita grandes volumes de dados. A escolha irá dependem do seu ler e escrever taxas e tipos de consultas que esperar para executar. Eis algumas sugestões sobre como escolher uma chave de boa partição.

- Se o seu caso utilize envolve uma taxa de pequenas escreve acculumating durante um longo período de tempo e preciso de consulta por intervalos de selos de tempo e outros filtros, em seguida, utilizando um conjunto do carimbo de data/hora, por exemplo, de data como uma chave de partição é uma boa abordagem. Esta opção permite-lhe a consulta sobre todos os dados para uma data a partir de uma única partição. 
- Se a sua carga de trabalho for grossa de escrita, que é geralmente mais comuns, deve utilizar uma chave de partição que não se baseia carimbo para que DocumentDB pode distribuir escritas uniformemente ao longo de um número de partições. Um nome de anfitrião, ID do processo, atividade ID ou outra propriedade com cardinalidade alta Eis uma boa escolha. 
- Uma abordagem de terceira é híbrida um onde tiver várias coleções de sites, uma para cada dia/mês e a chave de partição é uma propriedade granular, como o nome do anfitrião. Isto tem o benefício que pode definir níveis de desempenho diferentes, com base na janela de tempo, por exemplo, a coleção do mês atual está aprovisionada com débito superior uma vez que serve de operações de leitura e escrita, Considerando que meses anteriores com baixar débito, uma vez que servem apenas lê.

### <a name="partitioning-and-multi-tenancy"></a>Criar partições e multitenancy
Se estiver a implementar uma aplicação do inquilino com várias utilizando DocumentDB, existem dois principais padrões de execução arrendamento com DocumentDB – uma partição chave por inquilino e uma colecção de por inquilino. Eis as vantagens e desvantagens para cada:

* Uma partição de chave por inquilino: neste modelo, os inquilinos são co-instalados numa única coleção. Mas podem ser efetuadas consultas e inserções para documentos dentro de um inquilino única contra uma única partição. Também pode implementar lógica transaccional em todos os documentos dentro de um inquilino. Uma vez que múltiplos inquilinos do partilham uma coleção de, pode guardar os custos de armazenamento e débito ao agrupamento de recursos para inquilinos dentro de uma única colecção em vez de aprovisionamento espaço extra para cada inquilino. A desvantagem é que não possui isolamento de desempenho por inquilino. Aplica desempenho/débito aumenta a vs as coleções de sites inteira aumentos alvo para inquilinos.
* Uma coleção por inquilino: cada inquilino tem as suas próprias coleção. Neste modelo, pode reservar desempenho por inquilino. Com novo consumo com base comparar modelo do DocumentDB, este modelo é mais rentável para aplicações do inquilinos com várias com um pequeno número de inquilinos.

Também pode utilizar uma abordagem de combinação/camadas que collocates pequenas inquilinos e migra maiores inquilinos para os seus próprios coleção.

## <a name="next-steps"></a>Próximos passos
Neste artigo, descrevemos funciona como a partições no Azure DocumentDB, como pode criar coleções de sites com partições e como pode escolher uma chave de boa partição para a sua aplicação. 

-   Execute escala e testar com DocumentDB desempenho. Consulte o artigo [Desempenho e escala testes com Azure DocumentDB](documentdb-performance-testing.md) para uma amostra.
-   Começar a codificação com o [SDK](documentdb-sdk-dotnet.md) ou a [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx)
-   Saiba mais sobre [débito aprovisionado em DocumentDB](documentdb-performance-levels.md)
-   Se pretender para personalizar como a aplicação executa a partições, pode ligar-se na sua própria implementação de criação de partições do lado do cliente. Consulte o artigo [suporte de criação de partições do lado do cliente](documentdb-sharding.md).

[1]: ./media/documentdb-partition-data/partitioning.png
[2]: ./media/documentdb-partition-data/single-and-partitioned.png
[3]: ./media/documentdb-partition-data/documentdb-migration-partitioned-collection.png  

 
