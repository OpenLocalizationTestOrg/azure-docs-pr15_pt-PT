<properties
    pageTitle="Azure Active B2C de diretório: Configuração LinkedIn | Microsoft Azure"
    description="Fornecer inscrição e iniciar sessão para os consumidores com contas do LinkedIn nas suas aplicações que são protegidas por Azure Active Directory B2C"
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

# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-linkedin-accounts"></a>Azure B2C diretório ativos: Fornecer inscrição e iniciar sessão para os consumidores com contas do LinkedIn

## <a name="create-a-linkedin-application"></a>Criar uma aplicação do LinkedIn

Para utilizar o LinkedIn como um fornecedor de identidades no Azure Active Directory (Azure AD) B2C, precisa de criar uma aplicação do LinkedIn e fornecer-lhe os parâmetros de à direita. Precisa de uma conta do LinkedIn para o fazer. Se não tiver uma, pode acedê-lo na [https://www.linkedin.com/](https://www.linkedin.com/).

1. Aceda ao [Web site de programadores do LinkedIn](https://www.developer.linkedin.com/) e inicie sessão com as credenciais de conta do LinkedIn.
2. Clique nas **Minhas aplicações** na barra de menus superior e, em seguida, clique em **Criar aplicação**.

    ![LinkedIn - nova aplicação](./media/active-directory-b2c-setup-li-app/linkedin-new-app.png)

3. No formulário de **criar uma nova aplicação** , preencha as informações relevantes (**Nome da empresa**, **nome**, **Descrição**, **Logótipo URL da aplicação**, **Utilização de aplicações**, **URL do Web site**, **E-Mail da empresa** e **Telefone da empresa**).
4. Concorda a **LinkedIn API termos de utilização** e clique em **Submeter**.

    ![LinkedIn - Register aplicação](./media/active-directory-b2c-setup-li-app/linkedin-register-app.png)

5. Copie os valores de **ID de cliente** e o **Segredo de cliente**. (Pode encontrá-las em **Chaves de autenticação**.) Terá de ambos para configurar o LinkedIn como um fornecedor de identidades no seu inquilino.

    >[AZURE.NOTE] **Cliente segredo** é uma credencial de segurança importantes.

6. Introduza `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` no campo **Autorizados URLs redirecionar** (em **OAuth 2.0**). Substitua **{inquilino}** com o nome do seu inquilino (por exemplo, contoso.onmicrosoft.com). Clique em **Adicionar**e, em seguida, clique em **Atualizar**. O valor de **{inquilino}** é entre maiúsculas e minúsculas.

    ![LinkedIn - aplicação de configuração](./media/active-directory-b2c-setup-li-app/linkedin-setup.png)

## <a name="configure-linkedin-as-an-identity-provider-in-your-tenant"></a>Configurar o LinkedIn como um fornecedor de identidades no seu inquilino

1. Siga estes passos para [Navegar para o pá funcionalidades B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) no portal do Azure.
2. No pá de funcionalidades B2C, clique em **fornecedores de identidade**.
3. Clique em **+ Adicionar** no topo da pá.
4. Fornece um **nome** de amigável para a configuração do fornecedor de identidade. Por exemplo, introduza "lista horizontal".
5. Clique em **tipo de fornecedor de identidade**, selecione **LinkedIn**e clique em **OK**.
6. Clique em **Set up este fornecedor de identidade** e introduza o ID de cliente e o segredo de cliente da aplicação LinkedIn que criou anteriormente.
7. Clique em **OK** e, em seguida, clique em **Criar** para guardar a configuração do LinkedIn.
