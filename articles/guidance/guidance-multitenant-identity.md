<properties
   pageTitle="Gestão de identidades para aplicações multi-inquilino | Microsoft Azure"
   description="Práticas recomendadas para gestão de autenticação, autorização e identidade nas aplicações multi-inquilino."
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/02/2016"
   ms.author="mwasson"/>

# <a name="identity-management-for-multitenant-applications-in-microsoft-azure"></a>Gestão de identidades para aplicações multi-inquilino no Microsoft Azure

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Nesta série descreve melhores práticas para multitenancy, quando utilizar o Azure AD para gestão de autenticação e de identidade.

Quando estiver a criar uma aplicação do multi-inquilino, uma dos desafios primeiros está a gerir identidades do utilizador, uma vez que agora cada utilizador pertence a um inquilino. Por exemplo:

- Os utilizadores que inicie sessão com as respetivas credenciais organizacionais.
- Os utilizadores devem ter acesso a dados da sua organização, mas não os dados que pertence a outros inquilinos.
- Uma organização pode inscrever-se para a aplicação e, em seguida, atribuir funções de aplicação aos seus membros.

Azure Active Directory (Azure AD) tem algumas funcionalidades fantásticas que suportam todos os destes cenários.

Que acompanham esta série de artigos, criámos também uma completa, [implementação de ponto a ponto] [ tailspin] de uma aplicação do multi-inquilino. Os artigos refletem o podemos aprendeu processo de criação da aplicação. Para começar a com a aplicação, consulte o artigo [executar a aplicação de inquéritos](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md).

Eis a lista de artigos nesta série:

- [Introdução à gestão de identidades para aplicações multi-inquilino](guidance-multitenant-identity-intro.md)
- [Informações sobre a aplicação de brinquedos inquéritos](guidance-multitenant-identity-tailspin.md)
- [Autenticação utilizando o Azure AD e ligue OpenID](guidance-multitenant-identity-authenticate.md)
- [Trabalhar com identidades baseada em afirmações](guidance-multitenant-identity-claims.md)
- [Inscrição e ativação de inquilinos](guidance-multitenant-identity-signup.md)
- [Funções da aplicação](guidance-multitenant-identity-app-roles.md)
- [Autorização baseadas no recurso e baseado em funções](guidance-multitenant-identity-authorize.md)
- [Proteger uma back-end da web API](guidance-multitenant-identity-web-api.md)
- [Colocação em cache oauth2 ainda tokens de acesso](guidance-multitenant-identity-token-cache.md)
- [Federar com AD FS um cliente para as aplicações multi-inquilino no Azure](guidance-multitenant-identity-adfs.md)
- [Utilizar declaração do cliente para obter tokens de acesso a partir do Azure AD](guidance-multitenant-identity-client-assertion.md)
- [Utiliza cofre chave para proteger segredos de aplicação](guidance-multitenant-identity-keyvault.md)

[tailspin]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps
