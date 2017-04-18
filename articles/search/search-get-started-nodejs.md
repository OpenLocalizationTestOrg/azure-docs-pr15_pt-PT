<properties
    pageTitle="Introdução ao Azure pesquisa no NodeJS | Microsoft Azure | Serviço de pesquisa alojado na nuvem"
    description="Percorrer a criação de uma aplicação de pesquisa num serviço de pesquisa na nuvem alojado no Azure utilizando NodeJS como linguagem de programação."
    services="search"
    documentationCenter=""
    authors="EvanBoyle"
    manager="pablocas"
    editor="v-lincan"/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.date="07/14/2016"
    ms.author="evboyle"/>

# <a name="get-started-with-azure-search-in-nodejs"></a>Introdução ao Azure pesquisa no NodeJS
> [AZURE.SELECTOR]
- [Portal](search-get-started-portal.md)
- [.NET](search-howto-dotnet-sdk.md)

Saiba como criar uma aplicação de pesquisa NodeJS personalizada que utiliza Azure a pesquisa para a sua experiência de pesquisa. Neste tutorial utiliza a [REST API do serviço de pesquisa do Azure](https://msdn.microsoft.com/library/dn798935.aspx) para construir os objetos e operações utilizadas neste exercício.

Utilizámos [NodeJS](https://nodejs.org) e NPM, [Sublime 3 de texto](http://www.sublimetext.com/3)e o Windows PowerShell no Windows 8.1 para desenvolver e testar este código.

Para executar este exemplo, tem de ter um serviço de pesquisa do Azure, pode inscrever no [Portal do Azure](https://portal.azure.com). Consulte o artigo [criar um serviço de pesquisa do Azure no portal](search-create-service-portal.md) para obter instruções passo a passo.

## <a name="about-the-data"></a>Acerca dos dados

Esta aplicação exemplo utiliza os dados a partir de [Estados Unidos geológicas serviços (USG)](http://geonames.usgs.gov/domestic/download_data.htm), filtrado no estado da ilha Rhode para reduzir o tamanho do conjunto de dados. Vamos utilizar estes dados para criar uma aplicação de pesquisa devolve edifícios marco como hospitais e escolas, bem como funcionalidades geológicas como sequências, lagos e cimeiras.

Esta aplicação, o programa **DataIndexer** cria e carrega o índice utilizando uma construção de [indexador](https://msdn.microsoft.com/library/azure/dn798918.aspx) obter o conjunto de dados filtrado USG a partir de uma base de dados do SQL Azure público. Credenciais e ligação informações à origem de dados online são fornecidas no código de programa. Nenhuma configuração mais é necessária.

> [AZURE.NOTE] Vamos aplicado um filtro neste conjunto de dados para se manter abaixo do limite de 10.000 documento da camada comparar gratuito. Se utilizar a camada padrão, este limite não se aplica. Para obter detalhes sobre capacidade para cada camada comparar, consulte o artigo [limites de serviço de pesquisa](search-limits-quotas-capacity.md).


<a id="sub-2"></a>
## <a name="find-the-service-name-and-api-key-of-your-azure-search-service"></a>Localizar o nome de serviço e chave de api do seu serviço de pesquisa do Azure

Depois de criar o serviço, regresse ao portal para obter o URL ou `api-key`. Ligações para o serviço de pesquisa pedir que tenha tanto o URL e um `api-key` para autenticar a chamada.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Na barra de atalhos, clique em **serviço de pesquisa** para listar todos os serviços de pesquisa do Azure aprovisionados para a sua subscrição.
3. Selecione o serviço que pretende utilizar.
4. No dashboard de serviço, verá os mosaicos para as informações essenciais e o ícone de chave para aceder às teclas de administrador.

    ![][3]

5. Copie o URL do serviço, uma tecla de administrador e uma chave de consulta. Terá de todos os três mais tarde, quando adicioná-los para o ficheiro config.js.

## <a name="download-the-sample-files"></a>Transferir os ficheiros de exemplo

Utilize qualquer uma das seguintes abordagens para transferir a amostra.

1. Aceda a [AzureSearchNodeJSIndexerDemo](https://github.com/AzureSearch/AzureSearchNodeJSIndexerDemo).
2. Clique em **Transferir ZIP**, guardar o ficheiro. zip e, em seguida, extraia todos os ficheiros que contém.

Todas as modificações ficheiro subsequentes e executar declarações serão feitas contra ficheiros nesta pasta.


## <a name="update-the-configjs-with-your-search-service-url-and-api-key"></a>Atualize o config.js. com o seu URL do serviço de pesquisa e a chave de api

Utilizar o URL e api-chave que copiou anteriormente, especifique o URL, administração-chave e chave de consulta no ficheiro de configuração.

Teclas de administração conceder controlo total sobre operações de serviço, incluindo criar ou eliminar um índice remissivo e carregar documentos. Em contrapartida, as teclas de consulta são só de leitura para operações de, normalmente utilizadas por aplicações de cliente que se ligam ao Azure pesquisa.

Neste exemplo, vamos inclui a chave de consulta para o ajudar a reforçar a melhor prática de utilizar a chave de consulta em aplicações de cliente.

A seguinte captura de ecrã mostra **config.js** aberto num texto editor, com as entradas relevantes demarcadas para que possa ver onde pretende actualizar o ficheiro com os valores que são válidos para o seu serviço de pesquisa.

![][5]


## <a name="host-a-runtime-environment-for-the-sample"></a>Anfitrião um ambiente do runtime para a amostra

O exemplo requer um servidor HTTP, que pode instalar globalmente utilizando npm.

Utilize uma janela do PowerShell para os seguintes comandos.

1. Navegue para a pasta que contém o ficheiro **package.json** .
2. Tipo de `npm install`.
2. Tipo de `npm install -g http-server`.

## <a name="build-the-index-and-run-the-application"></a>Construir o índice remissivo e executar a aplicação

1. Tipo de `npm run indexDocuments`.
2. Tipo de `npm run build`.
3. Tipo de `npm run start_server`.
4. Direcionar o seu browser, em`http://localhost:8080/index.html`

## <a name="search-on-usgs-data"></a>Procurar dados USGS

O conjunto de dados USG inclui registos que são importantes para o estado da Rhode ilha. Se clicar em **pesquisa** com uma caixa de pesquisa vazia, obterá as entradas de 50 superiores, que é a predefinição.

Introduzir um termo de pesquisa irá dar-motor de busca algo para ir para no. Tente introduzir um nome regional. "Roger Williams" foi a primeira Governador de Rhode ilha. Vários parques, edifícios e escolas são o nome da contactá-la.

![][9]

Também pode tentar qualquer um destes termos:

- Pawtucket
- Pembroke
- Ganso + cabo


## <a name="next-steps"></a>Próximos passos

Este é o primeiro tutorial do Azure pesquisa com base em NodeJS e o conjunto de dados USG. Ao longo do tempo, irá Expandimos este tutorial para ficar demonstram funcionalidades de pesquisa adicionais que poderá querer utilizar no seu soluções personalizadas.

Se já tiver alguns fundo na pesquisa Azure, pode utilizar este exemplo como um springboard para tentar suggesters (limitam- ou de conclusão automática de consultas), filtros e navegação por facetas. Também pode melhorar relativamente a página de resultados de pesquisa ao adicionar contagens e documentos de lotes para que os utilizadores podem percorrer os resultados.

Novo no Azure pesquisa? Recomendamos que está a tentar outros tutoriais desenvolver compreender o que pode criar. Visite a nossa [página de documentação](https://azure.microsoft.com/documentation/services/search/) para obter mais recursos. Também pode ver as ligações no nosso [vídeo e lista de iniciação](search-video-demo-tutorial-list.md) para aceder a mais informações.

<!--Image references-->
[1]: ./media/search-get-started-nodejs/create-search-portal-1.PNG
[2]: ./media/search-get-started-nodejs/create-search-portal-2.PNG
[3]: ./media/search-get-started-nodejs/create-search-portal-3.PNG
[5]: ./media/search-get-started-nodejs/AzSearch-NodeJS-configjs.png
[9]: ./media/search-get-started-nodejs/rogerwilliamsschool.png
