<properties
    pageTitle="Executar Hadoop MapReduce exemplos no baseado em Linux HDInsight | Microsoft Azure"
    description="Começar a utilizar amostras MapReduce com baseado em Linux HDInsight. Utilizar SSH para ligar ao cluster, em seguida, utilize o comando Hadoop para executar tarefas de exemplo."
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
    ms.date="09/27/2016"
    ms.author="larryfr"/>




#<a name="run-the-hadoop-samples-in-hdinsight"></a>Executar as amostras Hadoop no HDInsight

[AZURE.INCLUDE [samples-selector](../../includes/hdinsight-run-samples-selector.md)]

Baseado em Linux clusters de HDInsight fornecem um conjunto de amostras MapReduce que podem ser utilizados para se familiarizar com tarefas em execução Hadoop MapReduce. Neste documento, saiba mais sobre as amostras disponíveis e guiá-lo através da execução das alguns dos mesmos.

##<a name="prerequisites"></a>Pré-requisitos

- **Azure uma subscrição**: consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)

- **Cluster baseado em respostas Linux HDInsight**: consulte o artigo [começar a utilizar o Hadoop com ramo no HDInsight em Linux](hdinsight-hadoop-linux-tutorial-get-started.md)

- **Um SSH cliente**: para obter informações sobre como utilizar SSH com HDInsight, consulte os seguintes artigos:

    - [Utilizar SSH com baseado em Linux Hadoop no HDInsight Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Utilizar SSH com baseado em Linux Hadoop no HDInsight a partir do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

## <a name="the-samples"></a>Os exemplos ##

**Localização**: as amostras estão localizadas no cluster HDInsight na **/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar**

**Conteúdo**: os exemplos seguintes estão contidos neste arquivo do:

- **aggregatewordcount**: uma agregação programa baseado no mapa/reduzir que conta as palavras em ficheiros de entrada
- **aggregatewordhist**: uma agregação programa baseado no mapa/reduzir que calcula o histograma as palavras de ficheiros de entrada
- **BBP**: um programa de mapa/reduzir utiliza Bailey-Borwein-Plouffe para calcular exatos dígitos de Pi
- **dbcount**: uma tarefa de exemplo que conta os registos de pageview armazenados num uma base de dados
- **distbbp**: um programa reduzir/mapa que utiliza uma fórmula de tipo de BBP para calcular exatos textos de Pi
- **GREP**: um programa reduzir/mapa que conta correspondências de um regex na entrada de dados
- **associação**: uma tarefa que efeitos uma associação superior ordenados, uniforme a partições conjuntos de dados
- **multifilewc**: uma tarefa que conta palavras a partir de vários ficheiros
- **pentomino**: um mapa/reduzir dispor em mosaico que programa para encontrar soluções para problemas de pentomino
- **PI**: um programa reduzir/mapa que estima Pi utilizando um período de quasi-Monte método Carlo
- **randomtextwriter**: um programa reduzir/mapa que escreve 10 GB de dados textuais aleatórios por nó
- **randomwriter**: um programa reduzir/mapa que escreve 10 GB de dados aleatórios por nó
- **secondarysort**: um exemplo que define uma ordenação secundária para a reduzir
- **Ordenar**: um programa reduzir/mapa que ordena dados escritos pelo gravador de aleatório
- **sudoku**: um sudoku o solver
- **teragen**: gerar dados para o terasort
- **terasort**: executar o terasort
- **teravalidate**: verificar os resultados da terasort
- **WordCount**: um programa reduzir/mapa que conta as palavras em ficheiros de entrada
- **wordmean**: um programa reduzir/mapa que conta o comprimento médio das palavras em ficheiros de entrada
- **wordmedian**: um programa reduzir/mapa que conta o comprimento médio das palavras em ficheiros de entrada
- **wordstandarddeviation**: um programa reduzir/mapa que conta o desvio padrão do comprimento de palavras em ficheiros de entrada

**Código fonte**: código fonte para estes exemplos está incluído no cluster HDInsight na **/usr/hdp/2.2.4.9-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples**

> [AZURE.NOTE] O `2.2.4.9-1` no caminho é a versão da plataforma Hortonworks dados para o cluster HDInsight e podem ser alteradas como HDInsight é atualizado.

## <a name="how-to-run-the-samples"></a>Como executar as amostras ##

1. Ligar ao HDInsight usando SSH, tal como descrito nos seguintes artigos:

    - [Utilizar SSH com baseado em Linux Hadoop no HDInsight Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Utilizar SSH com baseado em Linux Hadoop no HDInsight a partir do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. A partir do `username@#######:~$` pedir, utilize o seguinte comando para os exemplos da lista:

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar

    A lista de exemplo seguinte gera da secção anterior deste documento.

3. Utilize o seguinte comando para obter ajuda sobre uma amostra específica. Neste caso, a amostra de **wordcount** :

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount

    Deverá receber a seguinte mensagem:

        Usage: wordcount <in> [<in>...] <out>

    Isto indica que pode fornecer vários caminhos de entrada para os documentos de origem. O caminho final é onde armazenou o resultado (contagem de palavras em documentos de origem,).

4. Utilize o seguinte para contar palavras todos os blocos de notas de Leonardo Da Vinci, que são fornecidas como dados de exemplo com o seu cluster:

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount

    Entrada de dados para esta tarefa é de leitura a partir do **wasbs:///example/data/gutenberg/davinci.txt**.

    Para este exemplo é armazenado de saída **wasbs: / / / exemplo/dados/davinciwordcount**.

    > [AZURE.NOTE] Tal como indicado na ajuda para a amostra de wordcount, também pode especificar vários ficheiros de entrada. Por exemplo, `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` seria contar palavras davinci.txt e ulysses.txt.

5. Assim que a tarefa estiver concluída, utilize o seguinte comando para ver os resultados:

        hdfs dfs -cat /example/data/davinciwordcount/*

    Concatena todos os ficheiros de saída produzidos pela tarefa e apresentá-los. Para este exemplo básico existe apenas um ficheiro, no entanto se havia mais este comando seria iteramos sobre as mesmas.

    O resultado é semelhante ao seguinte:

        zum     1
        zur     1
        zwanzig 1
        zweite  1

    Cada linha representa uma palavra e número de vezes ocorreu nos dados de entrada.

## <a name="sudoku"></a>Sudoku

O exemplo Sudoku tem instruções a utilização de um pouco inútil.; "Incluir um puzzle na linha de comandos".

[Sudoku](https://en.wikipedia.org/wiki/Sudoku) é um puzzle lógica constituído por nove 3x3 grelhas. Algumas células na grelha de tem números, enquanto outras pessoas estão em branco e o objetivo é resolver para as células em branco. A ligação acima tem mais informações sobre o puzzle, mas o objetivo deste exemplo é resolver para as células em branco. Por isso, o nossa entrada deve ser um ficheiro que está no seguinte formato:

- Nove linhas das nove colunas

- Cada coluna pode conter um número ou `?` (que indica uma célula em branco)

- Células são separadas por um espaço

Existe uma determinada forma para construir Sudoku de palavras; Não é possível repita um número numa coluna ou linha. Existe um exemplo no cluster HDInsight que está corretamente construído. Encontra-se em **/usr/hdp/2.2.4.9-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta** e -contém o seguinte:

    8 5 ? 3 9 ? ? ? ?
    ? ? 2 ? ? ? ? ? ?
    ? ? 6 ? 1 ? ? ? 2
    ? ? 4 ? ? 3 ? 5 9
    ? ? 8 9 ? 1 4 ? ?
    3 2 ? 4 ? ? 8 ? ?
    9 ? ? ? 8 ? 5 ? ?
    ? ? ? ? ? ? 2 ? ?
    ? ? ? ? 4 5 ? 7 8

> [AZURE.NOTE] O `2.2.4.9-1` parte do caminho da podem ser alteradas, tal como as atualizações são efetuadas ao HDInsight cluster.

Para executar esta através do exemplo Sudoku, utilize o seguinte comando:

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/2.2.9.1-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta

Os resultados deverão aparecer semelhantes ao seguinte:

    8 5 1 3 9 2 6 4 7
    4 3 2 6 7 8 1 9 5
    7 9 6 5 1 4 3 8 2
    6 1 4 8 2 3 7 5 9
    5 7 8 9 6 1 4 2 3
    3 2 9 4 5 7 8 1 6
    9 4 7 2 8 6 5 3 1
    1 8 5 7 3 9 2 6 4
    2 6 3 1 4 5 9 7 8

## <a name="pi-"></a>PI (π)

O exemplo de pi utiliza uma estatística (período de quasi-Monte Carlo) método para estimar o valor de pi. Pontos aleatoriamente colocados dentro de uma unidade quadradas também se situar dentro de um círculo inscrito dentro esse quadrado com uma probabilidade igual para a área do círculo, pi/4. O valor de pi pode ser estimado do valor do 4R, onde R é o rácio entre o número de pontos que estão dentro do círculo para o número total de pontos que são utilizadas em quadrado. Maior a amostra de pontos utilizados, é melhor a estimativa.

Mapeamento de pontos para que este exemplo gera um número de pontos aleatoriamente dentro de um quadrado de unidade e, em seguida, conta o número desses pontos que estão dentro do círculo.

O redutor, em seguida, é acumulado pontos contados pelas mappers e calcula o valor de pi a partir do 4R fórmula, onde R é o rácio entre o número de pontos contados dentro do círculo para o número total de pontos que são utilizadas em quadrado.

Utilize o seguinte comando para executar este exemplo. Esta opção utiliza 16 mapas com 10.000.000 amostras para estimar o valor de pi:

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000

O valor devolvido por este deverá ser semelhante ao **3.14159155000000000000**. Para referências, as primeiros 10 casas decimais de pi são 3.1415926535.

##<a name="10gb-greysort"></a>10GB Greysort

GraySort é uma sequência de ordenação de referência cuja métrica é a taxa de ordenação (TB/minuto) que é obtida durante a ordenação muito grandes quantidades de dados, normalmente uma 100TB Mínimo.

Este exemplo utiliza uma conhecimentos básicos 10GB de dados para que possa ser executado relativamente rapidamente. Utiliza as aplicações de MapReduce desenvolvidas pelo ' Malley Owen e Arun Murthy que ganharam ao anual uso geral ("daytona") terabyte ordenar de referência no 2009 com uma taxa de 0.578 TB/min (100 TB em minutos 173). Para mais informações sobre esta e outras referências de ordenação, consulte o site [Sortbenchmark](http://sortbenchmark.org/) .

Este exemplo utiliza três conjuntos de MapReduce programas:

- **TeraGen**: um programa de MapReduce que gera linhas de dados para ordenar

- **TeraSort**: amostras de dados de entrada e utiliza MapReduce para ordenar os dados para um total da encomenda

    TeraSort é uma ordenação padrão das funções MapReduce, exceto um partitioner personalizada que utiliza uma lista ordenada de teclas N-1 amostrada que definem o intervalo de chave para cada reduzir. Em particular, todas as teclas dessas que exemplos [i-1] < = chave < exemplo [i] são enviadas para reduzir i. Isto garantias que saídas de reduzam i estão todas menor que o resultado de reduzir i + 1.

- **TeraValidate**: um programa de MapReduce valida que o resultado é ordenado globalmente

    Que cria um mapa por ficheiro no directório de saída e cada mapa garante que cada chave é menor ou igual a anterior. A função de mapa também gera registos das chaves primeiros e últimos de cada ficheiro e, a função de reduzir garante que a primeira chave do ficheiro i é maior do que a última chave da i-1 de ficheiro. Quaisquer problemas são reportados como um resultado da reduzir com as teclas que estão fora de ordem.

Utilizar os seguintes passos para gerar dados, ordenar e, em seguida, validar o resultado:

1. Gerar 10GB de dados, que serão armazenados para predefinição de armazenamento o cluster HDInsight na **wasbs: / / / exemplo / / 10GB-ordenar-introdução de dados**:

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input

    O `-Dmapred.map.tasks` indica Hadoop quantos de mapa de tarefas para utilizar para esta tarefa. Os dois parâmetros finais indique a tarefa para criar 10GB vale de dados e armazená-la na **wasbs: / / / exemplo / / 10GB-ordenar-introdução de dados**.

2. Utilize o seguinte comando para ordenar os dados:

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output

    O `-Dmapred.reduce.tasks` indica Hadoop quantos reduzir tarefas para utilizar para a tarefa. Os dois parâmetros finais são apenas as entrada e saídas localizações para os dados.

3. Utilize o seguinte para validar os dados gerados pela ordenação:

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate

##<a name="next-steps"></a>Próximos passos ##

A partir neste artigo, o que aprendeu como executar as amostras incluídas com os clusters baseado em Linux HDInsight. Para a tutoriais sobre como utilizar porco, ramo e MapReduce com HDInsight, consulte os seguintes tópicos:

* [Utilizar porco com Hadoop no HDInsight][hdinsight-use-pig]
* [Utilizar ramo com Hadoop no HDInsight][hdinsight-use-hive]
* [Utilizar MapReduce com Hadoop no HDInsight] [hdinsight-use-mapreduce]



[hdinsight-errors]: hdinsight-debug-jobs.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-sdk-documentation]: https://msdn.microsoft.com/library/azure/dn479185.aspx

[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-introduction]: hdinsight-hadoop-introduction.md



[hdinsight-samples]: hdinsight-run-samples.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
