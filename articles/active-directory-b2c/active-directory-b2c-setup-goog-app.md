<properties
    pageTitle="Azure Active B2C de diretório: Configuração Google + | Microsoft Azure"
    description="Fornece inscrição e iniciar sessão para os consumidores com contas Google + nas suas aplicações que são protegidas por Azure Active Directory B2C."
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

# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-google-accounts"></a>Azure B2C diretório ativos: Fornecer inscrição e iniciar sessão para os consumidores com contas Google +

## <a name="create-a-google-application"></a>Criar uma aplicação do Google +

Para utilizar o Google + como um fornecedor de identidades B2C Azure Active Directory (Azure AD), é necessário criar uma aplicação do Google + e fornecer-lhe os parâmetros de à direita. É necessária uma conta do Google + para o fazer. Se não tiver uma, pode acedê-lo na [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp).

1. Aceda à [Consola de programadores do Google](https://console.developers.google.com/) e inicie sessão com as credenciais da conta Google +.
2. Clique em **Criar projeto**, introduza um **nome do projeto**e, em seguida, clique em **Criar**.

    ![Google + - introdução](./media/active-directory-b2c-setup-goog-app/google-get-started.png)

    ![Google + - novo projeto](./media/active-directory-b2c-setup-goog-app/google-new-project.png)

3. Clique em **Gestor de API** e, em seguida, clique em **credenciais** no painel de navegação esquerdo.
4. Clique no separador **OAuth consentimento ecrã** na parte superior.

    ![Google + - credenciais](./media/active-directory-b2c-setup-goog-app/google-add-cred.png)

5. Selecione ou especifique um **endereço de E-Mail**de válido, forneça um **nome de produto**e clique em **Guardar**.

    ![Google + - OAuth consentimento ecrã](./media/active-directory-b2c-setup-goog-app/google-consent-screen.png)

6. Clique em **novas credenciais** e, em seguida, selecione **OAuth ID de cliente**.

    ![Google + - OAuth consentimento ecrã](./media/active-directory-b2c-setup-goog-app/google-add-oauth2-client-id.png)

7. Em **tipo de aplicação**, selecione a **aplicação Web**.

    ![Google + - OAuth consentimento ecrã](./media/active-directory-b2c-setup-goog-app/google-web-app.png)

8. Forneça um **nome** para a sua aplicação, introduza `https://login.microsoftonline.com` no campo **autorizados JavaScript origens** , e `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` no campo **autorizados redirecionar URIs** . Substitua **{inquilino}** com o nome do seu inquilino (por exemplo, contosob2c.onmicrosoft.com). O valor de **{inquilino}** é entre maiúsculas e minúsculas. Clique em **Criar**.

    ![Google + - criar ID de cliente](./media/active-directory-b2c-setup-goog-app/google-create-client-id.png)

9. Copie os valores de **ID de cliente** e o **segredo de cliente**. Terá de ambos para configurar o Google + como um fornecedor de identidades no seu inquilino. **Cliente segredo** é uma credencial de segurança importantes.

    ![Google + - secreta de cliente](./media/active-directory-b2c-setup-goog-app/google-client-secret.png)

## <a name="configure-google-as-an-identity-provider-in-your-tenant"></a>Configurar o Google + como um fornecedor de identidades no seu inquilino

1. Siga estes passos para [Navegar para o pá funcionalidades B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) no portal do Azure.
2. No pá de funcionalidades B2C, clique em **fornecedores de identidade**.
3. Clique em **+ Adicionar** no topo da pá.
4. Fornece um **nome** de amigável para a configuração do fornecedor de identidade. Por exemplo, introduza "G +".
5. Clique em **tipo de fornecedor de identidade**, selecione o **Google**e clique em **OK**.
6. Clique em **Set up este fornecedor de identidade** e introduza o ID de cliente e o segredo de cliente do Google + pedido que criou anteriormente.
7. Clique em **OK** e, em seguida, clique em **Criar** para guardar a configuração do Google +.
