<properties
    pageTitle="Descrição geral de ponto final 2.0 | Microsoft Azure"
    description="Uma introdução à criação de aplicações com Account Microsoft e Azure Active Directory iniciar sessão."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="dastrock"/>

# <a name="sign-in-microsoft-account--azure-ad-users-in-a-single-app"></a>Início de sessão no Account Microsoft e os utilizadores do Azure AD numa única aplicação

No passado, um programador de aplicação que pretendia para suportar tanto as contas do Microsoft Azure Active Directory tiver sido necessário para integrar com dois sistemas em separado.  Vamos agora tenha introduzido uma nova versão do API de autenticação que permite-lhe iniciar sessão em utilizadores com os dois tipos de contas utilizando o sistema de Azure AD.  Este sistema de autenticação convergentes é conhecido como **o ponto final 2.0**.  Com o ponto final 2.0, um integração simples permite-lhe alcançar uma audiência que abrange milhões de utilizadores com contas pessoais e trabalho/escola.

As aplicações que utilizam o ponto final 2.0 também podem consumir REST APIs a partir da [Microsoft Graph](https://graph.microsoft.io) e o [Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) com qualquer tipo de conta.

<!-- For a quick introduction to the v2.0 endpoint, please view the [Getting Started with Microsoft Identities: Enterprise Grade Sign In For Your Apps](https://azure.microsoft.com/documentation/videos/build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps/) video. -->

## <a name="getting-started"></a>Introdução
[AZURE.VIDEO build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps]

Selecione a plataforma de favorita a partir da lista seguinte para criar uma aplicação a utilizar o nosso bibliotecas abrir origem & quadros.  Em alternativa, pode utilizar a nossa documentação de protocolo OAuth 2.0 & OpenID ligar para enviar e receber mensagens de protocolo diretamente sem utilizar uma biblioteca de auth.

<!-- TODO: Finalize this table  -->
[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

## <a name="whats-new"></a>Quais são as novidades
As informações conceptuais aqui será útil compreender o que é e o que não é possível fazer com o ponto final 2.0.

- Saiba mais sobre os [tipos de aplicações que pode ser criados com o ponto final 2.0](active-directory-v2-flows.md).
- Compreenda as [limitações, restrições e restrições](active-directory-v2-limitations.md) com o ponto final 2.0.
- Foram adicionadas recentemente suporte para [Administração limitada âmbitos](active-directory-v2-scopes.md) e o [cliente oauth2 ainda conceder credenciais](active-directory-v2-protocols-oauth-client-creds.md).  Experimentá-los!

## <a name="reference"></a>Referência
Estas ligações será útil para explorar a plataforma de profundidade:

- Compilar 2016: [Introdução ao Microsoft identidades: Enterprise grau início de sessão para as suas aplicações](https://azure.microsoft.com/documentation/videos/build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps/)
- Obter ajuda na pilha de conteúdo adicional utilizando o [azure active directory](http://stackoverflow.com/questions/tagged/azure-active-directory) ou [adal](http://stackoverflow.com/questions/tagged/adal) etiquetas.
- [Referência de protocolo 2.0](active-directory-v2-protocols.md)
- [Referência de Token 2.0](active-directory-v2-tokens.md)
- [Referência de biblioteca 2.0](active-directory-v2-libraries.md)
- [Âmbitos e consentimento no ponto final 2.0](active-directory-v2-scopes.md)
- [O Portal de registo de aplicação da Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)
- [Referência da API do Office 365 restantes](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)
- [O Microsoft Graph](https://graph.microsoft.io)