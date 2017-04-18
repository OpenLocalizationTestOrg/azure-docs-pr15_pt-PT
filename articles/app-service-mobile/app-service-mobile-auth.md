<properties
    pageTitle="Autenticação e autorização em aplicações móveis Azure | Microsoft Azure"
    description="Descrição geral da autenticação e de referência conceptual / autorização de funcionalidades para as aplicações móveis do Azure"
    services="app-service\mobile"
    documentationCenter=""
    authors="mattchenderson"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="mahender"/>

# <a name="authentication-and-authorization-in-azure-mobile-apps"></a>Autenticação e autorização em aplicações móveis Azure

## <a name="what-is-app-service-authentication--authorization"></a>O que é a aplicação de serviço de autenticação / autorização?

> [AZURE.NOTE] Este tópico será migrado para consolidada [autenticação do serviço de aplicação / autorização](../app-service/app-service-authentication-overview.md) tópico que abrange Web, Mobile e API Apps.

Autenticação do serviço de aplicação / autorização é uma funcionalidade que permite que a aplicação iniciar sessão em utilizadores com sem alterações de código necessárias na aplicação back-end. Fornece uma forma fácil de proteger a aplicação e trabalhar com dados de por utilizador.

Aplicação de serviço utiliza identidade federada, na qual um 3- **fornecedor de identidades** ("IDP") armazena contas e autentica utilizadores, e a aplicação utiliza esta identidade em vez das suas próprias. Suporta a aplicação de serviço de cinco fornecedores de identidade terminar a caixa: _Azure Active Directory_, _Facebook_, _Google_, _Conta Microsoft_e _Twitter_. Também pode expandir este suporte para as suas aplicações através da integração noutro fornecedor de identidade ou o seu próprio solução de identidade personalizada.

A aplicação, pode tirar partido qualquer número destes fornecedores de identidade, para que possa fornecer os seus utilizadores finais com opções como sessão.

Se pretender começar imediatamente, consulte um dos seguintes tutoriais:

- [Adicionar autenticação para a sua aplicação iOS]
- [Adicionar autenticação para a sua aplicação Xamarin.iOS]
- [Adicionar autenticação para a sua aplicação Xamarin.Android]
- [Adicionar autenticação para a sua aplicação do Windows]

## <a name="how-authentication-works"></a>Como funciona a autenticação

Para autenticar utilizando um dos fornecedores de identidade, primeiro tem de configurar o fornecedor de identidade saber sobre a sua aplicação. O fornecedor de identidade, em seguida, irá fornecer IDs e segredos que fornecem novamente para a aplicação. Isto conclui a relação de confiança e permite que o serviço de aplicação validar identidades fornecidas para a mesma.

Estes passos encontram detalhadas mais os seguintes tópicos:

- [Como configurar a sua aplicação para utilizar o início de sessão do Azure Active Directory]
- [Como configurar a sua aplicação para utilizar o início de sessão do Facebook]
- [Como configurar a sua aplicação para utilizar o início de sessão do Google]
- [Como configurar a sua aplicação para utilizar o início de sessão do Microsoft Account]
- [Como configurar a sua aplicação para utilizar o início de sessão do Twitter]

Depois de tudo o que é configurado no back-end, pode modificar o seu cliente para iniciar sessão. Existem duas abordagens aqui:

- Utilizar uma única linha de código, permitir que as aplicações móveis cliente SDK início de sessão utilizadores.
- Tirar partido um SDK publicado por um fornecedor de identidade fornecida para estabelecer identidade e, em seguida, aceder à aplicação de serviço.

>[AZURE.TIP] A maioria das aplicações deve utilizar um fornecedor SDK para obter uma experiência de início de sessão mais nativo sentimento e tirar partido de suporte de atualização e outros benefícios de específicas do fornecedor.

### <a name="how-authentication-without-a-provider-sdk-works"></a>Como funciona a autenticação sem um fornecedor SDK

Se não pretender configurar um fornecedor SDK, pode permitir que aplicações móveis executar o início de sessão para si. O cliente de aplicações Mobile SDK irá abrir uma vista da web para o fornecedor da sua escolha e concluir o início de sessão. Ativar, ocasionalmente, blogues e fóruns, irá ver esta conhecida como "fluxo servidor" ou "fluxo direcionado servidor" desde que o servidor está a gerir o início de sessão e o cliente SDK nunca recebe o token de fornecedor.

O código necessário para iniciar este fluxo é abrangido no tutorial autenticação para cada plataforma. No final do fluxo da, o cliente SDK tem um token de aplicação de serviço e o token automaticamente é anexado às todos os pedidos para o back-end.

### <a name="how-authentication-with-a-provider-sdk-works"></a>Como funciona a autenticação com um fornecedor SDK

Trabalhar com um fornecedor SDK permite a experiência de início de sessão, mais intimamente interagir com a plataforma SO que a aplicação está em execução. Isto também dá-lhe um token de fornecedor e algumas informações do utilizador no cliente, que facilita muito de consumir graph APIs e personalizar a experiência do utilizador. Ocasionalmente, blogues e fóruns verá esta conhecida como "fluxo do cliente" ou "direcionado de cliente fluxo" desde o código do cliente está a processar o início de sessão e o código do cliente tem acesso a um token de fornecedor.

Assim que um token de fornecedor é obtido, necessita de ser enviadas para a aplicação de serviço para validação. No final do fluxo da, o cliente SDK tem um token de aplicação de serviço e o token automaticamente é anexado às todos os pedidos para o back-end. O programador também pode manter uma referência ao token de fornecedor se, por isso selecione.

## <a name="how-authorization-works"></a>Como funciona a autorização

Autenticação do serviço de aplicação / autorização expõe várias opções para a **ação a tomar quando não é autenticado pedido**. Antes do seu código recebe um pedido determinado, pode ter verificação da aplicação de serviço para ver se o pedido é autenticado e se não, rejeitar e tentar para que o utilizador iniciar sessão no antes de tentar novamente.

Uma das opções é ter não autenticados redirecionar pedidos para um dos fornecedores de identidade. Num browser, este procedimento seria demorar realmente ao utilizador para uma nova página. No entanto, o cliente móvel não é possível redirecionado desta forma e respostas não autenticadas receberão uma resposta HTTP _401 não autorizado_ . Dada isto, o primeiro pedido que torna o cliente deve estar sempre para o ponto final de início de sessão e, em seguida, que pode efetuar chamadas para qualquer outras APIs. Se tentar para ligar a outra API antes de início de sessão, o cliente vai receber um erro.

Se pretender ter mais granular controlo sobre quais os pontos finais precisam de autenticação, também pode escolher "nenhuma ação (Permitir pedido)" para pedidos de não autenticados. Neste caso, todas as decisões de autenticação são adiadas para o seu código da aplicação. Isto também permite-lhe permitir o acesso a utilizadores específicos com base em regras de autorização personalizado.

## <a name="documentation"></a>Documentação

Os seguintes tutoriais mostram como adicionar autenticação para os seus clientes móveis, utilizando a aplicação de serviço:

- [Adicionar autenticação para a sua aplicação iOS]
- [Adicionar autenticação para a sua aplicação Xamarin.iOS]
- [Adicionar autenticação para a sua aplicação Xamarin.Android]
- [Adicionar autenticação para a sua aplicação do Windows]

Os seguintes tutoriais mostram como configurar o serviço de aplicação para tirar partido de fornecedores de autenticação diferente:

- [Como configurar a sua aplicação para utilizar o início de sessão do Azure Active Directory]
- [Como configurar a sua aplicação para utilizar o início de sessão do Facebook]
- [Como configurar a sua aplicação para utilizar o início de sessão do Google]
- [Como configurar a sua aplicação para utilizar o início de sessão do Microsoft Account]
- [Como configurar a sua aplicação para utilizar o início de sessão do Twitter]

Se pretender utilizar um sistema de identidade que não seja os esquemas fornecidos aqui, pode também tirar partido de [suporte para autenticação personalizada no servidor .NET SDK de pré-visualizar](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth).

[Adicionar autenticação para a sua aplicação iOS]: app-service-mobile-ios-get-started-users.md
[Adicionar autenticação para a sua aplicação Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started-users.md
[Adicionar autenticação para a sua aplicação Xamarin.Android]: app-service-mobile-xamarin-android-get-started-users.md
[Adicionar autenticação para a sua aplicação do Windows]: app-service-mobile-windows-store-dotnet-get-started-users.md

[Como configurar a sua aplicação para utilizar o início de sessão do Azure Active Directory]: app-service-mobile-how-to-configure-active-directory-authentication.md
[Como configurar a sua aplicação para utilizar o início de sessão do Facebook]: app-service-mobile-how-to-configure-facebook-authentication.md
[Como configurar a sua aplicação para utilizar o início de sessão do Google]: app-service-mobile-how-to-configure-google-authentication.md
[Como configurar a sua aplicação para utilizar o início de sessão do Microsoft Account]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Como configurar a sua aplicação para utilizar o início de sessão do Twitter]: app-service-mobile-how-to-configure-twitter-authentication.md
