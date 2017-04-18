<properties
   pageTitle="Criar um modelo de implementação de aplicação de lógica | Microsoft Azure"
   description="Saiba como criar um modelo de implementação de aplicação de lógica e utilizá-lo para a gestão de lançamento"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="10/18/2016"
   ms.author="jehollan"/>

# <a name="create-a-logic-app-deployment-template"></a>Criar um modelo de implementação de aplicação de lógica

Depois de ter sido criada uma aplicação de lógica, poderá pretender criá-lo como um modelo de Gestor de recursos do Azure. Desta forma, pode implementar facilmente a aplicação de lógica a qualquer ambiente ou grupo de recursos, onde poderá ter. Para obter uma introdução a modelos de Gestor de recursos, certifique-se de que consulte os artigos sobre a [criação de modelos de Gestor de recursos do Azure](../resource-group-authoring-templates.md) e [implementação recursos por utilização de modelos de Gestor de recursos do Azure](../resource-group-template-deploy.md).

## <a name="logic-app-deployment-template"></a>Modelo de implementação de aplicação de lógica

Uma aplicação de lógica tem três componentes básicos:

* **Recurso de aplicação de lógica**. Este recurso contém informações sobre coisas como preços plano, a localização e a definição de fluxo de trabalho.
* **Definição de fluxo de trabalho**. Este é o que é visualizado na vista de código. Inclui a definição dos passos do fluxo de e como o motor deve ser executado. Este é o `definition` propriedade do recurso de aplicação lógica.
* **Ligações**. Estes são separados recursos que armazenam metadados sobre quaisquer ligações de conexão, tal como uma cadeia de ligação e um token de acesso. Fazer estas numa aplicação lógica na referência a `parameters` secção do recurso de aplicação lógica.

Pode ver todas estas partes para as aplicações de lógica existente utilizando uma ferramenta como [Azure recurso Explorer](http://resources.azure.com).

Para criar um modelo para uma aplicação de lógica utilizar com implementações do grupo de recursos, tem de definir os recursos e parametrizar conforme necessário. Por exemplo, se está a implementar um desenvolvimento, teste e ambiente de produção, que irá provável que pretende utilizar diferentes cadeias de ligação a uma base de dados do SQL em cada ambiente. Em alternativa, poderá pretender implementar dentro de subscrições diferentes ou grupos de recursos.  

## <a name="create-a-logic-app-deployment-template"></a>Criar um modelo de implementação de aplicação de lógica

A forma mais fácil de ter um modelo de implementação de aplicação de lógica válido é utilizar o [Visual Studio Tools para as aplicações de lógica](./app-service-logic-deploy-from-vs.md).  As ferramentas do Visual Studio geram um modelo de implementação válida que pode ser utilizado em qualquer subscrição ou localização.

Algumas outras ferramentas podem ajudá-lo à medida que cria um modelo de implementação de aplicação de lógica. Pode criar à mão, ou seja, utilizando os recursos já abordados aqui para criar os parâmetros conforme necessário. Outra opção é a utilizar um módulo de PowerShell [Criador do modelo de aplicação de lógica](https://github.com/jeffhollan/LogicAppTemplateCreator) . Este módulo abrir origem avalia pela primeira vez a aplicação de lógica e quaisquer ligações que está a utilizar e, em seguida, gera recursos de modelo com os parâmetros necessários para implementação. Por exemplo, se tiver uma aplicação de lógica que recebe uma mensagem de uma fila Bus de serviço do Azure e adiciona dados a uma base de dados do Azure SQL, a ferramenta irá preservar todos a lógica orchestration e parametrizar cadeias de ligação SQL e Bus de serviço para que podem ser definidas na implementação.

>[AZURE.NOTE] Ligações devem ser dentro do mesmo grupo recurso como a aplicação de lógica.

### <a name="install-the-logic-app-template-powershell-module"></a>Instalar o módulo de PowerShell do modelo de aplicação lógica

A forma mais fácil de instalar o módulo é através de do [PowerShell Galeria](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1), utilizando o comando `Install-Module -Name LogicAppTemplate`.  

Também pode instalar o módulo PowerShell manualmente:

1. Transfira a versão mais recente do [Criador de blocos de modelo de aplicação de lógica](https://github.com/jeffhollan/LogicAppTemplateCreator/releases).  
1. Extrair a pasta na sua pasta de módulo do PowerShell (normalmente `%UserProfile%\Documents\WindowsPowerShell\Modules`).

Para o módulo trabalhar com qualquer acesso inquilino e de subscrição token, recomendamos que utilize-lo com a ferramenta de linha de comandos [ARMClient](https://github.com/projectkudu/ARMClient) .  Esta [mensagem no blogue](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) aborda ARMClient mais detalhadamente.

### <a name="generate-a-logic-app-template-by-using-powershell"></a>Gerar um modelo de aplicação lógica utilizando o PowerShell

Após a instalação do PowerShell, pode gerar um modelo, utilizando o seguinte comando:

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId`é o ID da subscrição Azure. Esta linha obtém um acesso token através do ARMClient, pela primeira vez, em seguida, tubos-lo através de scripts de PowerShell e, em seguida, cria o modelo num ficheiro JSON.

## <a name="add-parameters-to-a-logic-app-template"></a>Adicionar parâmetros a um modelo de aplicação de lógica

Depois de criar o modelo de aplicação lógica, pode continuar a adicionar ou modificar parâmetros que poderá ter. Por exemplo, se a sua definição de incluir um ID do recurso a uma função Azure ou aninhado fluxo de trabalho que planeia implementar numa única implementação, pode adicionar mais recursos ao seu modelo e parametrizar IDs conforme necessário. O mesmo se aplica ao qualquer referências a personalizado APIs ou Swagger pontos finais de que esperar para implementar com cada grupo de recursos.

## <a name="deploy-a-logic-app-template"></a>Implementar um modelo de aplicação de lógica

Pode implementar o modelo utilizando qualquer número de ferramentas, incluindo PowerShell, REST API, gestão de lançamento do Visual Studio e o modelo de implementação do Azure Portal. Consulte este artigo sobre como [Implementar recursos utilizando o Gestor de recursos do Azure modelos](../resource-group-template-deploy.md) para obter informações adicionais. Além disso, recomendamos que cria um [ficheiro de parâmetro](../resource-group-template-deploy.md#parameter-file) para armazenar valores para o parâmetro.

### <a name="authorize-oauth-connections"></a>Autorizar OAuth ligações

Após a implementação, a aplicação de lógica funciona ponto a ponto com parâmetros válidas. No entanto, ligações de acesso OAuth ainda terão de ser autorizados para gerar um token de acesso válido. Pode fazê-lo ao abrir a aplicação de lógica no estruturador de e, em seguida, autorizar ligações. Ou, se pretende automatizar, pode utilizar um script para consentimento para cada ligação OAuth. Existe um script de exemplo no GitHub em projeto [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth) .

## <a name="visual-studio-release-management"></a>Gestão de lançamento do Visual Studio

Um cenário comum para implementar e gerir um ambiente é utilizar uma ferramenta de gestão de lançamento do Visual Studio, como o com um modelo de implementação de aplicação de lógica. Serviços de equipa do Visual Studio inclui uma tarefa de [Grupo de recursos do Azure implementar](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/DeployAzureResourceGroup) que pode adicionar para qualquer compilação ou solte em curso. Tem de ter um [principal de serviço](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/) de autorização para implementar e, em seguida, pode gerar a definição de lançamento.

1. Em gestão de lançamento, para criar uma nova definição, selecione **branco** para iniciar com uma definição de vazia.

    ![Criar uma definição de nova e vazia][1]   

1. Selecione qualquer recursos de que necessita para esta situação. Provavelmente, será o modelo de aplicação de lógica gerado manualmente ou como parte do processo de compilação.
1. Adicione uma tarefa de **Implementação de grupo de recursos do Azure** .
1. Configurar com um [principal de serviço](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)e os ficheiros de modelo e parâmetros de modelo de referência.
1. Continue a construir passos do processo de lançamento para quaisquer outros ambiente, teste automatizado ou os aprovadores, conforme necessário.

<!-- Image References -->
[1]: ./media/app-service-logic-create-deploy-template/emptyReleaseDefinition.PNG
