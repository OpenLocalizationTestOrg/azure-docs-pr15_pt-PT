<properties
   pageTitle="Criar HDInsight clusters com o arquivo de Lake Azure dados através do PowerShell | Azure"
   description="Utilizar o PowerShell do Azure para criar e utilizar HDInsight clusters com o Azure dados Lake"
   services="data-lake-store,hdinsight" 
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/21/2016"
   ms.author="nitinme"/>

# <a name="create-an-hdinsight-cluster-with-data-lake-store-using-azure-powershell"></a>Criar um cluster de HDInsight com o arquivo de dados de Lake através do PowerShell do Azure

> [AZURE.SELECTOR]
- [Através do Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Utilizar o PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)
- [Utilizar o Gestor de recursos](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

Saiba como utilizar o PowerShell do Azure para configurar um cluster de HDInsight (Hadoop, HBase ou tempestade) com o acesso ao Azure dados Lake arquivo. Algumas considerações importantes nesta versão:

* Só pode ser utilizado **para motores clusters (Linux) e Hadoop/tempestade clusters (Windows e Linux)**, o arquivo de Lake dados como uma conta de armazenamento adicional. A conta de armazenamento predefinida para essas clusters continuarão Azure armazenamento de Blobs (WASB).

* Pode ser utilizado **para HBase clusters (Windows e Linux)**, o arquivo de Lake dados como uma armazenamento predefinido ou armazenamento adicional.

> [AZURE.NOTE] Alguns pontos importantes a nota. 
> 
> * Opção para criar HDInsight clusters com acesso ao arquivo de dados de Lake só está disponível para versões de HDInsight 3,2 e 3.4 (para clusters Hadoop, HBase e tempestade no Windows, bem como Linux). Para clusters de motores no Linux, esta opção só está disponível em clusters HDInsight 3.4.
>
> * Tal como mencionado acima, arquivo de dados de Lake está disponível como o armazenamento de predefinido para alguns tipos de cluster (HBase) e armazenamento adicional para outros tipos de cluster (Hadoop, motores, tempestade). Utilizar o arquivo de dados de Lake como uma conta de armazenamento adicional não causam impacto na desempenho ou a capacidade de leitura/escrita ao armazenamento do cluster. Num cenário de onde o arquivo de dados de Lake é utilizado como armazenamento adicional, relacionado com o cluster ficheiros (como registos, etc.) são escritos ao armazenamento de predefinido (Blobs do Azure), enquanto os dados que pretende processar podem ser armazenados numa conta dados Lake loja.


Neste artigo, iremos aprovisionar um cluster de Hadoop com o arquivo de dados de Lake como armazenamento adicional.

Configurar o HDInsight para funcionar com o arquivo de dados de Lake através do PowerShell envolve os seguintes passos:

* Criar um arquivo de Lake de dados do Azure
* Configurar a autenticação de acesso baseado em funções ao arquivo de dados de Lake
* Criar cluster de HDInsight com autenticação ao arquivo de dados de Lake
* Executar uma tarefa de teste no cluster

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de ter o seguinte procedimento:

- **Azure uma subscrição**. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

- **Azure PowerShell 1.0 ou superior**. Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

- **Windows SDK**. Pode instalá-lo a partir de [aqui](https://dev.windows.com/en-us/downloads). Utilize esta para criar um certificado de segurança.

- **Principais de serviço de diretório do azure Active**. Os passos neste tutorial fornecem instruções sobre como criar um principal de serviço no Azure AD. No entanto, tem de ser um administrador do Azure AD para poder criar um principal de serviço. Se for um administrador do Azure AD, pode ignorar este pré-requisito e continuar com o tutorial.
    
    **Se estiver não um administrador do Azure AD**, não conseguir executar os passos necessários para criar um principal de serviço. Neste caso, o administrador do Azure AD tem primeiro de criar um principal de serviço antes de poder criar um cluster de HDInsight com dados Lake arquivo. Além disso, o capital de serviço deve ser criado utilizando um certificado, tal como descrito em [criar um serviço principal com certificado](../resource-group-authenticate-service-principal.md#create-service-principal-with-certificate). 


## <a name="create-an-azure-data-lake-store"></a>Criar um arquivo de Lake de dados do Azure

Siga estes passos para criar um arquivo de Lake de dados.

1. A partir do seu ambiente de trabalho, abrir uma nova janela do PowerShell do Azure e introduza o seguinte fragmento. Quando lhe for pedido para iniciar sessão, certifique-se de que iniciar sessão como um dos admininistrators/proprietário da subscrição:

        # Log in to your Azure account
        Login-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

    >[AZURE.NOTE] Se receber um erro semelhante a `Register-AzureRmResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` ao registar o fornecedor de recursos do arquivo de Lake de dados, é possível que o seu subsrcription não é whitelisted para Azure dados Lake arquivo. Certifique-se de que ativar a sua subscrição do Azure para pré-visualização pública dados Lake loja ao seguir estas [instruções](data-lake-store-get-started-portal.md#signup).

3. Uma conta Azure dados Lake arquivo está associada um grupo de recursos do Azure. Comece por criar um grupo de recursos do Azure.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![Criar um grupo de recursos do Azure] (./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateResourceGroup.png "Criar um grupo de recursos do Azure")

2. Crie uma conta Azure dados Lake loja. O nome da conta que especificar só tem de conter letras em minúsculas e números.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![Criar uma conta Azure dados Lake] (./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateADLAcc.png "Criar uma conta Azure dados Lake")

3. Certifique-se de que a conta estiver criada com êxito.

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    O resultado para este deverá ser **Verdadeiro**.

4. Carregar alguns dados de exemplo para o Azure dados Lake. Vamos utilizar este neste artigo para verificar se os dados estão acessíveis a partir de um cluster de HDInsight. Se está a procurar alguns dados de exemplo carregar, pode obter a pasta **Ambulância dados** a partir do [Azure dados Lake Git repositório](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).


        $myrootdir = "/"
        Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv


## <a name="set-up-authentication-for-role-based-access-to-data-lake-store"></a>Configurar a autenticação de acesso baseado em funções ao arquivo de dados de Lake

Cada subscrição Azure está associada um Azure Active Directory. Utilizadores e serviços que acedem a recursos de subscrição utilizando o Portal clássica Azure ou API do Gestor de recursos do Azure tem autenticar-se primeiro com essa Azure Active Directory. Acesso é concedido subscrições Azure e serviços, atribuindo-lhes o papel adequado num recurso Azure.  Para os serviços, um principal de serviço identifica o serviço no Azure Active Directory (AAD). Esta secção ilustra como conceder um serviço de aplicação, como o HDInsight, acesso a um recurso Azure (a conta Azure dados Lake loja que criou anteriormente) através da criação de um principal para a aplicação de serviço e atribuir funções da essa através do Azure PowerShell.

Para configurar a autenticação do Active Directory para Lake de dados do Azure, tem de executar as seguintes tarefas.

* Criar um certificado autoassinado
* Criar uma aplicação no Azure Active Directory e um Principal de serviço

### <a name="create-a-self-signed-certificate"></a>Criar um certificado autoassinado

Certifique-se de que tem o [Windows SDK](https://dev.windows.com/en-us/downloads) instalado antes de prosseguir com os passos nesta secção. Tem de ter também criado um diretório, tal como **C:\mycertdir**, onde o certificado será criado.

1. A partir da janela do PowerShell, navegue para a localização onde instalou o SDK do Windows (normalmente, `C:\Program Files (x86)\Windows Kits\10\bin\x86` e utilizar o [MakeCert] [ makecert] utility para criar um certificado autoassinado e uma chave privada. Utilize os comandos seguintes.

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir
        $startDate = (Get-Date).ToString('MM/dd/yyyy')
        $endDate = (Get-Date).AddDays(365).ToString('MM/dd/yyyy')

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -b $startDate -e $endDate -r -len 2048

    Vai ser-lhe para introduzir a palavra-passe da chave privada. Depois do comando executa com êxito, deverá ver uma **CertFile.cer** e **mykey.pvk** no diretório certificado que especificou.

4. Utilizar o [Pvk2Pfx] [ pvk2pfx] utility para converter os ficheiros. pvk e. cer que MakeCert criado um ficheiro. pfx. Execute o seguinte comando.

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    Quando lhe for pedido introduza a palavra-passe chave privada que especificou anteriormente. O valor especificado para o parâmetro **-referência** é a palavra-passe que está associada com o ficheiro. pfx. Depois do comando foi concluída com êxito, deverá ver também um CertFile.pfx no diretório certificado que especificou.

###  <a name="create-an-azure-active-directory-and-a-service-principal"></a>Criar um Azure Active Directory e um principal de serviço

Nesta secção, execute os passos para criar um serviço principal para uma aplicação do Azure Active Directory, atribuir uma função para o serviço de principal e autenticar como o capital de serviço, fornecendo um certificado. Execute os seguintes comandos para criar uma aplicação no Azure Active Directory.

1. Cole os seguintes cmdlets na janela da consola de PowerShell. Certifique-se o valor que especificar para a propriedade **- DisplayName** exclusivo. Além disso, os valores para a **Home page -** e **- IdentiferUris** são valores de marcador de posição e não esteja verificados.

        $certificateFilePath = "$certificateFileDir\CertFile.pfx"

        $password = Read-Host –Prompt "Enter the password" # This is the password you specified for the .pfx file

        $certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

        $rawCertificateData = $certificatePFX.GetRawCertData()

        $credential = [System.Convert]::ToBase64String($rawCertificateData)

        $application = New-AzureRmADApplication `
                    -DisplayName "HDIADL" `
                    -HomePage "https://contoso.com" `
                    -IdentifierUris "https://mycontoso.com" `
                    -KeyValue $credential  `
                    -KeyType "AsymmetricX509Cert"  `
                    -KeyUsage "Verify"  `
                    -StartDate $startDate  `
                    -EndDate $endDate

        $applicationId = $application.ApplicationId

2. Criar um principal de serviço utilizando o ID da aplicação.

        $servicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $applicationId

        $objectId = $servicePrincipal.Id

3. Conceda a acesso ao serviço principal para a ficheiro/pasta de arquivo de Lake de dados que irá aceder a partir do cluster HDInsight. O fragmento de abaixo oferece um acesso na raiz da conta de arquivo de Lake de dados.

        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path / -AceType User -Id $objectId -Permissions All

    No pedido, introduza **Y** para confirmar.

## <a name="create-an-hdinsight-cluster-with-authentication-to-data-lake-store"></a>Criar um cluster de HDInsight com autenticação ao arquivo de dados de Lake

Nesta secção, recomendamos criar um cluster de HDInsight Hadoop. Nesta versão, HDInsight cluster e o arquivo de Lake dados tem de ser na mesma localização (Leste dos EUA 2).

1. Começar com a obter o ID da subscrição inquilino. Irá necessitar que mais tarde.

        $tenantID = (Get-AzureRmContext).Tenant.TenantId

2. Nesta versão, para um cluster de Hadoop, arquivo de dados de Lake só pode ser utilizado como um armazenamento adicional para o cluster. O armazenamento de predefinido continuarão as blobs do Azure armazenamento (WASB). Assim sendo, vamos pela primeira vez criar a conta de armazenamento e contentores de armazenamento necessários para o cluster.

        # Create an Azure storage account
        $location = "East US 2"
        $storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name

        New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

        # Create an Azure Blob Storage container
        $containerName = "<ContainerName>"              # Provide a container name
        $storageAccountKey = Get-AzureRmStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        New-AzureStorageContainer -Name $containerName -Context $destContext

3. Crie o cluster HDInsight. Utilize os seguintes cmdlets.

        # Set these variables
        $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $rdpCredentials = Get-Credential

        New-AzureRmHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $httpCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -Version "3.2" -RdpCredential $rdpCredentials -RdpAccessExpiry (Get-Date).AddDays(14) -ObjectID $objectId -AadTenantId $tenantID -CertificateFilePath $certificateFilePath -CertificatePassword $password

    Depois do cmdlet foi concluída com êxito, deverá ver uma saída da seguinte forma:

        Name                      : hdiadlcluster
        Id                        : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/hdiadlgroup/providers/Mi
                                    crosoft.HDInsight/clusters/hdiadlcluster
        Location                  : East US 2
        ClusterVersion            : 3.2.7.707
        OperatingSystemType       : Windows
        ClusterState              : Running
        ClusterType               : Hadoop
        CoresUsed                 : 16
        HttpEndpoint              : hdiadlcluster.azurehdinsight.net
        Error                     :
        DefaultStorageAccount     :
        DefaultStorageContainer   :
        ResourceGroup             : hdiadlgroup
        AdditionalStorageAccounts :

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-store"></a>Executar tarefas de teste no cluster HDInsight para utilizar o arquivo de Lake de dados

Depois de ter configurado um cluster de HDInsight, pode executar tarefas de teste num cluster para testar a que o cluster HDInsight pode aceder a dados Lake loja. Para fazê-lo, podemos será executar uma tarefa de ramo de exemplo que cria uma tabela utilizando os dados de exemplo que carregou anteriormente para o arquivo de dados de Lake.

### <a name="for-a-linux-cluster"></a>Para um cluster de Linux

Nesta secção irá SSH para cluster e executar a consulta de ramo de exemplo. Windows não fornece um cliente SSH incorporado. Recomendamos que utilize **betumes**, que podem ser transferidos das [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Para mais informações sobre como utilizar betumes, consulte o artigo [Utilizar SSH com baseado em Linux Hadoop no HDInsight a partir do Windows ](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

1. Quando estiver ligado, inicie o clip Hive utilizando o seguinte comando:

        hive

2. Utilizar o clip, introduza as seguintes instruções para criar uma nova tabela denominada **veículos** utilizando os dados de exemplo do arquivo de dados Lake:

        DROP TABLE vehicles;
        CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestore>.azuredatalakestore.net:443/';
        SELECT * FROM vehicles LIMIT 10;

    Deverá visualizar um resultado semelhante ao seguinte:

        1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
        1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
        1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
        1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
        1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
        1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
        1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
        1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
        1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
        1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1


### <a name="for-a-windows-cluster"></a>Para um cluster do Windows

Utilize os seguintes cmdlets para executar a consulta ramo. Nesta consulta criar uma tabela a partir de dados do arquivo de dados Lake e, em seguida, executar uma consulta selecionar na tabela criada.

    $queryString = "DROP TABLE vehicles;" + "CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://$dataLakeStoreName.azuredatalakestore.net:443/';" + "SELECT * FROM vehicles LIMIT 10;"

    $hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString

    $hiveJob = Start-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $httpCredentials

    Wait-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $httpCredentials

Isto vai ter o seguinte resultado. **ExitValue** de 0 na saída sugere a tarefa foi concluída com êxito.

    Cluster         : hdiadlcluster.
    HttpEndpoint    : hdiadlcluster.azurehdinsight.net
    State           : SUCCEEDED
    JobId           : job_1445386885331_0012
    ParentId        :
    PercentComplete :
    ExitValue       : 0
    User            : admin
    Callback        :
    Completed       : done

Obter o resultado da tarefa utilizando o cmdlet seguinte:

    Get-AzureRmHDInsightJobOutput -ClusterName $clusterName -JobId $hiveJob.JobId -DefaultContainer $containerName -DefaultStorageAccountName $storageAccountName -DefaultStorageAccountKey $storageAccountKey -ClusterCredential $httpCredentials

O resultado da tarefa é semelhante ao seguinte:

    1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
    1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
    1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
    1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
    1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
    1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
    1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
    1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
    1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
    1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1


## <a name="access-data-lake-store-using-hdfs-commands"></a>Arquivo de Lake dados do Access utilizando os comandos HDFS

Depois de ter configurado o cluster de HDInsight para utilizar o arquivo de dados de Lake, pode utilizar os comandos de shell HDFS para aceder ao arquivo.

### <a name="for-a-linux-cluster"></a>Para um cluster de Linux

Nesta secção serão SSH para cluster e execute os comandos HDFS. Windows não fornece um cliente SSH incorporado. Recomendamos que utilize **betumes**, que podem ser transferidos das [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Para mais informações sobre como utilizar betumes, consulte o artigo [Utilizar SSH com baseado em Linux Hadoop no HDInsight a partir do Windows ](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

Quando estabelecida, utilize o seguinte comando do sistema de ficheiros HDFS para listar os ficheiros do arquivo de dados Lake.

    hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

Isto deve listar o ficheiro que carregou anteriormente para o arquivo de Lake de dados.

    15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
    Found 1 items
    -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

Também pode utilizar o `hdfs dfs -put` comando para carregar alguns ficheiros para a loja de Lake de dados e, em seguida, utilize `hdfs dfs -ls` para verificar se os ficheiros foram enviados com êxito.


### <a name="for-a-windows-cluster"></a>Para um cluster do Windows

1. Inicie sessão novo [Azure Portal](https://portal.azure.com).

2. Clique em **Procurar**, clique em **HDInsight clusters**e, em seguida, clique no cluster de HDInsight que criou.

3. Na pá cluster, clique em **Ambiente de trabalho remoto**e, em seguida, na pá **Ambiente de trabalho remoto** , clique em **Ligar**.

    ![Remoto num cluster HDI] (./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.HDI.PS.Remote.Desktop.png "Criar um grupo de recursos do Azure")

    Quando lhe for pedido, introduza as credenciais fornecidas para o utilizador de ambiente de trabalho remoto.

4. Na sessão remota, inicie o Windows PowerShell e utilize os comandos de sistema de ficheiros HDFS para listar os ficheiros do Azure arquivo de dados Lake.

        hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

    Isto deve listar o ficheiro que carregou anteriormente para o arquivo de Lake de dados.

        15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
        Found 1 items
        -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/vehicle1_09142014.csv

    Também pode utilizar o `hdfs dfs -put` comando para carregar alguns ficheiros para a loja de Lake de dados e, em seguida, utilize `hdfs dfs -ls` para verificar se os ficheiros foram enviados com êxito.

## <a name="see-also"></a>Consulte também

* [Portal: Criar um cluster de HDInsight para utilizar o arquivo de dados de Lake](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
