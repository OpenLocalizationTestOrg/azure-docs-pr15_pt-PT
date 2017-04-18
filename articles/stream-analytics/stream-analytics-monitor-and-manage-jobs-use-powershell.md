<properties 
    pageTitle="Monitorizar e gerir tarefas de análise da cadeia com o PowerShell | Microsoft Azure" 
    description="Saiba como utilizar o Azure PowerShell e cmdlets para monitorizar e gerir tarefas de análise da cadeia." 
    keywords="Azure powershell, os cmdlets do azure powershell, comando do powershell, scripts de powershell" 
    services="stream-analytics" 
    documentationCenter="" 
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"/>


# <a name="monitor-and-manage-stream-analytics-jobs-with-azure-powershell-cmdlets"></a>Monitorizar e gerir tarefas de análise da cadeia com os cmdlets do PowerShell do Azure

Saiba como monitorizar e gerir recursos de análise da cadeia com os cmdlets do PowerShell do Azure e os scripts de powershell que executam tarefas básicas de análise da cadeia.

## <a name="prerequisites-for-running-azure-powershell-cmdlets-for-stream-analytics"></a>Pré-requisitos para executar os cmdlets do Azure PowerShell para análise da cadeia

 - Crie um grupo de recursos do Azure na sua subscrição. Segue-se um exemplo de script Azure PowerShell. Para obter informações do Azure PowerShell, consulte o artigo [instalar e configurar o Azure PowerShell](../powershell-install-configure.md);  

Azure PowerShell 0.9.8:  

        # Log in to your Azure account
        Add-AzureAccount

        # Select the Azure subscription you want to use to create the resource group if you have more than one subscription on your account.
        Select-AzureSubscription -SubscriptionName <subscription name>
 
        # If Stream Analytics has not been registered to the subscription, remove remark symbol below (#) to run the Register-AzureProvider cmdlet to register the provider namespace.
        #Register-AzureProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

        # Create an Azure resource group
        New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>

Azure PowerShell 1.0:  

        # Log in to your Azure account
        Login-AzureRmAccount

        # Select the Azure subscription you want to use to create the resource group.
        Get-AzureRmSubscription –SubscriptionName “your sub” | Select-AzureRmSubscription

        # If Stream Analytics has not been registered to the subscription, remove remark symbol below (#) to run the Register-AzureProvider cmdlet to register the provider namespace.
        #Register-AzureRmResourceProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
        
        # Create an Azure resource group
        New-AzureRMResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
        


> [AZURE.NOTE] Tarefas de análise da cadeia criadas através de programação não possui a monitorização ativada por predefinição.  Pode ativar manualmente a monitorização no Portal do Azure, navegue até à página de Monitor da tarefa e clicando no botão Ativar ou pode fazê-lo através de programação ao seguir os passos que se encontra no [Azure da cadeia Analytics - Monitor da cadeia a análise de tarefas de forma programática](stream-analytics-monitor-jobs.md).

## <a name="azure-powershell-cmdlets-for-stream-analytics"></a>Azure os cmdlets do PowerShell para análise da cadeia
Os seguintes cmdlets do Azure PowerShell podem ser utilizados para monitorizar e gerir tarefas de análise da cadeia de Azure. Note que Azure PowerShell tem versões diferentes. 
**Nos exemplos listados o primeiro comando é para Azure PowerShell 0.9.8, o segundo comando destina-se Azure PowerShell 1.0.** Os comandos do Azure PowerShell 1.0 partilhou terão sempre "AzureRM" no comando.

### <a name="get-azurestreamanalyticsjob--get-azurermstreamanalyticsjob"></a>Get-AzureStreamAnalyticsJob | Get-AzureRMStreamAnalyticsJob
Lista todas as tarefas de análise da cadeia definidas na subscrição Azure ou grupo de recursos especificado ou obtém informações sobre a tarefa sobre uma tarefa específica dentro de um grupo de recursos.

**Exemplo 1**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsJob

Este comando do PowerShell devolve informações sobre todas as tarefas de análise da cadeia a subscrição Azure.

**Exemplo 2**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 

Este comando do PowerShell devolve informações sobre todas as tarefas de análise da cadeia, no grupo de recursos StreamAnalytics-predefinido Central-US.

**Exemplo 3**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob

Este comando do PowerShell devolve informações sobre a tarefa de análise da cadeia StreamingJob no grupo de recursos StreamAnalytics-predefinido Central-US.

### <a name="get-azurestreamanalyticsinput--get-azurermstreamanalyticsinput"></a>Get-AzureStreamAnalyticsInput | Get-AzureRMStreamAnalyticsInput
Lista todas as entradas que são definidas numa tarefa de análise da cadeia especificada ou obtém informações sobre uma entrada específica.

**Exemplo 1**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Este comando do PowerShell devolve informações sobre todas as entradas definidas na tarefa de StreamingJob.

**Exemplo 2**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream

Este comando do PowerShell devolve informações sobre a entrada de dados denominada EntryStream definida na tarefa de StreamingJob.

### <a name="get-azurestreamanalyticsoutput--get-azurermstreamanalyticsoutput"></a>Get-AzureStreamAnalyticsOutput | Get-AzureRMStreamAnalyticsOutput
Apresenta todos os resultados que são definidos numa tarefa de análise da cadeia especificada ou obtém informações sobre uma saída específica.

**Exemplo 1**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Este comando do PowerShell devolve informações sobre saídas definidas na tarefa de StreamingJob.

**Exemplo 2**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output

Este comando do PowerShell devolve informações sobre o resultado com o nome definida na tarefa de StreamingJob de saída.

### <a name="get-azurestreamanalyticsquota--get-azurermstreamanalyticsquota"></a>Get-AzureStreamAnalyticsQuota | Get-AzureRMStreamAnalyticsQuota
Obtém informações sobre a quota da transmissão unidades numa região especificado.

**Exemplo 1**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsQuota –Location "Central US" 

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsQuota –Location "Central US" 

Este comando do PowerShell devolve informações sobre a quota de utilização e da transmissão unidades na região Central (EUA).

### <a name="get-azurestreamanalyticstransformation--getazurermstreamanalyticstransformation"></a>Get-AzureStreamAnalyticsTransformation | GetAzureRMStreamAnalyticsTransformation
Obtém informações sobre uma transformação específica definida numa tarefa de análise da cadeia.

**Exemplo 1**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob

Este comando do PowerShell devolve informações sobre a transformação denominada StreamingJob na tarefa de StreamingJob.

### <a name="new-azurestreamanalyticsinput--new-azurermstreamanalyticsinput"></a>Novo AzureStreamAnalyticsInput | Novo AzureRMStreamAnalyticsInput
Cria uma nova entrada dentro de uma tarefa de análise da cadeia ou actualiza uma entrada existente especificada.
  
O nome da entrada pode ser especificado no ficheiro .json ou na linha de comandos. Se ambos forem especificados, o nome na linha de comandos tem de ser igual o um no ficheiro.

Se especificar uma entrada de que já existe e não especificar – forçar parâmetro, o cmdlet irá pedir ou não substituir a entrada existente.

Se especificar – forçar o parâmetro e especificar um existente entrada nome, será substituída a entrada de dados sem confirmação.

Para informações detalhadas sobre a estrutura de ficheiro JSON e o conteúdo, consulte a [Entrada criar (Azure da cadeia Analytics)] [ msdn-rest-api-create-stream-analytics-input] secção da [sequência Analytics REST API referência biblioteca de gestão de][stream.analytics.rest.api.reference].

**Exemplo 1**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 

Este comando do PowerShell cria uma nova entrada do ficheiro Input.json. Se já estiver definida uma entrada existente com o nome especificado no ficheiro de definição de entrada, irá pedir o cmdlet substituí-la ou não.

**Exemplo 2**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream

Este comando do PowerShell cria uma nova entrada na tarefa de denominado EntryStream. Se já estiver definida uma entrada existente com este nome, o cmdlet irá pedir substituí-la ou não.

**Exemplo 3**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force

Este comando do PowerShell substitui a definição da origem de entrada existente chamada EntryStream com a definição do ficheiro.

### <a name="new-azurestreamanalyticsjob--new-azurermstreamanalyticsjob"></a>Novo AzureStreamAnalyticsJob | Novo AzureRMStreamAnalyticsJob
Cria uma nova tarefa de análise da cadeia no Microsoft Azure ou atualiza a definição de uma tarefa existente especificada.

O nome da tarefa pode ser especificado no ficheiro .json ou na linha de comandos. Se ambos forem especificados, o nome na linha de comandos tem de ser igual o um no ficheiro.

Se especificar um nome de tarefa que já existe e não especificar – forçar parâmetro, o cmdlet irá pedir ou não substituir a tarefa existente.

Se especificar – forçar o parâmetro e especificar um nome de tarefa existente, a definição da tarefa será substituída sem confirmação.

Para informações detalhadas sobre a estrutura de ficheiro JSON e o conteúdo, consulte a [Tarefa de análise da cadeia criar] [ msdn-rest-api-create-stream-analytics-job] secção da [sequência Analytics REST API referência biblioteca de gestão de][stream.analytics.rest.api.reference].

**Exemplo 1**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 

Este comando do PowerShell cria uma nova tarefa da definição no JobDefinition.json. Se já estiver definida uma tarefa existente com o nome especificado no ficheiro de definição de tarefa, irá pedir o cmdlet substituí-la ou não.

**Exemplo 2**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force

Este comando do PowerShell substitui a definição da tarefa para StreamingJob.

### <a name="new-azurestreamanalyticsoutput--new-azurermstreamanalyticsoutput"></a>Novo AzureStreamAnalyticsOutput | Novo AzureRMStreamAnalyticsOutput
Cria uma nova saída dentro de uma tarefa de análise da cadeia ou actualiza uma saída existente.  

O nome do resultado pode ser especificado no ficheiro .json ou na linha de comandos. Se ambos forem especificados, o nome na linha de comandos tem de ser igual o um no ficheiro.

Se especificar um resultado que já existe e não especificar – forçar parâmetro, o cmdlet irá pedir ou não substituir a saída existente.

Se especificar – forçar parâmetro e especifique o nome de saída de um existente, o resultado será substituído sem confirmação.

Para informações detalhadas sobre a estrutura de ficheiro JSON e o conteúdo, consulte a [Criar saída (Azure da cadeia Analytics)] [ msdn-rest-api-create-stream-analytics-output] secção da [sequência Analytics REST API referência biblioteca de gestão de][stream.analytics.rest.api.reference].

**Exemplo 1**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output

Este comando do PowerShell cria uma nova saída denominada "saída" na tarefa de StreamingJob. Se já estiver definida uma saída existente com este nome, o cmdlet irá pedir substituí-la ou não.

**Exemplo 2**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force

Este comando do PowerShell substitui a definição para "saída" na tarefa de StreamingJob.

### <a name="new-azurestreamanalyticstransformation--new-azurermstreamanalyticstransformation"></a>Novo AzureStreamAnalyticsTransformation | Novo AzureRMStreamAnalyticsTransformation
Cria uma nova transformação dentro de uma tarefa de análise da cadeia ou atualiza a transformação existente.
  
O nome da transformação pode ser especificado no ficheiro .json ou na linha de comandos. Se ambos forem especificados, o nome na linha de comandos tem de ser igual o um no ficheiro.

Se especificar uma transformação que já existe e não especificar – forçar parâmetro, o cmdlet irá pedir ou não substituir a transformação existente.

Se especificar – forçar o parâmetro e especificar um nome de transformação existente, a transformação será substituída sem confirmação.

Para informações detalhadas sobre a estrutura de ficheiro JSON e o conteúdo, consulte a [Transformação criar (Azure da cadeia Analytics)] [ msdn-rest-api-create-stream-analytics-transformation] secção da [sequência Analytics REST API referência biblioteca de gestão de][stream.analytics.rest.api.reference].

**Exemplo 1**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform

Este comando do PowerShell cria uma nova transformação denominada StreamingJobTransform na tarefa de StreamingJob. Se uma transformação de existente já está definida com este nome, o cmdlet pergunta se pretende ou não substituí-la.

**Exemplo 2**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force

 Este comando do PowerShell substitui a definição de StreamingJobTransform na tarefa de StreamingJob.

### <a name="remove-azurestreamanalyticsinput--remove-azurermstreamanalyticsinput"></a>Remover AzureStreamAnalyticsInput | Remover AzureRMStreamAnalyticsInput
Modo assíncrono elimina uma entrada específica de uma tarefa de análise da cadeia no Microsoft Azure.  
Se especificar – forçar parâmetro, será eliminada a entrada de dados sem confirmação.

**Exemplo 1**

Azure PowerShell 0.9.8:  

    Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream

Azure PowerShell 1.0:  

    Remove-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream

Este comando do PowerShell remove a entrada EventStream na tarefa de StreamingJob.  

### <a name="remove-azurestreamanalyticsjob--remove-azurermstreamanalyticsjob"></a>Remover AzureStreamAnalyticsJob | Remover AzureRMStreamAnalyticsJob
Modo assíncrono elimina uma tarefa de análise da cadeia específica no Microsoft Azure.  
Se especificar – forçar parâmetro, será eliminada a tarefa sem confirmação.

**Exemplo 1**

Azure PowerShell 0.9.8:  

    Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Azure PowerShell 1.0:  

    Remove-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Este comando do PowerShell remove a tarefa StreamingJob.  

### <a name="remove-azurestreamanalyticsoutput--remove-azurermstreamanalyticsoutput"></a>Remover AzureStreamAnalyticsOutput | Remover AzureRMStreamAnalyticsOutput
Modo assíncrono elimina uma saída específica a partir de uma tarefa de análise da cadeia no Microsoft Azure.  
Se especificar – forçar parâmetro, o resultado será eliminado sem confirmação.

**Exemplo 1**

Azure PowerShell 0.9.8:  

    Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Azure PowerShell 1.0:  

    Remove-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Este PowerShell comando procedimento remove a saída de saída da tarefa de StreamingJob.  

### <a name="start-azurestreamanalyticsjob--start-azurermstreamanalyticsjob"></a>Iniciar AzureStreamAnalyticsJob | Iniciar AzureRMStreamAnalyticsJob
Modo assíncrono ser implementada e inicia uma tarefa de análise da cadeia no Microsoft Azure.

**Exemplo 1**

Azure PowerShell 0.9.8:  

    Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z

Azure PowerShell 1.0:  

    Start-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z

Este comando do PowerShell inicia a tarefa StreamingJob com uma hora de início de saída personalizado definido para 12 de Dezembro de 2012, 12:12:12 UTC.


### <a name="stop-azurestreamanalyticsjob--stop-azurermstreamanalyticsjob"></a>Parar AzureStreamAnalyticsJob | Parar AzureRMStreamAnalyticsJob
Modo assíncrono deixa de uma tarefa de análise da cadeia de executarem no Microsoft Azure e anular atribui recursos que foram que foram a ser utilizado. A definição de tarefa e metadados permanecerão disponíveis para a sua subscrição através do portal do Azure e a gestão APIs, assim que a tarefa pode ser editada e reiniciada. Não lhe será cobrado para uma tarefa no estado de parado.

**Exemplo 1**

Azure PowerShell 0.9.8:  

    Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Azure PowerShell 1.0:  

    Stop-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Este comando do PowerShell, impede a tarefa StreamingJob.  

### <a name="test-azurestreamanalyticsinput--test-azurermstreamanalyticsinput"></a>Teste AzureStreamAnalyticsInput | Teste AzureRMStreamAnalyticsInput
Testa a capacidade de análise da cadeia de ligar a uma entrada especificada.

**Exemplo 1**

Azure PowerShell 0.9.8:  

    Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream

Azure PowerShell 1.0:  

    Test-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream

Este comando do PowerShell testes o estado da ligação da entrada EntryStream em StreamingJob.  

###<a name="test-azurestreamanalyticsoutput--test-azurermstreamanalyticsoutput"></a>Teste AzureStreamAnalyticsOutput | Teste AzureRMStreamAnalyticsOutput
Testa a capacidade de análise da cadeia de ligar a uma saída especificado.

**Exemplo 1**

Azure PowerShell 0.9.8:  

    Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Azure PowerShell 1.0:  

    Test-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Este testes de comando do PowerShell o estado da ligação de saída de saída no StreamingJob.  

## <a name="get-support"></a>Obter suporte
Para obter mais assistência, experimente o nosso [Fórum Azure da cadeia Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics). 


## <a name="next-steps"></a>Próximos passos

- [Introdução ao Azure sequência Analytics](stream-analytics-introduction.md)
- [Começar a utilizar o Azure da cadeia Analytics](stream-analytics-get-started.md)
- [Escala Azure da cadeia Analytics tarefas](stream-analytics-scale-jobs.md)
- [Azure sequência Analytics referência da linguagem de consulta](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da sequência Azure Analytics gestão REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 



[msdn-switch-azuremode]: http://msdn.microsoft.com/library/dn722470.aspx
[powershell-install]: http://azure.microsoft.com/documentation/articles/powershell-install-configure/
[msdn-rest-api-create-stream-analytics-job]: https://msdn.microsoft.com/library/dn834994.aspx
[msdn-rest-api-create-stream-analytics-input]: https://msdn.microsoft.com/library/dn835010.aspx
[msdn-rest-api-create-stream-analytics-output]: https://msdn.microsoft.com/library/dn835015.aspx
[msdn-rest-api-create-stream-analytics-transformation]: https://msdn.microsoft.com/library/dn835007.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
 
