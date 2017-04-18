<properties 
    pageTitle="Ordenar dados DocumentDB utilizando Order By | Microsoft Azure" 
    description="Saiba como utilizar ORDER BY no DocumentDB consultas LINQ e SQL e como especificar uma política de indexação para ORDER BY consultas." 
    services="documentdb" 
    authors="arramac" 
    manager="jhubbard" 
    editor="cgronlun" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/03/2016" 
    ms.author="arramac"/>

# <a name="sorting-documentdb-data-using-order-by"></a>Ordenar dados DocumentDB utilizando Order By
Microsoft Azure DocumentDB suporta ao consultar documentos utilizando o SQL através de documentos JSON. Resultados da consulta podem ser ordenados utilizando a cláusula ORDER BY no declarações de consulta SQL.

Depois de ler este artigo, poderá atender as seguintes questões: 

- Como consulta com Order By
- Como configurar a uma política de indexação para Order By
- O que vem a seguir?

[Exemplos](#samples) e um [FAQ](#faq) também são fornecidos.

Para uma referência completa no SQL consultar, consulte o [tutorial DocumentDB consulta](documentdb-sql-query.md).

## <a name="how-to-query-with-order-by"></a>Como consulta com Order By
Como no ANSI SQL, pode agora incluir opcional cláusula Order By em instruções SQL quando consultar DocumentDB. A cláusula pode incluir um argumento ASC/DESC opcional para especificar a ordem pela qual devem ser obtidos resultados. 

### <a name="ordering-using-sql"></a>Ordenação utilizando SQL
Por exemplo, eis uma consulta para obter os livros de 10 principais por ordem descendente de seus títulos. 

    SELECT TOP 10 * 
    FROM Books 
    ORDER BY Books.Title DESC

### <a name="ordering-using-sql-with-filtering"></a>Utilizar o SQL com a filtragem de ordenação
Pode encomendar utilizando qualquer propriedade aninhada dentro de documentos, tal como Books.ShippingDetails.Weight e pode especificar filtros adicionais na cláusula WHERE em conjunto com Order By como neste exemplo:

    SELECT * 
    FROM Books 
    WHERE Books.SalePrice > 4000
    ORDER BY Books.ShippingDetails.Weight

### <a name="ordering-using-the-linq-provider-for-net"></a>Ordenação utilizando o fornecedor de LINQ para .NET
Utilizar o .NET SDK versão 1.2.0 e superior, também pode utilizar a cláusula OrderBy() ou OrderByDescending() dentro de consultas LINQ como neste exemplo:

    foreach (Book book in client.CreateDocumentQuery<Book>(UriFactory.CreateDocumentCollectionUri("db", "books"))
        .OrderBy(b => b.PublishTimestamp)
        .Take(100))
    {
        // Iterate through books
    }

DocumentDB suporta a ordenação com uma única numérico, a cadeia ou a propriedade booleana por consulta, com tipos de consulta adicionais brevemente. Consulte o artigo [o que está a chegar seguinte](#Whats_coming_next) para obter mais detalhes.

## <a name="configure-an-indexing-policy-for-order-by"></a>Configurar uma política de indexação para Order By

Recuperar que DocumentDB suporta dois tipos de índices (Hash e intervalo), que podem ser definidos caminhos/propriedades específicas, tipos de dados (cadeias/números) e em valores de precisão diferente (precisão máxima ou um valor de precisão fixa). Dado que DocumentDB utiliza Hash indexação como sendo o predefinido, tem de criar uma nova coleção de com uma política de indexação personalizada com intervalo de números, cadeias ou ambas, para que possa para utilizar Order By. 

>[AZURE.NOTE] Índices de intervalo de cadeia foram introduzidos no 7 de Julho de 2015 com a API REST versão 2015-06-03. Para poder criar políticas para Order By contra cadeias, tem de utilizar SDK versão 1.2.0 do .NET SDK ou versão 1.1.0 do Python, Node.js ou Java SDK.
>
>Antes de REST API versão 2015-06-03, a política predefinida indexação para coleções de sites foi Hash cadeias e números. Isto ter sido alterado para Hash para cadeias e intervalo de números. 

Para obter mais detalhes, consulte [DocumentDB políticas de indexação](documentdb-indexing-policies.md).

### <a name="indexing-for-order-by-against-all-properties"></a>Indexação para Order By contra todas as propriedades
Eis como pode criar uma coleção de com "Todos os intervalo" indexação para Order By contra qualquer/tudo numéricos ou as propriedades da cadeia que aparecem nos documentos JSON dentro da mesma. Aqui vamos substituir o tipo de índice remissivo predefinido para os valores de cadeia intervalo e, na precisão máxima (-1).
                   
    DocumentCollection books = new DocumentCollection();
    books.Id = "books";
    books.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    
    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), books);  

>[AZURE.NOTE] Tenha em atenção que Order By só irá devolver os resultados dos tipos de dados (cadeia e número) que estão indexados com um RangeIndex. Por exemplo, se tiver a predefinição indexação política que tem apenas RangeIndex em números, Order By contra um caminho com valores de cadeia irá devolver sem documentos.

### <a name="indexing-for-order-by-for-a-single-property"></a>Indexação para Order By para uma única propriedade
Eis como pode criar uma coleção de com a indexação para Order By contra apenas a propriedade título, que é uma cadeia. Existem dois caminhos, um para a propriedade título ("/ título /?") com o intervalo de indexação e outro para todas as outras propriedades com o esquema de indexação predefinido, que é Hash para cadeias e intervalo de números.                    
    
    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/Title/?", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = -1 } } 
            });
    
    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), booksCollection);  


## <a name="samples"></a>Amostras
Veja este [projeto de amostras Github](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries) demonstra como utilizar Order By, incluindo criar políticas de indexação e paginação utilizando Order By. Os exemplos são Abrir origem e aconselhamos submeta pedidos de solicitação com contribuições que poderiam beneficiar outros programadores DocumentDB. Consulte as [diretrizes de contribuição](https://github.com/Azure/azure-documentdb-net/blob/master/Contributing.md) para obter orientações sobre como pode contribuir.  

## <a name="faq"></a>PERGUNTAS MAIS FREQUENTES

**O que é o esperado consumo de pedir unidade Britanniche de consultas Order By?**

Uma vez que Order By utiliza o índice DocumentDB para pesquisas, o número de unidades pedido média consumida por Order By consultas será semelhante as consultas equivalente sem Order By. Como qualquer outra operação no DocumentDB, o número de unidades pedido depende de tamanhos de formas de documentos, bem como a complexidade da consulta. 


**O que é o esperado indexação de custos gerais para Order By?**

A indexação sobrecarga de armazenamento irão ser proporcional para o número de propriedades. Da pior hipótese, o overhead índice será 100% dos dados. Não existe nenhuma diferença de sobrecarga débito (unidades pedir) entre intervalo/Order By indexação e a indexação Hash predefinido.

**Como a consulta os meus dados existentes na DocumentDB utilizando Order By?**

Para ordenar os resultados da consulta utilizando Order By, tem de modificar a política de indexação da coleção de para utilizar um tipo de índice remissivo intervalo contra a propriedade utilizado para ordenar. Consulte o artigo [modificar a política de indexação](documentdb-indexing-policies.md#modifying-the-indexing-policy-of-a-collection). 

**Quais são as limitações atuais da Order By?**

Order By pode ser especificado apenas contra uma propriedade, quer numérico ou cadeia quando é intervalo indexados juntamente com a precisão máxima (-1).

Não pode efetuar o seguinte procedimento:
 
- Order By com as propriedades de cadeia interno como id, _rid e auto (brevemente).
- Order By com as propriedades do derivado do resultado de uma associação interna documento (brevemente).
- Encomendar por várias propriedades (brevemente).
- Order By com consultas de bases de dados, coleções de sites, os utilizadores, as permissões ou anexos (brevemente).
- Order By com as propriedades do calculado, por exemplo, o resultado de uma expressão ou uma função UDF/incorporado-in.

Order By não é suportada para publicação em partição consultas quando através do Explorador de consulta no portal do Azure.

## <a name="troubleshooting"></a>Resolução de problemas

Se receber um erro que Order By não é suportado, verifique para se certificar de que está a utilizar uma versão do [SDK](documentdb-sdk-dotnet.md) que suporte Order By. 

## <a name="next-steps"></a>Próximos passos

Bifurcam o [projeto de amostras Github](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries) e comece a ordenação os seus dados! 

## <a name="references"></a>Referências
* [Referência de consulta DocumentDB](documentdb-sql-query.md)
* [Referência da política de indexação DocumentDB](documentdb-indexing-policies.md)
* [Referência DocumentDB SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
* [Ordem de DocumentDB por amostras](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries)
 

