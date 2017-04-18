<properties
    pageTitle="Azure Active B2C de diretório: Configuração Amazon | Microsoft Azure"
    description="Fornece inscrição e iniciar sessão para os consumidores com contas Amazon nas suas aplicações que são protegidas por Azure Active Directory B2C."
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

# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-amazon-accounts"></a>Azure B2C diretório ativos: Fornecer inscrição e iniciar sessão para os consumidores com contas Amazon

## <a name="create-an-amazon-application"></a>Criar uma aplicação da Amazon

Para utilizar Amazon como um fornecedor de identidades B2C Azure Active Directory (Azure AD), é necessário criar uma aplicação do Amazon e fornecer-lhe os parâmetros de à direita. Precisa de uma conta de Amazon para o fazer. Se não tiver uma, pode acedê-lo na [http://www.amazon.com/](http://www.amazon.com/).

1. Ir para o [Centro de programadores do Amazon](https://login.amazon.com/) e inicie sessão com as credenciais da conta Amazon.
2. Se ainda não o tiver feito, clique em **Inscrever-se**, siga os passos de registo de programador e aceitar a política.
3. Clique em **Registe-se a nova aplicação**.

    ![Registar uma nova aplicação no Web site da Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-new-app.png)

4. Forneça informações da aplicação (**nome**, **Descrição**e **URL de aviso de privacidade**) e clique em **Guardar**.

    ![Fornecer informações da aplicação para o registo de uma nova aplicação na Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-register-app.png)

5. Na secção **Definições da Web** , copie os valores de **ID de cliente** e o **Segredo de cliente**. (Ter de clicar no botão **Mostrar segredo** para vê-la.) Precisa de ambos configurar Amazon como um fornecedor de identidades no seu inquilino. Clique em **Editar** na parte inferior da secção. **Cliente segredo** é uma credencial de segurança importantes.

    ![Fornecer ID de cliente e o segredo do cliente para a nova aplicação na Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-client-secret.png)

6. Introduza `https://login.microsoftonline.com` no campo **Permitido origens JavaScript** e `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` no campo **Permitido URLs devolvido** . Substitua **{inquilino}** com o nome do seu inquilino (por exemplo, contoso.onmicrosoft.com). Clique em **Guardar**. O valor de **{inquilino}** é entre maiúsculas e minúsculas.

    ![Fornecer JavaScript origens e URLs voltar para a nova aplicação na Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-urls.png)

## <a name="configure-amazon-as-an-identity-provider-in-your-tenant"></a>Configurar Amazon como um fornecedor de identidades no seu inquilino

1. Siga estes passos para [Navegar para o pá funcionalidades B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) no portal do Azure.
2. No pá de funcionalidades B2C, clique em **fornecedores de identidade**.
3. Clique em **+ Adicionar** no topo da pá.
4. Fornece um **nome** de amigável para a configuração do fornecedor de identidade. Por exemplo, introduza "Amzn".
5. Clique em **tipo de fornecedor de identidade**, selecione **Amazon**e clique em **OK**.
6. Clique em **Set up este fornecedor de identidade** e introduza o ID de cliente e o segredo de cliente da aplicação Amazon que criou anteriormente.
7. Clique em **OK** e, em seguida, clique em **Criar** para guardar a sua configuração Amazon.
