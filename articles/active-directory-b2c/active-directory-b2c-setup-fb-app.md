<properties
    pageTitle="Azure Active B2C de diretório: Configuração Facebook | Microsoft Azure"
    description="Fornece inscrição e iniciar sessão para os consumidores com contas do Facebook nas suas aplicações que são protegidas por Azure Active Directory B2C."
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

# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-facebook-accounts"></a>Azure B2C diretório ativos: Fornecer inscrição e iniciar sessão para os consumidores com contas do Facebook

## <a name="create-a-facebook-application"></a>Criar uma aplicação do Facebook

Para utilizar o Facebook como um fornecedor de identidades no Azure Active Directory (Azure AD) B2C, precisa de criar uma aplicação do Facebook e fornecer-lhe os parâmetros de à direita. Precisa de uma conta do Facebook para o fazer. Se não tiver uma, pode acedê-lo na [https://www.facebook.com/](https://www.facebook.com/).

1. Aceder ao site do [Facebook para os programadores](https://developers.facebook.com/) e inicie sessão com as credenciais de conta do Facebook.
2. Se ainda não o tiver feito, tem de registar como um programador do Facebook. Para executar esta tarefa, clique em **Registar** (no canto superior direito da página), aceitar as políticas do Facebook e concluir os passos de registo.
3. Clique nas **Minhas aplicações** e, em seguida, clique em **Adicionar uma nova aplicação**. Selecione **Web site** , como a plataforma e, em seguida, clique em **Ignorar e criar ID da aplicação**.

    ![Facebook - adicionar uma nova aplicação](./media/active-directory-b2c-setup-fb-app/fb-add-new-app.png)

    ![Facebook - adicionar uma nova aplicação - Web site](./media/active-directory-b2c-setup-fb-app/fb-add-new-app-website.png)

    ![Facebook - criar o ID da aplicação](./media/active-directory-b2c-setup-fb-app/fb-new-app-skip.png)

4. No formulário, forneça um **Nome a apresentar**, um **E-Mail de contacto**de válido, uma **categoria**de adequada e clique em **Criar ID da aplicação**. Isto requer que aceitar as políticas de plataforma do Facebook e concluir uma verificação de segurança online.

    ![Facebook - criar um novo ID da aplicação](./media/active-directory-b2c-setup-fb-app/fb-create-app-id.png)

5. No painel de navegação esquerdo, clique em **Definições** .
6. Clique em **+ Adicionar plataforma** e, em seguida, selecione o **Web site**.

    ![Facebook - definições](./media/active-directory-b2c-setup-fb-app/fb-settings.png)

    ![Site do Facebook - definições-](./media/active-directory-b2c-setup-fb-app/fb-website.png)

7. Introduza [https://login.microsoftonline.com/](https://login.microsoftonline.com/) no campo **URL do Site** e, em seguida, clique em **Guardar alterações**.

    ![Facebook - URL do Site](./media/active-directory-b2c-setup-fb-app/fb-site-url.png)

8. Copie o valor de **ID da aplicação**. Clique em **Mostrar** e copie o valor da **Aplicação secreta**. Terá de ambos para configurar o Facebook como um fornecedor de identidades no seu inquilino. **Aplicação segredo** é uma credencial de segurança importantes.

    ![Facebook - ID da aplicação e secreta de aplicação](./media/active-directory-b2c-setup-fb-app/fb-app-id-app-secret.png)

9. Clique em **+ Adicionar produto** no painel de navegação esquerdo e, em seguida, no botão **Começar** ao lado de **Início de sessão do Facebook**.

    ![Facebook - início de sessão do Facebook](./media/active-directory-b2c-setup-fb-app/fb-login.png)

10. Introduza `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` no campo **OAuth válido redirecionar URIs** na secção **Definições do token de cliente** . Substitua **{inquilino}** com o nome do seu inquilino (por exemplo, contosob2c.onmicrosoft.com). Clique em **Guardar alterações** na parte inferior da página.

    ![Facebook - OAuth Redirect URI](./media/active-directory-b2c-setup-fb-app/fb-oauth-redirect-uri.png)

11. Para tornar a sua aplicação Facebook utilizáveis por Azure AD B2C, é necessário para o tornar disponível ao público. Pode fazê-lo clicando em **Rever de aplicação** no painel de navegação esquerdo e ativar o parâmetro na parte superior da página para **Sim** e clicando em **Confirmar**.

    ![Facebook - público de aplicação](./media/active-directory-b2c-setup-fb-app/fb-app-public.png)

## <a name="configure-facebook-as-an-identity-provider-in-your-tenant"></a>Configurar o Facebook como um fornecedor de identidades no seu inquilino

1. Siga estes passos para [Navegar para o pá funcionalidades B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) no portal do Azure.
2. No pá de funcionalidades B2C, clique em **fornecedores de identidade**.
3. Clique em **+ Adicionar** no topo da pá.
4. Fornece um **nome** de amigável para a configuração do fornecedor de identidade. Por exemplo, introduza "Caixa de factos".
5. Clique em **tipo de fornecedor de identidade**, selecione **Facebook**e clique em **OK**.
6. Clique em **configurar este fornecedor de identidade** e introduza o ID da aplicação e o segredo de aplicação (da aplicação Facebook que criou anteriormente) no **ID de cliente** e o **cliente segredo** respetivamente de campos.
7. Clique em **OK**e, em seguida, clique em **Criar** para guardar a configuração do Facebook.
