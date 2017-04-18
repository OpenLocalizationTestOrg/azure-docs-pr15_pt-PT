<properties
   pageTitle="Utilizar MapReduce e Laço com Hadoop no HDInsight | Microsoft Azure"
   description="Saiba como remotamente executar tarefas de MapReduce com Hadoop no HDInsight com o laço."
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
   ms.date="09/27/2016"
   ms.author="larryfr"/>

#<a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-curl"></a>Executar tarefas MapReduce com Hadoop no HDInsight com o laço

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Este documento, vai aprender a utilizar o laço para efetuar tarefas MapReduce um Hadoop num cluster de HDInsight.

Laço é utilizado para demonstrar como pode interagir com HDInsight utilizando observou pedidos de HTTP para executar tarefas MapReduce. Isto funciona, utilizando o WebHCat REST API (anteriormente conhecido como Templeton) fornecido pelo seu cluster HDInsight.

> [AZURE.NOTE] Se já estiver familiarizado com a utilização de servidores baseado em Linux Hadoop, mas estiver familiarizado HDInsight, consulte o artigo [o que precisa de saber sobre baseado em Linux Hadoop no HDInsight](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Pré-requisitos

Para concluir os passos neste artigo, terá o seguinte procedimento:

* Um Hadoop num cluster de HDInsight (Linux ou baseados no Windows)

* [Laço](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##<a id="curl"></a>Executar tarefas de MapReduce utilizando o laço

> [AZURE.NOTE] Quando utiliza o laço ou outras comunicações de resto com WebHCat, tem de autenticar os pedidos ao fornecer HDInsight cluster administrador nome de utilizador e palavra-passe. Também tem de utilizar o nome do cluster como parte do URI que é utilizado para enviar os pedidos para o servidor.
>
> Para comandos nesta secção, substitua o **nome de utilizador** com o utilizador para autenticar ao cluster e a **palavra-passe** com a palavra-passe da conta de utilizador. Substitua o **nome de cluster** com o nome do seu cluster.
>
> REST API está protegida utilizando a [autenticação de acesso básico](http://en.wikipedia.org/wiki/Basic_access_authentication). Sempre deve certificar pedidos utilizando HTTPS para se certificar de que as suas credenciais em segurança são enviadas para o servidor.

1. A partir de uma linha de comandos, utilize o seguinte comando para verificar se pode ligar ao seu cluster HDInsight:

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    Deverá recebe uma resposta semelhante ao seguinte:

        {"status":"ok","version":"v1"}

    Os parâmetros utilizados neste comando são os seguintes:

    * **-u**: indica o nome de utilizador e palavra-passe utilizadas para autenticar o pedido
    * **-G**: indica que este é num pedido GET

    Início da URI, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, é a mesma para todos os pedidos.

2. Para submeter uma tarefa de MapReduce, utilize o seguinte comando:

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d jar=wasbs:///example/jars/hadoop-mapreduce-examples.jar -d class=wordcount -d arg=wasbs:///example/data/gutenberg/davinci.txt -d arg=wasbs:///example/data/CurlOut https://CLUSTERNAME.azurehdinsight.net/templeton/v1/mapreduce/jar

    O fim do URI (/ mapreduce/para caixa) indica WebHCat que este pedido irá iniciar uma tarefa de MapReduce a partir de uma classe num ficheiro para caixa. Os parâmetros utilizados neste comando são os seguintes:

    * **-d**: `-G` não é utilizada, para que o pedido predefinições para o método de mensagem. `-d`Especifica os valores de dados que são enviados com o pedido.

        * **User.Name**: O utilizador que está a executar o comando
        * **para caixa**: executou a localização do ficheiro para caixa que contém a classe de estar
        * **classe**: A classe que contém a lógica MapReduce
        * **argumento**: os argumentos a transmitir à tarefa MapReduce; Neste caso, o ficheiro de texto de entrada e o diretório que sejam utilizados para a saída

    Este comando deve devolver um ID da tarefa que pode ser utilizado para verificar o estado da tarefa:

        {"id":"job_1415651640909_0026"}

3. Para verificar o estado da tarefa, utilize o seguinte comando. Substitua o **JOBID** o valor devolvido no passo anterior. Por exemplo, se o valor de retorno foi `{"id":"job_1415651640909_0026"}`, em seguida, o JOBID seria `job_1415651640909_0026`.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

    Se a tarefa estiver concluída, o estado vai ser "teve êxito".

    > [AZURE.NOTE] Este pedido de Laço devolve um documento JSON com informações sobre a tarefa; jq é utilizado para recuperar apenas o valor de estado.

4. Quando o estado da tarefa foi alterada para **com êxito**, pode obter os resultados da tarefa a partir do armazenamento de Blobs do Azure. O `statusdir` parâmetro que lhe é transmitido com a consulta contém a localização do ficheiro de exportação; Neste caso, **wasbs: / / / exemplo/Laço**. Este endereço armazena o resultado da tarefa no diretório **exemplo/Laço** no contentor de armazenamento predefinido utilizado pelo seu cluster HDInsight.

Pode da lista e transferir estes ficheiros utilizando o [Clip do Azure](../xplat-cli-install.md). Por exemplo, para ficheiros de lista no **exemplo/Laço**, utilize o seguinte comando:

    azure storage blob list <container-name> example/curl

Para transferir um ficheiro, utilize o seguinte:

    azure storage blob download <container-name> <blob-name> <destination-file>

> [AZURE.NOTE] Tem de especificar o nome da conta de armazenamento que contenha o blob utilizando o `-a` e `-k` parâmetros ou configurar a **AZURE\_armazenamento\_conta** e **AZURE\_armazenamento\_acesso\_chave** variáveis de ambiente. Veja [como carregar dados ao HDInsight](hdinsight-upload-data.md) para obter mais informações.

##<a id="summary"></a>Resumo

Conforme demonstrado neste documento, pode utilizar o pedido de HTTP não processado para executar, monitorizar e ver os resultados de ramo de tarefas no seu cluster HDInsight.

Para mais informações sobre a interface do resto utilizados neste artigo, consulte a [Referência de WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

##<a id="nextsteps"></a>Próximos passos

Para obter informações gerais acerca de tarefas de MapReduce no HDInsight:

* [Utilizar MapReduce com Hadoop no HDInsight](hdinsight-use-mapreduce.md)

Para obter informações sobre outras formas pode trabalhar com Hadoop no HDInsight:

* [Utilizar ramo com Hadoop no HDInsight](hdinsight-use-hive.md)

* [Utilizar porco com Hadoop no HDInsight](hdinsight-use-pig.md)
