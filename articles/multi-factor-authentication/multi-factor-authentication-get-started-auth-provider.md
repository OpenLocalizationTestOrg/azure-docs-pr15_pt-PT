<properties
    pageTitle="Introdução ao Azure Multifator Auth fornecedor | Microsoft Azure"
    description="Saiba como criar um fornecedor de autenticação Multifator Azure."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/14/2016"
    ms.author="kgremban"/>



# <a name="getting-started-with-an-azure-multi-factor-auth-provider"></a>Começar com um fornecedor de autenticação Multifator Azure
Verificação de dois passos por predefinição está disponível para administradores globais que tenham o Azure Active Directory e utilizadores do Office 365. No entanto, se pretender tirar partido das [funcionalidades avançadas de](multi-factor-authentication-whats-next.md) deve de adquirir a versão completa de autenticação do Azure Multifator (MFA).

> [AZURE.NOTE]  Um fornecedor de autenticação Multifator Azure é utilizado para tirar partido das funcionalidades fornecidas pela versão completa do Azure MFA. É para utilizadores que **não tenham licenças através do Azure MFA, Azure AD Premium ou EMS**.  Azure MFA, Azure AD Premium e EMS incluem a versão completa do Azure MFA por predefinição.  Se tiver licenças, em seguida, não terá um fornecedor de autenticação Multifator Azure.

Um fornecedor de Azure Multifator Auth é necessário para transferir o SDK.

> [AZURE.IMPORTANT]  Para transferir o SDK, crie um fornecedor de autenticação Multifator Azure, mesmo se tiver Azure MFA, AAD Premium ou do licenças EMS.  Se criar um fornecedor de autenticação Multifator Azure para esse efeito e já tem licenças, certifique-se de que criar o fornecedor com o modelo **Por utilizadores** . Ligação, em seguida, o fornecedor para o diretório que contém as licenças Azure MFA, Azure AD Premium ou do EMS.  Este procedimento garante que só são faturada se tiver mais utilizadores exclusivos utilizando o SDK que o número de licenças que é o proprietário.


## <a name="to-create-a-multi-factor-auth-provider"></a>Para criar um fornecedor de autenticação Multifator

Utilize os passos seguintes para criar um fornecedor de autenticação Multifator Azure.

1. Inicie sessão no [portal clássica Azure](https://manage.windowsazure.com) como administrador.
2. No lado esquerdo, selecione **Do Active Directory**.
3. Na página do Active Directory, na parte superior, selecione **Fornecedores de autenticação Multifator**.
![Criar um fornecedor MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider1.png)
4. Na parte inferior, clique em **Novo**.
![Criar um fornecedor MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider2.png)
5. Em serviços de aplicação, selecione o **Fornecedor de autenticação Multifator**
![criação de um fornecedor de MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider3.png)
6. Selecione a **criação rápida**.
![Criar um fornecedor MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider4.png)
5. Preencha os campos seguintes e selecione **Criar**.
    1. **Nome** – o nome do fornecedor de autenticação Multifator.
    2. **Modelo de utilização** – se pretende permitir que os utilizadores individuais ou pagar por verificação. Selecione uma das duas opções:
        - Por autenticação – aquisição modelo que cobra por autenticação. Normalmente utilizado para cenários que utilizam autenticação Multifator de Azure numa aplicação do consumidor destinado.
        - Por utilizador ativado – aquisição modelo que cobra por ativada utilizador. Normalmente utilizado para acesso do colaborador aos aplicações como o Office 365. Escolha esta opção se tiver alguns utilizadores que já estão licenciados para Azure MFA.
    2. **Diretório** – inquilino do Azure Active Directory qual o fornecedor de autenticação Multifator está associado. Tenha em atenção o seguinte procedimento:
        - Não necessita de um diretório do Azure AD para criar um fornecedor de autenticação Multifator. Deixe a caixa em branco se apenas está a planear utilizar o servidor de autenticação Multifator Azure ou SDK.
        - O fornecedor de autenticação Multifator tem de ser associado a um directory Azure AD para tirar partido das funcionalidades avançadas.
        - Azure AD Connect, AAD Sync ou DirSync são apenas um requisito se estiver a sincronizar o seu ambiente do Active Directory no local com um directory Azure AD.  Se utilizar apenas um directório do Azure AD que não está sincronizado, em seguida, esta não é necessária.
![Criar um fornecedor MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider5.png)
5. Depois de clicar em criar, o fornecedor de autenticação Multifator é criado e deverá ver uma mensagem a informar: **fornecedor de autenticação Multifator foi criado com êxito**. Clique em **Ok**.
![Criar um fornecedor MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider6.png)
