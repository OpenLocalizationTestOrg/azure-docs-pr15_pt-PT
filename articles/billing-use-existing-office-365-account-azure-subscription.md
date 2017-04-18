<properties
    pageTitle="Partilhar uma única inquilino do Azure AD entre subscrições do Office 365 e Azure | Microsoft Azure"
    description="Saiba como partilhar o seu inquilino do Office 365 Azure AD e dos seus utilizadores com a sua subscrição Azure, ou vice-versa"
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
    ms.date="08/17/2016"
    ms.author="cjiang"/>

# <a name="use-an-existing-office-365-account-with-your-azure-subscription-or-vice-versa"></a>Utilizar uma conta existente do Office 365 com a sua subscrição Azure ou vice-versa
Cenário: Já tem uma subscrição do Office 365 e estiver pronto para uma subscrição do Azure, mas que pretende utilizar as contas de utilizador do Office 365 existentes para a sua subscrição Azure. Em alternativa, for um subscritor do Azure e pretende obter uma subscrição do Office 365 para os utilizadores na sua existente Azure Active Directory. Este artigo mostra-lhe como é fácil alcançar ambos.

> [AZURE.NOTE] Este artigo não se aplica a clientes do Enterprise Agreement (EA). Se precisar de mais ajuda em qualquer ponto neste artigo, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obter o seu problema resolvido rapidamente.


## <a name="quick-guidance"></a>Orientações rápidas

- Se já tiver uma subscrição do Office 365 e pretenda inscrever-se para o Azure, utilize a opção **Iniciar sessão com a sua conta institucional** . Em seguida, continue o processo de inscrição Azure com a sua conta do Office 365. Consulte o artigo [obter passos detalhados sobre neste artigo](#s1).

- Se já tiver uma subscrição do Azure e pretende obter uma subscrição do Office 365, inicie sessão no Office 365 com a sua conta Azure. Em seguida, avance com os passos de inscrição. Depois de concluir a inscrição, a subscrição do Office 365 é adicionada na mesma instância do Azure Active Directory que a sua subscrição Azure pertence. Para obter mais informações, consulte a secção [obter passos detalhados sobre neste artigo](#s2).

>[AZURE.NOTE] Para obter uma subscrição do Office 365, a conta utilize de inscrição tem de ser um membro da função de Administrador Global ou administrador de faturação diretório no inquilino do Azure Active Directory. [Saiba como determinar a função no Azure Active Directory](#how-to-know-your-role-in-your-azure-active-directory).

Para compreender o que acontece quando adiciona uma subscrição a uma conta, consulte as [informações de fundo posteriormente neste artigo](#background-information).

## <a name="detailed-steps"></a>Passos detalhados
<a id="s1"></a>
### <a name="scenario-1-office-365-users-who-plan-to-buy-azure"></a>Cenário 1: Os utilizadores do Office 365 que planeia compram Azure
Neste cenário, podemos partem do pressuposto de que parede Pinto é um utilizador que tem uma subscrição do Office 365 e está a planear subscrever Azure. Existem dois utilizadores ativos adicionais, Inês e Sofia. Conta de Pinto é admin@contoso.onmicrosoft.com.

![Centro de administração de utilizador do Office 365](./media/billing-use-existing-office-365-account-azure-subscription/1-office365-users-admin-center.png)

Para se inscrever Azure, siga estes passos:

1. Inscrever-se no Azure na [Azure.com](https://azure.microsoft.com/). Clique em **Experimente gratuitamente**. Na página seguinte, clique em **Iniciar agora**.

    ![Experimente Azure gratuitamente.](./media/billing-use-existing-office-365-account-azure-subscription/2-azure-signup-try-free.png)

2. Clique em **Iniciar sessão com a sua conta institucional**.

    ![Inicie sessão no Azure.](./media/billing-use-existing-office-365-account-azure-subscription/3-sign-in-to-azure.png)

3. Inicie sessão com a sua conta Office 365. Neste caso, é do Office 365 de Pinto.

    ![Inicie sessão com a sua conta Office 365.](./media/billing-use-existing-office-365-account-azure-subscription/4-sign-in-with-org-account.png)

4. Preencha as informações e conclua o processo de inscrição.

    ![Preencher as informações e concluir a inscrição.](./media/billing-use-existing-office-365-account-azure-subscription/5-azure-sign-up-fill-information.png)

    ![Clique em iniciar a gerir o meu serviço.](./media/billing-use-existing-office-365-account-azure-subscription/6-azure-start-managing-my-service.png)

Agora está pronto. No portal do Azure, deverá ver os mesmos utilizadores apareça. Para verificar isto, siga estes passos:

1. Clique em **Iniciar a gerir o meu serviço** no ecrã apresentado anteriormente.
2. Clique em **Procurar**e, em seguida, clique em **Do Active Directory**.

    ![Clique em Procurar e, em seguida, clique em do Active Directory.](./media/billing-use-existing-office-365-account-azure-subscription/7-azure-portal-browse-ad.png)

3. Clique em **utilizadores**.

    ![No separador de utilizadores](./media/billing-use-existing-office-365-account-azure-subscription/8-azure-portal-ad-users-tab.png)

4. Todos os utilizadores, incluindo Pinto, são listados conforme esperado.

    ![Lista de utilizadores](./media/billing-use-existing-office-365-account-azure-subscription/9-azure-portal-ad-users.png)

<a id="s2"></a>
### <a name="scenario-2-azure-users-who-plan-to-buy-office-365"></a>Cenário 2: Os utilizadores Azure que planeia compram o Office 365

Neste cenário, Pinto parede é um utilizador que tenha uma subscrição do Azure sob a conta admin@contoso.onmicrosoft.com. Pinto pretende subscrever ao Office 365 e utilizar posteriormente, já tem Azure do diretório do mesmo.

>[AZURE.NOTE] Para obter uma subscrição do Office 365, a conta que utiliza para iniciar sessão tem de ser um membro da função de Administrador Global ou administrador de faturação diretório no inquilino do Azure Active Directory. [Saiba como saber a função no Azure Active Directory](#how-to-know-your-role-in-your-azure-active-directory).

![Definições de subscrição portal Azure](./media/billing-use-existing-office-365-account-azure-subscription/10-azure-portal-settings-subscription.png)

![Utilizadores do Active Directory do portal Azure](./media/billing-use-existing-office-365-account-azure-subscription/11-azure-portal-ads-users.png)

Para subscrever ao Office 365, siga estes passos:

1. Aceda à [página de produto do Office 365](https://products.office.com/business)e, em seguida, selecione um plano que é adequado para si.
2. Depois de selecionar o plano, é apresentada a página seguinte. Não preencha o formulário. Clique em **Iniciar sessão** no canto superior direito da página.

    ![Página de avaliação do Office 365](./media/billing-use-existing-office-365-account-azure-subscription/12-office-365-trial-page.png)

3. Inicie sessão com as credenciais da conta. Neste exemplo, é conta de Pinto.

    ![Início de sessão no Office 365](./media/billing-use-existing-office-365-account-azure-subscription/13-office-365-sign-in.png)

4. Clique em **experimentar agora**.

    ![Confirme a sua encomenda para o Office 365.](./media/billing-use-existing-office-365-account-azure-subscription/14-office-365-confirm-your-order.png)

5. Na página de recibo encomenda, clique em **continuar**.

    ![Recibo de ordem do Office 365](./media/billing-use-existing-office-365-account-azure-subscription/15-office-365-order-receipt.png)

Agora está pronto. No Centro de administração do Office 365, deverá ver os utilizadores do diretório da Contoso aparecem como utilizadores ativos. Para verificar isto, siga estes passos:

1. Abra o Centro de administração do Office 365.
2. Expanda **utilizadores**e, em seguida, clique em **Utilizadores ativos**.

    ![Utilizadores de centro de administração do Office 365](./media/billing-use-existing-office-365-account-azure-subscription/16-office-365-admin-center-users.png)

### <a name="how-to-know-your-role-in-your-azure-active-directory"></a>Como saber a sua função na sua Azure Active Directory

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Clique em **Procurar**e, em seguida, clique em **Do Active Directory**.

    ![Do Active Directory no portal do Azure](./media/billing-use-existing-office-365-account-azure-subscription/7-azure-portal-browse-ad.png)

3. Clique em **utilizadores**.

    ![Utilizadores do Azure predefinido portal do Active Directory](./media/billing-use-existing-office-365-account-azure-subscription/17-azure-portal-default-ad-users.png)

4. Clique no utilizador. Neste exemplo, o utilizador é Pinto parede.

    Repare que o campo da **Função ORGANIZACIONAL**.

    ![Identidade do utilizador portal Azure](./media/billing-use-existing-office-365-account-azure-subscription/18-azure-portal-user-identity.png)

## <a name="background-information-about-azure-and-office-365-subscriptions"></a>Obter informações gerais sobre as subscrições do Azure e no Office 365
Office 365 e Azure utilizam o serviço do Azure Active Directory para gerir utilizadores e subscrições. Considere num diretório Azure como um contentor na qual pode agrupar os utilizadores e subscrições. Para utilizar a mesma conta de utilizador para as subscrições do Azure e no Office 365, é necessário para se certificar de que as subscrições são criadas no mesmo diretório. Tenha em atenção os seguintes pontos:

- Uma subscrição é criada num diretório, não a outra forma à volta.
- Os utilizadores pertencer a directórios, não a outra forma à volta.
- Uma subscrição cair no diretório do utilizador ao qual cria a subscrição. Como resultado, a sua subscrição do Office 365 está associada à mesma conta, como a sua subscrição do Azure quando utilizar essa conta para criar a subscrição do Office 365.

![Informações de fundo](./media/billing-use-existing-office-365-account-azure-subscription/19-background-information.png)

Para mais informações, consulte o artigo [como Azure subscrições estão associadas a Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md).

>[AZURE.NOTE] Subscrições do Azure são propriedade por utilizadores individuais no diretório.

>[AZURE.NOTE] Subscrições do Office 365 são pertencentes ao próprio directório. Se os utilizadores no diretório tiverem as permissões necessárias, pode funcionam destas subscrições.

## <a name="next-steps"></a>Próximos passos
Se tiver adquirido subscrições Azure e no Office 365 em separado no passado e pretende conseguir aceder o inquilino do Office 365 da subscrição Azure, consulte o artigo [associar um inquilino do Office 365 com uma subscrição do Azure](billing-add-office-365-tenant-to-azure-subscription.md).

> [AZURE.NOTE] Se ainda tiver perguntas, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obter o seu problema resolvido rapidamente.
