<properties
   pageTitle="Utilizar Hadoop Sqoop com laço no HDInsight | Microsoft Azure"
   description="Saiba como remotamente submeter tarefas de Sqoop para HDInsight com o laço."
   services="hdinsight"
   documentationCenter=""
   authors="mumian"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/21/2016"
   ms.author="jgao"/>

#<a name="run-sqoop-jobs-with-hadoop-in-hdinsight-with-curl"></a>Executar tarefas de Sqoop com Hadoop no HDInsight com laço

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Saiba como utilizar o laço para executar tarefas Sqoop num cluster Hadoop no HDInsight.

Laço é utilizado para demonstrar como pode interagir com HDInsight utilizando observou pedidos de HTTP para executar, monitorizar e obter os resultados de Sqoop tarefas. Isto funciona, utilizando o WebHCat REST API (anteriormente conhecido como Templeton) fornecido pelo seu cluster HDInsight.

> [AZURE.NOTE] Se já estiver familiarizado com a utilização de servidores baseado em Linux Hadoop, mas se estiver familiarizado com o HDInsight, consulte o artigo [informações sobre como utilizar HDInsight no Linux](hdinsight-hadoop-linux-information.md).

##<a name="prerequisites"></a>Pré-requisitos

Para concluir os passos neste artigo, terá o seguinte procedimento:

* Um Hadoop num cluster de HDInsight (Linux ou baseados no Windows)

* [Laço](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##<a name="submit-sqoop-jobs-by-using-curl"></a>Submeter Sqoop tarefas utilizando o laço

> [AZURE.NOTE] Quando utilizar Laço ou outras comunicações de resto com WebHCat, tem de autenticar os pedidos ao fornecer o nome de utilizador e palavra-passe para o administrador de cluster HDInsight. Também tem de utilizar o nome do cluster como parte do URI Uniform Resource Identifier () utilizado para enviar os pedidos para o servidor.
>
> Para os comandos nesta secção, substitua o **nome de utilizador** ao utilizador para autenticar ao cluster e substitua a **palavra-passe** a palavra-passe da conta de utilizador. Substitua o **nome de cluster** com o nome do seu cluster.
>
> REST API está protegida através de [autenticação básica](http://en.wikipedia.org/wiki/Basic_access_authentication). Sempre deve certificar pedidos utilizando HTTP seguro (HTTPS) para ajudar a garantir que as suas credenciais em segurança são enviadas para o servidor.

1. A partir de uma linha de comandos, utilize o seguinte comando para verificar se pode ligar ao seu cluster HDInsight:

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    Deverá recebe uma resposta semelhante ao seguinte:

        {"status":"ok","version":"v1"}

    Os parâmetros utilizados neste comando são os seguintes:

    * **-u** - o nome de utilizador e palavra-passe utilizadas para autenticar o pedido.
    * **-G** - indica que este é num pedido GET.

    Início do URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, serão os mesmos para todos os pedidos. O caminho, **/status**, indica que o pedido é devolvido um Estado de WebHCat (também conhecido como Templeton) para o servidor. 

2. Utilize o seguinte procedimento para submeter uma tarefa sqoop:


        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d command="export --connect jdbc:sqlserver://SQLDATABASESERVERNAME.database.windows.net;user=USERNAME@SQLDATABASESERVERNAME;password=PASSWORD;database=SQLDATABASENAME --table log4jlogs --export-dir /tutorials/usesqoop/data --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasbs:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/sqoop

    Os parâmetros utilizados neste comando são os seguintes:

    * **-d** - uma vez que `-G` não é utilizado o pedido predefinições para o método de mensagem. `-d`Especifica os valores de dados que são enviados com o pedido.

        * **user.name** - o utilizador que esteja a executar o comando.

        * **comando** - Sqoop o comando Executar.

        * **statusdir** - diretório que o estado para esta tarefa ser escrito.

    Este comando deve devolver um ID da tarefa que pode ser utilizado para verificar o estado da tarefa.

        {"id":"job_1415651640909_0026"}

3. Para verificar o estado da tarefa, utilize o seguinte comando. Substitua o valor devolvido no passo anterior **JOBID** . Por exemplo, se o valor de retorno foi `{"id":"job_1415651640909_0026"}`, em seguida, **JOBID** seria `job_1415651640909_0026`.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

    Se a tarefa for concluída, o estado será **bem sucedida**.

    > [AZURE.NOTE] Este pedido de Laço devolve um documento com notação de objeto JavaScript (JSON) com informações sobre a tarefa; jq é utilizado para recuperar apenas o valor de estado.

4. Assim que o estado da tarefa foi alterada para **com êxito**, pode obter os resultados da tarefa a partir do armazenamento de Blobs do Azure. O `statusdir` parâmetro transmitido com a consulta contém a localização do ficheiro de exportação; Neste caso, **wasbs: / / / exemplo/Laço**. Este endereço armazena o resultado da tarefa no diretório **exemplo/Laço** no contentor de armazenamento predefinido utilizado pelo seu cluster HDInsight.

    Pode da lista e transferir estes ficheiros utilizando o [Clip do Azure](../xplat-cli-install.md). Por exemplo, para ficheiros de lista no **exemplo/Laço**, utilize o seguinte comando:

        azure storage blob list <container-name> example/curl

    Para transferir um ficheiro, utilize o seguinte:

        azure storage blob download <container-name> <blob-name> <destination-file>

    > [AZURE.NOTE] Quer tem de especificar o nome da conta de armazenamento que contenha o blob utilizando o `-a` e `-k` parâmetros ou configurar a **AZURE\_armazenamento\_conta** e **AZURE\_armazenamento\_acesso\_chave** variáveis de ambiente. Consulte o artigo < um href = "data.md de carregamento de hdinsight" target = blank"para obter mais informações.

##<a name="limitations"></a>Limitações

* Em volume export - baseado em com Linux HDInsight, o conector de Sqoop utilizado para exportar dados para o Microsoft SQL Server ou base de dados do SQL Azure atualmente não suporta insere em volume.

* De lotes - com baseado em Linux HDInsight, ao utilizar o `-batch` mudar quando efetuar inserções, Sqoop irá efetuar várias insere em vez de lotes as operações de inserir.

##<a name="summary"></a>Resumo

Conforme demonstrado neste documento, pode utilizar um pedido de HTTP não processado para executar, monitorizar e ver os resultados de Sqoop tarefas no seu cluster HDInsight.

Para mais informações sobre a interface do resto utilizada neste artigo, consulte o <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">Guia Sqoop REST API</a>.

##<a name="next-steps"></a>Próximos passos

Para obter informações gerais sobre ramo com HDInsight:

* [Utilizar Sqoop com Hadoop no HDInsight](hdinsight-use-sqoop.md)

Para obter informações sobre outras formas pode trabalhar com Hadoop no HDInsight:

* [Utilizar ramo com Hadoop no HDInsight](hdinsight-use-hive.md)

* [Utilizar porco com Hadoop no HDInsight](hdinsight-use-pig.md)

* [Utilizar MapReduce com Hadoop no HDInsight](hdinsight-use-mapreduce.md)

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md




[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


