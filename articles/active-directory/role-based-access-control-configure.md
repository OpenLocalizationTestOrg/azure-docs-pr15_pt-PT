<properties
    pageTitle="Utilizar o controlo de acesso baseado em funções no portal do Azure | Microsoft Azure"
    description="Introdução ao gestão de acesso no controlo de acesso baseado em funções no Portal do Azure. Utilize as atribuições de funções para atribuir permissões para os recursos."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="10/10/2016"
    ms.author="kgremban"/>

# <a name="use-role-assignments-to-manage-access-to-your-azure-subscription-resources"></a>Utilizar as atribuições de funções para gerir o acesso aos recursos da subscrição Azure

> [AZURE.SELECTOR]
- [Gerir o acesso ao utilizador ou grupo](role-based-access-control-manage-assignments.md)
- [Gerir o acesso por recurso](role-based-access-control-configure.md)

Azure baseado em funções acesso controlo RBCA () permite gestão de acesso extensivamente do Azure. Utilizar RBAC, pode conceder apenas a quantidade de acesso que os utilizadores têm de efetuar as suas tarefas. Este artigo ajuda-lo a começar a trabalhar com RBAC no portal do Azure. Se pretender obter mais detalhes sobre como RBAC Ajuda-o a gerir o acesso, consulte o artigo [o que é o controlo de acesso baseado em funções](role-based-access-control-what-is.md).

## <a name="view-access"></a>Vista do access
Pode ver quem tem acesso a um recurso, o grupo de recursos ou a subscrição a partir do seu pá principal no [portal do Azure](https://portal.azure.com). Por exemplo, pretendemos ver quem tem acesso a um dos nossos grupos de recurso:

1. Selecione **grupos de recursos** na barra de navegação à esquerda.  
    ![Grupos de recursos - ícone](./media/role-based-access-control-configure/resourcegroups_icon.png)
2. Selecione o nome do grupo de recursos a partir de pá de **grupos de recursos** .
3. Selecione **o controlo de acesso (IAM)** no menu à esquerda.  
4. O pá de controlo de acesso lista todos os utilizadores, grupos e aplicações que foi concedidas acesso ao grupo de recursos.  

    ![Pá utilizadores - vs herdadas atribuído captura de ecrã do access](./media/role-based-access-control-configure/view-access.png)

Aviso que alguns utilizadores foram **atribuídas** aceder enquanto outras pessoas **herdadas** -lo. Acesso está atribuído especificamente para o grupo de recursos ou herdado a partir de uma atribuição para a subscrição principal.

> [AZURE.NOTE] Administradores de subscrição clássica e administradores de co são considerados os proprietários da subscrição no modelo de RBAC novo.


## <a name="add-access"></a>Adicionar o acesso
Conceder acesso a partir do recurso, o grupo de recursos ou a subscrição que é o âmbito da atribuição de funções.

1. Selecione **Adicionar** a pá de controlo de acesso.  
2. Selecione a função que pretende atribuir a partir de pá **selecionar uma função** .
3. Selecione o utilizador, grupo ou aplicação no seu diretório que pretende conceder acesso ao. Pode procurar o diretório com nomes a apresentar, endereços de e-mail e os identificadores de objecto.  

    ![Adicionar pá utilizadores - procurar captura de ecrã](./media/role-based-access-control-configure/grant-access2.png)

4. Selecione **OK** para criar a atribuição. Menu de contexto de **Adicionar utilizador** controla o progresso.  
    ![Adicionar barras de progresso de utilizador - captura de ecrã](./media/role-based-access-control-configure/addinguser_popup.png)

Depois de adicionar com êxito uma atribuição de funções, esta será apresentada no pá a **utilizadores** .

## <a name="remove-access"></a>Remover o acesso

1. Selecione a atribuição de funções a pá de controlo de acesso.
2. Selecione **Remover** o pá de detalhes da atribuição.  
3. Selecione **Sim** para confirmar a remoção.  
    ![Pá utilizadores - remover da função captura de ecrã](./media/role-based-access-control-configure/remove-access1.png)

Atribuições herdadas não podem ser removidas. Repare na imagem abaixo cinzento no botão Remover. Em vez disso, veja os detalhes **Atribuído a** . Vá para o recurso listado aí para remover a atribuição de funções.

![Pá utilizadores - herdadas access desativa remover captura de ecrã do botão](./media/role-based-access-control-configure/remove-access2.png)

## <a name="other-tools-to-manage-access"></a>Outras ferramentas para gerir o acesso
Pode atribuir funções e gerir o acesso com comandos Azure RBAC nas ferramentas de que não seja o portal do Azure.  Siga as ligações para mais informações sobre as pré-requisitos e comece a trabalhar com os comandos do Azure RBAC.

- [Azure PowerShell](role-based-access-control-manage-access-powershell.md)
- [Interface de comandos Azure](role-based-access-control-manage-access-azure-cli.md)
- [REST API](role-based-access-control-manage-access-rest.md)

## <a name="next-steps"></a>Próximos passos
- [Criar um relatório de histórico de alteração do access](role-based-access-control-access-change-history-report.md)
- Consulte as [funções incorporadas RBAC](role-based-access-built-in-roles.md)
- Definir as suas próprias [funções personalizadas no Azure RBAC](role-based-access-control-custom-roles.md)
