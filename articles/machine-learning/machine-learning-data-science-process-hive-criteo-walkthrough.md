<properties
    pageTitle="O processo de ciências de dados de equipa em ação: utilizar HDInsight Hadoop clusters no conjunto de dados de Criteo 1 TB | Microsoft Azure"
    description="Utilizar o processo de ciências de dados de equipa para um cenário de ponto a ponto que utilizem um cluster de HDInsight Hadoop para criar e implementar um modelo de utilizando um conjunto de dados publicamente disponível grande do (1 TB)"
    services="machine-learning,hdinsight"
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
    ms.date="09/13/2016"
    ms.author="bradsev" />

# <a name="the-team-data-science-process-in-action---using-azure-hdinsight-hadoop-clusters-on-a-1-tb-dataset"></a>O processo de ciências de dados de equipa em ação - utilizar o Azure HDInsight Hadoop Clusters de um conjunto de dados 1 TB

Neste tutorial, vamos demonstram utilizando o processo de ciências de dados de equipa num cenário de ponto a ponto com um [cluster de Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) para armazenar, explore, engenharia de funcionalidade e para baixo para a dados de exemplo a partir de um dos conjuntos de dados de [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) disponíveis ao público. Utilizamos Azure máquina formação para criar um modelo de classificação binário nestes dados. Mostramos-como publicar um destes modelos como um serviço Web também.

Também é possível utilizar um bloco de notas IPython para realizar as tarefas apresentadas neste tutorial. Os utilizadores que gostaria de experimentar esta abordagem, deverão consultar o tópico de [instruções Criteo utilizando uma ligação Hive ODBC](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) .


## <a name="dataset"></a>Descrição Criteo conjunto de dados

Criteo dados são um conjunto de dados de previsão clique que seja aproximadamente 370GB de gzip comprimido TSV ficheiros (~1.3TB não comprimido) que incluam mais de mil milhões de 4.3 registos. É assumido a partir de 24 dias da clique em dados disponibilizados pelos [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/). Para sua comodidade dos cientistas de dados, podemos ter descompactados dados disponíveis-para experimentar com.

Cada registo este conjunto de dados contém 40 colunas:

- a primeira coluna é uma coluna de etiqueta que indica se um utilizador clica uma **Adicionar** (valor 1) ou não clique numa (valor 0)
- seguinte 13 colunas são numéricas, e
- última 26 são colunas categorias

As colunas são anónimo e utiliza uma série de nomes enumerados: "Col1" (para a coluna de etiqueta) para ' Col40 "(para a última coluna categorias).            

Eis um excerto das primeiras 20 colunas de duas observações (linhas) a partir deste conjunto de dados:

    Col1    Col2    Col3    Col4    Col5    Col6    Col7    Col8    Col9    Col10   Col11   Col12   Col13   Col14   Col15           Col16           Col17           Col18           Col19       Col20

    0       40      42      2       54      3       0       0       2       16      0       1       4448    4       1acfe1ee        1b2ff61f        2e8b2631        6faef306        c6fc10d3    6fcd6dcb           
    0               24              27      5               0       2       1               3       10064           9a8cb066        7a06385f        417e6103        2170fc56        acf676aa    6fcd6dcb                      

Existem valores em falta nas colunas numéricas e categorias este conjunto de dados. Descrevemos um método simples de acordo com os valores em falta. Detalhes adicionais, os dados são explorou quando podemos armazená-los em tabelas do ramo.

**Definição:** *Clickthrough taxa (Sugerido):* Esta é a percentagem de cliques dos dados. No conjunto de dados Criteo, o Sugerido é cerca de 3.3% ou 0.033.

## <a name="mltasks"></a>Exemplos de tarefas de previsão
Dois problemas de previsão de exemplo estão endereçados neste tutorial:

1. **Classificação binária**: prevê, com um se um utilizador clicada uma adicionar:
    - Classe 0: Não houver qualquer clique
    - Classe 1: clique em

2. **Regressão**: prevê, com a probabilidade de um clique de ad de funcionalidades de utilizador.


## <a name="setup"></a>Para cima uma HDInsight Hadoop cluster para ciência de dados

**Nota:** Isto é, normalmente, uma tarefa do **administrador** .

Configure o seu ambiente do Azure dados ciência para construção de soluções de análise de aspeto do Office com HDInsight clusters em três passos:

1. [Criar uma conta de armazenamento](../storage/storage-create-storage-account.md): esta conta de armazenamento é utilizada para armazenar dados no armazenamento de Blobs do Azure. Os dados utilizados no HDInsight clusters estiver armazenados aqui.

2. [Personalizar o Azure HDInsight Hadoop Clusters para dados ciência](machine-learning-data-science-customize-hadoop-cluster.md): este passo cria um cluster de Azure HDInsight Hadoop com 64-bit Anaconda Python 2.7 instalado em todos os nós. Existem dois passos importantes (descritos neste tópico) para concluir quando personaliza HDInsight cluster.

    * Tem de ligar a conta de armazenamento que criou no passo 1 com o seu cluster HDInsight quando é criado. Esta conta de armazenamento é utilizada para aceder aos dados que podem ser processados dentro do cluster.

    * Tem de ativar o acesso remoto para o nó do cluster de cabeça após ter sido criada. Lembre-se as credenciais de acesso remoto que especificar aqui (diferentes das especificadas para o cluster na sua criação): que são necessários para completar os seguintes procedimentos.

3. [Criar uma área de trabalho do Azure ML](machine-learning-create-workspace.md): nesta máquina de Azure formação a área de trabalho é utilizada para criar modelos de aprendizagem máquina após uma informações detalhadas inicial dos dados e para baixo amostragem no HDInsight cluster.

## <a name="getdata"></a>Obter e consuma dados a partir de uma origem de público

O conjunto de dados [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) pode ser acedido ao clicar na ligação, aceitar os termos de utilização e fornecer um nome. Um instantâneo do seguinte aspeto é mostrado aqui:

![Aceite os termos de Criteo](./media/machine-learning-data-science-process-hive-criteo-walkthrough/hLxfI2E.png)

Clique em **continuar para transferência** para ler mais sobre o conjunto de dados e a sua disponibilidade.

Os dados estão localizados numa localização [Azure blob armazenamento](../storage/storage-dotnet-how-to-use-blobs.md) pública: wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/. "wasb" refere-se para a localização de armazenamento de Blobs do Azure. 

1. Os dados neste armazenamento de BLOBs público é constituído por três subpastas de dados deszipados.

    1. A subpasta */contagem não processada/* contém os primeira, 21 dias de dados - a partir do dia\_00 para dia\_20
    2. A subpasta *observou/comboio/* for constituída por um único dia de dados, dia\_21
    3. A subpasta *observou/testar/* consiste em dois dias de dados, dia\_22 e o dia\_23

2. Para aqueles que pretende iniciar com os dados não processados gzip, estas também estão disponíveis na pasta principal *observou /* como day_NN.gz, onde NN acede a partir de 00 a 23.

Uma abordagem alternativa para aceder ao, explorar e modelo estes dados que não necessitam de qualquer locais transferências estão explicados mais abaixo neste tutorial quando criamos ramo de tabelas.

## <a name="login"></a>Iniciar sessão na headnode cluster

Para iniciar sessão headnode do cluster, utilize o [Azure portal](https://ms.portal.azure.com) para localizar o cluster. Clique no ícone de elefante HDInsight à esquerda e, em seguida, faça duplo clique no nome do seu cluster. Navegue para o separador de **configuração** , faça duplo clique no ícone de ligar na parte inferior da página e introduza as suas credenciais de acesso remoto quando lhe for pedido. Isto leva-o para headnode do cluster.

Eis o aspeto de um registo primeiro típico para o headnode cluster:

![Iniciar sessão no cluster](./media/machine-learning-data-science-process-hive-criteo-walkthrough/Yys9Vvm.png)


À esquerda, vemos "Hadoop linha de comandos", que é o nosso workhorse para a exploração de dados. Recomendamos também ver duas URLs útil - "Hadoop fio estado" e "Hadoop nome nó". O URL do Estado de fio mostra o progresso da tarefa e o URL de nó nome fornece detalhes sobre a configuração do cluster.

Agora que estão configurados e pronto para começar a primeira parte do Tutorial: informações detalhadas de dados utilizando ramo e preparar dados para Azure máquina aprendizagem.

## <a name="hive-db-tables"></a>Criar ramo de base de dados e tabelas

Para criar tabelas de ramo de conjunto de dados nosso Criteo, abra a ***linha de comandos Hadoop*** no ambiente de trabalho do nó cabeça e introduza o diretório de ramo introduzindo o comando

    cd %hive_home%\bin

>[AZURE.NOTE] Executar todos os comandos de ramo neste tutorial a partir da posição ramo / pedido de diretório. Isto leva-o até cuidado dos problemas caminho automaticamente. Utilizamos os termos "Ramo diretório linha de comandos", "ramo posição / linha de comandos do diretório" e "linha de comandos Hadoop" alternadamente.

>[AZURE.NOTE]  Para executar qualquer consulta ramo, sempre um pode utilizar os seguintes comandos:

        cd %hive_home%\bin
        hive

Depois do REPL ramo é apresentada com uma "ramo >"iniciar sessão, basta cortar e colar a consulta para executá-la.

O código seguinte cria uma base de dados "criteo" e, em seguida, gera 4 tabelas:


* uma *tabela para gerar contagens* criada num dia de dias\_00 para dia\_20,
* uma *tabela para ser utilizado como o conjunto de dados comboio* criada num dia\_21, e
* duas *tabelas para utilizam como os conjuntos de dados de teste* criadas no dia\_22 e o dia\_23 respetivamente.

Vamos dividir os nossos conjunto de dados de teste em duas tabelas diferentes uma vez que um dos dias é um feriado e Queremos determinar se o modelo de conseguir detetar diferenças entre uma férias e que não sejam férias da taxa clickthrough.

O script [exemplo #95; ramo & 95 #; criar & #95; criteo & #95; #95; & base de dados e & #95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql) é mostrado aqui para maior conveniência:

    CREATE DATABASE IF NOT EXISTS criteo;
    DROP TABLE IF EXISTS criteo.criteo_count;
    CREATE TABLE criteo.criteo_count (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/count';

    DROP TABLE IF EXISTS criteo.criteo_train;
    CREATE TABLE criteo.criteo_train (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/train';

    DROP TABLE IF EXISTS criteo.criteo_test_day_22;
    CREATE TABLE criteo.criteo_test_day_22 (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_22';

    DROP TABLE IF EXISTS criteo.criteo_test_day_23;
    CREATE TABLE criteo.criteo_test_day_23 (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_23';

Vamos tenha em atenção que todas as tabelas seguintes são externas podemos simplesmente aponte para localizações de armazenamento de Blobs do Azure (wasb).

**Existem duas formas de executar a consulta Hive qualquer que recomendamos mencionam agora.**

1. **Utilizar o REPL ramo da linha de comandos**: A primeira consiste em emitir um comando "ramo" e copiar e colar uma consulta na REPL ramo da linha de comandos. Para executar esta tarefa, efetue:

        cd %hive_home%\bin
        hive

    Agora na REPL da linha de comandos, cortar e colar a consulta executa-lo.

2. **Guardar consultas para um ficheiro e executar o comando**: O segundo é guardar as consultas para um ficheiro de .hql ([exemplo #95; ramo & 95 #; criar & #95; criteo & #95; #95; & base de dados e & #95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql)) e, em seguida, emitir o seguinte comando para executar a consulta:

        hive -f C:\temp\sample_hive_create_criteo_database_and_tables.hql


### <a name="confirm-database-and-table-creation"></a>Confirmar a criação de base de dados e tabela

Em seguida, confirmamos a criação de base de dados com o seguinte comando na posição ramo / linha de comandos do diretório:

        hive -e "show databases;"

Isto dá:

        criteo
        default
        Time taken: 1.25 seconds, Fetched: 2 row(s)

Isto confirma a criação de uma nova base de dados, "criteo".

Para ver que criámos de tabelas, podemos simplesmente emitir o comando da posição ramo / linha de comandos do diretório:

        hive -e "show tables in criteo;"

Vamos, em seguida, consulte o seguinte resultado:

        criteo_count
        criteo_test_day_22
        criteo_test_day_23
        criteo_train
        Time taken: 1.437 seconds, Fetched: 4 row(s)

##<a name="exploration"></a>Informações detalhadas de dados no ramo

Agora, vamos está prontos para fazer algumas informações detalhadas de dados básico no ramo. Vamos começar por contagem do número de exemplos no comboio e testar tabelas de dados.

### <a name="number-of-train-examples"></a>Número de exemplos de comboio

Os conteúdos de [exemplo e #95; ramo & #95; contar & #95; comboio & #95; tabela & #95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_train_table_examples.hql) são apresentados aqui:

        SELECT COUNT(*) FROM criteo.criteo_train;

Este número contiver:

        192215183
        Time taken: 264.154 seconds, Fetched: 1 row(s)

Em alternativa, um pode também emitir o seguinte comando na posição ramo / linha de comandos do diretório:

        hive -f C:\temp\sample_hive_count_criteo_train_table_examples.hql

### <a name="number-of-test-examples-in-the-two-test-datasets"></a>Número de exemplos de teste de dois conjuntos de dados de teste

Agora, vamos contar o número de exemplos de dois conjuntos de dados de teste. Os conteúdos de [exemplo & #95; ramo & #95 contar & #95; criteo & #95; teste & #95; dia e #95; 22 & #95; tabela & #95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_22_table_examples.hql) está aqui:

        SELECT COUNT(*) FROM criteo.criteo_test_day_22;

Este número contiver:

        189747893
        Time taken: 267.968 seconds, Fetched: 1 row(s)

Como habitualmente, poderá também chamamos o script na posição ramo / directório de linha de comandos por emitir o comando:

        hive -f C:\temp\sample_hive_count_criteo_test_day_22_table_examples.hql

Por fim, vamos examinar o número de exemplos de teste o conjunto de dados de teste com base num dia\_23.

O comando para fazer isto é semelhante àquele apenas apresentado (consulte [exemplo & #95; ramo & #95; contar & #95; criteo & #95; #95 & teste; dia e #95; 23 & #95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_23_examples.hql)):

        SELECT COUNT(*) FROM criteo.criteo_test_day_23;

Isto dá:

        178274637
        Time taken: 253.089 seconds, Fetched: 1 row(s)

### <a name="label-distribution-in-the-train-dataset"></a>O conjunto de dados comboio a distribuição de etiqueta

A distribuição de etiqueta no comboio dataset é de interesse. Para ver isto, podemos apresentar os conteúdos de [exemplo & #95; ramo & #95; criteo & #95; #95 & etiqueta; distribuição & #95; comboio & #95;table.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_label_distribution_train_table.hql):

        SELECT Col1, COUNT(*) AS CT FROM criteo.criteo_train GROUP BY Col1;

Este número contiver a distribuição de etiqueta:

        1       6292903
        0       185922280
        Time taken: 459.435 seconds, Fetched: 2 row(s)

Note que a percentagem de etiquetas positivas é de cerca de 3.3% (consistente com o conjunto de dados original).

### <a name="histogram-distributions-of-some-numeric-variables-in-the-train-dataset"></a>Distribuições histograma de algumas variáveis numéricos no comboio conjunto de dados

Pode utilizamos nativo do ramo "histograma\_numéricos" função para descobrir o aspeto da distribuição das variáveis numéricas. Aqui estão os conteúdos de [exemplo e #95; ramo & #95; criteo & #95; histograma & #95;numeric.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql):

        SELECT CAST(hist.x as int) as bin_center, CAST(hist.y as bigint) as bin_height FROM
            (SELECT
            histogram_numeric(col2, 20) as col2_hist
            FROM
            criteo.criteo_train
            ) a
            LATERAL VIEW explode(col2_hist) exploded_table as hist;

Este número contiver o seguinte procedimento:

        26      155878415
        2606    92753
        6755    22086
        11202   6922
        14432   4163
        17815   2488
        21072   1901
        24113   1283
        27429   1225
        30818   906
        34512   723
        38026   387
        41007   290
        43417   312
        45797   571
        49819   428
        53505   328
        56853   527
        61004   160
        65510   3446
        Time taken: 317.851 seconds, Fetched: 20 row(s)

A vista - laterais destacar combinação no ramo serve para produzir uma saída SQL gosto em vez da lista habitual. Tenha em atenção que nesta tabela, a primeira coluna corresponde ao centro de posição e o segundo para a frequência de reciclagem.

### <a name="approximate-percentiles-of-some-numeric-variables-in-the-train-dataset"></a>Percentis aproximados de algumas variáveis numéricos no comboio conjunto de dados

Também de interesse com variáveis numéricas é o cálculo da percentis aproximados. Ramo do nativo "percentil\_aproximado" faz isto para-nos. Os conteúdos de [exemplo & #95; ramo & #95; criteo & #95; aproximada & #95;percentiles.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql) são:

        SELECT MIN(Col2) AS Col2_min, PERCENTILE_APPROX(Col2, 0.1) AS Col2_01, PERCENTILE_APPROX(Col2, 0.3) AS Col2_03, PERCENTILE_APPROX(Col2, 0.5) AS Col2_median, PERCENTILE_APPROX(Col2, 0.8) AS Col2_08, MAX(Col2) AS Col2_max FROM criteo.criteo_train;

Este número contiver:

        1.0     2.1418600917169246      2.1418600917169246    6.21887086390288 27.53454893115633       65535.0
        Time taken: 564.953 seconds, Fetched: 1 row(s)

Vamos comente que a distribuição dos percentis está intimamente relacionado com a distribuição de histograma de qualquer variável numérico normalmente.        

### <a name="find-number-of-unique-values-for-some-categorical-columns-in-the-train-dataset"></a>Encontrar o número de valores exclusivos para algumas colunas categorias no comboio conjunto de dados

Continuar a exploração de dados, podemos agora localizar, para algumas categorias colunas, o número de valores exclusivos tomem. Para fazer isto, podemos apresentar os conteúdos de [exemplo & #95; ramo & #95; criteo & #95; exclusivo & #95; valores & #95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_categoricals.hql):

        SELECT COUNT(DISTINCT(Col15)) AS num_uniques FROM criteo.criteo_train;

Este número contiver:

        19011825
        Time taken: 448.116 seconds, Fetched: 1 row(s)

Nota: que Col15 tem valores exclusivos 19M! Utilizar técnicas naïve como "codificação quente um" codificar variáveis categorias tais como sendo de alta dimensionais é inviável. Em particular, estamos a explicar e demonstram uma técnica eficientes e robusta denominada [Aprendizagem com contagens de palavras](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) para lidar com este problema de forma eficaz.

Esta sub-secção podemos terminar verificando o número de valores exclusivos para algumas outras categorias colunas também. Os conteúdos de [exemplo & #95; ramo & #95; criteo & #95; exclusivo & #95 #95 & valores; múltiplo & #95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_multiple_categoricals.hql) são:

        SELECT COUNT(DISTINCT(Col16)), COUNT(DISTINCT(Col17)),
        COUNT(DISTINCT(Col18), COUNT(DISTINCT(Col19), COUNT(DISTINCT(Col20))
        FROM criteo.criteo_train;

Este número contiver:

        30935   15200   7349    20067   3
        Time taken: 1933.883 seconds, Fetched: 1 row(s)

Novamente Vemos que exceto Col20, todas as outras colunas têm muitos valores exclusivos.

### <a name="co-occurence-counts-of-pairs-of-categorical-variables-in-the-train-dataset"></a>Cocriação ocorrência contagens de palavras de pares de variáveis de categorias no comboio conjunto de dados

As contagens de cocriação ocorrência de pares de variáveis de categorias é também de interesse. Isto pode ser determinado utilizando o código na [exemplo & #95; ramo & #95; criteo & #95; emparelhado & #95; categorias & #95;counts.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_paired_categorical_counts.hql):

        SELECT Col15, Col16, COUNT(*) AS paired_count FROM criteo.criteo_train GROUP BY Col15, Col16 ORDER BY paired_count DESC LIMIT 15;

Vamos inversa encomendar as contagens por sua ocorrência e procurar na parte superior 15 neste caso. Isto dá-nos:

        ad98e872        cea68cd3        8964458
        ad98e872        3dbb483e        8444762
        ad98e872        43ced263        3082503
        ad98e872        420acc05        2694489
        ad98e872        ac4c5591        2559535
        ad98e872        fb1e95da        2227216
        ad98e872        8af1edc8        1794955
        ad98e872        e56937ee        1643550
        ad98e872        d1fade1c        1348719
        ad98e872        977b4431        1115528
        e5f3fd8d        a15d1051        959252
        ad98e872        dd86c04a        872975
        349b3fec        a52ef97d        821062
        e5f3fd8d        a0aaffa6        792250
        265366bf        6f5c7c41        782142
        Time taken: 560.22 seconds, Fetched: 15 row(s)

## <a name="downsample"></a>Os conjuntos de dados de exemplo para baixo para Azure máquina aprendizagem

Está com dificuldades explorou os conjuntos de dados e demonstrado como podemos poderá fazer este tipo de informações detalhadas para quaisquer variáveis (incluindo combinações), vamos agora para baixo de exemplo os conjuntos de dados para que possamos criar modelos no Azure máquina aprendizagem. Resgate que é o problema, vamos concentrar-nos: fornecido um conjunto de atributos de exemplo (valores de funcionalidade do Col2 - Col40), o podemos prever se Col1 é um 0 (não clique) ou um 1 (clique).

Para baixo nosso comboio de exemplo e testar conjuntos de dados para 1% relativamente ao tamanho original, utilizamos função rand () nativa do ramo. O seguinte script, [exemplo & #95; ramo & #95; criteo & #95; tipo & #95; comboio & #95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_train_dataset.hql) faz isto para o conjunto de dados comboio:

        CREATE TABLE criteo.criteo_train_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        ---Now downsample and store in this table

        INSERT OVERWRITE TABLE criteo.criteo_train_downsample_1perc SELECT * FROM criteo.criteo_train WHERE RAND() <= 0.01;

Este número contiver:

        Time taken: 12.22 seconds
        Time taken: 298.98 seconds

O script [exemplo & #95; ramo & #95; criteo & #95; tipo & #95; teste & #95; dia e #95; 22 & #95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) significa para os dados de teste, dia\_22:

        --- Now for test data (day_22)

        CREATE TABLE criteo.criteo_test_day_22_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_22_downsample_1perc SELECT * FROM criteo.criteo_test_day_22 WHERE RAND() <= 0.01;

Este número contiver:

        Time taken: 1.22 seconds
        Time taken: 317.66 seconds


Por fim, o script [exemplo & #95; ramo & #95; criteo & #95; tipo & #95; teste & #95; dia e #95; 23 & #95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql) que para os dados de teste, dia\_23:

        --- Finally test data day_23
        CREATE TABLE criteo.criteo_test_day_23_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 srical feature; tring)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_23_downsample_1perc SELECT * FROM criteo.criteo_test_day_23 WHERE RAND() <= 0.01;

Este número contiver:

        Time taken: 1.86 seconds
        Time taken: 300.02 seconds

Com esta ação, iremos está prontos utilizar os nossos comboio amostra para baixo e testar conjuntos de dados para a criação de modelos no Azure máquina aprendizagem.

Existe um componente de importante final antes de podemos avançar para Azure máquina formação, que é preocupações relativos a tabela de contagem. Na secção seguinte subpastas, discutimos isto algumas detalhadamente.

##<a name="count"></a>Um breve debate sobre a tabela de contagem

Como é mostrado, várias variáveis de categorias têm uma dimensão muito alta. No nosso tutorial, podemos apresentar uma técnica poderosa denominada [Aprendizagem com contagens de palavras](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) para codificar estas variáveis de forma eficaz, robusta. Obter mais informações sobre esta técnica estão na hiperligação fornecida.

**Nota:** Neste tutorial, iremos concentrar-nos utilizando tabelas de contagem para produzir compactos representações de funcionalidades de categorias como sendo de alta dimensionais. Não é a única forma de codificar funcionalidades categorias; Para mais informações sobre outras técnicas, os utilizadores interessados podem dar saída [um quente-codificação](http://en.wikipedia.org/wiki/One-hot) e [funcionalidade hashing](http://en.wikipedia.org/wiki/Feature_hashing).

Para criar tabelas de contagem nos dados contar, utilizamos os dados na pasta não processado/contagem. Na secção de modelação, vamos mostrar aos utilizadores como criar tabelas estes contar para funcionalidades categorias de raiz, ou em alternativa para utilizar uma tabela de contagem previamente concebidos para os respetivos explorations. Em que se segue, quando a referência é feita "contar pré-concebidos tabelas", podemos significa utilizando as tabelas de contagem fornecemos. Obter instruções detalhadas sobre como aceder a estas tabelas são fornecidas na secção seguinte.

## <a name="aml"></a>Criar um modelo com Azure máquina aprendizagem

Nosso modelo de criação de processo no Azure máquina aprendizagem segue estes passos:

1. [Colocar os dados de tabelas de ramo no Azure máquina aprendizagem](#step1)
2. [Criar a experiência: limpar os dados, selecione um aprendiz e featurize com tabelas de contagem](#step2)
3. [Preparar o modelo](#step3)
4. [Pontuação o modelo de dados de teste](#step4)
5. [Avaliar o modelo](#step5)
6. [Publicar o modelo como um serviço web para ser consumidas](#step6)

Agora podemos está prontos criar modelos de studio Azure máquina aprendizagem. Os nossos dados de amostra para baixo são guardados como ramo de tabelas no cluster. Utilizamos o módulo Azure máquina aprendizagem **Importar dados** para ler estas dados. As credenciais para aceder à conta de armazenamento deste cluster são fornecidas no que se segue.

### <a name="step1"></a>Passo 1: Obter dados a partir do ramo de tabelas para o Azure máquina aprender a utilizar o módulo importar dados e selecione-o para uma experiência de aprendizagem automática

Comece por selecionar uma **+ Novo** -> **experiência** -> **Experiência em branco**. Em seguida, da caixa de **pesquisa** na parte superior esquerda, procure "Dados de importação". Arraste e largue o módulo **Importar dados** para a tela da experiência (parte meio do ecrã) para utilizar o módulo para acesso aos dados.

Este é o aspeto de **Importar dados** ao obter dados a partir da tabela Hive:

![Importar dados obtém dados](./media/machine-learning-data-science-process-hive-criteo-walkthrough/i3zRaoj.png)

Para o módulo **Importar dados** , os valores dos parâmetros que são fornecidos no gráfico são apenas exemplos de ordenação de valores que tem de fornecer. Aqui estão algumas informações gerais sobre como preencher o parâmetro definido para o módulo **Importar dados** .

1. Selecione "Ramo de consulta" **Origem** de dados
2. Na caixa **ramo de consulta de base de dados** , uma simple SELECT * FROM < seu\_base de dados\_name.your\_tabela\_nome >-é suficiente.
3. **URI do servidor de Hcatalog**: se seu cluster for "abc", em seguida, este é simplesmente: https://abc.azurehdinsight.net
4. **Hadoop nome da conta de utilizador**: O nome de utilizador escolhido no momento da entrada do cluster. (Não o acesso remoto nome de utilizador!)
5. **Palavra-passe de conta de utilizador Hadoop**: A palavra-passe para o nome de utilizador escolhido no momento da entrada do cluster. (Não a de acesso remoto palavra-passe!)
6. **Localização dos dados de saída**: selecione "Azure"
7. **Nome da conta Azure armazenamento**: A conta de armazenamento associada ao cluster
8. **Chave de conta de armazenamento Azure**: A chave da conta de armazenamento associado ao cluster.
9. **Nome do contentor Azure**: se o nome do cluster for "abc", em seguida, este nome costuma simplesmente "abc,".


Assim que a **Importar dados** de terminar a introdução de dados (pode ver a escala verde no módulo de), guarde estes dados como um conjunto de dados (com um nome da sua preferência). O seguinte aspeto:

![Importar dados guardar dados](./media/machine-learning-data-science-process-hive-criteo-walkthrough/oxM73Np.png)

Botão direito do rato a porta de saída do módulo **Importar dados** . Isto, são uma opção **Guardar como conjunto de dados** e uma opção de **visualizar** . A opção de **visualizar** , se clicou em, apresenta 100 linhas de dados, juntamente com um painel à direita, que é útil para algumas estatísticas de resumo. Para guardar os dados, basta selecione **Guardar como conjunto de dados** e siga as instruções.

Para selecionar o conjunto de dados guardado para utilizar uma experiência de formação de máquina, localize conjuntos de dados utilizando a caixa de **pesquisa** apresentada na figura seguinte. Em seguida, basta escrever saída o nome que lhe atribuiu o conjunto de dados parcialmente para aceder à mesma e arraste o conjunto de dados para o painel principal. Largá-lo para o painel principal seleciona para utilização em modelação de aprendizagem do computador.

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/cl5tpGw.png)

>[AZURE.NOTE] Fazê-lo para o comboio e conjuntos de dados de teste. Além disso, lembre-se utilizar o nome da base de dados e nomes de tabela que lhe atribuiu para esse efeito. Os valores utilizados na figura são exclusivamente para ilustração purposes.* *

### <a name="step2"></a>Passo 2: Criar uma experiência simple no Azure máquina formação para prever cliques / sem cliques

Experiência nosso Azure ML tem o seguinte aspeto:

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/xRpVfrY.png)

Vamos agora, examine os componentes de chave desta experiência. Como um lembrete, precisamos de arraste os nossos comboio guardado e testar conjuntos de dados para os nossos tela experiência pela primeira vez.

#### <a name="clean-missing-data"></a>Limpar os dados em falta

O módulo **LIMPARB dados em falta** é que o nome sugere: limpa os dados em falta de formas que podem ser especificado pelo utilizador. Está à procura para este módulo, vamos ver isto:

![Limpar dados em falta](./media/machine-learning-data-science-process-hive-criteo-walkthrough/0ycXod6.png)

Aqui, optamos por substitua todos os valores em falta um "0". Existem outras opções, assim, que podem ser vistas verificando listas pendentes no módulo.

#### <a name="feature-engineering-on-the-data"></a>Funcionalidade de engenharia nos dados

Pode ser milhões de valores exclusivos algumas funcionalidades categorias de grandes conjuntos de dados. Utilizar naïve métodos como quente um codificação para que a representa, tais como sendo de alta dimensionais funcionalidades de categorias é completamente inviável. Neste tutorial, vamos demonstram como utilizar funcionalidades de contagem utilizando incorporados módulos de formação do Azure máquina para gerar representações compactos destas variáveis de categorias como sendo de alta dimensionais. O resultado final é um tamanho de modelo mais pequeno, mais rápidas horas de formação e métricas de desempenho que são bastante comparáveis ao utilizar outras técnicas.

##### <a name="building-counting-transforms"></a>Edifício contando transformações

Para criar as funcionalidades de contagem, utilizamos o módulo **Construir contando transformar** que está disponível no Azure máquina aprendizagem. O módulo tem o seguinte aspeto:


![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/e0eqKtZ.png)
![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/OdDN0vw.png)


**Importante** : na caixa de **contagem de colunas** , podemos introduza das colunas que Gostaríamos para desempenhar contagens em. Normalmente, estes são (tal como mencionado) como sendo de alta dimensionais colunas categorias. No início, mencionado que o conjunto de dados Criteo tem 26 colunas categorias: a partir do Col15 para Col40. Aqui, podemos contar em todas as-las e dar à suas índices (a partir de 15 40 separados por vírgulas, conforme mostrado).

Para utilizar o módulo no modo de MapReduce (apropriado para grandes conjuntos de dados), precisamos acesso a um cluster de HDInsight Hadoop (a que utilizou para obter informações detalhadas funcionalidade pode ser reutilizada para esse efeito também) e as suas credenciais. Os valores do anterior ilustram a que os preenchido valores o aspeto (substitua os valores previstos ilustração com as relevantes para a sua própria casos de utilização).

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/05IqySf.png)

Na imagem acima, mostramos-como introduza a localização de BLOBs entrada. Esta localização tem os dados reservados para criar tabelas de contagem.


Depois deste módulo estiver concluída, podemos pode guardar a transformação para mais tarde ao clicar no módulo e selecionar a opção **Guardar como transformar** :

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/IcVgvHR.png)

No nossa arquitetura de experiência mostrada acima, o conjunto de dados "ytransform2" corresponde exatamente a uma transformação de contagem guardado. Para o resto desta experiência, recomendamos partem do princípio de que o leitor utilizado um módulo **Construir contando transformar** em alguns dados para gerar contagens e, em seguida, pode utilizar essas contagens para gerar a contagem de funcionalidades no comboio e testar conjuntos de dados.

##### <a name="choosing-what-count-features-to-include-as-part-of-the-train-and-test-datasets"></a>Escolher que contagem de funcionalidades para incluir como parte do conjuntos de dados comboio e teste

Assim que temos uma contagem transformar prontos, o utilizador pode escolher quais as funcionalidades incluir na sua comboio e testar a utilizar o módulo **Modificar parâmetros de tabela de contagem** de conjuntos de dados. Mostramos apenas este módulo aqui para integridade, mas interesse da simplificar não realmente utilizá-lo no nossa experiência.

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/PfCHkVg.png)

Neste caso, tal como podem ser vistos, podemos escolheu para utilizar apenas as registo de hipóteses e ignorar anterior desativar coluna. Recomendamos também pode definir parâmetros, tal como o limiar de posição de lixo, quantas exemplos pseudo-aleatória prévias adicionar para suavização e se deve utilizar qualquer ruído Laplacian ou não. Todos estes funcionalidades avançados e é referir que os valores predefinidos são um bom ponto de partida para os utilizadores que estiver familiarizado com este tipo de geração de funcionalidade.

##### <a name="data-transformation-before-generating-the-count-features"></a>Transformação de dados antes de gerar as funcionalidades de contagem

Agora que focar-se num ponto de importante sobre transformará nosso comboio e testar dados antes de gerar realmente funcionalidades de contagem. Note que existem dois **Executar scripts de R** módulos utilizados antes de podemos aplicam-se a transformação de contagem para os nossos dados.

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/aF59wbc.png)

Eis o script R primeiro:

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/3hkIoMx.png)


Neste script R, podemos mude o nossas colunas para nomes "Col1" para "Col40". Isto acontece porque a transformação de contagem espera nomes neste formato.

No segundo script R, podemos saldo a distribuição entre classes positivas e negativas (classes 1 e 0 respetivamente) por subamostragem a classe negativa. O script R aqui mostra como fazer isto:

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/91wvcwN.png)

Neste script R simples, utilizamos "posição\_insucesso\_rácio" para definir a quantidade de equilíbrio entre o positivo e negativas classes. Isto é importante para efetuar uma vez que melhorar a diferença de classe normalmente tem vantagens de desempenho para problemas de classificação onde está a distribuição de classe distorcida (resgate que no nosso caso, temos classe positivo 3.3% e escolares negativo 96.7%).

##### <a name="applying-the-count-transformation-on-our-data"></a>Aplicar a transformação de contar os nossos dados

Por fim, vamos pode utilizar o módulo de **Transformação aplicam-se** para aplicar as transformações de contagem no nosso comboio e testar conjuntos de dados. Este módulo assume a transformação de contagem guardada como uma entrada e conjuntos de dados comboio ou de teste como a outra entrada e devolve dados com funcionalidades de contagem. É mostrada aqui:

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/xnQvsYf.png)

##### <a name="an-excerpt-of-what-the-count-features-look-like"></a>Um excerto das funcionalidades que contar aspeto

É instruções para ver o aspeto das funcionalidades de contagem no nosso caso. Aqui vamos mostrar um excerto do presente:

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/FO1nNfw.png)

Este excerto, mostramos que para as colunas que recomendamos contados no, podemos aceder as contagens e inicie sessão hipóteses para além de qualquer backoffs relevantes.

Vamos agora está prontos criar um modelo de aprendizagem do Azure máquina utilizar estes conjuntos de dados transformados com logaritmos. Na secção seguinte, mostramos-como isto pode ser feito.

#### <a name="azure-machine-learning-model-building"></a>Criar um modelo máquina aprendizagem Azure

##### <a name="choice-of-learner"></a>Escolha apoios

Em primeiro lugar, precisamos de escolher uma aprendiz. Vamos utilizar uma árvore de decisões de duas classe aumentado como nosso aprendiz. Aqui estão as opções predefinidas para este Aprendiz:

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/bH3ST2z.png)

Para os nossos experiência, vamos para escolher os valores predefinidos. Vamos tenha em atenção que as predefinições são normalmente significativas e uma boa forma de obter rápida planos base no desempenho. Pode melhorar no desempenho por parâmetros de remoção se optar por assim que tiver um plano base.

#### <a name="train-the-model"></a>Preparar o modelo

De cursos de formação, podemos simplesmente invocar um módulo de **Modelo de comboio** . Duas entradas ao mesmo são aprendiz a árvore de decisões aumentado duas escolares e conjunto de dados nosso comboio. Isto é mostrado aqui:

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/2bZDZTy.png)


#### <a name="score-the-model"></a>O modelo de pontuação

Assim que temos um modelo de formação, podemos está prontos para o conjunto de dados de teste de pontuação e para avaliar o respetivo desempenho. Vamos fazê-lo ao utilizar o módulo de **Modelo de pontuação** apresentado na figura seguinte, juntamente com um módulo de **Modelo de avaliar** :

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/fydcv6u.png)


### <a name="step5"></a>Passo 5: Avaliar o modelo

Por fim, gostaríamos de analisar o desempenho do modelo. Normalmente, dois problemas de classificação (binárias) de classe, uma boa medida é o AUC. Para visualizar isto, podemos ligar o módulo de **Modelo de classificação** a um módulo de **Modelo de avaliar** para esta situação. Clicar em **visualizar** no módulo de **Modelo de avaliar** produz um gráfico tal como o seguinte:

![Avaliar módulo BDT modelo](./media/machine-learning-data-science-process-hive-criteo-walkthrough/0Tl0cdg.png)

Em binário (ou escolares dois) problemas de classificação, uma boa medida da precisão de previsão é a área em curva (AUC). O que se segue, mostramos resultados do nosso utilizando este modelo no nosso conjunto de dados de teste. Para obter este, com o botão direito do módulo de **Modelo de avaliar** e, em seguida, **visualizar**a porta de saída.

![Visualizar o módulo de modelo avaliar](./media/machine-learning-data-science-process-hive-criteo-walkthrough/IRfc7fH.png)

### <a name="step6"></a>Passo 6: Publicar o modelo, como um serviço Web
A capacidade de publicar um modelo de aprendizagem do Azure máquina como serviços web com um mínimo de facilmente é uma funcionalidade útil para tornando amplamente disponíveis. Uma vez que tenha feito, qualquer pessoa pode efetuar chamadas para o serviço web com dados de entrada que precisam de previsões para e o serviço web utiliza o modelo para devolver essas previsões.

Para fazer isto, podemos pela primeira vez guarde nossa formação modelo como um objeto de modelo de formação. Isto é feito ao clicar no módulo de **Modelo de comboio** e utilizar a opção **Guardar como modelo qualificados** .

Em seguida, precisamos de criar entrada e saída de portas para a nossa serviço web:

* porta de entrada leva-o até dados da mesma forma como os dados que precisamos de previsões para
* Devolve uma porta de saída as etiquetas classificados e as probabilidades associadas.

#### <a name="select-a-few-rows-of-data-for-the-input-port"></a>Selecione algumas linhas de dados para a porta de entrada

É conveniente utilizar um módulo de **Transformação de SQL aplicam-se** para selecionar apenas 10 linhas para servir como os dados de porta de entrada. Selecione apenas estes linhas de dados para os nossos porta de entrada utilizando a consulta SQL mostrada aqui:

![Porta de entrada de dados](./media/machine-learning-data-science-process-hive-criteo-walkthrough/XqVtSxu.png)

#### <a name="web-service"></a>Serviço Web
Agora, vamos está prontos executar uma experiência pequenas que pode ser utilizada para publicar o nosso serviço web.

#### <a name="generate-input-data-for-webservice"></a>Gerar dados de entrada para a função serviçoweb

Como um passo de zeroth, uma vez que a tabela de contagem for grande, vamos demorar algumas linhas de dados de teste e gerar dados de saída do mesmo com as funcionalidades de contagem. Isto pode servir o formato de dados de entrada para os nossos serviçoweb. Isto é mostrado aqui:

![Criar dados de entrada BDT](./media/machine-learning-data-science-process-hive-criteo-walkthrough/OEJMmst.png)

>[AZURE.NOTE] Para o formato de dados de entrada, utilizamos agora o resultado do módulo **Featurizer contagem** . Assim que esta experiência estiver concluída, guarde o resultado do módulo **Contar Featurizer** como um conjunto de dados. Este conjunto de dados é utilizado para os dados de entrada, a função serviçoweb.

#### <a name="scoring-experiment-for-publishing-webservice"></a>Experiência de pontuação para serviçoweb publicação

Em primeiro lugar, é apresentado o seguinte aspeto. A estrutura essencial é um módulo de **Modelo de pontuação** aceita objeto nosso modelo com formação e algumas linhas de dados de entrada que recomendamos gerados nos passos anteriores utilizar o módulo **Featurizer contagem** . Utilizamos "Selecionar colunas no conjunto de dados" para o project saída as etiquetas de Scored e as probabilidades de pontuação.

![Selecione as colunas no conjunto de dados](./media/machine-learning-data-science-process-hive-criteo-walkthrough/kRHrIbe.png)

Repare como o módulo **Selecionar colunas no conjunto de dados** pode ser utilizado para 'Filtrar' dados de um conjunto de dados. Mostramos-os conteúdos aqui:

![Filtrar com selecionar colunas no módulo do conjunto de dados](./media/machine-learning-data-science-process-hive-criteo-walkthrough/oVUJC9K.png)

Para obter os entrada azul e de portas de saída, basta clicar em **preparar serviçoweb** na parte inferior direita. Executar esta experiência também lhe permite-nos publicar o serviço web: clique no ícone de **Publicar serviço WEB** na parte inferior direita, é mostrado aqui:

![Publicar o serviço Web](./media/machine-learning-data-science-process-hive-criteo-walkthrough/WO0nens.png)


Assim que a função serviçoweb é publicado, podemos obter redirecionados para uma página com um aspeto assim:

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/YKzxAA5.png)

Vamos ver duas ligações para webservices no lado esquerdo:

* **Resposta/pedido de** serviço (ou RRS) destinam-se a única previsões e é o que recomendamos utilizam neste seminário.
* O serviço de **Execução de lote** (BES) é utilizada para previsões lote e requer que os dados de entrada utilizado para efetuar previsões residem na armazenamento de Blobs do Azure.

Clicar na ligação **Que pedido/resposta** leva-na uma página que dá-nos previamente enlatados código no c#, python e R. Este código pode ser utilizado de forma conveniente para efetuar chamadas para o serviçoweb. Tenha em atenção que a chave de API nesta página tem de ser utilizado para autenticação.

É conveniente copiar este código python sobre para uma nova célula no bloco de notas IPython.

Aqui é apresentado um segmento de código python com a chave de API correta.

![Código de Python](./media/machine-learning-data-science-process-hive-criteo-walkthrough/f8N4L4g.png)


Tenha em atenção que recomendamos substituídos a chave de API predefinida com a chave de API do nosso webservices. Clicar em **Executar** esta célula num bloco de notas IPython rendimentos a resposta seguinte:

![Resposta IPython](./media/machine-learning-data-science-process-hive-criteo-walkthrough/KSxmia2.png)

Vemos que para os dois exemplos de teste que podemos mais sobre (no quadro JSON do python script), podemos voltar respostas no formulário de "Scored etiquetas, Scored probabilidades". Tenha em atenção que neste caso, optamos pelos valores predefinidos que o código previamente em lata fornece (0 para todas as colunas numéricas e a cadeia "valor" para todas as colunas de categorias).

Isto conclui os nossos ponto a ponto instruções passo a passo que mostra como lidar com conjunto de dados em grande escala com o Azure máquina aprendizagem. Vamos começar com um terabyte de dados, construídos um modelo de previsão e implementado-lo como um serviço web na nuvem.
