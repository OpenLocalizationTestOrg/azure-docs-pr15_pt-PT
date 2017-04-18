<properties
   pageTitle="Ambientes de desenvolvimento e teste | Microsoft Azure"
   description="Saiba como utilizar o Gestor de recursos do Azure modelos para forma consistente e rapidamente criar e eliminar desenvolvimento e testar ambientes."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/22/2016"
   ms.author="tomfitz"/>

# <a name="development-and-test-environments-in-microsoft-azure"></a>Ambientes de desenvolvimento e teste no Microsoft Azure

Aplicações personalizadas são normalmente implementadas a múltiplos desenvolvimento e ambientes testes antes da implementação para produção. Quando são criadas ambientes no local, recursos informáticos são adquiridos ou atribuídos para cada ambiente para cada aplicação. Os ambientes incluem frequentemente várias física ou máquinas virtuais com configurações específicas que são implementadas manualmente ou com os scripts de automatização complexas. Implementações frequentemente demorar horas e resultam em configurações inconsistentes em ambientes.

## <a name="scenario"></a>Cenário ##

Quando aprovisionar desenvolvimento e ambientes de teste no Microsoft Azure, pode pagar apenas para os recursos que utiliza.  Este artigo explica como rapidamente e forma consistente pode criar, manter e eliminar desenvolvimento e testar ambientes, utilizando os modelos de Gestor de recursos do Azure e ficheiros de parâmetros, tal como ilustrado abaixo.

![Cenário](./media/solution-dev-test-environments/scenario.png)

Três ambientes testes de desenvolvimento e são apresentadas acima.  Cada tem a aplicação web e recursos de base de dados SQL, o que são especificados num ficheiro de modelo.  Os nomes da aplicação e a base de dados em cada ambiente são diferentes e estão especificados nos ficheiros do parâmetro exclusivo para cada ambiente.  

Se não estiver familiarizado com os conceitos de Gestor de recursos do Azure, é recomendável que leia o artigo [Descrição geral do Gestor de recursos do Azure](azure-resource-manager/resource-group-overview.md) antes de ler este artigo.

Pretende de aceder primeiro através dos passos neste artigo como listado sem qualquer um dos artigos referenciados para obter rapidamente alguma experiência de utilização de modelos de Gestor de recursos do Azure de leitura. Depois de ter sido através dos passos uma vez, poderá obter respostas para a maior parte das perguntas que surgiram o primeiro tempo através de fazendo experiências mais com os passos e lendo os artigos referenciados.

## <a name="plan-azure-resource-use"></a>Planear a utilização de recursos Azure
Assim que tiver uma estrutura de alto nível para a sua aplicação, pode definir:

- Quais os recursos Azure irá incluir a sua aplicação. Pode criar a sua aplicação e implementá-lo como uma aplicação Web do Azure com uma base de dados do SQL Azure.  Poderá criar a aplicação em máquinas virtuais do utilizando PHP e MySQL IIS e do SQL Server ou outros componentes. O artigo [Azure Service de aplicação, serviços em nuvem e máquinas virtuais comparação]( app-service-web/choose-web-site-cloud-service-vm.md) ajuda-o a decidir quais os recursos Azure que poderá querer utilizem-las para a sua aplicação.
- Requisitos nível que serviço como disponibilidade, segurança e escala que cumprem as aplicações.

## <a name="download-an-existing-template"></a>Transferir um modelo existente
Um modelo de Gestor de recursos do Azure define todos os recursos Azure que utiliza a sua aplicação. Vários modelos já existem que pode implementar diretamente no portal do Azure, ou transferir, modificar e guardar no sistema de controlo de origem com o código de aplicação.  Conclua os passos abaixo para transferir um modelo existente.

1. Procure modelos existentes no repositório de GitHub [Modelos de guia de introdução do Azure](https://github.com/Azure/azure-quickstart-templates/) . Na lista, verá uma pasta "[201-web-aplicação--base de dados sql](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)". Uma vez que incluem várias aplicações personalizadas numa aplicação web e a base de dados SQL, este modelo é utilizado como exemplo no restante deste artigo para ajudá-lo a compreender como utilizar modelos. Para além do âmbito deste artigo para explicar totalmente tudo este modelo cria e configura é, mas se planeia utilizar para criar ambientes reais na sua organização, irá querer compreendam totalmente lendo o artigo [aprovisionar uma aplicação web com uma base de dados do SQL](app-service-web/app-service-web-arm-with-sql-database-provision.md) .
Nota: Este artigo foi escrito para a versão de Dezembro de 2015 do modelo [201-web-aplicação--base de dados sql](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database) . As ligações abaixo que apontam para o modelo e ficheiros de parâmetros são para que versão do modelo.
2. Clique no ficheiro [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.json) na pasta 201-web-aplicação--base de dados sql para ver os seus conteúdos. Este é o ficheiro de modelo de Gestor de recursos do Azure. 
3. No modo de visualização, clique no botão "[matéria-prima](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.json)". 
4. Com o rato, selecionar o conteúdo deste ficheiro e guardá-los com o seu computador como um ficheiro com o nome "TestApp1 Template.json." 
5. Examinar o conteúdo do modelo e repare o seguinte procedimento:
 - Secção de **recursos** : Esta secção define os tipos de recursos Azure criados com este modelo. Entre outros tipos de recursos, este modelo cria recursos [Azure Web App](app-service-web/app-service-web-overview.md) e [Base de dados do SQL Azure](sql-database/sql-database-technical-overview.md) . Se preferir executar e gerir web e servidores do SQL Server em máquinas virtuais do, pode utilizar os modelos de "[iis-2vm sql 1vm](https://github.com/Azure/azure-quickstart-templates/tree/master/iis-2vm-sql-1vm)" ou "[luz aplicação](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app)", mas as instruções neste artigo são baseadas no modelo de [201-web-aplicação--base de dados sql](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database) .
 - Secção de **parâmetros** : Esta secção define os parâmetros de que cada recurso pode ser configurado com. Alguns dos parâmetros especificados no modelo de têm "ValorPredefinido" propriedades, enquanto outras pessoas não o fizer. Quando implementar o Azure recursos com um modelo, tem de fornecer valores para todos os parâmetros que não têm ValorPredefinido propriedades especificadas no modelo.  Se não fornecer valores para os parâmetros com as propriedades do ValorPredefinido, em seguida, é utilizado o valor especificado para o parâmetro ValorPredefinido no modelo.

Um modelo de define quais os recursos Azure são criados e os parâmetros de cada recurso pode ser configurado com. Pode obter mais informações sobre modelos e como estruturar o seu próprio ao ler o artigo [melhores práticas para criar modelos de Gestor de recursos do Azure](best-practices-resource-manager-design-templates.md) .

## <a name="download-and-customize-an-existing-parameter-file"></a>Transferir e personalizar um ficheiro de parâmetro existente

Apesar de provavelmente poderá querer o *mesmo* Azure recursos criados cada ambiente, provavelmente quererá a configuração dos recursos para ser *diferente* em cada ambiente.  Este é onde os ficheiros de parâmetro está disponível. Crie ficheiros de parâmetro que contêm valores exclusivos em cada ambiente completando os passos abaixo.   

1. Ver o conteúdo do ficheiro [azuredeploy.parameters.json](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.parameters.json) na pasta 201-web-aplicação--base de dados sql. Este é o ficheiro de parâmetro para o ficheiro de modelo que guardou na secção anterior. 
2. No modo de visualização, clique no botão "[matéria-prima](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.parameters.json)". 
3. Com o rato, selecione o conteúdo deste ficheiro e guardá-los três ficheiros em separado no seu computador com os seguintes nomes:
 - Parâmetros de TestApp1-Development.json
 - Parâmetros de TestApp1-Test.json
 - TestApp1-parâmetros Pre Production.json

3. Utilizando qualquer JSON editor de texto ou, edite o ficheiro de parâmetro de ambiente de desenvolvimento que criou no passo 3, substituir os valores listados à direita dos valores de parâmetros no ficheiro com os *valores* listados no lado direito dos **parâmetros** abaixo: 
 - **nome do site**: *TestApp1DevApp*
 - **hostingPlanName**: *TestApp1DevPlan*
 - **siteLocation**: *Central (EUA)*
 - **nomedoservidor**: *testapp1devsrv*
 - **serverLocation**: *Central (EUA)*
 - **administratorLogin**: *testapp1Admin*
 - **administratorLoginPassword**: *substitua a sua palavra-passe*
 - **databaseName**: *testapp1devdb*

4. Utilizando qualquer texto ou JSON editor, edite o ficheiro de parâmetro de ambiente de teste que criou no passo 3, substituindo o os valores listados à direita dos valores de parâmetros no ficheiro com os *valores* listados no lado direito dos **parâmetros** abaixo:
 - **nome do site**: *TestApp1TestApp*
 - **hostingPlanName**: *TestApp1TestPlan*
 - **siteLocation**: *Central (EUA)*
 - **nomedoservidor**: *testapp1testsrv*
 - **serverLocation**: *Central (EUA)*
 - **administratorLogin**: *testapp1Admin*
 - **administratorLoginPassword**: *substitua a sua palavra-passe*
 - **databaseName**: *testapp1testdb*

5. Utilizando qualquer JSON editor de texto ou, edite o ficheiro de parâmetro de pré-produção que criou no passo 3.  Substitua todo o conteúdo do ficheiro com o que é abaixo:

        {
          "$schema" : "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
          "contentVersion" : "1.0.0.0",
          "parameters" : {
        "administratorLogin" : {
          "value" : "testApp1Admin"
        },
        "administratorLoginPassword" : {
          "value" : "replace with your password"
        },
        "databaseName" : {
          "value" : "testapp1preproddb"
        },
        "hostingPlanName" : {
          "value" : "TestApp1PreProdPlan"
        },
        "serverLocation" : {
          "value" : "Central US"
        },
        "serverName" : {
          "value" : "testapp1preprodsrv"
        },
        "siteLocation" : {
          "value" : "Central US"
        },
        "siteName" : {
          "value" : "TestApp1PreProdApp"
        },
        "sku" : {
          "value" : "Standard"
        },
            "requestedServiceObjectiveName" : {
              "value" : "S1"
        }
          }
        }

No ficheiro de parâmetros de produção pré acima, os parâmetros **sku** e **requestedServiceObjectiveName** foram *adicionadas*, Considerando que estes não foram adicionados nos ficheiros de parâmetros desenvolvimento e teste. Esta é uma vez que existem valores predefinidos especificados para estes parâmetros no modelo e em ambientes desenvolvimento e teste, são utilizados os valores predefinidos, mas na pré-produção valores de não predefinida ambiente para estes parâmetros são utilizados.

O motivo não predefinida valores são utilizados para obter estes parâmetros no ambiente de produção pré é testar valores para estes parâmetros que poderá preferir para o seu ambiente de produção para que também pode ser testados.  Estes parâmetros todos os referem-se para o Azure [planos alojamento Web App](https://azure.microsoft.com/pricing/details/app-service/), ou **sku** e Azure [Base de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/)ou **requestedServiceObjectiveName** que são utilizadas pela aplicação.  Diferentes skus e nomes de objetivo de serviço tem diferentes custos e funcionalidades e suportam métricas de nível de serviços diferente.

A tabela abaixo lista os valores predefinidos para estes parâmetros especificados no modelo e os valores que são utilizados em vez dos valores predefinidos no ficheiro de parâmetros de produção pré.

| Parâmetro | Valor predefinido | Valor do parâmetro do ficheiro |
|---|---|---|
| **SKU** | Gratuito | Padrão |
| **requestedServiceObjectiveName** | S0 | S1 |

## <a name="create-environments"></a>Criar ambientes
Todos os recursos Azure devem ser criados dentro de um [Grupo de recursos do Azure](azure-resource-manager/resource-group-overview.md). Grupos de recursos permitem-lhe agrupar recursos Azure para que podem ser geridos constituem.  Podem ser atribuídas [permissões](./active-directory/role-based-access-built-in-roles.md) para grupos de recursos assim que as pessoas específicas da sua organização podem criar, modificar, eliminar ou vê-los e os recursos nelas.  Alertas e as informações de faturação para recursos no grupo de recursos podem ser visualizadas no [Portal do Azure](https://portal.azure.com). Grupos de recursos são criados numa [região](https://azure.microsoft.com/regions/)Azure.  Neste artigo, todos os recursos são criados na região Central (EUA). Quando começar a criar ambientes reais, irá escolhe o região que melhor corresponde às suas necessidades. 

Crie grupos de recursos para cada ambiente utilizar qualquer um dos métodos abaixo.  Todos os métodos de irão alcançar o mesmo resultado.

###<a name="azure-command-line-interface-cli"></a>Interface de linha de comandos Azure (CLI)

Certifique-se de que tem o clip [instalado](xplat-cli-install.md) no Windows, OS X, ou Linux computador e que tenha [ligado](xplat-cli-connect.md) sua [conta do Azure AD](./active-directory/active-directory-how-subscriptions-associated-directory.md) (também designado de uma conta escolar ou profissional) à sua subscrição do Azure. A partir da linha de comandos do clip, escreva o comando abaixo para criar o grupo de recursos para o ambiente de desenvolvimento.

    azure group create "TestApp1-Development" "Central US"

O comando irá devolver o seguinte se tiver êxito:

    info:    Executing command group create
    + Getting resource group TestApp1-Development
    + Creating resource group TestApp1-Development
    info:    Created resource group TestApp1-Development
    data:    Id:                  /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development
    data:    Name:                TestApp1-Development
    data:    Location:            centralus
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

Para criar o grupo de recursos para o ambiente de teste, escreva o comando abaixo:

    azure group create "TestApp1-Test" "Central US"

Para criar o grupo de recursos para o ambiente de pré-produção, escreva o comando abaixo:

    azure group create "TestApp1-Pre-Production" "Central US"

###<a name="powershell"></a>PowerShell

Certifique-se de que tem o Azure PowerShell 1.01 ou superior instalado no computador Windows e tiver ligado a sua [conta do Azure AD](./active-directory/active-directory-how-subscriptions-associated-directory.md) (também designado de uma conta escolar ou profissional) à sua subscrição, tal como descrito no artigo [como instalar e configurar o Azure PowerShell](powershell-install-configure.md) . A partir de uma linha de comandos do PowerShell, escreva o comando abaixo para criar o grupo de recursos para o ambiente de desenvolvimento.

    New-AzureRmResourceGroup -Name TestApp1-Development -Location "Central US"

O comando irá devolver o seguinte se tiver êxito:

    ResourceGroupName : TestApp1-Development
    Location          : centralus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development

Para criar o grupo de recursos para o ambiente de teste, escreva o comando abaixo:

    New-AzureRmResourceGroup -Name TestApp1-Test -Location "Central US"

Para criar o grupo de recursos para o ambiente de pré-produção, escreva o comando abaixo:

    New-AzureRmResourceGroup -Name TestApp1-Pre-Production -Location "Central US"

###<a name="azure-portal"></a>Portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta do [Azure AD](./active-directory/active-directory-how-subscriptions-associated-directory.md) (também denominado uma escolar ou profissional). Clique em Novo--> gestão--> grupo de recursos e introduza "TestApp1 desenvolvimento" na caixa de nome de grupo de recursos, selecione a sua subscrição e selecione "Central-nos" na caixa de localização do grupo de recursos, conforme mostrado na imagem abaixo.
   ![Portal](./media/solution-dev-test-environments/rgcreate.png)
2. Clique no botão Criar para criar o grupo de recursos.
3. Clique em Procurar, desloque-se para baixo na lista para grupos de recursos e clique em grupos de recursos, conforme apresentado abaixo.
   ![Portal](./media/solution-dev-test-environments/rgbrowse.png) 
4. Depois de clicar no grupos de recursos, verá o pá de grupos de recurso com o novo grupo de recursos.
   ![Portal](./media/solution-dev-test-environments/rgview.png)
5. Criar o teste TestApp1 e recurso de pré-TestApp1-produção grupos da mesma forma que criou o grupo de recursos de desenvolvimento TestApp1 acima.

##<a name="deploy-resources-to-environments"></a>Implementar recursos ambientes

Implemente Azure recursos para os grupos de recursos para cada ambiente com o ficheiro de modelo para a solução e os ficheiros de parâmetros para cada ambiente utilizando um dos métodos abaixo.  Ambos os métodos irão alcançar o mesmo resultado.

###<a name="azure-command-line-interface-cli"></a>Interface de linha de comandos Azure (CLI)

A Clip linha de comandos, escreva o comando abaixo para implementar recursos ao grupo de recursos que criou no ambiente de desenvolvimento, substituir [caminho] com o caminho para os ficheiros que guardou no passos anteriores.

    azure group deployment create -g TestApp1-Development -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Development.json 

Depois de ver uma mensagem "A aguardar para implementação concluir" para alguns minutos, o comando irá devolver o seguinte se tiver êxito:

    info:    Executing command group deployment create
    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "Deployment1"
    + Waiting for deployment to complete
    data:    DeploymentName     : Deployment1
    data:    ResourceGroupName  : TestApp1-Development
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : XXXX-XX-XXT20:20:23.5202316Z
    data:    Mode               : Incremental
    data:    Name                           Type          Value
    data:    -----------------------------  ------------  ----------------------------
    data:    siteName                       String        TestApp1DevApp
    data:    hostingPlanName                String        TestApp1DevPlan
    data:    siteLocation                   String        Central US
    data:    sku                            String        Free
    data:    workerSize                     String        0
    data:    serverName                     String        testapp1devsrv
    data:    serverLocation                 String        Central US
    data:    administratorLogin             String        testapp1Admin
    data:    administratorLoginPassword     SecureString  undefined
    data:    databaseName                   String        testapp1devdb
    data:    collation                      String        SQL_Latin1_General_CP1_CI_AS
    data:    edition                        String        Standard
    data:    maxSizeBytes                   String        1073741824
    data:    requestedServiceObjectiveName  String        S0
    info:    group deployment create command OKx

Se o comando não tiver êxito, resolva todas as mensagens de erro e volte a tentar.  Problemas comuns estiver a utilizar os valores de parâmetros não adira restrições de atribuição de nomes de recursos Azure. Podem encontrar outras sugestões de resolução de problemas no artigo [implementações de grupo de recursos de resolução de problemas no Azure](./resource-manager-troubleshoot-deployments-cli.md) .

A Clip linha de comandos, escreva o comando abaixo para implementar recursos ao grupo de recursos que criou para o ambiente de teste, substituir [caminho] com o caminho para os ficheiros que guardou no passos anteriores.

    azure group deployment create -g TestApp1-Test -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Test.json

A Clip linha de comandos, escreva o comando abaixo para implementar recursos ao grupo de recursos que criou para o ambiente de pré-produção, substituir [caminho] com o caminho para os ficheiros que guardou no passos anteriores.

    azure group deployment create -g TestApp1-Pre-Production -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Pre-Production.json
  
###<a name="powershell"></a>PowerShell

A partir de uma linha de comandos do PowerShell Azure (versão 1.01 ou superior), escreva o comando abaixo para implementar recursos ao grupo de recursos que criou no ambiente de desenvolvimento, substituir [caminho] com o caminho para os ficheiros que guardou no passos anteriores.

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Development -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Development.json -Name Deployment1 

Depois de a ver um cursor intermitente por alguns minutos, o comando irá devolver o seguinte se tiver êxito:

    DeploymentName    : Deployment1
    ResourceGroupName : TestApp1-Development
    ProvisioningState : Succeeded
    Timestamp         : XX/XX/XXXX 2:44:48 PM
    Mode              : Incremental
    TemplateLink      : 
    Parameters        : 
                        Name             Type                       Value     
                        ===============  =========================  ==========
                        siteName         String                     TestApp1DevApp
                        hostingPlanName  String                     TestApp1DevPlan
                        siteLocation     String                     Central US
                        sku              String                     Free      
                        workerSize       String                     0         
                        serverName       String                     testapp1devsrv
                        serverLocation   String                     Central US
                        administratorLogin  String                     testapp1Admin
                        administratorLoginPassword  SecureString                         
                        databaseName     String                     testapp1devdb
                        collation        String                     SQL_Latin1_General_CP1_CI_AS
                        edition          String                     Standard  
                        maxSizeBytes     String                     1073741824
                        requestedServiceObjectiveName  String                     S0        
                        
    Outputs           :

  Se o comando não tiver êxito, resolva todas as mensagens de erro e volte a tentar.  Problemas comuns estiver a utilizar os valores de parâmetros não adira restrições de atribuição de nomes de recursos Azure. Podem encontrar outras sugestões de resolução de problemas no artigo [implementações de grupo de recursos de resolução de problemas no Azure](./resource-manager-troubleshoot-deployments-powershell.md) .

  A partir de uma linha de comandos do PowerShell, escreva o comando abaixo para implementar recursos ao grupo de recursos que criou para o ambiente de teste, substituir [caminho] com o caminho para os ficheiros que guardou no passos anteriores.

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Test -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Test.json -Name Deployment1

  A partir de uma linha de comandos do PowerShell, escreva o comando abaixo para implementar recursos ao grupo de recursos que criou para o ambiente de pré-produção, substituir [caminho] com o caminho para os ficheiros que guardou no passos anteriores.

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Pre-Production -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Pre-Production.json -Name Deployment1

Os ficheiros de modelo e um parâmetro podem ser mantidos com o seu código da aplicação e versões num sistema de controlo da origem.  Também é possível guardar os comandos acima para ficheiros de script e guardá-los com o seu código também.

> [AZURE.NOTE] Pode implementar o modelo diretamente ao clicar no botão de "Implementar para Azure" no artigo do [aprovisionar uma aplicação Web com uma base de dados do SQL](https://azure.microsoft.com/documentation/templates/201-web-app-sql-database/) Azure.  Pode ser úteis isto para mais informações sobre modelos, mas ao fazê-lo por isso, não permitem-lhe editar, versão e guarde os valores do modelo e de parâmetro com o código da aplicação, para que mais detalhes sobre este método não está incluído neste artigo.

## <a name="maintain-environments"></a>Manter ambientes
Ao longo de desenvolvimento, configuração dos recursos Azure nos ambientes diferentes pode ser inconsistente alterada intencional ou acidentalmente.  Isto pode causar desnecessárias de resolução de problemas e resolução de problemas durante o ciclo de desenvolvimento de aplicações.

1. Altere os ambientes abrindo o [Azure portal](https://portal.azure.com). 
2. Inicie sessão para o mesmo com a mesma conta que utilizou para concluir os passos acima. 
3. Como é mostrado na imagem abaixo, clique em Procurar--> grupos de recursos (poderá ter de deslocar para baixo para ver os grupos de recurso).
   ![Portal](./media/solution-dev-test-environments/rgbrowse.png)
4. Depois de clicar em grupos de recursos na imagem acima, verá o pá de grupos do recurso e os grupos de três recursos que criou no passo anterior, conforme mostrado na imagem abaixo. Clique no grupo de recursos de desenvolvimento TestApp1 e verá a pá que lista os recursos criados pelo modelo na implementação de grupo de recursos de desenvolvimento TestApp1 concluída no passo anterior.  Elimine o recurso TestApp1DevApp Web App clicando em TestApp1DevApp no pá de grupo de recursos de desenvolvimento TestApp1 e, em seguida, clicar em eliminar na pá de aplicação TestApp1DevApp Web.
   ![Portal](./media/solution-dev-test-environments/portal2.png)
5. Clique em "Sim" quando o portal pede-lhe como para se tiver a certeza de que pretende eliminar o recurso. Fechar o pá de grupo de recursos de desenvolvimento TestApp1 e voltar a abri-lo serão agora mostrá-lo sem a aplicação Web que acabou de eliminar.  O conteúdo do grupo de recursos está agora diferente do que devem ser. Ainda pode experimentar ao eliminar vários recursos a partir de vários grupos de recursos ou mesmo alterar definições de configuração de alguns dos recursos. Em vez de utilizar o portal do Azure para eliminar um recurso a partir de um grupo de recursos, pode utilizar o comando PowerShell [Remover AzureResource](https://msdn.microsoft.com/library/azure/dn757676.aspx) ou a ou o comando "recurso azure eliminar" a partir do clip para efetuar a mesma tarefa.
6. Para obter todos os recursos e configuração que é suposto existirem dos grupos de recurso novamente para o Estado devem ser no, implementar novamente os ambientes para os grupos de recursos utilizando os comandos da mesma que utilizou na secção [recursos implementar a ambientes](#deploy-resources-to-environments) , mas substitua "Deployment1" com "Deployment2."
7.  Como é mostrado na secção de resumo da pá de desenvolvimento TestApp1 na imagem apresentada no passo 4, verá que a aplicação Web eliminados no portal no passo anterior exista novamente, tal como quaisquer outros recursos pode ter escolhido eliminar. Se tiver alterado a configuração de qualquer um dos recursos, também pode verificar que tiverem sido definidas novamente para os valores nos ficheiros do parâmetro demasiado. Uma das vantagens de implementação do seu ambientes com modelos de Gestor de recursos do Azure é que pode facilmente novamente implementar os ambientes de voltar a um estado conhecido em qualquer altura.
8. Se clicar no texto em "Última implementação" na imagem abaixo, verá um pá que mostra o histórico de implementação do grupo de recursos.  Uma vez que utilizou o nome "Deployment1" para a primeira implementação e "Deployment2" para a segunda implementação, terá de duas entradas.  Clicar numa implementação será apresentada uma pá que mostra os resultados para cada implementação.
  ![Portal](./media/solution-dev-test-environments/portal3.png)



## <a name="delete-environments"></a>Eliminar ambientes
Assim que tiver terminado de utilizar um ambiente, irá querer para eliminá-la para que não implicam custos de utilização de recursos Azure que já não estiver a utilizar.  Eliminar ambientes é ainda mais fácil de criá-las.  Nos passos anteriores, grupos de recursos do Azure individuais foram criados para cada ambiente e, em seguida, os recursos foram implementados para os grupos de recursos. 

Elimine os ambientes de utilizar qualquer um dos métodos abaixo.  Todos os métodos de irão alcançar o mesmo resultado.

### <a name="azure-cli"></a>Clip Azure

A partir de uma linha de comandos do clip, escreva o seguinte:

    azure group delete "TestApp1-Development"

Quando lhe for pedido, introduza y e, em seguida, prima enter para remover o ambiente de desenvolvimento e todos os seus recursos. Depois de alguns minutos, o comando irá devolver o seguinte procedimento:

    info:    group delete command OK

A partir de uma linha de comandos do clip, escreva o seguinte procedimento para eliminar os ambientes restantes:

    azure group delete "TestApp1-Test"
    azure group delete "TestApp1-Pre-Production"
  
### <a name="powershell"></a>PowerShell

A partir de uma linha de comandos do PowerShell Azure (versão 1.01 ou superior), escreva o comando abaixo para eliminar o grupo de recursos e todo o seu conteúdo.    

    Remove-AzureRmResourceGroup -Name TestApp1-Development

Quando lhe for perguntado se tem a certeza de que pretende remover o grupo de recursos, introduza y, seguido da tecla enter.

Escreva o seguinte procedimento para eliminar os ambientes restantes:

    Remove-AzureRmResourceGroup -Name TestApp1-Test
    Remove-AzureRmResourceGroup -Name TestApp1-Pre-Production

### <a name="azure-portal"></a>Portal do Azure

1. No portal do Azure, navegue para grupos de recursos conforme fez num passos anteriores. 
2. Selecione o grupo de recursos de desenvolvimento TestApp1 e, em seguida, clique em Eliminar no pá de grupo de recursos de desenvolvimento TestApp1. Uma nova pá irão aparecer. Introduza o nome do grupo de recursos e clique no botão Eliminar.
![Portal](./media/solution-dev-test-environments/rgdelete.png)
3. Eliminar o teste TestApp1 e recurso de pré-TestApp1-produção grupos da mesma forma que eliminar o grupo de recursos de desenvolvimento TestApp1.

Independentemente do método que utiliza, os grupos de recursos e todos os recursos que continham já não existirão e já não irá implicam despesas de faturaçãohttps para os recursos.  

Para minimizar o Azure as despesas de utilização de recursos implicam durante desenvolvimento de aplicações que pode utilizar a [Automatização Azure](automation/automation-intro.md) para agendar tarefas de que:

- Pare máquinas virtuais no final de cada dia e reinicie-los no início de cada dia.
- Elimine todos ambientes no final de cada dia e voltar a criá-los no início de cada dia.
 
Agora que já tenha passado como é fácil criar, manter e eliminar desenvolvimento e testar ambientes, pode obter mais informações sobre o que, basta tinha ainda mais experiências com os passos acima e ler as referências contidas neste artigo.

## <a name="next-steps"></a>Próximos passos

- [Delegar controlo administrativo](./active-directory/role-based-access-control-configure.md) para recursos diferentes em cada ambiente atribuindo Microsoft Azure AD grupos ou utilizadores a funções específicas que têm a capacidade de executar um subconjunto de operações em Azure recursos.
- [Atribuir etiquetas](resource-group-using-tags.md) para os grupos de recursos para cada ambiente e/ou os recursos individuais. Pode adicionar uma etiqueta de "Ambiente" aos grupos de recursos e defina o valor para correspondem aos seus nomes de ambiente. Etiquetas podem ser bastante úteis quando precisar de recursos de faturação ou gestão de organizar.
- Monitorizar os alertas e de faturação para o grupo de recursos no [portal do Azure](https://portal.azure.com).
