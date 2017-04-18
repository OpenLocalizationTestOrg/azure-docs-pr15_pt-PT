# <a name="azure-technical-documentation-contributor-guide"></a>Guia de contribuinte Azure documentação técnica

Ter encontrado o repositório de GitHub que aloja a origem de para a documentação técnica que é publicada no Centro de documentação do Azure na [http://azure.microsoft.com/documentation](http://azure.microsoft.com/documentation).

Este repositório também contém orientações para ajudá-lo a contribuir para a nossa documentação técnica.  Para obter uma lista de artigos guia dos colaboradores, consulte [o índice](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-index.md).

## <a name="contribute-to-azure-documentation"></a>Contribuir para a documentação do Azure

Obrigado pelo interesse na documentação Azure!

* [Formas para contribuir](#ways-to-contribute)
* [Código de realizar](#code-of-conduct)
* [Sobre as contribuições ao conteúdo Azure](#about-your-contributions-to-azure-content)
* [Organização repositório](#repository-organization)
* [Utilizar o GitHub, Git e este repositório](#use-github-git-and-this-repository)
* [Como utilizar markdown para formatar o seu tópico](#how-to-use-markdown-to-format-your-topic)
* [Comentários, comentários e suporte](./contributor-guide/feedback-and-comments.md)
* [Mais recursos](#more-resources)
* [Índice de artigos de guia de todos os contribuintes](./contributor-guide/contributor-guide-index.md) (abre a nova página)

## <a name="ways-to-contribute"></a>Formas para contribuir 

Possam contribuir a [documentação Azure](http://azure.microsoft.com/documentation/) em algumas maneiras diferentes:

* Contribuir a um [debate fórum](http://social.msdn.microsoft.com/Forums/windowsazure/home).
* Submeta comentários Disqus na parte inferior de artigos.
* Facilmente podem contribuir para artigos técnicos na interface de utilizador GitHub. Encontrar o artigo neste repositório ou visite o artigo sobre [http://azure.microsoft.com/documentation](http://azure.microsoft.com/documentation) e clique na ligação no artigo que vai para a origem de GitHub para o artigo.
* Se estiver a fazer alterações substanciais para um artigo existente, adicionar ou alterar imagens ou contribuir com um novo artigo, tem de bifurcam este repositório, instalar Git festa, Markdown de marcação e saiba alguns comandos git.

##<a name="code-of-conduct"></a>Código de realizar

Este projeto tem aprovadas o [Microsoft abrir origem código de realizar](https://opensource.microsoft.com/codeofconduct/). Para mais informações, consulte o [Código de realizar perguntas mais frequentes](https://opensource.microsoft.com/codeofconduct/faq/) ou o contacto [opencode@microsoft.com](mailto:opencode@microsoft.com) com as perguntas adicionais ou comentários.

##<a name="about-your-contributions-to-azure-content"></a>Sobre as contribuições ao conteúdo Azure

###<a name="minor-corrections"></a>Correções secundárias

Correções secundárias ou clarificações que submeter para obter exemplos de documentação e o código neste repo estejam tapadas pela [Azure termos de utilização do Web site (ToU)](http://azure.microsoft.com/support/legal/website-terms-of-use/).


###<a name="larger-submissions"></a>Maiores submissões

Se submeter um pedido de solicitação com alterações novos ou significativos para documentação e exemplos de código, enviaremos um comentário no GitHub pedir-lhe para submeter um contrato de licença de contribuição online (CLA) se estiver destes grupos:

* Membros do grupo tecnologias abrir da Microsoft.
* Contribuintes quem não funcionam para o Microsoft.

É necessário para concluir o formulário online antes do podemos pode aceitar o seu pedido de solicitação.

Detalhes completos estão disponíveis no [http://azure.github.io/guidelines/#cla](http://azure.github.io/guidelines/#cla).

## <a name="repository-organization"></a>Organização repositório

O conteúdo no repositório de conteúdo do azure segue-se a organização de documentação no [Azure.Microsoft.com](http://azure.microsoft.com). Este repositório contém duas pastas de raiz:

### <a name="articles"></a>\articles

A pasta *\articles* contém os artigos de documentação formatados como ficheiros de markdown com uma extensão de *md* .

Artigos no diretório de raiz são publicados Azure.Microsoft.com no caminho *http://azure.microsoft.com/documentation/articles/ {artigo-nome-sem-md} /*.

* **Artigo nomes de ficheiros:** Consulte o [nosso orientações de nomenclatura de ficheiros](./contributor-guide/file-names-and-locations.md).

Artigos dentro os seus próprios pasta serviço são publicados Azure.Microsoft.com no caminho *http://azure.microsoft.com/documentation/articles/service-folder/ {artigo-nome-sem-md} /*

* **Subpastas multimédia:** A pasta *\articles* contém a *\media.* pasta para ficheiros de multimédia de artigo de diretório de raiz, dentro que são subpastas com as imagens para cada artigo.  As pastas de serviço contêm uma pasta de suporte em separado para os artigos dentro de cada pasta de serviço. As pastas de imagem do artigo são com o nome idêntico ao ficheiro de artigo, menos a extensão de ficheiro *md* .

### <a name="includes"></a>\includes

Pode criar secções de conteúdo reutilizáveis devem ser incluídas num ou mais artigos. Ver [as extensões de personalizados utilizadas no nosso conteúdo técnico](./contributor-guide/custom-markdown-extensions.md).

### <a name="markdown-templates"></a>modelos de \markdown

Esta pasta contém o nosso modelo markdown padrão com a formatação de markdown básica que necessita para um artigo.

### <a name="contributor-guide"></a>\contributor-Guide

Esta pasta contém artigos que fazem parte do Guia dos nossos contribuintes.  

## <a name="use-github-git-and-this-repository"></a>Utilizar o GitHub, Git e este repositório

Para obter informações sobre como pode contribuir, como utilizar a IU GitHub para contribuir pequenas alterações e como bifurcam e clonar o repositório de contribuições mais significativos, consulte o artigo [instalar e configurar o ferramentas para a cocriação na GitHub](./contributor-guide/tools-and-setup.md).

Se instalar GitBash e optar por trabalhar localmente, os passos para criar um novo local ramo de trabalho, efetuar as alterações e submeter que as alterações novamente para o ramo principal estão listados na [Git comandos para criar um novo artigo ou atualizar um artigo existente](./contributor-guide/git-commands-for-master.md)

### <a name="branches"></a>Ramificações

Recomendamos que cria ramificações local de trabalho que um âmbito específico de alteração de destino. Ramo deve ser limitado a um único conceito/artigo tanto para simplificar o fluxo de trabalho e reduzir a possibilidade de conflitos de impressão em série.  Os esforços seguintes são do âmbito adequado para um novo ramo:

* Um novo artigo (e imagens associadas)
* Edições de ortografia e gramática num artigo.
* Aplicar uma única alteração de formatação ao longo de um conjunto maior de artigos (por exemplo, novo rodapé direitos de autor).

## <a name="how-to-use-markdown-to-format-your-topic"></a>Como utilizar markdown para formatar o seu tópico

Todos os artigos neste repositório utilizam GitHub flavored markdown.  Aqui está uma lista de recursos.

- [Noções básicas de markdown](https://help.github.com/articles/markdown-basics/)

- [Cheatsheet markdown imprimível](./contributor-guide/media/documents/markdown-cheatsheet.pdf?raw=true)

- Para nossa lista de editores markdown, consulte as [Ferramentas e do tópico de configuração](./contributor-guide/tools-and-setup.md#install-a-markdown-editor).

## <a name="article-metadata"></a>Metadados do artigo

Artigo metadados permite determinadas funcionalidades no web site da azure.microsoft.com, como o autor imputação, imputação de contribuinte, menu de trilhos, descrições de artigo e SEO otimizações, bem como Microsoft elaboração de relatórios utiliza para avaliar o desempenho do conteúdo. Por isso, os metadados são importante! [Eis as informações para se certificar de que seus metadados de concluir o processo para a direita](./contributor-guide/article-metadata.md).

### <a name="labels"></a>Etiquetas

Etiquetas automatizadas são atribuídas para separar os pedidos para ajude-na gerir o fluxo de trabalho do pedido de solicitação e para ajudar a permitem-lhe saber o que se passa com o seu pedido de solicitação:

* Contrato de licença de contribuição relacionados com
    * cla não obrigatório: A alteração é relativamente secundária e não requer que inicie sessão um CLA.
    * obrigatório cla: O âmbito da alteração está relativamente grande e requer que inicie sessão um CLA.
    * sessão iniciada cla: O colaborador assinado CLA, para que o pedido de solicitação pode agora avançar para revisão.
* Pilar etiquetas: etiquetas tais como PnP, aplicações modernos e CdC ajudam categorizar os pedidos de solicitação pela organização interna que necessita para rever o pedido de solicitação.
* Alterar enviada para o autor: foi notificado o autor do pedido solicitação pendentes.

## <a name="more-resources"></a>Mais recursos

Consulte o [índice do guia de nossos contribuinte](./contributor-guide/contributor-guide-index.md) para todos os tópicos nosso orientações.
