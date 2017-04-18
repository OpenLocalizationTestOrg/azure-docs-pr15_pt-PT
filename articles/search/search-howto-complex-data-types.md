<properties
    pageTitle="Como do modelo de tipos de dados complexos na pesquisa Azure | Pesquisa do Microsoft Azure"
    description="As funções aninhadas ou estruturas de dados hierárquicos podem ser modeladas num índice de pesquisa do Azure utilizando o conjunto de linhas plana e tipo de dados de coleções de sites."
    services="search"
    documentationCenter=""
    authors="LiamCa"
    manager="pablocas"
    editor=""
    tags="complex data types; compound data types; aggregate data types"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="09/07/2016"
    ms.author="liamca"
/>

# <a name="how-to-model-complex-data-types-in-azure-search"></a>Como tipos de dados complexos de modelo na pesquisa do Azure

Conjuntos de dados externos, utilizados para preencher um índice de pesquisa do Azure, por vezes, incluem hierárquicas ou aninhadas bem como as coberturas que não divide uma forma ordenada num conjunto de linhas de tabela. Exemplos de como estruturas podem incluir várias localizações e números de telefone para um único cliente, várias cores e tamanhos para um SKU único, vários autores de um livro única e assim sucessivamente. Modelação termos, poderá ver estas estruturas designadas de *tipos de dados complexos*, *compostos tipos de dados*, *tipos de dados compostos*ou *Agregar tipos de dados*, para atribuir um nome algumas.

Tipos de dados complexos não são suportados vierem na pesquisa Azure, mas uma solução provas dadas inclui um processo de dois passos de atenuamento a estrutura e, em seguida, utilizar um tipo de dados de **coleções de sites** para reconstituir a estrutura interior. Seguir técnica descrita neste artigo permite que o conteúdo a ser procurado por facetas, filtrados e ordenados.

## <a name="example-of-a-complex-data-structure"></a>Exemplo de uma estrutura de dados complexos

Normalmente, os dados em questão residem como um conjunto de documentos JSON ou XML ou itens num arquivo NoSQL como DocumentDB. Estrutural, o desafio deriva ter vários itens subordinados que precisam de ser pesquisados e filtrados.  Como ponto de partida para ilustrar a solução, siga o seguinte documento JSON que um conjunto de contactos como um exemplo de lista:

~~~~~
[
  {
    "id": "1",
    "name": "John Smith",
    "company": "Adventureworks",
    "locations": [
      {
        "id": "1",
        "description": "Adventureworks Headquarters"
      },
      {
        "id": "2",
        "description": "Home Office"
      }
    ]
  }, 
  {
    "id": "2",
    "name": "Jen Campbell",
    "company": "Northwind",
    "locations": [
      {
        "id": "3",
        "description": "Northwind Headquarter"
      },
      {
        "id": "4",
        "description": "Home Office"
      }
    ]
}]
~~~~~

Enquanto os campos Nome 'id', 'nome' e 'empresa' podem facilmente ser mapeadas uma como campos dentro de um índice de pesquisa do Azure, o campo 'localizações' contém uma matriz de localizações, ter ambas as um conjunto de IDs de localização, bem como as descrições de localização. Dado que Azure pesquisa não tiver um tipo de dados que suporta este procedimento, precisamos de forma diferente para modelar isto na pesquisa Azure. 

> [AZURE.NOTE] Esta técnica também é descrita pela Kirk Cardoso numa mensagem do blogue [Indexação DocumentDB com a pesquisa do Azure](https://blogs.msdn.microsoft.com/kaevans/2015/03/09/indexing-documentdb-with-azure-seach/), que mostra uma técnica denominado "atenuamento os dados," através do qual tem um campo denominado `locationsID` e `locationsDescription` que forem ambas [coleções de sites](https://msdn.microsoft.com/library/azure/dn798938.aspx) (ou uma matriz de cadeias).   

## <a name="part-1-flatten-the-array-into-individual-fields"></a>Parte 1: Aplanar a matriz para campos individuais

Para criar um índice de pesquisa do Azure adapta este conjunto de dados, crie campos individuais para o subestrutura aninhada: `locationsID` e `locationsDescription` com um tipo de dados de [coleções de sites](https://msdn.microsoft.com/library/azure/dn798938.aspx) (ou uma matriz de cadeias). Nestes campos faria indexe os valores '1' e '2' para o `locationsID` de campo para Silva e os valores '3' e '4' para o `locationsID` campo Jen Campbell.  

Os dados de pesquisa do Azure teria o seguinte aspeto: 

![dados de exemplo, 2 linhas](./media/search-howto-complex-data-types/sample-data.png)


## <a name="part-2-add-a-collection-field-in-the-index-definition"></a>Parte 2: Adicionar um campo de coleções de sites na definição do índice remissivo

No esquema de índice remissivo, as definições de campo poderão ser semelhantes a este exemplo.

~~~~
var index = new Index()
{
    Name = indexName,
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("name", DataType.String) { IsSearchable = true, IsFilterable = false, IsSortable = false, IsFacetable = false },
        new Field("company", DataType.String) { IsSearchable = true, IsFilterable = false, IsSortable = false, IsFacetable = false },
        new Field("locationsId", DataType.Collection(DataType.String)) { IsSearchable = true, IsFilterable = true, IsFacetable = true },
        new Field("locationsDescription", DataType.Collection(DataType.String)) { IsSearchable = true, IsFilterable = true, IsFacetable = true }
    }
};
~~~~

## <a name="validate-search-behaviors-and-optionally-extend-the-index"></a>Valide comportamentos de pesquisa e, opcionalmente, expandir o índice remissivo

Assumindo que criou o índice e carregou os dados, pode testar agora a solução para verificar a execução de consulta de pesquisa contra o conjunto de dados. Cada campo **coleção** deve ser **pesquisável**, **filtráveis** e **facetable**. Deverá conseguir a execução de consultas como:

* Localize todas as pessoas que trabalhem na 'Sede Adventureworks'.
* Obter uma contagem do número de pessoas que trabalhem num 'escritório de base'.  
* Das pessoas que trabalhem na 'Office de base', mostre que outros escritórios funcionam juntamente com uma contagem das pessoas em cada localização.  

Onde esta técnica determinado distinguir é quando precisar de fazer uma pesquisa que combina tanto o id de localização, bem como a descrição da localização. Por exemplo:

* Localizar todas as pessoas onde tenham um escritório e tiver um ID de localização de 4.  

Se recuperar o conteúdo original velhos isto:

~~~~
   {
        id: '4',
        description: 'Home Office'
   }
~~~~

No entanto, agora que recomendamos ter separadas os dados em campos separados, não temos nenhuma forma de se sabendo que o Office de base para Jen Campbell está relacionada com a `locationsID 3` ou `locationsID 4`.  

Gerir esta situação, pode defina outro campo no índice que combine todos os dados para uma coleção de única.  Para o nosso exemplo, chamamos este campo `locationsCombined` e podemos irá separar o conteúdo com um `||` embora pode escolher qualquer separador de que pensa que seria um conjunto único de carateres para o seu conteúdo. Por exemplo: 

![dados de exemplo, 2 linhas com separador](./media/search-howto-complex-data-types/sample-data-2.png)

Utilização desta `locationsCombined` campo, podemos pode agora acomodar ainda mais consultas, tais como:

* Mostre uma contagem das pessoas que trabalhem num 'base escritório' com a localização Id de '4'.  
* Procurar pessoas que trabalhem na 'Office de base' com a localização Id '4'. 

## <a name="limitations"></a>Limitações

Esta técnica é útil para um número de cenários, mas não é aplicável em todos os casos.  Por exemplo:

1. Se não tiver um conjunto de campos estático no seu tipo de dados complexos e não tiver nenhuma forma de mapear todos os tipos de possíveis para um único campo. 
2. Atualizar os objetos aninhados requer alguns trabalho extra para determinar exatamente o que precisa de ser atualizados no índice de pesquisa do Azure

## <a name="sample-code"></a>Código de exemplo

Pode ver um exemplo sobre como indexar um conjunto de dados JSON complexo Azure procurar e executar um número de consultas sobre este conjunto de dados neste [GitHub repo](https://github.com/liamca/AzureSearchComplexTypes).

## <a name="next-step"></a>Passo seguinte

[Voto para suporte nativo para tipos de dados complexos](https://feedback.azure.com/forums/263029-azure-search) no site de pesquisa Azure página e fornecer qualquer entrada adicional que gostaria-na ter em mente sobre a execução de funcionalidade. Também pode aceder para mim diretamente no Twitter na @liamca.


 