<properties
    pageTitle="Azure Active B2C de diretório: Registo de aplicações | Microsoft Azure"
    description="Como registar a sua aplicação com o Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/30/2016"
    ms.author="swkrish"/>


# <a name="azure-active-directory-b2c-register-your-application"></a>Azure B2C diretório ativos: Registar a aplicação

## <a name="prerequisite"></a>Pré-requisito

Para criar uma aplicação que aceita consumidor inscrição e iniciar sessão, primeiro tem de registar a aplicação com um inquilino do Azure Active Directory B2C. Obter o seu próprio inquilino através dos passos descritos no [artigo criar um inquilino do Azure AD B2C](active-directory-b2c-get-started.md). Depois de seguir todos os passos no artigo, terá de pá de funcionalidades B2C afixada à sua Startboard.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="navigate-to-the-b2c-features-blade"></a>Navegue para a pá funcionalidades B2C

Se tiver o pá de funcionalidades B2C afixada à sua Startboard, verá o pá assim que iniciar sessão no [portal do Azure](https://portal.azure.com/) como o Administrador Global do inquilino B2C.

Também pode aceder a pá ao clicar em **Procurar** e, em seguida, **Azure AD B2C** no painel de navegação esquerdo no [portal do Azure](https://portal.azure.com/).

> [AZURE.IMPORTANT] Tem de ser Administrador Global do inquilino B2C possam aceder a pá funcionalidades B2C. Um Administrador Global a partir de qualquer outro inquilino ou por um utilizador a partir de qualquer inquilino não é possível aceder à mesma.  Pode alternar ao seu inquilino B2C utilizando comutador de inquilino no canto superior direito do Portal do Azure.

## <a name="register-an-application"></a>Registe-se uma aplicação

1. No pá de funcionalidades do B2C no portal do Azure, clique em **aplicações**.
2. Clique em **+ Adicionar** no topo da pá.
3. Introduza um **nome** para a aplicação que descreve a sua aplicação para os consumidores. Por exemplo, pode introduzir "Contoso B2C aplicação".
4. Se estiver a escrever uma aplicação baseada na web, ativar/desativar a transição **incluem do web app / web API** para **Sim**. Os **URLs de resposta** são os pontos finais onde Azure AD B2C irá devolver qualquer tokens que solicita a sua aplicação. Por exemplo, introduza `https://localhost:44321/`. Se a sua aplicação web também irá chamar alguns web API protegido por Azure AD B2C, irá querer criar uma **Aplicação secreta** também ao clicar no botão **Gerar chave** .

    > [AZURE.NOTE] Um **Aplicação secreta** é uma credencial de segurança importantes e deverá ser protegido de forma adequada.

5. Se estiver a escrever uma aplicação móvel, ativar/desativar o parâmetro de **cliente nativo incluir** como **Sim**. Copiar para baixo a predefinição **Redirecionar URI** que é automaticamente criado por si.
6. Clique em **Criar** para registar a aplicação.
7. Clique na aplicação que acabou de criar e copie para baixo o globalmente exclusivo **ID da aplicação de cliente** que irá utilizar mais tarde no seu código.

> [AZURE.IMPORTANT] Aplicações criadas na pá funcionalidades B2C tem de gerido na mesma localização. Se editar B2C aplicações utilizando o PowerShell ou outro portal, tornam-se não suportadas e provavelmente não funcionam com Azure AD B2C.

## <a name="build-a-quick-start-application"></a>Criar uma aplicação do guia de introdução

Agora que tem uma aplicação registada com a Azure AD B2C, pode concluir um dos nossos tutoriais de guia para começar a trabalhar. Aqui estão alguns recomendações:

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]
