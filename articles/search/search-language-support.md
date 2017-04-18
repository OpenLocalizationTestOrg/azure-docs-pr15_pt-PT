<properties
   pageTitle="Criar um índice remissivo para documentos em vários idiomas na pesquisa Azure | Microsoft Azure | Serviço de pesquisa alojado na nuvem"
   description=" Pesquisa Azure suporta idiomas da 56, tirar partido da analisadores de idioma do tecnologias Lucene e processamento de linguagem Natural da Microsoft."
   services="search"
   documentationCenter=""
   authors="yahnoosh"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="na"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="07/14/2016"
   ms.author="jlembicz"/>

# <a name="create-an-index-for-documents-in-multiple-languages-in-azure-search"></a>Criar um índice remissivo para documentos em vários idiomas na pesquisa do Azure
> [AZURE.SELECTOR]
- [Portal](search-language-support.md)
- [RESTO](https://msdn.microsoft.com/library/azure/dn879793.aspx)
- [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.analyzername.aspx)

Unleashing potência de analisadores idioma é tão fácil como uma propriedade de definição de num campo pesquisável na definição do índice. Agora, pode executar este passo no portal.

Abaixo estão capturas de ecrã das lâminas Azure Portal para pesquisa Azure que permitem aos utilizadores definir um esquema de índice remissivo. A partir deste pá, os utilizadores podem criar todos os campos e defina a propriedade de análise para cada um deles.

> [AZURE.IMPORTANT] Apenas pode definir um analisador de idioma durante a definição do campo, a como na, quando criar um novo índice a partir do terreno para cima ou quando adicionar um novo campo a um índice remissivo existente. Certifique-se de que especificar completamente todos os atributos, incluindo a análise ao criar o campo. Não será possível editar os atributos de ou alterar o tipo de análise assim que guardar as suas alterações.

## <a name="define-a-new-field-definition"></a>Definir uma nova definição de campo

1. Inicie sessão no [Portal do Azure](https://portal.azure.com) e abra o pá de serviço do seu serviço de pesquisa.
2. Clique em **Adicionar índice** na barra de comandos na parte superior do dashboard de serviço para iniciar um novo índice ou abra um índice existente para definir um analisador no novos campos que está a adicionar a um índice existente.
3. É apresentada a pá de campos, que lhe dá opções para definir o esquema de índice remissivo, incluindo o separador Analyzer utilizado para escolher um analisador de idioma.
4. Em campos, inicie uma definição de campo ao fornecer um nome, escolher o tipo de dados e definição de atributos para marcar o campo como texto completo pesquisável e recuperável nos resultados de pesquisa, utilizáveis em estruturas de navegação Faceta, ordenável e assim sucessivamente. 
5. Antes de passar para o campo seguinte, abra o separador **Analyzer** . 

   
![][1]
*Para selecionar uma análise, clique no separador Analyzer a pá de campos*

## <a name="choose-an-analyzer"></a>Selecione uma análise

6. Desloque-se para localizar o campo que estiver a definir. 
7. Se ainda não o tiver marcado o campo como pesquisável, clique na caixa de verificação agora para marcá-la como **pesquisável**.
8. Clique na área análise para apresentar a lista de analisadores disponíveis.
9. Selecione o analisador de que pretende utilizar.

![][2]
*Selecione uma das analisadores suportados para cada campo*

Por predefinição, todos os campos pesquisáveis utilizam o [Analisador de Lucene padrão](http://lucene.apache.org/core/4_10_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) que é desconhecido de idioma. Para ver a lista completa dos analisadores suportados, consulte o artigo [Suporte de idiomas no Azure pesquisa](https://msdn.microsoft.com/library/azure/dn879793.aspx).

Assim que a análise de idioma está selecionada para um campo, será utilizada com cada pedido de pesquisa e indexação para esse campo. Quando uma consulta for emitida contra vários campos utilizando analisadores diferentes, a consulta será processada independentemente pelas analisadores direita para cada campo.

Muitos web e aplicações móveis servem utilizadores em todo o mundo utilizar idiomas diferentes. É possível definir um índice para um cenário assim através da criação de um campo para cada idioma suportado.

![][3]
*Definição de índice com um campo de descrição para cada idioma suportado*

Se o idioma do agente de emissão de uma consulta for conhecido, um pedido de pesquisa pode ser confinado a um campo específico, utilizando o parâmetro de consulta **searchFields** . A seguinte consulta que será emitida apenas contra a descrição na Polaco:

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=description_pl&api-version=2015-02-28`

Pode consultar o índice remissivo a partir do portal através do **Explorador de pesquisa** para colar numa consulta semelhante ao mostrada acima. Explorador de procura está disponível a partir da barra de comando no pá serviço. Consulte o artigo [Query índice de pesquisa do Azure no portal](search-explorer.md) para obter detalhes.

Por vezes, não é conhecido o idioma do agente de emissão de uma consulta, caso em que a consulta pode emitida relativamente a todos os campos em simultâneo. Se for necessário, pode ser definida preferência para obter os resultados num determinado idioma utilizar [pontuação perfis](https://msdn.microsoft.com/library/azure/dn798928.aspx). No exemplo abaixo, correspondências localizadas na descrição em inglês vai ser classificadas superior relativamente às correspondências em francês e Polaco:

    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2015-02-28`

Se for um programador .NET, tenha em atenção que pode configurar analisadores idioma utilizando o [Azure pesquisa .NET SDK](http://www.nuget.org/packages/Microsoft.Azure.Search). A versão mais recente inclui suporte para também o analisadores de idioma do Microsoft.

<!-- Image References -->
[1]: ./media/search-language-support/AnalyzerTab.png
[2]: ./media/search-language-support/SelectAnalyzer.png
[3]: ./media/search-language-support/IndexDefinition.png
