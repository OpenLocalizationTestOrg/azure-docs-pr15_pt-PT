<properties
    pageTitle="Adicionar uma nova conta de inquilino do Azure pilha no Azure Active Directory | Microsoft Azure"
    description="Depois de a implementar o conceito de pilha do Microsoft Azure, tem de criar a conta de utilizador, pelo menos, um inquilino para pode explorar o portal do inquilino."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="erikje"/>

# <a name="add-a-new-azure-stack-tenant-account-in-azure-active-directory"></a>Adicionar uma nova conta de inquilino do Azure pilha no Azure Active Directory

Depois da [Implementar o conceito de pilha Azure](azure-stack-run-powershell-script.md), terá de uma conta de utilizador do inquilino para que possa explorar o portal do inquilino e testar o seu ofertas e planos. Pode criar uma conta de inquilino utilizando [o portal do Azure](#create-an-azure-stack-tenant-account-using-the-azure-portal) ou ao [utilizar o PowerShell](#create-an-azure-stack-tenant-account-using-powershell).

## <a name="create-an-azure-stack-tenant-account-using-the-azure-portal"></a>Criar uma conta de inquilino de pilha de Azure utilizando o portal do Azure

Tem de ter uma subscrição do Azure para utilizar o portal do Azure.

1. Iniciar sessão no [Azure](http://manage.windowsazure.com).

2.  Na barra de navegação do lado esquerdo do Microsoft Azure, clique em **Do Active Directory**.

3.  Na lista de diretório, clique no directório que pretende utilizar para Azure pilha ou crie um novo.

4.  Na página diretório, clique em **utilizadores**.

5.  Clique em **Adicionar utilizador**.

6.  No Assistente de **Adicionar utilizador** , na lista **tipo de utilizador** , selecione **novo utilizador na sua organização**.

7.  Na caixa **nome de utilizador** , escreva um nome para o utilizador.

8.  No **@** caixa, selecione a entrada apropriada.

9.  Clique na seta seguinte.

10.  Na página de **perfil de utilizador** do assistente, escreva um **nome próprio**, **Apelido**e **nome a apresentar**.

11. Na lista de **funções** , selecione o **utilizador**.

12. Clique na seta seguinte.

13. Na página de **obter a palavra-passe temporária** , clique em **Criar**.

14. Copie a **nova palavra-passe**.

15. Inicie sessão no Microsoft Azure com a nova conta. Altere a palavra-passe quando lhe for pedido.

16. Iniciar sessão no `https://portal.azurestack.local` com a nova conta para ver o portal do inquilino.

## <a name="create-an-azure-stack-tenant-account-using-powershell"></a>Criar uma conta de inquilino pilha Azure através do PowerShell

Se não tiver uma subscrição do Azure, não pode utilizar o portal do Azure para adicionar uma conta de utilizador do inquilino. Neste caso, pode utilizar em vez disso, o Azure Active Directory módulo para Windows PowerShell.

> [AZURE.NOTE] Se estiver a utilizar o Microsoft Account (Live ID) para implementar o conceito de pilha Azure, não pode utilizar o AAD PowerShell para criar a conta de inquilino. 

1.  Instale o [Assistente Microsoft Online Services iniciar sessão para profissionais de TI RTW](https://www.microsoft.com/en-us/download/details.aspx?id=41950).

2.  Instale o [Módulo Azure Active Directory para Windows PowerShell (versão de 64 bits)](http://go.microsoft.com/fwlink/p/?linkid=236297) e abra-o.

3.  Execute os seguintes cmdlets:




    ```powershell
    # Provide the AAD credential you use to deploy Azure Stack PoC
   
            $msolcred = get-credential
    
    # Add a tenant account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".
    
            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId
    
    ```

4.  Inicie sessão no Microsoft Azure com a nova conta. Altere a palavra-passe quando lhe for pedido.

5.  Inicie sessão no `https://portal.azurestack.local` com a nova conta para ver o portal do inquilino.



