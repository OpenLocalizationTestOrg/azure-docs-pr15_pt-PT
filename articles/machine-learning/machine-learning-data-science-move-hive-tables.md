<properties
    pageTitle="Criar e carregar os dados para ramo de tabelas a partir do armazenamento de BLOBs | Microsoft Azure"
    description="Criar tabelas de ramo e carregar os dados no blob para ramo de registo de tabelas"
    services="machine-learning,storage"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="bradsev" />


#<a name="create-and-load-data-into-hive-tables-from-azure-blob-storage"></a>Criar e carregar os dados para ramo de tabelas a partir do armazenamento de Blobs do Azure

Este tópico apresenta as consultas de ramo genéricas que criar tabelas de ramo e carregar os dados a partir do armazenamento de Blobs do Azure. Também são fornecida algumas orientações sobre a partições ramo de tabelas e sobre como utilizar otimizado linha colunar (ORC) formatação para melhorar o desempenho da consulta.

Este **menu** contém ligações para tópicos que descrevem como ingerir esta última dados em ambientes de destino, onde os dados podem ser armazenados e processados durante o processo de ciência do equipa dados (TDSP).

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]


## <a name="prerequisites"></a>Pré-requisitos
Este artigo assume que tem:

* Criar uma conta de armazenamento Azure. Se precisar de instruções, consulte o artigo [sobre Azure contas de armazenamento](../storage/storage-create-storage-account.md). 
* Aprovisionar um cluster de Hadoop personalizado com o serviço do HDInsight.  Se precisar de instruções, consulte o artigo [Personalizar o Azure HDInsight Hadoop clusters de análise avançadas](machine-learning-data-science-customize-hadoop-cluster.md).
* Capacidade de acesso remoto para cluster, tem sessão iniciada e abertos consola da linha de comandos do Hadoop. Se precisar de instruções, consulte o artigo [aceder a cabeça de Hadoop Cluster de nó](machine-learning-data-science-customize-hadoop-cluster.md#headnode).

## <a name="upload-data-to-azure-blob-storage"></a>Carregar dados ao armazenamento de Blobs do Azure
Se tiver criado uma máquina virtual Azure ao seguir as instruções fornecidas na [Configurar uma máquina de virtual Azure para análise avançadas](machine-learning-data-science-setup-virtual-machine.md), este ficheiro de script deve tiverem sido transferido para o *c:\\utilizadores\\\<nome de utilizador\>\\documentos\\dados ciência Scripts* directório na máquina virtual. Estas consultas ramo só exigem que que ligue o seu próprio esquema de dados e a configuração de armazenamento de Blobs do Azure nos campos do adequado para estar pronto para apresentação.

Vamos partem do princípio de que os dados de tabelas de ramo são um formato tabular **não comprimidas** e que os dados foi carregados para a predefinição (ou para uma adicional) contentor da conta de armazenamento utilizada pelo Hadoop cluster.

Se pretender exercícios práticos nos **Dados de viagem de veículo pt**, é necessário para:

- **Transferir** o 24 pt veículo viagem de ficheiros de [Dados](http://www.andresmh.com/nyctaxitrips) do (12 ficheiros de viagem e ficheiros de tarifa 12)
- **deszipar** todos os ficheiros em ficheiros. csv e, em seguida,
- **carregue** -os para a predefinição (ou contentor apropriado) da conta de armazenamento Azure que foi criada através do procedimento descrito no tópico [clusters de personalizar o Azure HDInsight Hadoop para tecnologia e o processo de análise avançadas](machine-learning-data-science-customize-hadoop-cluster.md) . Pode encontrar o processo para carregar os ficheiros. csv para o contentor predefinido na conta de armazenamento nesta [página](machine-learning-data-science-process-hive-walkthrough.md#upload).


## <a name="submit"></a>Como pretende submeter ramo de consultas

Ramo de consultas podem ser apresentadas ao utilizar:

1. [Submeter ramo consultas através de linha de comandos do Hadoop headnode de Hadoop cluster](#headnode)
2. [Submeter ramo de consultas com o Editor de ramo](#hive-editor)
3. [Submeter ramo de consultas com comandos do PowerShell Azure](#ps)

Ramo as consultas são semelhantes a SQL. Se estiver familiarizado com SQL, poderá constatar [Hive para SQL utilizadores fazer batota folha](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf) úteis.

Quando uma consulta de ramo de a submeter, também pode controlar o destino dos resultados de consultas de ramo, quer seja no ecrã ou para um ficheiro local no nó cabeça ou para um BLOBs do Azure.


###<a name="headnode"></a>1. submeter ramo consultas através de linha de comandos do Hadoop headnode de Hadoop cluster

Se a consulta ramo for complexa, submetê-lo diretamente no nó cabeça da Hadoop cluster normalmente leva a mais rápido ativar à volta de que submetê-lo com um Hive Editor ou Azure PowerShell scripts.

Iniciar sessão cabeça nó do Hadoop cluster, abra a linha de comandos Hadoop no ambiente de trabalho do nó cabeça e introduza comando `cd %hive_home%\bin`.

Tem três formas de submeter ramo consultas na linha de comandos Hadoop:

* diretamente
* utilizar ficheiros .hql
* com a consola de comandos de ramo

#### <a name="submit-hive-queries-directly-in-hadoop-command-line"></a>Submeta ramo consultas diretamente no Hadoop linha de comandos.

Pode executar o comando como `hive -e "<your hive query>;` para submeter consultas ramo simples diretamente no Hadoop linha de comandos. Eis um exemplo, onde uma caixa vermelha destaca o comando que submete a consulta ramo e a caixa verde destaca o resultado da consulta de ramo.

![Criar a área de trabalho](./media/machine-learning-data-science-move-hive-tables/run-hive-queries-1.png)

#### <a name="submit-hive-queries-in-hql-files"></a>Submeter ramo consultas nos ficheiros do .hql

Quando a consulta ramo é mais complicada e tem várias linhas, edição de consultas na linha de comandos ou consola de comandos do ramo não é prático. Uma alternativa consiste em utilizar um editor de texto no nó de cabeça do Hadoop cluster para guardar as consultas de ramo num ficheiro .hql num diretório local do nó cabeça. Em seguida, a consulta ramo no ficheiro .hql pode ser apresentada ao utilizar o `-f` argumento da seguinte forma:

    hive -f "<path to the .hql file>"

![Criar a área de trabalho](./media/machine-learning-data-science-move-hive-tables/run-hive-queries-3.png)


**Suprimir a impressão de ecrã de estado de progresso de consultas de ramo**

Por predefinição, depois de ramo de consulta é apresentada na linha de comandos do Hadoop, o progresso da tarefa mapa/reduzir é impressa no ecrã. Para suprimir a impressão de ecrã do progresso da tarefa de mapa/reduzir, pode utilizar um argumento `-S` ("S" em maiúsculas) no comando linha da seguinte forma:

    hive -S -f "<path to the .hql file>"
    hive -S -e "<Hive queries>"

#### <a name="submit-hive-queries-in-hive-command-console"></a>Submeta ramo consultas na consola do ramo de comandos.

Pode também introduz em primeiro lugar da consola de comandos ramo ao executar o comando `hive` na linha de comandos do Hadoop e, em seguida, submeta ramo consultas na consola do ramo de comandos. Eis um exemplo. Neste exemplo, as duas caixas vermelhas realçar os comandos utilizados para introduzir a consola de comandos ramo e a consulta de ramo apresentados na consola do ramo de comando, respetivamente. A caixa verde realça o resultado da consulta de ramo.

![Criar a área de trabalho](./media/machine-learning-data-science-move-hive-tables/run-hive-queries-2.png)

Os exemplos anteriores saída diretamente os resultados da consulta ramo no ecrã. Também pode escrever o resultado para um ficheiro local no nó cabeça ou para um BLOBs do Azure. Em seguida, pode utilizar outras ferramentas para analisar ainda mais a saída de ramo de consultas.

**Resultado do ramo de resultados da consulta para um ficheiro local.**

Para exportar ramo de resultados da consulta para um diretório local no nó cabeça, tem de submeter a consulta ramo na linha de comandos Hadoop da seguinte forma:

    hive -e "<hive query>" > <local path in the head node>

No exemplo seguinte, o resultado da consulta ramo destina-se para um ficheiro `hivequeryoutput.txt` no diretório `C:\apps\temp`.

![Criar a área de trabalho](./media/machine-learning-data-science-move-hive-tables/output-hive-results-1.png)

**Resultados da consulta ramo de saída para um BLOBs do Azure**

Também pode exportar os resultados da consulta Hive para um BLOBs do Azure, dentro do contentor predefinido do Hadoop cluster. A consulta Hive para este é o seguinte:

    insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

No exemplo seguinte, o resultado da consulta ramo é escrito um diretório de BLOBs `queryoutputdir` dentro do contentor predefinido do Hadoop cluster. Aqui, só tem de fornecer o nome do diretório, sem o nome do blob. Um erro é devolvido se fornecer blob e diretório de nomes, tais como `wasb:///queryoutputdir/queryoutput.txt`.

![Criar a área de trabalho](./media/machine-learning-data-science-move-hive-tables/output-hive-results-2.png)

Se abrir o contentor predefinido do cluster Hadoop através do Explorador de armazenamento do Azure, pode ver o resultado da consulta ramo conforme apresentado na figura seguinte. Pode aplicar o filtro (realçado por uma caixa vermelha) para obter apenas o blob com letras especificadas nos nomes.

![Criar a área de trabalho](./media/machine-learning-data-science-move-hive-tables/output-hive-results-3.png)

###<a name="hive-editor"></a>2. submeter ramo de consultas com o Editor de ramo

Também pode utilizar a consola de consulta (Hive Editor) ao introduzir um URL do formulário *https://&#60; Nome do cluster Hadoop >.azurehdinsight.net/Home/HiveEditor* num browser. Tem de ser tem sessão iniciada o ver esta consola e por isso, necessita das suas Hadoop cluster credenciais aqui.

###<a name="ps"></a>3. submeter ramo de consultas com comandos do PowerShell Azure

Também pode utilizar o PowerShell para submeter ramo de consultas. Para obter instruções, consulte o artigo [Submeter Hive tarefas através do PowerShell](../hdinsight/hdinsight-submit-hadoop-jobs-programmatically.md#hive-powershell).


## <a name="create-tables"></a>Criar ramo de base de dados e tabelas

As consultas de ramo são partilhadas no [repositório Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) e podem ser transferidas a partir daí.

Segue-se a consulta de ramo que cria uma tabela Hive.

    create database if not exists <database name>;
    CREATE EXTERNAL TABLE if not exists <database name>.<table name>
    (
        field1 string,
        field2 int,
        field3 float,
        field4 double,
        ...,
        fieldN string
    )
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' lines terminated by '<line separator>'
    STORED AS TEXTFILE LOCATION '<storage location>' TBLPROPERTIES("skip.header.line.count"="1");

Eis as descrições dos campos que precisa de plug-in e outras configurações:

- **& #60; nome da base de dados >**: o nome da base de dados que pretende criar. Se pretende apenas a base de dados predefinido, a consulta de *criar a base de dados...* podem ser omitidos.
- **& #60; nome da tabela >**: o nome da tabela na qual pretende criar dentro de base de dados especificada. Se pretender utilizar a base de dados predefinido, a tabela que se pode ser referida diretamente por *& #60; nome da tabela >* sem & #60; nome da base de dados >.
- **& #60; separador de campo >**: o separador que delimits campos no ficheiro de dados ser carregado para a tabela Hive.
- **& #60; separador de linha >**: o separador que delimits linhas no ficheiro de dados.
- **& #60; localização de armazenamento >**: a localização de armazenamento Azure para guardar os dados de tabelas de ramo. Se não especificar *Localização & #60; localização de armazenamento >*, a base de dados e as tabelas são armazenadas na *ramo/armazém/* directório no contentor predefinido do cluster ramo por predefinição. Se pretender especificar a localização de armazenamento, a localização de armazenamento tem de estar dentro do contentor predefinido para a base de dados e as tabelas. Esta localização tem de ser referidas como localização relativamente ao contentor predefinido do cluster no formato de *' wasb: / / / & #60; directório 1 > /'* ou *' wasb: / / / & #60; directório 1 > / & #60; directório 2 > /'*, etc. Depois da consulta é executada, os directórios relativos são criados dentro do contentor predefinido.
- **TBLPROPERTIES("Skip.Header.line.Count"="1")**: se o ficheiro de dados tiver uma linha de cabeçalho, terá de adicionar esta propriedade **no final** da consulta *Criar tabela* . Caso contrário, a linha de cabeçalho é carregada como um registo para a tabela. Se o ficheiro de dados não tiver uma linha de cabeçalho, esta configuração pode ser omitida na consulta.

## <a name="load-data"></a>Carregar os dados a tabelas de ramo
Segue-se a consulta de ramo carrega dados para uma tabela Hive.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

- **& #60; caminho para dados blob >**: se o ficheiro de BLOBs ser carregado para a tabela Hive estiver no contentor predefinido do cluster HDInsight Hadoop, o *& #60; caminho para dados blob >* devem estar no formato *' wasb: / / / & #60; diretório neste contentor > / & #60; nome do ficheiro blob >'*. O ficheiro de BLOBs também pode ser um contentor adicionais do cluster HDInsight Hadoop. Neste caso, *& #60; caminho para dados blob >* devem estar no formato *' wasb: / / & #60; contentor name>@&#60;storage nome da conta >.blob.core.windows.net/ & #60; nome do ficheiro blob >'*.

    >[AZURE.NOTE] Os dados de BLOBs ser carregado para a tabela Hive tem de estar no contentor adicional da conta de armazenamento para Hadoop cluster ou predefinido. Caso contrário, a consulta de *Carregar dados* falha reclamar que não consegue aceder os dados.


## <a name="partition-orc"></a>Tópicos de avançadas: divididos dados de ramo de tabela e o arquivo no formato ORC

Se os dados forem grandes, a partições a tabela são útil para consultas que só precisam de para digitalizar a partições alguns da tabela. Por exemplo, é razoável partição os dados do registo de um web site por datas.

Para além de criar a partições ramo de tabelas, também é vantajoso armazenar os dados de ramo no formato otimizado linha colunar (ORC). Para mais informações sobre a formatação de ORC, consulte o artigo <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">utilizar ORC ficheiros melhora o desempenho quando ramo de leitura, escrever e processamento de dados</a>.

### <a name="partitioned-table"></a>Tabela com partições
Segue-se a consulta de ramo que cria uma tabela com partições e carrega dados para o mesmo.

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
    (field1 string,
    ...
    fieldN string
    )
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
         lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
    LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name>
        PARTITION (<partitionfieldname>=<partitionfieldvalue>);

Quando consultar tabelas com partições, recomenda-se para adicionar a condição partição no **início** da `where` cláusula como Isto melhora a eficácia da significativamente a procurar.

    select
        field1, field2, ..., fieldN
    from <database name>.<partitioned table name>
    where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>Ramo de armazenar dados no formato ORC

Não é possível diretamente carregar os dados a partir do armazenamento de Blobs do ramo tabelas que está armazenado no formato de ORC. Eis os passos que o que precisa de tomar para carregar dados a partir do Azure blobs a tabelas ramo armazenadas no formato de ORC.

Crie dados de carga e **ARMAZENADOS TEXTFILE como** uma tabela externa a partir do armazenamento de BLOBs à tabela.

        CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<external textfile table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
            lines terminated by '<line separator>' STORED AS TEXTFILE
            LOCATION 'wasb:///<directory in Azure blob>' TBLPROPERTIES("skip.header.line.count"="1");

        LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<table name>;

Criar uma tabela interna com o mesmo esquema como sendo a tabela externa no passo 1, com o mesmo delimitador e armazenar os dados de ramo no formato de ORC.

        CREATE TABLE IF NOT EXISTS <database name>.<ORC table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

Selecione os dados a partir da tabela externa no passo 1 e inserir na tabela ORC

        INSERT OVERWRITE TABLE <database name>.<ORC table name>
            SELECT * FROM <database name>.<external textfile table name>;

>[AZURE.NOTE] Se a tabela TEXTFILE *& #60; nome da base de dados >. & #60; nome da tabela externa textfile >* tem a partições, no passo 3, o `SELECT * FROM <database name>.<external textfile table name>` comando seleciona a variável de partição como um campo no conjunto de dados devolvido. A inseri-lo a *& #60; nome da base de dados >. & #60; nome da tabela ORC >* falha desde *& #60; nome da base de dados >. & #60; nome da tabela ORC >* não tem a variável de partição como um campo no esquema de tabela. Neste caso, precisa de mais especificamente, selecione os campos a ser inserida para *& #60; nome da base de dados >. & #60; nome da tabela ORC >* da seguinte forma:

        INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
           SELECT field1, field2, ..., fieldN
           FROM <database name>.<external textfile table name>
           WHERE <partition variable>=<partition value>;

É seguro largar o *& #60; nome da tabela externa textfile >* quando utilizar a seguinte consulta após todos os dados tem foi inserido *& #60; nome da base de dados >. & #60; nome da tabela ORC >*:

        DROP TABLE IF EXISTS <database name>.<external textfile table name>;

Depois de seguir este procedimento, que deve ter uma tabela com dados no formato de ORC pronto a utilizar.  
