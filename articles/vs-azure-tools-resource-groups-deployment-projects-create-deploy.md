<properties
   pageTitle="Projetos de grupo de recursos Visual Studio Azure | Microsoft Azure"
   description="Utilize o Visual Studio para criar um projeto de grupo de recursos Azure e implementar os recursos para Azure."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn" />
<tags
   ms.service="azure-resource-manager"
   ms.devlang="multiple"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/20/2016"
   ms.author="tomfitz" />

# <a name="creating-and-deploying-azure-resource-groups-through-visual-studio"></a>Criar e implementar o grupos de recursos Azure através do Visual Studio

Com o Visual Studio e o [Azure SDK](https://azure.microsoft.com/downloads/), pode criar um projeto que implementa o código de infraestruturas e Azure. Por exemplo, pode definir o anfitrião da web, o web site e a base de dados para a sua aplicação e implementar essa infraestrutura juntamente com o código. Em alternativa, pode definir uma Máquina Virtual, a rede Virtual e a conta de armazenamento e implementar essa infraestrutura juntamente com um script que é executada numa máquina Virtual. O projeto de implementação do **Grupo de recursos do Azure** permite-lhe implementar todos os recursos necessários numa operação de forma repetida, único. Para mais informações sobre como implementar e gerir os recursos, consulte o artigo [Descrição geral do Gestor de recursos do Azure](azure-resource-manager/resource-group-overview.md).

Projetos de grupo de recursos do Azure contêm modelos de JSON de Gestor de recursos do Azure, que definam os recursos que implemente Azure. Para saber mais sobre os elementos do modelo de Gestor de recursos, consulte [Gestor de recursos do Azure criação de modelos](resource-group-authoring-templates.md). Visual Studio permite-lhe editar estes modelos e fornece ferramentas que simplificam o trabalho com modelos.

Neste tópico, implementar uma aplicação web e a base de dados SQL. No entanto, os passos são quase o mesmo para qualquer tipo de recurso. É possível como implementar facilmente uma Máquina Virtual e os seus recursos relacionados. Visual Studio fornece muitos modelos de starter diferentes para implementar cenários comuns.

Este artigo mostra o Visual Studio 2015 Update 2 e Microsoft Azure SDK do .NET 2.9. Se utiliza o Visual Studio 2013 com Azure SDK 2.9, sua experiência em grande medida é a mesma. Pode utilizar versões do SDK Azure a partir do 2.6 ou posterior; No entanto, a sua experiência da interface de utilizador pode ser diferente que a interface de utilizador neste artigo. Recomendamos vivamente que instale a versão mais recente do [Azure SDK](https://azure.microsoft.com/downloads/) antes de iniciar os passos. 

## <a name="create-azure-resource-group-project"></a>Projeto de criar grupo de recursos do Azure

Este procedimento, pode criar um projeto de grupo de recursos do Azure com um modelo de **aplicação Web + SQL** .

1. No Visual Studio, selecione o **ficheiro**, o **Novo projeto**, selecione **c#** ou o **Visual Basic**. Em seguida, selecione **na nuvem**e, em seguida, escolha projeto de **Grupo de recursos do Azure** .

    ![Projecto de implementação na nuvem](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-project.png)

1. Selecione o modelo que pretende implementar para o Gestor de recursos do Azure. Não existem aviso são várias opções diferentes, consoante o tipo de projeto que pretende implementar. Para este tópico, escolha o modelo de **aplicação Web + SQL** .

    ![Escolha um modelo](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-project.png)

    O modelo que escolher está apenas um ponto de partida; Pode adicionar e remover recursos para cumprir o seu cenário.

    >[AZURE.NOTE] Visual Studio obtém uma lista de modelos disponíveis online. Pode alterar a lista.

    Visual Studio cria um projeto de implementação do grupo de recursos para a web app e a base de dados SQL.

1. Para ver o que criou, expanda os nós no projeto implementação.

    ![Mostrar nós](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-items.png)

    Uma vez que optamos pela aplicação Web + modelo SQL para neste exemplo, verá os seguintes ficheiros: 

  	|Nome do ficheiro|Descrição|
  	|---|---|
  	|AzureResourceGroup.ps1 implementar|Um script do PowerShell invoca comandos do PowerShell para implementar o Gestor de recursos para Azure.<br />**Nota** Visual Studio utiliza este script do PowerShell para implementar o modelo. Quaisquer alterações realizadas para este script afeta implementação no Visual Studio, pelo que tenha o cuidado de.|
  	|WebSiteSQLDatabase.json|O modelo de Gestor de recursos que define a infraestrutura de que pretende implementar Azure e os parâmetros que pode fornecer durante a implementação. Também define as dependências entre os recursos para os recursos na ordem correta implementa o Gestor de recursos.|
  	|WebSiteSQLDatabase.parameters.json|Um ficheiro de parâmetros que contém valores necessários pelo modelo. Passar valores de parâmetro para personalizar cada implementação.|

    Todos os projetos de implementação do grupo de recursos contêm estes ficheiros básicos. Outros projetos podem conter ficheiros adicionais para suportar outras funcionalidades.

## <a name="customize-the-resource-manager-template"></a>Personalizar o modelo de Gestor de recursos

Pode personalizar um projeto de implementação modificando os modelos JSON que descrevem os recursos que pretende implementar. JSON designa JavaScript Object Notation e é um formato de série de dados que seja fácil de trabalhar com. Os ficheiros JSON utilizam um esquema que fizer referência na parte superior de cada ficheiro. Se pretender compreender o esquema, pode transferir e analisá-los. O esquema define que elementos são válidos, os tipos de e formatos de campos, os valores possíveis de valores enumerados e assim sucessivamente. Para saber mais sobre os elementos do modelo de Gestor de recursos, consulte [Gestor de recursos do Azure criação de modelos](resource-group-authoring-templates.md).

Para trabalhar no seu modelo, abra **WebSiteSQLDatabase.json**.

O editor do Visual Studio fornece ferramentas para ajudá-lo com a editar o modelo de Gestor de recursos. A janela **Hierárquica de JSON** torna mais fácil ver os elementos definidos no seu modelo.

![Mostrar JSON destaque](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-json-outline.png)

Selecionar qualquer um dos elementos no destaque leva-o para essa parte do modelo e realça o JSON correspondente.

![navegar JSON](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/navigate-json.png)

Pode adicionar um recurso seleccionando o botão **Adicionar recursos** no topo da janela hierárquica de tópicos JSON ou ao clicar **recursos** e selecionar **Adicionar novo recurso**.

![Adicionar recurso](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource.png)

Para este tutorial, selecione a **Conta de armazenamento** e atribua um nome. Forneça um nome mais do que 11 caracteres e que contém apenas números e letras minúsculas.

![adicionar armazenamento](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-storage.png)

Repare que apenas não foi o recurso adicionado, mas também um parâmetro para a conta de armazenamento do tipo e uma variável para o nome da conta de armazenamento.

![Mostrar destaque](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-new-items.png)

O parâmetro **storageType** é predefinido com tipos de permitidos e um tipo predefinido. Pode deixar estes valores ou edite-os para o seu cenário. Se não pretender que qualquer pessoa para implementar uma conta de armazenamento **Premium_LRS** através este modelo, remova-a partir de tipos de permitidos. 

    "storageType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_ZRS",
        "Standard_GRS",
        "Standard_RAGRS"
      ]
    }

O Visual Studio fornece também intellisense para ajudar a compreender o que propriedades estão disponíveis quando edita o modelo. Por exemplo, para editar as propriedades para o seu plano de serviço de aplicação, navegue para o recurso **HostingPlan** e adicione um valor para as **Propriedades**. Repare que o intellisense mostra os valores disponíveis e fornece uma descrição desse valor.

![Mostrar o intellisense](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-intellisense.png)

Pode definir **numberOfWorkers** a 1.

    "properties": {
      "name": "[parameters('hostingPlanName')]",
      "numberOfWorkers": 1
    }

## <a name="deploy-the-resource-group-project-to-azure"></a>Implementar o projeto de grupo de recursos para Azure

Agora está pronto para implementar o projeto. Quando implementar um projeto de grupo de recursos do Azure, implemente-o a um grupo de recursos Azure. O grupo de recursos é um agrupamento de recursos que partilham um ciclo de vida comuns lógico.

1. No menu de atalho do nó de projeto de implementação, selecione **Implementar** > **Nova implementação**.

    ![Implementar, item de menu novo implementação](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/deploy.png)

    É apresentada a caixa de diálogo **Implementar a grupo de recursos** .

    ![Implementar a caixa de diálogo grupo de recursos](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployment.png)

1. Na caixa de lista pendente de **grupo de recursos** , selecione um grupo de recursos existente ou crie um novo. Para criar um grupo de recursos, abra a caixa de lista pendente do **Grupo de recursos** e selecione **Criar novo**.

    ![Implementar a caixa de diálogo grupo de recursos](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-new-group.png)

    É apresentada a caixa de diálogo **Criar grupo de recursos** . Dê ao seu grupo um nome e uma localização e, selecione o botão **Criar** .

    ![Criar a caixa de diálogo grupo de recursos](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-resource-group.png)
   
1. Edite os parâmetros para a implementação ao selecionar o botão **Editar parâmetros** .

    ![Imagem de botão de parâmetros](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/edit-parameters.png)

1. Forneça os valores para os parâmetros vazios e selecione o botão **Guardar** . Os parâmetros vazios são **hostingPlanName**, **administratorLogin**, **administratorLoginPassword**e **databaseName**.

    **hostingPlanName** Especifica um nome para o [plano de serviço de aplicação](./app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) criar. 
    
    **administratorLogin** Especifica o nome de utilizador para o administrador do SQL Server. Não utilize nomes de administração comuns, como **sa** ou **administrador**. 
    
    O **administratorLoginPassword** Especifica uma palavra-passe de administrador do SQL Server. A opção **guardar palavras-passe como texto simples no ficheiro de parâmetros** não está protegida; Por conseguinte, não selecione esta opção. Uma vez que a palavra-passe não é guardada como texto simples, terá de fornecer esta palavra-passe novamente durante a implementação. 
    
    **databaseName** Especifica um nome para a base de dados criar. 

    ![Caixa de diálogo parâmetros de editar](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/provide-parameters.png)
    
1. Selecione o botão **Implementar** para implementar o projeto Azure. É aberta uma consola do PowerShell fora da instância do Visual Studio. Introduza a palavra-passe de administrador do SQL Server na consola do PowerShell, quando lhe for pedido. **Consola PowerShell poderá estar oculta por trás de outros itens ou minimizada na barra de tarefas.** Procure esta consola e selecione-a para fornecer a palavra-passe.

    >[AZURE.NOTE] Visual Studio pode pedir-lhe instalar os cmdlets do Azure PowerShell. Precisa dos cmdlets do Azure PowerShell para implementar com êxito grupos de recursos. Se lhe for pedido, instale-os.
    
1. A implementação poderá demorar alguns minutos. No windows **saída** , verá o estado da implementação. Quando tiver terminado a implementação, a última mensagem indica uma implementação com êxito com algo semelhante ao:

        ... 
        18:00:58 - Successfully deployed template 'c:\users\user\documents\visual studio 2015\projects\azureresourcegroup1\azureresourcegroup1\templates\websitesqldatabase.json' to resource group 'DemoSiteGroup'.


1. Num browser, abra o [portal do Azure](https://portal.azure.com/) e inicie sessão na sua conta. Para ver o grupo de recursos, selecione **grupos de recursos** e o grupo de recursos que implementado.

    ![Selecione o grupo](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-group.png)

1. Ver todos os recursos implementados. Repare que o nome da conta de armazenamento é não exatamente o que especificou quando adicionar esse recurso. A conta de armazenamento tem de ser exclusiva. O modelo adiciona automaticamente uma cadeia de carateres para o nome que forneceu para fornecer um nome exclusivo. 

    ![Mostrar recursos](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-resources.png)

1. Se efetuar alterações e pretende implementar o projeto, selecione o grupo de recursos existente no menu de atalho do projeto de grupo de recursos Azure. No menu de atalho, selecione a **Implementar**e, em seguida, selecione o grupo de recursos que implementado.

    ![Grupo de recursos Azure implementado](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/redeploy.png)

## <a name="deploy-code-with-your-infrastructure"></a>Implementar código com a sua infraestrutura de

Neste momento, ter implementado a infraestrutura de para a sua aplicação, mas não existe nenhum código real implementado ao projeto. Este tópico mostra como implementar uma aplicação web e tabelas de base de dados SQL durante a implementação. Se estiver a implementar uma Máquina Virtual em vez de uma aplicação web, que pretende executar algum código no computador como parte de implementação. O processo de implementação de código para uma aplicação web ou para a configuração de uma Máquina Virtual é praticamente igual.

1. Adicione um projeto a sua solução do Visual Studio. A solução com o botão direito e selecione **Adicionar** > **Novo projeto**.

    ![adicionar o project](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-project.png)

1. Adicione uma **aplicação Web do ASP.NET**. 

    ![Adicionar aplicação web](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-app.png)
    
1. Selecione **MVC** e limpe o campo do **anfitrião na nuvem** , uma vez que o projeto de grupo de recursos executa essa tarefa.

    ![Selecione MVC](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-mvc.png)
    
1. Após criar a aplicação web, o Visual Studio, verá ambos os projetos da solução.

    ![Mostrar projetos](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-projects.png)

1. Agora, é necessário para se certificar de que o seu projeto de grupo de recursos está em atenção o novo projeto. Volte ao seu projeto de grupo de recursos (AzureResourceGroup1). Clique no separador **referências** e selecione **Adicionar referência**.

    ![Adicionar uma referência](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-new-reference.png)

1. Selecione o projecto da aplicação web que criou.

    ![Adicionar uma referência](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-reference.png)
    
    Ao adicionar uma referência, ligar o projecto da aplicação web para o projeto de grupo de recursos e configurar automaticamente as propriedades da chave três. Consulte estas propriedades na janela de **Propriedades** para a referência.

      ![consulte o artigo referência](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/see-reference.png)
    
    As propriedades são:

    - As **Propriedades adicionais** contém o pacote de implementação de web teste a localização que é enviada para o armazenamento do Windows Azure. Tenha em atenção a pasta (ExampleApp) e o ficheiro (package.zip). Irá fornecer estes valores como parâmetros quando implementar a aplicação. 
    - O **Caminho do ficheiro incluir** contém o caminho onde o pacote é criado. Os **Destinos incluir** contém o comando que executa implementação. 
    - O valor predefinido de **construir; Pacote** permite a implementação para criar e criar um pacote de implementação de web (package.zip).  
    
    Não tem um perfil de publicar como a implementação obtém as informações necessárias a partir de propriedades para criar o pacote.
      
1. Adicione um recurso para o modelo.

    ![Adicionar recurso](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource-2.png)

1. Desta vez, selecione **Web implementar para Web Apps**. 

    ![Adicionar web implementar](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-web-deploy.png)
    
1. Implementar novamente o seu projeto de grupo de recursos ao grupo de recursos. Desta vez existem algumas novos parâmetros. Não tem de fornecer valores para **_artifactsLocation** ou **_artifactsLocationSasToken** porque o Visual Studio gera automaticamente esses valores. No entanto, tem de definir a pasta e o nome de ficheiro para o caminho que contém o pacote de implementação (mostrado como **ExampleAppPackageFolder** e **ExampleAppPackageFileName** na seguinte imagem). Forneça os valores que viu anteriormente nas propriedades de referência (**ExampleApp** e **package.zip**).

    ![Adicionar web implementar](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/set-new-parameters.png)
    
    Para a **conta de armazenamento artefacto**, selecione a implementado com este grupo de recursos.
    
1. Depois da implementação for concluída, selecione a aplicação web no portal. Selecione o URL para navegar para o site.

    ![Procurar site](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/browse-site.png)

1. Repare que tenham implementado a aplicação ASP.NET predefinida com êxito.

    ![Mostrar a aplicação implementada](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-app.png)

## <a name="next-steps"></a>Próximos passos

- Para saber mais sobre a gestão dos seus recursos através do portal do, consulte [utilizar o portal do Azure para gerir os seus recursos Azure](./azure-portal/resource-group-portal.md).
- Para saber mais acerca dos modelos, consulte o artigo [modelos de criação de Gestor de recursos do Azure](resource-group-authoring-templates.md).
