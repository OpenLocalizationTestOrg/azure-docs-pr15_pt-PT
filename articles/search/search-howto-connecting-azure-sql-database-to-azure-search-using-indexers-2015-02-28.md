<properties 
    pageTitle="Ligação de base de dados Azure SQL a procura Azure utilizando indexadores | Microsoft Azure | Indexadores" 
    description="Saiba como importar dados a partir de base de dados do SQL Azure a um índice de pesquisa do Azure utilizando indexadores." 
    services="search" 
    documentationCenter="" 
    authors="chaosrealm" 
    manager="pablocas" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="05/26/2016" 
    ms.author="eugenesh"/>

#<a name="connecting-azure-sql-database-to-azure-search-using-indexers"></a>Ligar a base de dados do SQL Azure a procura de Azure utilizando indexadores

Serviço de procura do Azure é um serviço de pesquisa de nuvem alojado que torna mais fácil fornecer uma experiência de pesquisa excelente. Antes de procurar, tem de preencher um índice de pesquisa do Azure com os seus dados. Se os dados residem na base de dados Azure SQL, a nova **pesquisa Azure indexador para base de dados do SQL Azure** (ou **indexador Azure SQL** para abreviada) na pesquisa Azure pode automatizar o processo de indexação. Isto significa que tiver menos código para escrever e menos infraestrutura de interessam.

Atualmente, indexadores só funcionam com base no Azure SQL, SQL Server Azure VMs e [Azure DocumentDB](../documentdb/documentdb-search-indexer.md). Neste artigo, iremos concentrar-nos indexadores que funcionam com a base de dados do SQL Azure. Se gostaria ver o suporte para origens de dados adicionais, fornecer os seus comentários no [Fórum de comentários do Azure pesquisa](https://feedback.azure.com/forums/263029-azure-search/).

Este artigo aborda mecânica da utilização indexadores, mas recomendamos também irá desagregar em funcionalidades e comportamentos só estão disponíveis com bases de dados do SQL (por exemplo, integrada alterações).

## <a name="indexers-and-data-sources"></a>Indexadores e origens de dados

Para instalar e configurar um indexador Azure SQL, pode ligar a [Azure pesquisa REST API](http://go.microsoft.com/fwlink/p/?LinkID=528173) para criar e gerir **indexadores** e **origens de dados**. 

Também pode utilizar a [classe de indexador](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.indexer.aspx) no [.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)ou Assistente de importação de dados no [portal do Azure](https://portal.azure.com) para criar e agendar um indexador.

Uma **origem de dados** indica quais os dados para indexar, credenciais necessárias para aceder aos dados e as políticas que permitem o Azure pesquisa eficazmente identificar alterações nos dados (novos, modificadas ou eliminadas linhas). É definida como um recurso independente para que possa ser utilizado por vários indexadores.

Um **indexador** é um recurso que liga as origens de dados com índices de procura de destino. Um indexador é utilizado das seguintes formas:
 
- Execute uma única cópia dos dados para preencher um índice remissivo.
- Atualize um índice remissivo com alterações da origem de dados com base numa agenda.
- Execute a pedido para atualizar um índice remissivo, conforme necessário. 

## <a name="when-to-use-azure-sql-indexer"></a>Quando utilizar indexador Azure SQL

Dependendo de fatores várias relativas aos seus dados, a utilização do indexador Azure SQL pode ou não é adequada. Se os seus dados couber os seguintes requisitos, pode utilizar indexador Azure SQL: 

- Todos os dados provêm de uma tabela única ou vista
    - Se os dados são dispersos em várias tabelas, pode criar uma vista e utilizar essa vista com o indexador. No entanto, tenha em atenção de que se utilizar uma vista, não será possível utilizar a deteção de alteração do SQL Server integrada. Consulte o artigo nesta secção para obter mais detalhes. 
- Os tipos de dados utilizados na origem de dados são suportados pelo indexador. A maioria, mas não em todos os de SQL tipos são suportados. Para obter detalhes, consulte o artigo [mapeamento de tipos de dados no Azure pesquisa](http://go.microsoft.com/fwlink/p/?LinkID=528105). 
- Não precisa de quase em tempo real atualizações para o índice quando uma linha é alterado. 
    - O indexador pode reindexar a tabela no máximo em 5 minutos. Se os dados forem alterados com frequência e as alterações têm de ser refletidas no índice remissivo dentro segundos ou minutos única, recomendamos que utilize [API de índice de pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn798930.aspx) diretamente. 
- Se tiver um conjunto de dados grande e planear a executar o indexador numa agenda, o esquema nos eficazmente identificar permite alteradas (e eliminado, se aplicável) linhas. Para obter mais detalhes, consulte o artigo "Capturar alteradas e eliminadas linhas" abaixo. 
- O tamanho dos campos indexados numa linha não excede o tamanho máximo de uma pesquisa de Azure indexação pedido, o que é 16 MB. 

## <a name="create-and-use-an-azure-sql-indexer"></a>Criar e utilizar um indexador Azure SQL

Em primeiro lugar, crie a origem de dados: 

    POST https://myservice.search.windows.net/datasources?api-version=2015-02-28 
    Content-Type: application/json
    api-key: admin-key
    
    { 
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }


Pode obter a cadeia de ligação do [Azure clássica Portal](https://portal.azure.com); utilizar o `ADO.NET connection string` opção.

Em seguida, crie o índice de pesquisa do Azure de destino se ainda não tiver um. Pode fazê-lo a partir do [portal da IU](https://portal.azure.com) ou ao utilizar a [API do índice de criar](https://msdn.microsoft.com/library/azure/dn798941.aspx).  Certifique-se de que o esquema do seu índice de destino é compatível com o esquema da tabela de origem - consulte [mapeamento entre SQL e Azure tipos de dados de pesquisa](#TypeMapping) para obter detalhes.

Por fim, crie o indexador ao atribuir-lhe um nome e referenciar o índice de origem e destino de dados:

    POST https://myservice.search.windows.net/indexers?api-version=2015-02-28 
    Content-Type: application/json
    api-key: admin-key
    
    { 
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }

Um indexador criado desta forma não tem uma agenda. Este é executado automaticamente uma vez assim que é criado. Pode executá-la novamente em qualquer altura utilizando um pedido de **Executar indexador** :

    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2015-02-28 
    api-key: admin-key

Pode personalizar vários aspetos de comportamento do indexador, como o tamanho de lote e podem ser ignorados quantos documentos antes da execução de um indexador vai ser falhou. Para obter mais detalhes, consulte o artigo [Criar API do indexador](https://msdn.microsoft.com/library/azure/dn946899.aspx).
 
Poderá ter de permitir que os serviços Azure ligar à sua base de dados. Consulte o artigo [Ligar a partir do Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) para obter instruções sobre como fazê-lo.

Para monitorizar o estado do indexador e execução histórico (número de itens indexados, falhas, etc.), utilize um pedido de **Estado do indexador** : 

    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2015-02-28 
    api-key: admin-key

A resposta deverá ter um aspeto semelhante ao seguinte: 

    {
        "@odata.context":"https://myservice.search.windows.net/$metadata#Microsoft.Azure.Search.V2015_02_28.IndexerExecutionInfo",
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2015-02-21T00:23:24.957Z",
            "endTime":"2015-02-21T00:36:47.752Z",
            "errors":[],
            "itemsProcessed":1599501,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null 
        },
        "executionHistory":
        [
            {
                "status":"success",
                "errorMessage":null,
                "startTime":"2015-02-21T00:23:24.957Z",
                "endTime":"2015-02-21T00:36:47.752Z",
                "errors":[],
                "itemsProcessed":1599501,
                "itemsFailed":0,
                "initialTrackingState":null,
                "finalTrackingState":null 
            },
            ... earlier history items 
        ]
    }

Histórico de execução contém até 50 das execuções recentemente concluídas, que são ordenadas por ordem cronológica inversa (para que a execução do mais recente vem primeira na resposta). Podem encontrar informações adicionais sobre a resposta na [Obter o estado do indexador](http://go.microsoft.com/fwlink/p/?LinkId=528198)

## <a name="run-indexers-on-a-schedule"></a>Executar indexadores numa agenda

Também pode dispor indexador para executar periodicamente numa agenda. Para fazer isto, basta adicione a propriedade de **agenda** quando criar ou atualizar o indexador. O exemplo abaixo mostra um pedido de colocar para atualizar o indexador:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2015-02-28 
    Content-Type: application/json
    api-key: admin-key 

    { 
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

É necessário o parâmetro de **intervalo** . O intervalo de refere-se para o tempo entre o início do duas execuções do indexador consecutivas. O intervalo mais pequeno permitido é de 5 minutos; célula mais longa é um dia. Este deve ser formatado como um valor de "dayTimeDuration" XSD (um subconjunto de um valor de [duração ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) restrito). O padrão para este é: `P(nD)(T(nH)(nM))`. Exemplos: `PT15M` para cada 15 minutos, `PT2H` todas as horas de 2.

A **hora de início** de opcional indica quando devem começar as execuções agendadas; Se este for omitido, a hora atual do UTC será utilizada. Desta vez, pode ser no passado – em que será agendado caso a primeira execução como se o indexador tem de ser executado continuamente desde a hora de início.  

Apenas uma execução de um determinado indexador pode executar uma vez. Se um indexador já está a executar quando é suposto seguinte para começar, a execução do é ignorada e currículos no seguinte intervalo, partindo do princípio de outra tarefa de indexador não está a ser executado.

Vamos considere um exemplo para tornar este mais betão. Suponha que estamos a agenda de hora seguinte configurada: 

    "schedule" : { "interval" : "PT1H", "startTime" : "2015-03-01T00:00:00Z" }

Eis o que acontece: 

1. A primeira execução do indexador é iniciado, ou em torno de 1 de Março de 2015 às 12:00 UTC.
1. Partem do pressuposto que esta execução leva-o até 20 minutos (ou em qualquer altura menos de 1 hora).
1. Inicia a execução do segunda, ou em torno de 1 de Março de 2015 1:00:00 
1. Agora suponha que este execução demora mais do que uma hora (isso, é necessário um grande número de documentos para que isto ocorra, mas é uma ilustração útil) – por exemplo, de 70 minutos – para que o que é concluído cerca 2:10:00
1. É agora 2:00 horas, tempo para a execução de terceira começar. No entanto, uma vez que a execução do segunda a partir de 1: 00 é ainda em execução, a execução do terceira é ignorada. A execução do terceira começa em 3: 00.

Pode adicionar, alterar ou eliminar uma agenda para um indexador existente utilizando um pedido de **colocar indexador** . 

## <a name="capturing-new-changed-and-deleted-rows"></a>Capturar novos, alterados e linhas eliminadas

Se estiver a utilizar uma agenda e a sua tabela contém um número de linhas que não sejam comum, deve utilizar uma política de deteção de alteração de dados, para que o indexador pode obter eficientemente apenas as linhas novas ou alteradas sem ter de reindexar a tabela inteira.

### <a name="sql-integrated-change-tracking-policy"></a>SQL integrada política de registo de alterações

Se a sua base de dados do SQL suportar a [monitorização de alterações](https://msdn.microsoft.com/library/bb933875.aspx), recomendamos que utilize **SQL integrada alterar Política Rastreio**. Esta política permite que as alterações mais eficiente e também lhe permite procurar Azure identificar linhas eliminadas sem ter de adicionar uma coluna de explícitas "eliminar contornos" a sua tabela.

Monitorização integrada é suportada começando com as seguintes versões de base de dados do SQL Server:
 
- SQL Server 2008 R2 e posterior, se estiver a utilizar o SQL Server no Azure VMs. 
- Azure SQL da base de dados V12, se estiver a utilizar a base de dados do SQL Azure.

Quando utilizar a política, de monitorização SQL integrada não especificar uma política de deteção de eliminação de dados separado - esta política tem suporte incorporado para identificar eliminadas linhas.

Esta política só pode ser utilizada com tabelas; não podem ser utilizado com vistas. Tem de ativar o registo de alterações para a tabela que estiver a utilizar o antes de poder utilizar esta política. Consulte o artigo [Ativar e desativar o registo de alterações](https://msdn.microsoft.com/library/bb964713.aspx) para obter instruções. 

Para utilizar esta política, criar ou atualizar a sua origem de dados da seguinte forma:
 
    { 
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" }, 
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy" 
      }
    }

### <a name="high-water-mark-change-detection-policy"></a>Política de deteção de alteração de marca de água de alta

Enquanto a política de SQL integrada registo de alterações é recomendada, não poderá utilizá-lo se os dados estiverem numa vista, ou se estiver a utilizar uma versão anterior do base de dados Azure SQL. Neste caso, considere utilizar a política de marca de água de alta. Esta política pode ser utilizada se a sua tabela contém uma coluna que cumpre os seguintes critérios:

- Insere todas as especificar um valor para a coluna. 
- Todas as atualizações para um item também alteram o valor da coluna. 
- O valor desta coluna aumenta com cada alteração.
- Consultas que utilizem um `WHERE` cláusula semelhante ao `WHERE [High Water Mark Column] > [Current High Water Mark Value]` pode ser executada de forma eficaz.

Por exemplo, uma coluna de indexados **rowversion** é um candidatos ideais para a coluna de marca de água de alta. Para utilizar esta política, criar ou atualizar a sua origem de dados da seguinte forma: 

    { 
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" }, 
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
           "highWaterMarkColumnName" : "[a row version or last_updated column name]" 
      }
    }

### <a name="soft-delete-column-deletion-detection-policy"></a>Política de deteção de eliminação de coluna eliminar contornos

Quando são eliminadas linhas da tabela de origem, provavelmente pretende eliminar essas linhas de índice de pesquisa. Se utilizar a alteração SQL integrada política rastreio, esta é a tratar por si. No entanto, a alteração de marca de água de alta política rastreio não ajudá-lo com linhas eliminadas. O que fazer? 

Se as linhas são removidas física a partir da tabela, se encontra fora sorte – não existe nenhuma forma inferir a presença de registos que já não existe.  No entanto, pode utilizar a técnica "contornos-delete" para marcar linhas como logicamente eliminados sem removê-los a partir da tabela ao adicionar uma coluna e marcar as linhas como eliminadas utilizando um valor de marcador dessa coluna.

Ao utilizar a técnica de contornos eliminar, pode especificar os contornos Eliminar política da seguinte forma quando criar ou atualizar a origem de dados: 

    { 
        …, 
        "dataDeletionDetectionPolicy" : { 
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]", 
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]" 
        }
    }

Tenha em atenção que a **softDeleteMarkerValue** tem de ser uma cadeia – utilize a representação de cadeia do seu valor real. Por exemplo, se tiver uma coluna de número inteiro onde eliminadas linhas são marcadas com o valor 1, utilize `"1"`; Se tiver uma coluna BIT onde eliminadas linhas são marcadas com o valor booleano de true, utilize `"True"`. 

<a name="TypeMapping"></a>
## <a name="mapping-between-sql-data-types-and-azure-search-data-types"></a>Mapeamento entre tipos de dados de pesquisa do Azure e tipos de dados SQL

|Tipo de dados SQL | Permitido tipos de campo de índice de destino |Notas 
|------|-----|----|
|bit|Edm.Boolean, Edm.String| |
|int, smallint, tinyint |Edm.Int32, Edm.Int64, Edm.String| |
| bigint | Edm.Int64, Edm.String | |
| flutuar real, |Edm.Double, Edm.String | |
| smallmoney, numérico decimal dinheiro | Edm.String| Pesquisa Azure não suporta a converter tipos de casas decimais Edm.Double porque este seria perder precisão |
| CARÁCT, nchar, varchar, nvarchar | Edm.String<br/>COLLECTION(EDM.String)|Transformar uma coluna de cadeia Collection(Edm.String) requer a utilização de uma pré-visualização API versão 2015-02-28-pré-visualização. Consulte [Este artigo](search-api-indexers-2015-02-28-preview.md#CreateIndexer) para obter mais detalhes| 
|smalldatetime, datetime, datetime2, data, datetimeoffset |Edm.DateTimeOffset, Edm.String| |
|uniqueidentifer | Edm.String | |
|Geografia | Edm.GeographyPoint | São suportadas apenas geografia instâncias tipo de ponto com 4326 SRID (que é a predefinição) | | 
|ROWVERSION| N/D |Colunas de versão linha não podem ser armazenadas no índice de pesquisa, mas podem ser utilizadas para o registo de alterações | |
| tempo, timespan, binário, varbinary, imagem, xml, geometria, tipos CLR | N/D |Não é suportado |


## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**Q:** Pode utilizar indexador Azure SQL com bases de dados SQL em execução no IaaS VMs no Azure?

R: Sim. No entanto, é necessário permitir que o seu serviço de pesquisa ligar à sua base de dados ao efetuar as seguintes duas coisas. Consulte o artigo [Configurar uma ligação a partir de um indexador Azure pesquisa para o SQL Server numa VM Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md) para obter mais informações.

1. Poderá ter configurar a sua base de dados com um certificado fidedigno para que o serviço de pesquisa pode abrir as ligações de SSL à base de dados.
2. Configure a firewall para permitir o acesso ao endereço IP do seu serviço de pesquisa.

**Q:** Pode utilizar indexador Azure SQL com bases de dados SQL em execução no local? 

R: Recomendamos não recomendamos ou não suportam esta situação, tal como fazê-lo seria necessitam que abrir as bases de dados para o tráfego da Internet. 

**Q:** Pode utilizar indexador Azure SQL com bases de dados que não seja o SQL Server em execução no IaaS no Azure? 

R: Não suportamos neste cenário, porque não testámos indexador com quaisquer bases de dados que não seja do SQL Server.  

**Q:** Pode criar vários indexadores executar uma agenda? 

R: Sim. No entanto, apenas uma indexador pode estar em execução num nó ao mesmo tempo. Se precisar de múltiplas indexadores em execução em simultâneo, considere escalar para cima o serviço de pesquisa mais do que uma unidade de pesquisa. 

**Q:** Executar um indexador afeta carga de trabalho minha consulta? 

R: Sim. Indexador é executado num de nós no seu serviço de pesquisa e recursos que nó são partilhados entre indexação e servir de tráfego de consulta e outros pedidos de API. Se executar intensivos indexação e consulta das cargas de trabalho e encontrar uma taxa alta de 503 erros ou crescente tempos de resposta, considere dimensionamento o seu serviço de pesquisa.
