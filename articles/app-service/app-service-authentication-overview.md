<properties
    pageTitle="Autenticação e autorização na aplicação de serviço de Azure | Microsoft Azure"
    description="Descrição geral da autenticação e de referência conceptual / autorização de funcionalidades do serviço de aplicação do Azure"
    services="app-service"
    documentationCenter=""
    authors="mattchenderson"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="mahender"/>

# <a name="authentication-and-authorization-in-azure-app-service"></a>Autenticação e autorização na aplicação de serviço do Azure

## <a name="what-is-app-service-authentication--authorization"></a>O que é a aplicação de serviço de autenticação / autorização?

Autenticação do serviço de aplicação / autorização é uma funcionalidade que fornece uma forma para a sua aplicação iniciar sessão no utilizadores para que não tem de alterar código numa aplicação back-end. Fornece uma forma fácil de proteger a aplicação e trabalhar com dados de por utilizador.

Aplicação de serviço utiliza identidade federada, na qual um fornecedor de identidade de terceiros armazena contas e autentica utilizadores. A aplicação baseia-se nas informações de identidade do fornecedor para que não tem a aplicação armazenar essas informações própria. Suporta a aplicação de serviço de cinco fornecedores de identidade terminar a caixa: Azure Active Directory, Facebook, Google, Account Microsoft e Twitter. A aplicação pode utilizar qualquer número destes fornecedores de identidades para disponibilizar aos seus utilizadores com opções como de iniciar sessão. Para expandir o suporte técnico da incorporados, pode integrar o outro fornecedor de identidades ou [uma solução de identidade personalizada própria][custom-auth].

Se pretender começar imediatamente, consulte um dos seguintes tutoriais:

- [Adicionar autenticação para a sua aplicação iOS] [ iOS] (ou [Android], [Windows], [Xamarin.iOS], [Xamarin.Android], [Xamarin.Forms]ou [Cordova])
- [Autenticação de utilizador para as aplicações de API na aplicação de serviço do Azure][apia-user]
- [Introdução ao serviço de aplicação do Azure - parte 2][web-getstarted]

## <a name="how-authentication-works-in-app-service"></a>Como funciona o autenticação no serviço de aplicação

Para autenticar utilizando um dos fornecedores de identidade, primeiro tem de configurar o fornecedor de identidade saber sobre a sua aplicação. O fornecedor de identidade, em seguida, irá fornecer IDs e segredos que fornece a aplicação de serviço. Este procedimento conclui a relação de confiança para que a aplicação de serviço pode validar declarações de utilizador, tal como tokens de autenticação a partir do fornecedor de identidade.

Para iniciar a sessão do utilizador ao utilizar um destes fornecedores, o utilizador tem de ser redirecionado para um ponto final que inicia a sessão utilizadores por esse fornecedor. Se os clientes estiverem a utilizar um browser, pode ter aplicação de serviço de direcionar automaticamente todos os utilizadores não autenticados para o ponto final que inicia sessão no utilizadores. Caso contrário, terá de direcionar os seus clientes para `{your App Service base URL}/.auth/login/<provider>`, onde `<provider>` é um dos seguintes valores: aad, facebook, google, microsoft ou twitter. Cenários Mobile e API são explicados nas secções neste artigo.

Os utilizadores que interagem com a aplicação através de um browser web terá um cookie definido para que pode permaneça autenticados à medida que navegam a aplicação. Para outros tipos de cliente, como o telemóvel, um token de web JSON (JWT), que devem ser apresentado na `X-ZUMO-AUTH` cabeçalho, será emitida para o cliente. O cliente de aplicações Mobile SDK processará isto por si. Em alternativa, um token de identidade do Azure Active Directory ou token de acesso pode ser diretamente incluído na `Authorization` cabeçalho, tal como uma [ligação OAuth](https://tools.ietf.org/html/rfc6750).

Aplicação de serviço irá validar qualquer cookie ou token de que a aplicação de problemas para autenticar os utilizadores. Para restringir quem pode aceder a sua aplicação, consulte a secção de [autorização](#authorization) neste artigo.

### <a name="mobile-authentication-with-a-provider-sdk"></a>Autenticação móvel com um fornecedor SDK

Depois de tudo o que é configurado no back-end, pode modificar os clientes móveis para iniciar sessão com a aplicação de serviço. Existem duas abordagens aqui:

- Utilize um SDK do que um fornecedor de identidade fornecida publica para estabelecer identidade e, em seguida, aceder à aplicação de serviço.
- Utilize uma única linha de código para que o cliente de aplicações Mobile SDK pode iniciar sessão utilizadores.

>[AZURE.TIP] A maioria das aplicações deve utilizar um fornecedor SDK para obter uma experiência mais consistente quando os utilizadores iniciar sessão, para utilizar o suporte de atualização e para obter outros benefícios que especifica o fornecedor.

Quando utiliza um fornecedor SDK, os utilizadores podem iniciar sessão para uma experiência de que integra-se mais intimamente com o sistema operativo que a aplicação está em execução no. Isto também dá-lhe um token de fornecedor e algumas informações do utilizador no cliente, que facilita muito de consumir graph APIs e personalizar a experiência do utilizador. Ocasionalmente, blogues e fóruns, verá esta conhecida como "fluxo do cliente" ou "fluxo direcionado cliente" porque sinais de código do cliente em utilizadores e o código do cliente tem acesso a um token de fornecedor.

Depois de um token de fornecedor é obtido, necessita de ser enviadas para a aplicação de serviço para validação. Após a aplicação de serviço de validar o token, o serviço de aplicação cria um novo token de aplicação de serviço, que é devolvido para o cliente. O cliente de aplicações Mobile SDK tem métodos helper para gerir este exchange e automaticamente anexar o token a todos os pedidos para o aplicação back-end. Os programadores também podem manter uma referência ao token de fornecedor se, por isso selecione.

### <a name="mobile-authentication-without-a-provider-sdk"></a>Autenticação móvel sem um fornecedor SDK

Se não pretender configurar um fornecedor SDK, pode permitir que a funcionalidade de aplicações móveis do Azure Service de aplicação para iniciar sessão para si. O cliente de aplicações Mobile SDK irá abrir uma vista da web para o fornecedor da sua escolha e o utilizador de início de sessão. Ocasionalmente, blogues e fóruns, verá esta conhecida como a "fluxo de servidor" ou "fluxo direcionado servidor" porque o servidor gere o processo de sinais de em utilizadores e o cliente SDK nunca recebe o token de fornecedor.

Código para iniciar este fluxo é incluído no tutorial autenticação para cada plataforma. No final do fluxo da, o cliente SDK tem um token de aplicação de serviço e o token automaticamente é anexado às todos os pedidos para o aplicação back-end.

### <a name="service-to-service-authentication"></a>Serviço de serviço de autenticação

Apesar de lhe conceder acesso a utilizadores à sua aplicação, também pode confiar outra aplicação para o seu próprio API de chamadas. Por exemplo, poderia ter uma aplicação web chamar uma API noutra aplicação web. Neste cenário, utilize as credenciais para uma conta de serviço em vez de credenciais de utilizador para obter um token. Uma conta de serviço também conhecido como é um *serviço principal* no terminologia do Azure Active Directory e autenticação que utiliza essa conta também conhecido como é um cenário de serviço de serviço.

>[AZURE.IMPORTANT] Uma vez que aplicações móveis executadas em dispositivos do cliente, aplicações móveis faça _não_ contar como fidedignos aplicações e não deve utilizar um fluxo principal de serviço. Em vez disso, devem utilizar um fluxo de utilizador que foi detalhado anterior.

Cenários de serviço de serviço, o serviço de aplicação pode proteger a aplicação utilizando o Azure Active Directory. A aplicação de chamada apenas tem de fornecer um token de autorização principal de serviço do Azure Active Directory é obtido ao fornecer o cliente de ID e cliente secreta a partir do Azure Active Directory. Um exemplo deste cenário que utiliza as aplicações do ASP.NET API é explicado pela tutorial, [autenticação principal do serviço para as aplicações de API][apia-service].

Se pretender utilizar a autenticação de aplicação de serviço para gerir um cenário de serviço de serviço, pode utilizar certificados de cliente ou de autenticação básica. Para obter informações sobre os certificados de cliente no Azure, consulte o artigo [Como para configurar comum autenticação TLS para Web Apps](../app-service-web/app-service-web-configure-tls-mutual-auth.md). Para obter informações sobre a autenticação básica no ASP.NET, consulte o artigo [Filtros de autenticação ASP.NET Web API 2](http://www.asp.net/web-api/overview/security/authentication-filters).

Autenticação da conta serviço a partir de uma aplicação de lógica de aplicação de serviço para uma aplicação de API é um caso especial que é indicado na [utilizar a API personalizado alojado no serviço de aplicação com aplicações de lógica](../app-service-logic/app-service-logic-custom-hosted-api.md).

## <a name="authorization"></a>Como funciona o autorização no serviço de aplicação

Tem controlo total sobre os pedidos de que pode aceder a sua aplicação. Autenticação do serviço de aplicação / autorização pode ser configurada com qualquer um dos seguintes comportamentos:

- Permitir apenas autenticados pedidos alcançar a sua aplicação.

    Se um browser receber um pedido anónimo, serviço de aplicação irá redirecioná-se a uma página para o fornecedor de identidade que escolher para que os utilizadores possam iniciar sessão. Se o pedido provêm de um dispositivo móvel, a resposta devolvida é uma resposta HTTP _401 não autorizado_ .

    Com esta opção, não precisa de escrever um código de autenticação de todo na sua aplicação. Se precisar de autorização melhor, informações sobre o utilizador estão disponíveis para o seu código.

- Permitir que todos os pedidos chegar a sua aplicação, mas validar pedidos autenticados e passar ao longo de informações de autenticação nos cabeçalhos de HTTP.

    Esta opção adia utilizado decisões de autorização para o seu código da aplicação. Fornece mais flexibilidade no processamento de pedidos anónimos, mas tem de escrever código.

- Permitir que todos os pedidos alcançar a aplicação e, sem agir sobre informações de autenticação nos pedidos.

    Neste caso, a autenticação / funcionalidade de autorização está desativada. As tarefas de autenticação e autorização são totalmente por excesso para o seu código da aplicação.

Os comportamentos anterior são controlados pela opção de **ação a tomar quando pedido não é autenticado** no portal do Azure. Se optar por * *Iniciar sessão com *o nome do fornecedor* **, todos os pedidos de tem de ser autenticadas.** Permitir que o pedido (nenhuma acção) * * adia utilizado a decisão de autorização para o seu código, mas ainda fornece informações de autenticação. Se pretender que o seu código processar tudo, pode desativar a autenticação / funcionalidade de autorização.

## <a name="working-with-user-identities-in-your-application"></a>Trabalhar com identidades dos utilizadores na sua aplicação

Aplicação de serviço transmite algumas informações do utilizador para a sua aplicação utilizando cabeçalhos especiais. Pedidos de externos proibi-estes cabeçalhos e será apenas se apresentar definido pela aplicação do serviço de autenticação / autorização. Alguns cabeçalhos de exemplo incluem:

* X-MS-CLIENTE-CAPITAL-NAME
* X-MS-CLIENTE-CAPITAL-ID
* X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN
* X-MS-TOKEN-FACEBOOK-EXPIRES-ON

Código escrito qualquer idioma ou framework pode obter as informações que necessita destes cabeçalhos. Para as aplicações do ASP.NET 4.6, o **ClaimsPrincipal** é definido automaticamente com os valores adequados.

A aplicação também pode obter os detalhes do utilizador adicionais através de um HTTP GET na `/.auth/me` ponto final da sua aplicação. Um token válido, que é incluído com o pedido irá devolver uma carga útil JSON com detalhes sobre o fornecedor de que está a ser utilizado, o token de fornecedor subjacentes e outras informações de utilizador. O servidor de aplicações Mobile SDK fornecem métodos de helper para trabalhar com estes dados. Para mais informações, consulte o artigo [como utilizar o Azure Mobile aplicações Node.js SDK](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity)e [trabalhar com o servidor de back-end .NET SDK para as aplicações móveis do Azure](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info).

## <a name="documentation-and-additional-resources"></a>Documentação e recursos adicionais

### <a name="identity-providers"></a>Fornecedores de identidade
Os seguintes tutoriais mostram como configurar a aplicação de serviço para utilizar fornecedores de autenticação diferente:

- [Como configurar a sua aplicação para utilizar o início de sessão do Azure Active Directory][AAD]
- [Como configurar a sua aplicação para utilizar o início de sessão do Facebook][Facebook]
- [Como configurar a sua aplicação para utilizar o início de sessão do Google][Google]
- [Como configurar a sua aplicação para utilizar o início de sessão do Microsoft Account][MSA]
- [Como configurar a sua aplicação para utilizar o início de sessão do Twitter][Twitter]

Se quiser utilizar um sistema de identidade que não seja os esquemas fornecidos aqui, também pode utilizar a [Pré-visualizar suporte de autenticação personalizada no servidor Mobile aplicações .NET SDK][custom-auth], que podem ser utilizado em aplicações web, aplicações móveis ou API aplicações.

### <a name="web-applications"></a>Aplicações Web
Os seguintes tutoriais mostram como adicionar autenticação para uma aplicação web:

- [Introdução ao serviço de aplicação do Azure - parte 2][web-getstarted]

### <a name="mobile-applications"></a>Aplicações móveis
Os seguintes tutoriais mostram como adicionar autenticação para os seus clientes móveis utilizando o fluxo direcionado server:

- [Adicionar autenticação para a sua aplicação iOS][iOS]
- [Adicionar a autenticação para a sua aplicação Android] [Android]
- [Adicionar a autenticação para a sua aplicação do Windows] [Windows]
- [Adicionar a autenticação para a sua aplicação Xamarin.iOS] [Xamarin.iOS]
- [Adicionar a autenticação para a sua aplicação Xamarin.Android] [Xamarin.Android]
- [Adicionar a autenticação para a sua aplicação Xamarin.Forms] [Xamarin.Forms]
- [Adicionar a autenticação para a sua aplicação Cordova] [Cordova]

Se pretender utilizar o fluxo de cliente-direcionado para o Azure Active Directory, utilize os seguintes recursos:

- [Utilizar a biblioteca de autenticação do Active Directory para iOS][ADAL-iOS]
- [Utilizar a biblioteca de autenticação do Active Directory para Android][ADAL-Android]
- [Utilizar a biblioteca de autenticação do Active Directory para Windows e Xamarin][ADAL-dotnet]

Se pretender utilizar o fluxo de cliente-direcionado para o Facebook, utilize os seguintes recursos:

- [Utilizar o SDK do Facebook para iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

Se pretender utilizar o fluxo direcionado cliente para Twitter, utilize os seguintes recursos:

- [Utilizar ferro Twitter para iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

Se pretender utilizar o fluxo de cliente-direcionado para o Google, utilize os seguintes recursos:

- [Utilizar o SDK do início de sessão no Google para iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

### <a name="api-applications"></a>Aplicações de API
Os seguintes tutoriais mostram como pode proteger as suas aplicações API:

- [Autenticação de utilizador para as aplicações de API na aplicação de serviço do Azure][apia-user]
- [Autenticação principal do serviço para as aplicações de API na aplicação de serviço do Azure][apia-service]









[apia-user]: ../app-service-api/app-service-api-dotnet-user-principal-auth.md
[apia-service]: ../app-service-api/app-service-api-dotnet-service-principal-auth.md

[web-getstarted]: ../app-service-web/app-service-web-get-started-2.md#authenticate-your-users

[iOS]: ../app-service-mobile/app-service-mobile-ios-get-started-users.md
[Android]: ../app-service-mobile/app-service-mobile-android-get-started-users.md
[Xamarin.iOS]: ../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md
[Xamarin.Android]: ../app-service-mobile/app-service-mobile-xamarin-android-get-started-users.md
[Xamarin.Forms]: ../app-service-mobile/app-service-mobile-xamarin-forms-get-started-users.md
[Windows]: ../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md
[Cordova]: ../app-service-mobile/app-service-mobile-cordova-get-started-users.md

[AAD]: ../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md
[Facebook]: ../app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication.md
[Google]: ../app-service-mobile/app-service-mobile-how-to-configure-google-authentication.md
[MSA]: ../app-service-mobile/app-service-mobile-how-to-configure-microsoft-authentication.md
[Twitter]: ../app-service-mobile/app-service-mobile-how-to-configure-twitter-authentication.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
