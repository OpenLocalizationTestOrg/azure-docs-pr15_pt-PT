<properties
   pageTitle="Introdução ao arquivo de dados de Lake | Azure"
   description="Utilizar o PowerShell do Azure para criar uma conta do arquivo de Lake de dados e executar operações básicas"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/04/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-azure-powershell"></a>Introdução ao arquivo de Lake Azure dados através do PowerShell do Azure

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [REST API](data-lake-store-get-started-rest-api.md)
- [Clip Azure](data-lake-store-get-started-cli.md)
- [NODE.js](data-lake-store-manage-use-nodejs.md)

Saiba como utilizar o PowerShell do Azure para criar uma conta do arquivo de Lake de dados do Azure e executar operações básicas, tais como criar pastas, carregar e transferir ficheiros de dados, eliminar a sua conta, etc. Para mais informações sobre o arquivo de dados de Lake, consulte o artigo [Descrição geral dos dados Lake loja](data-lake-store-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de ter o seguinte procedimento:

* **Azure uma subscrição**. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

* **Azure PowerShell 1.0 ou superior**. Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

## <a name="authentication"></a>Autenticação

Este artigo utiliza uma abordagem de autenticação mais simples com o arquivo de dados de Lake onde lhe for pedido para introduzir as suas credenciais de conta Azure. O nível de acesso ao arquivo de dados de Lake sistema de ficheiros de conta e, em seguida, é regulado pelo nível de acesso do utilizador com sessão iniciada. No entanto, existem outras abordagens também para autenticar com dados Lake loja, que são **autenticação de utilizador final** ou do **serviço de serviço**. Para obter instruções e mais informações sobre como autenticar, consulte o artigo [autenticar com o arquivo de dados de Lake utilizando o Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="create-an-azure-data-lake-store-account"></a>Criar uma conta do arquivo de Lake de dados do Azure

1. A partir do seu ambiente de trabalho, abra uma nova janela do Windows PowerShell e introduza o fragmento que se segue para iniciar sessão na sua conta Azure, defina a subscrição e registar o fornecedor de dados Lake arquivo. Quando lhe for pedido para iniciar sessão, certifique-se de que iniciar sessão como um dos admininistrators/proprietário da subscrição:

        # Log in to your Azure account
        Login-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Azure Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"


2. Uma conta Azure dados Lake arquivo está associada um grupo de recursos do Azure. Comece por criar um grupo de recursos do Azure.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![Criar um grupo de recursos do Azure] (./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Criar um grupo de recursos do Azure")

2. Crie uma conta Azure dados Lake loja. O nome que especificar tem de conter apenas números e letras em minúsculas.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![Criar uma conta do arquivo de Lake de dados do Azure] (./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Criar uma conta do arquivo de Lake de dados do Azure")

3. Certifique-se de que a conta estiver criada com êxito.

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    O resultado para este deverá ser **Verdadeiro**.

## <a name="create-directory-structures-in-your-azure-data-lake-store"></a>Criar estruturas de directório no arquivo de Lake dados Azure

Pode criar directórios sob a sua conta Azure dados Lake arquivo para gerir e armazenar dados.

1. Especifique um diretório de raiz.

        $myrootdir = "/"

2. Crie um novo directório denominado **mynewdirectory** na raiz especificada.

        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/mynewdirectory

3. Certifique-se de que o novo directório é criado com êxito.

        Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -Path $myrootdir

    Deverá ser apresentado um resultado semelhante ao seguinte:

    ![Certifique-se de diretório] (./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Certifique-se de diretório")


## <a name="upload-data-to-your-azure-data-lake-store"></a>Carregar dados para o arquivo de Lake de dados do Azure

Pode carregar os dados para o arquivo de dados de Lake diretamente no nível de raiz ou para um diretório que criou dentro da conta. Os fragmentos abaixo demonstram como carregar alguns dados de exemplo para o diretório (**mynewdirectory**) que criou na secção anterior.

Se está a procurar alguns dados de exemplo carregar, pode obter a pasta **Ambulância dados** a partir do [Azure dados Lake Git repositório](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Transferir o ficheiro e armazená-lo num diretório local no seu computador, tal como C:\sampledata\.

    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## <a name="rename-download-and-delete-data-from-your-data-lake-store"></a>Mudar o nome, transferir e eliminar os dados a partir do seu arquivo de dados de Lake

Para mudar o nome de um ficheiro, utilize o seguinte comando:

    Move-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Para transferir um ficheiro, utilize o seguinte comando:

    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

Para eliminar um ficheiro, utilize o seguinte comando:

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Quando lhe for pedido, introduza **Y** para eliminar o item. Se tiver mais do que um ficheiro para eliminar, pode disponibilizar todos os caminhos separados por ponto e vírgula.

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## <a name="delete-your-azure-data-lake-store-account"></a>Eliminar a sua conta do arquivo de Lake de dados do Azure

Utilize o seguinte comando para eliminar a sua conta do arquivo de Lake de dados.

    Remove-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

Quando lhe for pedido, introduza **Y** para eliminar a conta.


## <a name="next-steps"></a>Próximos passos

- [Proteger os dados no arquivo de dados de Lake](data-lake-store-secure-data.md)
- [Utilizar a análise de dados Azure Lake com o arquivo de dados de Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utilizar o Azure HDInsight com o arquivo de dados de Lake](data-lake-store-hdinsight-hadoop-use-portal.md)
