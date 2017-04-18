

#<a name="metadata-for-azure-technical-articles"></a>Metadados para artigos técnicos Azure

Todos os artigos técnicos Azure contenham duas secções de metadados - uma secção de propriedades e uma secção de etiquetas. Na secção propriedades permite algumas automatização do Web site e conteúdos SEO, enquanto a secção etiquetas permite muitas relatórios de conteúdo internos. Ambas as secções são necessárias.

- [Sintaxe]
- [Utilização]
- [Atributos e valores para a secção de propriedades]
- [Atributos e valores para a secção de etiquetas]

##<a name="syntax"></a>Sintaxe

Na secção Propriedades utiliza a seguinte sintaxe:

    <properties
       pageTitle="Page title that displays in search results and the browser tab | Microsoft Azure"
       description="Article description that will be displayed on landing pages and in most search results"
       services="service-name"
       documentationCenter="dev-center-name"
       authors="GitHub-alias-of-only-one-author"
       manager="manager-alias"
       editor=""
       tags="optional"
       keywords="For use by SEO champs only. Separate terms with commas. Check with your SEO champ before you change content in this article containing these terms."/>

A secção etiquetas utiliza a seguinte sintaxe:

    <tags
       ms.service="required"
       ms.devlang="may be required"
       ms.topic="article"
       ms.tgt_pltfrm="may be required"
       ms.workload="na"
       ms.date="mm/dd/yyyy"
       ms.author="Your MSFT alias or your full email address;semicolon separates two or more"/>

##<a name="usage"></a>Utilização

- O nome do elemento e os nomes de atributo são sensíveis a maiúsculas.
- O <properties> secção tem de ser a primeira linha do seu ficheiro.
- Sair de uma linha em branco depois de cada secção de metadados e antes do título da página para se certificar de que o título da página é corretamente convertido HTML durante o processo de publicação.

## <a name="attributes-and-values-for-the-properties-section"></a>Atributos e valores para a secção de propriedades

![](./media/article-metadata/checkmark-small.png)**TítuloPágina**: necessário; importante para SEO. O texto deste atributo é apresentada no separador do browser e como o título num resultado de pesquisa. Utilizar caracteres de 55 60, incluindo espaços e incluindo o identificador de site *| Microsoft Azure* (introduzido como: espaço Espaço pipe Microsoft Azure).  A TítuloPágina deve ser diferente da H1.

![](./media/article-metadata/checkmark-small.png)**Descrição**: necessário; importante para a funcionalidade de site e SEO (relevância). A descrição deve ser de pelo menos 125 caracteres longas para 155 caracteres máximos, incluindo espaços. Descreva o objetivo do seu conteúdo, para que os clientes ficará a saber se pretende selecione-a partir de uma lista de resultados de pesquisa. O valor é:

- Este texto poderá ser apresentado como a descrição ou parágrafo abstratos nos resultados de pesquisa no Google.
- Este texto é apresentado nos [resultados de índice remissivo do artigo](https://azure.microsoft.com/documentation/articles/).

![](./media/article-metadata/checkmark-small.png)**Serviços**: necessário para artigos que lidar com um serviço. Este valor é referido ofter como "Libra massa servço". Liste todos os serviços aplicáveis, separados por vírgulas. O primeiro serviço lista irá unidade o menu de trilhos navegação para a página e o painel de navegação esquerdo que é apresentada com a página.

Artigos que especificar um valor de serviços e um valor de documentationCenter, o valor de serviços será unidade de navegação de trilho. Valores adicionais que lista irá aparecer como etiquetas no artigo publicado. Valores:

- Active directory
- ativo-diretório-b2c
- ativo-diretório-ds
- aplicação service\api
- gestão de API
- aplicação de serviço
- aplicação servic\mobile
- aplicação service\web
- aplicação service\logic
- gateway de aplicação
- informações de aplicação
- automatização
- portal do Azure
- Gestor de recursos do Azure
- pilha de Azure
- cópia de segurança
- batch
- melhores práticas
- Serviços de BizTalk
- cache
- CDN
- serviços em nuvem
- catálogo de dados
- fábrica de dados
- análise de dados lake
- arquivo de dados lake
- laboratório de devtest
- DNS
- documentdb
- expressroute
- concentradores de evento
- hdinsight
- concentrador IOT
- Cofre de palavras-chave
- Balanceador de carga
- máquina de aprendizagem
- Marketplace
- Serviços de multimédia
- Mobile Cativação
- Serviços de Mobile
- multi a autenticação multifator
- notificação concentradores
- informações de operacionais avançados
- conjunto de gestão de operações
- powerapps
- Gestor de recuperação
- cache de redis
- RemoteApp
- gestão de direitos
- Programador
- pesquisa
- Centro de segurança
- serviço-bus
- serviço ferro
- recuperação de sites
- base de dados SQL
- armazém de dados SQL
- relatório de SQL
- armazenamento
- armazenar
- storsimple
- sequência de análise
- Gestor de tráfego
- virtual máquinas
- rede virtual
- visual-studio online
- VPN gateway
- Web sites

![](./media/article-metadata/checkmark-small.png)**documentationCenter**: obrigatório para artigos centrados em Dev Center melhor com todas as funcionalidades através do Centro de Dev Center. Especifique o único Dev Center do ou o idioma que aplica-se para o artigo. O valor que lista irá unidade o menu de trilhos navegação para a página. Artigos que especificar um valor de serviços e um valor de documentationCenter, o valor de serviços será unidade de navegação de trilho. Valores:

- **.NET**
- **nodejs**
- **Java**
- **PHP**
- **Python**
- **Rubi**
- **dispositivos móveis**: preterida. Substitua plataforma móvel específica.
- **IOS**: Verifing este valor novo
- **Android**: este novo valor a verificar
- **Windows**: este novo valor a verificar
- **xamarin**: este novo valor a verificar

![](./media/article-metadata/checkmark-small.png)**autores**: necessária, apenas um valor. A conta de GitHub para o autor principal ou o artigo PME da lista. Este atributo unidades pela linha no artigo do publicado. Liste apenas uma, apesar do nome do atributo plural.

![](./media/article-metadata/checkmark-small.png)**Gestor de**: necessários para se estão um contribuinte da Microsoft. Liste o alias de e-mail do Gestor de publicação de conteúdos para a área de tecnologia de apoio. Se for um contribuinte da Comunidade, incluir o atributo mas deixá-la vazia para que recomendamos o possam preencher.

![](./media/article-metadata/checkmark-small.png)**Editor**: não utilizado. Não utilize-lo para outras finalidades ().

![](./media/article-metadata/checkmark-small.png)**etiquetas**: opcional. Inclua apenas se pretende ativar uma ligação em navegação de trilho artigo para a página de índice remissivo artigo (http://azure.microsoft.com/documentation/articles/) a uma lista de artigos que correspondam a um dos valores aprovados prefiltered. Estes valores destinam-se para fornecer uma forma de agrupar conteúdo quando o agrupamento de conteúdo não é específico do serviço. Também podem fornecer seguintes tags rotular que indica a pilha de tecnologia que o artigo aplica-se ao. Este valor que **não** suporta etiquetas de formato livre ou hashtags; as etiquetas devem ser ativadas no site. Pode fornecer vários valores de etiquetas para um artigo, separados por vírgulas. Os valores aprovados são:

  - arquitetura
  - Gestor de recursos do Azure
  - gestão de serviços de Azure
  - Faturação
  - MySQL

![](./media/article-metadata/checkmark-small.png)**palavras-chave**: opcional. Para utilização por SEO champs apenas. Termos de separada com vírgulas. **Selecione com o seu âmbito SEO antes de alterar ou eliminar o conteúdo neste artigo que contém estes termos.** Este atributo registos palavras-chave o âmbito SEO tem atribuído e está a registar para melhorar a classificação de pesquisa. As palavras-chave não compostas no HTML publicado. Validação não requer este atributo.

## <a name="attributes-and-values-for-the-tags-section"></a>Atributos e valores para a secção de etiquetas

![](./media/article-metadata/checkmark-small.png)**MS.Service**: obrigatório. Especifica o serviço Azure, ferramenta ou funcionalidade que o artigo aplica-se ao. Um valor por página.

 Se uma página aplica-se ao vários serviços, selecione o serviço para que a maioria diretamente aplica-se; Por exemplo, um artigo que utiliza uma aplicação alojada em web sites para demonstrar a funcionalidade de serviço Bus deverá ter o valor de **serviço bus** em vez de **web sites**. Se uma página aplica-se ao vários serviços uniforme, selecione **várias**. Se uma página não se aplica a quaisquer serviços (esta será rara), selecione **Disp**.

 - **Active directory**
 - **ativo-diretório-b2c**
 - **ativo-diretório-ds**
 - **gestão de API**
 - **aplicação de serviço**: só se aplica a geral material conceptual na aplicação de serviço
 - **api do serviço aplicação**
 - **lógica de serviço de aplicação**
 - **aplicação de serviço-mobile**
 - **aplicação-serviço-web**
 - **informações de aplicação**
 - **gateway de aplicação**
 - **automatização**
 - **Gestor de recursos do Azure**
 - **segurança do Azure**
 - **pilha de Azure**
 - **cópia de segurança**
 - **batch**
 - **melhores práticas**
 - **Serviços de BizTalk**
 - **Faturação**
 - **cache**
 - **CDN**
 - **serviços em nuvem**
 - **catálogo de dados**
 - **arquivo de dados lake**
 - **análise de dados lake**
 - **laboratório de devtest**
 - **expressroute**
 - **hdinsight**
 - **Internet de elementos**
 - **concentrador IOT**
 - **Cofre de palavras-chave**
 - **máquina de aprendizagem**
 - **Marketplace**: artigos sobre o Azure marketplace
 - **Serviços de multimédia**
 - **Mobile Cativação**
 - **Serviços de Mobile**
 - **multi a autenticação multifator**
 - **múltiplos**: A página aplica-se ao vários serviços uniforme
 - **Disp**: A página não se aplica a quaisquer serviços (raros)
 - **notificação concentradores**
 - **informações de operacionais avançados**
 - **powerapps**
 - **Gestor de recuperação**
 - **cache de redis**
 - **RemoteApp**
 - **gestão de direitos**
 - **Programador**
 - **Centro de segurança**
 - **serviço-bus**
 - **serviço ferro**
 - **recuperação de sites**: serviços de recuperação anteriormente
 - **base de dados SQL**
 - **armazém de dados SQL**
 - **relatório de SQL**
 - **armazenamento**
 - **armazenar**: artigos sobre os serviços disponíveis através da loja do Azure
 - **storsimple**
 - **Gestor de tráfego**
 - **virtual máquinas**
 - **rede virtual**
 - **visual-studio online**
 - **VPN gateway**
 - **Web sites**

![](./media/article-metadata/checkmark-small.png)**MS.devlang**: obrigatório. Especifica a linguagem de programação o artigo aplica-se ao. Único valor por página.

 Se uma página aplica-se ao linguagens de programação duas uniforme, selecione **várias**. Se uma página é principalmente conceptual e o respetivo conteúdo é geralmente aplicável ao várias linguagens de programação, selecione **várias**. Se uma página não está atribuída a programadores e a aplicação de linguagem de programação não for relevante, selecione **Disp**. Utilize a **rest api** para identificar os tópicos de referência de REST API.

 - **cpp**
 - **DotNet**
 - **Java**
 - **JavaScript**
 - **múltiplos**: A página aplica-se a várias linguagens de programação uniforme.
 - **Disp**: A página não está a filtrar os programadores e não é específica a qualquer linguagens de programação.
 - **nodejs**
 - **objectivo-c**
 - **PHP**
 - **Python**
 - **REST api**
 - **Rubi**


![](./media/article-metadata/checkmark-small.png)**MS.topic**: obrigatório. Escreva o o tópico de detalhes. A maioria das novas páginas criadas por contribuintes serão artigo ou referência.

 - **artigo**: um tópico conceptual, tutorial, guia de funcionalidade ou outro artigo não referência

 - **página de campanha**: Azure.com apenas.  Uma página que é concebida especificamente como uma página de destino para campanhas externas e não está incluído como parte do site principal i a.  Não deve ser utilizada para artigos documentação ou documento normal páginas de destino.  Exemplos: azure.microsoft.com/develop/net/aspnet/; Azure.microsoft.com/develop/Mobile/IOS/

 - **Dev center-home page**: Azure.com apenas.  Um dev center home page do, por exemplo, / desenvolver/líquido /

 - **artigo Get iniciado**: atribuir para artigos que são apresentados na secção introdução ou descrição geral do painel de navegação esquerdo para um serviço.

 - **artigo herói**: um tutorial "herói" que foi concebido para fornecer uma introdução para um serviço ou funcionalidade que obtém os visitantes a começar a utilizar o serviço rapidamente e unidades de versão de avaliação livre desportivas e e MSDN. Atribuir este valor apenas para artigos que são apresentados na parte superior da página principal documentação do seu serviço.

 - **Home page**: home page da documentação de nível superior. Temos apenas dois: azure.microsoft.com/documentation/ e msdn.microsoft.com/library/azure/

 - **página de índice**: segundo nível, páginas para idiomas, services ou funcionalidades de programação de destino. Estes são distribuir por Azure.com e a biblioteca e são utilizados como pontos de entrada para obter informações mais específicas, âmbito. Exemplos: http://azure.microsoft.com/develop/mobile/resources-wp8/, http://msdn.microsoft.com/library/azure/jj673460.aspx, http://msdn.microsoft.com/library/azure/hh689864.aspx

 - **página infographic**: Azure.com apenas. Uma página que contém uma infographic navegável ou cartaz, por exemplo http://azure.microsoft.com/documentation/infographics/windows-azure/

 - **referência**: página de referência de uma API (incluindo REST API) ou página de referência do cmdlet do PowerShell

 - **página de base de serviço**: Azure.com apenas.  Uma documento home page do serviço, por exemplo, /documentation/services/virtual-machines /

 - **site da secção-home page**: Azure.com apenas. Uma "home page" para um determinado tipo de conteúdo no azure.com exemplos: http://azure.microsoft.com/documentation/infographics/, http://azure.microsoft.com/documentation/scripts/, http://azure.microsoft.com/documentation/videos/home/, http://azure.microsoft.com/downloads/

 - **página de vídeo**: Azure.com apenas.  Uma página que funcionalidades de um vídeo, por exemplo http://azure.microsoft.com/documentation/videos/azure-webjobs-hosting-testing-net/

![](./media/article-metadata/checkmark-small.png)**MS.tgt_pltfrm**: obrigatório. Especifica a plataforma de destino, por exemplo Windows, Linux, Windows Phone, iOS, Android ou plataformas de cache especial. Um valor por página. Este valor será **Disp** para a maioria dos tópicos exceto mobile e máquinas virtuais.

 - **cache na função**
 - **múltiplos de cache**
 - **cache redis**
 - **serviço de cache**
 - **partilhado em cache**
 - **comandos linha interface**
 - **Ibiza**: conteúdo que utiliza o portal de Ibiza. Utilize esta opção apenas nos casos onde a funcionalidade que está a ser abordada está disponível através do portal de Ibiza e o portal atual.
 - **telemóvel android**: Azure.com apenas momento
 - **Mobile html**: Azure.com apenas momento
 - **Mobile ios**: Azure.com apenas momento
 - **Mobile kindle**: Azure.com apenas momento
 - **múltiplos Mobile**
 - **Mobile-nokia-x**: Azure.com apenas momento
 - **Mobile phonegap**: Azure.com apenas momento
 - **Mobile sencha**: Azure.com apenas momento
 - **Mobile windows**: Azure.com apenas momento; Universal do Windows
 - **telemóvel windows**
 - **arquivo do windows Mobile**
 - **Mobile xamarin**: Azure.com apenas momento; Xamarin todas as plataformas
 - **Mobile-xamarin-android**: Azure.com apenas momento
 - **Mobile-xamarin-ios**: Azure.com apenas momento
 - **múltiplos**: A página aplica-se a múltiplas plataformas uniforme
 - **Disp**: um especificador de plataforma não é aplicável para esta página
 - **PowerShell**
 - **VM linux**
 - **múltiplos VM**
 - **VM windows**
 - **VM-windows-sharepoint**
 - **VM-windows-sql server**
 - **vs--introdução**: identifica o grupo de página Introdução ao VS. Sinalização adicionada 1/12/14.
 - **vs-que-aconteceu**: identifica a página VS introdução iniciado o que aconteceu. Sinalização adicionada 1/12/14.

![](./media/article-metadata/checkmark-small.png)**MS.workload**: obrigatório. Especifica a carga de trabalho Azure que se aplica a página. Um valor apenas por artigo.

**Atualizar 8/6/15** O valor de ms.workload está a ser mapeado por um xls, não o valor no ficheiro MD. O valor de ms.workload ainda é necessário para validação até que a funcionalidade pode ser atualizada. Agora está a ser agendado que funcionam.  Utilize **"disp"** como o valor por agora.

![](./media/article-metadata/checkmark-small.png)**MS.Date**: obrigatório. Especifica a data que o artigo última foi revisto para relevância, precisão, capturas de ecrã correto e hiperligações úteis. Introduza a data no formato dd/mm/aaaa. Esta data também aparece no artigo do publicado como a última data atualizada.

![](./media/article-metadata/checkmark-small.png)**MS.Author**: obrigatório. Especifica o es associados com o tópico. Relatórios internos (como última modificação da) utilizam este valor para associar a es à direita o artigo. Para especificar múltiplos valores devem separá-los por ponto e vírgula. Aliases da Microsoft ou endereços de e-mail completo são aceites. O comprimento não pode ter mais de 200 carateres.


###<a name="contributors-guide-links"></a>Ligações de guia dos colaboradores

- [Artigo Descrição geral](./../README.md)
- [Índice de artigos de orientação](./contributor-guide-index.md)


<!--Anchors-->
[Sintaxe]: #syntax
[Utilização]: #usage
[Atributos e valores para a secção de propriedades]: #attributes-and-values-for-the-properties-section
[Atributos e valores para a secção de etiquetas]: #attributes-and-values-for-the-tags-section
