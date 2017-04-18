<properties 
   pageTitle="Introdução ao Azure dados Lake Analytics através do Azure PowerShell | Azure" 
   description="Saiba como utilizar o PowerShell Azure para criar uma conta do arquivo de Lake de dados, criar uma tarefa de análise de Lake dados utilizando o U SQL e submeter a tarefa. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/21/2016"
   ms.author="edmaca"/>

# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>Tutorial: introdução ao Azure dados Lake Analytics através do PowerShell do Azure

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Saiba como utilizar o PowerShell Azure para criar contas do Azure dados Lake Analytics, definir tarefas de dados Lake Analytics no [U SQL](data-lake-analytics-u-sql-get-started.md)e submeter tarefas para contas dados Lake analítico. Para mais informações sobre Lake a análise de dados, consulte o artigo [Descrição geral do Azure dados Lake Analytics](data-lake-analytics-overview.md).

Neste tutorial, irá desenvolver uma tarefa que lê um separador separados por ficheiro de valores (TSV) e converte-o para um ficheiro (CSV valores) de separados por vírgulas. Percorrer o mesmo tutorial utilizar outras ferramentas suportadas, clique nos separadores na parte superior desta secção.

##<a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de ter o seguinte procedimento:

- **Azure uma subscrição**. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
- **Uma estação de trabalho com o Azure PowerShell**. Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).
    
##<a name="create-data-lake-analytics-account"></a>Criar dados Lake Analytics conta

Tem de ter uma conta de análise de Lake dados antes de poder executar quaisquer tarefas. Para criar uma conta de análise de Lake de dados, tem de especificar o seguinte procedimento:

- **Grupo de recursos do Azure**: conta A dados Lake Analytics tem de ser criada dentro de um grupo de recursos do Azure. [Gestor de recursos do Azure](../azure-resource-manager/resource-group-overview.md) permite-lhe trabalhar com os recursos na sua aplicação como um grupo. Pode implementar, atualizar ou eliminar todos os recursos para a sua aplicação numa operação de única e coordenada.  

    Enumerar os grupos de recursos na sua subscrição:
    
        Get-AzureRmResourceGroup
    
    Para criar um novo grupo de recursos:

        New-AzureRmResourceGroup `
            -Name "<Your resource group name>" `
            -Location "<Azure Data Center>" # For example, "East US 2"

- **Nome de conta de análise de dados Lake**
- **Localização**: um dos centros de dados Azure que suporte Lake a análise de dados.
- **Conta Lake de dados predefinido**: cada conta dados Lake Analytics tem uma conta de dados Lake predefinida.

    Para criar uma nova conta Lake de dados:

        New-AzureRmDataLakeStoreAccount `
            -ResourceGroupName "<Your Azure resource group name>" `
            -Name "<Your Data Lake account name>" `
            -Location "<Azure Data Center>"  # For example, "East US 2"

    > [AZURE.NOTE] O nome da conta dados Lake só tem de conter letras em minúsculas e números.



**Para criar uma conta de dados Lake Analytics**

1. Abra o ISE do PowerShell a partir da sua estação de trabalho do Windows.
2. Execute o seguinte script:

        $resourceGroupName = "<ResourceGroupName>"
        $dataLakeStoreName = "<DataLakeAccountName>"
        $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
        $location = "East US 2"
        
        Write-Host "Create a resource group ..." -ForegroundColor Green
        New-AzureRmResourceGroup `
            -Name  $resourceGroupName `
            -Location $location
        
        Write-Host "Create a Data Lake account ..."  -ForegroundColor Green
        New-AzureRmDataLakeStoreAccount `
            -ResourceGroupName $resourceGroupName `
            -Name $dataLakeStoreName `
            -Location $location 
        
        Write-Host "Create a Data Lake Analytics account ..."  -ForegroundColor Green
        New-AzureRmDataLakeAnalyticsAccount `
            -Name $dataLakeAnalyticsName `
            -ResourceGroupName $resourceGroupName `
            -Location $location `
            -DefaultDataLake $dataLakeStoreName
        
        Write-Host "The newly created Data Lake Analytics account ..."  -ForegroundColor Green
        Get-AzureRmDataLakeAnalyticsAccount `
            -ResourceGroupName $resourceGroupName `
            -Name $dataLakeAnalyticsName  

##<a name="upload-data-to-data-lake"></a>Carregar dados para dados Lake

Neste tutorial, irá processar alguns registos de pesquisa.  O registo de pesquisa pode ser armazenado no arquivo de dados Lake ou armazenamento de Blobs do Azure. 

Um ficheiro de registo de pesquisa de exemplo foi copiado para um contentor de Blobs do Azure público. Utilize o seguinte script do PowerShell para transferir o ficheiro da estação de trabalho e, em seguida, carregar o ficheiro para a conta de dados Lake arquivo predefinida da sua conta de análise de Lake de dados.

    $dataLakeStoreName = "<The default Data Lake Store account name>"
    
    $localFolder = "C:\Tutorials\Downloads\" # A temp location for the file. 
    $storageAccount = "adltutorials"  # Don't modify this value.
    $container = "adls-sample-data"  #Don't modify this value.

    # Create the temp location  
    New-Item -Path $localFolder -ItemType Directory -Force 

    # Download the sample file from Azure Blob storage
    $context = New-AzureStorageContext -StorageAccountName $storageAccount -Anonymous
    $blobs = Azure\Get-AzureStorageBlob -Container $container -Context $context
    $blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder

    # Upload the file to the default Data Lake Store account    
    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $localFolder"SearchLog.tsv" -Destination "/Samples/Data/SearchLog.tsv"

O seguinte script do PowerShell mostra-lhe como obter o nome do arquivo de Lake de dados predefinido para uma conta de análise de Lake de dados:


    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticsName).Properties.DefaultDataLakeAccount
    echo $dataLakeStoreName

>[AZURE.NOTE] Portal do Azure disponibiliza uma interface de utilizador para copiar os ficheiros de dados de exemplo para a conta de dados Lake arquivo predefinida. Para obter instruções, consulte o artigo [Introdução ao Azure dados Lake Analytics através do Portal do Azure](data-lake-analytics-get-started-portal.md#upload-data-to-the-default-data-lake-store-account).

Também pode aceder a dados Lake Analytics armazenamento de Blobs do Azure.  Para carregar os dados ao armazenamento de Blobs do Azure, consulte o artigo [Utilizar o PowerShell Azure com armazenamento do Windows Azure](../storage/storage-powershell-guide-full.md).

##<a name="submit-data-lake-analytics-jobs"></a>Submeter dados Lake Analytics tarefas

As tarefas de dados Lake Analytics estão escritas em linguagem U SQL. Para saber mais sobre U SQL, consulte o artigo [Introdução ao linguagem U SQL](data-lake-analytics-u-sql-get-started.md) e a [referência da linguagem U SQL](http://go.microsoft.com/fwlink/?LinkId=691348).

**Para criar um script de tarefa dados Lake Analytics**

- Criar um ficheiro de texto com o seguinte script U SQL e guardar o ficheiro de texto para a sua estação de trabalho:

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

    Este script do U SQL lê o ficheiro de dados de origem utilizando **Extractors.Tsv()**e, em seguida, cria um ficheiro csv com **Outputters.Csv()**. 
    
    Não modifique os dois caminhos, a menos que copie o ficheiro de origem para uma localização diferente.  A análise de dados Lake irá criar a pasta de saída, se não existe.
    
    É mais simples utilizar os caminhos relativos para ficheiros nas predefinições dados armazenados contas Lake. Também pode utilizar caminhos absolutos.  Por exemplo 
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    Tem de utilizar referências absolutos caminhos para aceder a ficheiros nas contas ligadas de armazenamento.  A sintaxe para os ficheiros guardados na conta de armazenamento do Windows Azure associada é:
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE] Azure Blob contentor com blobs públicos ou permissões de acesso de contentores público atualmente não são suportadas.    
    
    
**Para submeter a tarefa**

1. Abra o ISE do PowerShell a partir da sua estação de trabalho do Windows.
2. Execute o seguinte script:

        $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
        $usqlScript = "c:\tutorials\data-lake-analytics\copyFile.usql"
        
        $job = Submit-AzureRmDataLakeAnalyticsJob -Name "convertTSVtoCSV" -AccountName $dataLakeAnalyticsName –ScriptPath $usqlScript 

        Wait-AdlJob -Account $dataLakeAnalyticsName -JobId $job.JobId

        Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticsName -JobId $job.JobId

    Em script, o ficheiro de script de U SQL é armazenado na c:\tutorials\data-lake-analytics\copyFile.usql. Atualize o caminho do ficheiro em conformidade.
 
Depois da tarefa está concluída, pode utilizar os seguintes cmdlets para listar o ficheiro e transferir o ficheiro:
    
    $resourceGroupName = "<Resource Group Name>"
    $dataLakeAnalyticName = "<Data Lake Analytic Account Name>"
    $destFile = "C:\tutorials\data-lake-analytics\SearchLog-from-Data-Lake.csv"
    
    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.DefaultDataLakeAccount
    
    Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -path "/Output"
    
    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "/Output/SearchLog-from-Data-Lake.csv" -Destination $destFile

## <a name="see-also"></a>Consulte também

- Para ver o mesmo tutorial utilizar outras ferramentas, clique em seletores o separador na parte superior da página.
- Para ver uma consulta mais complexa, consulte o artigo [registos de Web site de analisar utilizando a análise do Azure dados Lake](data-lake-analytics-analyze-weblogs.md).
- Para começar a desenvolver aplicações U SQL, consulte o artigo [scripts de desenvolver U-SQL utilizando ferramentas de Lake de dados para o Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Para saber U SQL, consulte [Introdução ao idioma do Azure dados Lake Analytics U-SQL](data-lake-analytics-u-sql-get-started.md).
- Para tarefas de gestão, consulte o artigo [Gerir Azure dados Lake Analytics através do Portal do Azure](data-lake-analytics-manage-use-portal.md).
- Para obter uma descrição geral do Lake a análise de dados, consulte o artigo [Descrição geral do Azure dados Lake Analytics](data-lake-analytics-overview.md).

