<properties 
   pageTitle="Introdução ao Azure dados Lake Analytics utilizando a Interface de comandos do Azure | Microsoft Azure" 
   description="Saiba como utilizar a Interface de comandos do Azure para criar uma conta do arquivo de Lake de dados, criar uma tarefa de análise de Lake dados utilizando o U SQL e submeter a tarefa. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-command-line-interface-cli"></a>Tutorial: introdução ao Azure dados Lake Analytics utilizando Azure Interface da linha de comandos (CLI)

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Saiba como utilizar o clip do Azure para criar contas do Azure dados Lake Analytics, definir tarefas de dados Lake Analytics no [U SQL](data-lake-analytics-u-sql-get-started.md)e submeter tarefas para contas de análise de Lake de dados. Para mais informações sobre Lake a análise de dados, consulte o artigo [Descrição geral do Azure dados Lake Analytics](data-lake-analytics-overview.md).

Neste tutorial, irá desenvolver uma tarefa que lê um separador separados por ficheiro de valores (TSV) e converte-o para um ficheiro (CSV valores) de separados por vírgulas. Percorrer o mesmo tutorial utilizar outras ferramentas suportadas, clique nos separadores na parte superior desta secção.

##<a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de ter o seguinte procedimento:

- **Azure uma subscrição**. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
- **Clip azure**. Consulte o artigo [instalar e configurar o clip do Azure](../xplat-cli-install.md).
    - Transfira e instale a **versão de pré-lançamento** [Azure clip ferramentas](https://github.com/MicrosoftBigData/AzureDataLake/releases) para poder concluir este demonstração.
- **Autenticação**, utilizando o seguinte comando:

        azure login
    Para mais informações sobre a autenticação utilizando uma conta escolar ou profissional, consulte o artigo [ligar a uma subscrição Azure a partir do clip o Azure](../xplat-cli-connect.md).
- **Mudar para o modo de Gestor de recursos do Azure**, utilizando o seguinte comando:

        azure config mode arm
        
## <a name="create-data-lake-analytics-account"></a>Criar dados Lake Analytics conta

Tem de ter uma conta de análise de Lake dados antes de poder executar quaisquer tarefas. Para criar uma conta de análise de Lake de dados, tem de especificar o seguinte procedimento:

- **Grupo de recursos do Azure**: conta A dados Lake Analytics tem de ser criada dentro de um grupo de recursos do Azure. [Gestor de recursos do Azure](../azure-resource-manager/resource-group-overview.md) permite-lhe trabalhar com os recursos na sua aplicação como um grupo. Pode implementar, atualizar ou eliminar todos os recursos para a sua aplicação numa operação de única e coordenada.  

    Enumerar os grupos de recursos na sua subscrição:
    
        azure group list 
    
    Para criar um novo grupo de recursos:

        azure group create -n "<Resource Group Name>" -l "<Azure Location>"

- **Nome de conta de análise de dados Lake**
- **Localização**: um dos centros de dados Azure que suporte Lake a análise de dados.
- **Conta Lake de dados predefinido**: cada conta dados Lake Analytics tem uma conta de dados Lake predefinida.

    A lista a conta Lake de dados existente:
    
        azure datalake store account list

    Para criar uma nova conta Lake de dados:

        azure datalake store account create "<Data Lake Store Account Name>" "<Azure Location>" "<Resource Group Name>"

    > [AZURE.NOTE] O nome da conta dados Lake só tem de conter letras em minúsculas e números.



**Para criar uma conta de dados Lake Analytics**

        azure datalake analytics account create "<Data Lake Analytics Account Name>" "<Azure Location>" "<Resource Group Name>" "<Default Data Lake Account Name>"

        azure datalake analytics account list
        azure datalake analytics account show "<Data Lake Analytics Account Name>"          

![A análise de dados Lake Mostrar da conta](./media/data-lake-analytics-get-started-cli/data-lake-analytics-show-account-cli.png)

> [AZURE.NOTE] O nome da conta dados Lake Analytics só tem de conter letras em minúsculas e números.


## <a name="upload-data-to-data-lake-store"></a>Carregar dados para o arquivo de dados de Lake

Neste tutorial, irá processar alguns registos de pesquisa.  O registo de pesquisa pode ser armazenado no arquivo de dados Lake ou armazenamento de Blobs do Azure. 

Portal do Azure fornece uma interface de utilizador para copiar alguns ficheiros de dados de exemplo para a conta de dados Lake predefinida, que incluem um ficheiro de registo de pesquisa. Consulte o artigo [preparar dados de origem](data-lake-analytics-get-started-portal.md#prepare-source-data) para carregar os dados para a conta de dados Lake arquivo predefinida.

Para carregar ficheiros através do clip, utilize o seguinte comando:

    azure datalake store filesystem import "<Data Lake Store Account Name>" "<Path>" "<Destination>"
    azure datalake store filesystem list "<Data Lake Store Account Name>" "<Path>"

Também pode aceder a dados Lake Analytics armazenamento de Blobs do Azure.  Para carregar os dados ao armazenamento de Blobs do Azure, consulte o artigo [utilizar o clip do Azure com armazenamento do Windows Azure](../storage/storage-azure-cli.md).

## <a name="submit-data-lake-analytics-jobs"></a>Submeter dados Lake Analytics tarefas

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


    azure datalake analytics job create  "<Data Lake Analytics Account Name>" "<Job Name>" "<Script>"
    
    
Os seguintes comandos podem ser utilizados para listar tarefas, obter detalhes sobre o trabalho e cancelar tarefas:

    azure datalake analytics job cancel "<Data Lake Analytics Account Name>" "<Job Id>"
    azure datalake analytics job list "<Data Lake Analytics Account Name>"
    azure datalake analytics job show "<Data Lake Analytics Account Name>" "<Job Id>"

Depois da tarefa está concluída, pode utilizar os seguintes cmdlets para listar o ficheiro e transferir o ficheiro:
    
    azure datalake store filesystem list "<Data Lake Store Account Name>" "/Output"
    azure datalake store filesystem export "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" "<Destination>"
    azure datalake store filesystem read "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" <Length> <Offset>

## <a name="see-also"></a>Consulte também

- Para ver o mesmo tutorial utilizar outras ferramentas, clique em seletores o separador na parte superior da página.
- Para ver uma consulta mais complexa, consulte o artigo [registos de Web site de analisar utilizando a análise do Azure dados Lake](data-lake-analytics-analyze-weblogs.md).
- Para começar a desenvolver aplicações U SQL, consulte o artigo [scripts de desenvolver U-SQL utilizando ferramentas de Lake de dados para o Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Para saber U SQL, consulte [Introdução ao idioma do Azure dados Lake Analytics U-SQL](data-lake-analytics-u-sql-get-started.md).
- Para tarefas de gestão, consulte o artigo [Gerir Azure dados Lake Analytics através do Portal do Azure](data-lake-analytics-manage-use-portal.md).
- Para obter uma descrição geral do Lake a análise de dados, consulte o artigo [Descrição geral do Azure dados Lake Analytics](data-lake-analytics-overview.md).

