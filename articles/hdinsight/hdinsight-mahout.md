<properties
    pageTitle="Gerar recomendações utilizando Mahout e HDInsight baseados no WIndows | Microsoft Azure"
    description="Saiba como utilizar a biblioteca de aprendizagem automática de Apache Mahout para gerar recomendações de filme com HDInsight baseados no Windows (Hadoop)."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/11/2016"
    ms.author="larryfr"/>

#<a name="generate-movie-recommendations-by-using-apache-mahout-with-hadoop-in-hdinsight"></a>Gerar recomendações de filme utilizando Apache Mahout com Hadoop no HDInsight

[AZURE.INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Saiba como utilizar a biblioteca com o Azure HDInsight de aprendizagem automática de [Apache Mahout](http://mahout.apache.org) para gerar recomendações de filme.

> [AZURE.NOTE] Os passos neste documento requerem um cliente do Windows e um cluster de HDInsight baseados no Windows. Para obter informações sobre como utilizar Mahout a partir de um cliente de Unix, OS X ou Linux com um cluster de baseado em Linux HDInsight, consulte o artigo [recomendações de filme gerar utilizando Apache Mahout com baseado em Linux Hadoop no HDInsight](hdinsight-hadoop-mahout-linux-mac.md)


##<a name="learn"></a>O que vai aprender

Mahout é uma [máquina de formação] [ ml] biblioteca para Apache Hadoop. Mahout contém algoritmos para processamento de dados, como filtrar, classificação e clusters. Neste artigo, irá utilizar um motor de recomendação para gerar recomendações de filme que sejam baseiam os seus amigos viu de filmes. Também irá Saiba como efetuar classificações de com uma floresta de decisão. Isto vai ensina-lhe o seguinte procedimento:

* Como executar tarefas Mahout ao utilizar o Windows PowerShell

* Como executar Mahout tarefas da linha de comandos Hadoop

* Como instalar Mahout no clusters HDInsight 3.0 e HDInsight 2.0

    > [AZURE.NOTE] Mahout é fornecido com a versão HDInsight 3.1 de sectores pertencem a. Se estiver a utilizar uma versão anterior do HDInsight, consulte o artigo [Instalar Mahout](#install) antes de continuar.

##<a name="prerequisites"></a>Pré-requisitos

- **Hadoop baseados no Windows um cluster no HDInsight**. Para obter informações sobre a criação de um, consulte o artigo [começar a utilizar o Hadoop no HDInsight][getstarted]
- **Uma estação de trabalho com o Azure PowerShell**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


##<a name="recommendations"></a>Gerar recomendações utilizando o Windows PowerShell

> [AZURE.NOTE] Apesar da tarefa é utilizada nesta secção funciona com o Windows PowerShell, muitas das aulas fornecidas com o Mahout atualmente não funcionam com o Windows PowerShell e tem de ser executados utilizando a linha de comandos Hadoop. Para obter uma lista de classes que não funcionam com o Windows PowerShell, consulte a secção de [resolução de problemas](#troubleshooting) .
>
> Para obter um exemplo da utilização da linha de comandos Hadoop para executar tarefas Mahout, consulte o artigo [/classify dados utilizando a linha de comandos Hadoop](#classify).

Uma das funções fornecido pelo Mahout é um motor de recomendação. Este motor aceita dados no formato de `userID`, `itemId`, e `prefValue` (a preferência de utilizadores do item). Mahout, em seguida, pode efetuar análise de cocriação ocorrência para determinar: _os utilizadores que tenham uma preferência de um item também têm uma preferência para estes outros itens_. Mahout determina, em seguida, os utilizadores com gosto item preferências, que podem ser utilizados para apresentar recomendações.

Segue-se um exemplo extremamente simple que utiliza filmes:

* __Cocriação ocorrência__: João, o Alice e o Rui gostou _Estrela os conflitos_, _O império greves novamente_e _devolver da Jedi_. Mahout determina que os utilizadores que gosta qualquer uma das seguintes filmes também como as outras duas.

* __Cocriação ocorrência__: Bruno e Alice gostou também _A ameaça de fantasma_, _ataque no que diz_e _Revenge do Sith_. Mahout determina que os utilizadores a quem gostou de três filmes anterior também como estes três.

* __Recomendação semelhança__: João porque gostou os três primeiros filmes, Mahout analisa filmes que outras pessoas com as preferências semelhantes gostou, mas João não viu (gosto/nominal). Neste caso, Mahout recomenda _A ameaça de fantasma_, _ataque no que diz_e _Revenge do Sith_.

###<a name="understanding-the-data"></a>Noções sobre os dados

Convenientemente, [GroupLens investigação] [ movielens] fornece dados de classificação para filmes num formato compatível com Mahout. Estes dados estão disponíveis no predefinição de armazenamento seu cluster na `/HdiSamples/MahoutMovieData`.

Existem dois ficheiros, `moviedb.txt` (informações sobre os filmes) e `user-ratings.txt`. O utilizador ratings.txt ficheiro é utilizado durante a análise, enquanto moviedb.txt é utilizado para fornecer informações de texto simples ao apresentar os resultados da análise.

Os dados contidos no ratings.txt do utilizador tem uma estrutura de `userID`, `movieID`, `userRating`, e `timestamp`, que lhe indica-nos como altamente cada utilizador classificou um filme. Eis um exemplo de dados:


    196 242 3   881250949
    186 302 3   891717742
    22  377 1   878887116
    244 51  2   880606923
    166 346 1   886397596

###<a name="run-the-job"></a>Executar a tarefa

Utilize o seguinte script do Windows PowerShell para executar uma tarefa que utiliza o motor de recomendação Mahout com os dados de filme:

    # The HDInsight cluster name.
    $clusterName = "the cluster name"
    
    #Get HTTPS/Admin credentials for submitting the job later
    $creds = Get-Credential
    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    $storageAccountKey=(Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
            
    #Create a storage content and upload the file
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey
            
    # NOTE: The version number in the file path
    # may change in future versions of HDInsight.
    $jarFile =  "file:///C:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar"
    #
    # If you are using an earlier version of HDInsight,
    # set $jarFile to the jar file you
    # uploaded.
    # For example,
    # $jarFile = "wasbs:///example/jars/mahout-core-0.9-job.jar"

    # The arguments for this job
    # * input - the path to the data uploaded to HDInsight
    # * output - the path to store output data
    # * tempDir - the directory for temp files
    $jobArguments = "--similarityClassname", "recommenditembased", `
                    "-s", "SIMILARITY_COOCCURRENCE", `
                    "--input", "wasbs:///HdiSamples/MahoutMovieData/user-ratings.txt",
                    "--output", "wasbs:///example/out",
                    "--tempDir", "wasbs:///example/temp"

    # Create the job definition
    $jobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
      -JarFile $jarFile `
      -ClassName "org.apache.mahout.cf.taste.hadoop.item.RecommenderJob" `
      -Arguments $jobArguments

    # Start the job
    $job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds

    # Wait on the job to complete
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
    # Download the output
    Get-AzureStorageBlobContent `
            -Blob example/out/part-r-00000 `
            -Container $container `
            -Destination output.txt `
            -Context $context
            
    # Write out any error information
    Write-Host "STDERR"
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

> [AZURE.NOTE] Tarefas de mahout não remove dados temporário que são criados durante o processamento da tarefa. O `--tempDir` parâmetro for especificado na tarefa de exemplo para identificar os ficheiros temporários num directório especifico.

A tarefa de Mahout não devolve o resultado para STDOUT. Em vez disso, armazena-lo no diretório saída especificado como __parte-r-00000__. O script transfere este ficheiro para __saída__ no diretório atual na sua estação de trabalho.

Segue-se um exemplo do conteúdo deste ficheiro:

    1   [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2   [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3   [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4   [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

A primeira coluna é o `userID`. Os valores contidos em ' [' e ']' são `movieId`:`recommendationScore`.

###<a name="view-the-output"></a>Ver os resultados

Apesar da saída gerada poderá ser OK para utilização numa aplicação, não é muito legível. O `moviedb.txt` do servidor podem ser utilizados para resolver a `movieId` a um filme nome, mas tem de transferir primeiro-lo e o ficheiro de classificações do servidor utilizando o seguinte script:

    # The HDInsight cluster name.
    $clusterName = "the cluster name"
    
    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    $storageAccountKey=(Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
    #Create a storage content and upload the file
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey
    #Download the files
    Get-AzureStorageBlobContent -blob "HdiSamples/MahoutMovieData/moviedb.txt" `
    -Container $container `
    -Destination moviedb.txt `
    -Context $context
    Get-AzureStorageBlobContent -blob "HdiSamples/MahoutMovieData/user-ratings.txt" `
    -Container $container `
    -Destination user-ratings.txt `
    -Context $context

Assim que tiver transferido os ficheiros, utilize o seguinte script do PowerShell para apresentar recomendações com nomes de filme:

    <#
    .SYNOPSIS
        Displays recommendations for movies.
    .DESCRIPTION
        Displays recommendations generated by Mahout
        with HDInsight example in a human readable format.
    .EXAMPLE
        .\Show-Recommendation -userId 4
            -userDataFile "user-ratings.txt"
            -movieFile "moviedb.txt"
            -recommendationFile "output.txt"
    #>

    [CmdletBinding(SupportsShouldProcess = $true)]
    param(
        #The user ID
        [Parameter(Mandatory = $true)]
        [String]$userId,

        [Parameter(Mandatory = $true)]
        [String]$userDataFile,

        [Parameter(Mandatory = $true)]
        [String]$movieFile,

        [Parameter(Mandatory = $true)]
        [String]$recommendationFile
    )
    # Read movie ID & description into hash table
    Write-Host "Reading movies descriptions" -ForegroundColor Green
    $movieById = @{}
    foreach($line in Get-Content $movieFile)
    {
        $tokens = $line.Split("|")
        $movieById[$tokens[0]] = $tokens[1]
    }
    # Load movies user has already seen (rated)
    # into a hash table
    Write-Host "Reading rated movies" -ForegroundColor Green
    $ratedMovieIds = @{}
    foreach($line in Get-Content $userDataFile)
    {
        $tokens = $line.Split("`t")
        if($tokens[0] -eq $userId)
        {
            # Resolve the ID to the movie name
            $ratedMovieIds[$movieById[$tokens[1]]] = $tokens[2]
        }
    }
    # Read recommendations generated by Mahout
    Write-Host "Reading recommendations" -ForegroundColor Green
    $recommendations = @{}
    foreach($line in get-content $recommendationFile)
    {
        $tokens = $line.Split("`t")
        if($tokens[0] -eq $userId)
        {
            #Trim leading/treailing [] and split at ,
            $movieIdAndScores = $tokens[1].TrimStart("[").TrimEnd("]").Split(",")
            foreach($movieIdAndScore in $movieIdAndScores)
            {
                #Split at : and store title and score in a hash table
                $idAndScore = $movieIdAndScore.Split(":")
                $recommendations[$movieById[$idAndScore[0]]] = $idAndScore[1]
            }
            break
        }
    }

    Write-Host "Rated movies" -ForegroundColor Green
    Write-Host "---------------------------" -ForegroundColor Green
    $ratedFormat = @{Expression={$_.Name};Label="Movie";Width=40}, `
                   @{Expression={$_.Value};Label="Rating"}
    $ratedMovieIds | format-table $ratedFormat
    Write-Host "---------------------------" -ForegroundColor Green

    write-host "Recommended movies" -ForegroundColor Green
    Write-Host "---------------------------" -ForegroundColor Green
    $recommendationFormat = @{Expression={$_.Name};Label="Movie";Width=40}, `
                            @{Expression={$_.Value};Label="Score"}
    $recommendations | format-table $recommendationFormat

Segue-se um exemplo de executar o script:

    PS C:\> show-recommendation.ps1 -userId 4 -userDataFile .\user-ratings.txt -movieFile .\moviedb.txt -recommendationFile .\output.txt

O resultado deverá ser semelhante ao seguinte:

    Reading movies descriptions
    Reading rated movies
    Reading recommendations
    Rated movies
    ---------------------------
    Movie                                    Rating
    -----                                    ------
    Devil's Own, The (1997)                  1
    Alien: Resurrection (1997)               3
    187 (1997)                               2
    (lines ommitted)

    ---------------------------
    Recommended movies
    ---------------------------

    Movie                                    Score
    -----                                    -----
    Good Will Hunting (1997)                 4.6504064
    Swingers (1996)                          4.6862745
    Wings of the Dove, The (1997)            4.6666665
    People vs. Larry Flynt, The (1996)       4.834559
    Everyone Says I Love You (1996)          4.707071
    Secrets & Lies (1996)                    4.818182
    That Thing You Do! (1996)                4.75
    Grosse Pointe Blank (1997)               4.8235292
    Donnie Brasco (1997)                     4.6792455
    Lone Star (1996)                         4.7099237  

##<a name="classify"></a>Classificar dados utilizando a linha de comandos Hadoop

Um dos métodos classificação disponíveis com Mahout é criar uma [floresta aleatória][forest]. Este é um processo de passos vários que envolve utilizando dados de formação para gerar árvores de decisão, em seguida, são utilizadas para classificar dados. Esta opção utiliza a classe de __org.apache.mahout.classifier.df.tools.Describe__ fornecida pela Mahout. Atualmente deve ser executado utilizando a linha de comandos Hadoop.

###<a name="load-the-data"></a>Carregar os dados

1. Transfira os seguintes ficheiros a partir [do conjunto de dados NSL KDD](http://nsl.cs.unb.ca/NSL-KDD/).

  * [KDDTrain +. ARFF](http://nsl.cs.unb.ca/NSL-KDD/KDDTrain+.arff): o ficheiro de formação

  * [KDDTest +. ARFF](http://nsl.cs.unb.ca/NSL-KDD/KDDTest+.arff): os dados de teste

2. Abra a cada ficheiro e remova as linhas na parte superior que comecem por '@', e, em seguida, guarde os ficheiros. Se estes não são removidos, vai receber mensagens de erro ao utilizar estes dados com Mahout.

2. Carregue os ficheiros para o __exemplo de dados__. Pode fazê-lo utilizando o seguinte script. Substitua o __nome de cluster__ com o nome do HDInsight cluster. Substituir o nome de ficheiro com o nome do tipo de ficheiro de ser carregado.

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterName="CLUSTERNAME"
        $fileToUpload="FILENAME"
        $blobPath="example/data/FILENAME"
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        
        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
            
        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob $blobPath `
            -Container $container `
            -Context $context

###<a name="run-the-job"></a>Executar a tarefa

1. Esta tarefa requer a linha de comandos Hadoop. Ativar o ambiente de trabalho remoto para HDInsight cluster, em seguida, ligar para o mesmo ao seguir as instruções em [ligar ao clusters HDInsight utilizando o RDP](hdinsight-administer-use-management-portal.md#rdp).

3. Depois de ligar, utilize o ícone de __linha de comandos Hadoop__ para abrir a linha de comandos Hadoop:

    ![Clip hadoop][hadoopcli]

3. Utilize o seguinte comando para gerar o descritor de ficheiro (__KDDTrain + .info__), que utiliza Mahout.

        hadoop jar "c:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar" org.apache.mahout.classifier.df.tools.Describe -p "wasbs:///example/data/KDDTrain+.arff" -f "wasbs:///example/data/KDDTrain+.info" -d N 3 C 2 N C 4 N C 8 N 2 C 19 N L

    O `N 3 C 2 N C 4 N C 8 N 2 C 19 N L` descreve os atributos de dados no ficheiro. Por exemplo, L indica uma etiqueta.

4. Crie uma floresta das pereiras, no decisão utilizando o seguinte comando:

        hadoop jar c:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar org.apache.mahout.classifier.df.mapreduce.BuildForest -Dmapred.max.split.size=1874231 -d wasbs:///example/data/KDDTrain+.arff -ds wasbs:///example/data/KDDTrain+.info -sl 5 -p -t 100 -o nsl-forest

    O resultado desta operação está armazenado no diretório __nsl floresta__ , que está localizado no armazenamento de cluster HDInsight em _ wasbs://user/&lt;nome de utilizador > / nsl floresta/nsl forest.seq. O &lt;nome de utilizador > é o nome de utilizador que utilizou para a sessão de ambiente de trabalho remoto. Este ficheiro não é legível por homem.

5. Teste a floresta por classificar o conjunto de dados __KDDTest + .arff__ . Utilize o seguinte comando:

        hadoop jar c:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar org.apache.mahout.classifier.df.mapreduce.TestForest -i wasbs:///example/data/KDDTest+.arff -ds wasbs:///example/data/KDDTrain+.info -m nsl-forest -a -mr -o wasbs:///example/data/predictions

    Este comando devolver informações de resumo acerca do processo de classificação semelhante ao seguinte:

        14/07/02 14:29:28 INFO mapreduce.TestForest:

        =======================================================
        Summary
        -------------------------------------------------------
        Correctly Classified Instances          :      17560       77.8921%
        Incorrectly Classified Instances        :       4984       22.1079%
        Total Classified Instances              :      22544

        =======================================================
        Confusion Matrix
        -------------------------------------------------------
        a       b       <--Classified as
        9437    274      |  9711        a     = normal
        4710    8123     |  12833       b     = anomaly

        =======================================================
        Statistics
        -------------------------------------------------------
        Kappa                                       0.5728
        Accuracy                                   77.8921%
        Reliability                                53.4921%
        Reliability (standard deviation)            0.4933

  Esta tarefa também produz um ficheiro localizado na __wasbs:///example/data/predictions/KDDTest+.arff.out__. No entanto, este ficheiro não é legível por homem.

> [AZURE.NOTE] Tarefas de mahout não substituam os ficheiros. Se pretender executar estas tarefas novamente, tem de eliminar os ficheiros que foram criados ao tarefas anteriores.

##<a name="troubleshooting"></a>Resolução de problemas

###<a name="install"></a>Instalar Mahout

Mahout estiver instalado no HDInsight 3.1 clusters e pode ser instalado manualmente no HDInsight 3.0 ou HDInsight 2.1 clusters através dos seguintes passos:

1. A versão do Mahout utilizar depende da versão HDInsight do seu cluster. Pode encontrar a versão de cluster visualizando as propriedades para o cluster no portal do Azure.

  * __Para HDInsight 2.1__, pode transferir um ficheiro de arquivo de Java (para caixa) que contém [Mahout 0,9](http://repo2.maven.org/maven2/org/apache/mahout/mahout-core/0.9/mahout-core-0.9-job.jar).

  * __Para HDInsight 3.0__, tem de [Criar Mahout da origem] [ build] e especifique a versão de Hadoop fornecida pela HDInsight. Instalar as pré-requisitos listadas na página Criar, transferir a origem e, em seguida, utilize o seguinte comando para criar os ficheiros de para caixa Mahout:

            mvn -Dhadoop2.version=2.2.0 -DskipTests clean package

        After the build completes, you can find the JAR file at __mahout\mrlegacy\target\mahout-mrlegacy-1.0-SNAPSHOT-job.jar__.

        > [AZURE.NOTE] Quando for disponibilizado Mahout 1.0, deverá conseguir utilizar os pacotes pré-concebidas com HDInsight 3.0.

2. Carregue o ficheiro para caixa para __exemplo/jarros__ no armazenamento predefinida para o seu cluster. Substituir o nome de cluster no script seguinte com o nome do seu cluster HDInsight e substituir o nome de ficheiro com o caminho para o ficheiro __mahout-coure 0,9 job.jar__ ...

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterName = "CLUSTERNAME"
        $fileToUpload = "FILENAME"
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        
        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
            
        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob "example/jars/mahout-core-0.9-job.jar" `
            -Container $container `
            -Context $context

###<a name="cannot-overwrite-files"></a>Não é possível substituir ficheiros

Mahout tarefas não não limpar ficheiros temporários que foram criadas durante o processamento. Além disso, as tarefas não irão substituir um ficheiro de exportação existente.

Para evitar erros quando Mahout tarefas em execução, elimine os ficheiros temporários e de saída entre execuções ou utilizar nomes exclusivos diretório temporário e de saída.

###<a name="cannot-find-the-jar-file"></a>Não é possível localizar o ficheiro para caixa

HDInsight 3.1 clusters incluem Mahout. O caminho e nome de ficheiro incluir o número da versão de Mahout estiver instalado no cluster. O exemplo de script do Windows PowerShell neste tutorial utiliza um caminho que seja válido a partir de Novembro de 2015, mas o número da versão irá alterar no futuro atualizações para HDInsight. Para determinar o caminho atual para o ficheiro Mahout JAR para o seu cluster, utilize o seguinte comando Windows PowerShell e, em seguida, modifique o script para fazer referência ao caminho do ficheiro que é devolvido:

    Use-AzureRmHDInsightCluster -ClusterName $clusterName
    #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
    Invoke-AzureRmHDInsightHiveJob `
            -StatusFolder "wasbs:///example/statusout" `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -Query '!${env:COMSPEC} /c dir /b /s ${env:MAHOUT_HOME}\examples\target\*-job.jar'

###<a name="nopowershell"></a>Classes que não funcionam com o Windows PowerShell

Tarefas de mahout que utilizam as seguintes classes devolvem uma variedade de mensagens de erro se forem utilizados a partir do Windows PowerShell:

* org.apache.mahout.utils.clustering.ClusterDumper
* org.apache.mahout.utils.SequenceFileDumper
* org.apache.mahout.utils.vectors.lucene.Driver
* org.apache.mahout.utils.vectors.arff.Driver
* org.apache.mahout.text.WikipediaToSequenceFile
* org.apache.mahout.clustering.streaming.tools.ResplitSequenceFiles
* org.apache.mahout.clustering.streaming.tools.ClusterQualitySummarizer
* org.apache.mahout.classifier.sgd.TrainLogistic
* org.apache.mahout.classifier.sgd.RunLogistic
* org.apache.mahout.classifier.sgd.TrainAdaptiveLogistic
* org.apache.mahout.classifier.sgd.ValidateAdaptiveLogistic
* org.apache.mahout.classifier.sgd.RunAdaptiveLogistic
* org.apache.mahout.classifier.sequencelearning.hmm.BaumWelchTrainer
* org.apache.mahout.classifier.sequencelearning.hmm.ViterbiEvaluator
* org.apache.mahout.classifier.sequencelearning.hmm.RandomSequenceGenerator
* org.apache.mahout.classifier.df.tools.Describe

Para executar tarefas que utilizam estas classes, ligar ao HDInsight cluster e executar as tarefas utilizando a linha de comandos Hadoop. Consulte o artigo [/classify dados utilizando a linha de comandos Hadoop](#classify) para obter um exemplo.

##<a name="next-steps"></a>Próximos passos

Agora que aprendeu como utilizar Mahout, descobri outras formas de trabalhar com dados no HDInsight:

* [Ramo de registo com HDInsight](hdinsight-use-hive.md)
* [Porco com HDInsight](hdinsight-use-pig.md)
* [MapReduce com HDInsight](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[aps]: ../powershell-install-configure.md
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: hdinsight-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[management]: https://manage.windowsazure.com/
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
 
