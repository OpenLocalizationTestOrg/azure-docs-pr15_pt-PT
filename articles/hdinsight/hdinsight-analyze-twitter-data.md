<properties
    pageTitle="Analisar dados de Twitter com Hadoop no HDInsight | Microsoft Azure"
    description="Saiba como utilizar ramo para analisar dados de Twitter no Hadoop no HDInsight para localizar a frequência de utilização de uma determinada palavra."
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="jgao"/>

# <a name="analyze-twitter-data-using-hive-in-hdinsight"></a>Analisar dados de Twitter utilizando ramo no HDInsight

Web sites sociais são um das forças de condução principais para adoção grande dados. APIs públicos fornecidas pela sites semelhantes Twitter são uma origem de dados para analisar e compreender tendências populares útil. Neste tutorial, irá obter tweets utilizando um Twitter transmissão API e, em seguida, utilize Apache Hive Azure HDInsight para obter uma lista de utilizadores do Twitter que enviou as maioria dos tweets que continha uma determinada palavra.

> [AZURE.NOTE] Os passos neste documento requerem um cluster de HDInsight baseados no Windows. Para obter passos específicos a um cluster baseado em Linux, consulte o artigo [Analisar Twitter dados utilizando ramo no HDInsight (Linux)](hdinsight-analyze-twitter-data-linux.md).


##<a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de ter o seguinte procedimento:

- **Uma estação de trabalho** com o Azure PowerShell instalado e configurado. 

    Para executar scripts do Windows PowerShell, tem de executar o Azure PowerShell como administrador e definir a política de execução para *RemoteSigned*. Consulte o artigo [scripts de executar o Windows PowerShell][powershell-script].

    Antes de executar scripts do Windows PowerShell, certifique-se de que está ligado à sua subscrição do Azure utilizando o cmdlet seguinte:

        Login-AzureRmAccount

    Se tiver múltiplas subscrições Azure, utilize o cmdlet seguinte para definir a subscrição atual:

        Select-AzureRmSubscription -SubscriptionID <Azure Subscription ID>

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

- **Cluster de um Azure HDInsight**. Para obter instruções sobre cluster de aprovisionamento, consulte o artigo [começar a utilizar o HDInsight] [ hdinsight-get-started] ou [aprovisionar HDInsight clusters] [hdinsight-provision]. Terá do nome do cluster mais tarde no tutorial.

A tabela seguinte lista os ficheiros utilizados neste tutorial:

Ficheiros|Descrição
---|---
/Tutorials/twitter/data/tweets.txt|Os dados de origem para a tarefa de ramo.
/Tutorials/twitter/output|A pasta para a tarefa de ramo de registo de saída. O nome de ficheiro de saída do predefinido ramo tarefa é **000000_0**.
Tutorials/twitter/twitter.hql|O ficheiro de script HiveQL.
/Tutorials/twitter/JobStatus|O estado da tarefa Hadoop.


##<a name="get-twitter-feed"></a>Obter Twitter feed

Neste tutorial, irá utilizar o [Twitter APIs transmissão][twitter-streaming-api]. O específico Twitter transmissão API que irá utilizar é [Estados/filtro][twitter-statuses-filter].

>[AZURE.NOTE] Um ficheiro que contenha 10.000 tweets e o ficheiro de script colmeia (coberto na secção seguinte) tenham sido carregados num contentor de BLOBs público. Pode ignorar esta secção se quiser utilizar os ficheiros carregados.

[Dados de tweets](https://dev.twitter.com/docs/platform-objects/tweets) são armazenados no formato de objeto de JavaScript notação (JSON) que contém numa estrutura complexa aninhada. Em vez de escrever o número de linhas de código utilizando uma linguagem de programação convencional, pode transformar esta estrutura aninhada uma tabela Hive, para que podem ser consultado por uma linguagem SQL (Structured Query)-como linguagem denominada HiveQL.

Twitter utiliza OAuth para fornecer acesso autorizado aos seu API. OAuth é um protocolo de autenticação que permite aos utilizadores aprovar aplicações para funcionar em nome dos utilizadores sem partilhar a sua palavra-passe. Obter mais informações podem encontrar na [oauth.net](http://oauth.net/) ou o [manual para OAuth principiantes do](http://hueniverse.com/oauth/) excelente de Hueniverse.

O primeiro passo para utilizar OAuth é criar uma nova aplicação no site de programador Twitter.

**Para criar uma aplicação do Twitter**

1. Inicie sessão no [https://apps.twitter.com/](https://apps.twitter.com/). Clique na ligação **Inscreva-se agora** se não tiver uma conta do Twitter.
2. Clique em **Criar nova aplicação**.
3. Introduza o **nome**, **Descrição**, **Web site**. Pode fazer cópia de um URL para o campo **Web site** . A tabela seguinte mostra alguns valores de exemplo para utilizar:

    Campo|Valor
    ---|---
    Nome|MyHDInsightApp
    Descrição|MyHDInsightApp
    Web site|http://www.myhdinsightapp.com

4. Selecione **Sim, Concordo**e, em seguida, clique em **criar a aplicação Twitter**.
5. Clique no separador **permissões** . A permissão predefinido é **só de leitura**. Isto é suficiente para este tutorial.
6. Clique no separador **teclas e Tokens de acesso** .
7. Clique em **Criar meu token de acesso**.
8. Clique em **Testar OAuth** no canto superior direito da página.
9. Anote **chave do consumidor**, **secreta consumidor**, **token de acesso**e **secreta token de acesso**. Terá os valores mais tarde no tutorial.

Neste tutorial, irá utilizar o Windows PowerShell para tornar o serviço web de chamadas. Para uma .NET c# amostra, consulte o artigo [analisar em tempo real Twitter sentimento com HBase no HDInsight][hdinsight-hbase-twitter-sentiment]. A ferramenta de popular para efetuar chamadas de serviço web é [*Curl*][curl]. Laço pode ser transferido das [aqui][curl-download].

>[AZURE.NOTE] Quando utiliza o comando de Laço no Windows, utilize aspas em vez de aspas simples para os valores de opção.

**Para obter tweets**

1. Abra o Windows PowerShell Scripting ambiente integrado (ISE). (No ecrã Iniciar do Windows 8, escreva **PowerShell_ISE** e, em seguida, clique em **Windows PowerShell ISE**. Consulte o artigo [Iniciar do Windows PowerShell no Windows 8 e Windows][powershell-start].)

2. Copie o seguinte script para o painel de script:

        #region - variables and constants
        $clusterName = "<HDInsightClusterName>" # Enter the HDInsight cluster name

        # Enter the OAuth information for your Twitter application
        $oauth_consumer_key = "<TwitterAppConsumerKey>";
        $oauth_consumer_secret = "<TwitterAppConsumerSecret>";
        $oauth_token = "<TwitterAppAccessToken>";
        $oauth_token_secret = "<TwitterAppAccessTokenSecret>";

        $destBlobName = "tutorials/twitter/data/tweets.txt" # This script saves the tweets into this blob.

        $trackString = "Azure, Cloud, HDInsight" # This script gets the tweets containing these keywords.
        $track = [System.Uri]::EscapeDataString($trackString);
        $lineMax = 10000  # The script will get this number of tweets. It is about 3 minutes every 100 lines.
        #endregion

        #region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        Login-AzureRmAccount
        #endregion

        #region - Create a block blob object for writing tweets into Blob storage
        Write-Host "Get the default storage account name and Blob container name using the cluster name ..." -ForegroundColor Green
        $myCluster = Get-AzureRmHDInsightCluster -Name $clusterName
        $resourceGroupName = $myCluster.ResourceGroup
        $storageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
        $containerName = $myCluster.DefaultStorageContainer
        Write-Host "`tThe storage account name is $storageAccountName." -ForegroundColor Yellow
        Write-Host "`tThe blob container name is $containerName." -ForegroundColor Yellow

        Write-Host "Define the Azure storage connection string ..." -ForegroundColor Green
        $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
        $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"
        Write-Host "`tThe connection string is $storageConnectionString." -ForegroundColor Yellow

        Write-Host "Create block blob object ..." -ForegroundColor Green
        $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
        $storageClient = $storageAccount.CreateCloudBlobClient();
        $storageContainer = $storageClient.GetContainerReference($containerName)
        $destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
        #end region

        # region - Format OAuth strings
        Write-Host "Format oauth strings ..." -ForegroundColor Green
        $oauth_nonce = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes([System.DateTime]::Now.Ticks.ToString()));
        $ts = [System.DateTime]::UtcNow - [System.DateTime]::ParseExact("01/01/1970", "dd/MM/yyyy", $null)
        $oauth_timestamp = [System.Convert]::ToInt64($ts.TotalSeconds).ToString();

        $signature = "POST&";
        $signature += [System.Uri]::EscapeDataString("https://stream.twitter.com/1.1/statuses/filter.json") + "&";
        $signature += [System.Uri]::EscapeDataString("oauth_consumer_key=" + $oauth_consumer_key + "&");
        $signature += [System.Uri]::EscapeDataString("oauth_nonce=" + $oauth_nonce + "&");
        $signature += [System.Uri]::EscapeDataString("oauth_signature_method=HMAC-SHA1&");
        $signature += [System.Uri]::EscapeDataString("oauth_timestamp=" + $oauth_timestamp + "&");
        $signature += [System.Uri]::EscapeDataString("oauth_token=" + $oauth_token + "&");
        $signature += [System.Uri]::EscapeDataString("oauth_version=1.0&");
        $signature += [System.Uri]::EscapeDataString("track=" + $track);

        $signature_key = [System.Uri]::EscapeDataString($oauth_consumer_secret) + "&" + [System.Uri]::EscapeDataString($oauth_token_secret);

        $hmacsha1 = new-object System.Security.Cryptography.HMACSHA1;
        $hmacsha1.Key = [System.Text.Encoding]::ASCII.GetBytes($signature_key);
        $oauth_signature = [System.Convert]::ToBase64String($hmacsha1.ComputeHash([System.Text.Encoding]::ASCII.GetBytes($signature)));

        $oauth_authorization = 'OAuth ';
        $oauth_authorization += 'oauth_consumer_key="' + [System.Uri]::EscapeDataString($oauth_consumer_key) + '",';
        $oauth_authorization += 'oauth_nonce="' + [System.Uri]::EscapeDataString($oauth_nonce) + '",';
        $oauth_authorization += 'oauth_signature="' + [System.Uri]::EscapeDataString($oauth_signature) + '",';
        $oauth_authorization += 'oauth_signature_method="HMAC-SHA1",'
        $oauth_authorization += 'oauth_timestamp="' + [System.Uri]::EscapeDataString($oauth_timestamp) + '",'
        $oauth_authorization += 'oauth_token="' + [System.Uri]::EscapeDataString($oauth_token) + '",';
        $oauth_authorization += 'oauth_version="1.0"';

        $post_body = [System.Text.Encoding]::ASCII.GetBytes("track=" + $track);
        #endregion

        #region - Read tweets
        Write-Host "Create HTTP web request ..." -ForegroundColor Green
        [System.Net.HttpWebRequest] $request = [System.Net.WebRequest]::Create("https://stream.twitter.com/1.1/statuses/filter.json");
        $request.Method = "POST";
        $request.Headers.Add("Authorization", $oauth_authorization);
        $request.ContentType = "application/x-www-form-urlencoded";
        $body = $request.GetRequestStream();

        $body.write($post_body, 0, $post_body.length);
        $body.flush();
        $body.close();
        $response = $request.GetResponse() ;

        Write-Host "Start stream reading ..." -ForegroundColor Green

        Write-Host "Define a MemoryStream and a StreamWriter for writing ..." -ForegroundColor Green
        $memStream = New-Object System.IO.MemoryStream
        $writeStream = New-Object System.IO.StreamWriter $memStream

        $sReader = New-Object System.IO.StreamReader($response.GetResponseStream())

        $inrec = $sReader.ReadLine()
        $count = 0
        while (($inrec -ne $null) -and ($count -le $lineMax))
        {
            if ($inrec -ne "")
            {
                Write-Host "`n`t $count tweets received." -ForegroundColor Yellow

                $writeStream.WriteLine($inrec)
                $count ++
            }

            $inrec=$sReader.ReadLine()
        }
        #endregion

        #region - Write tweets to Blob storage
        Write-Host "Write to the destination blob ..." -ForegroundColor Green
        $writeStream.Flush()
        $memStream.Seek(0, "Begin")
        $destBlob.UploadFromStream($memStream)

        $sReader.close()
        #endregion

        Write-Host "Completed!" -ForegroundColor Green

3. Defina o primeiro cinco a oito variáveis no script:


    Variável|Descrição
    ---|---
    $clusterName|Este é o nome do cluster HDInsight em que pretende executar a aplicação.
    $oauth_consumer_key|Esta é a aplicação Twitter **chave do consumidor** que anotou anteriormente quando criou a aplicação Twitter.
    $oauth_consumer_secret|Esta é a aplicação Twitter **secreta consumidor** que anotou anterior.
    $oauth_token|Esta é a aplicação Twitter **token de acesso** que anotou anterior.
    $oauth_token_secret|Esta é a aplicação Twitter **secreta token de acesso** que anotou anterior.
    $destBlobName|Este é o nome de BLOBs de saída. O valor predefinido é **tutorials/twitter/data/tweets.txt**. Se alterar o valor predefinido, terá de atualizar os scripts do Windows PowerShell, consoante adequado.
    $trackString|O serviço web irá devolver tweets relacionados com estas palavras-chave. O valor predefinido é **Azure, na nuvem, HDInsight**. Se alterar o valor predefinido, irá atualizar os scripts do Windows PowerShell, consoante adequado.
    $lineMax|O valor determina quantos tweets vai ler o script. Demora cerca de três minutos para ler 100 tweets. Pode definir um número maior, mas irá demorar mais tempo a transferir.

5. Prima **F5** para executar o script. Caso se depare problemas, como solução, selecione todas as linhas e, em seguida, prima **F8**.
6. Deve ver "Concluída!" no final do resultado. Todas as mensagens de erro são apresentadas na vermelho.

Como um procedimento de validação, pode verificar o ficheiro de exportação, **/tutorials/twitter/data/tweets.txt**, no seu armazenamento de Blobs do Azure utilizando o Explorador de um armazenamento Azure ou Azure PowerShell. Para obter um script do Windows PowerShell de exemplo para indicar ficheiros, consulte o artigo [armazenamento de BLOBs utilizar com HDInsight][hdinsight-storage-powershell].



##<a name="create-hiveql-script"></a>Criar HiveQL script

Utilizar o Azure PowerShell, pode executar várias declarações de HiveQL um de cada vez ou a declaração de HiveQL para um ficheiro de script do pacote. Neste tutorial, irá criar um script HiveQL. O ficheiro de script tem de ser carregado para o armazenamento de Blobs do Azure. Na secção seguinte, será executada o ficheiro de script através do Azure PowerShell.

>[AZURE.NOTE] O ficheiro de script colmeia e um ficheiro que contenha 10.000 tweets tenham sido carregados num contentor de BLOBs público. Pode ignorar esta secção se quiser utilizar os ficheiros carregados.

O script HiveQL irá efetuar o seguinte procedimento:

1. **Largue a tabela tweets_raw** caso a tabela já existe.
2. **Criar a tabela Hive tweets_raw**. Esta tabela de estruturadas ramo temporária contém os dados para extrair suplementar, transformar e carregar o processamento de (ETL). Para obter informações sobre a partições, consulte o artigo [Hive tutorial][apache-hive-tutorial].  
3. **Carregar dados** a partir da pasta de origem, /tutorials/twitter/data. O conjunto de dados grandes tweets no formato do aninhadas JSON agora de logaritmos numa estrutura de tabela ramo temporária.
3. **Largue a tabela tweets** caso a tabela já existe.
4. **Criar a tabela tweets**. Antes de pode consultar contra o conjunto de dados tweets utilizando ramo, que precisa para executar o processo ETL outra. Este processo ETL define um esquema de tabela mais detalhado para os dados que tenham armazenados na tabela "twitter_raw".  
5. **Inserir tabela substituir**. Este complexa ramo irá iniciar um conjunto de tarefas de MapReduce longos pelo Hadoop cluster. Dependendo do seu conjunto de dados e o tamanho do seu cluster, isto pode demorar cerca de 10 minutos.
6. **Inserir substituir diretório**. Executar uma consulta e o conjunto de dados para um ficheiro de saída. Esta consulta irá devolver uma lista de utilizadores do Twitter que enviou a maior parte dos tweets que continha a palavra "Azure".

**Para criar um script ramo e carregue-o para Azure**

1. Abra o Windows PowerShell ISE.
2. Copie o seguinte script para o painel de script:

        #region - variables and constants
        $clusterName = "<Existing HDInsight Cluster Name>" # Enter your HDInsight cluster name
        $subscriptionID = "<Azure Subscription ID>"
        
        $sourceDataPath = "/tutorials/twitter/data"
        $outputPath = "/tutorials/twitter/output"
        $hqlScriptFile = "tutorials/twitter/twitter.hql"
        
        $hqlStatements = @"
        set hive.exec.dynamic.partition = true;
        set hive.exec.dynamic.partition.mode = nonstrict;
        
        DROP TABLE tweets_raw;
        CREATE EXTERNAL TABLE tweets_raw (
            json_response STRING
        )
        STORED AS TEXTFILE LOCATION '$sourceDataPath';
        
        DROP TABLE tweets;
        CREATE TABLE tweets
        (
            id BIGINT,
            created_at STRING,
            created_at_date STRING,
            created_at_year STRING,
            created_at_month STRING,
            created_at_day STRING,
            created_at_time STRING,
            in_reply_to_user_id_str STRING,
            text STRING,
            contributors STRING,
            retweeted STRING,
            truncated STRING,
            coordinates STRING,
            source STRING,
            retweet_count INT,
            url STRING,
            hashtags array<STRING>,
            user_mentions array<STRING>,
            first_hashtag STRING,
            first_user_mention STRING,
            screen_name STRING,
            name STRING,
            followers_count INT,
            listed_count INT,
            friends_count INT,
            lang STRING,
            user_location STRING,
            time_zone STRING,
            profile_image_url STRING,
            json_response STRING
        );
        
        FROM tweets_raw
        INSERT OVERWRITE TABLE tweets
        SELECT
            cast(get_json_object(json_response, '$.id_str') as BIGINT),
            get_json_object(json_response, '$.created_at'),
            concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
            substr (get_json_object(json_response, '$.created_at'),27,4)),
            substr (get_json_object(json_response, '$.created_at'),27,4),
            case substr (get_json_object(json_response, '$.created_at'),5,3)
                when "Jan" then "01"
                when "Feb" then "02"
                when "Mar" then "03"
                when "Apr" then "04"
                when "May" then "05"
                when "Jun" then "06"
                when "Jul" then "07"
                when "Aug" then "08"
                when "Sep" then "09"
                when "Oct" then "10"
                when "Nov" then "11"
                when "Dec" then "12" end,
            substr (get_json_object(json_response, '$.created_at'),9,2),
            substr (get_json_object(json_response, '$.created_at'),12,8),
            get_json_object(json_response, '$.in_reply_to_user_id_str'),
            get_json_object(json_response, '$.text'),
            get_json_object(json_response, '$.contributors'),
            get_json_object(json_response, '$.retweeted'),
            get_json_object(json_response, '$.truncated'),
            get_json_object(json_response, '$.coordinates'),
            get_json_object(json_response, '$.source'),
            cast (get_json_object(json_response, '$.retweet_count') as INT),
            get_json_object(json_response, '$.entities.display_url'),
            array(
                trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
            array(
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
            trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
            get_json_object(json_response, '$.user.screen_name'),
            get_json_object(json_response, '$.user.name'),
            cast (get_json_object(json_response, '$.user.followers_count') as INT),
            cast (get_json_object(json_response, '$.user.listed_count') as INT),
            cast (get_json_object(json_response, '$.user.friends_count') as INT),
            get_json_object(json_response, '$.user.lang'),
            get_json_object(json_response, '$.user.location'),
            get_json_object(json_response, '$.user.time_zone'),
            get_json_object(json_response, '$.user.profile_image_url'),
            json_response
        WHERE (length(json_response) > 500);
        
        INSERT OVERWRITE DIRECTORY '$outputPath'
        SELECT name, screen_name, count(1) as cc
            FROM tweets
            WHERE text like "%Azure%"
            GROUP BY name,screen_name
            ORDER BY cc DESC LIMIT 10;
        "@
        #endregion
        
        #region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        
        Try{
            Get-AzureRmSubscription
        }
        Catch{
            Login-AzureRmAccount
        }
        
        Select-AzureRmSubscription -SubscriptionId $subscriptionID
        
        #endregion
        
        #region - Create a block blob object for writing the Hive script file
        Write-Host "Get the default storage account name and container name based on the cluster name ..." -ForegroundColor Green
        $myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroupName = $myCluster.ResourceGroup
        $defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
        $defaultBlobContainerName = $myCluster.DefaultStorageContainer
        Write-Host "`tThe storage account name is $defaultStorageAccountName." -ForegroundColor Yellow
        Write-Host "`tThe blob container name is $defaultBlobContainerName." -ForegroundColor Yellow
        
        Write-Host "Define the connection string ..." -ForegroundColor Green
        $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
        $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$defaultStorageAccountName;AccountKey=$defaultStorageAccountKey"
        
        Write-Host "Create block blob objects referencing the hql script file" -ForegroundColor Green
        $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
        $storageClient = $storageAccount.CreateCloudBlobClient();
        $storageContainer = $storageClient.GetContainerReference($defaultBlobContainerName)
        $hqlScriptBlob = $storageContainer.GetBlockBlobReference($hqlScriptFile)
        
        Write-Host "Define a MemoryStream and a StreamWriter for writing ... " -ForegroundColor Green
        $memStream = New-Object System.IO.MemoryStream
        $writeStream = New-Object System.IO.StreamWriter $memStream
        $writeStream.Writeline($hqlStatements)
        #endregion
        
        #region - Write the Hive script file to Blob storage
        Write-Host "Write to the destination blob ... " -ForegroundColor Green
        $writeStream.Flush()
        $memStream.Seek(0, "Begin")
        $hqlScriptBlob.UploadFromStream($memStream)
        #endregion
        
        Write-Host "Completed!" -ForegroundColor Green

        

4. Defina as primeiras duas variáveis no script:

    Variável|Descrição
    ---|---
    $clusterName|Introduza o nome do cluster HDInsight em que pretende executar a aplicação.
    $subscriptionID|Introduza o seu ID de subscrição Azure.
    $sourceDataPath|A localização de armazenamento de Blobs do Azure onde as consultas de ramo vai ler os dados a partir de. Não tem de alterar esta variável.
    $outputPath|A localização de armazenamento de Blobs do Azure onde as consultas de ramo irão imprimir os resultados. Não tem de alterar esta variável.
    $hqlScriptFile|A localização e o nome do ficheiro do ficheiro de script HiveQL. Não tem de alterar esta variável.

5. Prima **F5** para executar o script. Caso se depare problemas, como solução, selecione todas as linhas e, em seguida, prima **F8**.
6. Deve ver "Concluída!" no final do resultado. Todas as mensagens de erro são apresentadas na vermelho.

Como um procedimento de validação, pode verificar o ficheiro de exportação, **/tutorials/twitter/twitter.hql**, no seu armazenamento de Blobs do Azure utilizando o Explorador de um armazenamento Azure ou Azure PowerShell. Para obter um script do Windows PowerShell de exemplo para indicar ficheiros, consulte o artigo [armazenamento de BLOBs utilizar com HDInsight][hdinsight-storage-powershell].  


##<a name="process-twitter-data-by-using-hive"></a>Processo Twitter dados utilizando ramo

Tiver concluído a todo o trabalho de preparação. Agora, pode invocar o script ramo e verificar os resultados.

### <a name="submit-a-hive-job"></a>Submeter uma tarefa de ramo

Utilize o seguinte script do Windows PowerShell para executar o script ramo. Terá de definir a variável de primeira.

>[AZURE.NOTE] Para utilizar os tweets e o script HiveQL carregou nas dois últimos secções, defina $hqlScriptFile para "/ tutorials/twitter/twitter.hql". Para utilizar as fases, que tenham sido carregados para um público blob para si, defina $hqlScriptFile para "wasbs://twittertrend@hditutorialdata.blob.core.windows.net/twitter.hql".

    #region variables and constants
    $clusterName = "<Existing Azure HDInsight Cluster Name>"
    $httpUserName = "admin"
    $httpUserPassword = "<HDInsight Cluster HTTP User Password>"
    
    #use one of the following
    $hqlScriptFile = "wasbs://twittertrend@hditutorialdata.blob.core.windows.net/twitter.hql"
    $hqlScriptFile = "/tutorials/twitter/twitter.hql"
    
    $statusFolder = "/tutorials/twitter/jobstatus"
    #endregion
    
    $myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $myCluster.ResourceGroup
    $defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    
    $defaultBlobContainerName = $myCluster.DefaultStorageContainer
    
    
    #region - Invoke Hive
    Write-Host "Invoke Hive ... " -ForegroundColor Green
    
    # Create the HDInsight cluster
    $pw = ConvertTo-SecureString -String $httpUserPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)
    
    Use-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName -HttpCredential $httpCredential 
    $response = Invoke-AzureRmHDInsightHiveJob -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -file $hqlScriptFile -StatusFolder $statusFolder #-OutVariable $outVariable
    
    Write-Host "Display the standard error log ... " -ForegroundColor Green
    $jobID = ($response | Select-String job_ | Select-Object -First 1) -replace ‘\s*$’ -replace ‘.*\s’
    Get-AzureRmHDInsightJobOutput -ClusterName $clusterName -JobId $jobID -DefaultContainer $defaultBlobContainerName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -HttpCredential $httpCredential
    #endregion

### <a name="check-the-results"></a>Verificar os resultados

Utilize o seguinte script do Windows PowerShell para verificar o resultado da tarefa ramo. Terá de definir as primeiras duas variáveis.

    #region variables and constants
    $clusterName = "<Existing Azure HDInsight Cluster Name>"
    
    $blob = "tutorials/twitter/output/000000_0" # The name of the blob to be downloaded.
    #engregion
    
    #region - Create an Azure storage context object
    Write-Host "Get the default storage account name and container name based on the cluster name ..." -ForegroundColor Green
    $myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $myCluster.ResourceGroup
    $defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultBlobContainerName = $myCluster.DefaultStorageContainer
    
    Write-Host "`tThe storage account name is $defaultStorageAccountName." -ForegroundColor Yellow
    Write-Host "`tThe blob container name is $defaultBlobContainerName." -ForegroundColor Yellow
    
    Write-Host "Create a context object ... " -ForegroundColor Green
    $storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey  
    #endregion
    
    #region - Download blob and display blob
    Write-Host "Download the blob ..." -ForegroundColor Green
    cd $HOME
    Get-AzureStorageBlobContent -Container $defaultBlobContainerName -Blob $blob -Context $storageContext -Force
    
    Write-Host "Display the output ..." -ForegroundColor Green
    Write-Host "==================================" -ForegroundColor Green
    cat "./$blob"
    Write-Host "==================================" -ForegroundColor Green
    #end region

> [AZURE.NOTE] A tabela Hive utiliza \001 como delimitador. O delimitador não estiver visível no resultado.

Depois dos resultados da análise foram colocados no armazenamento de Blobs do Azure, pode exportar os dados para um servidor de base de dados/SQL Azure SQL, exportar os dados para o Excel ao utilizar o Power Query ou ligar a sua aplicação aos dados utilizando o controlador de ODBC ramo de registo. Para obter mais informações, consulte o artigo [Utilizar Sqoop com HDInsight][hdinsight-use-sqoop], [Analisar os dados de atrasos em voos utilizando HDInsight][hdinsight-analyze-flight-delay-data], [Ligar o Excel ao HDInsight com o Power Query][hdinsight-power-query]e [Ligar o Excel ao HDInsight com o controlador de ODBC ramo de registo do Microsoft][hdinsight-hive-odbc].

##<a name="next-steps"></a>Próximos passos

Neste tutorial podemos ter visto como transformar um conjunto de dados JSON não estruturado uma tabela Hive estruturada para consultar, explorar e analisar os dados a partir do Twitter utilizando o HDInsight no Azure. Para obter mais informações, consulte o artigo:

- [Introdução ao HDInsight][hdinsight-get-started]
- [Analisar em tempo real Twitter sentimento com HBase no HDInsight][hdinsight-hbase-twitter-sentiment]
- [Analisar os dados de atrasos em voos utilizando HDInsight][hdinsight-analyze-flight-delay-data]
- [Ligar o Excel ao HDInsight com o Power Query][hdinsight-power-query]
- [Ligar o Excel ao HDInsight com o controlador ODBC ramo da Microsoft][hdinsight-hive-odbc]
- [Utilizar Sqoop com HDInsight][hdinsight-use-sqoop]

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176961.aspx


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage-powershell]: ../hdinsight-hadoop-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-odbc-driver.md
[hdinsight-hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
