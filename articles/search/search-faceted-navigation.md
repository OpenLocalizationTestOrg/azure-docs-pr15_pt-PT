<properties 
    pageTitle="Como implementar o navegação por facetas na pesquisa Azure | Microsoft Azure | procurar categorias de navegação" 
    description="Adicione navegação por facetas às aplicações que se integram com Azure procura, um serviço de pesquisa na nuvem alojado no Microsoft Azure." 
    services="search" 
    documentationCenter="" 
    authors="HeidiSteen" 
    manager="mblythe" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="10/17/2016" 
    ms.author="heidist"/>

#<a name="how-to-implement-faceted-navigation-in-azure-search"></a>Como implementar o navegação por facetas na pesquisa do Azure

Navegação por facetas é um mecanismo filtragem que fornece a navegação de pesquisa personalizada direccionado em aplicações de pesquisa. Enquanto o termo «navegação por facetas» poderá estar familiarizado, quase é um dado que tenha utilizado uma antes. Conforme o exemplo seguinte mostra, navegação por facetas é nada mais do que as categorias utilizadas para filtrar os resultados.

## <a name="what-it-looks-like"></a>O aspeto

 ![][1]
  
Facetas podem ajudar a encontrar o procura, garantindo que não receberá zero resultados. Como um programador facetas permitem-lhe expor os critérios de pesquisa mais úteis para navegar na sua corpo de pesquisa. Em aplicações de retalho online, navegação por facetas com frequência é construída sobre marcas, os departamentos (sapatos das crianças), tamanho, preço, popularidade e classificações. 

Implementar a navegação por facetas difere através de tecnologias de pesquisa e pode ser muito complexo. Em pesquisa Azure, navegação por facetas é construída no momento da consulta, utilizando campos atribuídos que tenham sido especificados anteriormente no seu esquema. Em consultas que constrói a aplicação, uma consulta terá de enviar *parâmetros de consulta faceta* para receberem os valores de filtro faceta disponível para esse conjunto de resultados do documento. Cortar realmente o resultado de documento definir, a aplicação têm de aplicar uma `$filter` expressão.

Em termos de desenvolvimento de aplicações, escrever o código que constrói consultas constitui em volume do trabalho. Muitos dos comportamentos de aplicação que pretende a partir de navegação por facetas é fornecido pelo serviço, incluindo suporte incorporado para configurar intervalos e introdução contagens para obter os resultados faceta. O serviço também inclui predefinições cuidadas que ajudá-lo a evitar estruturas de difícil de navegação. 

Este artigo contém as secções seguintes:

- [Como construi-lo](#howtobuildit)
- [Construir a camada de apresentação](#presentationlayer)
- [Construir o índice remissivo](#buildindex)
- [Verificar existência de qualidade dos dados](#checkdata)
- [Criar a consulta](#buildquery)
- [Sugestões sobre como controlar a navegação por facetas](#tips)
- [Navegação por facetas com base em valores de intervalo](#rangefacets)
- [Navegação por facetas com base em GeoPoints](#geofacets)
- [Experimentar](#tryitout)

##<a name="why-use-it"></a>Por que motivo utilizá-la
As aplicações de pesquisa mais eficazes tem vários modelos de interação para além de uma caixa de pesquisa. Navegação por facetas é um ponto de entrada alternativo para procurar, que oferece uma alternativa conveniente para escrever expressões de pesquisa complexa desenhada à mão.

##<a name="know-the-fundamentals"></a>Saber os conceitos básicos

Se estiver familiarizado com o desenvolvimento de pesquisa, a melhor forma de pensar navegação por facetas é que mostra as possibilidades de pesquisa personalizada direccionada. É um tipo de experiência de pesquisa de desagregação, com base em filtros predefinidos, utilizados para rapidamente limitar os resultados da pesquisa através do ponto-e-clique em ações. 

**Modelo de interação**

A experiência de pesquisa para navegação por facetas é iterativa, vamos começar por compreendê-la como uma sequência de consultas que Desdobrar em resposta ao ações do utilizador.

O ponto de partida é uma página de aplicação que fornece navegação por facetas, normalmente colocada em periferia. Navegação por facetas é frequentemente uma estrutura de árvore, com caixas de verificação para cada valor ou texto clicável. 

1.  Uma consulta enviada para pesquisa Azure Especifica a estrutura de navegação por facetas através de um ou mais parâmetros de consulta faceta. Por exemplo, a consulta pode incluir `facet=Rating`, talvez com um `:values` ou `:sort` opção para aperfeiçoar a apresentação.
2.  A camada de apresentação compõe uma página de pesquisa fornece navegação por facetas, utilizando as facetas especificadas no pedido.
3.  Dada uma estrutura de navegação por facetas que inclui a classificação, o utilizador clica em "4" para indicar que devem ser apresentados apenas os produtos com uma classificação de 4 ou superior. 
4.  Em resposta, a aplicação de envia uma consulta que inclui`$filter=Rating ge 4` 
5.  A camada de apresentação atualiza a página, que mostra um conjunto de resultados reduzida, que contém apenas os itens que correspondam aos critérios novos (neste caso, produtos classificados 4 e até).

Uma faceta é um parâmetro de consulta, mas não confunda-lo com a entrada da consulta. Nunca é utilizada como critérios de seleção numa consulta. Em vez disso, pense parâmetros de consulta faceta como entradas à estrutura de navegação que vem novamente na resposta. Para cada parâmetro de consulta faceta fornecidos, Azure pesquisa irá avaliar como muitos documentos são nos resultados da parciais para cada valor faceta.

Aviso de `$filter` no passo 4. Este é um aspecto importante de navegação por facetas. Apesar de facetas e filtros são independentes no API, terá de ambas para entregar a experiência que pretenda. 

**Padrões de design**

No código de aplicação, o padrão é utilizar parâmetros de consulta faceta para devolver a estrutura de navegação por facetas juntamente com os resultados de Faceta, assim como uma expressão de $filter que trata o evento clique em. Pense a `$filter` devolvido expressão como o código por trás a limitação por motivos real dos resultados da pesquisa para a camada de apresentação. Dada uma faceta de cores, clicando na cor vermelho é implementada através de um `$filter` expressão que seleciona apenas os itens que têm uma cor de vermelho. 

**Noções básicas de consulta na pesquisa Azure**

Na pesquisa Azure, é especificado um pedido de através de um ou mais parâmetros de consulta (consulte o artigo [Procurar documentos](http://msdn.microsoft.com/library/azure/dn798927.aspx) para obter uma descrição de cada um deles). Nenhum dos parâmetros de consulta são necessários, mas tem de ter, pelo menos, para que uma consulta seja válido.

Precisão, geralmente entendido como a capacidade para filtrarem os acertos irrelevantes, é obtida através de um ou ambos estas expressões:

- **pesquisa =**<br/>
O valor deste parâmetro constitui a expressão de pesquisa. Poderá ser uma única parte de um de texto ou uma expressão de pesquisa complexa que inclui vários termos e operadores. No servidor, uma expressão de pesquisa é utilizada para pesquisa de texto completo, consultar campos pesquisáveis no índice remissivo para a correspondência de termos, devolve resultados na ordem de classificação. Se definir `search` para null consulta execução é ao longo de todo o índice (ou seja, `search=*`). Neste caso, outros elementos da consulta, tal como um `$filter` ou perfil de pontuação, que serão os fatores principais que afetam os quais os documentos são devolvidos `($filter`) e por que ordem (`scoringProfile` ou `$orderb`y).

- **$filter =**<br/>
Um filtro é um mecanismo poderoso para limitar o tamanho dos resultados de pesquisa com base em valores de atributos de documento específico. A `$filter` é avaliado em primeiro lugar, seguido de lógica faceting que gera os valores disponíveis e as contagens correspondentes para cada valor

Expressões de pesquisa complexa serão diminuir o desempenho da consulta. Sempre que possível, utilizam expressões de filtro bem construídos para aumentar a precisão e melhorar o desempenho da consulta.

Para compreender melhor como um filtro adiciona mais precisão, compare uma expressão de pesquisa complexa para outro que inclua uma expressão de filtro:

- `GET /indexes/hotel/docs?search=lodging budget +Seattle –motel +parking`

- `GET /indexes/hotel/docs?search=lodging&$filter=City eq ‘Seattle’ and Parking and Type ne ‘motel’`

Apesar de ambas as consultas são válidas, o segundo é superior se que procura não motéis estacionamento em Seattle. A primeira consulta baseia-se nessas palavras específicas a ser mencionados ou não mencionados nos campos de cadeia como nome, descrição e qualquer outro campo que contém dados pesquisáveis. A segunda consulta Procura correspondências precisas no dados estruturados e é provável que seja muito mais preciso.

Em aplicações que incluem navegação por facetas, irá querer Certifique-se de que cada ação do utilizador através de uma estrutura de navegação por facetas é acompanhar por um limitar de resultados de pesquisa, obtidos através de uma expressão de filtro.

<a name="howtobuildit"></a>
##<a name="how-to-build-it"></a>Como construi-lo

Navegação por facetas na pesquisa Azure é implementada no seu código da aplicação que cria o pedido, mas depende de elementos predefinidos no seu esquema.

Predefinidas na sua pesquisa índice é o `Facetable [true|false]` atributo de índice remissivo, definido no campos selecionados para ativar ou desativar a sua utilização numa estrutura de navegação por facetas. Sem `"Facetable" = true`, um campo não pode ser utilizado no painel de navegação faceta.

No momento da consulta, o código da aplicação cria um pedido que inclua `facet=[string]`, um parâmetro de pedido que fornece o campo a faceta por. Uma consulta pode ter vários facetas, tais como `&facet=color&facet=category&facet=rating`, cada uma delas separados por um caráter e comercial (&).

Código de aplicação também tem de construir um `$filter` expressão para processar eventos clique no painel de navegação por facetas. A `$filter` reduz resultados da pesquisa, utilizando o valor de faceta como critérios de filtro.

Azure pesquisa devolve resultados da pesquisa, por termos introduzidos pelo utilizador, juntamente com as atualizações à estrutura de navegação por facetas. Pesquisa do Azure, navegação por facetas é uma construção de um só nível, com valores de faceta e conta quantos resultados forem encontrados para cada um deles.

A camada de apresentação no seu código fornece a experiência do utilizador. -Deve listar partes constituintes do painel de navegação por facetas, tal como a etiqueta, valores, caixas de verificação e a contagem. A pesquisa Azure REST API é desconhecido plataforma, pelo que o idioma e a plataforma que pretende. O importante é incluir elementos de IU que suportam a atualização elementar, com atualizada estado de IU que cada faceta adicional está seleccionada. 

Nas secções seguintes, podemos irá demorar mais detalhada como criar cada peça, começando a camada de apresentação.

<a name="presentationlayer"></a>
##<a name="build-the-presentation-layer"></a>Construir a camada de apresentação

Trabalhar a partir de voltar a camada de apresentação pode ajudá-lo a descobrir requisitos que podem ser perdidos caso contrário e compreender as capacidades são essenciais para a experiência de pesquisa.

Em termos de navegação por facetas, a página web ou aplicação apresenta a estrutura de navegação por facetas, Deteta entrada do utilizador na página e insere os elementos alterados. 

Para as aplicações web, AJAX é geralmente utilizado na camada de apresentação porque permite-lhe atualizar alterações utilizarão. Também pode utilizar ASP.NET MVC ou qualquer plataforma de visualização que pode ligar a um serviço de pesquisa do Azure através de HTTP. A aplicação de exemplo referenciada ao longo deste artigo – **AdventureWorks catálogo** – acontece ao ser uma aplicação do ASP.NET MVC.

O exemplo seguinte, tirado do ficheiro **index.cshtml** da aplicação de exemplo, cria uma estrutura HTML dinâmica para apresentar a navegação por facetas na sua página de resultados de pesquisa. No exemplo, navegação por facetas incorporada na página de resultados da pesquisa e é apresentada depois do utilizador tenha apresentado um termo de pesquisa.

Aviso de que cada faceta tem uma etiqueta (preços de cores, categorias,), uma ligação a um campo por facetas (cor, NomeDaCategoria, listPrice) bem como um `.count` parâmetro, utilizado para devolver o número de itens que se encontram para esse resultado faceta.

  ![][2]
 

> [AZURE.TIP] Ao estruturar a página de resultados da pesquisa, lembre-se adicionar um mecanismo para limpar facetas. Se utilizar caixas de verificação, os utilizadores podem facilmente intuit como limpar os filtros. Para outros esquemas, poderá ter um padrão de trilho ou outra abordagem creative. Por exemplo, na aplicação de exemplo AdventureWorks catálogo, pode clicar no título, AdventureWorks catálogo, para repor a página de pesquisa.

<a name="buildindex"></a>
##<a name="build-the-index"></a>Construir o índice remissivo

Faceting está ativada por campo faseadamente no índice, através deste atributo de índice remissivo no: `"Facetable": true`.  
Todos os tipos de campo que possivelmente podem ser utilizados no painel de navegação por facetas são `Facetable` por predefinição. Os tipos de campo incluem `Edm.String`, `Edm.DateTimeOffset`e todos os tipos de campo numérico (essencialmente, todos os tipos de campo são facetable exceto `Edm.GeographyPoint`, que não podem ser utilizado no painel de navegação por facetas). 

Ao criar um índice remissivo, prática recomendada para navegação por facetas é explicitamente desativar faceting para os campos que nunca devem ser utilizados como uma faceta.  Em particular, os campos de cadeia para valores singleton, como o nome ID ou produto, devem ser definidos para `"Facetable": false` para impedir que o uso acidental (e ineficaz) num painel de navegação por facetas.

Segue-se o esquema para a aplicação de exemplo AdventureWorks catálogo (recortada de alguns atributos para reduzir o tamanho geral):

 ![][3]
 
Tenha em atenção que `Facetable` está desativado para campos de cadeia que não deveriam ser utilizados como facetas, tal como um ID ou um nome. Ativar o registo de faceting desativar onde não precise do mesmo ajuda a manter o tamanho do índice remissivo pequeno e geralmente melhora o desempenho.

> [AZURE.TIP] Como prática recomendada, inclua o conjunto completo de atributos de índice para cada campo. Apesar de `Facetable` está ativada por predefinição para quase todos os campos, intencionalmente definir cada atributo pode ajudá-lo pensar as implicações de cada decisão de esquema. 

<a name="checkdata"></a>
##<a name="check-for-data-quality"></a>Verificar existência de qualidade dos dados 

Quando desenvolver qualquer aplicação centrados em dados, preparar os dados é frequentemente uma das partes maiores da tarefa. Aplicações de pesquisa não são exceção. A qualidade dos seus dados tem direta influenciar se a estrutura de navegação por facetas ocorrência como esperado-lo para, assim como a sua eficácia tornam-se construir filtros reduzir o resultado definir.

Na pesquisa Azure, o corpo de pesquisa é formado com documentos que preenchem um índice remissivo. Os documentos fornecem os valores que são utilizados para calcular facetas. Se pretender faceta por marca ou preço, cada documento deve conter valores para *BrandName* e *ProductPrice* são válidos, consistente e produtivo como uma opção de filtro.

Alguns lembretes que deve apagar para estão indicados abaixo:

- Para cada campo que pretende faceta por, pergunte a próprio se contém valores que são adequados como filtros na pesquisa direccionada personalizada. Os valores devem ser curto, descritivo e suficientemente distintivo para oferecer uma escolha entre as opções concorrentes limpar.
- Erros ortográficos ou valores quase correspondentes. Se faceta em cor e valores de campo incluir cor de laranja e Ornage (um erro ortográfico), uma faceta baseada no campo cor seria Pegue ambos.
- Texto de maiúsculas/minúsculas misto também pode instalar o caos no painel de navegação por facetas, com a cor de laranja e a cor de laranja aparecem como dois valores de diferentes. 
- As versões do mesmo valor únicos e plural podem resultar numa faceta separada para cada um.

Como pode imaginar, empenho na preparar os dados é um aspecto essencial de navegação por facetas eficaz.

<a name="buildquery"></a>
##<a name="build-the-query"></a>Criar a consulta

O código que escrever para construir consultas deverá especificar todas as partes de uma consulta válida, incluindo expressões de procura, facetas, filtros, pontuação perfis – nada utilizado para elaborar um pedido. Nesta secção, vamos explorar onde facetas se ajustam a uma consulta e como os filtros são utilizados com facetas para a prestação num conjunto de resultados reduzido.

Um exemplo com frequência é um bom local para começar. O exemplo seguinte, tirado de ficheiro **CatalogSearch.cs** , cria um pedido de que cria navegação faceta com base na cor, categoria e preço. 

Repare que facetas são integral nesta aplicação de exemplo. A experiência de pesquisa no catálogo de AdventureWorks destina-se à volta de navegação por facetas e filtros. Este é evidente o proeminente posicionamento da navegação por facetas na página. A aplicação de exemplo inclui os parâmetros URI de facetas (cor, categoria, preços) como propriedades o método de pesquisa (tal como construídos com base na aplicação de exemplo).

  ![][4]
 
Um parâmetro de consulta faceta está definido para um campo e consoante o tipo de dados, pode ser ainda mais parametrizada por lista delimitado por vírgulas que inclui `count:<integer>`, `sort:<>`, `intervals:<integer>`, e `values:<list>`. Uma lista de valores é suportada para dados numéricos quando configurar intervalos. Consulte o artigo [Procurar documentos (Azure pesquisa API)](http://msdn.microsoft.com/library/azure/dn798927.aspx) para obter detalhes de utilização.

Juntamente com facetas, o pedido elaborado pela aplicação também deve criar filtros para restringir o conjunto de documentos candidatos com base numa seleção de valor faceta. Para um arquivo de bicicleta, navegação por facetas fornece pistas a perguntas como "que cores, fabricantes e tipos de bicicletas estão disponíveis", enquanto filtrar as respostas a perguntas como "quais bicicletas exatas são vermelhas, bicicletas de montanha, isto preço de intervalo".

Quando um utilizador clica "Vermelho" para indicar que devem ser apresentados apenas vermelhos produtos, a consulta seguinte envia a aplicação deverá incluir `$filter=Color eq ‘Red’`.

## <a name="best-practices-for-faceted-navigation"></a>Práticas recomendadas para navegação por facetas

A lista que se segue resume alguns melhores práticas.

- **Precisão**<br/>
Utilize os filtros. Se depender apenas expressões de procura por si só, radicais podem fazer com que um documento para ser devolvido que não tem o valor de faceta precisos em qualquer um dos seus campos. 

- **Campos de destino**<br/>
Por facetas desagregar, normalmente pretende incluir apenas os documentos que tenham o valor de faceta num campo específico (por facetas), não em qualquer lugar através de todos os campos pesquisáveis. Adicionar um filtro reforça o campo de destino ao direcionar o serviço de procura apenas no campo por facetas para um valor correspondente.

- **Eficiência de índice remissivo**<br/>
Se a sua aplicação utiliza exclusivamente navegação por facetas (ou seja, sem caixa de pesquisa), pode marcar o campo como `searchable=false`, `facetable=true` para produzir um índice mais compacto. Além disso, a indexação ocorre apenas nos valores faceta todo, com sem quebra word ou indexação dos componentes de um valor com várias palavra.

- **Desempenho**<br/>
Filtros de limitar o conjunto de documentos candidatos para a pesquisa e exclui-los a partir de classificação. Se tiver um conjunto de documentos grande, utilizando uma desagregação faceta muito seletivo para baixo com frequência dá-lhe significativamente melhor desempenho.


<a name="tips"></a> 
##<a name="tips-on-how-to-control-faceted-navigation"></a>Sugestões sobre como controlar a navegação por facetas

Segue-se uma folha de sugestão com orientações sobre os problemas específicos.

**Adicionar rótulos para cada campo no painel de navegação faceta**

Etiquetas normalmente são definidas em HTML ou formulário (**index.cshtml** na aplicação do exemplo). Não existe nenhuma API em Azure procurar etiquetas de navegação faceta ou qualquer outro tipo de metadados.

**Definir os campos que podem ser utilizados como faceta**

Recuperar a que o esquema do índice remissivo determina quais os campos que estão disponíveis para utilizar como uma faceta. Partindo do princípio de que é de um campo facetable, a consulta Especifica os campos a faceta por. O campo pelo qual estiver faceting fornece os valores que são apresentadas abaixo da etiqueta. 

Os valores que são apresentados em cada etiqueta são obtidos a partir do índice. Por exemplo, se o campo faceta *cores*, os valores disponíveis para filtrar adicionais serão os valores para esse campo (vermelho, preto e assim sucessivamente).

Para valores numéricos e de data/hora apenas, explicitamente pode definir valores no campo faceta (por exemplo, `facet=Rating,values:1|2|3|4|5`). Uma lista de valores é permitida para estes tipos de campo simplificar a separação de resultados de faceta em intervalos contíguas (ambos os intervalos com base em valores numéricos ou períodos de tempo). 

**Cortar resultados faceta**

Resultados de faceta os documentos que se encontram nos resultados da pesquisa que correspondem a um termo faceta. No exemplo seguinte, nos resultados de pesquisa para *computação de nuvem*, de 254 itens também tem *especificação interna* como um tipo de conteúdo. Itens não são necessariamente mutuamente exclusivos. Se um item cumpre os critérios de ambos os filtros, é contado em cada um deles. Isto pode acontecer quando faceting no `Collection(Edm.String)` campos, que são utilizados com frequência para implementar o documento de marcação.

        Search term: "cloud computing"
        Content type
           Internal specification (254)
           Video (10) 

Em geral, se achar que resultados faceta forma persistente são demasiado grandes, recomendamos que pode adicionar mais filtros, tal como é explicado nas secções anteriores, para dar ao seu os utilizadores da aplicação mais opções para limitar a pesquisa.

**Limite de itens no painel de navegação faceta**

Para cada campo na árvore de navegação por facetas, existe um limite predefinido de 10 valores. Esta predefinição sentido para estruturas de navegação porque mantém a lista de valores para um tamanho fácil. Pode substituir o predefinido, atribuindo um valor para contar.

- `&facet=city,count:5`Especifica que são devolvidas apenas as primeiras 5 cidades que se encontram na parte superior resultados classificada como um resultado de faceta. Atribuído um termo de pesquisa de "aeroporto" e 32 correspondências, se a consulta especifica `&facet=city,count:5`, apenas as exclusivas primeiros cinco cidades com mais documentos nos resultados da pesquisa são incluídas nos resultados da faceta.

Aviso a distinção entre resultados faceta e os resultados da pesquisa. Resultados da pesquisa são todos os documentos que correspondem à consulta. Resultados de faceta são as correspondências para cada valor faceta. No exemplo, os resultados da pesquisa irão incluir nomes de cidades que não estão na lista de classificação faceta (5 no nosso exemplo). Os resultados que são filtrados através da navegação por facetas ficam visíveis ao utilizador quando limpa facetas ou seleciona outras facetas para além de localidade. 

> [AZURE.NOTE] Debater `count` quando existe mais do que um tipo pode ser confuso. A tabela seguinte oferece um breve resumo do como o termo é utilizado no Azure pesquisa API, código de exemplo e documentação. 

- `@colorFacet.count`<br/>
No código de apresentação, deverá ver um parâmetro de contagem no Faceta, utilizada para apresentar o número de resultados de faceta. Nos resultados de Faceta, contagem indica o número de documentos que correspondem no faceta termo ou intervalo.

- `&facet=City,count:12`<br/>
Numa consulta Faceta, pode definir contar a um valor.  A predefinição é 10, mas pode definir superiores ou inferiores. Definir `count:12` obtém o início 12 corresponde nos resultados de faceta por contagem de documentos.

- "`@odata.count`"<br/>
Na resposta à consulta, este valor indica o número de itens correspondentes nos resultados da pesquisa. Em média, for maior do que a soma de todos os resultados de faceta combinado, devido a presença de itens que correspondem ao termo de pesquisa, mas tem sem correspondências de valor faceta.


**Níveis no painel de navegação por facetas** 

Tal como indicado, não existe suporte para o aninhamento de facetas numa hierarquia direta. Terminar a caixa navegação por facetas suporta apenas um nível de filtros. No entanto, existe soluções. Pode codificar uma estrutura hierárquica faceta num `Collection(Edm.String)` com uma entrada aponte por hierarquia. Implementar a esta solução é para além do âmbito deste artigo, mas pode ler sobre colecções de [OData, por exemplo](http://msdn.microsoft.com/library/ff478141.aspx). 

**Validar campos**

Se criar a lista de facetas dinamicamente com base na entrada do utilizador ou não fidedignos, quer deve validar que os nomes dos campos por facetas são válidos, ou os nomes de escape durante a criação de URLs através de `Uri.EscapeDataString()` no .NET ou o equivalente em sua plataforma à escolha.

**Contagens de palavras no resultados faceta**

Quando adicionar um filtro a uma consulta por facetas, poderá pretender manter a declaração de faceta (por exemplo, `facet=Rating&$filter=Rating ge 4`). Tecnicamente, faceta = classificação não é necessária, mas mantê-lo devolve as contagens de valores de faceta classificações de 4 e superior. Por exemplo, se um utilizador clica "4" e a consulta inclui um filtro para maior ou igual a "4", as contagens são devolvidas para cada classificação que é 4 e e para cima.  

**Implicações sharding no contagens faceta**

Em determinadas circunstâncias, pode achar que as contagens faceta não correspondem aos conjuntos de resultados (consulte [Navegação por facetas na pesquisa Azure (fórum)](https://social.msdn.microsoft.com/Forums/azure/06461173-ea26-4e6a-9545-fbbd7ee61c8f/faceting-on-azure-search?forum=azuresearch)).

Contagens faceta podem ser imprecisas devido a arquitetura de sharding. Cada índice de pesquisa tem vários shards e cada uma delas relatórios facetas início N por contagem de documentos, em seguida, é combinada num único resultado. Se alguns shards tem muitas correspondência de valores, enquanto que as outras pessoas tiverem menor, poderá constatar que alguns valores de faceta estão em falta ou em-contados nos resultados de.

Apesar deste comportamento pode alterar em qualquer altura, se se deparar com este comportamento hoje, pode trabalhar à volta ao artificialmente insuflar a contagem de:<number> para um número para impor a relatórios completo de cada shard muito grande. Se o valor de count: maior ou igual ao número de valores exclusivos no campo, se tenha a certeza resultados precisos. No entanto, quando contagens de documento são realmente altos, que existe uma sanções de desempenho, por isso, utilizada esta opção racional.

<a name="rangefacets"></a>
##<a name="facet-navigation-based-on-a-range-values"></a>Navegação faceta com base em valores num intervalo

Faceting sobre intervalos é um requisito de aplicação de pesquisa comuns. Intervalos são suportados para dados numéricos e valores de data/hora. Pode ler mais sobre cada abordagem em [Documentos de pesquisa (Azure pesquisa API)](http://msdn.microsoft.com/library/azure/dn798927.aspx).

Pesquisa Azure simplifica construção de intervalo, fornecendo duas abordagens para computação de um intervalo de. Para as duas abordagens, pesquisa Azure cria os intervalos adequados, tendo em conta as entradas que forneceu. Por exemplo, se especificar valores do intervalo de 10 | 20 | 30, irá criar automaticamente intervalos de 0 -10, 10 a 20, 20-30. A aplicação de exemplo remove quaisquer intervalos que estão vazios. 

**Abordagem 1: Utilize o parâmetro de intervalo**<br/>
Para definir facetas preço em incrementos de $10, especifique:`&facet=price,interval:10`


**Abordagem 2: Utilizar uma lista de valores**<br/>
Para dados numéricos, pode utilizar uma lista de valores.  Considere o intervalo de faceta para listPrice, processado da seguinte forma:

  ![][5]

O intervalo especificado no ficheiro **CatalogSearch.cs** através de uma lista de valores:

    facet=listPrice,values:10|25|100|500|1000|2500

É criado a cada intervalo 0 a utilizar como ponto de partida, um valor a partir da lista como um ponto final e, em seguida, cortada do intervalo anterior para criar intervalos descontínuos. Pesquisa Azure irá fazê-lo como parte da navegação por facetas. Não tem de escrever código para estruturar cada intervalo.

### <a name="build-a-filter-for-facet-ranges"></a>Criar um filtro para intervalos faceta ###

Para filtrar documentos baseados num intervalo selecionado pelo utilizador, pode utilizar o `"ge"` e `"lt"` filtrar operadores numa expressão de duas partes que define os pontos finais do intervalo. Por exemplo, se o utilizador escolhe o intervalo de 10-25, o filtro seria `$filter=listPrice ge 10 and listPrice lt 25`.

Na aplicação do exemplo, a expressão de filtro utiliza parâmetros **priceFrom** e **priceTo** para definir os pontos finais. O método de **BuildFilter** **CatalogSearch.cs** contém a expressão de filtro que dá-lhe os documentos num intervalo.

  ![][6]

<a name="geofacets"></a> 
##<a name="filtered-navigation-based-on-geopoints"></a>Navegação filtrada com base em GeoPoints

É comum para ver filtros que o ajudam a escolher um arquivo, restaurante ou destino com base no seu proximidade à sua localização atual. Enquanto este tipo de filtro poderá ser semelhante a navegação por facetas, é realmente apenas um filtro. Vamos refere-lo aqui para aqueles que especificamente está à procura conselhos de implementação para esse problema de apresentação em particular.

Existem duas funções Geoespaciais no Azure pesquisa, **geo.distance** e **geo.intersects**.

- A função **geo.distance** se devolve a distância quilómetros entre dois pontos, um ser um campo e um ser uma constante passou como parte do filtro. 

- A função **geo.intersects** devolve verdadeira se for um determinado ponto dentro de um determinado polígono, onde o ponto de é um campo e o polígono for especificado como uma constante lista das coordenadas passada como parte do filtro.

Pode encontrar exemplos de filtro [da sintaxe das expressões OData (Azure procura)](http://msdn.microsoft.com/library/azure/dn798921.aspx).

<a name="tryitout"></a>
##<a name="try-it-out"></a>Experimentar

Azure pesquisa da Adventure Works demonstração no Codeplex contém os exemplos mencionados neste artigo. À medida que trabalha com os resultados da pesquisa, veja o URL para as alterações de construção de consulta. Esta aplicação acontece acrescentar facetas para o URI à medida que seleciona cada um deles.

1.  Configure a aplicação de exemplo para utilizar o seu URL do serviço e chave de api. 

    Repare que o esquema que é definido no ficheiro do projeto CatalogIndexer Program.cs. Especifica os campos de facetable para cor, listPrice, tamanho, peso, NomeDaCategoria e modelName.  Apenas algumas destas (cor, listPrice, NomeDaCategoria) realmente são implementadas no painel de navegação por facetas.

3.  Execute a aplicação. 

    Na primeira, está visível apenas uma caixa de pesquisa. Pode clique no botão Procurar imediato para obter todos os resultados ou escreva um termo de pesquisa.

    ![][7]
 
4.  Introduza um termo de pesquisa, tal como bicicleta e clique em **Procurar**. A consulta é executada rapidamente.
 
    Uma estrutura de navegação por facetas também é devolvida com os resultados da pesquisa.  No URL, são nulos facetas para as cores, categorias e preços. Na página de resultados de pesquisa, a estrutura de navegação por facetas inclui contagens para cada resultado faceta.

     ![][8]
 
5.  Clique numa cor, categoria e preço intervalo. Facetas são nulos numa pesquisa inicial, mas como tomem em valores, os resultados da pesquisa são cortados de itens que já não correspondem. Repare que o URI escolhe as alterações à consulta.

    ![][9]
 
6.  Para limpar a consulta por facetas para que pode experimentar comportamentos de consulta diferentes, clique em **Catálogo AdventureWorks** na parte superior da página.

    ![][10]
 
<a name="nextstep"></a>
##<a name="next-step"></a>Passo seguinte

Para testar os seus conhecimentos, pode adicionar um campo de faceta *modelName*. O índice já está configurado para este Faceta, para que não ao índice são necessárias alterações. Mas terá de modificar o código HTML para incluir uma nova faceta para modelos de e adicione o campo faceta o construtor de consultas.

Para obter mais informações sobre princípios de estrutura para navegação por facetas, recomendamos que as ligações seguintes:

- [Estruturar para pesquisa por facetas](http://www.uie.com/articles/faceted_search/)
- [Padrões de estrutura: Navegação por facetas](http://alistapart.com/article/design-patterns-faceted-navigation)

Pode também ver [Vôo picado abrangente do Azure pesquisa](http://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410). Em 45:25, não existe uma demonstração sobre como implementar facetas.

<!--Anchors-->
[How to build it]: #howtobuildit
[Build the presentation layer]: #presentationlayer
[Build the index]: #buildindex
[Check for data quality]: #checkdata
[Build the query]: #buildquery
[Tips on how to control faceted navigation]: #tips
[Faceted navigation based on range values]: #rangefacets
[Faceted navigation based on GeoPoints]: #geofacets
[Try it out]: #tryitout

<!--Image references-->
[1]: ./media/search-faceted-navigation/Facet-1-slide.PNG
[2]: ./media/search-faceted-navigation/Facet-2-CSHTML.PNG
[3]: ./media/search-faceted-navigation/Facet-3-schema.PNG
[4]: ./media/search-faceted-navigation/Facet-4-SearchMethod.PNG
[5]: ./media/search-faceted-navigation/Facet-5-Prices.PNG
[6]: ./media/search-faceted-navigation/Facet-6-buildfilter.PNG
[7]: ./media/search-faceted-navigation/Facet-7-appstart.png
[8]: ./media/search-faceted-navigation/Facet-8-appbike.png
[9]: ./media/search-faceted-navigation/Facet-9-appbikefaceted.png
[10]: ./media/search-faceted-navigation/Facet-10-appTitle.png

<!--Link references-->
[Designing for Faceted Search]: http://www.uie.com/articles/faceted_search/
[Design Patterns: Faceted Navigation]: http://alistapart.com/article/design-patterns-faceted-navigation
[Create your first application]: search-create-first-solution.md
[OData expression syntax (Azure Search)]: http://msdn.microsoft.com/library/azure/dn798921.aspx
[Azure Search Adventure Works Demo]: https://azuresearchadventureworksdemo.codeplex.com/
[http://www.odata.org/documentation/odata-version-2-0/overview/]: http://www.odata.org/documentation/odata-version-2-0/overview/ 
[Faceting on Azure Search forum post]: ../faceting-on-azure-search.md?forum=azuresearch
[Search Documents (Azure Search API)]: http://msdn.microsoft.com/library/azure/dn798927.aspx

 