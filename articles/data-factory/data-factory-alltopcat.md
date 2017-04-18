<properties
    pageTitle="Todos os tópicos do serviço de dados fábrica | Microsoft Azure"
    description="Tabela de todos os tópicos para o serviço Azure com o nome fábrica de dados existentes no http://azure.microsoft.com/documentation/articles/, título e descrição."
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="MightyPen"/>

<tags
    ms.service="data-factory"
    ms.workload="data-factory"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="spelluru"/>


# <a name="all-topics-for-azure-data-factory-service"></a>Todos os tópicos do serviço de fábrica de dados do Azure

Este tópico indica cada tópico que se aplica diretamente para o serviço de **Dados fábrica** do Azure. Pode procurar esta página Web para palavras-chave utilizando **Ctrl + F**, para localizar os tópicos de interesse atual.




## <a name="new"></a>Novo

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 1 | [Mover os dados a partir do Redshift de Amazon utilizando a fábrica de dados do Azure](data-factory-amazon-redshift-connector.md) | Saiba mais sobre como mover dados de Redshift Amazon utilizando Azure fábrica de dados. |
| 2 | [Mover os dados a partir do Amazon simples serviço de armazenamento utilizando a fábrica de dados do Azure](data-factory-amazon-simple-storage-service-connector.md) | Saiba mais sobre como mover dados do serviço de armazenamento simples (S3) do Amazon utilizando Azure fábrica de dados. |
| 3 | [Dados Azure Factory - copiar o Assistente](data-factory-azure-copy-wizard.md) | Saiba mais sobre como utilizar o Assistente para copiar dados fábrica Azure para copiar dados de origens de dados suportadas para sumidouros. |
| 4 | [Tutorial: Criar a primeira fábrica de dados Azure utilizando dados fábrica REST API](data-factory-build-your-first-pipeline-using-rest-api.md) | Neste tutorial, crie um pipeline de Azure fábrica de dados de exemplo com dados fábrica REST API. |
| 5 | [Tutorial: Criar uma tubagem com cópia atividade utilizando .NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md) | Neste tutorial, crie um pipeline de fábrica do Azure dados com uma atividade de cópia ao utilizar a .NET API. |
| 6 | [Tutorial: Criar uma tubagem com actividade de cópia utilizando REST API](data-factory-copy-activity-tutorial-using-rest-api.md) | Neste tutorial, crie uma pipeline de fábrica do Azure dados com uma atividade de cópia ao utilizar a REST API. |
| 7 | [Assistente para copiar dados fábrica](data-factory-copy-wizard.md) | Saiba mais sobre como utilizar o Assistente para copiar dados fábrica para copiar dados de origens de dados suportadas para sumidouros. |
| 8 | [O Data Management Gateway](data-factory-data-management-gateway.md) | Configure um gateway de dados para mover dados entre no local e na nuvem. Utilize o Data Management Gateway no Azure dados fábrica para mover os seus dados. |
| 9 | [Mover os dados a partir de uma base de dados no local Cassandra utilizando a fábrica de dados do Azure](data-factory-onprem-cassandra-connector.md) | Saiba mais sobre como mover dados de uma base de dados no local Cassandra utilizando Azure fábrica de dados. |
| 10 | [Mover os dados a partir do MongoDB utilizando a fábrica de dados do Azure](data-factory-on-premises-mongodb-connector.md) | Saiba mais sobre como mover dados de base de dados de MongoDB utilizando Azure fábrica de dados. |
| 11 | [Mover dados do Salesforce utilizando a fábrica de dados do Azure](data-factory-salesforce-connector.md) | Saiba mais sobre como mover dados do Salesforce utilizando a fábrica de dados do Azure. |


## <a name="updated-articles-data-factory"></a>Artigos atualizados, fábrica de dados

Esta secção apresenta artigos que foram atualizados recentemente, onde a atualização foi grande ou significativo. Para cada artigo actualizado, é apresentado um fragmento aproximado do texto markdown adicionados. Os artigos foram atualizados no intervalo de datas do **2016-08-22** **2016-10 -**05.

| &nbsp; | Artigo | Texto actualizado, fragmento | Actualizada quando |
| --: | :-- | :-- | :-- |
| 12 | [Azure dados Factory - registo de alterações da .NET API](data-factory-api-change-log.md) | Este artigo fornece informações sobre as alterações ao SDK de fábrica do Azure dados numa versão específica. Pode localizar o pacote de NuGet mais recente para a Azure dados fábrica aqui (https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories) **versão 4.11.0** funcionalidade adições: / os seguintes tipos de serviço ligadas foram adicionados: - OnPremisesMongoDbLinkedService (https://msdn.microsoft.com/library/mt765129.aspx) - AmazonRedshiftLinkedService (https://msdn.microsoft.com/library/mt765121.aspx) - AwsAccessKeyLinkedService (https://msdn.microsoft.com/library/mt765144.aspx) / os seguintes tipos de conjunto de dados foram adicionados: AmazonS3Dataset - MongoDbCollectionDataset (https://msdn.microsoft.com/library/mt765145.aspx) - (https://msdn.microsoft.com/library/mt765112.aspx) / foram adicionados os seguintes tipos de origem da cópia :-MongoDbSource (https://msdn.microsoft.com/en-US/library/mt765123.aspx) **versão 4.10.0** / as seguintes propriedades opcionais foram adicionadas ao TextFormat:-esquis | 2016-09-22 |
| 13 | [Mover os dados de Blobs do Azure utilizando a fábrica de dados do Azure e para](data-factory-azure-blob-connector.md) |  / copyBehavior / define o comportamento de cópia quando a origem for BlobSource ou sistema de ficheiros.  / **PreserveHierarchy:** preserva a hierarquia de ficheiro na pasta de destino. O caminho do ficheiro de origem para a pasta de origem relativo é idêntico ao relativo caminho do ficheiro de destino para a pasta de destino... BR /... BR /. **FlattenHierarchy:** todos os ficheiros da pasta de origem estão a ser o primeiro nível de pasta de destino. Os ficheiros de destino tem automática gerada nome. .br /... BR /. **MergeFiles: (predefinição)** intercala todos os ficheiros da pasta de origem para um ficheiro. Se o nome de ficheiro/Blob for especificado, o nome do ficheiro Unidas seria o nome especificado; caso contrário, seria nome de ficheiro gerados automaticamente.  / Não / **BlobSource** também suporta estas duas propriedades para compatibilidade com versões anteriores. / **treatEmptyAsNull**: Especifica se se tratar de uma cadeia vazia ou nula como valor nulo. / **skipHeaderLineCount** - Especifica quantas linhas precisam de ser ignoradas. É aplicável apenas ao conjunto de dados de entrada está a utilizar TextFormat. Da mesma forma, **BlobSink** suporta ésimo | 2016-09-28 |
| 14 | [Criar o aspeto do Office tubagens utilizando actividades de aprendizagem de máquina do Azure](data-factory-azure-ml-batch-execution-activity.md) | **Múltiplas entradas requer o serviço Web** Se o serviço web demora múltiplas entradas, utilize a propriedade **webServiceInputs** em vez de utilizar **webServiceInput**. Conjuntos de dados que são referenciados pela **webServiceInputs** também devem ser incluídos na atividade **entradas do tipo**. Na sua experiência do Azure ML, entrada de serviço web e portas de saída e parâmetros globais têm nomes predefinidos ("input1", "input2") que pode personalizar. Os nomes que utiliza para webServiceInputs, webServiceOutputs e definições de globalParameters tem de corresponder exatamente os nomes nas experiências. Pode ver a carga de útil de pedido de exemplo na página de ajuda do lote execução para o ponto final Azure ML verificar o mapeamento do esperado.    {"nome": "PredictivePipeline", "Propriedades": {"Descrição": "utilizar o modelo de AzureML", "atividades": {"nome": "MLActivity", "tipo": "AzureMLBatchExecution", "Descrição": "análise de previsão no lote de entrada", "as entradas": {"nome": "inputDataset1"}, {"nome": "inputDatase | 2016-09-13 |
| 15 | [Copiar o guia de sintonização e desempenho de atividade](data-factory-copy-activity-performance.md) | 1. **a caixa de verificação estabelecer um plano base**. Durante a fase de desenvolvimento, teste o pipeline utilizando atividade de cópia contra uma amostra de representante de dados. Pode utilizar a fábrica de dados cortar modelo (dados-fábrica-agendamento-e-execution.md time-series-datasets-and-data-slices) para limitar a quantidade de dados que com quem trabalha.  Recolha tempo de execução e características de desempenho ao utilizar a **monitorização e gestão de aplicação**. Selecione **Monitor e gerir** na sua página de base de dados fábrica do mesmo. Na vista de árvore, selecione o **conjunto de dados de saída**. Na lista de **Atividade Windows** , selecione a atividade de cópia executar. **Atividade Windows** listas a duração de atividade de cópia e o tamanho dos dados que são copiados. O débito está listado na **Atividade janela Explorador**. Para saber mais sobre a aplicação, consulte o Monitor e gerir tubagens Azure fábrica de dados ao utilizar a monitorização e gestão de aplicação (dados-fábrica-monitor-gerir-app.md).  ! Executar detalhes (./media/data-factory-copy-activity-performance/mmapp-activity-run-details.pn de atividade | 2016-09-27 |
| 16 | [Tutorial: Criar uma tubagem com cópia atividade utilizando o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) |   Tenha em atenção os seguintes pontos:-conjunto de dados **tipo** está definido para **AzureBlob**.     - **linkedServiceName** está definido para **AzureStorageLinkedService**. Este serviço ligado que criou no passo 2.     - **caminhopasta** está definido para o contentor **adftutorial** . Também pode especificar o nome de um blob dentro da pasta utilizando a propriedade **nome do ficheiro** . Uma vez que não está a especificar o nome do blob dados a partir de todos os blobs no contentor são considerados como dados de entrada.    -Formatar **tipo** está definido para **TextFormat** - existem dois campos no texto ficheiro ΓÇô **NomePróprio** e **Apelido** ΓÇô separado por um caráter de ponto e vírgula (**columnDelimiter**) - a **disponibilidade** está definido para **cada hora** (**frequência** está definido para **hora** e **intervalo** está definido para **1**). Por conseguinte, dados fábrica procura dados de entrada cada hora na pasta de raiz do contentor de BLOBs (**adftutorial**) que especificou.  Se não especificar um **nome de ficheiro** de um conjunto de dados de **entrada** , todos os ficheiros por blobs a partir da pasta de entrada (**caminhopasta**) são consid | 2016-09-29 |
| 17 | [Criar, monitorizar e gerir fábricas dados Azure utilizando dados fábrica .NET SDK](data-factory-create-data-factories-programmatically.md) | Anote o ID da aplicação e a palavra-passe (secreta de cliente) e utilize-o no tutorial. **Obter Azure subscrição e inquilino IDs** Se não possui a versão mais recente do PowerShell instalado no seu computador, siga as instruções no artigo como instalar e configurar o Azure PowerShell (... / powershell-instalar-configure.md) artigo instalá-lo. 1. Inicie o PowerShell do Azure e execute o seguinte comando 2. Execute o seguinte comando e introduza o nome de utilizador e palavra-passe que utiliza para iniciar sessão portal do Azure.         Início de sessão AzureRmAccount se que tiver apenas uma subscrição do Azure associada esta conta, não terá realizar os dois passos. 3. Execute o seguinte comando para ver todas as subscrições para esta conta.       Get-AzureRmSubscription 4. Execute o seguinte comando para selecionar a subscrição à qual pretende trabalhar com. Substitua o nome da sua subscrição do Azure **NameOfAzureSubscription** .       Get AzureRmSubscription - SubscriptionName NameOfAzureSubscription / AzureRmCo conjunto | 2016-09-14 |
| 18 | [Tubagens e atividades nos dados Azure fábrica](data-factory-create-pipelines.md) |       "Iniciar": "2016-07-12T00:00:00Z", "fim": "2016-07-13T00:00:00Z"}} tenha em atenção os seguintes pontos: / na secção de atividades, existe apenas uma atividade cujo **tipo** está definido para **Copiar**. / Entrada de dados para a atividade está definida para **InputDataset** e saída da atividade de está definida para **OutputDataset**. / Na secção **typeProperties** , **BlobSource** for especificada como o tipo de origem e **SqlSink** for especificada como o tipo de sink. Para concluir obter instruções sobre criar neste curso, consulte o artigo Tutorial: copiar dados de armazenamento de BLOBs para base de dados do SQL (data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). **Pipeline de transformação de exemplo** No pipeline de exemplo seguinte, existe uma atividade do tipo **HDInsightHive** na secção **atividades** . Neste exemplo, a atividade de HDInsight ramo (dados-fábrica-ramo-activity.md) transforma dados a partir de um armazenamento de Blobs do Azure através da execução de um ficheiro de script ramo num cluster Azure HDInsight Hadoop.  {"nome": "TransformPipeline", "p | 2016-09-27 |
| 19 | [Transformar dados numa fábrica de dados do Azure](data-factory-data-transformation-activities.md) | Dados fábrica suporta as seguintes atividades de transformação de dados que podem ser adicionadas a tubagens (dados-fábrica-Criar-pipelines.md) quer individualmente ou ligados em com outra atividade. .  AZURE. Nota para obter instruções sobre com instruções passo a passo, consulte o artigo criar uma tubagem com o artigo do ramo de transformação (data-factory-build-your-first-pipeline.md). **Ramo de HDInsight atividade** A atividade de HDInsight ramo no pipeline fábrica dados executa ramo de consultas no seu próprio ou cluster de baseados no Windows/Linux HDInsight a pedido. Consulte o artigo (dados-fábrica-ramo-activity.md) de atividade Hive para obter detalhes sobre este atividade. **Porco HDInsight atividade** A atividade de HDInsight porco no pipeline fábrica dados executa consultas de porco no seu próprio ou cluster de baseados no Windows/Linux HDInsight a pedido. Consulte o artigo de (dados-fábrica-porco-activity.md) porco atividade para obter detalhes sobre este atividade. **Atividade de HDInsight MapReduce** A atividade de HDInsight MapReduce no pipeline fábrica dados executa MapReduce programas em y | 2016-09-26 |
| 20 | [Agendamento de fábrica do mesmo de dados e execução](data-factory-scheduling-and-execution.md) | CopyActivity2 seria executado apenas se o CopyActivity1 tem de executar com êxito e Dataset2 está disponível. Eis pipeline de exemplo JSON: {"nome": "ChainActivities", "Propriedades": {"Descrição": "Executar atividades sequência", "atividades": {"tipo": "Copiar", "typeProperties": {"origem": {"tipo": "BlobSource"}, "sink": {"tipo": "BlobSink", "copyBehavior": "PreserveHierarchy", "writeBatchSize": 0, "writeBatchTimeout": "00: 00:00"}}, "entradas": {"nome": "Dataset1"}, "exporta": {"nome": "Dataset2"}, "política": {"tempo limite": "01: 00:00"}, "scheduler": {"frequência": "Hora", "intervalo": 1}, "nome": "CopyFromBlob1ToBlob2", "Descrição": "Copiar dados de um blob para outro"}, {"tipo": "Copiar", "typeProperties": {"origem": {"tipo": "BlobSource"}, "sink" : {"tipo": "BlobSink", "writeBatchSize": 0, "writeBatchTimeout": "00: 00:00"}}, "em | 2016-09-28 |





## <a name="tutorials"></a>Tutoriais

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 21 | [Tutorial: Criar o seu pipeline primeiro aos dados de processo utilizando Hadoop cluster](data-factory-build-your-first-pipeline.md) | Neste tutorial de fábrica do Azure dados mostra-lhe como criar e agendar uma fábrica de dados que processa dados através de script de ramo num Hadoop cluster. |
| 22 | [Tutorial: Criar a primeira fábrica de dados Azure utilizando o modelo de Gestor de recursos do Azure](data-factory-build-your-first-pipeline-using-arm.md) | Neste tutorial, crie um pipeline de fábrica do Azure dados de exemplo utilizando um modelo de Gestor de recursos do Azure. |
| 23 | [Tutorial: Criar o seu primeira fábrica de dados Azure através do portal Azure](data-factory-build-your-first-pipeline-using-editor.md) | Neste tutorial, crie um pipeline de fábrica do Azure dados de exemplo utilizando o Editor de fábrica do mesmo de dados no portal do Azure. |
| 24 | [Tutorial: Criar o seu primeira fábrica de dados Azure através do PowerShell do Azure](data-factory-build-your-first-pipeline-using-powershell.md) | Neste tutorial, crie um pipeline de Azure fábrica de dados de exemplo através do Azure PowerShell. |
| 25 | [Tutorial: Criar o primeiro Azure dados fábrica do mesmo utilizando o Microsoft Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) | Neste tutorial, crie uma tubagem Azure fábrica de dados de exemplo utilizando o Visual Studio. |
| 26 | [Tutorial: Criar uma tubagem com atividade cópia através do portal Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) | Neste tutorial, crie uma pipeline de fábrica do Azure dados com uma atividade de cópia utilizando o Editor de fábrica do mesmo de dados no portal do Azure. |
| 27 | [Tutorial: Criar uma tubagem com atividade cópia através do PowerShell do Azure](data-factory-copy-activity-tutorial-using-powershell.md) | Neste tutorial, crie uma pipeline de fábrica do Azure dados com uma atividade de cópia utilizando o Azure PowerShell. |
| 28 | [Tutorial: Criar uma tubagem com cópia atividade utilizando o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) | Neste tutorial, crie uma pipeline de fábrica do Azure dados com uma atividade de cópia utilizando o Visual Studio. |
| 29 | [Copiar dados de armazenamento de BLOBs para base de dados SQL com a fábrica de dados](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) | Este tutorial mostra-lhe como utilizar cópia atividade no pipeline fábrica de dados do Azure para copiar dados de armazenamento de BLOBs para base de dados SQL. |
| 30 | [Tutorial: Criar uma tubagem com cópia atividade utilizando o Assistente de cópia de fábrica do mesmo de dados](data-factory-copy-data-wizard-tutorial.md) | Neste tutorial, criar uma pipeline de fábrica do Azure dados com uma atividade de cópia utilizando o Assistente de cópia suportada pela fábrica de dados |



## <a name="data-movement"></a>Movimentação de dados

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 31 | [Mover os dados de Blobs do Azure utilizando a fábrica de dados do Azure e para](data-factory-azure-blob-connector.md) | Saiba como copiar dados blob no Azure fábrica de dados. Utilizar o nosso exemplo: como copiar dados para e a partir do armazenamento de Blobs do Azure e base de dados do SQL Azure. |
| 32 | [Mover os dados de e para a loja de Lake Azure dados utilizando a fábrica de dados do Azure](data-factory-azure-datalake-connector.md) | Saiba como mover os dados para a loja de Lake Azure dados utilizando a fábrica de dados do Azure |
| 33 | [Mover os dados de e para a DocumentDB utilizando a fábrica de dados do Azure](data-factory-azure-documentdb-connector.md) | Saiba como mover os dados para/da colecção de Azure DocumentDB utilizando a fábrica de dados do Azure |
| 34 | [Mover os dados de e para a base de dados do SQL Azure utilizando a fábrica de dados do Azure](data-factory-azure-sql-connector.md) | Saiba como mover os dados para a base de dados do SQL Azure com o Azure fábrica de dados. |
| 35 | [Mover os dados de e para a armazém de dados SQL Azure utilizando a fábrica de dados do Azure](data-factory-azure-sql-data-warehouse-connector.md) | Saiba como mover os dados para a armazém de dados SQL Azure utilizando a fábrica de dados do Azure |
| 36 | [Mover os dados de tabela do Azure utilizando a fábrica de dados do Azure e para](data-factory-azure-table-connector.md) | Saiba como mover os dados para a armazenamento de tabela do Azure através do Azure fábrica de dados. |
| 37 | [Copiar o guia de sintonização e desempenho de atividade](data-factory-copy-activity-performance.md) | Saiba mais sobre chaves fatores que afetam o desempenho de movimento de dados no Azure dados fábrica quando utiliza a atividade de cópia. |
| 38 | [Mover dados utilizando a atividade de cópia](data-factory-data-movement-activities.md) | Saiba mais sobre movimento de dados no tubagens fábrica de dados: migração de dados entre armazena na nuvem e entre um arquivo no local e numa loja na nuvem. Utilize actividade de cópia. |
| 39 | [Notas de lançamento do Data Management Gateway](data-factory-gateway-release-notes.md) | Notas de lançamento do dados data Management Gateway tory |
| 40 | [Mover os dados a partir do HDFS no local com a fábrica de dados do Azure](data-factory-hdfs-connector.md) | Saiba mais sobre como mover dados de HDFS no local com o Azure fábrica de dados. |
| 41 | [Monitorizar e gerir a fábrica de dados do Azure tubagens utilizando novas monitorização e gestão de aplicação](data-factory-monitor-manage-app.md) | Saiba como utilizar a monitorização e gestão de aplicação para monitorizar e gerir fábricas dados Azure e tubagens. |
| 42 | [Mover dados entre origens no local e na nuvem com o Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) | Configure um gateway de dados para mover dados entre no local e na nuvem. Utilize o Data Management Gateway no Azure dados fábrica para mover os seus dados. |
| 43 | [Mover os dados de origem a partir de OData utilizando a fábrica de dados do Azure](data-factory-odata-connector.md) | Saiba mais sobre como mover dados de origens de OData através do Azure fábrica de dados. |
| 44 | [Deslocar-se de arquivos de dados ODBC a partir de dados utilizando a fábrica de dados do Azure](data-factory-odbc-connector.md) | Saiba mais sobre como mover dados de arquivos de dados ODBC utilizando Azure fábrica de dados. |
| 45 | [Mover dados do DB2 utilizando a fábrica de dados do Azure](data-factory-onprem-db2-connector.md) | Saiba mais sobre como mover os dados da base de dados DB2 utilizando a fábrica de dados do Azure |
| 46 | [Mover dados de um sistema de ficheiros no local e para utilizando a fábrica de dados do Azure](data-factory-onprem-file-system-connector.md) | Saiba como mover dados de um sistema de ficheiros no local e para utilizando a fábrica de dados do Azure. |
| 47 | [Mover dados do MySQL utilizando a fábrica de dados do Azure](data-factory-onprem-mysql-connector.md) | Saiba mais sobre como mover dados de base de dados do MySQL utilizando Azure fábrica de dados. |
| 48 | [Mover os dados para a no local Oracle utilizando a fábrica de dados do Azure](data-factory-onprem-oracle-connector.md) | Saiba como mover dados para a base de dados Oracle que está no local utilizando Azure fábrica de dados. |
| 49 | [Mover dados do PostgreSQL utilizando a fábrica de dados do Azure](data-factory-onprem-postgresql-connector.md) | Saiba mais sobre como mover dados de base de dados do PostgreSQL utilizando Azure fábrica de dados. |
| 50 | [Mover dados do Sybase utilizando a fábrica de dados do Azure](data-factory-onprem-sybase-connector.md) | Saiba mais sobre como mover dados de base de dados do Sybase utilizando Azure fábrica de dados. |
| 51 | [Mover dados do Teradata utilizando a fábrica de dados do Azure](data-factory-onprem-teradata-connector.md) | Saiba mais sobre Teradata Connector para o serviço de dados fábrica que permite-lhe mover os dados a partir da base de dados Teradata |
| 52 | [Mover os dados para e a partir do SQL Server no local ou no IaaS (Azure VM) utilizando a fábrica de dados do Azure](data-factory-sqlserver-connector.md) | Saiba mais sobre como mover dados para a base de dados do SQL Server que está no local ou numa VM Azure utilizando Azure fábrica de dados. |
| 53 | [Mover os dados a partir de uma fonte de tabela Web utilizando a fábrica de dados do Azure](data-factory-web-table-connector.md) | Saiba mais sobre como mover os dados no local a partir de uma tabela numa página Web utilizando a fábrica de dados do Azure. |



## <a name="data-transformation"></a>Transformação de dados

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 54 | [Criar o aspeto do Office tubagens utilizando actividades de aprendizagem de máquina do Azure](data-factory-azure-ml-batch-execution-activity.md) | Descreve como criar criar tubagens aspeto do Office utilizando a fábrica de dados do Azure e aprendizagem de máquina do Azure |
| 55 | [Calcular serviços ligados](data-factory-compute-linked-services.md) | Saiba mais sobre cluster enviornments que pode utilizar no Azure dados fábrica tubagens a transformação/processo de dados. |
| 56 | [Processar conjuntos de dados em grande escala utilizando fábrica de dados e a secção](data-factory-data-processing-using-batch.md) | Descreve como a processar grandes quantidades de dados no pipeline fábrica de dados do Azure utilizando a capacidade de processamento paralelas do Azure lote. |
| 57 | [Transformar dados numa fábrica de dados do Azure](data-factory-data-transformation-activities.md) | Saiba como transformar dados ou dados de processo no Azure dados fábrica utilizando Hadoop, formação de máquina ou Azure dados Lake Analytics. |
| 58 | [Atividade de transmissão de Hadoop](data-factory-hadoop-streaming-activity.md) | Saiba como pode utilizar a atividade de transmissão de Hadoop numa fábrica dados Azure para executar programas Hadoop transmissão num cluster HDInsight no-pedido/seu próprio. |
| 59 | [Ramo de atividade](data-factory-hive-activity.md) | Saiba como pode utilizar a atividade Hive numa fábrica dados Azure para executar consultas de ramo num cluster HDInsight no-pedido/seu próprio. |
| 60 | [Invocar MapReduce programas a partir de dados fábrica](data-factory-map-reduce.md) | Saiba como a processar dados ao executar programas MapReduce num cluster Azure HDInsight a partir de uma fábrica dados Azure. |
| 61 | [Porco atividade](data-factory-pig-activity.md) | Saiba como pode utilizar a atividade de porco numa fábrica dados Azure para executar scripts de porco num cluster HDInsight no-pedido/seu próprio. |
| 62 | [Invocar motores programas a partir de dados fábrica](data-factory-spark.md) | Saiba como invocar programas de motores de uma fábrica de dados Azure utilizando a atividade de MapReduce. |
| 63 | [Do SQL Server armazenados procedimento atividade](data-factory-stored-proc-activity.md) | Saiba como pode utilizar a atividade de procedimento armazenado do SQL Server para invocar um procedimento armazenado numa base de dados do SQL Azure ou armazém de dados do SQL Azure a partir de uma tubagem fábrica de dados. |
| 64 | [Utilizar atividades personalizadas no pipeline fábrica de dados do Azure](data-factory-use-custom-activities.md) | Saiba como criar atividades personalizadas e utilizá-los no pipeline fábrica de dados do Azure. |
| 65 | [Executar o script U SQL no Azure dados Lake análise a partir do Azure fábrica de dados](data-factory-usql-activity.md) | Saiba como a processar dados ao executar scripts U SQL Azure dados Lake Analytics cluster serviço. |



## <a name="samples"></a>Amostras

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 66 | [Dados Azure Factory - amostras](data-factory-samples.md) | Fornece detalhes sobre exemplos que trabalham com o serviço do Azure fábrica de dados. |



## <a name="use-cases"></a>Casos de utilização

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 67 | [Cliente casos práticos](data-factory-customer-case-studies.md) | Saiba mais sobre como alguns dos nossos clientes têm sido com a fábrica de dados do Azure. |
| 68 | [Utilizar caso - criação de perfis de cliente](data-factory-customer-profiling-usecase.md) | Saiba como Azure dados fábrica é utilizada para criar um condicionados por dados fluxo de trabalho (pipeline) para criar um perfil clientes de jogos. |
| 69 | [Utilizar caso - recomendações de produto](data-factory-product-reco-usecase.md) | Saiba mais sobre um caso de utilização implementado através da utilização do Azure dados fábrica juntamente com outros serviços. |



## <a name="monitor-and-manage"></a>Monitorizar e gerir

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 70 | [Monitorizar e gerir a fábrica de dados do Azure tubagens](data-factory-monitor-manage-pipelines.md) | Saiba como utilizar o Portal do Azure e Azure PowerShell para monitorizar e gerir fábricas dados Azure e tubagens que criou. |



## <a name="sdk"></a>SDK

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 71 | [Azure dados Factory - registo de alterações da .NET API](data-factory-api-change-log.md) | Descreve as alterações de última hora, adições de funcionalidade, correções de erros etc... numa versão específica do .NET API para a fábrica de dados do Azure. |
| 72 | [Criar, monitorizar e gerir fábricas dados Azure utilizando dados fábrica .NET SDK](data-factory-create-data-factories-programmatically.md) | Saiba como criar, monitorizar e gerir fábricas dados Azure utilizando dados fábrica SDK através de programação. |
| 73 | [Referência do Azure dados fábrica para programadores](data-factory-sdks.md) | Saiba mais sobre as diferentes maneiras de criar, monitorizar e gerir fábricas dados Azure |



## <a name="miscellaneous"></a>Diversas

| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 74 | [Dados Azure Factory - perguntas mais frequentes](data-factory-faq.md) | Perguntas mais frequentes sobre a fábrica de dados do Azure. |
| 75 | [Dados Azure Factory - funções e variáveis do sistema](data-factory-functions-variables.md) | Fornece uma lista de funções Azure dados fábrica e as variáveis do sistema |
| 76 | [Dados Azure Factory - regras de nomenclatura](data-factory-naming-rules.md) | Descreve as regras de nomenclatura para entidades fábrica de dados. |
| 77 | [Resolução de problemas de fábrica de dados](data-factory-troubleshoot.md) | Saiba como resolver problemas com a utilização de fábrica do Azure dados. |

