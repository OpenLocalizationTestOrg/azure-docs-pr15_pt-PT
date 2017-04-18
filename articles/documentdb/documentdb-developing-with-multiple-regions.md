<properties
   pageTitle="Desenvolver com vários regiões DocumentDB | Microsoft Azure"
   description="Saiba como aceder aos dados em várias regiões a partir do Azure DocumentDB, um serviço de base de dados NoSQL totalmente gerido."
   services="documentdb"
   documentationCenter=""
   authors="kiratp"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="documentdb"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="kipandya"/>
   
# <a name="developing-with-multi-region-documentdb-accounts"></a>Desenvolver com contas de DocumentDB da região com várias

> [AZURE.NOTE] Distribuição global DocumentDB bases de dados é ativada automaticamente para todas as contas de DocumentDB recentemente criadas e ficará disponível. Estamos a trabalhar para ativar a distribuição global em todas as contas existentes, mas entretanto, se pretender que a distribuição globais ativada na sua conta, consulte [contactar o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) e podemos irá ativá-lo para agora.

Forma a tirar partido da [distribuição global](documentdb-distribute-data-globally.md), aplicações de cliente podem especificar a lista ordenada preferência de regiões para ser utilizado para executar operações de documento. Isto pode ser feito ao definir a política de ligação. Com base na configuração da conta de Azure DocumentDB, disponibilidade regional atual e a lista de preferência especificada, o ponto final mais ideal será escolhido pelo SDK para executar escrita e operações de leitura. 

Esta lista de preferência for especificada quando a inicialização uma ligação utilizando o cliente de DocumentDB SDK. Os SDK aceitar um parâmetro opcional "PreferredLocations" Isto é uma lista ordenada das regiões Azure.

O SDK irá enviar automaticamente todas as gravações para atuais escrever região. 

Todos os lê serão enviadas para a primeira região disponível na lista PreferredLocations. Se o pedido falhar, o cliente irá falhar para baixo na lista à região de seguinte e assim sucessivamente. 

O cliente que SDK tentará apenas ler a partir das regiões PreferredLocations especificado no. Por isso, por exemplo, se a conta de base de dados está disponível em três regiões, mas o cliente especifica apenas dois das regiões que não sejam escrita para PreferredLocations, em seguida, sem lê será servida sair da região de escrita, mesmo no caso de activação pós-falha.

A aplicação pode verificar o ponto final de escrita atual e ler o ponto final escolhido pelo SDK verificando duas propriedades, WriteEndpoint e ReadEndpoint, disponível na versão SDK 1.8 e acima. 

Se a propriedade PreferredLocations não estiver definida, todos os pedidos de serão servidos da região do escrita atual. 


## <a name="net-sdk"></a>.NET SDK
O SDK pode ser utilizado sem alterações código. Neste caso, o SDK automaticamente encaminha ambas as lê e escreve à região de escrita atual. 

Na versão 1,8 e posterior do .NET SDK, o parâmetro ConnectionPolicy para o construtor de DocumentClient tem uma propriedade denominada Microsoft.Azure.Documents.ConnectionPolicy.PreferredLocations. Esta propriedade é do tipo de coleção de `<string>` e devem conter uma lista de nomes de região. Os valores de cadeia são formatados à coluna Nome da região as [Regiões do Azure]  [ regions] página, sem espaços antes ou depois do primeiro e último caráter respetivamente.

A escrita atual e leia os pontos finais estão disponíveis no DocumentClient.WriteEndpoint e DocumentClient.ReadEndpoint respetivamente.

> [AZURE.NOTE] Os URLs para os pontos finais não devem ser considerados como constantes longa vida. O serviço poderá actualizar estas em qualquer ponto. O SDK trata esta alteração automaticamente.

    // Getting endpoints from application settings or other configuration location
    Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
    string accountKey = Properties.Settings.Default.GlobalDatabaseKey;

    //Setting read region selection preference 
    connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
    connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
    connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

    // initialize connection
    DocumentClient docClient = new DocumentClient(
        accountEndPoint,
        accountKey,
        connectionPolicy);

    // connect to DocDB 
    await docClient.OpenAsync().ConfigureAwait(false);


## <a name="nodejs-javascript-and-python-sdks"></a>NodeJS, JavaScript e Python SDK
O SDK pode ser utilizado sem alterações código. Neste caso, o SDK automaticamente reencaminhará as operações de leitura e escrita atuais escreve região. 

Na versão 1,8 e versões posterior do cada SDK, o parâmetro ConnectionPolicy para o construtor de DocumentClient uma nova propriedade denominado DocumentClient.ConnectionPolicy.PreferredLocations. Este é o parâmetro é uma matriz de cadeias que demora uma lista de nomes de região. Os nomes são formatados por coluna Nome da região as [Regiões do Azure]  [ regions] página. Também pode utilizar as constantes predefinidas no objeto conveniência AzureDocuments.Regions

A escrita atual e leia os pontos finais estão disponíveis no DocumentClient.getWriteEndpoint e DocumentClient.getReadEndpoint respetivamente.

> [AZURE.NOTE] Os URLs para os pontos finais não devem ser considerados como constantes longa vida. O serviço poderá actualizar estas em qualquer ponto. O SDK processará automaticamente esta alteração.

Segue-se um exemplo de código para NodeJS/Javascript. Python e Java vai ter de seguir o mesmo padrão.

    // Creating a ConnectionPolicy object
    var connectionPolicy = new DocumentBase.ConnectionPolicy();
    
    // Setting read region selection preference, in the following order -
    // 1 - West US
    // 2 - East US
    // 3 - North Europe
    connectionPolicy.PreferredLocations = ['West US', 'East US', 'North Europe'];
    
    // initialize the connection
    var client = new DocumentDBClient(host, { masterKey: masterKey }, connectionPolicy);


## <a name="rest"></a>RESTO 
Assim que uma conta de base de dados foi disponibilizada em várias regiões, os clientes podem consultar a sua disponibilidade executando num pedido GET no URI seguinte.

    https://{databaseaccount}.documents.azure.com/

O serviço irá devolver uma lista das regiões e os respetivos correspondente DocumentDB ponto final URIs para as réplicas. A área de escrita atual será indicada na resposta. O cliente, em seguida, pode selecionar o ponto final adequado para contra REST API pedidos de modo que se segue.

Resposta de exemplo

    {
        "_dbs": "//dbs/",
        "media": "//media/",
        "writableLocations": [
            {
                "Name": "West US",
                "DatabaseAccountEndpoint": "https://globaldbexample-westus.documents.azure.com:443/"
            }
        ],
        "readableLocations": [
            {
                "Name": "East US",
                "DatabaseAccountEndpoint": "https://globaldbexample-eastus.documents.azure.com:443/"
            }
        ],
        "MaxMediaStorageUsageInMB": 2048,
        "MediaStorageUsageInMB": 0,
        "ConsistencyPolicy": {
            "defaultConsistencyLevel": "Session",
            "maxStalenessPrefix": 100,
            "maxIntervalInSeconds": 5
        },
        "addresses": "//addresses/",
        "id": "globaldbexample",
        "_rid": "globaldbexample.documents.azure.com",
        "_self": "",
        "_ts": 0,
        "_etag": null
    }


-   Pedidos de mensagem, colocar e eliminar tem de ir para a escrita indicada URI
-   Obtém todos os e outros pedidos só de leitura (por exemplo consultas) podem ir para qualquer ponto final da escolha do cliente

Escreva pedidos para só de leitura regiões irão falhar com o código de erro HTTP 403 ("proibido").

Se alterar a região de escrita após fase de deteção inicial do cliente, escritas subsequentes à região de escrita anterior irão falhar com o código de erro HTTP 403 ("proibido"). O cliente, em seguida, deve obter a lista de regiões novamente para obter a região de escrita atualizado.

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre os dados da distribuição globalmente DocumentDB nos seguintes artigos:

- [Distribuir dados globalmente com DocumentDB](documentdb-distribute-data-globally.md)
- [Níveis de consistência](documentdb-consistency-levels.md)
- [Como funciona o débito com várias regiões](documentdb-manage.md#how-throughput-works-with-multiple-regions)
- [Adicionar regiões através do portal Azure](documentdb-portal-global-replication.md)

[regions]: https://azure.microsoft.com/regions/ 
