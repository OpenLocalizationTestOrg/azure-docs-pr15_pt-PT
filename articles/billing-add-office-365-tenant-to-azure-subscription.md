<properties
    pageTitle="Utilizar um inquilino do Office 365 com uma subscrição Azure | Microsoft Azure"
    description="Saiba como adicionar um diretório do Office 365 (inquilino) para uma subscrição do Azure para tornar a associação."
    services=""
    documentationCenter=""
    authors="JiangChen79"
    manager="mbaldwin"
    editor=""
    tags="billing,top-support-issue"/>

<tags
    ms.service="billing"
    ms.workload="na"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="cjiang"/>

# <a name="associate-an-office-365-tenant-with-an-azure-subscription"></a>Associar um inquilino do Office 365 com uma subscrição do Azure
Se tiver adquirido subscrições Azure e no Office 365 em separado no passado e agora pretende será possível aceder ao inquilino do Office 365 da subscrição Azure, é fácil fazê-lo. Este artigo mostra-lhe como.

> [AZURE.NOTE] Este artigo não se aplica a clientes do Enterprise Agreement (EA).

## <a name="quick-guidance"></a>Orientações rápidas
Para associar o seu inquilino do Office 365 com a sua subscrição Azure, utilize a sua conta Azure para adicionar o seu inquilino do Office 365 e, em seguida, associar a sua subscrição Azure o inquilino do Office 365.

## <a name="detailed-steps"></a>Passos detalhados
Neste cenário, Pinto parede é um utilizador que tenha uma subscrição do Azure sob a conta kelley.wall@outlook.com. Pinto também tem uma subscrição do Office 365 sob a conta kelley.wall@contoso.onmicrosoft.com. Agora Pinto quer ter acesso o inquilino do Office 365 com a subscrição Azure.

![Estado de captura de ecrã do Azure Active Directory](./media/billing-add-office-365-tenant-to-azure-subscription/s31_msa-aad-status.png)

![Captura de ecrã do Office 365 admin center público-alvo](./media/billing-add-office-365-tenant-to-azure-subscription/s32_office-365-user.png)

### <a name="prerequisites"></a>Pré-requisitos
Para a associação funcione corretamente, são necessários os pré-requisitos seguintes:

- Tem as credenciais de administrador de serviços da subscrição do Azure. Os administradores de cocriação não é possível executar um subconjunto dos passos.
- Tem as credenciais de administrador global do inquilino do Office 365.
- O endereço de e-mail do administrador de serviço não tem de estar contido no inquilino do Office 365.
- O endereço de e-mail do administrador de serviço não tem de corresponder que qualquer administrador global do inquilino do Office 365.
- Se estiver a utilizar um endereço de e-mail que é uma conta Microsoft e uma conta institucional, altere temporariamente o administrador do serviço da sua subscrição do Azure utilizar outra conta Microsoft. Pode criar uma nova conta do Microsoft na [página de inscrição de conta Microsoft](https://signup.live.com/).


Para alterar o seu administrador de serviços, siga estes passos:

1. Inicie sessão no [portal de gestão de conta](https://account.windowsazure.com/subscriptions).
2. Selecione a subscrição que pretende alterar.
3. Selecione **Editar detalhes da subscrição**.

    ![Captura de ecrã do Azure informações sobre a subscrição, com "Editar detalhes da subscrição" realçada](./media/billing-add-office-365-tenant-to-azure-subscription/s33_azure-edit-subscription-details.png)

4. Na caixa de **Administrador do serviço** , introduza o endereço de e-mail do novo administrador de serviço.

    ![Captura de ecrã da caixa de diálogo "Editar a sua subscrição"](./media/billing-add-office-365-tenant-to-azure-subscription/s34_change-subscription-service-admin.png)

### <a name="associate-the-office-365-tenant-with-the-azure-subscription"></a>Associar o inquilino do Office 365 com a subscrição do Azure
Para associar a subscrição Azure o inquilino do Office 365, siga estes passos:

1.  Inicie sessão no [portal de gestão de contas](https://account.windowsazure.com/subscriptions) com as credenciais de administrador do serviço.
2.  No painel esquerdo, selecione **Do ACTIVE DIRECTORY**.

    ![Entrada de captura de ecrã do Active Directory](./media/billing-add-office-365-tenant-to-azure-subscription/s35-classic-portal-active-directory-entry.png)

    > [AZURE.NOTE] Não deverá ver o inquilino do Office 365. Se vir, ignore o passo seguinte.

    ![Captura de ecrã do directório predefinido do Azure Active Directory](./media/billing-add-office-365-tenant-to-azure-subscription/s36-aad-tenant-default.png)

3. Adicione o inquilino do Office 365 à sua subscrição do Azure.

    um. Selecione **Novo** > **DIRETÓRIO** > **Criar PERSONALIZADO**.

    ![Captura de ecrã do Azure Active Directory personalizada criar](./media/billing-add-office-365-tenant-to-azure-subscription/s37-aad-custom-create.png)

    b. Na página **Adicionar diretório** , em **DIRETÓRIO**, selecione **Utilizar directório existente**. Em seguida, selecione **estou pronto para ser assinado agora**e, selecione **concluída** ![ícone concluir](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).

    ![Captura de ecrã de "Utilizar existente directório"](./media/billing-add-office-365-tenant-to-azure-subscription/s39_add-directory-use-existing.png)

    c. Depois de que tem sessão iniciada, inicie sessão com as credenciais de administrador global do seu inquilino do Office 365.

    ![Captura de ecrã do Office 365 administrador global iniciar sessão](./media/billing-add-office-365-tenant-to-azure-subscription/s310_sign-in-global-admin-office-365.png)

    d. Selecione **continuar**.

    ![Captura de ecrã da verificação](./media/billing-add-office-365-tenant-to-azure-subscription/s311_use-contoso-directory-azure-verify.png)

    "e". Selecione **Terminar sessão agora**.

    ![Captura de ecrã de terminar sessão](./media/billing-add-office-365-tenant-to-azure-subscription/s312_use-contoso-directory-azure-confirm-and-sign-out.png)

    f. Inicie sessão no [portal de gestão de contas](https://account.windowsazure.com/subscriptions) com as credenciais de administrador do serviço.

    ![Captura de ecrã do Azure iniciar sessão](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)

    g. Deverá ver o seu inquilino do Office 365 no dashboard.

    ![Captura de ecrã do dashboard](./media/billing-add-office-365-tenant-to-azure-subscription/s314_office-365-tenant-appear-in-azure.png)

4. Altere o directório associado à subscrição Azure.

    um. Selecione **Definições**.

    ![Ícone de definições do portal clássico de captura de ecrã do Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s315_azure-classic-portal-settings-icon.png)

    b. Selecione a sua subscrição do Azure e, em seguida, selecione **Editar DIRETÓRIO**.
    ![Diretório de edição de subscrição de captura de ecrã do Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s316_azure-subscription-edit-directory.png)

    c. Selecione **seguinte** ![seguinte ícone](./media/billing-add-office-365-tenant-to-azure-subscription/s317_next-icon.png).

    ![Captura de ecrã de "Alterar diretório associado"](./media/billing-add-office-365-tenant-to-azure-subscription/s318_azure-change-associated-directory.png)

    > [AZURE.WARNING] Receberá um aviso de que todos os administradores de cocriação serão removidos.

    ![Azure-confirmar--mapeamento de directório](./media/billing-add-office-365-tenant-to-azure-subscription/s322_azure-confirm-directory-mapping.png)

    >[AZURE.WARNING] Para além disso, também serão removidos todos os utilizadores de [controlo de acesso baseado em funções RBCA ()](./active-directory/role-based-access-control-configure.md) com o access atribuído dos grupos de recursos existente. No entanto, o aviso que recebe menções apenas a remoção da coadministradores.

    ![atribuído-utilizadores-removido--grupos de recursos](./media/billing-add-office-365-tenant-to-azure-subscription/s325_assigned-users-removed-resource-groups.png)

    d. Selecione **concluída** ![ícone concluir](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).

5. Agora pode adicionar o seu contas organizacionais do Office 365 como coadministradores para o inquilino do Azure Active Directory.

    um. Selecione o separador de **administradores** e, em seguida, selecione **Adicionar**.

    ![Separador de administradores de definições do portal clássico de captura de ecrã do Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s319_azure-classic-portal-settings-administrators.png)

    b. Introduza uma conta institucional do seu inquilino do Office 365, selecione a subscrição Azure e, em seguida, selecione **concluída** ![ícone concluir](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).

    ![Caixa de diálogo de administrador cocriação de adicionar a captura de ecrã do Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s320_azure-add-co-administrator.png)

    c. Volte ao separador **administradores** . Deverá ver conta institucional apresentada como administrador cocriação.

    ![Captura de ecrã do separador de administradores](./media/billing-add-office-365-tenant-to-azure-subscription/s321_azure-co-administrator-added.png)

6. Seguinte pode testar o access com o administrador de cocriação.

    um. Terminar sessão no portal de gestão de conta.

    b. Abra o [portal de gestão de contas](https://account.windowsazure.com/subscriptions) ou o [Azure portal](https://portal.azure.com/).

    c. Se a página de início de sessão no Azure tem uma ligação de **Iniciar sessão com a sua conta de organização**, selecione a ligação. Caso contrário, ignore este passo.

    ![Captura de ecrã do Azure início de sessão na página](./media/billing-add-office-365-tenant-to-azure-subscription/3-sign-in-to-azure.png)

    d. Introduza as credenciais de administrador a cocriação e, em seguida, selecione **Iniciar sessão**.

    ![Captura de ecrã do Azure início de sessão na página](./media/billing-add-office-365-tenant-to-azure-subscription/s324_azure-sign-in-with-co-admin.png)

## <a name="next-steps"></a>Próximos passos
Cenários relacionados incluem:

- Já tem uma subscrição do Office 365 e estiver pronto para uma subscrição do Azure, mas que pretende utilizar as contas de utilizador do Office 365 existentes para a sua subscrição Azure.
- For um subscritor do Azure e pretende obter uma subscrição do Office 365 para os utilizadores na sua instância do Azure Active Directory existente.

Para saber como realizar estas tarefas, consulte o artigo [utilizar o Office 365 existente de conta com a sua subscrição Azure, ou vice versa](billing-use-existing-office-365-account-azure-subscription.md).
