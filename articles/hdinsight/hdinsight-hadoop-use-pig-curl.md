<properties
   pageTitle="Utilizar Hadoop porco com laço no HDInsight | Microsoft Azure"
   description="Saiba como utilizar o laço para executar tarefas porco Latim num cluster Hadoop no Azure HDInsight."
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
   ms.date="08/23/2016"
   ms.author="larryfr"/>

#<a name="run-pig-jobs-with-hadoop-on-hdinsight-by-using-curl"></a>Executar tarefas porco com Hadoop HDInsight utilizando o laço

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Este documento, vai aprender a utilizar o laço para executar tarefas porco Latim num cluster Azure HDInsight. A linguagem de programação porco Latim permite-lhe descrever as transformações que são aplicadas a dados de entrada para produzir o resultado pretendido.

Laço é utilizado para demonstrar como pode interagir com HDInsight utilizando observou pedidos de HTTP para executar, monitorizar e obter os resultados das tarefas porco. Isto funciona, utilizando o WebHCat API REST (anteriormente conhecido como Templeton) que é fornecido pelo seu cluster HDInsight.

> [AZURE.NOTE] Se já estiver familiarizado com a utilização de servidores baseado em Linux Hadoop, mas se estiver familiarizado com o HDInsight, consulte o artigo [Sugestões de HDInsight baseado em Linux](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Pré-requisitos

Para concluir os passos neste artigo, terá o seguinte procedimento:

* Um cluster de Azure HDInsight (Hadoop no HDInsight) (baseado em Linux ou baseados no Windows)

* [Laço](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##<a id="curl"></a>Executar tarefas porco utilizando o laço

> [AZURE.NOTE] Quando utilizar Laço ou outras comunicações de resto com WebHCat, tem de autenticar os pedidos ao fornecer o nome de utilizador do administrador e a palavra-passe para o cluster HDInsight. Também tem de utilizar o nome do cluster como parte do URI Uniform Resource Identifier () que é utilizado para enviar os pedidos para o servidor.
>
> Para os comandos nesta secção, substitua o **nome de utilizador** ao utilizador para autenticar ao cluster e substitua a **palavra-passe** a palavra-passe da conta de utilizador. Substitua o **nome de cluster** com o nome do seu cluster.
>
> REST API está protegida através de [autenticação de acesso básico](http://en.wikipedia.org/wiki/Basic_access_authentication). Sempre deve certificar pedidos utilizando HTTP seguro (HTTPS) para ajudar a garantir que as suas credenciais em segurança são enviadas para o servidor.

1. A partir de uma linha de comandos, utilize o seguinte comando para verificar se pode ligar ao seu cluster HDInsight:

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    Deverá recebe uma resposta semelhante ao seguinte:

        {"status":"ok","version":"v1"}

    Os parâmetros utilizados neste comando são os seguintes:

    * **-u**: O nome de utilizador e palavra-passe utilizadas para autenticar o pedido
    * **-G**: indica que este é num pedido GET

    Início do URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, serão os mesmos para todos os pedidos. O caminho, **/status**, indica que o pedido é devolvido o estado de WebHCat (também conhecido como Templeton) para o servidor.

2. Utilize o seguinte código para submeter uma tarefa porco Latim para cluster:

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="LOGS=LOAD+'wasbs:///example/data/sample.log';LEVELS=foreach+LOGS+generate+REGEX_EXTRACT($0,'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)',1)+as+LOGLEVEL;FILTEREDLEVELS=FILTER+LEVELS+by+LOGLEVEL+is+not+null;GROUPEDLEVELS=GROUP+FILTEREDLEVELS+by+LOGLEVEL;FREQUENCIES=foreach+GROUPEDLEVELS+generate+group+as+LOGLEVEL,COUNT(FILTEREDLEVELS.LOGLEVEL)+as+count;RESULT=order+FREQUENCIES+by+COUNT+desc;DUMP+RESULT;" -d statusdir="wasbs:///example/pigcurl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/pig

    Os parâmetros utilizados neste comando são os seguintes:

    * **-d**: uma vez que `-G` não é utilizado o pedido predefinições para o método de mensagem. `-d`Especifica os valores de dados que são enviados com o pedido.

        * **User.Name**: O utilizador que está a executar o comando
        * **Executar**: declarações de porco Latim executar
        * **statusdir**: diretório que o estado para esta tarefa ser escrito

    > [AZURE.NOTE] Repare que os espaços no Latim porco declarações são substituídos pela `+` caráter quando utilizado com o laço.

    Este comando deve devolver um ID da tarefa que pode ser utilizado para verificar o estado da tarefa, por exemplo:

        {"id":"job_1415651640909_0026"}

3. Para verificar o estado da tarefa, utilize o seguinte comando. Substitua o valor devolvido no passo anterior **JOBID** . Por exemplo, se o valor de retorno foi `{"id":"job_1415651640909_0026"}`, em seguida, **JOBID** seria `job_1415651640909_0026`.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

    Se a tarefa for concluída, o estado será **bem sucedida**.

    > [AZURE.NOTE] Este pedido de Laço devolve um documento com notação de objeto JavaScript (JSON) com informações sobre a tarefa e jq é utilizada para obter o valor de estado.

##<a id="results"></a>Ver resultados

Quando o estado da tarefa foi alterada para **com êxito**, pode obter os resultados da tarefa a partir do armazenamento de Blobs do Azure. O `statusdir` parâmetro transmitido com a consulta contém a localização do ficheiro de exportação; Neste caso, **wasbs: / / / exemplo/pigcurl**. Este endereço armazena o resultado da tarefa no diretório **exemplo/pigcurl** no contentor de armazenamento predefinido utilizado pelo seu cluster HDInsight.

Pode da lista e transferir estes ficheiros utilizando o [Clip do Azure](../xplat-cli-install.md). Por exemplo, para ficheiros de lista no **exemplo/pigcurl**, utilize o seguinte comando:

    azure storage blob list <container-name> example/pigcurl

Para transferir um ficheiro, utilize o seguinte:

    azure storage blob download <container-name> <blob-name> <destination-file>

> [AZURE.NOTE] Tem de especificar o nome da conta de armazenamento que contenha o blob utilizando o `-a` e `-k` parâmetros ou configurar a **AZURE\_armazenamento\_conta** e **AZURE\_armazenamento\_acesso\_chave** variáveis de ambiente.

##<a id="summary"></a>Resumo

Conforme demonstrado neste documento, pode utilizar um pedido de HTTP não processado para executar, monitorizar e ver os resultados das tarefas porco no seu cluster HDInsight.

Para mais informações sobre a interface do resto utilizada neste artigo, consulte a [Referência de WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

##<a id="nextsteps"></a>Próximos passos

Para obter informações gerais sobre porco no HDInsight:

* [Utilizar porco com Hadoop no HDInsight](hdinsight-use-pig.md)

Para obter informações sobre outras formas pode trabalhar com Hadoop no HDInsight:

* [Utilizar ramo com Hadoop no HDInsight](hdinsight-use-hive.md)

* [Utilizar MapReduce com Hadoop no HDInsight](hdinsight-use-mapreduce.md)
