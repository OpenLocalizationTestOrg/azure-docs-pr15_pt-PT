<properties
    pageTitle="Azure B2C diretório ativos: Criar um inquilino do Azure Active Directory B2C | Microsoft Azure"
    description="Um tópico sobre como criar um inquilino do Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.topic="article"
    ms.devlang="na"
    ms.date="08/30/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-create-an-azure-ad-b2c-tenant"></a>Azure B2C diretório ativos: Criar um inquilino do Azure AD B2C

Para começar a utilizar B2C do Microsoft Azure Active Directory (Azure AD), siga os três passos descritos neste artigo.

## <a name="step-1-sign-up-for-an-azure-subscription"></a>Passo 1: Inscrever-se para uma subscrição do Azure

Se já tiver uma subscrição do Azure, ignore este passo. Caso contrário, inscrever-se para uma [subscrição do Azure](../active-directory/sign-up-organization.md) e obter acesso a Azure AD B2C.

## <a name="step-2-create-an-azure-ad-b2c-tenant"></a>Passo 2: Criar um inquilino do Azure AD B2C

Utilize os passos seguintes para criar um novo inquilino do Azure AD B2C. Atualmente B2C funcionalidades não podem ser ativadas no seu inquilinos existentes.

1. Inicie sessão no [portal clássica Azure](https://manage.windowsazure.com/) como o administrador de subscrição. Este é o mesmo trabalho ou conta escolar ou a mesma conta Microsoft que utilizou para se inscrever Azure.
2. Clique em **Novo** > **Serviços de aplicação** > **do Active Directory** > **diretório** > **Criar personalizado**.

    ![Captura de ecrã do início criar um inquilino](./media/active-directory-b2c-get-started/new-directory.png)

3. Selecione o **nome**, **Nome de domínio** e **país ou região** do seu inquilino.
4. Selecione a opção que indica que **Este é um diretório de B2C**.
5. Clique na marca de verificação para concluir a ação.

    ![Captura de ecrã da marca de verificação para criar um diretório de B2C](./media/active-directory-b2c-get-started/create-b2c-directory.png)

6. O inquilino agora é criado e serão apresentados na extensão do Active Directory. Também são efetuadas um Administrador Global do inquilino. Pode adicionar outros administradores globais conforme necessário.

    > [AZURE.IMPORTANT]
    Se está a planear a utilizar um inquilino B2C para uma aplicação de produção, leia o artigo da [escala de produção vs. inquilinos de pré-visualização B2C](active-directory-b2c-reference-tenant-type.md). Tenha em atenção que existem conhecidos problemas quando eliminar um inquilino do B2C existente e criar novamente com o mesmo nome de domínio. Tem de criar um inquilino B2C com um nome de domínio diferente.

## <a name="step-3-navigate-to-the-b2c-features-blade-on-the-azure-portal"></a>Passo 3: Navegar para o pá de funcionalidades B2C no portal do Azure

1. Navegue para a extensão do Active Directory na barra de navegação no lado esquerdo.
2. Localize o inquilino em separador de **diretório** e clique na mesma.
3. Clique no separador **Configurar** .
4. Clique na ligação **Gerir B2C definições** na secção **Administração de B2C** .

    ![Captura de ecrã da configuração de diretório para B2C](./media/active-directory-b2c-get-started/b2c-directory-configure-tab.png)

5. Portal do Azure com a pá de funcionalidades B2C mostrando será aberto numa janela de novo separador do browser ou.

    > [AZURE.IMPORTANT]
    Pode demorar até 2 a 3 minutos do seu inquilino para serem acessíveis no portal do Azure. Repetir os passos seguintes após algum tempo irá corrigir. Caso contrário, contacte o suporte.

6. Afixe este pá para sua Startboard para facilitar o acesso. (A ferramenta de Pin está marcada como vermelho no canto superior direito da pá a funcionalidades.)

    ![Captura de ecrã da pá funcionalidades B2C](./media/active-directory-b2c-get-started/b2c-features-blade.png)

    > [AZURE.NOTE]
    Pode gerir utilizadores e grupos, configuração de reposição personalizada de palavra-passe e funcionalidades de imagem corporativa da empresa do seu inquilino no [Azure portal clássica](https://manage.windowsazure.com/).

## <a name="easy-access-to-the-b2c-features-blade-on-the-azure-portal"></a>Acesso fácil a pá de funcionalidades a B2C no portal do Azure

Para melhorar a descoberta, adicionámos um atalho para o pá de funcionalidades B2C no portal do Azure.

1. Inicie sessão no portal do Azure como Administrador Global do seu inquilino B2C. Se já tiver sessão iniciada para um inquilino diferentes, mude inquilinos (no canto superior direito).
2. Clique em **Procurar** no painel de navegação esquerdo.
3. Clique em **B2C do Azure AD** para aceder a pá funcionalidades B2C.

    ![Captura de ecrã de procurar para B2C pá de funcionalidades](./media/active-directory-b2c-get-started/b2c-browse.png)

## <a name="next-steps"></a>Próximos passos

Saiba como registar uma aplicação do Azure AD B2C e para criar uma aplicação do guia de introdução ao ler o artigo [Azure Active Directory B2C: registar a sua aplicação](active-directory-b2c-app-registration.md).
