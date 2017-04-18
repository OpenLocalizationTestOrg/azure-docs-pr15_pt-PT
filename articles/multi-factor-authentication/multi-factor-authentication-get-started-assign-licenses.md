<properties
    pageTitle="Atribuir licenças do Azure MFA | Microsoft Azure"
    description="Saiba como atribuir licenças de utilizador do Microsoft Azure Multifator autenticação."
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
    ms.date="10/17/2016"
    ms.author="kgremban"/>

# <a name="assigning-an-azure-mfa-azure-ad-premium-or-enterprise-mobility-license-to-users"></a>Atribuir uma licença do Azure MFA, Azure AD Premium ou do Enterprise mobilidade aos utilizadores

Se tiver adquirido o Azure MFA, Azure AD Premium ou do licenças do conjunto de aplicações do Enterprise mobilidade, não terá criar um fornecedor de autenticação Multifator. Depois de atribuir licenças aos seus utilizadores, pode começar a permitindo-lhes para MFA.

## <a name="to-assign-a-license"></a>Para atribuir uma licença

1. Inicie sessão no [portal clássica Azure](https://manage.windowsazure.com) como administrador.
2. No lado esquerdo, selecione **Do Active Directory**.
3. Na página do Active Directory, faça duplo clique no directório que tem os utilizadores que pretende ativar.
4. Na parte superior da página diretório, selecione **licenças**.
![Atribuir licenças](./media/multi-factor-authentication-get-started-assign-licenses/assign1.png)
5. Na página licenças, selecione a **Autenticação Multifator de Azure**, **Active Directory Premium**ou **Conjunto de mobilidade Enterprise**.  Se tiver apenas um, em seguida,-deverá ser selecionada automaticamente.
6. Na parte inferior da página, clique em **atribuir**.
![Atribuir licenças](./media/multi-factor-authentication-get-started-assign-licenses/assign3.png)
6. Na caixa que surgir, clique em junto a utilizadores ou grupos que pretende atribuir licenças a.  Deverá visualizar uma marca de verificação verde apareça.
7. Clique no ícone de marca de verificação para guardar as alterações.
![Atribuir licenças](./media/multi-factor-authentication-get-started-assign-licenses/assign4.png)
8. Deverá visualizar uma mensagem a informar quantas licenças foram atribuídas e quantas podem ter ocorrido uma falha.  Clique em **Ok**.
![Atribuir licenças](./media/multi-factor-authentication-get-started-assign-licenses/assign5.png)
