<properties 
    pageTitle="Utilizar o portal do Azure para implementar o Azure recursos | Microsoft Azure" 
    description="Utilize o Azure portal e gerir o recurso Azure para implementar os recursos." 
    services="azure-resource-manager,azure-portal" 
    documentationCenter="" 
    authors="tfitzmac" 
    manager="timlt" 
    editor="tysonn"/>

<tags 
    ms.service="azure-resource-manager" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/15/2016" 
    ms.author="tomfitz"/>

# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>Implementar recursos com modelos de Gestor de recursos e o portal do Azure

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [Clip Azure](resource-group-template-deploy-cli.md)
- [Portal](resource-group-template-deploy-portal.md)
- [REST API](resource-group-template-deploy-rest.md)

Este tópico mostra como utilizar o [Azure portal](https://portal.azure.com) com o [Gestor de recursos do Azure](azure-resource-manager/resource-group-overview.md) para implementar os recursos do Azure. Para obter informações sobre como gerir os recursos, consulte o artigo [Gerir Azure recursos através do portal](./azure-portal/resource-group-portal.md).

Atualmente, não cada serviço suporta o portal ou o Gestor de recursos. Desses serviços, terá de utilizar o [portal clássica](https://manage.windowsazure.com). Para o estado de cada serviço, consulte o artigo [gráfico de disponibilidade portal Azure](https://azure.microsoft.com/features/azure-portal/availability/).

## <a name="create-resource-group"></a>Criar grupo de recursos

1. Para criar um grupo de recursos vazia, selecione **Novo** > **Gestão** > **Grupo de recursos**.

    ![Criar grupo de recursos vazia](./media/resource-group-template-deploy-portal/create-empty-group.png)

2. Dar-lhe um nome e uma localização e, se necessário, selecione uma subscrição. Tem de fornecer uma localização para o grupo de recursos, uma vez que o grupo de recursos armazena metadados sobre os recursos. Por razões de conformidade, poderá querer especificar a localização onde armazenou esse metadados. Em geral, recomendamos que especifique uma localização onde a maior parte dos seus recursos residirão. Utilizar a mesma localização, pode simplificar o modelo.

    ![valores do conjunto de grupo](./media/resource-group-template-deploy-portal/set-group-properties.png)

## <a name="deploy-resources-from-marketplace"></a>Implementar recursos a partir do Marketplace

Depois de criar um grupo de recursos, pode implementar os recursos à mesma a partir de mercado. Mercado fornece soluções predefinidas para cenários comuns.

1. Para iniciar uma implementação, selecione **Novo** e o tipo de recurso que pretende implementar. Em seguida, procure a versão específica do recurso que pretende implementar.

    ![implementar o recurso](./media/resource-group-template-deploy-portal/deploy-resource.png)

2. Se não vir a solução específica que pretende implementar, pode procurar mercado-lo.

    ![marketplace de pesquisa](./media/resource-group-template-deploy-portal/search-resource.png)

3. Dependendo do tipo de recurso seleccionado, tem de uma coleção de propriedades relevantes para definir antes da implementação. Estas opções não são apresentadas aqui, tal como estes podem variar com base no tipo de recurso. Para todos os tipos, tem de selecionar um grupo de recursos de destino. A imagem seguinte mostra como criar uma aplicação web e implementá-lo ao grupo de recursos que criou.

    ![Criar grupo de recursos](./media/resource-group-template-deploy-portal/select-existing-group.png)

    Em alternativa, pode optar por criar um grupo de recursos quando implementar os recursos. Selecione **Criar nova** e dê um nome ao grupo de recursos.

    ![Criar novo grupo de recursos](./media/resource-group-template-deploy-portal/select-new-group.png)

4. Começa a sua implementação. A implementação pode demorar alguns minutos. Quando tiver terminado a implementação, verá uma notificação.

    ![notificação de vista](./media/resource-group-template-deploy-portal/view-notification.png)

5. Depois de o implementar os recursos, pode adicionar mais recursos para o grupo de recursos ao utilizar o comando **Adicionar** no pá de grupo de recursos.

    ![Adicionar recurso](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Implementar recursos a partir do modelo personalizado

Se pretender executar uma implementação, mas não utilizar qualquer um dos modelos no mercado, pode criar um modelo personalizado que define a infraestrutura de para a sua solução. Para saber mais sobre a criação de modelos, consulte [Gestor de recursos do Azure criação de modelos](resource-group-authoring-templates.md).

1. Para implementar um modelo personalizado através do portal do, selecione **Nova**e iniciar a procura de **Implementação do modelo** , até pode selecionar a mesma nas opções.

    ![implementação do modelo de pesquisa](./media/resource-group-template-deploy-portal/search-template.png)

2. Selecione **Implementação do modelo** a partir dos recursos disponíveis.

    ![Selecione a implementação de modelo](./media/resource-group-template-deploy-portal/select-template.png)

3. Depois de iniciar a implementação do modelo, abra o modelo em branco que está disponível para personalizar.

    ![Criar modelo](./media/resource-group-template-deploy-portal/show-custom-template.png)

    No editor, adicione a sintaxe JSON que define os recursos que pretende implementar. Selecione **Guardar** quando terminar. Para obter orientações sobre como escrever a sintaxe JSON, consulte o artigo [Tutorial do Gestor de recursos do modelo](resource-manager-template-walkthrough.md).

    ![Editar modelo](./media/resource-group-template-deploy-portal/edit-template.png)

4. Em alternativa, pode selecionar um modelo pré-existentes os [modelos de guia de introdução Azure](https://azure.microsoft.com/documentation/templates/). Estes modelos são contribuiu com pela Comunidade. Abrangem vários cenários comuns e alguém adicionou um modelo que é semelhante a que está a tentar implementar. Pode procurar os modelos para encontrar algo que corresponde ao seu cenário.

    ![Selecione o modelo de guia de introdução](./media/resource-group-template-deploy-portal/select-quickstart-template.png)

    Pode ver o modelo seleccionado no editor.

5. Depois de fornecer todos os outros valores, selecione **Criar** para implementar o modelo. 

    ![implementar o modelo](./media/resource-group-template-deploy-portal/create-custom-deploy.png)

## <a name="deploy-resources-from-a-template-saved-to-your-account"></a>Implementar recursos a partir de um modelo guardado à sua conta

O portal permite-lhe guardar um modelo à sua conta Azure e implementá-lo mais tarde. Para obter mais informações sobre como trabalhar com estes guardado modelos, [começar a trabalhar com modelos privados no portal do Azure](./marketplace-consumer/mytemplates-getstarted.md).

1. Para localizar os seus modelos guardados, selecione **Procurar** > **modelos**.

    ![Procurar modelos](./media/resource-group-template-deploy-portal/browse-templates.png)

2. Na lista de modelos guardados à sua conta, selecione aquele que pretende trabalhar.

    ![modelos guardados](./media/resource-group-template-deploy-portal/saved-templates.png)

3. Selecione **Implementar** a implementar este modelo que guardou.

    ![implementar o modelo guardado](./media/resource-group-template-deploy-portal/deploy-saved-template.png)

## <a name="next-steps"></a>Próximos passos

- Para ver registos de auditoria, consulte o artigo [Auditar operações com o Gestor de recursos](resource-group-audit.md).
- Para resolver problemas de erros de implementação, consulte o artigo [implementações de grupo de recursos de resolução de problemas com o Azure portal](resource-manager-troubleshoot-deployments-portal.md).
- Para obter um modelo a partir de uma implementação ou grupo de recursos, consulte o artigo [Gestor de recursos do Azure exportar modelo a partir de recursos existentes](resource-manager-export-template.md).





