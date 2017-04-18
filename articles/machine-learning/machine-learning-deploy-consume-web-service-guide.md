<properties
    pageTitle="Azure máquina aprendizagem Web Services: Implementação e consumo | Microsoft Azure"
    description="Recursos para implementar e utilizado por outros serviços web."
    services="machine-learning"
    documentationCenter=""
    authors="vDonGlover"
    manager="raymondl"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="v-donglo"/>

# <a name="azure-machine-learning-web-services-deployment-and-consumption"></a>Formação de máquina Azure Web Services: Implementação e consumo

Pode utilizar o Azure máquina de formação para implementar os fluxos de trabalho e modelos de serviços web máquina de aprendizagem. Estes serviços web, em seguida, podem ser utilizados para ligar os modelos de máquina de aprendizagem a partir de aplicações na Internet para efetuar previsões em tempo real ou no modo do lote. Porque os serviços web são RESTful, pode ligá-los a partir de diferentes plataformas, tais como .NET e Java e linguagens de programação e de aplicações, como o Excel.

As secções seguintes fornecem ligações para instruções passo a passo, código e documentação para ajudar a começar.

## <a name="deploy-a-web-service"></a>Implementar um serviço web

### <a name="with-azure-machine-learning-studio"></a>Com o Azure máquina aprendizagem Studio

Studio de aprendizagem automática e o portal do Microsoft Azure máquina aprendizagem Web Services ajudá-lo a implementar e gerir um serviço web sem escrever código.

As ligações seguintes fornecem informações gerais sobre como implementar um novo serviço web:

* Para obter uma descrição geral sobre como implementar um novo serviço web que é baseado no Gestor de recursos do Azure, consulte o artigo [Implementar um novo serviço web](machine-learning-webservice-deploy-a-web-service.md).
* Para obter instruções sobre como implementar um serviço web, consulte [Implementar um serviço web de aprendizagem do Azure máquina](machine-learning-publish-a-machine-learning-web-service.md).
* Para uma inteira instruções passo a passo sobre como criar e implementar um serviço web, consulte o artigo [tutorial passo 1: criar uma área de trabalho de formação de máquina](machine-learning-walkthrough-1-create-ml-workspace.md).
* Para obter exemplos específicos que implementar um serviço web, consulte:

    * [Tutorial passo 5: Implementar o serviço web de aprendizagem de máquina do Azure](machine-learning-walkthrough-5-publish-web-service.md)
    * [Como implementar um serviço web para várias regiões](machine-learning-how-to-deploy-to-multiple-regions.md)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Com o fornecedor de recursos de serviços web APIs (APIs de Gestor de recursos do Azure)

O fornecedor de recursos de aprendizagem de máquina Azure para serviços web permite implementação e gestão de serviços web utilizando REST API chamadas. Para detalhes adicionais, consulte a referência de [Máquina aprendizagem Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) no MSDN.

### <a name="with-powershell-cmdlets"></a>Com os cmdlets do PowerShell

Azure fornecedor de recursos de aprendizagem automática para os serviços web permite implementação e gestão de serviços web utilizando os cmdlets do PowerShell.

Para utilizar os cmdlets, tem primeiro de iniciar sessão à sua conta Azure a partir de dentro do ambiente de PowerShell utilizando o cmdlet [AzureRmAccount adicionar](https://msdn.microsoft.com/library/mt619267.aspx) . Se não estiver familiarizado com como chamar comandos do PowerShell com baseiam no Gestor de recursos, consulte o artigo [Utilizar o PowerShell Azure com o Gestor de recursos do Azure](../powershell-azure-resource-manager.md#login-to-your-azure-account).

Para exportar a sua experiência de aspeto do Office, utilize [Este código de exemplo](https://github.com/ritwik20/AzureML-WebServices). Depois de criar o ficheiro .exe do código, pode escrever:

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

Executar a aplicação cria um modelo JSON de serviço web. Para utilizar o modelo para implementar um serviço web, tem de adicionar as seguintes informações:

* Nome de conta de armazenamento e a chave

    Pode obter o nome da conta de armazenamento e a chave a partir do [Azure portal](https://portal.azure.com/) ou do [Azure portal clássica](http://manage.windowsazure.com/).
* ID de plano de consolidação

    Pode obter o ID de plano a partir do portal de [Serviços Web do Azure máquina formação](https://services.azureml.net) ao iniciar sessão e clicar em nome de um plano.

Adicioná-los para o modelo JSON como subordinados do nó *Propriedades* do mesmo nível, como o nó do *MachineLearningWorkspace* .

Eis um exemplo:

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

Consulte os artigos seguintes e exemplos de código para detalhes adicionais:

* [Cmdlets de aprendizagem do Azure máquina]( https://msdn.microsoft.com/library/azure/mt767952.aspx) de referência no MSDN
* Exemplo [instruções passo a passo](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) no GitHub

## <a name="consume-the-web-services"></a>Consumir os serviços web

### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>A partir de serviços Web do Azure máquina aprendizagem IU (testes)

Pode testar o seu serviço web a partir do portal de serviços Web do Azure máquina aprendizagem. Isto inclui testar o serviço de pedido de resposta (RRS) e interfaces do serviço de execução de lote (BES).

* [Implementar um novo serviço web](machine-learning-webservice-deploy-a-web-service.md)
* [Implementar um serviço web de aprendizagem de máquina do Azure](machine-learning-publish-a-machine-learning-web-service.md)
* [Tutorial passo 5: Implementar o serviço web de aprendizagem de máquina do Azure](machine-learning-walkthrough-5-publish-web-service.md)

### <a name="from-excel"></a>A partir do Excel

Pode transferir um modelo do Excel que consome o serviço web:

* [Consumo de um serviço web de aprendizagem de máquina Azure a partir do Excel](machine-learning-consuming-from-excel.md)
* [Suplemento do Excel para serviços Web do Azure máquina aprendizagem](machine-learning-excel-add-in-for-web-services.md)


### <a name="from-a-rest-based-client"></a>A partir de um cliente com base no resto

Serviços de Web de aprendizagem do Azure máquina são RESTful APIs. Pode consumir estas APIs a partir de várias plataformas, tal como .NET, Python, R, Java, etc. Página de **consumir** para o seu serviço web no [portal do Microsoft Azure máquina aprendizagem Web Services](https://services.azureml.net) tem exemplos de código que o podem ajudar a começar a utilizar. Para mais informações, consulte o artigo [como consumir um serviço web de aprendizagem do Azure máquina que tenha sido implementado a partir de uma experiência de aprendizagem automática](machine-learning-consume-web-services.md).

