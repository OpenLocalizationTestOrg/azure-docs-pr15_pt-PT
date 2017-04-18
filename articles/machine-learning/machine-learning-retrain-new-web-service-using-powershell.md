<properties
    pageTitle="Um novo serviço web utilizando os cmdlets do PowerShell de gestão de aprendizagem máquina de retransmissão | Microsoft Azure"
    description="Aprenda a Reciclagem de um modelo e atualizar o serviço web para utilizar o modelo de recentemente formação em Azure máquina aprender a utilizar os cmdlets do PowerShell de gestão de aprendizagem máquina através de programação."
    services="machine-learning"
    documentationCenter=""
    authors="vDonGlover"
    manager="raymondlaghaeian"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="v-donglo"/>

# <a name="retrain-a-new-web-service-using-the-machine-learning-management-powershell-cmdlets"></a>Reciclagem de um novo serviço web utilizando os cmdlets do PowerShell de gestão de aprendizagem automática

Quando Reciclagem um novo serviço web, pode atualizar a definição do serviço web aspeto do Office para o novo modelo de formação de referência.  

## <a name="prerequisites"></a>Pré-requisitos

Tem configurou uma experiência de formação e uma experiência de aspeto do Office como apresentado na [Reciclagem formação de máquina modelos através de programação](machine-learning-retrain-models-programmatically.md). 

>[AZURE.IMPORTANT] A experiência de aspeto do Office tem de ser implementada como uma serviço web de aprendizagem automática de Gestor de recursos do Azure baseado (novo). 
 
Para obter informações adicionais sobre implementar os serviços web, consulte o artigo [Implementar um serviço web de aprendizagem do Azure máquina](machine-learning-publish-a-machine-learning-web-service.md).

Este processo requer que instalou os Cmdlets do Azure máquina aprendizagem. Para instalar os cmdlets de formação de máquina de informações, consulte a referência de [Cmdlets de aprendizagem do Azure máquina](https://msdn.microsoft.com/library/azure/mt767952.aspx) no MSDN.

Copiado a saída readaptação as seguintes informações:

* BaseLocation
* RelativeLocation

Os passos que seguir são:

1.  Inicie sessão sua conta do Gestor de recursos do Azure.
2.  Obter a definição do serviço web
3.  Exportar a definição do serviço Web como JSON
4.  Atualize a referência para o blob ilearner no JSON.
5.  Importar o JSON para uma definição de serviço Web
6.  Atualizar o serviço web com a nova definição do serviço Web

## <a name="sign-in-to-your-azure-resource-manager-account"></a>Iniciar sessão na sua conta do Gestor de recursos do Azure

Tem iniciar sessão na sua conta Azure a partir de dentro do ambiente de PowerShell utilizando o cmdlet [AzureRmAccount adicionar](https://msdn.microsoft.com/library/mt619267.aspx) pela primeira vez.

## <a name="get-the-web-service-definition"></a>Obter a definição do serviço Web

Em seguida, obtenha o serviço Web ao contactar o cmdlet [Get-AzureRmMlWebService](https://msdn.microsoft.com/library/mt619267.aspx) . A definição do serviço Web é uma representação interna do modelo de formação do serviço web e não é diretamente pode ser modificada. Certifique-se de que está a obter a definição do serviço Web para a experiência de aspeto do Office e não a experiência de formação.

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Para determinar o nome do grupo de recursos de um serviço web já existente, execute o cmdlet Get-AzureRmMlWebService sem quaisquer parâmetros para apresentar os serviços web na sua subscrição. Localize o serviço web e, em seguida, veja o ID de serviço web. O nome do grupo de recursos é o quarto elemento ID, imediatamente após o elemento *resourceGroups* . No exemplo seguinte, o nome do grupo de recursos é predefinido-MachineLearning-SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Em alternativa, para determinar o nome do grupo de recursos de um serviço web já existente, inicie sessão no portal do Microsoft Azure máquina aprendizagem Web Services. Selecione o serviço web. O nome do grupo de recursos é o elemento quinto do URL do serviço web, imediatamente após o elemento *resourceGroups* . No exemplo seguinte, o nome do grupo de recursos é predefinido-MachineLearning-SouthCentralUS.

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-as-json"></a>Exportar a definição do serviço Web como JSON

Para modificar a definição para o modelo de formação para utilizar com recentemente formação modelo, primeiro tem de utilizar o cmdlet [AzureRmMlWebService de exportação](https://msdn.microsoft.com/library/azure/mt767935.aspx) para exportá-lo para um ficheiro de formato JSON.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob-in-the-json"></a>Atualize a referência para o blob ilearner no JSON.

Nos activos, localize [modelo qualificado], atualizar o valor de *uri* no nó *locationInfo* com o URI do blob ilearner. O URI é gerado ao combinar o *BaseLocation* e *RelativeLocation* a partir dos resultados do BES Reciclagem chamada.

     "asset3": {
        "name": "Retrain Samp.le [trained model]",
        "type": "Resource",
        "locationInfo": {
          "uri": "https://mltestaccount.blob.core.windows.net/azuremlassetscontainer/baca7bca650f46218633552c0bcbba0e.ilearner"
        },
        "outputPorts": {
          "Results dataset": {
            "type": "Dataset"
          }
        }
      },

## <a name="import-the-json-into-a-web-service-definition"></a>Importar o JSON para uma definição de serviço Web

Tem de utilizar o cmdlet [AzureRmMlWebService importar](https://msdn.microsoft.com/library/azure/mt767925.aspx) para converter novamente o ficheiro JSON modificado uma definição de serviço Web que pode utilizar para atualizar a experiência de Predicative.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service-with-new-web-service-definition"></a>Atualizar o serviço web com a nova definição do serviço Web

Por fim, utilizar o cmdlet [AzureRmMlWebService atualizar](https://msdn.microsoft.com/library/azure/mt767922.aspx) para atualizar a experiência de aspeto do Office.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'  -ServiceUpdates $wsd

## <a name="summary"></a>Resumo

Utilizar os cmdlets de gestão de máquina aprendizagem PowerShell, pode atualizar o modelo de formação de um serviço Web aspeto do Office, permitindo-cenários, tais como:

* Modelo periódico Reciclagem por novos dados.
* Distribuição de um modelo de clientes com o propósito de dando-lhes a Reciclagem o modelo com os seus próprios dados.
