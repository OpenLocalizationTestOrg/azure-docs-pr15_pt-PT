<properties
pageTitle="Criar múltiplos modelos a partir de uma experiência | Microsoft Azure"
description="Utilize o PowerShell para criar vários modelos de aprendizagem automática e web pontos finais de serviço com o mesmo algoritmo mas formação diferentes conjuntos de dados."
services="machine-learning"
documentationCenter=""
authors="hning86"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="machine-learning"
ms.workload="data-services"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="10/03/2016"
ms.author="garye;haining"/>

# <a name="create-many-machine-learning-models-and-web-service-endpoints-from-one-experiment-using-powershell"></a>Criar muitos modelos de aprendizagem automática e web pontos finais de serviço a partir de uma experiência através do PowerShell

Eis um problema de aprendizagem máquina comum: que pretende criar muitos modelos que tenham o mesmo fluxo de trabalho de formação e utilizam o mesmo algoritmo, mas tem formação diferentes conjuntos de dados como entrada. Este artigo mostra-lhe como o fazer em escala no Azure máquina aprendizagem Studio utilizando apenas uma única experiência.

Por exemplo, digamos que é o proprietário um bicicleta global aluguer franchise de negócio. Pretende criar um modelo de regressão prever a procura de aluguer com base nos dados do histórico. Tiver 1.000 localizações de aluguer em todo o mundo e recolhidas um conjunto de dados para cada localização que inclui funcionalidades importantes, como data, hora, meteorologia e o tráfego que são específicos para cada localização.

Foi preparar o seu modelo de uma vez com uma versão unida do todos os conjuntos de dados através de todas as localizações. Mas uma vez que cada uma das suas localizações tem um ambiente exclusivo, uma abordagem melhor seria preparar o seu modelo de regressão separadamente utilizando o conjunto de dados para cada localização. Desta forma, cada modelo qualificado poderia ter em consideração tamanhos de arquivo diferente, volume, geografia, população, ambiente de tráfego de bicicleta amigável, *etc.*.

Que podem a melhor abordagem, mas não quiser criar 1.000 experiências de formação em Azure máquina aprender com cada um deles que representa uma localização exclusiva. Além de uma tarefa confuso, também é parece muito ineficaz uma vez que cada experiência teria as mesmas componentes exceto o conjunto de dados de formação.

Felizmente, podemos consegui-lo utilizando o [Azure máquina aprendizagem Reciclagem API](machine-learning-retrain-models-programmatically.md) e automatizar tarefas com o [Azure máquina aprendizagem PowerShell](machine-learning-powershell-module.md).

> [AZURE.NOTE] Para tornar o nosso exemplo mais rápido, podemos irá reduzir o número de localizações de 1000 e 10. Mas os mesmos princípios e procedimentos aplicam a 1.000 localizações. A única diferença é que se pretender dar formação a partir de conjuntos de 1.000 dados provavelmente pretende que a ter em conta básicos para realizar os seguintes scripts PowerShell em paralelo. Como fazê-lo está fora do âmbito deste artigo, mas pode encontrar exemplos de PowerShell multithreading na Internet.  

## <a name="set-up-the-training-experiment"></a>Configurar a experiência de formação

Iremos utilizar um exemplo [experimentar de formação](https://gallery.cortanaintelligence.com/Experiment/Bike-Rental-Training-Experiment-1) que já criámos na [Galeria de informações da empresa Cortana](http://gallery.cortanaintelligence.com). Abra esta experiência na área de trabalho [Azure máquina aprendizagem Studio](https://studio.azureml.net) .

>[AZURE.NOTE] Para seguir juntamente com neste exemplo, poderá querer utilizar uma área de trabalho padrão em vez de uma área de trabalho gratuita. Vamos está a criar um ponto final de cada cliente - para um total de 10 pontos finais - e que irão necessitar uma área de trabalho padrão, uma vez que uma área de trabalho gratuita está limitada à 3 pontos finais. Se tiver apenas uma área de trabalho livre, basta modificar os scripts abaixo para permitir apenas 3 localizações.

A experiência utiliza um módulo de **Importar dados** para importar o conjunto de dados de formação *customer001.csv* a partir de uma conta de armazenamento Azure. Imaginemos que tem recolhidas formação conjuntos de dados a partir de todas as localizações de aluguer de bicicleta e armazenados-los na mesma localização de armazenamento de Blobs com nomes de ficheiro que se situa entre *rentalloc001.csv* para *rentalloc10.csv*.

![imagem](./media/machine-learning-create-models-and-endpoints-with-powershell/reader-module.png)

Tenha em atenção que foi adicionado um módulo de **Saída de serviço Web** para o módulo de **Modelo de comboio** .
Quando esta experiência é implementada como um serviço web, o ponto final associado que saída irá devolver o modelo de formação no formato de um ficheiro de .ilearner.

Tenha em atenção que é configurado um parâmetro de serviço web para o URL que utiliza o módulo **Importar dados** . Esta opção permite--nos utilizar o parâmetro para especificar os conjuntos de dados individuais de formação para formar o modelo para cada localização.
Existem outras formas que poderia fizemos esta situação, tal como utilizar uma consulta SQL com um parâmetro de serviço web para obter dados a partir de uma base de dados do SQL Azure ou simplesmente utilizar um módulo de **Entrada de serviço Web** para transmitir num conjunto de dados para o serviço web.

![imagem](./media/machine-learning-create-models-and-endpoints-with-powershell/web-service-output.png)

Agora, vamos executar esta experiência de formação, utilizar a predefinição valor *rental001.csv* como o conjunto de dados de formação. Se visualizar o resultado do módulo **avaliar** (clique a saída e selecione **visualizar**), pode ver podemos obter um desempenho decente do *AUC* = 0.91. Neste momento, podemos está prontos para implementar um serviço web terminar este experiência de formação.

## <a name="deploy-the-training-and-scoring-web-services"></a>Implementar a formação e pontuação serviços web

Para implementar o serviço web de formação, clique no botão **O serviço Web** por baixo da tela de experiência e selecione **Implementar o serviço Web**. Ligar este serviço web "" bicicleta aluguer formação".

Agora precisamos de implementar o serviço web pontuação.
Para fazer isto, que possamos clique em **Configurar o serviço Web** por baixo da tela e selecione o **Serviço Web aspeto do Office**. Esta ação cria uma experiência de pontuação.
Irá precisamos de fazer alguns ajustes mínima para torná-lo a trabalhar como um serviço web, tal como remover a coluna etiqueta "not" a partir de dados de entrada e limitar a saída para apenas o id da instância e o correspondente previstos valor.

Para guardar o seu próprio que funcionam, pode simplesmente abrir a [experiência de aspeto do Office](https://gallery.cortanaintelligence.com/Experiment/Bike-Rental-Predicative-Experiment-1) na Galeria de que já está preparada.

Para implementar o serviço web, executar a experiência de aspeto do Office, em seguida, clique no botão **Implementar o serviço Web** por baixo da tela. Atribuir um nome de serviço web pontuação "Bicicleta aluguer pontuação" ".

## <a name="create-10-identical-web-service-endpoints-with-powershell"></a>Criar pontos finais de serviço web idênticos 10 com o PowerShell

Este serviço web é fornecido com um ponto final predefinido. Mas não estamos interessados como o ponto final predefinido desde que não pode ser atualizado. O que precisamos de o fazer consiste em criar 10 adicionais pontos finais, uma para cada localização. Vamos irá fazê-lo com o PowerShell.

Em primeiro lugar, vamos configurar o nosso ambiente PowerShell:

    Import-Module .\AzureMLPS.dll
    # Assume the default configuration file exists and is properly set to point to the valid Workspace.
    $scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
    $trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

Em seguida, execute o seguinte comando PowerShell:

    # Create 10 endpoints on the scoring web service.
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        Write-Host ('adding endpoint ' + $endpointName + '...')
        Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
    }

Agora já criámos 10 pontos finais e contiverem o mesmo modelo de formação formado em *customer001.csv*. Pode vê-los no Portal de gestão do Azure.

![imagem](./media/machine-learning-create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>Atualizar os pontos finais para utilizar em separado formação conjuntos de dados através do PowerShell

O passo seguinte é atualizar os pontos finais com os modelos de forma exclusiva formados em dados individuais de cada cliente. Mas primeiro precisamos de produtos agrícolas estes modelos de serviço web **Bicicleta aluguer formação** . Vamos ir novamente para o serviço web de **Bicicleta aluguer formação** . Precisamos de chamada respectivo ponto final BES 10 vezes com conjuntos de dados de diferentes formação 10 para produzir 10 diferentes modelos. Vamos utilizar o cmdlet do PowerShell **InovkeAmlWebServiceBESEndpoint** para o fazer.

Também terá de fornecer as credenciais para a sua conta de armazenamento de BLOBs para `$configContent`, nomeadamente, os campos `AccountName`, `AccountKey` e `RelativeLocation`. O `AccountName` pode ser um dos seus nomes de conta, conforme visto no **Portal de gestão do Azure clássico** (separador*armazenamento* ). Depois de clicar numa conta de armazenamento, respetivo `AccountKey` pode ser encontrado premindo o botão de **Teclas de acesso gerir** na parte inferior e copiando a *Chave primária do Access*. O `RelativeLocation` é o caminho relativamente às seu armazenamento onde será armazenado um novo modelo. Por exemplo, o caminho `hai/retrain/bike_rental/` no script abaixo aponta para um contentor denominada `hai`, e `/retrain/bike_rental/` são subpastas. Atualmente, não é possível criar subpastas através do portal da IU, mas existem [Vários Explorador de armazenamento do Azure](../storage/storage-explorers.md) que permitem-lhe para fazê-lo. É recomendado que crie um novo contentor no seu armazenamento para armazenar os modelos de formação novos (.ilearner ficheiros) da seguinte forma: a partir da sua página de armazenamento, clique no botão **Adicionar** na parte inferior e -lhe o nome `retrain`. Em resumo, as alterações necessárias para o script abaixo pertencem aos `AccountName`, `AccountKey` e `RelativeLocation` (:`"retrain/model' + $seq + '.ilearner"`).

    # Invoke the retraining API 10 times
    # This is the default (and the only) endpoint on the training web service
    $trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
    $submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
    $apiKey = $trainingSvcEp.PrimaryKey;
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
        $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
        Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
        Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
    }

>[AZURE.NOTE] O ponto final BES é o modo suportado apenas para esta operação. RRS não podem ser utilizados para produzir os modelos de formação.

Como pode ver acima, em vez de construir 10 BES emprego configuração json ficheiros diferentes, estamos dinamicamente crie em vez disso, a cadeia de configuração e feed-lo para o parâmetro *jobConfigString* do **InvokeAmlWebServceBESEndpoint** cmdlet, uma vez que não é realmente necessário manter uma cópia no disco.

Se tudo o que vai bem, após algum tempo deverá ver 10 .ilearner ficheiros, a partir do *model001.ilearner* para *model010.ilearner*, na sua conta de armazenamento Azure. Agora estamos prontos para atualizar os nossos 10 pontuação web pontos finais de serviço com estes modelos de utilizar o cmdlet do PowerShell **Patches AmlWebServiceEndpoint** . Lembre-se novamente podemos só pode correcção os pontos finais não predefinida que através de programação criámos anterior.

    # Patch the 10 endpoints with respective .ilearner models
    $baseLoc = 'http://bostonmtc.blob.core.windows.net/'
    $sasToken = '<my_blob_sas_token>'
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
        Write-Host ('Patching endpoint ' + $endpointName + '...');
        Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
    }

Isto deve ser executada bastante rapidamente. Quando termina a execução do, irá com êxito criámos 10 aspeto do Office web pontos finais de serviço, cada que contém um modelo de formação exclusivamente formado sobre o conjunto de dados específico para uma localização de aluguer, tudo a partir de uma experiência de formação único. Para confirmar se o fez, pode experimentar chamar estes pontos finais utilizando o cmdlet **InvokeAmlWebServiceRRSEndpoint** , fornecendo-los com os mesmos dados de entrada e que deve esperar ver resultados de diferentes predição uma vez que os modelos são qualificados com conjuntos de formação diferente.

## <a name="full-powershell-script"></a>Script PowerShell completo

Eis a listagem do código fonte completo:

    Import-Module .\AzureMLPS.dll
    # Assume the default configuration file exists and properly set to point to the valid workspace.
    $scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
    $trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

    # Create 10 endpoints on the scoring web service
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        Write-Host ('adding endpoint ' + $endpontName + '...')
        Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
    }

    # Invoke the retraining API 10 times to produce 10 regression models in .ilearner format
    $trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
    $submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
    $apiKey = $trainingSvcEp.PrimaryKey;
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
        $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
        Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
        Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
    }

    # Patch the 10 endpoints with respective .ilearner models
    $baseLoc = 'http://bostonmtc.blob.core.windows.net/'
    $sasToken = '?test'
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
        Write-Host ('Patching endpoint ' + $endpointName + '...');
        Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
    }
