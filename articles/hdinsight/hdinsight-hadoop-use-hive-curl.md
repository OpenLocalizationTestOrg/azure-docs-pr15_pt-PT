<properties
   pageTitle="Utilizar ramo de Hadoop com laço no HDInsight | Microsoft Azure"
   description="Saiba como remotamente submeter tarefas de porco para HDInsight com o laço."
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
   ms.date="09/07/2016"
   ms.author="larryfr"/>

#<a name="run-hive-queries-with-hadoop-in-hdinsight-with-curl"></a>Executar consultas de ramo com Hadoop no HDInsight com laço

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Este documento, vai aprender a utilizar o laço para executar consultas de ramo num Hadoop num cluster de Azure HDInsight.

Laço é utilizado para demonstrar como pode interagir com HDInsight utilizando observou pedidos de HTTP para executar, monitorizar e obter os resultados de consultas de ramo. Isto funciona, utilizando o WebHCat REST API (anteriormente conhecido como Templeton) fornecido pelo seu cluster HDInsight.

> [AZURE.NOTE] Se já estiver familiarizado com a utilização de servidores baseado em Linux Hadoop, mas se estiver familiarizado com o HDInsight, consulte o artigo [o que precisa de saber sobre Hadoop no baseado em Linux HDInsight](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Pré-requisitos

Para concluir os passos neste artigo, terá o seguinte procedimento:

* Um Hadoop num cluster de HDInsight (Linux ou baseados no Windows)

* [Laço](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##<a id="curl"></a>Executar consultas de ramo utilizando o laço

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

    Início do URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, serão os mesmos para todos os pedidos. O caminho, **/status**, indica que o pedido é devolvido um Estado de WebHCat (também conhecido como Templeton) para o servidor. Também pode pedir a versão do ramo utilizando o seguinte comando:

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive

    Isto deve devolver uma resposta semelhante ao seguinte:

        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}

2. Utilize o seguinte para criar uma nova tabela denominada **log4jLogs**:

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="set+hive.execution.engine=tez;DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'wasbs:///example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="wasbs:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive

    Os parâmetros utilizados neste comando são os seguintes:

    * **-d** - uma vez que `-G` não é utilizado o pedido predefinições para o método de mensagem. `-d`Especifica os valores de dados que são enviados com o pedido.

        * **user.name** - o utilizador que esteja a executar o comando.

        * **Executar** - HiveQL de instruções para executar.

        * **statusdir** - diretório que o estado para esta tarefa ser escrito.

    Estas instruções executar as seguintes ações:

    * **LARGAR tabela** - elimina a tabela e o ficheiro de dados se a tabela já existe.

    * **Criar tabela externa** - cria uma nova tabela «externa» no ramo. Tabelas externas armazenam apenas a definição da tabela Hive. Os dados falta na localização original.

        > [AZURE.NOTE] Tabelas externas devem ser utilizadas quando pretende os dados subjacentes ser atualizados por uma fonte externa, tal como um processo de carregamento de dados automatizado, ou por outra MapReduce operação, mas pretende sempre ramo de consultas para utilizar os dados mais recentes.
        >
        > Largar uma tabela externa é que **não** eliminar os dados, apenas a definição da tabela.

    * **Formato de linha** - indica Hive a forma como os dados estão formatados. Neste caso, os campos no registo de cada são separados por um espaço.

    * **Localização de TEXTFILE como ARMAZENADOS** - indica Hive onde estão os dados armazenados (o directório de exemplo de dados) e que a mesma está armazenada como texto.

    * **SELECIONE** - seleciona uma contagem de todas as linhas onde coluna **t4** contém o valor **[erro]**. Isto deve devolver um valor de **3** como existem três linhas que contêm este valor.

    > [AZURE.NOTE] Repare que os espaços entre HiveQL declarações são substituídos pela `+` caráter quando utilizado com o laço. Valores entre aspas que contêm um espaço, tal como o delimitador, não devem ser substituídos por `+`.

    * **INPUT__FILE__NAME como '% 25.log'** - presente limita a pesquisa a apenas utilizar ficheiros que terminem em. log. Se não estiver presente, ramo irá tentar procurar todos os ficheiros neste diretório e os seus subdiretórios, incluindo os ficheiros que não correspondem ao esquema de coluna definido para esta tabela.

    > [AZURE.NOTE] Nota que 25% é o URL codificada formulário de %, pelo que é a condição real `like '%.log'`. A % tem de ser URL codificada, tal como é tratado como um caráter especial em URLs.

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

6. Utilize as seguintes instruções para criar uma nova tabela «interna» denominada **errorLogs**:

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="set+hive.execution.engine=tez;CREATE+TABLE+IF+NOT+EXISTS+errorLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+STORED+AS+ORC;INSERT+OVERWRITE+TABLE+errorLogs+SELECT+t1,t2,t3,t4,t5,t6,t7+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log';SELECT+*+from+errorLogs;" -d statusdir="wasbs:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive

    Estas instruções executar as seguintes ações:

    * **Criar a tabela se não existe** - cria uma tabela, se ainda não existir. Uma vez que não é utilizada a palavra-chave **externa** , este é uma tabela interna, que está armazenada no armazém de ramo de dados e é gerida completamente por ramo.

        > [AZURE.NOTE] Ao contrário de tabelas externas, largar uma tabela interna irá eliminar dados subjacentes.

    * **ARMAZENADOS ORC como** - armazena os dados no formato de otimizado linha colunar (ORC). Este é um formato de altamente optimizado e eficaz para armazenar dados ramo.
    * SUBSTITUIR **INSERT... SELECIONE** - seleciona linhas da tabela **log4jLogs** que contêm **[erro]**, em seguida, insere os dados na tabela **errorLogs** .
    * **SELECIONE** - seleciona todas as linhas da nova tabela **errorLogs** .

7. Utilize o ID da tarefa devolvido para verificar o estado da tarefa. Quando foi bem sucedido, utilize Azure clip para Mac, Linux e o Windows, tal como descrito anteriormente para transferir e ver os resultados. O resultado deve conter três linhas, as quais contêm **[erro]**.


##<a id="summary"></a>Resumo

Conforme demonstrado neste documento, pode utilizar um pedido de HTTP não processado para executar, monitorizar e ver os resultados de ramo de tarefas no seu cluster HDInsight.

Para mais informações sobre a interface do resto utilizada neste artigo, consulte a <a href="https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference" target="_blank">Referência de WebHCat</a>.

##<a id="nextsteps"></a>Próximos passos

Para obter informações gerais sobre ramo com HDInsight:

* [Utilizar ramo com Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outras formas pode trabalhar com Hadoop no HDInsight:

* [Utilizar porco com Hadoop no HDInsight](hdinsight-use-pig.md)

* [Utilizar MapReduce com Hadoop no HDInsight](hdinsight-use-mapreduce.md)

Se estiver a utilizar Tez com ramo, consulte os seguintes documentos para as informações de depuração:

* [Utilizar a IU Tez no HDInsight baseados no Windows](hdinsight-debug-tez-ui.md)

* [Utilizar a vista de Ambari Tez no baseado em Linux HDInsight](hdinsight-debug-ambari-tez-view.md)

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


