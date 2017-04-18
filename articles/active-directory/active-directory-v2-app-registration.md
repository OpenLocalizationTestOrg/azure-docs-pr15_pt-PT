<properties
    pageTitle="registo de aplicação 2.0 | Microsoft Azure"
    description="Como registar uma aplicação com a Microsoft para ativar a iniciar sessão e aceder a serviços da Microsoft utilizando o ponto final 2.0"
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
    ms.date="09/16/2016"
    ms.author="dastrock"/>

# <a name="how-to-register-an-app-with-the-v20-endpoint"></a>Como registar uma aplicação com o ponto final 2.0

Para criar uma aplicação que aceita MSA & Azure AD iniciar sessão, primeiro terá de registar uma aplicação com a Microsoft.  Neste momento, não poderá utilizar qualquer aplicações existentes, poderá ter com Azure AD ou MSA - terá de criar um novo.

> [AZURE.NOTE]
    Nem todos os cenários do Azure Active Directory e funcionalidades são suportadas pelo ponto final 2.0.  Para determinar se deve utilizar o ponto final 2.0, leia sobre as [limitações 2.0](active-directory-v2-limitations.md).

## <a name="visit-the-microsoft-app-registration-portal"></a>Visite o portal de registo de aplicação do Microsoft
Primeira coisas pela primeira vez - navegue até ao [https://apps.dev.microsoft.com/?deeplink=/appList](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).  Este é um novo portal de registo de aplicação onde pode gerir as aplicações do Microsoft.

Inicie sessão com um uma pessoal ou trabalho ou escola conta Microsoft.  Se não tiver o quer, inscrever-se para uma nova conta pessoal. Prosseguir, não irá demorar muito - podemos irá esperar aqui.

Concluído? Que deverá agora estar procura na sua lista de aplicações do Microsoft, que é provavelmente vazia.  Vamos alterar que.

Clique em **Adicionar uma aplicação**e atribua um nome.  O portal vai atribuir a sua aplicação um Id de aplicação globalmente exclusivo que irá utilizar mais tarde no seu código.  Se a sua aplicação inclui um componente do lado do servidor tokens de acesso que necessita para APIs chamados (tenha em conta: Office, Azure ou o seu próprio web API), que pretende criar uma **Aplicação secreta** aqui também.
<!-- TODO: Link for app secrets -->

Em seguida, adicione as plataformas que irá utilizar a sua aplicação.

- Para aplicações baseadas na web, fornece um **URI redirecionar** onde o início de sessão no mensagens podem ser enviadas.
- Para obter aplicações móveis, copie para baixo o uri de redirecionamento predefinido criado automaticamente.

Opcionalmente, pode personalizar o aspeto e funcionalidade da sua página de início de sessão na secção de perfil.  Certifique-se clicar em **Guardar** antes de passar.

> [AZURE.NOTE] Quando cria uma aplicação utilizando [https://apps.dev.microsoft.com/?deeplink=/appList](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), a aplicação será registada no inquilino principal da conta que utiliza para iniciar sessão no portal.  Isto significa que não pode registar uma aplicação no seu inquilino do Azure AD utilizando uma conta Microsoft pessoal.  Se pretender explicitamente registar uma aplicação num inquilino específico, inicie sessão com uma conta esse inquilino criada originalmente.

## <a name="build-a-quick-start-app"></a>Criar uma aplicação do guia de introdução
Agora que tem uma aplicação do Microsoft, pode concluir um dos nossos tutoriais de guia de introdução do 2.0.  Aqui estão alguns recomendações:

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]
