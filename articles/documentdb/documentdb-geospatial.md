<properties 
    pageTitle="Trabalhar com dados Geoespaciais no Azure DocumentDB | Microsoft Azure" 
    description="Compreenda como criar, índice e espaciais objetos com Azure DocumentDB da consulta." 
    services="documentdb" 
    documentationCenter="" 
    authors="arramac" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="documentdb" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="10/25/2016" 
    ms.author="arramac"/>
    
# <a name="working-with-geospatial-data-in-azure-documentdb"></a>Trabalhar com dados Geoespaciais Azure DocumentDB

Este artigo é uma introdução à funcionalidade geoespaciais no [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/). Depois de ler isto, será capaz de responder às seguintes questões:

- Como posso armazenar dados espaciais no Azure DocumentDB?
- Como pode a consulta dados geoespaciais no Azure DocumentDB no SQL e LINQ?
- Como ativar ou desativar a indexação espacial na DocumentDB?

Consulte o artigo este [Github project](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs) para obter exemplos de código.

## <a name="introduction-to-spatial-data"></a>Introdução a dados espaciais

Dados espaciais descreve a posição e forma de objetos no espaço. Na maioria das aplicações, estes correspondem à objetos no terra, ou seja, os dados geoespaciais. Dados espaciais podem ser utilizados para representar a localização de uma pessoa, um local de interesse ou o limite de uma cidade ou um lake. Casos de utilização comum envolvem frequentemente consultas proximidade, por exemplo, "Localizar todas as café junto à minha localização atual". 

### <a name="geojson"></a>GeoJSON
DocumentDB suporta a indexação e consultar de geoespaciais ponto de dados que é representado através da [especificação de GeoJSON](http://geojson.org/geojson-spec.html). Estruturas de dados GeoJSON são sempre objectos JSON válidos, para que podem ser armazenados e consultado utilizando DocumentDB sem as ferramentas de especializadas ou bibliotecas. O SDK DocumentDB fornecem helper classes e métodos que tornam mais fácil trabalhar com dados espaciais. 

### <a name="points-linestrings-and-polygons"></a>Pontos, linestrings e polígonos
Um **ponto** indica uma única posição no espaço. Em dados geoespaciais, um ponto representa a localização exata, o que pode ser uma morada de uma loja de compras, num local público, um automóveis ou uma cidade.  Um ponto é representado em GeoJSON (e DocumentDB) utilizando o respetiva coordenadas par ou longitude e latitude. Eis um exemplo JSON para um ponto.

**Pontos DocumentDB**

    {
       "type":"Point",
       "coordinates":[ 31.9, -4.8 ]
    }

>[AZURE.NOTE] A especificação de GeoJSON Especifica longitude em primeiro lugar e latitude segundo. Como noutras aplicações de mapeamento, longitude e latitude são ângulos e representado em termos de graus. Valores de longitude medem a partir do primeiro meridiano e se encontrarem entre-180 e 180.0 graus e latitude valores são medidos a partir de Equador e se encontrarem entre-90.0 e 90.0 graus. 
>
> DocumentDB interpreta coordenadas conforme representado pelo sistema de referência WGS 84. Consulte o artigo abaixo para obter mais detalhes sobre os sistemas de referência das coordenadas.

Isto pode ser incorporado num documento de DocumentDB como é mostrado neste exemplo de um perfil de utilizador que contém os dados de localização:

**Utilizar o perfil com a localização armazenada no DocumentDB**

    {
       "id":"documentdb-profile",
       "screen_name":"@DocumentDB",
       "city":"Redmond",
       "topics":[ "NoSQL", "Javascript" ],
       "location":{
          "type":"Point",
          "coordinates":[ 31.9, -4.8 ]
       }
    }

Para além de pontos, GeoJSON também suporta LineStrings e polígonos. **LineStrings** representar uma série de duas ou mais pontos espaço e segmentos de linha que ligação-los. Em dados geoespaciais, linestrings frequentemente são utilizadas para representar auto-estradas ou rios. Um **Polígono** é um limite de pontos ligados de que forma uma LineString fechada. Polígonos são geralmente utilizados para representar formações naturais como lagos ou esquerdas jurisdições como cidades e Estados-membros. Eis um exemplo de um polígono no DocumentDB. 

**Polígonos no DocumentDB**

    {
       "type":"Polygon",
       "coordinates":[
           [ 31.8, -5 ],
           [ 31.8, -4.7 ],
           [ 32, -4.7 ],
           [ 32, -5 ],
           [ 31.8, -5 ]
       ]
    }

>[AZURE.NOTE] A especificação de GeoJSON requer que para polígonos válidos, o par das coordenadas último fornecido deve ser igual a primeira, para criar uma forma fechada.
>
>Pontos dentro de um polígono tem de ser especificados por ordem para a esquerda. Um polígono especificado na ordem dos ponteiros do relógio representa o inverso da região de dentro da mesma.

Para além de ponto, LineString e polígono, GeoJSON também especifica a representação de para agrupar várias localizações geoespaciais, bem como associar propriedades arbitrários geolocalização como uma **funcionalidade**. Uma vez que estes objetos são JSON válido, podem todas ser armazenados e processadas na DocumentDB. No entanto DocumentDB suporta apenas a indexação automática dos pontos.

### <a name="coordinate-reference-systems"></a>Coordenar sistemas de referência

Uma vez que a forma da terra irregular, coordenadas dos dados geoespaciais é representado em muitos sistemas de referência das coordenadas (CRS), cada uma com os seus próprios molduras de referência e unidades de medida. Por exemplo, o "nacionais grelha da Grã-Bretanha" é um sistema de referência é muito preciso para o Reino Unido, mas não fora da. 

SIR mais popular em utilização hoje é o sistema geodésico mundo [WGS 84](http://earth-info.nga.mil/GandG/wgs84/). Dispositivos GPS e muitos de mapeamento de serviços, incluindo mapas Google e APIs de mapas Bing utilizam WGS 84. DocumentDB suporta a indexação e consultar dados geoespaciais SIR WGS 84 apenas a utilizar. 

## <a name="creating-documents-with-spatial-data"></a>Criar documentos com dados espaciais
Quando criar documentos que contenham valores GeoJSON, estes são indexados automaticamente com um índice espacial de acordo com a política da coleção de indexação. Se estiver a trabalhar com um SDK DocumentDB num idioma dinamicamente escrito como Python ou Node.js, terá de criar GeoJSON válida.

**Criar documentos com dados Geoespaciais Node.js**

    var userProfileDocument = {
       "name":"documentdb",
       "location":{
          "type":"Point",
          "coordinates":[ -122.12, 47.66 ]
       }
    };

    client.createDocument(`dbs/${databaseName}/colls/${collectionName}`, userProfileDocument, (err, created) => {
        // additional code within the callback
    });

Se estiver a trabalhar com o .NET (ou Java) SDK, pode utilizar as novas classes ponto e polígono no espaço de nomes Microsoft.Azure.Documents.Spatial para incorporar informações de localização dentro os objetos de aplicação. Estas classes ajudam a simplificar a serialização e desserialização do dados espaciais para GeoJSON.

**Criar documentos com dados Geoespaciais no .NET**

    using Microsoft.Azure.Documents.Spatial;
    
    public class UserProfile
    {
        [JsonProperty("name")]
        public string Name { get; set; }

        [JsonProperty("location")]
        public Point Location { get; set; }
        
        // More properties
    }
    
    await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri("db", "profiles"), 
        new UserProfile 
        { 
            Name = "documentdb", 
            Location = new Point (-122.12, 47.66) 
        });

Se não tiver as informações de latitude e longitude, mas tiverem o nome de localização como localidade ou país ou endereços físicos, pode procurar as coordenadas reais utilizando um serviço de geocodificação como o Bing Maps restantes serviços. Saiba mais sobre geocodificação mapas Bing [aqui](https://msdn.microsoft.com/library/ff701713.aspx).

## <a name="querying-spatial-types"></a>Consultar tipos espaciais

Agora que recomendamos tirou um olhar sobre como inserir dados geoespaciais, vamos ver como estes dados utilizando DocumentDB utilizando SQL e LINQ da consulta.

### <a name="spatial-sql-built-in-functions"></a>Espaciais funções incorporadas de SQL
DocumentDB suporta as seguintes funções incorporadas abrir Geoespaciais Consortium (OGC) para consultar geoespaciais. Para obter mais detalhes sobre o conjunto completo de funções incorporadas no idioma SQL, consulte [DocumentDB de consulta](documentdb-sql-query.md).

<table>
<tr>
  <td><strong>Utilização</strong></td>
  <td><strong>Descrição</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (point_expr, point_expr)</td>
  <td>Devolve a distância entre as duas expressões de ponto GeoJSON.</td>
</tr>
<tr>
  <td>ST_WITHIN (point_expr, polygon_expr)</td>
  <td>Devolve uma expressão booleana que indica se o ponto de GeoJSON especificado no primeiro argumento estiver dentro polígono GeoJSON no segundo argumento.</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>Devolve um valor booleano que indica se a expressão de ponto ou polígono GeoJSON especificada é válida.</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>Devolve um valor JSON que contém um valor booleano valor se a expressão de ponto ou polígono GeoJSON especificada é válida e inválido, para além do motivo como um valor de cadeia.</td>
</tr>
</table>

Funções espaciais podem ser utilizadas para executar consultas de proximidade contra dados espaciais. Por exemplo, eis uma consulta que devolve todos os documentos da família que estão dentro de 30 km da localização especificada utilizando a função incorporada ST_DISTANCE. 

**Consulta**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Resultados**

    [{
      "id": "WakefieldFamily"
    }]

Se incluir indexação espacial na política de indexação, em seguida, "consultas distância" serão servidas eficazmente através de um índice. Para obter mais detalhes sobre a indexação espacial, consulte a secção abaixo. Se não tiver um índice espacial para caminhos especificados, ainda pode realizar consultas espaciais ao especificar `x-ms-documentdb-query-enable-scan` cabeçalho de pedido com o conjunto de valor para "true". No .NET, pode fazê-lo ao passar o argumento **FeedOptions** opcional para consultas com [EnableScanInQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery) definida como verdadeiro. 

ST_WITHIN podem ser utilizados para verificar se um ponto de se situar dentro de um polígono. Frequentemente polígonos são utilizados para representar os limites de como códigos postais, limites de estado ou formações naturais. Novamente se incluir indexação espacial na política de indexação, em seguida, "tem" consultas serão servidas eficazmente através de um índice. 

Argumentos polígono no ST_WITHIN podem conter apenas um único toque, ou seja, os polígonos não pode conter buracos nas mesmas. 

**Consulta**

    SELECT * 
    FROM Families f 
    WHERE ST_WITHIN(f.location, {
        'type':'Polygon', 
        'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })

**Resultados**

    [{
      "id": "WakefieldFamily",
    }]
    
>[AZURE.NOTE] Semelhante a como sem correspondência de tipos de funciona no DocumentDB consulta, se o valor de localização especificado no quer argumento está mal ou é inválido, em seguida, irá devolver **indefinido** e documento avaliado para ignorado dos resultados da consulta. Se a sua consulta não devolver resultados, execute ST_ISVALIDDETAILED para depuração por que motivo o tipo de spatail é inválido.     

DocumentDB também suporta a realizar consultas inversas, ou seja, pode indexar polígonos ou linhas na DocumentDB, em seguida, de consulta para as áreas que contêm um ponto especificado. Este padrão é geralmente utilizado logística para identificar, por exemplo, quando um carro introduz ou deixa de uma área designada. 

**Consulta**

    SELECT * 
    FROM Areas a 
    WHERE ST_WITHIN({'type': 'Point', 'coordinates':[31.9, -4.8]}, a.location)


**Resultados**

    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon", 
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]

ST_ISVALID e ST_ISVALIDDETAILED podem ser utilizados para verificar se um objecto espacial é válido. Por exemplo, a seguinte consulta verifica a validade de um ponto com uma fora do valor de latitude intervalo (-132.8). ST_ISVALID devolve apenas um valor Booleano e ST_ISVALIDDETAILED devolve o valor Booleano e uma cadeia que contém o motivo por que é considerada inválida.

**Consulta**

    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })

**Resultados**

    [{
      "$1": false
    }]

Estas funções podem também ser utilizadas para validar polígonos. Por exemplo, aqui utilizamos ST_ISVALIDDETAILED para validar um polígono que não está fechado. 

**Consulta**

    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})

**Resultados**

    [{
       "$1": { 
          "valid": false, 
          "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points." 
        }
    }]
    
### <a name="linq-querying-in-the-net-sdk"></a>LINQ consultar de .NET SDK

O .NET SDK DocumentDB também fornecedores stub métodos `Distance()` e `Within()` para utilização no prazo de expressões LINQ. O fornecedor de DocumentDB LINQ traduz estas chamadas método para as chamadas de função incorporada SQL equivalentes (ST_DISTANCE e ST_WITHIN respetivamente). 

Eis um exemplo de uma consulta LINQ localiza todos os documentos na coleção de DocumentDB cujo valor "localização" é num raio de 30km de especificado aponte utilizando LINQ.

**Consulta LINQ para distância**

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(u => u.ProfileType == "Public" && a.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }

Da mesma forma, eis uma consulta para encontrar todos os documentos cuja "localização" está dentro caixa/polígono especificado. 

**LINQ de consulta para dentro**

    Polygon rectangularArea = new Polygon(
        new[]
        {
            new LinearRing(new [] {
                new Position(31.8, -5),
                new Position(32, -5),
                new Position(32, -4.7),
                new Position(31.8, -4.7),
                new Position(31.8, -5)
            })
        });

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }


Agora que recomendamos tirou um olhar sobre como obter documentos utilizando LINQ e SQL da consulta, vamos um olhar sobre como configurar DocumentDB a indexação espacial.

## <a name="indexing"></a>Indexação

Tal como foi descrito no papel [Esquema desconhecido indexação com Azure DocumentDB](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) , podemos concebido motor de base de dados do DocumentDB para ser verdadeiramente desconhecido de esquema e fornecer suporte primeira classe para JSON. O motor de base de dados de escrita otimizada do DocumentDB vierem compreende dados espaciais (pontos, polígonos e linhas) representados na norma GeoJSON.

Em Eis um resumo dos, a geometria é projectada das coordenadas geodésicas para um plano 2D, em seguida, dividida gradualmente em células utilizando um **quadtree**. Estas células são mapeadas para 1D com base na localização da célula dentro de uma **curva de enchimento Hilbert espaço**, que preserva localidade dos pontos. Para além disso quando são indexados dados de localização,-acede através de um processo conhecido como **tessellation**, ou seja, todas as células que se intersetam uma localização estão identificadas e armazenadas como teclas no índice de DocumentDB. No momento da consulta, argumentos, como pontos e polígonos são também tessellated para extrair os intervalos de ID de célula relevantes, em seguida, utilizados para obter dados a partir do índice.

Se especificar uma política de indexação que inclui o índice remissivo espacial / * (todos os caminhos), em seguida, todos os pontos de que se encontram dentro da coleção estão indexados para consultas espaciais eficientes (ST_WITHIN e ST_DISTANCE). Índices espaciais não têm um valor de precisão e utilizar sempre um valor de precisão predefinido.

>[AZURE.NOTE] DocumentDB suporta a indexação automática de pontos, polígonos e LineStrings

O fragmento de JSON seguinte mostra uma política de indexação com a indexação espacial ativado, ou seja, índice qualquer ponto do GeoJSON que se encontram dentro de documentos para consultar espacial. Se estiver a modificar a política de indexação utilizando o Portal do Azure, pode especificar o JSON seguinte para a política de indexação para ativar a indexação na sua coleção de espacial.

**Colecção de indexação política JSON com espacial ativada para os pontos e polígonos**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "includedPaths":[
          {
             "path":"/*",
             "indexes":[
                {
                   "kind":"Range",
                   "dataType":"String",
                   "precision":-1
                },
                {
                   "kind":"Range",
                   "dataType":"Number",
                   "precision":-1
                },
                {
                   "kind":"Spatial",
                   "dataType":"Point"
                },
                {
                   "kind":"Spatial",
                   "dataType":"Polygon"
                }                
             ]
          }
       ],
       "excludedPaths":[
       ]
    }

Eis um fragmento de código no .NET que mostra como criar uma coleção de com espacial indexação ativada para todos os caminhos que contém pontos. 

**Criar uma coleção de com a indexação espacial**

    DocumentCollection spatialData = new DocumentCollection()
    spatialData.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point)); //override to turn spatial on by default
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), spatialData);

E Eis como pode modificar um conjunto existente para tirar partido da indexação espacial substitua quaisquer pontos que estão armazenados nos documentos.

**Modificar um conjunto existente com a indexação espacial**

    Console.WriteLine("Updating collection with spatial indexing enabled in indexing policy...");
    collection.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point));
    await client.ReplaceDocumentCollectionAsync(collection);

    Console.WriteLine("Waiting for indexing to complete...");
    long indexTransformationProgress = 0;
    while (indexTransformationProgress < 100)
    {
        ResourceResponse<DocumentCollection> response = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
        indexTransformationProgress = response.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromSeconds(1));
    }

> [AZURE.NOTE] Se a localização do valor GeoJSON dentro do documento está mal ou é inválido, em seguida, que irá não obter indexado para consultar espacial. Pode validar os valores de localização utilizando ST_ISVALID e ST_ISVALIDDETAILED.
>
> Se a sua definição de coleções de sites inclui uma chave de partição, não é comunicado progresso de transformação de indexação. 

## <a name="next-steps"></a>Próximos passos
Agora que já extraídos sobre como começar com o suporte de geoespaciais no DocumentDB, pode:

- Iniciar a codificação com os [exemplos de código Geoespaciais .NET no Github](https://github.com/Azure/azure-documentdb-dotnet/blob/fcf23d134fc5019397dcf7ab97d8d6456cd94820/samples/code-samples/Geospatial/Program.cs)
- Obter mãos com geoespaciais consultar na [DocumentDB parques de consulta](http://www.documentdb.com/sql/demo#geospatial)
- Saiba mais sobre [DocumentDB consulta](documentdb-sql-query.md)
- Saiba mais sobre [As políticas de indexação do DocumentDB](documentdb-indexing-policies.md)
