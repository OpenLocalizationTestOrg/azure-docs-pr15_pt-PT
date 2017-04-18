<properties
   pageTitle="Otimizar as consultas Hive para execução mais rápida no HDInsight | Microsoft Azure"
   description="Saiba como otimizar as suas consultas Hive para Hadoop no HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="rashimg"
   manager="mwinkle"
   editor="cgronlun"
   tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/28/2015"
   ms.author="rashimg"/>


# <a name="optimize-hive-queries-for-hadoop-in-hdinsight"></a>Otimizar ramo de consultas para Hadoop no HDInsight

Por predefinição, Hadoop clusters não estão otimizados para obter um desempenho. Este artigo aborda algumas dos mais comuns ramo de desempenho otimização métodos que pode aplicar a nossa consultas.

##<a name="scale-out-worker-nodes"></a>Dimensionar saída nós de trabalho

Aumentar o número de nós de trabalho num cluster pode tirar partido mais mappers e redução a ser executado em paralelo. Existem duas formas pode aumentar escala HDInsight:

- Em altura aprovisionar, pode especificar o número de nós de trabalho utilizando o Portal do Azure, o Azure PowerShell ou a interface de linha de comandos em diferentes plataformas.  Para mais informações, consulte o artigo [clusters de aprovisionar HDInsight](hdinsight-provision-clusters.md). O ecrã seguinte Mostrar o trabalhador a nó configuração no Portal do Azure:

    ![scaleout_1][image-hdi-optimize-hive-scaleout_1]

- Em tempo de execução, pode também dimensionar saída um cluster sem recriar um. Isto é mostrado abaixo.
![scaleout_1][image-hdi-optimize-hive-scaleout_2]

Para obter mais detalhes sobre as máquinas virtuais diferentes suportadas pelo HDInsight, consulte o artigo [HDInsight preços](https://azure.microsoft.com/pricing/details/hdinsight/).

##<a name="enable-tez"></a>Ativar Tez

[Apache Tez](http://hortonworks.com/hadoop/tez/) é um motor de execução alternativo para o motor de MapReduce:

![tez_1][image-hdi-optimize-hive-tez_1]


Tez é mais rápido porque:

- Executar direcionado incluindo Graph (Comprimentos) como uma única tarefa no motor de MapReduce, o que são expressa Comprimentos requer cada conjunto de mappers para ser seguido por um conjunto de redução. Isto faz com que várias tarefas MapReduce ser fiadas para cada consulta ramo. Tez não ter essa constrangimento e pode processar Comprimentos complexo como uma tarefa, por conseguinte, minimizar gerais de arranque de tarefa.
- **Escreve Avoids desnecessários** Devido a ser fiadas para a mesma consulta ramo no motor de MapReduce de várias tarefas, o resultado de cada tarefa está escrito HDFS para os dados intermédios. Uma vez que Tez minimiza o número de tarefas para cada consulta ramo é conseguir evitar escrita desnecessária.
- **Atrasos de arranque Minimizes** Tez melhor é possível minimizar atraso de arranque, reduzindo o número de mappers precisa de iniciar e também melhorar a otimização ao longo.
- **Volta contentores** Sempre que possível Tez é possível reutilizar contentores para se certificar de que a latência devido a iniciar contentores é reduzida.
- **Técnicas de otimização contínuo** Tradicionais otimização foi feita durante a fase de compilação. No entanto, estão disponíveis mais informações sobre as entradas que permita otimização melhor durante o tempo de execução. Tez utiliza técnicas de otimização contínuo que lhe permite optimizar o plano ainda mais para a fase de runtime.

Para obter mais detalhes sobre estes conceitos, clique [aqui](http://hortonworks.com/hadoop/tez/)

Pode efetuar qualquer consulta ramo Tez ativada por definir um prefixo para a consulta com a definição de abaixo:

    set hive.execution.engine=tez;

Para clusters HDInsight baseados no Windows, tem de estar ativada ao tempo aprovisionar Tez. Segue-se um script do Azure PowerShell de exemplo para aprovisionar um cluster de Hadoop com Tez ativado:

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $clusterName = "[HDInsightClusterName]"
    $location = "[AzureDataCenter]" #i.e. West US
    $dataNodes = 32 # number of worker nodes in the cluster

    $defaultStorageAccountName = "[DefaultStorageAccountName]"
    $defaultStorageContainerName = "[DefaultBlobContainerName]"
    $defaultStorageAccountKey = $defaultStorageAccountKey = Get-AzureStorageKey $defaultStorageAccountName.ToLower() | %{ $_.Primary }

    $hdiUserName = "[HTTPUserName]"
    $hdiPassword = "[HTTPUserPassword]"

    $hdiSecurePassword = ConvertTo-SecureString $hdiPassword -AsPlainText -Force
    $hdiCredential = New-Object System.Management.Automation.PSCredential($hdiUserName, $hdiSecurePassword)

    $hiveConfig = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightHiveConfiguration'
    $hiveConfig.Configuration = @{ "hive.execution.engine"="tez" }

    New-AzureHDInsightClusterConfig -ClusterSizeInNodes $dataNodes -HeadNodeVMSize Standard_D14 -DataNodeVMSize Standard_D14 |
    Set-AzureHDInsightDefaultStorage -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" -StorageAccountKey $defaultStorageAccountKey -StorageContainerName $defaultStorageContainerName |
    Add-AzureHDInsightConfigValues -Hive $hiveConfig |
    New-AzureHDInsightCluster -Name $clusterName -Location $location -Credential $hdiCredential

    
> [AZURE.NOTE] Baseado em Linux clusters HDInsight tem Tez ativada por predefinição.
    

## <a name="hive-partitioning"></a>Ramo a partições

Operação e/s é a redução do desempenho principais para executar consultas de ramo. O desempenho pode ser melhorado se a quantidade de dados que tem de ser lidos pode ser reduzida. Por predefinição, ramo consultas pesquisar todas ramo de tabelas. O que é ótimo para consultas como analisa de tabela, no entanto consultas que só precisa de para digitalizar uma pequena quantidade de dados (por exemplo, consultas com a filtragem), esta ação cria uma sobrecarga desnecessária. Ramo de criar a partições permite que as consultas de Hive aceder a apenas a necessária quantidade de dados em tabelas do ramo.

Ramo de criar a partições são implementada reorganizando os dados não processados para novos directórios com cada partição está a ter os suas próprias directório - onde a partição é definida pelo utilizador. O diagrama seguinte ilustra a partições uma tabela Hive pela coluna de *ano*. Um novo directório é criado para cada ano.

![criação de partições][image-hdi-optimize-hive-partitioning_1]

Algumas considerações de divisão:

- **Não partição inferior** - criar a partições em colunas com apenas alguns valores podem causar a partições muito poucos. Por exemplo, a partições no género irá apenas criar duas partições seja criado (masculino e feminino), assim reduzir a latência apenas por um máximo de metade.

- **Não indevidamente partição** - no outro lado, criar uma partição de uma coluna com um valor exclusivo (por exemplo, ID de utilizador) causará múltiplas partições a causar muitas limite a namenode cluster à medida que vai ter que processar grandes quantidades de directórios.

- **Evite dados prejudicar** - escolher a partições chave externos para que todas as partições estão mesmo tamanho. Um exemplo é a partições no *Estado* pode fazer com que o número de registos em California para ser quase 30 x que Vermont devido a diferença de população.

Para criar uma tabela de partição, utilize a cláusula *Divididos por* :

    CREATE TABLE lineitem_part
        (L_ORDERKEY INT, L_PARTKEY INT, L_SUPPKEY INT,L_LINENUMBER INT,
         L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
         L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
         L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE        STRING, L_SHIPINSTRUCT STRING, L_SHIPMODE STRING,
         L_COMMENT STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    STORED AS TEXTFILE;

Quando a tabela com partições estiver criada, pode criar a partições estática ou dinâmica a partições.

- **Criar a partições estático** significa que tiver dados já sharded nos directórios adequados e pode pedir a partições ramo manualmente com base na localização do diretório. Isto é apresentado no fragmento de código abaixo.

        INSERT OVERWRITE TABLE lineitem_part
        PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’)
        SELECT * FROM lineitem 
        WHERE lineitem.L_SHIPDATE = ‘5/23/1996 12:00:00 AM’

        ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’))
        LOCATION ‘wasbs://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'

- **A partições dinâmico** significa que pretende que Hive para criar a partições automaticamente para si. Uma vez que já criámos a partições tabela a partir da tabela transição, tudo precisamos de fazer é inserir dados para a tabela com partições conforme apresentado abaixo:

        SET hive.exec.dynamic.partition = true;
        SET hive.exec.dynamic.partition.mode = nonstrict;
        INSERT INTO TABLE lineitem_part
        PARTITION (L_SHIPDATE)
        SELECT L_ORDERKEY as L_ORDERKEY, L_PARTKEY as L_PARTKEY , 
             L_SUPPKEY as L_SUPPKEY, L_LINENUMBER as L_LINENUMBER,
             L_QUANTITY as L_QUANTITY, L_EXTENDEDPRICE as L_EXTENDEDPRICE,
             L_DISCOUNT as L_DISCOUNT, L_TAX as L_TAX, L_RETURNFLAG as       L_RETURNFLAG, L_LINESTATUS as L_LINESTATUS, L_SHIPDATE as       L_SHIPDATE_PS, L_COMMITDATE as L_COMMITDATE, L_RECEIPTDATE as   L_RECEIPTDATE, L_SHIPINSTRUCT as L_SHIPINSTRUCT, L_SHIPMODE as      L_SHIPMODE, L_COMMENT as L_COMMENT, L_SHIPDATE as L_SHIPDATE FROM lineitem;

Para obter mais detalhes, consulte o artigo [Divididos tabelas](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables).

##<a name="use-the-orcfile-format"></a>Utilize o formato de ORCFile

Ramo suporta formatos de ficheiro diferente. Por exemplo:

- **Texto**: Este é o formato de ficheiro predefinido e funciona com a maioria dos cenários
- **Avro**: funciona bem com cenários de interoperabilidade
- **ORC/Parquet**: mais adequado para obter um desempenho

Formato ORC (otimizado linha em colunas) é uma forma para armazenar dados ramo altamente eficiente. Em comparação com outros formatos, ORC tem as seguintes vantagens:

- suporte para tipos de complexos, incluindo tipos de complexos estruturados e semiestruturados e data/hora
- seta para cima para 70% de compressão
- índices de todas as 10.000 linhas que permitem a ignorar linhas
- uma diminuição significativa execução de tempo de execução

Para ativar o formato ORC, crie primeiro uma tabela com a cláusula *armazenados como ORC*:

    CREATE TABLE lineitem_orc_part
        (L_ORDERKEY INT, L_PARTKEY INT,L_SUPPKEY INT, L_LINENUMBER INT,
         L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
         L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
         L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
         L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT     STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    STORED AS ORC;

Em seguida, inserir dados para a tabela ORC a partir da tabela transição. Por exemplo:

    INSERT INTO TABLE lineitem_orc
    SELECT L_ORDERKEY as L_ORDERKEY, 
           L_PARTKEY as L_PARTKEY , 
           L_SUPPKEY as L_SUPPKEY,
           L_LINENUMBER as L_LINENUMBER,
           L_QUANTITY as L_QUANTITY, 
           L_EXTENDEDPRICE as L_EXTENDEDPRICE,
           L_DISCOUNT as L_DISCOUNT,
           L_TAX as L_TAX,
           L_RETURNFLAG as L_RETURNFLAG,
           L_LINESTATUS as L_LINESTATUS,
           L_SHIPDATE as L_SHIPDATE,
           L_COMMITDATE as L_COMMITDATE,
           L_RECEIPTDATE as L_RECEIPTDATE, 
           L_SHIPINSTRUCT as L_SHIPINSTRUCT,
           L_SHIPMODE as L_SHIPMODE,
           L_COMMENT as L_COMMENT
    FROM lineitem;

Pode ler mais sobre o formato ORC [aqui](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC).

##<a name="vectorization"></a>Vectorization

Vectorization permite Hive processar um lote de 1024 linhas em conjunto em vez de uma linha a processar cada vez. Isto significa que as operações simples terminadas mais rápido porque menos código interno precisa para executar.

Para ativar vectorization prefixo a sua consulta ramo com as seguintes definições:

    set hive.vectorized.execution.enabled = true;

Para mais informações, consulte o artigo [Vectorized a execução da consulta](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution).


##<a name="other-optimization-methods"></a>Outros métodos de otimização

Existem mais métodos de otimização pode ter em conta, por exemplo:

- **Hive bucketing:** uma técnica que permite-lhe cluster ou segmentar grandes conjuntos de dados para otimizar o desempenho da consulta.
- **Participar otimização:** Otimização do ramo execução da consulta planeamento para melhorar a eficiência de associações e reduzir a necessidade de sugestões de utilizador. Para mais informações, consulte o artigo [participar otimização](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization).
- **aumentar a redução**

##<a id="nextsteps"></a>Próximos passos
Neste artigo, aprendeu vários métodos de Otimização da consulta ramo comuns. Para saber mais, consulte os artigos seguintes:

- [Utilizar Apache ramo no HDInsight](hdinsight-use-hive.md)
- [Analisar os dados de atrasos em voos utilizando ramo no HDInsight](hdinsight-analyze-flight-delay-data.md)
- [Analisar dados de Twitter utilizando ramo no HDInsight](hdinsight-analyze-twitter-data.md)
- [Analisar dados de sensor utilizando a consola de consulta Hive no Hadoop no HDInsight](hdinsight-hive-analyze-sensor-data.md)
- [Utilizar ramo com HDInsight para analisar os registos de Web sites](hdinsight-hive-analyze-website-log.md)


[image-hdi-optimize-hive-scaleout_1]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_1.png
[image-hdi-optimize-hive-scaleout_2]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_2.png
[image-hdi-optimize-hive-tez_1]: ./media/hdinsight-hadoop-optimize-hive-query/tez_1.png
[image-hdi-optimize-hive-partitioning_1]: ./media/hdinsight-hadoop-optimize-hive-query/partitioning_1.png
