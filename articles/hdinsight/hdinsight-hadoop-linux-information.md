<properties
   pageTitle="Sugestões para utilizar o Hadoop baseado em Linux HDInsight | Microsoft Azure"
   description="Obter sugestões de implementação para utilizar o clusters baseado em Linux HDInsight (Hadoop) com um ambiente familiar do Linux executar na nuvem Azure."
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
   ms.date="09/13/2016"
   ms.author="larryfr"/>

# <a name="information-about-using-hdinsight-on-linux"></a>Informações sobre como utilizar HDInsight Linux

Baseado em Linux clusters de Azure HDInsight fornecem Hadoop num ambiente Linux familiar, a ser executada em nuvem Azure. Para a maioria dos elementos, deverá funcionar exatamente como qualquer outra instalação Hadoop no Linux. Este documento chamadas saída as diferenças específicas que deve ter em mente.

##<a name="prerequisites"></a>Pré-requisitos

Muitos dos passos neste documento utilizam os seguintes utilitários, poderão ter de ser instalada no seu sistema.

* [cURL](https://curl.haxx.se/) - utilizado para comunicar com serviços baseados na web
* [jq](https://stedolan.github.io/jq/) - utilizado para analisar JSON documentos
* [Clip do azure](../xplat-cli-install.md) - utilizado para gerir serviços Azure remotamente

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell-and-cli.md)] 

## <a name="domain-names"></a>Nomes de domínio

É o nome de domínio completamente qualificado (FQDN) a utilizar quando se liga ao cluster da internet ** &lt;nome de cluster >. azurehdinsight.net** ou (para SSH) ** &lt;nome de cluster-ssh >. azurehdinsight.net**.

Internamente, cada nó no cluster tem um nome que é atribuído durante a configuração do cluster. Para localizar os nomes das cluster, pode visitar a página de __anfitriões__ na IU do Web de Ambari ou utilize o seguinte para devolver uma lista de anfitriões de Ambari REST API:

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

Substitua a palavra-passe da conta de administrador e __nome de cluster__ com o nome do seu cluster de __palavra-passe__ . Isto irá devolver um documento JSON que contém uma lista de anfitriões no cluster, em seguida, obtém jq a `host_name` valor do elemento para cada anfitrião no cluster.

Se precisar de localizar o nome do nó para um serviço específico, pode consultar Ambari para esse componente. Por exemplo, para localizar os anfitriões para o nó do nome HDFS, utilize o seguinte.

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

Isto devolve um documento JSON descrição do serviço e, em seguida, jq obtém apenas a `host_name` valor para os anfitriões.

## <a name="remote-access-to-services"></a>Acesso remoto aos serviços

* **Ambari (web)** - https://&lt;nome de cluster >. azurehdinsight.net

    Autenticar com o utilizador do administrador de cluster e a palavra-passe e, em seguida, inicie sessão no Ambari. Esta opção utiliza também cluster administrador utilizador e palavra-passe.

    Autenticação é texto simples - utilizar sempre HTTPS para ajudar a garantir que a ligação é segura.

    > [AZURE.IMPORTANT] Enquanto está acessível Ambari para o seu cluster diretamente através da Internet, algumas funcionalidades baseia-se sobre como aceder a nós pelo nome de domínio interno utilizado pelo cluster. Uma vez que este é um nome de domínio interno e não pública, vai receber erros "servidor não encontrado" quando tentarem aceder ao algumas funcionalidades através da Internet.
    >
    > Para utilizar a funcionalidade completa do web Ambari IU, utilize um túnel SSH para o tráfego para o nó do cabeça cluster proxy na web. Consulte o artigo [Utilizar SSH túnel para aceder ao web Ambari IU, ResourceManager, JobHistory, NameNode, Oozie e outros web da IU](hdinsight-linux-ambari-ssh-tunnel.md)

* **Ambari (REST)** - https://&lt;nome de cluster >.azurehdinsight.net/ambari

    > [AZURE.NOTE] Autenticar com o utilizador do administrador de cluster e a palavra-passe.
    >
    > Autenticação é texto simples - utilizar sempre HTTPS para ajudar a garantir que a ligação é segura.

* **WebHCat (Templeton)** - https://&lt;nome de cluster >.azurehdinsight.net/templeton

    > [AZURE.NOTE] Autenticar com o utilizador do administrador de cluster e a palavra-passe.
    >
    > Autenticação é texto simples - utilizar sempre HTTPS para ajudar a garantir que a ligação é segura.

* **SSH** - &lt;nome de cluster >-ssh.azurehdinsight.net na porta 22 ou 23. Porta 22 é utilizada para ligar para o headnode principal, enquanto 23 é utilizado para ligar a secundária. Para mais informações sobre os nós de cabeça, consulte o artigo [disponibilidade e fiabilidade dos clusters de Hadoop HDInsight](hdinsight-high-availability-linux.md).

    > [AZURE.NOTE] Só pode aceder os nós de cabeça cluster através de SSH a partir de um computador cliente. Quando estiver ligado, em seguida, pode aceder aos nós de trabalho utilizando SSH a partir de um headnode.

## <a name="file-locations"></a>Localizações dos ficheiros

Ficheiros relacionados com Hadoop podem ser encontrados em nós de cluster na `/usr/hdp`. Este directório contém os seus subdiretórios seguintes:

* __2.2.4.9-1__: este diretório chama-se para a versão da plataforma de dados Hortonworks utilizada pelo HDInsight, para que o número no seu cluster poderão ser diferente daquele listado aqui.
* __atual__: este diretório contém ligações para directórios em diretório __2.2.4.9-1__ e existe para que não tenha que escrever um número de versão (que podem ser alterados,) sempre que pretende aceder a um ficheiro.

Dados de exemplo e ficheiros para caixa podem ser encontrados no armazenamento distribuído ficheiro Hadoop sistema (HDFS) ou BLOBs do Azure ' / exemplo ' ou ' wasbs: / / / exemplo '.

## <a name="hdfs-azure-blob-storage-and-storage-best-practices"></a>HDFS, armazenamento de Blobs do Azure e as melhores práticas de armazenamento

Na maioria dos Hadoop distribuições, HDFS é suportado pelo armazenamento local nos computadores no cluster. Enquanto este estiver eficaz, pode ser dispendioso para obter uma solução baseada na nuvem onde lhe é cobrada à hora ou por minuto para recursos de cluster.

HDInsight utiliza armazenamento de Blobs do Azure como o arquivo predefinida, que fornece os seguintes benefícios:

* Armazenamento a longo prazo tarifa reduzido

* Acessibilidade de serviços externos, como sites públicos, utilitários de carregamento/transferência do ficheiro, vários idiomas SDK e os browsers

Uma vez que é o arquivo predefinida para HDInsight, normalmente não tem de fazer nada para utilizá-la. Por exemplo, o seguinte comando irá listar ficheiros na pasta **/example/data** , que está armazenado no armazenamento de Blobs do Azure:

    hdfs dfs -ls /example/data

Alguns comandos podem pedir-lhe especificar que estiver a utilizar o armazenamento de Blobs. Para estas, pode atribuir um prefixo o comando com **wasb: / /**, ou **wasbs: / /**.

HDInsight também permite-lhe associar várias contas de armazenamento de BLOBs um cluster. Para aceder a dados numa conta de armazenamento de BLOBs não predefinida, pode utilizar o formato **wasbs: / /&lt;container-name>@&lt;nome da conta >.blob.core.windows.net/**. Por exemplo, o seguinte procedimento irá listar o conteúdo do directório **/example/data** para o contentor especificado e a conta de armazenamento de BLOBs:

    hdfs dfs -ls wasbs://mycontainer@mystorage.blob.core.windows.net/example/data

### <a name="what-blob-storage-is-the-cluster-using"></a>Armazenamento de Blobs do que é o cluster a utilizar?

Durante a criação de cluster, que selecionou para a utilizar uma conta de armazenamento do Windows Azure existente e contentor, ou criar um novo. Em seguida, provavelmente Esqueceu-se acerca do mesmo. Pode encontrar a conta de armazenamento predefinida e contentor ao utilizar a API do resto Ambari.

1. Utilize o seguinte comando para obter informações de configuração de HDFS utilizando laço e filtragem dos mesmos utilizando [jq](https://stedolan.github.io/jq/):

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
    
    > [AZURE.NOTE] Isto irá devolver a configuração da primeira aplicada ao servidor (`service_config_version=1`,) que irá conter esta informação. Se estiver a obter um valor que terem sido modificado após a criação de cluster, terá das versões de configuração da lista e obter a mais recente.

    Isto irá devolver um valor semelhante ao seguinte, onde __contentor__ é o contentor predefinido e __ACCOUNTNAME__ é o nome da conta de armazenamento do Azure:

        wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net

1. Obter o grupo de recursos para a conta de armazenamento, utilize o [Clip do Azure](../xplat-cli-install.md). No seguinte comando, substitua __ACCOUNTNAME__ com o nome da conta de armazenamento obtido a partir do Ambari:

        azure storage account list --json | jq '.[] | select(.name=="ACCOUNTNAME").resourceGroup'
    
    Isto irá devolver o nome do grupo de recursos para a conta.
    
    > [AZURE.NOTE] Se nada devolvido por este comando, terá de alterar o clip do Azure para o modo de Gestor de recursos do Azure e volte a executar o comando. Para mudar para modo de Gestor de recursos do Azure, utilize o seguinte comando.
    >
    > `azure config mode arm`
    
2. Obter a chave da conta de armazenamento. Substitua o __nome de grupo__ com o grupo de recursos a partir do passo anterior. Substitua __ACCOUNTNAME__ com o nome da conta de armazenamento:

        azure storage account keys list -g GROUPNAME ACCOUNTNAME --json | jq '.[0].value'

    Isto irá devolver a chave primária da conta.

Também pode encontrar as informações de armazenamento utilizando o Portal do Azure:

1. No [Portal do Azure](https://portal.azure.com/), selecione o seu cluster HDInsight.

2. A partir da secção __Essentials__ , selecione __todas as definições__.

3. Em __Definições__, selecione __As teclas de armazenamento do Azure__.

4. __Teclas de armazenamento do Azure__, selecione uma das contas armazenamento listadas. Isto vai mostrar informações sobre a conta de armazenamento.

5. Selecione o ícone de chave. Isto vai Mostrar teclas para esta conta de armazenamento.

### <a name="how-do-i-access-blob-storage"></a>Como posso aceder ao armazenamento Blob do?

De através do comando Hadoop do cluster, existem várias formas de aceder ao blobs:

* [Clip do Azure para Mac, Linux e Windows](../xplat-cli-install.md): interface da linha de comandos comandos para trabalhar com o Azure. Após a instalação, utilize o `azure storage` comando para obter ajuda sobre a utilização do armazenamento, ou `azure blob` para comandos específicos de Blobs.

* [blobxfer.PY](https://github.com/Azure/azure-batch-samples/tree/master/Python/Storage): uma python script para trabalhar com blobs no armazenamento do Windows Azure.

* Uma variedade de SDK:

    * [Java](https://github.com/Azure/azure-sdk-for-java)

    * [NODE.js](https://github.com/Azure/azure-sdk-for-node)

    * [PHP](https://github.com/Azure/azure-sdk-for-php)

    * [Python](https://github.com/Azure/azure-sdk-for-python)

    * [Rubi](https://github.com/Azure/azure-sdk-for-ruby)

    * [.NET](https://github.com/Azure/azure-sdk-for-net)

* [Armazenamento REST API](https://msdn.microsoft.com/library/azure/dd135733.aspx)

##<a name="scaling"></a>Cluster de dimensionamento

Cluster dimensionamento funcionalidade permite-lhe alterar o número de nós de dados utilizada por um cluster de que está em execução no Azure HDInsight sem ter de eliminar e voltar a criar o cluster.

Pode realizar operações dimensionamento enquanto outras tarefas ou processos estiverem em execução num cluster.

Os tipos de cluster diferentes são afetados por dimensionamento da seguinte forma:

* __Hadoop__: quando dimensionamento para baixo o número de nós num cluster, alguns dos serviços no cluster reiniciados. Isto pode causar trabalhos de começar a trabalhar ou pendente falha após a conclusão da operação de dimensionamento. Pode submeter novamente as tarefas de uma vez concluída a operação.

* __HBase__: servidores regionais são automaticamente com balanceamento de dentro de alguns minutos após a conclusão da operação de dimensionamento. Para equilibrar manualmente os servidores regionais, utilize os seguintes passos:

    1. Ligar ao cluster HDInsight utilizando SSH. Para obter mais informações sobre como utilizar SSH com HDInsight, consulte um dos seguintes documentos:

        * [Utilizar SSH com HDInsight do Linux, Unix e Mac OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

        * [Utilizar SSH com HDInsight a partir do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

    1. Utilize o seguinte para começar a shell de HBase:

            hbase shell

    2. Assim que a shell de HBase tem carregada, utilize o seguinte para equilibrar manualmente os servidores regionais:

            balancer

* __Tempestade__: deve redistribuir qualquer topologias tempestade em execução após ter uma operação de dimensionamento foi efetuada. Esta opção permite-a topologia de reajustar definições de paralelismo com base no novo número de nós no cluster. Para redistribuir topologias em execução, utilize uma das seguintes opções:

    * __SSH__: ligar ao servidor e utilize o seguinte comando para redistribuir uma topologia de:

            storm rebalance TOPOLOGYNAME

        Também pode especificar parâmetros para substituir as sugestões de paralelismo originalmente fornecidas da topologia. Por exemplo, `storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` irá reconfigurar a topologia de 5 processos de trabalho, 3 testamenteiros para o componente de vareta azul e 10 testamenteiros para o componente de amarelo raio.

    * __IU tempestade__: Utilize os passos seguintes para redistribuir uma topologia de utilizar a IU tempestade.

        1. Abra __https://CLUSTERNAME.azurehdinsight.net/stormui__ no seu browser, onde o nome de cluster é o nome do seu cluster tempestade. Se lhe for pedido, introduza o nome de administrador (admin) HDInsight cluster e a palavra-passe que especificou quando criar o cluster.

        3. Selecione a topologia de que pretender redistribuir, em seguida, selecione o botão __redistribuir__ . Introduza o atraso antes de é efetuada a operação de rebalance.

Para obter informações específicas sobre dimensionamento seu cluster HDInsight, consulte:

* [Gerir clusters de Hadoop HDInsight utilizando o Portal do Azure](hdinsight-administer-use-portal-linux.md#scaling)

* [Gerir clusters de Hadoop HDinsight utilizando o PowerShell do Azure](hdinsight-administer-use-command-line.md#scaling)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>Como instalar matiz (ou outros componentes Hadoop)?

HDInsight é um serviço gerido, o que significa que nós num cluster podem ser e de reprovisioned automaticamente ao Azure se é detetado um problema. Por esta razão, recomenda-não se instalar manualmente o coisas diretamente em nós de cluster. Em alternativa, utilize [HDInsight Script ações](hdinsight-hadoop-customize-cluster.md) quando precisar de instalar o seguinte:

* Um serviço ou o web site como motores ou matiz.
* Um componente que requer alterações da configuração em vários nós no cluster. Por exemplo, uma variável de ambiente necessários, criação de um registo do diretório ou criação de um ficheiro de configuração.

Acções de script estão festa scripts que estão executou durante o aprovisionamento de cluster e podem ser utilizadas para instalar e configurar componentes adicionais no cluster. Scripts de exemplo são fornecidos para instalar os seguintes componentes:

* [Matiz](hdinsight-hadoop-hue-linux.md)
* [Giraph](hdinsight-hadoop-giraph-install-linux.md)
* [Solr](hdinsight-hadoop-solr-install-linux.md)

Para obter informações sobre como desenvolver o seu próprio Script ações, consulte o artigo [desenvolvimento de acção de Script com HDInsight](hdinsight-hadoop-script-actions-linux.md).

###<a name="jar-files"></a>Para caixa ficheiros

Algumas tecnologias de Hadoop são fornecidas em ficheiros de para caixa autónomo que são contiverem funções utilizadas como parte de uma tarefa de MapReduce ou a partir do dentro porco ou ramo. Enquanto estas podem ser instaladas utilizar ações de Script, eles frequentemente não exigir início de qualquer programa de configuração e podem apenas ser carregados para o cluster depois de aprovisionar e utilizados diretamente. Se pretender makle-se de que o componente survives reimaging do cluster, pode guardar o ficheiro para caixa WASB.

Por exemplo, se pretender utilizar a versão mais recente do [DataFu](http://datafu.incubator.apache.org/), pode transferir para uma caixa que contém o projeto e carregue-o para HDInsight cluster. Em seguida, siga a documentação DataFu como utilizá-la a partir do porco ou ramo.

> [AZURE.IMPORTANT] Alguns componentes que são autónomo para caixa ficheiros são fornecidos com o HDInsight, mas não estão no caminho. Se estiver à procura para um componente específico, pode utilizar o seguimento para procurar no seu cluster:
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> Isto irá devolver o caminho de quaisquer ficheiros para caixa correspondentes.

Se o cluster já fornece uma versão de um componente como um ficheiro de para caixa autónomo, mas que pretende utilizar uma versão diferente, pode carregar uma nova versão do componente para cluster e tente utilizá-lo nas suas tarefas.

> [AZURE.WARNING] Componentes fornecidos com o cluster HDInsight são totalmente suportadas e o Microsoft Support irá ajudar a identificar e resolver problemas relacionados com a estes componentes.
>
> Componentes personalizados recebem faremos suporte para o ajudar a resolver ainda mais o problema. Isto poderá resultar em resolver o problema ou pedir-lhe para participar canais disponíveis para as tecnologias de abrir origem onde se encontram conhecimentos abrangente para essa tecnologia. Por exemplo, existem muitos sites de Comunidade que podem ser utilizados, tal como: [Fórum MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Também projetos Apache tem sites de projeto no [http://apache.org](http://apache.org), por exemplo: [Hadoop](http://hadoop.apache.org/), [motores](http://spark.apache.org/).

## <a name="next-steps"></a>Próximos passos

* [Migrar do HDInsight baseados no Windows para baseado em Linux](hdinsight-migrate-from-windows-to-linux.md)
* [Utilizar ramo com HDInsight](hdinsight-use-hive.md)
* [Utilizar porco com HDInsight](hdinsight-use-pig.md)
* [Utilizar tarefas de MapReduce com HDInsight](hdinsight-use-mapreduce.md)
