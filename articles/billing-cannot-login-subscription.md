<properties
    pageTitle="Não consigo iniciar sessão subscrição do Azure | Microsoft Azure"
    description="Descreve como resolver problemas de alguns problemas de início de sessão de subscrição Azure comuns."
    services=""
    documentationCenter=""
    authors="genlin"
    manager="mbaldwin"
    editor=""
    tags="billing"
    />

<tags
    ms.service="billing"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="genli"/>

# <a name="i-cant-sign-in-to-manage-my-azure-subscription"></a>Não consigo fazer logon gerir a minha subscrição Azure

Este artigo orienta-o através de alguns dos métodos mais comuns para resolver problemas de início de sessão.

## <a name="page-hangs-in-the-loading-status"></a>Página fica bloqueada no estado de carregamento

Se a sua página do browser de internet bloqueia, experimente cada um dos seguintes passos até que pode aceder ao [Azure portal](https://portal.azure.com).

-   Atualize a página.
-   Utilize um browser diferente da Internet.
-   Se estiver a utilizar o Microsoft Internet Explorer, navegue para o portal do Azure utilizando o modo de Navegação InPrivate. 

    RESPOSTAS.  Clique em **Ferramentas** ![botão ferramentas](./media/billing-cannot-login-subscription/Toolsbutton.png) > **segurança** > **A Navegação InPrivate**.

    B.  Navegue para o [portal do Azure](https://portal.azure.com)e, em seguida, inicie sessão portal do.

## <a name="error-message-no-subscriptions-found"></a>Mensagem de erro "Sem subscrições encontradas"

Se a sua conta não possui permissões suficientes, poderá ver uma mensagem de erro **nenhuma subscrição encontrada** . Pode obter um administrador de conta para o [Centro de conta](https://account.windowsazure.com/), não a administradores do serviço (SA) ou coadministradores (AC).

**Cenário 1: A mensagem de erro é recebida no [portal do Azure](https://portal.azure.com)**

Para resolver este problema, [adicione a função de administrador de cocriação ou proprietário](billing-add-change-azure-subscription-administrator.md) da conta.

**Cenário 2: A mensagem de erro é recebida no [Centro de conta do Azure](https://account.windowsazure.com/Subscriptions)**

Verifique se a conta que utilizou o administrador da conta. Para verificar quem é o administrador da conta, siga estes passos:

1.  Inicie sessão no [portal do Azure](https://portal.azure.com).
2.  No menu concentrador, selecione a **subscrição**.
3.  Selecione a subscrição à qual pretende verificar e, em seguida, selecione **Definições**.
4.  Selecione **Propriedades**. O administrador da conta da subscrição é apresentado na caixa **Conta de administrador** .

## <a name="you-are-automatically-signed-in-as-a-different-user"></a>Automaticamente tiver sessão iniciada no como um utilizador diferente

Este problema pode ocorrer se estiver a utilizar mais do que uma conta de utilizador num browser da Internet.

Para resolver o problema, experimente um dos seguintes métodos:

-   Limpar a cache e eliminar os cookies de Internet. No Internet Explorer, clique em **Ferramentas** ![botão ferramentas](./media/billing-cannot-login-subscription/Toolsbutton.png) > **Opções da Internet** > **Eliminar**. Certifique-se de que as caixas de verificação para os ficheiros temporários, cookies, palavra-passe e histórico de navegação são selecionadas e, em seguida, clique em eliminar.

-   Repor as definições do Internet Explorer para reverter quaisquer definições pessoais que tenha feito. Clique em **Ferramentas** ![botão ferramentas](./media/billing-cannot-login-subscription/Toolsbutton.png)> **Opções da Internet** > **Avançadas** > selecione a caixa **Eliminar definições pessoais** > **Repor**.

-   Navegue até ao portal do Azure no modo de Navegação InPrivate. Clique em **Ferramentas** ![botão ferramentas](./media/billing-cannot-login-subscription/Toolsbutton.png) > **segurança** > **A Navegação InPrivate**.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte. 

Se ainda precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obter o seu problema resolvido rapidamente. 