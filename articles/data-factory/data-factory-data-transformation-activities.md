<properties 
    pageTitle="Transformação de dados: Dados processo e transformar | Microsoft Azure" 
    description="Saiba como transformar dados ou dados de processo no Azure dados fábrica utilizando Hadoop, formação de máquina ou Azure dados Lake Analytics." 
    keywords="transformação de dados, processo dados transformar dados, atividade de transformação"
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/23/2016" 
    ms.author="shlo"/>

# <a name="transform-data-in-azure-data-factory"></a>Transformar dados numa fábrica de dados do Azure
> [AZURE.SELECTOR]
[Ramo](data-factory-hive-activity.md)  
[Porco](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Transmissão de Hadoop](data-factory-hadoop-streaming-activity.md)
[Formação de máquina](data-factory-azure-ml-batch-execution-activity.md) 
[Procedimento armazenado](data-factory-stored-proc-activity.md)
[Dados Lake Analytics U-SQL](data-factory-usql-activity.md)
[.NET personalizado](data-factory-use-custom-activities.md)
   

## <a name="overview"></a>Descrição geral 
Este artigo explica as atividades de transformação de dados no Azure fábrica de dados que pode utilizar para transformar e processos os seus dados não processados em previsões e informações. Executa uma atividade de transformação num ambiente de computação como Azure HDInsight cluster ou um lote de Azure. Fornece ligações para artigos com informações detalhadas sobre cada actividade de transformação.
 
Dados fábrica suporta as seguintes dados transformação atividades que podem ser adicionadas a [tubagens](data-factory-create-pipelines.md) quer individualmente ou ligados em com outra atividade.

> [AZURE.NOTE] Para obter instruções sobre com instruções passo a passo, consulte o artigo [criar uma tubagem com ramo de transformação](data-factory-build-your-first-pipeline.md) artigo.  

## <a name="hdinsight-hive-activity"></a>Ramo de HDInsight atividade
A atividade de HDInsight ramo no pipeline fábrica dados executa ramo de consultas no seu próprio ou cluster de baseados no Windows/Linux HDInsight a pedido. Consulte o artigo [Hive atividade](data-factory-hive-activity.md) artigo para obter detalhes sobre este atividade. 

## <a name="hdinsight-pig-activity"></a>Atividade de porco HDInsight
A atividade de HDInsight porco no pipeline fábrica dados executa consultas de porco no seu próprio ou cluster de baseados no Windows/Linux HDInsight a pedido. Consulte o artigo [Porco atividade](data-factory-pig-activity.md) artigo para obter detalhes sobre este atividade. 

## <a name="hdinsight-mapreduce-activity"></a>Atividade de HDInsight MapReduce
A atividade de HDInsight MapReduce no pipeline fábrica dados executa MapReduce programas do seu próprio ou cluster de baseados no Windows/Linux HDInsight a pedido. Consulte o artigo [MapReduce atividade](data-factory-map-reduce.md) artigo para obter detalhes sobre este atividade.

Pode utilizar MapReduce atividade para executar motores programas no seu cluster de motores de HDInsight. Consulte o artigo [invocar motores programas a partir do Azure dados fábrica](data-factory-spark.md) para obter detalhes.

## <a name="hdinsight-streaming-activity"></a>Atividade de transmissão de HDInsight
A atividade de transmissão de HDInsight no pipeline fábrica dados executa Hadoop transmissão programas no seu próprio ou cluster de baseados no Windows/Linux HDInsight a pedido. Ver [atividades HDInsight transmissão](data-factory-hadoop-streaming-activity.md) para obter detalhes sobre este atividade.

## <a name="machine-learning-activities"></a>Atividades de aprendizagem automática
Azure fábrica de dados permite-lhe criar facilmente tubagens utilizam um serviço web de aprendizagem do Azure máquina publicado para análise aspeto do Office. Utilizar a [Atividade de execução do lote](data-factory-azure-ml-batch-execution-activity.md#invoking-a-web-service-using-batch-execution-activity) no pipeline fábrica de dados do Azure, pode invocar um serviço web de aprendizagem Machine para efetuar previsões nos dados no lote.

Ao longo do tempo, os modelos de aspeto do Office em aprender a máquina pontuação experiências tem de ser retrained utilizando novos conjuntos de dados de entrada. Depois de terminar com a Reciclagem, que pretende atualizar o serviço web pontuação com o modelo de formação de máquina retrained. Pode utilizar a [Atividade de recursos de atualizar](data-factory-azure-ml-batch-execution-activity.md#updating-models-using-update-resource-activity) para atualizar o serviço web com o modelo recentemente qualificado.  

Consulte o artigo [utilizar máquina aprendizagem atividades](data-factory-azure-ml-batch-execution-activity.md) para obter detalhes sobre estas actividades de máquina aprendizagem. 

## <a name="stored-procedure-activity"></a>Atividade de procedimento armazenado
Pode utilizar a atividade de procedimento de armazenado do SQL Server no pipeline fábrica de dados para invocar um procedimento armazenado de uma das seguintes arquivos de dados: Azure SQL base de dados, armazém de dados do SQL Azure, base de dados do SQL Server na sua empresa ou um VM Azure. Consulte o artigo [Atividade de procedimento armazenado](data-factory-stored-proc-activity.md) artigo para obter detalhes.  

## <a name="data-lake-analytics-u-sql-activity"></a>Atividade de Lake U-SQL de análise de dados
Dados Lake análise U SQL atividade é executado um script de U SQL num cluster Azure dados Lake Analytics. Consulte o artigo [Dados análise U SQL atividade](data-factory-usql-activity.md) artigo para obter detalhes. 

## <a name="net-custom-activity"></a>Atividade personalizada .NET
Se precisar de transformar dados de uma forma que não seja suportado pela fábrica de dados, pode criar uma atividade personalizada com o seu próprio lógica de processamento de dados e utilizar a atividade no pipeline de. Pode configurar a atividade de .NET personalizada para executar a utilizar um serviço de Azure lote ou um cluster de Azure HDInsight. Ver [atividades personalizadas de utilização](data-factory-use-custom-activities.md) artigo para obter detalhes. 

Pode criar uma atividade personalizada para executar scripts de R em seu cluster HDInsight com R instalado. Consulte o artigo [Executar Script R utilizando Azure fábrica de dados](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample). 

## <a name="compute-environments"></a>Calcular ambientes
Criar um serviço ligado para o ambiente de cluster e, em seguida, utilizar o serviço de ligadas quando definir uma atividade de transformação. Existem dois tipos de ambientes de cluster suportadas pela fábrica de dados. 

1. **A pedido**: neste caso, o ambiente informático totalmente é gerido pela fábrica de dados. É automaticamente criada pelo serviço fábrica de dados antes de uma tarefa é apresentada aos dados do processo e removida quando a tarefa é concluída. Pode configurar e controlar as definições de granulares do ambiente cluster a pedido para execução de tarefas, gestão de cluster e iniciar ações. 
2. **Trazer o seu**: neste caso, pode registar o seu próprio ambiente informático (por exemplo HDInsight cluster) como um serviço ligado numa fábrica de dados. O ambiente informático é gerido por si e o serviço de dados fábrica utiliza-lo para executar as atividades. 

Consulte o artigo [Calcular serviços ligados](data-factory-compute-linked-services.md) artigo para saber mais sobre calcular serviços suportados por dados fábrica do mesmo. 


## <a name="summary"></a>Resumo
Azure dados fábrica suporta as seguintes atividades de transformação de dados e os ambientes de cluster para as atividades. As atividades de transformação podem ser adicionadas a tubagens quer individualmente ou ligados em com outra atividade.

Atividade de transformação de dados |  Calcular ambiente 
:----------------------- | :--------------------
[Ramo](data-factory-hive-activity.md) | HDInsight [Hadoop] 
[Porco](data-factory-pig-activity.md) | HDInsight [Hadoop]  
[MapReduce](data-factory-map-reduce.md) | HDInsight [Hadoop]  
[Hadoop transmissão](data-factory-hadoop-streaming-activity.md) | HDInsight [Hadoop]
[Máquina de atividades de formação: lote execução e de recursos de atualização](data-factory-azure-ml-batch-execution-activity.md) | Azure VM 
[Procedimento armazenado](data-factory-stored-proc-activity.md) | Azure SQL, armazém de dados do Azure SQL ou do SQL Server |
[Lake análise de dados U-SQL](data-factory-usql-activity.md) | A análise de dados Azure Lake 
[DotNet](data-factory-use-custom-activities.md) | HDInsight [Hadoop] ou lote Azure
   

