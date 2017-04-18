Azure dados fábrica suporta as seguintes transformação atividades que podem ser adicionadas a tubagens quer individualmente ou ligados em com outra atividade.

Atividade de transformação de dados |  Calcular ambiente 
:----------------------- | :--------------------
[Ramo](../articles/data-factory/data-factory-hive-activity.md) | HDInsight [Hadoop] 
[Porco](../articles/data-factory/data-factory-pig-activity.md) | HDInsight [Hadoop]  
[MapReduce](../articles/data-factory/data-factory-map-reduce.md) | HDInsight [Hadoop]  
[Hadoop transmissão](../articles/data-factory/data-factory-hadoop-streaming-activity.md) | HDInsight [Hadoop]
[Máquina de atividades de formação: lote execução e de recursos de atualização](../articles/data-factory/data-factory-azure-ml-batch-execution-activity.md) | Azure VM 
[Procedimento armazenado](../articles/data-factory/data-factory-stored-proc-activity.md) | Azure SQL, armazém de dados do Azure SQL ou do SQL Server |
[Lake análise de dados U-SQL](../articles/data-factory/data-factory-usql-activity.md) | A análise de dados Azure Lake 
[DotNet](../articles/data-factory/data-factory-use-custom-activities.md) | HDInsight [Hadoop] ou lote Azure
   
> [AZURE.NOTE] 
> Pode utilizar MapReduce atividade para executar motores programas no seu cluster de motores de HDInsight. Consulte o artigo [invocar motores programas a partir do Azure dados fábrica](../articles/data-factory/data-factory-spark.md) para obter detalhes.
> Pode criar uma atividade personalizada para executar scripts de R em seu cluster HDInsight com R instalado. Consulte o artigo [Executar Script R utilizando Azure fábrica de dados](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample).