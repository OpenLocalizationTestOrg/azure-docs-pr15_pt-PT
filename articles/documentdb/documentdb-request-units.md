<properties 
    pageTitle="Pedir unidades na DocumentDB | Microsoft Azure" 
    description="Saiba mais sobre como compreender, especificar e estimar pedido unidade requisitos no DocumentDB." 
    services="documentdb" 
    authors="syamkmsft" 
    manager="jhubbard" 
    editor="mimig" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/29/2016" 
    ms.author="syamk"/>

#<a name="request-units-in-documentdb"></a>Pedir unidades na DocumentDB
Agora disponível: DocumentDB [Calculadora de unidade pedido](https://www.documentdb.com/capacityplanner). Saiba mais em [Estimating necessita do seu débito](documentdb-request-units.md#estimating-throughput-needs).

![Calculadora de débito][5]

##<a name="introduction"></a>Introdução
Este artigo fornece uma descrição geral de unidades pedido no [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/). 

Depois de ler este artigo, poderá atender as seguintes questões:  

-   O que são pedir unidades e pedir taxas?
-   Como especificar a capacidade de unidade pedido para uma coleção de?
-   Como estimar que necessita de unidade de pedido de minha aplicação?
-   O que acontece se excederem o pedido capacidade de unidade para uma coleção de?


##<a name="request-units-and-request-charges"></a>Unidades de pedido e taxas de pedido
DocumentDB proporciona um desempenho e previsível por *reserva de* recursos para satisfazer que necessita de débito da sua aplicação.  Uma vez que a aplicação carregar e aceder a alteração de padrões ao longo do tempo, DocumentDB permite-lhe facilmente aumentar ou diminuir a quantidade de débito reservada disponível para a sua aplicação.

Com DocumentDB, débito reservado é especificado em termos de unidades de pedido de processamento por segundo.  Poderá pensar de unidades pedido como moeda débito, através das quais *reservar* um montante de garantia unidades pedido disponíveis para a aplicação no segundo a segundo.  Cada operação de DocumentDB - escrever um documento, efetuar uma consulta, atualizar um documento - consome IOPS, memória e CPU.  Isto é, cada operação como um *pedido gratuitas*, que é expresso em *unidades de pedido*.  Noções sobre os fatores que causam impacto na taxas de unidade pedido, juntamente com os requisitos de débito da sua aplicação, permite-lhe executar a sua aplicação como o custo de forma eficaz quanto possível. 

##<a name="specifying-request-unit-capacity"></a>Especificar a capacidade de unidade pedido
Ao criar uma coleção de DocumentDB, pode especificar o número de unidades pedido por segundo (RUs) que pretende reservadas para a coleção.  Quando a coleção de estiver criada, a alocação completa da RUs especificado é reservada para utilizar a coleção.  Cada uma das coleções garante tem dedicada e isolados características de débito.  

É importante ter em atenção que DocumentDB funciona num modelo de reserva; Isto é, são faturada durante o período de débito *reservadas* para a coleção, independentemente da quantidade desse débito está ativamente *utilizado*.  Tenha em atenção, no entanto, que como carregar a aplicação, dados e alteração de padrões de utilização que pode facilmente Dimensionar para a quantidade de reservados RUs através de DocumentDB SDK ou utilizando o [Portal do Azure](https://portal.azure.com).  Para mais informações para débito escala cima e para baixo, consulte o artigo [níveis de desempenho DocumentDB](documentdb-performance-levels.md).

##<a name="request-unit-considerations"></a>Considerações sobre a unidade pedido
Quando estimar o número de unidades pedido para reservar para a sua coleção de DocumentDB, é importante ter as seguintes variáveis em consideração:

- **Tamanho do documento**. À medida que os tamanhos de documento aumentam as unidades consumidas para ler ou escrever que os dados também irão aumentar.
- **Contagem de propriedade do documento**. Pressupondo que a indexação predefinido de todas as propriedades, as unidades consumidas para escrever um documento aumentará como os aumentos de contagem de propriedade.
- **Consistência dos dados**. Ao utilizar níveis de consistência de dados de forte ou delimitada Staleness, serão consumidas unidades adicionais para ler documentos.
- **Propriedades de indexado**. Uma política de índice remissivo cada coleção determina quais são as propriedades estão indexadas por predefinição. Pode reduzir o consumo de unidade pedido ao limitar o número das propriedades indexadas ou ao ativar a indexação lenta.
- **Indexação do documento**. Por predefinição que cada documento é indexado automaticamente, irá consumir menos unidades de pedido se optar por não indexar alguns dos seus documentos.
- **Padrões de consulta**. A complexidade de uma consulta impactos são consumidas quantas unidades pedir para uma operação. O número de predicados, natureza do predicados, projecções, número de UDFs e o tamanho do conjunto de dados de origem todos os influenciar o custo de operações de consulta.
- **A utilização de script**.  Tal como acontece com consultas, procedimentos armazenados e accionadores consumam unidades pedido com base na complexidade das operações a ser executado. À medida que desenvolver a sua aplicação, inspecionar no cabeçalho da encargo pedido para compreender melhor como cada operação está a consumir a capacidade de unidade pedido.

##<a name="estimating-throughput-needs"></a>Precisa de débito estimativas
Uma unidade de pedido é uma medida normalizada de custo de processamento do pedido. Uma unidade de único pedido representa a capacidade de processamento necessária para ler (através da ligação automática ou id) num único documento JSON 1KB que consiste 10 valores de propriedade exclusivo (excluindo as propriedades do sistema). Um pedido para criar (inserir), substituir ou eliminar o mesmo documento irá consumir mais processamento de serviço e portanto mais unidades de pedido.   

> [AZURE.NOTE] O plano base da unidade 1 pedido para um documento de 1KB corresponde ao obter simple por ligação automática ou id do documento.

###<a name="use-the-request-unit-calculator"></a>Utilizar a Calculadora de unidade pedido
Para ajudar os clientes finos sintonizar os respetivos estimativas débito, não existe uma [Calculadora de unidade de pedido](https://www.documentdb.com/capacityplanner) de baseada na web para o ajudar a calcular os requisitos de unidade pedido para operações típicas, incluindo:

- Documento cria (escritas)
- Lê do documento
- Elimina o documento
- Atualizações de documentos

A ferramenta de também inclui suporte para estimar necessidades de armazenamento de dados com base em documentos de exemplo fornecidos.

É fácil utilizar a ferramenta de:

1. Carregar um ou mais documentos JSON representativos.

    ![Carregar documentos para a Calculadora de unidade pedido][2]

2. Para calcular os requisitos de armazenamento de dados, introduza o número total de documentos que esperava para armazenar.

3. Introduza o número de documento criar, ler, atualizar e eliminar operações exigir (numa base por segundo). Para estimar as taxas de unidade de pedido de operações de atualização do documento, carregue uma cópia do documento de exemplo a partir do passo 1 acima, que inclui o campo típicos atualizações.  Por exemplo, se actualizações do documento, normalmente, modificar duas propriedades com o nome lastLogin e userVisits, em seguida, simplesmente copiar o documento de exemplo, atualizar os valores para esses duas propriedades e carregue o documento copiado.

    ![Introduza os requisitos de débito na Calculadora unidade pedido][3]

4. Clique em calcular e examine os resultados.

    ![Pedido de resultados da Calculadora de unidade][4]

>[AZURE.NOTE]Se tiver tipos de documentos que serão diferentes significativamente em termos de tamanho e o número de propriedades indexadas, em seguida, carregue uma amostra de cada *tipo* de documento típico para a ferramenta de e, em seguida, calcular os resultados.

###<a name="use-the-documentdb-request-charge-response-header"></a>Utilizar o cabeçalho de resposta do DocumentDB pedido gratuitas
Cada resposta do serviço DocumentDB inclui um cabeçalho personalizado (x-ms-pedido-encargo) que contém as unidades de pedido consumidas para o pedido. Este cabeçalho também está acessível através dos SDK DocumentDB. No .NET SDK, RequestCharge é uma propriedade do objeto ResourceResponse.  Para consultas, o Explorador de consulta DocumentDB no portal do Azure fornece pedido gratuitas informações sobre consultas executadas.

![Examinar taxas RU no Explorador de consulta][1]

Com o seguinte em mente, um método para estimar é a quantidade de débito reservada exigido por sua aplicação gravar o encargo de unidade pedido associado a em execução de operações típicas relativamente a um documento representativo utilizado pela sua aplicação e, em seguida, estimar o número de operações prevê a efetuar cada segundo.  Certifique-se de que medir e incluir típicas consultas e a utilização de script de DocumentDB também.

>[AZURE.NOTE]Se tiver tipos de documentos que serão diferentes significativamente em termos de tamanho e o número de propriedades indexadas, em seguida, registo o encargo de unidade de pedido de operação aplicável associado a cada *tipo* de documento típico.

Por exemplo:

1. Gravar o encargo de unidade de pedido de criação de (inserir) um documento normal. 
2. Registo o encargo de unidade de pedido de leitura de um documento normal.
3. Registo o encargo de unidade de pedido de atualização de um documento normal.
3. Registo o encargo de unidade de pedido de consultas de documento típica, comuns.
4. Gravar o pedido unidade encargo de qualquer scripts personalizados (procedimentos armazenados, accionadores, as funções definidas pelo utilizador) utilizado pela aplicação
5. Calcule as unidades de pedido necessários tendo em conta o número de operações que prevê a necessidade de executar cada segundo estimado.

##<a name="a-request-unit-estimation-example"></a>Um exemplo de estimativa de unidade pedido
Considere o seguinte ~ 1KB documento:

    {
     "id": "08259",
    "description": "Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX",
    "tags": [
        {
        "name": "cereals ready-to-eat"
        },
        {
        "name": "kellogg"
        },
        {
        "name": "kellogg's crispix"
        }
    ],
    "version": 1,
    "commonName": "Includes USDA Commodity B855",
    "manufacturerName": "Kellogg, Co.",
    "isFromSurvey": false,
    "foodGroup": "Breakfast Cereals",
    "nutrients": [
        {
        "id": "262",
        "description": "Caffeine",
        "nutritionValue": 0,
        "units": "mg"
        },
        {
        "id": "307",
        "description": "Sodium, Na",
        "nutritionValue": 611,
        "units": "mg"
        },
        {
        "id": "309",
        "description": "Zinc, Zn",
        "nutritionValue": 5.2,
        "units": "mg"
        }
    ],
    "servings": [
        {
        "amount": 1,
        "description": "cup (1 NLEA serving)",
        "weightInGrams": 29
        }
    ]
    }

>[AZURE.NOTE]Documentos são minified no DocumentDB, de modo a que o sistema calculado o tamanho do documento acima é ligeiramente menor que 1KB.


A tabela seguinte mostra o pedido aproximado taxas de unidade para operações de típicas neste documento (o encargo de unidade pedido aproximada assume que o nível de consistência conta está definido para "Sessão" e que todos os documentos automaticamente estão indexados):

Operação|Pedido de unidade gratuitas 
---|---
Criar documento|~ 15 RU 
Ler documento|~ 1 RU
Documento de consulta por id|~2.5 RU

Para além disso, esta tabela mostra pedido aproximado taxas de unidade para típicas consultas utilizadas na aplicação:

Consulta|Pedido de unidade gratuitas|# de documentos devolvidos
---|---|--- 
Selecione alimentação por id|~2.5 RU|1 
Selecione alimentos pelo fabricante|~ 7 RU|7
Selecione ao grupo de alimentação e ordem de peso|~ 70 RU|100
Selecione superiores 10 alimentos num grupo de alimentação|~ 10 RU|10

>[AZURE.NOTE]Taxas de RU podem variar com base no número de documentos devolvido.

Com esta informação, podemos pode calcular os requisitos de RU para esta aplicação tendo em conta o número de operações e consultas que poderemos esperar por segundo:

Operação/consulta|Número estimado por segundo|RUs necessários 
---|---|--- 
Criar documento|10|150 
Ler documento|100|100 
Selecione alimentos pelo fabricante|25|175 
Selecione ao grupo de alimentação|10|700 
Selecione superior 10|15|150 total|155|1275

Neste caso, recomendamos que um requisito de débito médio de cálculo de 1 275 RU/s.  Arredondar por excesso para o 100 mais próximo, podemos seria aprovisionar 1 300 RU/s para coleção esta aplicação.

##<a id="RequestRateTooLarge"></a>Que excedam os limites de débito reservadas
Recuperar que consumo de unidade pedido é avaliado como uma taxa por segundo. Para as aplicações que excederem a taxa de unidade pedido aprovisionada para uma coleção de, pedidos a essa coleção irão estar limitados até a taxa desce abaixo do nível reservado. Quando ocorre um controlo de potência, o servidor preemptively terminar o pedido com RequestRateTooLargeException (código de estado HTTP 429) e devolver o cabeçalho x-ms-Repetir-após-ms que indica a quantidade de tempo, em milissegundos, que o utilizador tem de aguardar antes de tentar novamente o pedido.

    HTTP Status 429
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

Se estiver a utilizar as consultas .NET cliente SDK e LINQ, em seguida, a maior parte das vezes que nunca terá de lidar com esta exceção, tal como a versão atual do .NET cliente SDK implicitamente capturas esta resposta, respeita especificado servidor após Repetir cabeçalho e repetições o pedido. A menos que a sua conta está a ser acedida em simultâneo por vários clientes, a tentativa seguinte será bem sucedida.

Se tiver mais do que um cliente cumulativamente operativo acima da taxa de pedido, o comportamento de repetir predefinido não pode ser suficiente e o cliente irá gerar um DocumentClientException com código de estado 429 para a aplicação. Em casos como estas, pode considerar tratamento de comportamento de repetir e lógica erro da sua aplicação processamento rotinas ou aumentar o débito reservado para a coleção.

##<a name="next-steps"></a>Próximos passos

Para saber mais sobre reservado débito com bases de dados do Azure DocumentDB, explore estes recursos:
 
- [DocumentDB preços](https://azure.microsoft.com/pricing/details/documentdb/)
- [Gerir a capacidade de DocumentDB](documentdb-manage.md) 
- [Modelação de dados DocumentDB](documentdb-modeling-data.md)
- [Níveis de desempenho DocumentDB](documentdb-partition-data.md)

Para saber mais sobre DocumentDB, consulte a [documentação](https://azure.microsoft.com/documentation/services/documentdb/)do Azure DocumentDB. 

Para começar com escala e testar com DocumentDB desempenho, consulte o artigo [Desempenho e escala testes com Azure DocumentDB](documentdb-performance-testing.md).


[1]: ./media/documentdb-request-units/queryexplorer.png 
[2]: ./media/documentdb-request-units/RUEstimatorUpload.png
[3]: ./media/documentdb-request-units/RUEstimatorDocuments.png
[4]: ./media/documentdb-request-units/RUEstimatorResults.png
[5]: ./media/documentdb-request-units/RUCalculator2.png
