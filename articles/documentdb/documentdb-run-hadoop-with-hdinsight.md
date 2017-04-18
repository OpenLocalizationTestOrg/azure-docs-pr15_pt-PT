<properties
    pageTitle="Executar uma tarefa de Hadoop utilizando DocumentDB e HDInsight | Microsoft Azure"
    description="Saiba como executar uma tarefa de ramo, porco e MapReduce simple com DocumentDB e Azure HDInsight."
    services="documentdb"
    authors="dennyglee"
    manager="jhubbard"
    editor="mimig"
    documentationCenter=""/>


<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="java"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="denlee"/>

#<a name="DocumentDB-HDInsight"></a>Executar uma tarefa de Hadoop utilizando DocumentDB e HDInsight

Este tutorial mostra como executar [Apache Hive][apache-hive], [Apache porco][apache-pig]e [Apache Hadoop] [ apache-hadoop] MapReduce tarefas no Azure HDInsight com o conector de Hadoop do DocumentDB. Conexão de Hadoop do DocumentDB permite DocumentDB funcionar como uma origem e o sink para tarefas ramo, porco e MapReduce. Neste tutorial irá utilizar DocumentDB como a origem de dados e o destino dos Hadoop tarefas.

Depois de concluir este tutorial, poderá atender as seguintes questões:

- Como carregar dados a partir DocumentDB utilizando uma tarefa de ramo, porco ou MapReduce?
- Como armazenar dados DocumentDB utilizando uma tarefa de ramo, porco ou MapReduce?

Recomendamos que a introdução ao ver o vídeo seguinte, onde vamos executar através de uma tarefa de ramo utilizando DocumentDB e HDInsight.

> [AZURE.VIDEO use-azure-documentdb-hadoop-connector-with-azure-hdinsight]

Em seguida, regresse neste artigo, onde irá receber os detalhes na totalidade sobre como pode executar tarefas de análise DocumentDB dos seus dados.

> [AZURE.TIP] Neste tutorial assume que tem experiência prévia utilizando Apache Hadoop, ramo e/ou porco. Se estiver familiarizado com o Apache Hadoop, ramo e porco, recomendamos que visita a [documentação Apache Hadoop][apache-hadoop-doc]. Neste tutorial também assume que tiver experiência prévia com DocumentDB e se tiver uma conta de DocumentDB. Se estiver familiarizado DocumentDB ou não possui uma conta de DocumentDB, fórum veja os nossos [Introdução] [ getting-started] página.

Não ter tempo para concluir a iniciação e apenas pretende obter os scripts de exemplo completo do PowerShell para ramo, porco e MapReduce? Não é um problema, obtê-lo [aqui][documentdb-hdinsight-samples]. A transferência também contém os ficheiros hql, porco e java para estes exemplos.

## <a name="NewestVersion"></a>Versão mais recente

<table border='1'>
    <tr><th>Versão Hadoop conexão</th>
        <td>1.2.0</td></tr>
    <tr><th>Uri de script</th>
        <td>https://portalcontent.blob.Core.Windows.NET/scriptaction/documentdb-hadoop-Installer-v04.ps1</td></tr>
    <tr><th>Data de modificação</th>
        <td>26/04/2016</td></tr>
    <tr><th>Versões de HDInsight suportados</th>
        <td>3.1, 3,2</td></tr>
    <tr><th>Registo de alterações</th>
        <td>Actualizado DocumentDB Java SDK para 1.6.0</br>
            Suporte adicionada para coleções de sites com partições como uma origem e o sink</br>
        </td></tr>
</table>

## <a name="Prerequisites"></a>Pré-requisitos
Antes de seguir as instruções neste tutorial, certifique-se de que tem o seguinte procedimento:

- Uma conta de DocumentDB, uma base de dados e uma coleção de documentos no interior. Para obter mais informações, consulte o artigo [Introdução ao DocumentDB][getting-started]. Importar dados de exemplo para a sua conta de DocumentDB com a [ferramenta de importar DocumentDB][documentdb-import-data].
- Débito. Lê e escritas do HDInsight vão ser contadas no seu unidades pedido atribuído para as suas coleções. Para obter mais informações, consulte o artigo [Provisioned débito, unidades de pedido e operações de base de dados][documentdb-manage-throughput].
- Capacidade para um procedimento armazenado adicional dentro de cada coleção de saída. Os procedimentos armazenados são utilizados para transferir documentos resultantes. Para obter mais informações, consulte o artigo [débito aprovisionado e coleções de][documentdb-manage-document-storage].
- Capacidade para os documentos a partir de tarefas de ramo, porco ou MapReduce resultantes. Para obter mais informações, consulte o artigo [Desempenho e capacidade de gerir DocumentDB][documentdb-manage-collections].
- [*Opcional*] Capacidade de uma coleção de adicional. Para obter mais informações, consulte o artigo [Provisioned armazenamento de documentos e índice remissivo sobrecarga][documentdb-manage-document-storage].

> [AZURE.WARNING] Para evitar a criação de uma nova coleção de durante qualquer um dos trabalhos, pode imprimir os resultados para stdout, guardar a saída ao seu contentor WASB ou, especifique uma coleção de já existente. No caso de especificar uma coleção de existente, serão criados dentro da coleção de novos documentos e documentos já existentes, só serão afectados se existir um conflito na *ids de*. **A conexão automaticamente substituirá documentos existentes com conflitos de id**. Pode desativar esta funcionalidade, definindo a opção de upsert FALSO. Se upsert for FALSO e um conflito ocorre, a tarefa de Hadoop irá falhar; reportar um erro de conflito de id.


## <a name="ProvisionHDInsight"></a>Passo 1: Criar um novo cluster de HDInsight
Neste tutorial utiliza Script ação a partir do Portal do Azure para personalizar o seu cluster HDInsight. Neste tutorial, irá utilizamos Portal do Azure para criar o seu cluster HDInsight. Para obter instruções sobre como utilizar os cmdlets do PowerShell ou o .NET SDK HDInsight, consulte o artigo [Personalizar HDInsight clusters utilizando Script acção] [ hdinsight-custom-provision] artigo.

1. Inicie sessão no [Portal do Azure][azure-portal].

2. Clique em **+ Novo** na parte superior de painel de navegação esquerdo, procure **HDInsight** na barra de pesquisa superior sobre a nova pá.

3. **HDInsight** publicada pela **Microsoft** serão apresentados na parte superior dos resultados. Clique na mesma e, em seguida, clique em **Criar**.

4. No novo HDInsight Cluster criar pá, introduza o seu **Nome de Cluster** e selecione a **subscrição** que pretende aprovisionar este recurso em.

    <table border='1'>
        <tr><td>Nome do cluster</td><td>Nome do cluster.<br/>
   Nome de DNS tem de iniciar e terminar com um caráter alfanumérico e podem conter traços.<br/>
   O campo tem de ser uma cadeia entre 3 e 63 caracteres.</td></tr>
        <tr><td>Nome da subscrição</td>
            <td>Se tiver mais do que uma subscrição do Azure, selecione a subscrição que irá alojar o seu cluster HDInsight. </td></tr>
    </table>

5. Clique em **Selecionar tipo de Cluster** e definir as seguintes propriedades para os valores especificados.

    <table border='1'>
        <tr><td>Tipo de cluster</td><td><strong>Hadoop</strong></td></tr>
        <tr><td>Camadas de cluster</td><td><strong>Padrão</strong></td></tr>
        <tr><td>Sistema operativo</td><td><strong>Windows</strong></td></tr>
        <tr><td>Versão</td><td>versão mais recente</td></tr>
    </table>

    Agora, clique em **SELECIONAR**.

    ![Fornecer Hadoop HDInsight detalhes de cluster inicial][image-customprovision-page1]

6. Clique em **credenciais** para definir o seu ID de utilizador e as credenciais de acesso remoto. Selecione o **nome de utilizador de início de sessão de Cluster** e a **palavra-passe de início de sessão do Cluster**.

    Se pretender remoto para o seu cluster, selecione *Sim* na parte inferior da pá e fornecer um nome de utilizador e palavra-passe.

7. Clique em **Origem de dados** para configurar a sua localização principal para acesso aos dados. Selecione o **Método de seleção** e especifique uma conta de armazenamento já existente ou crie um novo.

8. Sobre a mesma pá, especifique um **Contentor predefinido** e uma **localização**. E, clique em **SELECIONAR**.

    > [AZURE.NOTE] Selecione uma localização perto sua região de conta DocumentDB para um melhor desempenho

8. Clique em **preços** para selecionar o número e o tipo de nós. Pode manter a configuração predefinida e dimensionar o número de nós de trabalho mais tarde.

9. Clique em **configuração opcional**, em seguida, as **ações de Script** pá a configuração opcional.

    Em ações de Script, introduza as seguintes informações para personalizar o seu cluster HDInsight.

    <table border='1'>
        <tr><th>Propriedade</th><th>Valor</th></tr>
        <tr><td>Nome</td>
            <td>Especifique um nome para a ação de script.</td></tr>
        <tr><td>Script URI</td>
            <td>Especifique o URI para o script que é chamado para personalizar o cluster.</br></br>
   Introduza: </br> <strong>https://portalcontent.blob.core.windows.net/scriptaction/documentdb-hadoop-installer-v04.ps1</strong>.</td></tr>
        <tr><td>Cabeça</td>
            <td>Clique na caixa de verificação para executar o script do PowerShell o nó de cabeça.</br></br>
            <strong>Selecione esta caixa de verificação</strong>.</td></tr>
        <tr><td>Trabalhador</td>
            <td>Clique na caixa de verificação para executar o script do PowerShell o nó do trabalho.</br></br>
            <strong>Selecione esta caixa de verificação</strong>.</td></tr>
        <tr><td>Zookeeper</td>
            <td>Clique na caixa de verificação para executar o script do PowerShell a Zookeeper.</br></br>
            <strong>Não é necessário</strong>.
            </td></tr>
        <tr><td>Parâmetros</td>
            <td>Especifique os parâmetros, se necessário pelo script.</br></br>
            <strong>Sem parâmetros conforme necessário</strong>.</td></tr>
    </table>

10. Crie um novo **Grupo de recursos** ou utilização de um grupo de recursos existente com a sua subscrição do Azure.

11. Agora, selecione **Afixar ao dashboard** para controlar a sua implementação e clique em **Criar**!

## <a name="InstallCmdlets"></a>Passo 2: Instalar e configurar o PowerShell do Azure

1. Instale o Azure PowerShell. Podem encontrar instruções [aqui][powershell-install-configure].

    > [AZURE.NOTE] Em alternativa, apenas para ramo de consultas, pode utilizar online Hive Editor do HDInsight. Para fazê-lo, inicie sessão no [Portal do Azure][azure-portal], clique em **HDInsight** no painel da esquerda para ver uma lista dos clusters HDInsight. Clique no cluster que pretende executar consultas de ramo e, em seguida, clique em **Consola de consulta**.

2. Abra o ambiente de scripts Azure PowerShell integrado:
    - Num computador a executar o Windows 8 ou Windows Server 2012 ou posterior, pode utilizar a pesquisa incorporada. A partir do ecrã Iniciar, escreva **Ise do powershell** e clique em **Enter**.
    - Num computador a executar uma versão anterior ao Windows 8 ou Windows Server 2012, utilize o menu Iniciar. Menu Iniciar, escreva **linha de comandos** na caixa de pesquisa, em seguida, na lista de resultados, clique em **linha de comandos**. Na linha de comandos, escreva **powershell_ise** e prima **Enter**.

3. Adicione a sua conta Azure.
    1. No painel da consola, escreva **AzureAccount adicionar** e clique em **Enter**.
    2. Escreva o endereço de e-mail associado à sua subscrição do Azure e clique em **continuar**.
    3. Escreva a palavra-passe para a sua subscrição Azure.
    4. Clique em **Iniciar sessão**.

4. O diagrama seguinte identifica as partes importantes do seu ambiente de criação de scripts de PowerShell do Azure.

    ![Diagrama para Azure PowerShell][azure-powershell-diagram]

## <a name="RunHive"></a>Passo 3: Executar uma tarefa de ramo utilizando DocumentDB e HDInsight

> [AZURE.IMPORTANT] Todas as variáveis indicadas por < > tem de ser preenchidas utilizando as definições de configuração.

1. Defina as seguintes variáveis no painel de Script do PowerShell.

        # Provide Azure subscription name, the Azure Storage account and container that is used for the default HDInsight file system.
        $subscriptionName = "<SubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<AzureStorageContainerName>"

        # Provide the HDInsight cluster name where you want to run the Hive job.
        $clusterName = "<HDInsightClusterName>"

2. <p>Vamos começar a construir a sua cadeia de consulta. Vamos irá escrever uma consulta de ramo que leva-o até selos de tempo de todos os documentos sistema gerado (_ts) ids exclusivos (_rid) a partir de uma coleção de DocumentDB calcula todos os documentos pelos minutos, e, em seguida, armazena os resultados de volta para uma nova coleção de DocumentDB.</p>

    <p>Primeiro, vamos criar uma tabela Hive da nossa coleção DocumentDB. Adicione o seguinte fragmento de código para o PowerShell Script painel <strong>depois do</strong> fragmento de código a partir de #1. Certifique-se de incluir o t de parâmetro de DocumentDB.query opcional compactar nossos documentos para _ts basta e _rid.</p>

    > [AZURE.NOTE]**Nomenclatura DocumentDB.inputCollections não era engane.** Sim, podemos permitir a adição de várias colecções como uma entrada: </br>

        '*DocumentDB.inputCollections*' = '*\<DocumentDB Input Collection Name 1\>*,*\<DocumentDB Input Collection Name 2\>*' A1A</br> The collection names are separated without spaces, using only a single comma.


        # Create a Hive table using data from DocumentDB. Pass DocumentDB the query to filter transferred data to _rid and _ts.
        $queryStringPart1 = "drop table DocumentDB_timestamps; "  +
                            "create external table DocumentDB_timestamps(id string, ts BIGINT) "  +
                            "stored by 'com.microsoft.azure.documentdb.hive.DocumentDBStorageHandler' "  +
                            "tblproperties ( " +
                                "'DocumentDB.endpoint' = '<DocumentDB Endpoint>', " +
                                "'DocumentDB.key' = '<DocumentDB Primary Key>', " +
                                "'DocumentDB.db' = '<DocumentDB Database Name>', " +
                                "'DocumentDB.inputCollections' = '<DocumentDB Input Collection Name>', " +
                                "'DocumentDB.query' = 'SELECT r._rid AS id, r._ts AS ts FROM root r' ); "

3.  Em seguida, vamos criar uma tabela Hive para a coleção de saída. As propriedades do documento saída será o mês, dia, hora, minuto e o número total de ocorrências.

    > [AZURE.NOTE]**Ainda novamente, nomenclatura DocumentDB.outputCollections não era engane.** Sim, podemos permitir a adição de várias colecções como uma saída: </br>
'*DocumentDB.outputCollections*'='*\<nome da coleção de saída DocumentDB 1\>*,*\<nome da coleção de saída DocumentDB 2\>*' </br> Os nomes de coleções de sites são separados sem espaços, com apenas um ponto e vírgula única. </br></br>
Documentos serão round robin distribuído em várias coleções. Um conjunto de documentos será armazenado na coleção de um, em seguida, um segundo conjunto de documentos será armazenado na coleção de seguinte e assim sucessivamente.

        # Create a Hive table for the output data to DocumentDB.
        $queryStringPart2 = "drop table DocumentDB_analytics; " +
                              "create external table DocumentDB_analytics(Month INT, Day INT, Hour INT, Minute INT, Total INT) " +
                              "stored by 'com.microsoft.azure.documentdb.hive.DocumentDBStorageHandler' " +
                              "tblproperties ( " +
                                  "'DocumentDB.endpoint' = '<DocumentDB Endpoint>', " +
                                  "'DocumentDB.key' = '<DocumentDB Primary Key>', " +  
                                  "'DocumentDB.db' = '<DocumentDB Database Name>', " +
                                  "'DocumentDB.outputCollections' = '<DocumentDB Output Collection Name>' ); "

4. Por fim, vamos concordância os documentos por mês, dia, horas e minutos e inserir os resultados novamente o resultado da tabela Hive.

        # GROUP BY minute, COUNT entries for each, INSERT INTO output Hive table.
        $queryStringPart3 = "INSERT INTO table DocumentDB_analytics " +
                              "SELECT month(from_unixtime(ts)) as Month, day(from_unixtime(ts)) as Day, " +
                              "hour(from_unixtime(ts)) as Hour, minute(from_unixtime(ts)) as Minute, " +
                              "COUNT(*) AS Total " +
                              "FROM DocumentDB_timestamps " +
                              "GROUP BY month(from_unixtime(ts)), day(from_unixtime(ts)), " +
                              "hour(from_unixtime(ts)) , minute(from_unixtime(ts)); "

5. Adicione o seguinte fragmento de script para criar uma definição de tarefa ramo de consulta anterior.

        # Create a Hive job definition.
        $queryString = $queryStringPart1 + $queryStringPart2 + $queryStringPart3
        $hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString

    Também pode utilizar o ficheiro parâmetro - para especificar um ficheiro de script HiveQL no HDFS.

6. Adicione o fragmento que se segue para guardar a hora de início e submeter a tarefa de ramo.

        # Save the start time and submit the job to the cluster.
        $startTime = Get-Date
        Select-AzureSubscription $subscriptionName
        $hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition

7. Adicione o seguinte de esperar para a tarefa de Hive concluir.

        # Wait for the Hive job to complete.
        Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600

8. Adicione o seguinte para imprimir a saída padrão e as horas de início e de fim.

        # Print the standard error, the standard output of the Hive job, and the start and end time.
        $endTime = Get-Date
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput
        Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green

9. **Executar** o script novo! **Clique** no botão executar verde.

10. Verificar os resultados. Inicie sessão no [Portal do Azure][azure-portal].
    1. Clique em <strong>Procurar</strong> no painel do lado esquerdo. </br>
    2. Clique em <strong>Tudo</strong> na parte superior direita do painel procurar. </br>
    3. Localize e clique em <strong>Contas DocumentDB</strong>. </br>
    4. Em seguida, localize <strong>DocumentDB conta</strong>, em seguida, <strong>DocumentDB base de dados</strong> e a sua <strong>Coleção de DocumentDB</strong> associada a coleção de saída especificada na sua consulta ramo.</br>
    5. Por fim, clique em <strong>Explorador de documentos</strong> por baixo de <strong>Ferramentas de programador</strong>.</br></p>

    Verá os resultados da consulta ramo.

    ![Ramo de resultados da consulta][image-hive-query-results]

## <a name="RunPig"></a>Passo 4: Executar uma tarefa de porco com DocumentDB e HDInsight

> [AZURE.IMPORTANT] Todas as variáveis indicadas por < > tem de ser preenchidas utilizando as definições de configuração.

1. Defina as seguintes variáveis no painel de Script do PowerShell.

        # Provide Azure subscription name.
        $subscriptionName = "Azure Subscription Name"

        # Provide HDInsight cluster name where you want to run the Pig job.
        $clusterName = "Azure HDInsight Cluster Name"

2. <p>Vamos começar a construir a sua cadeia de consulta. Vamos irá escrever uma consulta de porco leva-o até selos de tempo de todos os documentos sistema gerado (_ts) ids exclusivos (_rid) a partir de uma coleção de DocumentDB calcula todos os documentos pelos minutos, e, em seguida, armazena os resultados de volta para uma nova coleção de DocumentDB.</p>
    <p>Em primeiro lugar, carregar documentos a partir do DocumentDB no HDInsight. Adicione o seguinte fragmento de código para o PowerShell Script painel <strong>depois do</strong> fragmento de código a partir de #1. Certifique-se adicionar uma consulta de DocumentDB para o parâmetro de consulta DocumentDB opcional para cortar o nossos documentos para _ts basta e _rid.</p>

    > [AZURE.NOTE]Sim, podemos permitir a adição de várias colecções como uma entrada: </br>
'*\<Nome da entrada de DocumentDB coleção 1\>*,*\<nome da entrada de DocumentDB coleção 2\>*'</br> Os nomes de coleções de sites são separados sem espaços, com apenas um ponto e vírgula única. </b>

    Documentos serão round robin distribuído em várias coleções. Um conjunto de documentos será armazenado na coleção de um, em seguida, um segundo conjunto de documentos será armazenado na coleção de seguinte e assim sucessivamente.

        # Load data from DocumentDB. Pass DocumentDB query to filter transferred data to _rid and _ts.
        $queryStringPart1 = "DocumentDB_timestamps = LOAD '<DocumentDB Endpoint>' USING com.microsoft.azure.documentdb.pig.DocumentDBLoader( " +
                                                        "'<DocumentDB Primary Key>', " +
                                                        "'<DocumentDB Database Name>', " +
                                                        "'<DocumentDB Input Collection Name>', " +
                                                        "'SELECT r._rid AS id, r._ts AS ts FROM root r' ); "

3.  Em seguida, vamos concordância os documentos por mês, dia, hora, minuto e o número total de ocorrências.

        # GROUP BY minute and COUNT entries for each.
        $queryStringPart2 = "timestamp_record = FOREACH DocumentDB_timestamps GENERATE `$0#'id' as id:int, ToDate((long)(`$0#'ts') * 1000) as timestamp:datetime; " +
                            "by_minute = GROUP timestamp_record BY (GetYear(timestamp), GetMonth(timestamp), GetDay(timestamp), GetHour(timestamp), GetMinute(timestamp)); " +
                            "by_minute_count = FOREACH by_minute GENERATE FLATTEN(group) as (Year:int, Month:int, Day:int, Hour:int, Minute:int), COUNT(timestamp_record) as Total:int; "

4. Por fim, vamos armazenar os resultados no nossa nova coleção de saída.

    > [AZURE.NOTE]Sim, podemos permitir a adição de várias colecções como uma saída: </br>
'\<Nome da coleção de saída DocumentDB 1\>,\<nome da coleção de saída DocumentDB 2\>'</br> Os nomes de coleções de sites são separados sem espaços, com apenas um ponto e vírgula única.</br>
Documentos serão round robin distribuída ao longo de várias colecções. Um conjunto de documentos será armazenado na coleção de um, em seguida, um segundo conjunto de documentos será armazenado na coleção de seguinte e assim sucessivamente.

        # Store output data to DocumentDB.
        $queryStringPart3 = "STORE by_minute_count INTO '<DocumentDB Endpoint>' " +
                            "USING com.microsoft.azure.documentdb.pig.DocumentDBStorage( " +
                                "'<DocumentDB Primary Key>', " +
                                "'<DocumentDB Database Name>', " +
                                "'<DocumentDB Output Collection Name>'); "

5. Adicione o seguinte fragmento de script para criar uma definição de tarefa porco da consulta anterior.

        # Create a Pig job definition.
        $queryString = $queryStringPart1 + $queryStringPart2 + $queryStringPart3
        $pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $queryString -StatusFolder $statusFolder

    Também pode utilizar o ficheiro parâmetro - para especificar um ficheiro de script porco no HDFS.

6. Adicione o fragmento que se segue para guardar a hora de início e submeter a tarefa de porco.

        # Save the start time and submit the job to the cluster.
        $startTime = Get-Date
        Select-AzureSubscription $subscriptionName
        $pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition

7. Adicione o seguinte de esperar para a tarefa de porco concluir.

        # Wait for the Pig job to complete.
        Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

8. Adicione o seguinte para imprimir a saída padrão e as horas de início e de fim.

        # Print the standard error, the standard output of the Hive job, and the start and end time.
        $endTime = Get-Date
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput
        Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green

9. **Executar** o script novo! **Clique** no botão executar verde.

10. Verificar os resultados. Inicie sessão no [Portal do Azure][azure-portal].
    1. Clique em <strong>Procurar</strong> no painel do lado esquerdo. </br>
    2. Clique em <strong>Tudo</strong> na parte superior direita do painel procurar. </br>
    3. Localize e clique em <strong>Contas DocumentDB</strong>. </br>
    4. Em seguida, localize <strong>DocumentDB conta</strong>, em seguida, <strong>DocumentDB base de dados</strong> e a sua <strong>Coleção de DocumentDB</strong> associada a coleção de saída especificada na sua consulta porco.</br>
    5. Por fim, clique em <strong>Explorador de documentos</strong> por baixo de <strong>Ferramentas de programador</strong>.</br></p>

    Verá os resultados da consulta porco.

    ![Resultados da consulta porco][image-pig-query-results]

## <a name="RunMapReduce"></a>Passo 5: Executar uma tarefa de MapReduce utilizando DocumentDB e HDInsight

1. Defina as seguintes variáveis no painel de Script do PowerShell.

        $subscriptionName = "<SubscriptionName>"   # Azure subscription name
        $clusterName = "<ClusterName>"             # HDInsight cluster name

2. Vamos vai executar uma tarefa de MapReduce conta o número de ocorrências para cada propriedade do documento a partir da sua coleção de DocumentDB. Adicione este script fragmento **depois do** fragmento de acima.

        # Define the MapReduce job.
        $TallyPropertiesJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/TallyProperties-v01.jar" -ClassName "TallyProperties" -Arguments "<DocumentDB Endpoint>","<DocumentDB Primary Key>", "<DocumentDB Database Name>","<DocumentDB Input Collection Name>","<DocumentDB Output Collection Name>","<[Optional] DocumentDB Query>"

    > [AZURE.NOTE] TallyProperties v01.jar vem com a instalação personalizada do conector de Hadoop DocumentDB.

3. Adicione o seguinte comando para submeter a tarefa de MapReduce.

        # Save the start time and submit the job.
        $startTime = Get-Date
        Select-AzureSubscription $subscriptionName
        $TallyPropertiesJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $TallyPropertiesJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

    Para além da definição da tarefa MapReduce, também fornecem o nome do cluster HDInsight em que pretende executar a tarefa de MapReduce e as credenciais. A iniciar-AzureHDInsightJob é uma chamada assincronizada. Para verificar a conclusão da tarefa, utilize o cmdlet *AzureHDInsightJob espera* .

4. Adicione o seguinte comando para verificar erros com a executar a tarefa de MapReduce.

        # Get the job output and print the start and end time.
        $endTime = Get-Date
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $TallyPropertiesJob.JobId -StandardError
        Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green

5. **Executar** o script novo! **Clique** no botão executar verde.

6. Verificar os resultados. Inicie sessão no [Portal do Azure][azure-portal].
    1. Clique em <strong>Procurar</strong> no painel do lado esquerdo.
    2. Clique em <strong>Tudo</strong> na parte superior direita do painel procurar.
    3. Localize e clique em <strong>Contas DocumentDB</strong>.
    4. Em seguida, localize a sua <strong>DocumentDB conta</strong>, em seguida, <strong>DocumentDB base de dados</strong> e a sua <strong>Coleção de DocumentDB</strong> associada a coleção de saída especificada no seu trabalho MapReduce.
    5. Por fim, clique em <strong>Explorador de documentos</strong> por baixo de <strong>Ferramentas de programador</strong>.

    Verá os resultados da sua tarefa MapReduce.

    ![Resultados da consulta MapReduce][image-mapreduce-query-results]

## <a name="NextSteps"></a>Próximos passos

Parabéns! Acabou de executar as tarefas de ramo, porco e MapReduce primeiros utilizando Azure DocumentDB e HDInsight.

Temos abrir origem nosso conexão Hadoop. Se está interessado, podem contribuir no [GitHub][documentdb-github].

Para saber mais, consulte os artigos seguintes:

- [Desenvolver uma aplicação de Java com Documentdb][documentdb-java-application]
- [Desenvolver Java MapReduce programas para Hadoop no HDInsight][hdinsight-develop-deploy-java-mapreduce]
- [Começar a utilizar Hadoop com ramo no HDInsight para analisar auscultador móveis de utilização][hdinsight-get-started]
- [Utilizar MapReduce com HDInsight][hdinsight-use-mapreduce]
- [Utilizar ramo com HDInsight][hdinsight-use-hive]
- [Utilizar porco com HDInsight][hdinsight-use-pig]
- [Personalizar clusters HDInsight utilizando a ação de Script][hdinsight-hadoop-customize-cluster]

[apache-hadoop]: http://hadoop.apache.org/
[apache-hadoop-doc]: http://hadoop.apache.org/docs/current/
[apache-hive]: http://hive.apache.org/
[apache-pig]: http://pig.apache.org/
[getting-started]: documentdb-get-started.md

[azure-portal]: https://portal.azure.com/
[azure-powershell-diagram]: ./media/documentdb-run-hadoop-with-hdinsight/azurepowershell-diagram-med.png

[documentdb-hdinsight-samples]: http://portalcontent.blob.core.windows.net/samples/documentdb-hdinsight-samples.zip
[documentdb-github]: https://github.com/Azure/azure-documentdb-hadoop
[documentdb-java-application]: documentdb-java-application.md
[documentdb-manage-collections]: documentdb-manage.md#Collections
[documentdb-manage-document-storage]: documentdb-manage.md#IndexOverhead
[documentdb-manage-throughput]: documentdb-manage.md#ProvThroughput
[documentdb-import-data]: documentdb-import-data.md

[hdinsight-custom-provision]: ../hdinsight/hdinsight-provision-clusters.md#powershell
[hdinsight-develop-deploy-java-mapreduce]: ../hdinsight/hdinsight-develop-deploy-java-mapreduce-linux.md
[hdinsight-hadoop-customize-cluster]: ../hdinsight/hdinsight-hadoop-customize-cluster.md
[hdinsight-get-started]: ../hdinsight/hdinsight-hadoop-tutorial-get-started-windows.md
[hdinsight-storage]: ../hdinsight/hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]: ../hdinsight/hdinsight-use-hive.md
[hdinsight-use-mapreduce]: ../hdinsight/hdinsight-use-mapreduce.md
[hdinsight-use-pig]: ../hdinsight/hdinsight-use-pig.md

[image-customprovision-page1]: ./media/documentdb-run-hadoop-with-hdinsight/customprovision-page1.png
[image-hive-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/hivequeryresults.PNG
[image-mapreduce-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/mapreducequeryresults.PNG
[image-pig-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/pigqueryresults.PNG

[powershell-install-configure]: ../powershell-install-configure.md
