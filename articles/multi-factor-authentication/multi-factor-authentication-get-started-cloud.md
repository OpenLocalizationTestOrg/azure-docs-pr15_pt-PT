<properties
    pageTitle="Introdução ao Azure MFA na nuvem | Microsoft Azure"
    description="Esta é a página de autenticação do Microsoft Azure Multifator que descreve como introdução ao Azure MFA na nuvem."
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

# <a name="getting-started-with-azure-multi-factor-authentication-in-the-cloud"></a>Introdução ao Azure a autenticação Multifator na nuvem
Este artigo explica como começar a utilizar o Azure a autenticação Multifator na nuvem.

> [AZURE.NOTE]  A seguinte documentação fornece informações sobre como permitir que os utilizadores utilizando o **Portal clássica Azure**. Se está a procurar informações sobre como configurar a autenticação Multifator de Azure para utilizadores do Office 365, consulte o artigo [Configure a autenticação multifator para o Office 365.](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6?ui=en-US&rs=en-US&ad=US)

![MFA na nuvem](./media/multi-factor-authentication-get-started-cloud/mfa_in_cloud.png)

## <a name="prerequisites"></a>Pré-requisitos
Os pré-requisitos seguintes são necessários antes de poder ativar a autenticação Multifator de Azure para os seus utilizadores.


1. [Inscrever-se para uma subscrição do Azure](https://azure.microsoft.com/pricing/free-trial/) - se ainda não tiver uma subscrição do Azure, tem de inscrição para um. Se apenas for começando e utilizar Azure MFA pode utilizar uma subscrição de avaliação
2. [Criar um fornecedor de autenticação Multifator](multi-factor-authentication-get-started-auth-provider.md) e atribuí-la ao seu diretório ou [atribuir licenças a utilizadores](multi-factor-authentication-get-started-assign-licenses.md)

> [AZURE.NOTE]  Licenças estão disponíveis para utilizadores que têm Azure MFA, Azure AD Premium ou do conjunto de aplicações de mobilidade da empresa (EMS).  MFA é incluída no Azure AD Premium e EMS. Se tiver licenças suficientes, não terá criar um fornecedor de Auth.


## <a name="turn-on-two-step-verification-for-users"></a>Ativar a verificação de dois passos para utilizadores
Para começar a que exige o início duas verificação para um utilizador, altere o estado do utilizador de desativado para ativado.  Para mais informações sobre os Estados de utilizador, consulte o artigo [Estados de utilizador no Azure a autenticação Multifator](multi-factor-authentication-get-started-user-states.md)

Utilize o seguinte procedimento para ativar MFA para os seus utilizadores.

### <a name="to-turn-on-multi-factor-authentication"></a>Para ativar a autenticação multifator

1.  Inicie sessão no [portal clássica Azure](https://manage.windowsazure.com) como administrador.
2.  No lado esquerdo, clique em **Do Active Directory**.
3.  Em diretório, selecione a pasta para o utilizador que pretende ativar.
![Clique no diretório](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  Na parte superior, clique em **utilizadores**.
5.  Na parte inferior da página, clique em **Gerir Auth Multifator**. É aberta num novo separador do browser.
![Clique no diretório](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  Localize o utilizador que pretende ativar a verificação de dois passos. Poderá ter alterar a vista na parte superior. Certifique-se de que o estado é **desativado.** 
 ![Permitir que o utilizador](./media/multi-factor-authentication-get-started-cloud/enable1.png)
7.  Coloque um **Verificar** na caixa junto ao respetivo nome.
7.  No lado direito, clique em **Ativar**.
![Permitir que o utilizador](./media/multi-factor-authentication-get-started-cloud/user1.png)
8.  Clique em **Ativar a autenticação multifator**.
![Permitir que o utilizador](./media/multi-factor-authentication-get-started-cloud/enable2.png)
9.  Repare que o estado do utilizador foi alterada de **desativado** para **ativado**.
![Permitir que os utilizadores](./media/multi-factor-authentication-get-started-cloud/user.png)

Depois de ter ativado os seus utilizadores, deverá notificá-los por correio eletrónico. Da próxima vez que tentarem iniciar sessão, ser-se-á pedidos para se inscrever respetiva conta para verificação de dois passos. Assim que começarem a utilizar a verificação de dois passos, estes também terá de configurar as palavras-passe de aplicação para evitar a ser bloqueado terminar aplicações não baseadas no browser.


## <a name="use-powershell-to-automate-turning-on-two-step-verification"></a>Utilizar o PowerShell para automatizar a ativar a verificação de dois passos

Para alterar o [Estado](multi-factor-authentication-whats-next.md) através do [PowerShell do Azure AD](../powershell-install-configure.md), pode utilizar o seguinte.  Pode alterar `$st.State` seja igual a um dos seguintes Estados:

- Ativado
- Impostas
- Desativado  

> [AZURE.IMPORTANT]  Estamos a impedir a contra mover utilizadores diretamente a partir do Estado de desativar o estado de forçado. Não-baseada no browser e apps para de funcionar porque o utilizador não tem desaparece através de registo MFA e obtido uma [palavra-passe de aplicação](multi-factor-authentication-whats-next.md#app-passwords). Se tiver aplicações com base no browser e solicitar palavras-passe de aplicação, recomendamos que vão a partir de um Estado de desativado para ativado. Isto permite aos utilizadores registar e obter as suas palavras-passe de aplicação. Após esta ação, pode movê-las para forçado.

Utilizar o PowerShell seria uma opção para permitir que os utilizadores de em volume. Neste momento não existe nenhuma funcionalidade de ativar em volume no portal do Azure e precisa selecionar cada utilizador individualmente. Isto pode ser bastante uma tarefa se tiver vários utilizadores. Ao criar um PowerShell script utilizando o seguinte, pode repetir continuamente através de uma lista de utilizadores e ativá-las.

        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "\*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

Eis um exemplo:

    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "\*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }


Para obter mais informações, consulte o artigo [Estados de utilizador no Azure a autenticação Multifator](multi-factor-authentication-get-started-user-states.md)

## <a name="next-steps"></a>Próximos passos
Agora que configurou Azure a autenticação Multifator na nuvem, pode configurar e configurar a sua implementação. Consulte o artigo [Configurar a autenticação Multifator Azure](multi-factor-authentication-whats-next.md) para obter mais detalhes.
