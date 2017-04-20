<properties
   pageTitle="Utilizar Hadoop suínos com SSH num HDInsight cluster | Microsoft Azure"
   description="Obter informações sobre como ligar a um cluster baseado em Linux Hadoop com SSH e, em seguida, utilize o comando de suínos seja executada interactivamente demonstrações de porco Latim ou como uma tarefa batch."
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

#<a name="run-pig-jobs-on-a-linux-based-cluster-with-the-pig-command-ssh"></a>Executar tarefas de suíno num cluster com o comando de suínos (SSH) baseado em Linux

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Este documento irá percorrer o processo de ligação a um cluster baseado em Linux Azure HDInsight através da utilização segura da Shell (SSH), em seguida, utilizando o comando de suínos seja executada interactivamente, demonstrações de porco Latim ou como uma tarefa batch.

A linguagem de programação do porco Latim permite-lhe descrever as transformações são aplicadas aos dados de entrada para produzir o resultado pretendido.

> [AZURE.NOTE] Se já estiver familiarizado com a utilização de servidores de Hadoop baseado em Linux, mas for um novo HDInsight, consulte as [Sugestões de HDInsight baseado em Linux](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Pré-requisitos

Para concluir os passos descritos neste artigo, terá o seguinte.

* Um cluster baseado em Linux HDInsight (Hadoop em HDInsight).

* Um cliente SSH. Linux, Unix e Mac OS devem estar acompanhado de um cliente SSH. Os utilizadores do Windows tem de transferir um cliente, como [betumes](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a id="ssh"></a>Ligar com SSH

Estabelecer ligação com o nome de domínio totalmente qualificado (FQDN) do HDInsight cluster utilizando o comando SSH. O FQDN será o nome que deu ao cluster, em seguida, **. azurehdinsight.net**. Por exemplo, a seguinte estabeleceria ligação a um cluster com o nome **myhdinsight**.

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**Se forneceu uma chave de certificado para autenticação de SSH** quando criou o cluster de HDInsight, poderá ter de especificar a localização da chave privada no sistema cliente.

    ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Se fornecer uma palavra-passe para autenticação SSH** quando criou o cluster de HDInsight, terá de fornecer a palavra-passe quando lhe for pedido.

Para mais informações sobre como utilizar SSH com HDInsight, consulte [Utilizar SSH com baseado em Linux Hadoop no HDInsight do Linux, OS X e Unix](hdinsight-hadoop-linux-use-ssh-unix.md).

###<a name="putty-windows-based-clients"></a>Betumes (clientes baseados no Windows)

Windows não fornece um cliente SSH incorporado. Recomendamos que utilize **betumes**, que pode ser transferido do [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Para mais informações sobre como utilizar betumes, consulte [Utilizar SSH com baseado em Linux Hadoop no HDInsight do Windows ](hdinsight-hadoop-linux-use-ssh-windows.md).

##<a id="pig"></a>Utilize o comando de suíno

2. Uma vez ligado, inicie a interface de linha de comandos de suínos (CLI) utilizando o comando seguinte.

        pig

    Após o momento, deverá ver uma `grunt>` linha de comandos.

3. Introduza a instrução seguinte.

        LOGS = LOAD 'wasbs:///example/data/sample.log';

    Este comando carrega o conteúdo do ficheiro sample.log em registos. Pode ver o conteúdo do ficheiro, utilizando o seguinte.

        DUMP LOGS;

4. Em seguida, transforme os dados através da aplicação de uma expressão regular para extrair o nível de registo de cada registo, utilizando o seguinte.

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

    Pode utilizar a **informação** para visualizar os dados após a transformação. Neste caso, utilize `DUMP LEVELS;`.

5. Continue a aplicar transformações utilizando as instruções seguintes. Utilizar `DUMP` para ver o resultado da transformação após cada passo.

    <table>
    <tr>
    <th>Instrução</th><th>O que faz</th>
    </tr>
    <tr>
    <td>FILTEREDLEVELS = níveis de filtro por LOGLEVEL não for nulo;</td><td>Remove linhas que contêm um valor nulo para o nível de registo e armazena os resultados em FILTEREDLEVELS.</td>
    </tr>
    <tr>
    <td>GROUPEDLEVELS = FILTEREDLEVELS de grupo por LOGLEVEL;</td><td>Agrupa as linhas por nível de registo e armazena os resultados em GROUPEDLEVELS.</td>
    </tr>
    <tr>
    <td>FREQUÊNCIAS = foreach GROUPEDLEVELS gerar grupo como LOGLEVEL, CONTAGEM (FILTEREDLEVELS. LOGLEVEL) como contar;</td><td>Cria um novo conjunto de dados que contém cada registo único valor de nível e o número de vezes ocorre. Este é armazenado em frequências.</td>
    </tr>
    <tr>
    <td>RESULTADO = ordem frequências por CONTAGEM desc;</td><td>Os níveis de registo as encomendas por contagem (descendente) e armazena no resultado.</td>
    </tr>
    </table>

6. Também pode guardar os resultados de uma transformação utilizando a `STORE` instrução. Por exemplo, a seguinte guarda a `RESULT` para o directório **/example/data/pigout** no contentor de armazenamento predefinida para o cluster.

        STORE RESULT into 'wasbs:///example/data/pigout';

    > [AZURE.NOTE] Os dados são armazenados no directório especificado nos ficheiros referidos **nnnnn parte**. Se o directório já existir, receberá um erro.

7. Para sair da linha de comandos roncador, introduza a instrução seguinte.

        QUIT;

###<a name="pig-latin-batch-files"></a>Ficheiros de comandos do porco Latim

Também pode utilizar o comando de suínos para executar Latim suíno contidos num ficheiro.

3. Depois de sair da linha de comandos roncador, utilize o seguinte comando para o pipe STDIN num ficheiro chamado **pigbatch.pig**. Este ficheiro será criado no directório raiz para a conta que tiver iniciado sessão para a sessão SSH.

        cat > ~/pigbatch.pig

4. Escreva ou cole as seguintes linhas e, em seguida, utilize Ctrl + D quando terminar.

        LOGS = LOAD 'wasbs:///example/data/sample.log';
        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;

5. Utilize o seguinte para executar o ficheiro de **pigbatch.pig** , utilizando o comando de suínos.

        pig ~/pigbatch.pig

    Depois de termina a rotina, deverá consultar o seguinte resultado, o que deve ser a mesma que quando utilizado `DUMP RESULT;` nos passos anteriores.

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

##<a id="summary"></a>Resumo

Como pode ver, o comando de porco permite-lhe interactivamente executar operações de MapReduce através da utilização Latim de suínos, bem como executar instruções armazenadas num ficheiro batch.

##<a id="nextsteps"></a>Passos seguintes

Para obter informações gerais sobre porco no HDInsight.

* [Utilizar suínos com Hadoop em HDInsight](hdinsight-use-pig.md)

Para obter informações sobre outras formas pode trabalhar com Hadoop em HDInsight.

* [Utilizar o ramo de registo com Hadoop em HDInsight](hdinsight-use-hive.md)

* [Utilizar MapReduce com Hadoop em HDInsight](hdinsight-use-mapreduce.md)
