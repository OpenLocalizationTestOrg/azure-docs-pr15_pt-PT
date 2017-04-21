<properties
    title="required"
    pageTitle="Extensões markdown personalizados utilizadas no nossos artigos técnicos"
    description="Lista as extensões de markdown personalizado que permitem vídeos incorporados, notas e sugestões, conteúdo reutilizável e outro item nos artigos técnico azure.microsoft.com."
    services=""
    solutions=""
    documentationCenter=""
    authors="tysonn"
    manager="carolz"
    editor=""/>

<tags
    ms.service="contributor-guide"
    ms.devlang=""
    ms.topic="article"
    ms.tgt_pltfrm=""
    ms.workload=""
    ms.date="01/22/2015"
    ms.author="tysonn"/>

## <a name="markdown-for-azuremicrosoftcom"></a>Markdown para Azure.microsoft.com

Para obter sugestões de markdown gerais, consulte o artigo [Noções básicas de Markdown](https://help.github.com/articles/markdown-basics/) e os nossos [markdown cheatsheet](./media/documents/markdown-cheatsheet.pdf?raw=true). Se precisar de criar ligaçoes artigo cruzadas no markdown, consulte o artigo [orientações ligação] (. / create-links-markdown.md#markdown-syntax-for-acom-relative-links.md/).

Azure.microsoft.com suporta [vedadas blocos de código](https://help.github.com/articles/github-flavored-markdown/#fenced-code-blocks) e o [Realce de sintaxe](https://help.github.com/articles/github-flavored-markdown/#syntax-highlighting). No entanto, ACOM suporta apenas uma sintaxe de cores, independentemente do idioma que especificar num bloco de código de realce.

## <a name="custom-markdown-extensions-used-in-our-technical-articles"></a>Extensões markdown personalizados utilizadas no nossos artigos técnicos

Utilizam o nossos artigos GitHub flavored markdown para a maioria dos artigo formatação - os parágrafos, ligações, listas, cabeçalhos, etc. Mas utilizamos extensões markdown personalizado onde precisamos formatação mais rica nas páginas compostas no azure.microsoft.com. Eis as extensões de que estamos atualmente a utilizar:

+ [Notas e sugestões]
+ [Inclui]
+ [Vídeos incorporados]
+ [Seletores tecnologia e a plataforma]

## <a name="notes-and-tips"></a>Notas e sugestões

Pode escolher a partir de 4 tipos de notas e sugestões:

- AZURE. NOTA
- AZURE. AVISO
- AZURE. TIPss
- AZURE. IMPORTANTE

###<a name="usage"></a>Utilização
Em geral, utilize as notas e sugestões com pouca frequência em toda a sua artigos. Quando utilizá-las, escolha o tipo de nota ou sugestão adequado:

- Utilize o AZURE. Nota: para realçar informações neutras ou positivas que realça ou os pontos-chave do texto principal de suplementos. Uma nota fornece informações que só se aplica a casos especiais.

  ![](./media/custom-markdown-extensions/Notes-note.PNG)

- Utilize o AZURE. AVISO ao alertar o utilizador para uma condição que poderá fazer com que um problema no futuro. Por exemplo, selecionar uma opção de determinadas ou efetuar uma determinada escolha poderá permanentemente bloquear lhe num cenário em particular.

  ![](./media/custom-markdown-extensions/Notes-warning.PNG)

- Utilize o AZURE. Sugestão para ajudar os utilizadores que se aplicam a técnicas e procedimentos descritos no texto para as suas necessidades específicas. Uma sugestão também pode sugerir métodos alternativos que poderão não ser óbvios. No entanto, sugestões, não são essenciais para a noções básicas sobre o texto.

  ![](./media/custom-markdown-extensions/Notes-tip.PNG)

- Utilize o AZURE. IMPORTANTE para fornecer informações que são fundamental para a conclusão de uma tarefa.

  ![](./media/custom-markdown-extensions/Notes-important.PNG)

Enquanto estas notas e sugestões de suporte blocos de código, imagens, listas e hiperligações, experimente manter as suas notas e sugestões simples e de fácil. Se encontrar o seu próprio criar notas complexas com muitos formatação, que poderá um sinal de que só precisa de outra secção no texto principal do artigo. E, notas demasiadas um artigo podem ser que distraiam e disco rígido digitalizar ou ler.

###<a name="sample-markdown"></a>Markdown de exemplo

Os exemplos todos os mostram um AZURE. TENHA EM ATENÇÃO. Para utilizar uma sugestão, aviso ou importante, substitua "Nota" na markdown:

    > [AZURE.TIP]

    > [AZURE.WARNING]

    > [AZURE.IMPORTANT]

Único parágrafo:

    > [AZURE.NOTE] Para concluir este tutorial, tem de ter uma conta do Microsoft Azure active. Se não tiver uma conta, pode criar uma conta de avaliação gratuita apenas de duas minutos.

Multiparagraph:

    > [AZURE.NOTE] Para concluir este tutorial, tem de ter uma conta do Microsoft Azure active.
    >
    > Se não tiver uma conta, pode [criar uma conta de avaliação gratuita](http://www.windowsazure.com/pricing/free-trial/) apenas de duas minutos.

## <a name="includes"></a>Inclui

Texto reutilizável no nosso repositório GitHub reside nos ficheiros que chamamos "inclui". Quando tiver o texto que tem de ser utilizados em vários artigos, incluir uma referência a esse ficheiro das informações reutilizáveis. A incluir propriamente dito é um ficheiro simples markdown (MD). Pode conter qualquer markdown válido, incluindo texto, hiperligações e imagens. Todos os incluem markdown ficheiros tem de estar no [a / inclui directório](https://github.com/Azure/azure-content/tree/master/includes) na raiz do repositório. Quando o artigo é publicado, o texto incluir está totalmente integrado no tópico publicado.

- Utilizamos uma sintaxe específica para fazer referência a uma incluir.

- Ficheiros de multimédia que introduz um incluir tem de ser criados numa pasta multimédia específicos para a incluir. Pastas de multimédia para inclui pertence na [pasta azure-conteúdo/inclui/multimédia](https://github.com/Azure/azure-content/tree/master/includes/media). O diretório de multimédia não deve conter imagens nas suas de raiz. Se a incluir não tiver imagens, em seguida, um diretório de multimédia correspondentes não é necessário.

###<a name="usage"></a>Utilização

- Utilização inclui onde precisar o mesmo texto seja apresentado nos vários artigos.

- Inclui destinam-se a ser utilizado para significativas quantidades de conteúdo - um parágrafo ou dois, um procedimento partilhado ou uma secção partilhada. Não utilizá-los para tudo mais pequeno do que uma frase; **são não para nomes de produtos**.

- Certifique-se todo o texto numa incluir escrito frases completas ou frases que não dependem de texto anterior ou seguinte texto no artigo que referencia a incluir. Ignorar a estas orientações cria uma cadeia traduzí no artigo que interrompe a experiência localizada. 

- Incorporar não inclui outras inclui. Não são suportadas pelo DPS sistema de publicação.

- Não partilhe ficheiros de multimédia entre ficheiros. Utilize um ficheiro separado com um nome exclusivo para cada incluir e o artigo. Armazene o ficheiro de multimédia na pasta de multimédia associada a incluir.

- Não utilize uma incluir como apenas o conteúdo de um artigo.  Inclui destinam-se para ser suplementar o conteúdo do resto do artigo.

- Uma vez que todas as inclui têm de ser o / inclui diretório, o caminho para uma incluir a partir de um artigo está sempre

    .. / inclui

- NÃO repetir uma referência de nome de ficheiro hiperligação ou imagem no artigo e a incluir. Adicionar "-incluir" para a ligação referência ou multimédia nome de ficheiro para evitar a referência de repetição:

 **Referência de ligação**

 Alterar: odata.org para: incluir odata.org

 **Referência de imagem**

 Alterar: table.png para: include.png de tabela

###<a name="sample-markdown"></a>Markdown de exemplo
A sintaxe para adicionar um incluir para um artigo de documentação é:

    [AZURE.INCLUDE [include-short-name](../includes/include-file-name.md)]

Exemplo

    [AZURE.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

A primeira parte da incluir é o nome de incluir sem o caminho e sem a extensão de MD. A segunda parte é o caminho relativo para a incluir na / inclui diretório, com a extensão de MD.

###<a name="rendering"></a>Composição

Na página de compostas GitHub, a incluir serão compostas da seguinte forma:

 [AZURE. INCLUIR o armazenamento de BLOBs How to]

No HTML composta na azure.microsoft.com, o HTML a partir de inclui é fundidos com o resto do HTML do documento. No entanto, o código HTML irá conter uma HTML comentário com o original incluir markdown nome de ficheiro e o hash de consolidação de GitHub. Este comentário é incluído para fins de resolução de problemas para que o conteúdo de origem facilmente pode ser identificado e que se encontram no GitHub:

  ![](./media/custom-markdown-extensions/include.png)


## <a name="embedded-videos"></a>Vídeos incorporados

Os nossos artigos técnicos suportam embeddeded vídeos no artigos técnicos desde que os vídeos são num site do [canal 9](http://channel9.msdn.com/) da Microsoft. Os vídeos a partir do canal 9 devem estar integrados com [o Centro de vídeo azure.microsoft.com](http://azure.microsoft.com/documentation/videos/home/). Atualmente não suportamos vídeos do YouTube incorporados; Se for um contribuinte da Comunidade, está bem-vindo ao ligar no YouTube, se o vídeo que pretende destacar publicado aí. Devem utilizar contribuintes Microsoft 9 de canal e do Centro de vídeo.

### <a name="usage"></a>Utilização

- Certifique-se de que o vídeo está no centro do vídeo.

- Copie o ID do vídeo a partir do URL amigável do vídeo no canal 9 ou a partir do Centro de vídeo do Azure. Por exemplo, o ID do vídeo para o vídeo no [http://azure.microsoft.com/documentation/videos/azure-scheduler-unusual-schedules/](http://azure.microsoft.com/documentation/videos/azure-scheduler-unusual-schedules/) é **azure-scheduler-invulgares-agendas**.

### <a name="syntax"></a>Sintaxe

    > [AZURE.VIDEO video-id-string]

### <a name="rendering"></a>Composição

Em GitHub: [https://github.com/Azure/azure-content-pr/blob/master/articles/web-sites-backup.md](https://github.com/Azure/azure-content-pr/blob/master/articles/web-sites-backup.md)

Artigo publicado: [http://azure.microsoft.com/documentation/articles/web-sites-backup/](http://azure.microsoft.com/documentation/articles/web-sites-backup/)


## <a name="technology-and-platform-selectors"></a>Seletores tecnologia e a plataforma

Utilize tecnologia e a plataforma switchers artigos técnicos quando o autor várias versões do mesmo artigo diferenças de endereço na implementação ao longo do tecnologias ou plataformas. Este é normalmente mais aplicável a nossa conteúdo plataforma móvel para programadores. Existem dois tipos diferentes de seletores, [seletores simples](#simple-selectors) e [seletores bidirecional](#two-way-selectors).

Uma vez que o mesmo markdown selector vai cada tópico na seleção, recomendamos que colocar no selector de seu tópico numa incluir, em seguida, referenciar o que incluir em todos os tópicos que utilizam o mesmo selector.

###<a id="simple-selectors"></a>Seletores simples

Compor simples seletores (unidirecional) como um conjunto de botões de opção imediatamente abaixo do título. Utilize estes botões quando precisam de clientes escolher entre tópicos num conjunto de plataforma ou tecnologia único, como o .NET, Node.js e Java.  Utilizar a extensão markdown personalizado para qualquer seletores - não utilizar HTML para seletores.  

Consulte o artigo [Introdução ao concentradores de notificação](http://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started/) para ver como o autor criado 8 versões do mesmo artigo, mas seletores utilizados para ativar a navegação ao longo-los a todos.

![Exemplo de Seletor simples](./media/custom-markdown-extensions/selectors.PNG)

####<a name="syntax"></a>Sintaxe

    > [AZURE.SELECTOR]
    - [Ligar a etiqueta #1](link #1 url)
    - [etiqueta da hiperligação #2](link #2 url)

Exemplo:

    > [AZURE.SELECTOR]
    - [Universal Windows](../articles/notification-hubs-windows-store-dotnet-get-started/)
    - [Windows Phone](../articles/notification-hubs-windows-phone-get-started/)
    - [iOS](../articles/notification-hubs-ios-get-started/)
    - [Android](../articles/notification-hubs-android-get-started/)
    - [Kindle](../articles/notification-hubs-kindle-get-started/)
    - [Baidu](../articles/notification-hubs-baidu-get-started/)
    - [Xamarin.iOS](../articles/partner-xamarin-notification-hubs-ios-get-started/)
    - [Xamarin.Android](../articles/partner-xamarin-notification-hubs-android-get-started/)

#### <a name="rendering"></a>Composição

A imagem acima mostra a composição no azure.microsoft.com. Nas páginas compostas GitHub, os seletores compostos como uma lista com marcas de ligações.

###<a id="two-way-selectors"></a>Seletores bidirecional

Seletores bidirecional permite que os utilizadores uma tópicos a partir de uma matriz de duas forma. Isto é essencial quando uma Azure a tecnologia de apoio, tais como de serviços móveis, suporta múltiplas plataformas de back-end, bem como vários clientes. Tenha em atenção o seguinte:

- Enquanto que foi concebido como `(Platform | Backend)`, o texto dropwdown agora pode ser personalizado.
- Não necessita de um item de lista para cada ponto na matriz, mas apenas ter um item onde um URL de tópico existe e não é um duplicado.
- A ligação pode ser qualquer URL, embora é geralmente outro GitHub tópico.

Consulte o artigo [começar a trabalhar com os serviços do telemóvel](http://azure.microsoft.com/en-us/documentation/articles/mobile-services-ios-get-started/) para ver como o autor criado 15 versões do mesmo artigo (9 plataformas de cliente móvel e plataformas de back-end 2), mas seletores utilizados para ativar a navegação ao longo-los a todos. Note que 3 artigos não tem ambas as versões de back-end.

![Exemplo de seletores bidirecional](./media/custom-markdown-extensions/selector-list.png)

####<a name="syntax"></a>Sintaxe

    > [AZURE. SELECTOR de lista (Dropdown1 | Dropdown2)]     -  [(Dropdown1Text1 | Dropdown2Text1)](../articles/dropdown1-text1-dropdown2-text1.md)
    - [(Dropdown1Text1 | Dropdown2Text2)](../articles/dropdown1-text1-dropdown2-text1.md)
    - [(Dropdown1Text2 | Dropdown2Text3)](../articles/dropdown1-text1-dropdown2-text1.md)
    - [(Dropdown1Text3 | Dropdown2Text4)](../articles/dropdown1-text1-dropdown2-text1.md)

Exemplo:

    > [AZURE. SELECTOR de lista (plataforma | Back-end)]     -  [(iOS | .NET)](./mobile-services-dotnet-backend-ios-get-started-push.md)
    - [(iOS | JavaScript)](./mobile-services-javascript-backend-ios-get-started-push.md)
    - [(Windows universal c# | .NET)](./mobile-services-dotnet-backend-windows-universal-dotnet-get-started-push.md)
    - [(Windows universal c# | JavaScript)](./mobile-services-javascript-backend-windows-universal-dotnet-get-started-push.md)
    - [(Windows Phone | .NET)](./mobile-services-dotnet-backend-windows-phone-get-started-push.md)
    - [(Windows Phone | JavaScript)](./mobile-services-javascript-backend-windows-phone-get-started-push.md)
    - [(Android | .NET)](./mobile-services-dotnet-backend-android-get-started-push.md)
    - [(Android | JavaScript)](./mobile-services-javascript-backend-android-get-started-push.md)
    - [(Xamarin iOS | JavaScript)](./partner-xamarin-mobile-services-ios-get-started-push.md)
    - [(Xamarin Android | JavaScript)](./partner-xamarin-mobile-services-android-get-started-push.md)

#### <a name="rendering"></a>Composição

A imagem acima mostra a composição no azure.microsoft.com. Nas páginas compostas GitHub, os seletores compostos como uma lista com marcas de ligações.

<!--Anchors-->
[Notas e sugestões]: #notes-and-tips
[Inclui]: #includes
[Vídeos incorporados]: #embedded-videos
[Tecnologia e a plataforma seletores]: #technology-and-platform-selectors

###<a name="contributors-guide-links"></a>Ligações de guia dos colaboradores

- [Artigo Descrição geral](./../README.md)
- [Índice de artigos de orientação](./contributor-guide-index.md)
