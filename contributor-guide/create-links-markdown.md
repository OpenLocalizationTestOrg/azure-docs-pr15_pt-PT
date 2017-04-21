<properties
   pageTitle="Criar ligações no artigos markdown" description="Explica como código ligaçoes cruzadas no markdown." metaKeywords="" services="" solutions="" documentationCenter="" authors="tysonn" videoId="" scriptId="" manager="carolz" />

<tags ms.service="contributor-guide" ms.devlang="" ms.topic="article" ms.tgt_pltfrm="" ms.workload="" ms.date="02/03/2015" ms.author="tysonn" />

# <a name="linking-guidance-for-azure-technical-content"></a>Ligar a seta de quatro pontas Azure conteúdo técnico

### <a name="links-from-one-acom-article-to-another"></a>Ligações a partir de um artigo ACOM para outro

Para criar uma ligação de inline a partir de um artigo técnico ACOM para outro artigo técnico de ACOM, utilize a seguinte sintaxe de ligação:  

- Artigo de uma hiperligações do directório de serviço para outro artigo no mesmo directório de serviço:

  `[link text](article-name.md)`

- Uma artigo contém ligações a partir de um subdirectório de serviço para um artigo no diretório de raiz:

  `[link text](../article-name.md)`

- Um artigo nas ligações de diretório de raiz para um artigo num subdirectório serviço: 

  `[link text](./service-directory/article-name.md)`

- Artigo de uma ligações de subdirectório de serviço para um artigo no outro subdirectório de serviço:

  `[link text](../service-directory/article-name.md)`
 

## <a name="links-to-anchors"></a>Ligações para as âncoras

Não possui que criar as âncoras - são gerados automaticamente na hora para todos os títulos de H2 de publicação. A única coisa que tem de fazer é criar ligações para as secções H2.

- Para ligar para um título no mesmo artigo:

  `[link](#the-text-of-the-H2-section-separated-by-hyphens)`  
  `[Create cache](#create-cache)`

- Para ligar a uma âncora no outro artigo no mesmo subdirectório:

  `[link text](article-name.md#anchor-name)`
  `[Configure your profile](media-services-create-account.md#configure-your-profile)`

- Para ligar a uma âncora no outro subdirectório de serviço:

  `[link text](../service-directory/article-name.md#anchor-name)`
  `[Configure your profile](../service-directory/media-services-create-account.md#configure-your-profile)`

Uma forma de automatizar o processo de criação de ligações nos seus artigos para ligações de âncora gerados automaticamente é [MarkdownAnchorLinkGenerator - uma ferramenta para gerar ligações de âncora para ACOM no formato adequado](https://github.com/Azure/Azure-CSI-Content-Tools/tree/master/Tools/ACOMMarkdownAnchorLinkGenerator).

## <a name="links-from-includes"></a>Incluem ligações a partir de

Uma vez que incluir ficheiros estão localizados no diretório de outra, terá de utilizar já relativos caminhos, conforme apresentado abaixo. Para ligar a um artigo de um ficheiro de incluir, utilize este formato:

    [link text](../articles/service-folder/article-name.md)
    
Saiba mais sobre como utilizar um ficheiro de incluir as [diretrizes de extensões de markdown personalizada](custom-markdown-extensions.md#includes).

## <a name="links-in-selectors"></a>Ligações no seletores

Se tiver seletores que se encontram incorporados numa incluir, utilizaria este tipo de ligação de: 

    > [AZURE. SELECTOR de lista (Dropdown1 | Dropdown2)]     -  [(Texto1 | Exemplo1)](../articles/service-folder/article-name1.md)
    - [(Texto1 | Exemplo2)](../articles/service-folder/article-name2.md)
    - [(Texto2 | Exemplo3)](../articles/service-folder/article-name3.md)
    - [(Texto2 | Exemplo4)](../articles/service-folder/article-name4.md)


## <a name="reference-style-links"></a>Ligações de estilo de referência

Pode utilizar ligações de estilo de referência para facilitar a leitura do seu conteúdo de origem. As ligações de estilo de referência substitua a sintaxe da ligação de inline simplificado sintaxe que permite-lhe mover os URLs longos para o final do artigo. Eis um exemplo de audácia Fireball:

Texto inline:

    I get 10 times more traffic from [Google][1] than from [Yahoo][2] or [MSN][3].

Ligação referências no final do artigo:

    <!--Reference links in article-->
    [1]: http://google.com/
    [2]: http://search.yahoo.com/  
    [3]: http://search.msn.com/

Certifique-se de que incluir o espaço após os dois pontos, antes da ligação. Ao associar a outros artigos técnicos, caso se esqueça de incluir o espaço, vai ser dividida a ligação no artigo publicado. 

## <a name="link-to-acom-pages-that-are-not-part-of-the-technical-documentation-set"></a>Ligação para páginas ACOM que não fazem parte do conjunto de documentação técnica

Para ligar a uma página no ACOM (como uma página comparar, página SLA ou mais nada que não seja um artigo de documentação), utilize um URL absoluto, mas omitir a região. O objetivo aqui é que funcionam ligações na GitHub e no site composto:

    [link text](http://azure.microsoft.com/pricing/details/virtual-machines/)


## <a name="link-to-msdn-or-technet"></a>Ligação para o MSDN ou TechNet

Quando precisar de ligação para o MSDN ou TechNet, utilize a hiperligação completa para o tópico e remover a en-us idioma a partir da hiperligação. 

### <a name="use-friendly-link-text-for-all-links"></a>Utilizar texto de ligação amigável para todas as ligações

As palavras que incluir uma ligação devem ser amigáveis - por outras palavras, devem ser normais palavras em inglês ou o título da página que está a ligar. Não utilize "Clique aqui". É recomendável SEO e não descrever adequadamente de destino.

**Corrigir:**

- `For more information, see the [contributor guide index](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-index.md).`

- `For more details, see the [SET TRANSACTION ISOLATION LEVEL](https://msdn.microsoft.com/library/ms173763.aspx) reference.`

**Incorretos:**

- `For more details, see [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).`

- `For more information, click [here](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-index.md).`


## <a name="fwlinks"></a>FWLinks

Evite FWLinks (nosso sistema redirecionamento) no conteúdo azure.microsoft.com. Devem ser utilizados apenas como um último recurso quando precisar de criar uma ligação para uma página cujo URL ainda não sabe. Quase nunca realmente forem necessárias. Para ACOM, pode definir o nome do ficheiro, para que pode saiba o que irá estar antecedência. Para um tópico de biblioteca que ainda não foi publicado, pode criar uma ligação que utiliza o tópico GUID para que não tem de utilizar uma ligação FW.

Se tem de utilizar uma ligação FW numa página web, inclua o parâmetro P para torná-lo um redirecionamento permanente:

    http://go.microsoft.com/fwlink/p/?LinkId=389595

Quando colar o URL de destino para a ferramenta de ligação FW, lembre-se remover a região, se a sua ligação de destino é biblioteca ACOM, ou o MSDN ou TechNet.

## <a name="remember-the-azure-library-chrome"></a>Lembre-se o chrome biblioteca Azure!
Se pretender ligar a um tópico de biblioteca Azure que encontra-se neste [nó](https://msdn.microsoft.com/library/azure), lembre-se especificar o Azure chrome na ligação (/ azure /). O chrome Azure partilha as opções de navegação ACOM e apresenta apenas o conteúdo da biblioteca da MSDN Azure. Uma ligação corretamente âmbito tem o seguinte aspeto:

    http://msdn.microsoft.com/library/azure/dd163896.aspx

Caso contrário, a página será apresentada na vista MSDN padrão, com toda a árvore MSDN apresentada.

### <a name="contributors-guide-links"></a>Ligações de guia dos colaboradores

- [Artigo Descrição geral](./../README.md)
- [Índice de artigos de orientação](./contributor-guide-index.md)

<!--image references-->
[1]: ./media/create-tables-markdown/table-markdown.png
[2]: ./media/create-tables-markdown/break-tables.png
