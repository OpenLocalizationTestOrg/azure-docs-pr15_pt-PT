<properties
    pageTitle="Aprovisionar e implementar microservices seja previsível no Azure"
    description="Saiba como implementar uma aplicação composto por microservices na aplicação de serviço de Azure como uma única unidade e de uma forma previsível utilizar modelos de grupo de recursos JSON e os scripts de PowerShell."
    services="app-service"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor="jimbe"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/06/2016"
    ms.author="cephalin"/>


# <a name="provision-and-deploy-microservices-predictably-in-azure"></a>Aprovisionar e implementar microservices seja previsível no Azure #

Este tutorial mostra como aprovisionar e implementar uma aplicação composto por [microservices](https://en.wikipedia.org/wiki/Microservices) na [Aplicação de serviço de Azure](/services/app-service/) como uma única unidade e de uma forma previsível utilizar modelos de grupo de recursos JSON e os scripts de PowerShell. 

Quando aprovisiona e implementar aplicações de escala de alta que são compostas pelos altamente desacoplado microservices, repetibilidade e previsibilidade são cruciais para success. [Aplicação de serviço de Azure](/services/app-service/) permite-lhe criar microservices que incluem aplicações web, aplicações móveis, aplicações API e apps lógica. [Gestor de recursos do Azure](../azure-resource-manager/resource-group-overview.md) permite-lhe gerir todas as microservices como uma unidade, juntamente com dependências de recursos, tal como a base de dados e as definições de controlo da origem. Agora, também pode implementar uma aplicação de utilizar modelos JSON e os scripts de PowerShell simples. 

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="what-you-will-do"></a>O que vai fazer ##

Tutorial, irá implementar uma aplicação que inclui:

-   Dois web apps (ou seja, dois microservices)
-   Uma base de dados SQL de back-end
-   Definições de aplicação, as cadeias de ligação e controlo da origem
-   Definições de autoscaling, alertas, informações de aplicação

## <a name="tools-you-will-use"></a>Ferramentas que irá utilizar ##

Neste tutorial, irá utilizar as seguintes ferramentas. Uma vez que não é exaustiva debate em ferramentas, vou para continuar ao cenário de ponto a ponto e apenas dar-lhe uma breve introdução a cada, e onde pode encontrar mais informações no mesmo. 

### <a name="azure-resource-manager-templates-json"></a>Modelos de Gestor de recursos Azure (JSON) ###
 
Sempre que criar uma aplicação web na aplicação de serviço de Azure, por exemplo, Gestor de recursos do Azure utiliza um modelo JSON para criar o grupo de recursos inteiro com os recursos de componente. Um modelo a partir do [Azure Marketplace](/marketplace) como a aplicação de [WordPress dimensionáveis](/marketplace/partners/wordpress/scalablewordpress/) pode incluir a base de dados do MySQL, contas de armazenamento, o plano de serviço de aplicação, a aplicação web propriamente dito, complexo regras de alertas, as definições da aplicação, as definições de autoscale e mais e todos os estes modelos estão disponíveis para si através do PowerShell. Para obter informações sobre como transferir e utilizar estes modelos, consulte o artigo [Utilizar o PowerShell Azure com o Gestor de recursos do Azure](../powershell-azure-resource-manager.md).

Para mais informações sobre os modelos de Gestor de recursos do Azure, consulte o artigo [Criação de modelos de Gestor de recursos do Azure](../resource-group-authoring-templates.md)

### <a name="azure-sdk-26-for-visual-studio"></a>Azure SDK 2.6 para Visual Studio ###

O mais recente SDK contém melhorias para o suporte de modelo de Gestor de recursos no editor de JSON. Pode Utilize esta opção para criar um modelo de grupo de recursos do zero ou abrir um modelo JSON existente (tal como um modelo da Galeria transferido) rapidamente para modificação, preencher o ficheiro de parâmetros e até mesmo implementar o grupo de recursos diretamente a partir de uma solução de grupo de recursos do Azure.

Para mais informações, consulte o artigo [Azure SDK 2.6 para Visual Studio](/blog/2015/04/29/announcing-the-azure-sdk-2-6-for-net/).

### <a name="azure-powershell-080-or-later"></a>Azure PowerShell 0.8.0 ou posterior ###

Começando na versão 0.8.0, a instalação do Azure PowerShell inclui o módulo Azure o Gestor de recursos para além do módulo Azure. Este novo módulo permite-lhe o script de implementação de grupos de recursos.

Para obter mais informações, consulte o artigo [Utilizar o PowerShell Azure com o Gestor de recursos do Azure](../powershell-azure-resource-manager.md)

### <a name="azure-resource-explorer"></a>Recurso Azure Explorer ###

Esta [ferramenta de pré-visualização](https://resources.azure.com) permite-lhe explorar as definições de JSON de todos os grupos de recursos na sua subscrição e os recursos individuais. Na ferramenta de, pode editar as definições de JSON de um recurso, eliminar uma hierarquia inteira de recursos e criar novos recursos.  As informações facilmente disponíveis nesta ferramenta são muito útil para a criação de modelo, uma vez que mostra que propriedades tem de definir para um determinado tipo de recurso, os valores corretos, etc. Ainda pode criar o seu grupo de recursos no [Portal do Azure](https://portal.azure.com/), depois inspecionar as suas definições de JSON na ferramenta de explorer para ajudá-lo a templatize o grupo de recursos.

### <a name="deploy-to-azure-button"></a>Implementar a botão Azure ###

Se utiliza o GitHub para controlo de origem, pode colocar uma [Implementar ao botão Azure](/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/) para o seu ficheiro Leia-me. MD, que permite a uma implementação de chave de ativar UI para Azure. Enquanto pode fazê-lo para qualquer simples web app, pode expandir esta opção para activar implementar um grupo de recursos inteira ao colocar um ficheiro de azuredeploy.json na raiz da repositório. Este ficheiro JSON, que contém o modelo de grupo de recursos, será utilizado para criar o grupo de recursos por implementar ao botão Azure. Por exemplo, consulte o artigo amostra [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) , que irá utilizar neste tutorial.

## <a name="get-the-sample-resource-group-template"></a>Obter o modelo de grupo de recursos de exemplo ##

Agora vamos obter para a direita para a mesma.

1.  Navegue para a amostra de aplicação de serviço [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) .

2.   No readme.md, clique em **implementar para Azure**.
 
3.  Está redirecionado para o site de [implementar para azure](https://deploy.azure.com) e -lhe pedido para implementação parâmetros de entrada. Repare que a maioria dos campos é propagada com o nome do repositório e algumas cadeias da aleatórias para si. Pode alterar todos os campos se pretender, mas as coisas só que tem de introduzir são login administrativo do SQL Server e a palavra-passe, em seguida, clique em **seguinte**.
 
    ![](./media/app-service-deploy-complex-application-predictably/gettemplate-1-deploybuttonui.png)

4.  Em seguida, clique em **Implementar** para iniciar o processo de implementação. Quando executa o processo para conclusão, clique na http://todoapp*XXXX*. azurewebsites.net ligação para procurar a aplicação implementada. 

    ![](./media/app-service-deploy-complex-application-predictably/gettemplate-2-deployprogress.png)

    IU seria ser um pouco lenta ao primeiro navegar para a mesma uma vez que as aplicações estão apenas a iniciar, mas convencer-se de que é uma aplicação totalmente funcional.

5.  Novamente na página de implementar, clique na ligação **Gerir** para ver a nova aplicação no Portal do Azure.

6.  Na lista pendente **Essentials** , clique na ligação do grupo de recursos. Tenha também em atenção que a aplicação web já está ligada para o repositório de GitHub em **Projecto externo**. 

    ![](./media/app-service-deploy-complex-application-predictably/gettemplate-3-portalresourcegroup.png)
 
7.  No pá de grupo de recursos, tenha em atenção que já existem duas aplicações web e uma base de dados do SQL no grupo de recursos.

    ![](./media/app-service-deploy-complex-application-predictably/gettemplate-4-portalresourcegroupclicked.png)
 
Tudo o que acabou de ver alguns minutos breve é uma aplicação de duas-microservice totalmente implementado, com todos os componentes, dependências, definições, base de dados e a publicação contínua, configurada por um orchestration automatizado no Gestor de recursos do Azure. Tudo isto foi feito por dois aspetos:

-   Implementar a botão Azure
-   azuredeploy.JSON na raiz da repo

Pode implementar esta aplicação mesmo dezenas, centenas ou milhares de horas e têm a mesma configuração sempre exata. A repetibilidade e previsibilidade desta abordagem permite-lhe implementar aplicações de escala de alta com facilidade e de confiança.

## <a name="examine-or-edit-azuredeployjson"></a>Examinar (ou editar) AZUREDEPLOY. JSON ##

Agora vamos ver como o repositório GitHub tiver sido configurado. Será utilizando o editor de JSON no Azure .NET SDK, por isso, se ainda não instalou já [Azure .NET SDK 2.6](/downloads/), fazê-lo agora.

1.  Clonar do repositório de [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) utilizando a ferramenta de git favorito. Na captura de ecrã abaixo, posso estou a fazê-lo no Explorador de equipa no Visual Studio 2013.

    ![](./media/app-service-deploy-complex-application-predictably/examinejson-1-vsclone.png)

2.  A partir da raiz do repositório, abra azuredeploy.json no Visual Studio. Se não vir o painel de destaques de JSON, terá de instalar o Azure .NET SDK.

    ![](./media/app-service-deploy-complex-application-predictably/examinejson-2-vsjsoneditor.png)

Não estou a passar para descrever todos os detalhes do formato JSON, mas a secção [Mais recursos](#resources) tem ligações para conhecer o idioma de modelo de grupo de recursos. Aqui, basta vou mostrar-lhe as funcionalidades interessantes que podem ajudar a começar a fazer o seu próprio modelo personalizado para implementação de aplicações.

### <a name="parameters"></a>Parâmetros ###

Veja a secção de parâmetros para ver que a maioria destes parâmetros são o que o botão **implementar para Azure** pede-lhe um ficheiro. O site atrás do botão **implementar para Azure** preenche a entrada da IU utilizando os parâmetros definidos no azuredeploy.json. Estes parâmetros são utilizados em toda as definições de recursos, como nomes de recursos, valores de propriedade, etc.

### <a name="resources"></a>Recursos ###

O nó de recursos, pode ver o que são definidos 4 recursos de nível superiores, incluindo uma instância do SQL Server, um plano de serviço de aplicação e duas aplicações web. 

#### <a name="app-service-plan"></a>Plano de serviço de aplicação ####

Vamos começar com um recurso de nível de raiz simple na JSON. Destaque JSON, clique no plano de serviço de aplicações com o nome **[hostingPlanName]** para realçar o código JSON correspondente. 

![](./media/app-service-deploy-complex-application-predictably/examinejson-3-appserviceplan.png)

Tenha em atenção que a `type` elemento Especifica a cadeia para um plano de serviço de aplicação (era denominado um farm de servidores vez longo e longo há) e outros elementos e propriedades são preenchidas utilizando os parâmetros definidos no ficheiro JSON e este recurso não tem qualquer recursos aninhados.

>[AZURE.NOTE] Tenha também em atenção que o valor de `apiVersion` indica qual a versão do API REST para utilizar a definição de recurso JSON com e pode afetar o modo como o recurso deve ser formatado dentro do Azure a `{}`. 

#### <a name="sql-server"></a>Do SQL Server ####

Em seguida, clique em recurso do SQL Server chamado **SQLServer** no destaque JSON.

![](./media/app-service-deploy-complex-application-predictably/examinejson-4-sqlserver.png)
 
Tenha em atenção as seguintes, sobre o código JSON realçado:

-   A utilização dos parâmetros assegura que os recursos criados são com o nome e configurados de forma que faz com que sejam consistentes com um do outro.
-   O recurso SQLServer tem dois recursos aninhados, cada uma com um valor diferente para `type`.
-   Os recursos aninhados dentro `“resources”: […]`, onde são definidas a base de dados e as regras de firewall, tem uma `dependsOn` elemento que especifica o ID do recurso do recurso SQLServer nível de raiz. Isto indica que a Gestor de recursos do Azure, "antes de criar este recurso, que já tem de existir outro recurso; e se esse outro recurso é definido no modelo, em seguida, criar que um primeiro".

    >[AZURE.NOTE] Para obter informações detalhadas sobre como utilizar o `resourceId()` função, consulte [Funções de modelo de Gestor de recursos do Azure](../resource-group-template-functions.md).

-   Efeito de `dependsOn` elemento é que o Gestor de recursos do Azure pode saber quais os recursos que podem ser criados em paralelo e quais os recursos que têm de ser criados sequencialmente. 

#### <a name="web-app"></a>Aplicação Web ####

Agora, vamos avance para o real web apps si, que são mais complicado. Clique na aplicação web do [variables('apiSiteName')] no destaque JSON para realçar o respectivo código JSON. Notará que coisas estão a obter muito mais interessantes. O efeito, posso falar-me sobre as funcionalidades de um a um:

##### <a name="root-resource"></a>Recurso de raiz #####

A aplicação web depende dois recursos diferentes. Isto significa que o Gestor de recursos do Azure irá criar a aplicação web apenas depois do plano de serviço de aplicação e instância do SQL Server são criadas.

![](./media/app-service-deploy-complex-application-predictably/examinejson-5-webapproot.png)

##### <a name="app-settings"></a>Definições da aplicação #####

As definições de aplicação também são definidas como um recurso aninhado.

![](./media/app-service-deploy-complex-application-predictably/examinejson-6-webappsettings.png)

No `properties` elemento para `config/appsettings`, tem duas definições de aplicação no formato `“<name>” : “<value>”`.

-   `PROJECT`é uma [definição de KUDU](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) que indica que projecto para utilizar numa solução Visual Studio project com várias de implementação do Azure. Posso irá mostrar-lhe mais tarde como o controlo da origem de está configurado, mas uma vez que é o código de ToDoApp numa solução com várias projeto Visual Studio, precisamos desta definição.
-   `clientUrl`é utiliza simplesmente uma aplicação a definição de que o código da aplicação.

##### <a name="connection-strings"></a>Cadeias de ligação #####

Cadeias de ligação também são definidas como um recurso aninhado.

![](./media/app-service-deploy-complex-application-predictably/examinejson-7-webappconnstr.png)

No `properties` elemento para `config/connectionstrings`, cada cadeia de ligação também é definida como um par de valor: nome, com a formatação específica da `“<name>” : {“value”: “…”, “type”: “…”}`. Para o `type` elemento, os valores possíveis são `MySql`, `SQLServer`, `SQLAzure`, e `Custom`.

>[AZURE.TIP] Para obter uma lista dos tipos de cadeia de ligação definitiva, execute o seguinte comando no Azure PowerShell: \[Enum]::GetNames("Microsoft.WindowsAzure.Commands.Utilities.Websites.Services.WebEntities.DatabaseType")
    
##### <a name="source-control"></a>Controlo da origem #####

As definições de controlo da origem também são definidas como um recurso aninhado. Gestor de recursos do Azure utiliza este recurso para configurar a publicação de contínua (consulte o artigo truque no `IsManualIntegration` mais tarde) e também para iniciar a implementação do código da aplicação automaticamente durante o processamento do ficheiro JSON.

![](./media/app-service-deploy-complex-application-predictably/examinejson-8-webappsourcecontrol.png)

`RepoUrl`e `branch` deve ser muito intuitiva e devem apontar para o repositório de Git e o nome do ramo a publicar a partir de. Novamente, estes são definidos pelas parâmetros de entrada. 

Tenha em atenção na `dependsOn` elemento que, para além do recurso de aplicação web propriamente dito, `sourcecontrols/web` também depende `config/appsettings` e `config/connectionstrings`. Isto acontece porque assim `sourcecontrols/web` está configurado, o processo de implementação do Azure tentará automaticamente implementar, criar e iniciar o código da aplicação. Por conseguinte, a inserir esta dependência ajuda a certificar-se de que a aplicação tem acesso às definições da aplicação necessários e cadeias de ligação antes do código da aplicação é executado. 

>[AZURE.NOTE] Tenha também em atenção que `IsManualIntegration` está definido para `true`. Esta propriedade é necessária neste tutorial, uma vez que não é realmente o proprietário do repositório de GitHub e, por conseguinte, não é realmente conceder permissão ao Azure para configurar a publicação contínua a partir [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) (isto é atualizações automáticas repositório de emissão para Azure). Pode utilizar o valor predefinido `false` para o repositório especificado apenas se tiver configurado as credenciais de GitHub o proprietário no [portal do Azure](https://portal.azure.com/) antes. Por outras palavras, se configurou controlo da origem para GitHub ou BitBucket para qualquer aplicação no [Portal do Azure](https://portal.azure.com/) anteriormente, com as suas credenciais de utilizador, em seguida, Azure irá Lembre-se as credenciais e utilizá-los sempre implementar qualquer aplicação a partir do GitHub ou BitBucket no futuro. No entanto, se ainda não o tiver feito já, implementação do modelo JSON falhará quando tenta Azure o Gestor de recursos configurar as definições de controlo de origem do web app, uma vez que não consegue a iniciar sessão para GitHub ou BitBucket com credenciais o proprietário de repositório.

## <a name="compare-the-json-template-with-deployed-resource-group"></a>Comparar o modelo JSON com o grupo de recursos implementado ##

Aqui, pode percorrer pás do todos os a aplicação web no [Portal do Azure](https://portal.azure.com/), mas existe outra ferramenta que tal como se útil, não é muito mais. Vá para a ferramenta de pré-visualização do [Azure recurso Explorer](https://resources.azure.com) , que lhe dá uma representação JSON de todos os grupos de recursos nas suas subscrições, tal como as que realmente existam no Azure back-end. Também pode ver como a hierarquia JSON o grupo de recursos no Azure corresponde com a hierarquia no ficheiro de modelo que é utilizado para criá-lo.

Por exemplo, quando posso ir para a ferramenta [Do Explorer de recursos do Azure](https://resources.azure.com) e expanda os nós no Explorador de, pode vejo o grupo de recursos e os recursos de nível de raiz que são recolhidos nos tipos de recursos respetivos.

![](./media/app-service-deploy-complex-application-predictably/ARM-1-treeview.png)

Se desagregar uma aplicação web, deverá conseguir ver detalhes de configuração de aplicação web semelhantes a abaixo captura de ecrã:

![](./media/app-service-deploy-complex-application-predictably/ARM-2-jsonview.png)

Novamente, os recursos aninhados devem ter uma hierarquia muito semelhante no seu ficheiro de modelo JSON e deverá ver as definições da aplicação, cadeias de ligação, etc., corretamente refletidas no painel de JSON. Falta de definições aqui pode indicar um problema com o seu ficheiro JSON e pode ajudar a resolver o seu ficheiro de modelo JSON.

## <a name="deploy-the-resource-group-template-yourself"></a>Implementar o modelo de grupo de recursos si próprio ##

O botão **implementar para Azure** é ótimo, mas permite-lhe implementar o modelo de grupo de recursos no azuredeploy.json apenas se já tiver seguia azuredeploy.json para GitHub. O Azure .NET SDK também fornece as ferramentas que pode implementar qualquer ficheiro de modelo JSON diretamente a partir do seu computador local. Para fazer isto, siga os passos abaixo:

1.  No Visual Studio, clique em **ficheiro** > **Novo** > **projeto**.

2.  Clique em **Visual c#** > **nuvem** > **Grupo de recursos do Azure**, em seguida, clique em **OK**.

    ![](./media/app-service-deploy-complex-application-predictably/deploy-1-vsproject.png)

3.  **Selecione o Azure modelo**, selecione **Modelo em branco** e clique em **OK**.

4.  Arraste azuredeploy.json para a pasta de **modelo** de um novo projeto.

    ![](./media/app-service-deploy-complex-application-predictably/deploy-2-copyjson.png)

5.  Solução Explorer, abra o azuredeploy.json copiada.

6.  Apenas por motivos de demonstração, vamos adicionar alguns recursos de conhecimentos aprofundados de aplicação padrão ao nosso JSON ficheiro, ao clicar em **Adicionar recursos**. Se estiver interessado apenas implementar o ficheiro JSON, avance para os passos de implementar.

    ![](./media/app-service-deploy-complex-application-predictably/deploy-3-newresource.png)

7.  Selecione **Informações de aplicação para aplicações Web**, em seguida, certifique-se de um serviço de aplicação existente planear e web app está selecionada e, em seguida, clique em **Adicionar**.

    ![](./media/app-service-deploy-complex-application-predictably/deploy-4-newappinsight.png)

    Agora poderá ver várias novos recursos que, dependendo do recurso e o que faz, ter dependências no plano de serviço de aplicação ou a aplicação web. Estes recursos não estão ativados para sua definição existente e que vai alterar.

    ![](./media/app-service-deploy-complex-application-predictably/deploy-5-appinsightresources.png)
 
8.  Destaque JSON, clique em **appInsights AutoScale** para realçar o respectivo código JSON. Esta é a definição de dimensionamento para o seu plano de serviço de aplicação.

9.  No código JSON realçado, localize o `location` e `enabled` propriedades e defini-las, conforme apresentado abaixo.

    ![](./media/app-service-deploy-complex-application-predictably/deploy-6-autoscalesettings.png)

10. Destaque JSON, clique em **CPUHigh appInsights** para realçar o respectivo código JSON. Este é um alerta.

11. Localize o `location` e `isEnabled` propriedades e defini-las, conforme apresentado abaixo. Faça o mesmo para que os outros três alertas (bolbos Lilás).

    ![](./media/app-service-deploy-complex-application-predictably/deploy-7-alerts.png)

12. Agora está pronto para implementar. Com o botão direito do projeto e selecione **Implementar** > **Nova implementação**.

    ![](./media/app-service-deploy-complex-application-predictably/deploy-8-newdeployment.png)

13. Inicie sessão na sua conta Azure se ainda não o tiver feito.

14. Selecione um grupo de recursos existente na sua subscrição ou criar um novo, selecione **azuredeploy.json**e, em seguida, clique em **Editar parâmetros**.

    ![](./media/app-service-deploy-complex-application-predictably/deploy-9-deployconfig.png)

    Agora poderá editar todos os parâmetros definidos no ficheiro de modelo numa tabela totalmente. Parâmetros que definem as predefinições irão já tem os valores predefinidos e parâmetros que definirem uma lista de valores permitidos serão apresentados como dropdowns.

    ![](./media/app-service-deploy-complex-application-predictably/deploy-10-parametereditor.png)

15. Preencha todos os parâmetros vazios e utilize o [endereço de repo GitHub ToDoApp](https://github.com/azure-appservice-samples/ToDoApp.git) **repoUrl**. Em seguida, clique em **Guardar**.
 
    ![](./media/app-service-deploy-complex-application-predictably/deploy-11-parametereditorfilled.png)

    >[AZURE.NOTE] Autoscaling é uma funcionalidade oferecida no camada **padrão** ou superior e os alertas de nível de plano são funcionalidades-lhe fornecidos na camada **básicas** ou superior, terá de definir o parâmetro de **sku** para **padrão** ou **Premium** para poder ver todos os novos aplicação conhecimentos de forma ilumine recursos.
    
16. Clique em **Implementar**. Se tiver seleccionado **guardar palavras-passe**, a palavra-passe será guardada no parâmetro ficheiro **em texto simples**. Caso contrário, ser-lhe-á pedido para introduzir a palavra-passe de base de dados durante o processo de implementação.

Já está! Agora que precise de ir para o [Portal do Azure](https://portal.azure.com/) e a ferramenta de [Azure Explorer de recursos](https://resources.azure.com) para ver os alertas de novos e definições de autoscale adicionadas à sua JSON implementado aplicação.

Os passos nesta secção principalmente feito o seguinte procedimento:

1.  Preparar o ficheiro de modelo
2.  Criado um ficheiro de parâmetro para condizer com o ficheiro de modelo
3.  Implementado o ficheiro de modelo com o ficheiro de parâmetro

O último passo é facilmente realizado por um cmdlet do PowerShell. Para ver o que o Visual Studio fez quando implementado a aplicação, abra Scripts\Deploy AzureResourceGroup.ps1. Há muitas código lá, mas apenas vou para realçar o código de pertinente que precisa para implementar o ficheiro de modelo com o ficheiro de parâmetro.

![](./media/app-service-deploy-complex-application-predictably/deploy-12-powershellsnippet.png)

O último cmdlet, `New-AzureResourceGroup`, é aquele que realmente executa a ação. Tudo isto deve demonstrar a si que, com a ajuda de ferramentas, é relativamente simples implementar a aplicação de nuvem seja previsível. Sempre que executar o cmdlet no mesmo modelo com o mesmo ficheiro de parâmetro, vai obter o mesmo resultado.

## <a name="summary"></a>Resumo ##

No DevOps, repetibilidade e previsibilidade são teclas para qualquer implementação com êxito de uma aplicação de escala de alta composto por microservices. Neste tutorial, ter implementado uma aplicação de duas microservice para Azure como um grupo de recursos única utilizando o modelo de Gestor de recursos do Azure. Começadas,-lhe atribuiu o conhecimento que precisa para começar a converter a aplicação no Azure um modelo e pode aprovisionar e implementá-lo seja previsível. 

## <a name="next-steps"></a>Próximos passos ##

Saiba como [Aplicar ágil métodos e continuamente publicar a sua aplicação microservices com facilidade](app-service-agile-software-development.md) e implementação avançadas técnicas como [implementação flighting](app-service-web-test-in-production-controlled-test-flight.md) facilmente.

<a name="resources"></a>
## <a name="more-resources"></a>Mais recursos ##

-   [Idioma do modelo do Gestor de recursos do Azure](../resource-group-authoring-templates.md)
-   [Criação de modelos de Gestor de recursos do Azure](../resource-group-authoring-templates.md)
-   [Funções de modelo de Gestor de recursos do Azure](../resource-group-template-functions.md)
-   [Implementar uma aplicação com o modelo de Gestor de recursos do Azure](../resource-group-template-deploy.md)
-   [Utilizar o Azure PowerShell com o Gestor de recursos Azure](../powershell-azure-resource-manager.md)
-   [Resolução de problemas de implementações do grupo de recursos no Azure](../resource-manager-troubleshoot-deployments-portal.md)




 
