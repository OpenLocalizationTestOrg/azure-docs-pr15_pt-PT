<properties
    pageTitle="Reciclagem de um serviço web aspeto do Office existente | Microsoft Azure"
    description="Saiba como um modelo de retransmissão e atualizar o serviço web para utilizar o modelo recentemente qualificado no Azure máquina aprendizagem."
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
    ms.date="10/06/2016"
    ms.author="v-donglo"/>


# <a name="retrain-an-existing-predictive-web-service"></a>Reciclagem de um serviço web aspeto do Office existente

Este documento descreve o processo de readaptação de cenário que se segue:

* Tiver uma experiência de formação e uma experiência de aspeto do Office que implementados como um serviço web operacionalizada.
* Tiver novos dados que pretende que o seu serviço web aspeto do Office utilizar para executar a sua pontuação.

Iniciar com o seu serviço web existente e experiências, que tem de seguir estes passos:

1. Atualize o modelo.
    1. Modificar a sua experiência de formação para permitir a entradas de serviço web e saídas.
    2. Implemente a experiência de formação como um serviço web readaptação.
    3. Utilize lote execução do serviço (BES a experiência de formação) para o modelo de retransmissão.
2. Utilize os cmdlets do PowerShell de aprendizagem do Azure máquina para atualizar a experiência de aspeto do Office.
    1.  Inicie sessão sua conta do Gestor de recursos do Azure.
    2.  Obter a definição do serviço web.
    3.  Exporte a definição do serviço web como JSON.
    4.  Atualize a referência para o blob ilearner no JSON.
    5.  Importe o JSON para uma definição de serviço web.
    6.  Atualize o serviço web com uma nova definição de serviço web.

## <a name="deploy-the-training-experiment"></a>Implementar a experiência de formação

Para implementar a experiência de formação como um serviço web readaptação, tem de adicionar entradas de serviço web e saídas para o modelo. Ligando um módulo de *Saída de serviço Web* para a experiência * [Comboio modelo] [ train-model] * módulo, ativar a experiência de formação produzir um novo modelo de formação que pode utilizar na sua experiência de aspeto do Office. Se tiver um módulo de *Modelo de avaliar* , também pode anexar saída de serviço web para obter os resultados de avaliação como saída.

Para atualizar a sua experiência de formação:

1. Ligar um módulo de *Entrada de serviço Web* para a introdução de dados (por exemplo, um módulo de *Dados em falta LIMPARB* ). Normalmente, que pretende garantir que os dados de entrada são processados da mesma forma como os dados originais de formação.
2. Ligar um módulo de *Saída de serviço Web* para a saída do seu módulo de *Modelo de comboio* .
3. Se tiver um módulo de *Modelo de avaliar* e pretende que os resultados de avaliação de saída, ligar um módulo de *Saída de serviço Web* para a saída do seu módulo de *Modelo de avaliar* .

Execute a sua experiência.

Em seguida, tem de implementar a experiência de formação como um serviço web que gera um modelo de formação e resultados de avaliação do modelo.  

Na parte inferior da tela experiência, clique em **Configurar o serviço Web**e, em seguida, selecione **Implementar [novo] serviço Web**. O portal de serviços Web do Azure máquina aprendizagem abre-se para a página de **Implementar o serviço Web** . Escreva um nome para o serviço web, selecione um plano de pagamento e, em seguida, clique em **Implementar**. Só pode utilizar o método de execução de lote para a criação de modelos de formação.


## <a name="retrain-the-model-with-new-data-by-using-bes"></a>Reciclagem o modelo com novos dados ao utilizar BES

Neste exemplo, estamos a utilizar c# para criar a aplicação readaptação. Também pode utilizar o código de exemplo Python ou R para realizar esta tarefa.

Para ligar as APIs readaptação:

1. Criar uma aplicação de consola do c# no Visual Studio (**Novo** > **projeto** > **Ambiente de trabalho Windows** > **Aplicação da consola**).
2.  Inicie sessão no portal do máquina aprendizagem Web Services.
3.  Clique em serviço web ao qual está a trabalhar.
2.  Clique em **consumir**.
3.  Na parte inferior da página **consumir** , na secção de **Exemplos de código** , clique em **lotes**.
5.  Copiar o exemplo c# código para execução lote e colá-la o ficheiro Program.cs. Certifique-se de que o espaço de nomes permanece intacto.

Adicione o pacote de NuGet Microsoft.AspNet.WebApi.Client, conforme especificado nos comentários. Para adicionar a referência a Microsoft.WindowsAzure.Storage.dll, poderá tem primeiro de instalar a [biblioteca do cliente para serviços de armazenamento do Windows Azure](https://www.nuget.org/packages/WindowsAzure.Storage).

A seguinte captura de ecrã mostra a página de **consumir** no portal do Azure máquina aprendizagem Web Services.

![Consumir página][1]

### <a name="update-the-apikey-declaration"></a>Atualizar a declaração de apikey

Localize a declaração de **apikey** :

    const string apiKey = "abc123"; // Replace this with the API key for the web service

Na secção **informações básicas de consumo** da página **consumir** , localize a chave primária e copiá-lo para a declaração de **apikey** .

### <a name="update-the-azure-storage-information"></a>Atualizar as informações de armazenamento do Windows Azure

O código de exemplo BES carregamentos de um ficheiro a partir de uma unidade local (por exemplo, "C:\temp\CensusIpnput.csv") para o armazenamento do Windows Azure, processa-lo e escreve os resultados armazenamento do Windows Azure.  

Para atualizar as informações de armazenamento do Windows Azure, terá de obter o nome da conta de armazenamento, chave e informações de contentor para a sua conta de armazenamento a partir do portal clássica Azure e, em seguida, atualização correspondi depois de executar a sua experiência, o fluxo de trabalho resultante deve ser semelhante ao seguinte:

![Fluxo de trabalho resultante depois de ter executado][4]valores de GN no código.

1. Inicie sessão no portal do Azure clássico.
1. Na coluna da navegação do lado esquerdo, clique em **armazenamento**.
1. A partir da lista de contas de armazenamento, selecione um para armazenar o modelo de retrained.
1. Na parte inferior da página, clique em **Gerir as teclas de acesso**.
1. Copiar e guarde a **Chave primária de acesso** e feche a caixa de diálogo.
1. Na parte superior da página, clique em **contentores**.
1. Selecione um contentor existente, ou crie um novo e guardar o nome.

Localize o *StorageAccountName*, *StorageAccountKey*e *StorageContainerName* declarações e atualizar os valores que guardou a partir do portal clássico.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name

Também tem de garantir que o ficheiro de entrada está disponível a localização especificada pelo utilizador no código.

### <a name="specify-the-output-location"></a>Especificar a localização de saída

Quando especificar a localização de saída a carga de pedir útil, a extensão do ficheiro que é especificada no *RelativeLocation* tem de ser especificada como `ilearner`. Consulte o exemplo seguinte:

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
            }
        },

Segue-se um exemplo de saída readaptação: ![Reciclagem saída][6]

## <a name="evaluate-the-retraining-results"></a>Avaliar os resultados readaptação

Quando executar a aplicação, o resultado inclui o URL e token de assinaturas acesso partilhado que são necessários para aceder aos resultados de avaliação.

Pode ver os resultados de desempenho do modelo retrained combinando os *BaseLocation*, *RelativeLocation*e *SasBlobToken* partir dos resultados de saída para *output2* (como mostrado na imagem saída readaptação anterior) e colar o URL completo na barra de endereço do browser.  

Examine os resultados para determinar se o modelo recentemente qualificado executa bem suficiente para substituir a existente.

Copie o *BaseLocation*, *RelativeLocation*e *SasBlobToken* dos resultados de saída.

## <a name="retrain-the-web-service"></a>Reciclagem o serviço web

Quando Reciclagem um novo serviço web, pode atualizar a definição do serviço web aspeto do Office para o novo modelo de formação de referência. A definição do serviço web é uma representação interna do modelo de formação do serviço web e não é diretamente pode ser modificada. Certifique-se de que está a obter a definição do serviço web para a experiência de aspeto do Office e não a experiência de formação.

## <a name="sign-in-to-azure-resource-manager"></a>Início de sessão para o Gestor de recursos Azure

Tem primeiro de iniciar sessão à sua conta Azure a partir de dentro do ambiente de PowerShell utilizando o cmdlet [AzureRmAccount adicionar](https://msdn.microsoft.com/library/mt619267.aspx) .

## <a name="get-the-web-service-definition-object"></a>Obtenha o objeto de definição de serviço Web

Em seguida, obtenha o objeto de definição de serviço Web ao contactar o cmdlet [Get-AzureRmMlWebService](https://msdn.microsoft.com/library/mt619267.aspx) .

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Para determinar o nome do grupo de recursos de um serviço web já existente, execute o cmdlet Get-AzureRmMlWebService sem quaisquer parâmetros para apresentar os serviços web na sua subscrição. Localize o serviço web e, em seguida, veja o ID de serviço web. O nome do grupo de recursos é o quarto elemento ID, imediatamente após o elemento *resourceGroups* . No exemplo seguinte, o nome do grupo de recursos é predefinido-MachineLearning-SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Em alternativa, para determinar o nome do grupo de recursos de um serviço web já existente, inicie sessão no portal do Azure máquina aprendizagem Web Services. Selecione o serviço web. O nome do grupo de recursos é o elemento quinto do URL do serviço web, imediatamente após o elemento *resourceGroups* . No exemplo seguinte, o nome do grupo de recursos é predefinido-MachineLearning-SouthCentralUS.

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-object-as-json"></a>Exportar o objeto de definição de serviço Web como JSON

Para modificar a definição de modelo de formação para utilizar o modelo de recentemente qualificado, primeiro tem de utilizar o cmdlet [AzureRmMlWebService de exportação](https://msdn.microsoft.com/library/azure/mt767935.aspx) para exportá-lo para um ficheiro de formato JSON.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob"></a>Atualizar a referência para o blob ilearner

Nos activos, localize [modelo qualificado], atualizar o valor de *uri* no nó *locationInfo* com o URI do blob ilearner. O URI é gerado ao combinar o *BaseLocation* e *RelativeLocation* a partir dos resultados do BES Reciclagem chamada.

     "asset3": {
        "name": "Retrain Sample [trained model]",
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

## <a name="import-the-json-into-a-web-service-definition-object"></a>Importar o JSON para um objeto de definição de serviço Web

Tem de utilizar o cmdlet [AzureRmMlWebService importar](https://msdn.microsoft.com/library/azure/mt767925.aspx) para converter novamente o ficheiro JSON modificado um objeto de definição de serviço Web que pode utilizar para atualizar a experiência predicative.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service"></a>Atualizar o serviço web

Por fim, utilize o cmdlet [AzureRmMlWebService atualizar](https://msdn.microsoft.com/library/azure/mt767922.aspx) para atualizar a experiência de aspeto do Office.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -

[1]: ./media/machine-learning-retrain-existing-arm-web-service/machine-learning-retrain-models-consume-page.png
[4]: ./media/machine-learning-retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE04.png
[6]: ./media/machine-learning-retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE06.png

<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
