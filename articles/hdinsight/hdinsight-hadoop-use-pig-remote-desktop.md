<properties
   pageTitle="Utilizar Hadoop porco com o ambiente de trabalho remoto no HDInsight | Microsoft Azure"
   description="Saiba como utilizar o comando porco para executar porco Latim declarações a partir de uma ligação de ambiente de trabalho remoto para um cluster de baseados no Windows Hadoop HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/11/2016"
   ms.author="larryfr"/>

#<a name="run-pig-jobs-from-a-remote-desktop-connection"></a>Executar tarefas porco a partir de uma ligação de ambiente de trabalho remoto

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Este artigo fornece instruções para utilizar o comando porco para executar porco Latim declarações a partir de uma ligação de ambiente de trabalho remoto para um cluster de HDInsight baseados no Windows. Porco Latim permite-lhe criar aplicações de MapReduce por descrever transformações de dados, em vez de mapear e reduzir funções.

Neste documento, saiba como

##<a id="prereq"></a>Pré-requisitos

Para concluir os passos neste artigo, terá o seguinte.

* Um cluster de HDInsight baseados no Windows (Hadoop no HDInsight)

* Um computador cliente a executar o Windows 10, Windows 8 ou Windows 7

##<a id="connect"></a>Ligar-se ao ambiente de trabalho remoto

Ativar o ambiente de trabalho remoto para HDInsight cluster, em seguida, ligar para o mesmo ao seguir as instruções em [ligar ao clusters HDInsight utilizando o RDP](hdinsight-administer-use-management-portal.md#rdp).

##<a id="pig"></a>Utilize o comando porco

2. Depois de ter uma ligação de ambiente de trabalho remoto, comece a **linha de comandos Hadoop** utilizando o ícone no ambiente de trabalho.

2. Utilize o seguinte procedimento para iniciar o comando porco:

        %pig_home%\bin\pig

    Será apresentada com uma `grunt>` linha de comandos.

3. Introduza a seguinte instrução:

        LOGS = LOAD 'wasbs:///example/data/sample.log';

    Este comando carrega o conteúdo do ficheiro sample.log para o ficheiro de registos. Pode ver o conteúdo do ficheiro ao utilizar o seguinte comando:

        DUMP LOGS;

4. Transforme os dados ao aplicar uma expressão regular para extrair o nível de registo de cada registo:

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

    Pode utilizar **Falha de sistema** para ver os dados após a transformação. Neste caso, `DUMP LEVELS;`.

5. Continue a aplicar transformações utilizando as seguintes instruções. Utilizar `DUMP` para ver o resultado da transformação depois de cada passo.

    <table>
    <tr>
    <th>Declaração</th><th>O que faz</th>
    </tr>
    <tr>
    <td>FILTEREDLEVELS = níveis de filtro por LOGLEVEL não é nulo;</td><td>Remove linhas que contêm um valor nulo para o nível de registo e armazena os resultados para FILTEREDLEVELS.</td>
    </tr>
    <tr>
    <td>GROUPEDLEVELS = FILTEREDLEVELS grupo por LOGLEVEL;</td><td>Grupos de linhas por nível de registo e armazena os resultados para GROUPEDLEVELS.</td>
    </tr>
    <tr>
    <td>FREQUÊNCIA = foreach GROUPEDLEVELS gerar grupo como LOGLEVEL, contar (FILTEREDLEVELS. LOGLEVEL) como contar o número de;</td><td>Cria um novo conjunto de dados que contém a cada registo único valor nível e quantas vezes-ocorre. Este é armazenado na frequência</td>
    </tr>
    <tr>
    <td>RESULTADO = ordem frequência por CONTAGEM desc;</td><td>Os níveis de registo as encomendas por contagem (descendente) e armazena num resultado</td>
    </tr>
    </table>

6. Também pode guardar os resultados de uma transformação utilizando o `STORE` declaração. Por exemplo, o seguinte comando guarda a `RESULT` ao diretório **/example/data/pigout** no contentor de armazenamento predefinido para o seu cluster:

        STORE RESULT into 'wasbs:///example/data/pigout'

    > [AZURE.NOTE] Os dados são armazenados no directório em ficheiros com o nome **nnnnn peça**especificado. Se já existir diretório, receberá uma mensagem de erro.

7. Para sair da linha de comandos do roncador, introduza a seguinte instrução.

        QUIT;

###<a name="pig-latin-batch-files"></a>Ficheiros de batch porco Latim

Também pode utilizar o comando porco para executar Latim porco que estão contidas num ficheiro.

3. Depois de sair o pedido de roncador, abra o **Bloco de notas** e crie um novo ficheiro chamado **pigbatch.pig** no diretório **PIG_HOME %** .

4. Escreva ou cole as linhas seguintes no ficheiro **pigbatch.pig** e, em seguida, guardá-lo:

        LOGS = LOAD 'wasbs:///example/data/sample.log';
        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;

5. Utilize o seguinte procedimento para executar o ficheiro de **pigbatch.pig** utilizando o comando porco.

        pig %PIG_HOME%\pigbatch.pig

    Quando concluir o processo, deverá ver o resultado seguinte, o que deve ser igual a quando utilizado `DUMP RESULT;` nos passos anteriores:

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

##<a id="summary"></a>Resumo

Como pode ver, o comando porco permite-lhe executar operações de MapReduce de forma interativa ou executar tarefas porco Latim que estão armazenadas num ficheiro batch.

##<a id="nextsteps"></a>Próximos passos

Para obter informações gerais sobre porco no HDInsight:

* [Utilizar porco com Hadoop no HDInsight](hdinsight-use-pig.md)

Para obter informações sobre outras formas pode trabalhar com Hadoop no HDInsight:

* [Utilizar ramo com Hadoop no HDInsight](hdinsight-use-hive.md)

* [Utilizar MapReduce com Hadoop no HDInsight](hdinsight-use-mapreduce.md)
