<properties
    pageTitle="Azure Active B2C de diretório: Configuração da conta do Microsoft | Microsoft Azure"
    description="Fornece inscrição e iniciar sessão para os consumidores com contas do Microsoft nas suas aplicações que são protegidas por Azure Active Directory B2C."
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
    ms.topic="article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-microsoft-accounts"></a>Azure B2C diretório ativos: Fornecer inscrição e iniciar sessão para os consumidores com contas do Microsoft

## <a name="create-a-microsoft-account-application"></a>Criar uma aplicação da conta Microsoft

Para utilizar a conta Microsoft como um fornecedor de identidades B2C Azure Active Directory (Azure AD), é necessário criar uma aplicação da conta Microsoft e fornecer-lhe os parâmetros de à direita. Precisa de uma conta Microsoft para o fazer. Se não tiver uma, pode acedê-lo na [https://www.live.com/](https://www.live.com/).

1. Aceda ao [Portal de registo do Microsoft aplicação](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) e inicie sessão com as credenciais da conta Microsoft.
2. Clique em **Adicionar uma aplicação**.

    ![Microsoft conta - adicionar uma nova aplicação](./media/active-directory-b2c-setup-msa-app/msa-add-new-app.png)

3. Forneça um **nome** para a sua aplicação e clique em **Criar aplicação**.

    ![Conta Microsoft - nome da aplicação](./media/active-directory-b2c-setup-msa-app/msa-app-name.png)

4. Copie o valor de **Id da aplicação**. Terá de-o para configurar a conta Microsoft como um fornecedor de identidades no seu inquilino.

    ![Conta Microsoft - Id da aplicação](./media/active-directory-b2c-setup-msa-app/msa-app-id.png)

5. Clique em **Adicionar plataforma** e escolher **a Web**.

    ![Microsoft conta - adicionar plataforma](./media/active-directory-b2c-setup-msa-app/msa-add-platform.png)

    ![Conta Microsoft - Web](./media/active-directory-b2c-setup-msa-app/msa-web.png)

6. Introduza `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` no campo **URIs redirecionar** . Substitua **{inquilino}** com o nome do seu inquilino (por exemplo, contosob2c.onmicrosoft.com).

    ![Conta Microsoft - redirecionamento de URL](./media/active-directory-b2c-setup-msa-app/msa-redirect-url.png)

7. Clique em **Gerar nova palavra-passe** na secção de **Segredos de aplicação** . Copie a nova palavra-passe apresentada no ecrã. Terá de-o para configurar a conta Microsoft como um fornecedor de identidades no seu inquilino. Esta palavra-passe é uma credencial de segurança importantes.

    ![Microsoft conta - gerar nova palavra-passe](./media/active-directory-b2c-setup-msa-app/msa-generate-new-password.png)

    ![Conta Microsoft - nova palavra-passe](./media/active-directory-b2c-setup-msa-app/msa-new-password.png)

8. Selecione a caixa que diz **Live SDK suporte** na secção **Opções avançadas** . Clique em **Guardar**.

    ![Conta Microsoft - suporte Live SDK](./media/active-directory-b2c-setup-msa-app/msa-live-sdk-support.png)

## <a name="configure-microsoft-account-as-an-identity-provider-in-your-tenant"></a>Configurar a conta Microsoft como um fornecedor de identidades no seu inquilino

1. Siga estes passos para [Navegar para o pá funcionalidades B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) no portal do Azure.
2. No pá de funcionalidades B2C, clique em **fornecedores de identidade**.
3. Clique em **+ Adicionar** no topo da pá.
4. Fornece um **nome** de amigável para a configuração do fornecedor de identidade. Por exemplo, introduza "MSA".
5. Clique em **tipo de fornecedor de identidade**, selecione a **conta Microsoft**e clique em **OK**.
6. Clique em **Set up este fornecedor de identidade** e introduza o Id da aplicação e a palavra-passe da aplicação de conta Microsoft que criou anteriormente.
7. Clique em **OK** e, em seguida, clique em **Criar** para guardar a configuração da sua conta Microsoft.
