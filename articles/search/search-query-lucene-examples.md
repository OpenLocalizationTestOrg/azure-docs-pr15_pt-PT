<properties
    pageTitle="Exemplos de consulta Lucene para pesquisa Azure | Pesquisa do Microsoft Azure"
    description="Sintaxe de consulta de Lucene para pesquisa nítida, pesquisa proximidade, aumentar a termos, pesquisa expressão regular e pesquisa de caracteres universais."
    services="search"
    documentationCenter=""
    authors="LiamCa"
    manager="pablocas"
    editor=""
    tags="Lucene query analyzer syntax"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="liamca"
/>

# <a name="lucene-query-syntax-examples-for-building-queries-in-azure-search"></a>Exemplos de sintaxe de consulta Lucene para construir consultas na pesquisa do Azure

Quando construir consultas para a pesquisa do Azure, pode utilizar a predefinição de [sintaxe de consulta simples](https://msdn.microsoft.com/library/azure/dn798920.aspx) ou o alternativo [Lucene analisador de consulta na pesquisa Azure](https://msdn.microsoft.com/library/azure/mt589323.aspx). O analisador de consulta Lucene suporta mais complexas construções de consulta, como consultas confinados de campo, pesquisa nítida, pesquisa de proximidade, aumentar a termos e pesquisa de expressão reqular.

Neste artigo, pode percorrer exemplos que apresentam Lucene sintaxe de consulta e resultados lado a lado. Exemplos, execute um índice de pesquisa pré-carregado no [JSFiddle](https://jsfiddle.net/), um editor de código online para testes script e HTML. 

Botão direito do rato sobre os URLs de exemplo de consulta para abrir JSFiddle numa janela do browser em separado.

> [AZURE.NOTE] Os exemplos seguintes tirar partido de um índice de pesquisa que consiste projectos disponíveis com base num conjunto de dados fornecido pelo iniciativa [Cidade de Nova Iorque OpenData](https://nycopendata.socrata.com/) . Estes dados não devem ser considerados atual ou concluída. O índice é um serviço de sandbox fornecida pela Microsoft. Não tem uma subscrição do Azure ou Azure a pesquisa para tentar estes consultas.

## <a name="viewing-the-examples-in-this-article"></a>Ver os exemplos neste artigo

Todos os exemplos neste artigo especifique o analisador de consulta Lucene através do parâmetro de pesquisa**queryType** . Ao utilizar o analisador de consulta Lucene a partir do seu código, vai especificar o **queryType** cada pedido.  Os valores válidos incluem **simples**|**completo**, com **simples** como o predefinido e **completo** para o analisador de consulta Lucene. Consulte o artigo [Procurar documentos (API do resto de serviço de pesquisa Azure)](https://msdn.microsoft.com/library/azure/dn798927.aspx) para obter detalhes sobre como especificar parâmetros de consulta.

**Exemplo 1** – botão direito do rato a consulta seguinte fragmento para o abrir numa nova página browser que carrega JSFiddle e executa a consulta:
- [& queryType = completa & Procurar = *](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*)

Esta consulta devolve os documentos a partir do nosso índice de tarefas (carregado num serviço sandbox)

Numa nova janela do browser, verá a fonte JavaScript e saída em HTML lado a lado. O script faz referência a uma consulta, que é fornecida pelos URLs de exemplo neste artigo. Por exemplo, no **exemplo 1**, a consulta subjacente é da seguinte forma:

    http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*

Aviso a consulta utiliza um índice de pesquisa do Azure pré-configurado denominado nycjobs. O parâmetro **searchFields** restringe a pesquisa a apenas empresas campo título. O **queryType** está definido para **completo**, que indica o Azure pesquisa a utilizar o analisador de consulta Lucene para esta consulta.

### <a name="fielded-query-operation"></a>Operação de consulta fielded

Pode modificar os exemplos neste artigo ao especificar uma construção **fieldname:searchterm** para definir uma operação de consulta fielded, onde o campo é uma única palavra e o termo de pesquisa é também uma única palavra ou expressão, opcionalmente, com operadores booleanos. Alguns exemplos incluem o seguinte:

- business_title:(senior NOT junior)
- Estado: ("Lisboa" e "Nova Jersey")

Certifique-se de que colocar várias cadeias entre aspas, se pretender ambas as cadeias de ser avaliada como uma única entidade, tal como neste caso procurar duas cidades distintas no campo localização. Além disso, certifique-se o operador está em maiúscula como ver com NOT e and.

O campo especificado na **fieldname:searchterm** tem de ser um campo pesquisável. Consulte o artigo [Criar índice (API do resto de serviço de pesquisa Azure)](https://msdn.microsoft.com/library/azure/dn798941.aspx) para obter detalhes sobre como os atributos de índice são utilizados nas definições do campo.

## <a name="fuzzy-search"></a>Pesquisa nítida

Uma pesquisa nítida localiza correspondências nos termos que têm uma construção semelhante. Por [Lucene documentação](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html), pesquisas nítidas são baseadas em [Damerau Levenshtein distância](https://en.wikipedia.org/wiki/Damerau%e2%80%93Levenshtein_distance).

Para fazer uma pesquisa nítida, utilize o til "~" símbolo no final de uma única palavra com um parâmetro opcional, um valor entre 0 e 2, que especifica a distância de editar. Por exemplo, "azul ~" ou "azul ~ 1" deve devolver azul, azuis e colar.

**Exemplo 2** --contexto o seguinte fragmento de consulta para lhe conferir um experimentar. Esta consulta procura nos títulos de negócio com sénior o termo no-los, mas não juniores:

- [& queryType = completa & Procurar = business_title:senior não juniores](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:senior+NOT+junior)

## <a name="proximity-search"></a>Pesquisa de proximidade

As pesquisas de proximidade são utilizadas para localizar termos que são próximas umas das outras num documento. Inserir um til "~" símbolo no final de uma expressão seguido do número de palavras que criar o limite de proximidade. Por exemplo, "hotel dos aeroportos" ~ 5 irá encontrar a hotel termos e dos aeroportos dentro das 5 palavras uns dos outros num documento.

**Exemplo 3** – o botão direito do rato o seguinte fragmento de consulta. Esta consulta procura para os projectos com associar o termo (onde-está mal escrita):

- [& queryType = completa & Procurar = business_title:asosiate ~](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:asosiate~)

**Exemplo 4** – o botão direito do rato na consulta. Procure tarefas com o termo "Analista Sénior" onde está separado por mais do que uma palavra:

- [& queryType = completa & Procurar = business_title: "Analista Sénior" ~ 1](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~1)

**Exemplo 5** -- experimente remover novamente as palavras entre o termo "Analista Sénior".

- [& queryType = completa & Procurar = business_title: "Analista Sénior" ~ 0](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~0)

## <a name="term-boosting"></a>Prazo Boosting

Aumentar a termos que se refere a classificação de um documento superior se contém o termo boosted, relativamente às documentos que não contêm o termo. Isto é diferente de perfis de pontuação em que os perfis de pontuação impulsionar determinados campos em vez de termos específicos. O exemplo seguinte ajuda ilustram as diferenças.

Considere um perfil de pontuação que aumenta corresponde a um determinado campo, tal como **Adicionar nova** no exemplo musicstoreindex. Aumentar a termos pode ser utilizada para ainda mais impulsionar determinada pesquisa termos mais elevados que outras pessoas. Por exemplo, "pedra ^ 2 eletrónica" impulsionar a documentos que contenham os termos de pesquisa no campo **género** mais elevado que os outros campos pesquisáveis no índice remissivo. Além disso, os documentos que contenham o termo de pesquisa "pedra" irão ser classificados superiores o outros termo de pesquisa "eletrónico" como resultado o valor de intensidade do termo (2).

Para aumentar um termo, utilize o acento circunflexo, "^", símbolo com um fator de intensidade (um número) no final do termo está a procurar. Quanto maior fator de intensidade, mais relevantes o termo serão relativamente às outras termos de pesquisa. Por predefinição, o factor intensidade é 1. Apesar do factor intensidade tem de ser positivo, pode ser menor que 1 (por exemplo, 0,2).

**Exemplo 6** – o botão direito do rato na consulta. Procurar tarefas com o termo "analista de computador" onde vemos não existem resultados com o computador de palavras e analista ainda, trabalhos de analista estão na parte superior dos resultados.

- [& queryType = completa & Procurar = business_title:computer analista](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

**Exemplo 7** – volte a tentar, este aumentar a altura de resultados com o computador termos sobre analista o termo se ambas as palavras não existir.

- [& queryType = completa & Procurar = business_title:computer ^ 2 analista](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

## <a name="regular-expression"></a>Expressão regular

Uma pesquisa de expressão regular localiza uma correspondência com base no conteúdo entre diagonais "/", como documentado a [classe de RegExp](http://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html).

**Exemplo 8** – o botão direito do rato na consulta. Procure tarefas com quer o termo sénior ou Junior.

- `&queryType=full&$select=business_title&search=business_title:/(Sen|Jun)ior/`

O URL para este exemplo não serão compostas corretamente na página. Como solução, copie o URL abaixo e colá-la o endereço de URL do browser:    `http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26queryType=full%26$select=business_title%26search=business_title:/(Sen|Jun)ior/)`


## <a name="wildcard-search"></a>Pesquisa de caracteres universais

Pode utilizar geralmente reconhecido sintaxe para o múltiplo (\*) ou única pesquisas de carateres universais do caráter (?). Tenha em atenção que o analisador de consulta Lucene suporta a utilização destes símbolos com um único termo e não uma expressão.

**Exemplo 9** – o botão direito do rato na consulta. Procurar tarefas que contêm o prefixo 'programa' que iria incluem títulos de negócio com os termos de programação e programador no mesmo.

- [& queryType = inteiro & $select = business_title & pesquisa = business_title:prog*](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26queryType=full%26$select=business_title%26search=business_title:prog*)

Não é possível utilizar um * ou? símbolo de como o primeiro caráter de uma pesquisa.


## <a name="next-steps"></a>Próximos passos

Experimente especificando o analisador de consulta Lucene no seu código. As ligações seguintes explicam como configurar consultas de pesquisa para .NET e os REST API. As ligações utilizam a sintaxe simples predefinida para que irá necessitar de aplicar o que aprendeu a partir deste artigo para especificar o **queryType**.

- [Índice de pesquisa Azure utilizando o SDK .NET de consulta](search-query-dotnet.md)
- [Índice de pesquisa Azure utilizar a API REST de consulta](search-query-rest-api.md)


 