<properties
   pageTitle="Monitorizar e gerir clusters HDInsight utilizando o Apache Ambari REST API | Microsoft Azure"
   description="Saiba como utilizar Ambari para monitorizar e gerir clusters baseado em Linux HDInsight. Este documento, vai aprender a utilizar a API do resto Ambari incluído com o HDInsight clusters."
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
   ms.date="09/20/2016"
   ms.author="larryfr"/>

#<a name="manage-hdinsight-clusters-by-using-the-ambari-rest-api"></a>Gerir HDInsight clusters utilizando o Ambari REST API

[AZURE.INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari simplifica a gestão e controlo de um cluster de Hadoop, fornecendo um fácil utilizar IU da web e REST API. Ambari está incluído no baseado em Linux HDInsight clusters e é utilizado para monitorizar o cluster e fazer alterações à configuração. Este documento, aprendeu os princípios básicos para trabalhar com a API do resto Ambari executando tarefas comuns utilizando o laço.

##<a name="prerequisites"></a>Pré-requisitos

* [cURL](http://curl.haxx.se/): Laço é um utilitário de em diferentes plataformas que pode ser utilizado para trabalhar com REST APIs da linha de comandos. Neste documento, é utilizado para comunicar com a API REST Ambari.
* [jq](https://stedolan.github.io/jq/): jq é um utilitário de linha de comandos de em diferentes plataformas para trabalhar com documentos do JSON. Neste documento, é utilizado para analisar os documentos JSON devolvidos a partir da API do resto Ambari.
* [Clip do Azure](../xplat-cli-install.md): um utilitário de linha de comandos em diferentes plataformas para trabalhar com os serviços do Azure.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 

##<a id="whatis"></a>O que é Ambari?

[Apache Ambari](http://ambari.apache.org) torna Hadoop gestão mais simples, fornecendo um web fáceis de utilizar IU que pode ser utilizada para fornecer, gerir e monitorizar Hadoop clusters. Os programadores podem integrar estas capacidades suas aplicações utilizando as [Ambari REST APIs](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Ambari é fornecido por predefinição com clusters baseado em Linux HDInsight.

##<a name="rest-api"></a>REST API

O URI base para o Ambari REST API no HDInsight é https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME, onde o __nome de cluster__ é o nome do seu cluster.

> [AZURE.IMPORTANT] Enquanto o nome do cluster na parte domínio completamente qualificado (FQDN) do nome do URI (CLUSTERNAME.azurehdinsight.net) é maiúsculas e minúsculas, outras ocorrências no URI estão entre maiúsculas e minúsculas. Por exemplo, se o seu cluster se chamar MyCluster, seguem-se URIs válidos:
>
> `https://mycluster.azurehdinsight.net/api/v1/clusters/MyCluster`
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/MyCluster`
>
> A seguinte URIs devolver um erro porque a segunda ocorrência do nome não for o caso correto.
>
> `https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster`
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/mycluster`

Ligar-se ao Ambari no HDInsight requer HTTPS. Quando autentica a ligação, tem de utilizar o nome de conta de administrador (a predefinição é __administrador__) e a palavra-passe fornecida quando o cluster foi criado.

O exemplo seguinte utiliza Laço para tornar num pedido GET contra REST API. Substitua a palavra-passe de administrador para o cluster __palavra-passe__ . Substitua o __nome de cluster__ com o nome do cluster:

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME"
    
A resposta é um documento JSON que começa com informações semelhantes ao seguinte:

    {
    "href" : "http://10.0.0.10:8080/api/v1/clusters/CLUSTERNAME",
    "Clusters" : {
        "cluster_id" : 2,
        "cluster_name" : "CLUSTERNAME",
        "health_report" : {
        "Host/stale_config" : 0,
        "Host/maintenance_state" : 0,
        "Host/host_state/HEALTHY" : 7,
        "Host/host_state/UNHEALTHY" : 0,
        "Host/host_state/HEARTBEAT_LOST" : 0,
        "Host/host_state/INIT" : 0,
        "Host/host_status/HEALTHY" : 7,
        "Host/host_status/UNHEALTHY" : 0,
        "Host/host_status/UNKNOWN" : 0,
        "Host/host_status/ALERT" : 0

Dado que se trata JSON, é mais fácil de utilizar um analisador JSON para trabalhar com os dados. Por exemplo, o exemplo seguinte utiliza jq para apresentar apenas os `health_report` elemento.

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME" | jq '.Clusters.health_report'

##<a name="example-get-the-fqdn-of-cluster-nodes"></a>Exemplo: Obter o FQDN de nós de cluster

Quando trabalha com HDInsight, poderá ter de souber o nome de domínio completamente qualificado (FQDN) de um nó de cluster. Pode obter facilmente o FQDN para os vários nós num cluster utilizando o seguinte:

* __Nós de cabeça__:`curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'`
* __Nós de trabalho__:`curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/DATANODE" | jq '.host_components[].HostRoles.host_name'`
* __Nós zookeeper__:`curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq '.host_components[].HostRoles.host_name'`

Nota que cada um destes exemplos siga o mesmo padrão:

1. Um componente que recomendamos saber de consulta é executada no em nós.
2. Obter o `host_name` elementos, que contêm o FQDN para estes nós.

O `host_components` elemento do documento remetente contém vários itens. Utilizar `.host_components[]`, e, em seguida, especificar um caminho dentro do elemento será loop em cada item e separar o valor a partir do caminho específico. Se pretender apenas um valor, tal como a primeira entrada FQDN, pode devolver os itens como uma coleção de e, em seguida, selecione uma entrada específica:

    jq '[.host_components[].HostRoles.host_name][0]'

Isto devolve o primeiro FQDN da coleção.

##<a name="example-get-the-default-storage-account-and-container"></a>Exemplo: Obter a conta de armazenamento predefinida e contentor

Quando cria um cluster de HDInsight, tem de utilizar uma conta de armazenamento do Azure e um contentor blob como o armazenamento de predefinida para o cluster. Pode utilizar Ambari para obter estas informações depois cluster ter sido criado. Por exemplo, se quiser através de programação escreva dados diretamente para o contentor.

Irá obter o URI WASB do armazenamento clusters predefinido:

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
    
> [AZURE.NOTE] Isto devolve a primeira configuração aplicada ao servidor (`service_config_version=1`,) que contém estas informações. Se obtiver um valor que terem sido modificado após a criação de cluster, terá das versões de configuração da lista e obter a mais recente.

Isto devolve um valor semelhante ao exemplo seguinte, onde __contentor__ é o contentor predefinido e __ACCOUNTNAME__ é o nome da conta de armazenamento do Azure:

    wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net

Em seguida, pode utilizar estas informações com o [Azure clip](../xplat-cli-install.md) para carregar ou transferir dados do contentor.

1. Obter o grupo de recursos para a conta de armazenamento. Substitua o nome da conta de armazenamento obtido a partir do Ambari __ACCOUNTNAME__ :

        azure storage account list --json | jq '.[] | select(.name=="ACCOUNTNAME").resourceGroup'
    
    Isto devolve o nome do grupo de recursos para a conta.
    
    > [AZURE.NOTE] Se nada devolvido por este comando, terá de alterar o clip do Azure para o modo de Gestor de recursos do Azure e volte a executar o comando. Para mudar para modo de Gestor de recursos do Azure, utilize o seguinte comando:
    >
    > `azure config mode arm`
    
2. Obter a chave da conta de armazenamento. Substitua o __nome de grupo__ com o grupo de recursos a partir do passo anterior. Substitua __ACCOUNTNAME__ com o nome da conta de armazenamento:

        azure storage account keys list -g GROUPNAME ACCOUNTNAME --json | jq '.storageAccountKeys.key1'

    Este exemplo devolve a chave primária da conta.
    
3. Utilize o comando de carregamento para armazenar um ficheiro no contentor:

        azure storage blob upload -a ACCOUNTNAME -k ACCOUNTKEY -f FILEPATH --container __CONTAINER__ -b BLOBPATH
        
    Substitua o nome da conta de armazenamento __ACCOUNTNAME__ . Substituir __ACCOUNTKEY__ com a tecla obtidos anteriormente. __FILEPATH__ é o caminho para o ficheiro que pretende carregar, enquanto __BLOBPATH__ é o caminho no contentor.

    Por exemplo, se pretender que o ficheiro seja apresentado no HDInsight na wasbs://example/data/filename.txt, em seguida, __BLOBPATH__ seria `example/data/filename.txt`.

##<a name="example-update-ambari-configuration"></a>Exemplo: Ambari de atualização de configuração

1. Obter a configuração atual, que armazena Ambari como "configuração pretendida":

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME?fields=Clusters/desired_configs"
        
    Este exemplo devolve um documento JSON que contém a configuração atual (identificada pelo valor de _etiqueta_ ) para os componentes instalados no cluster. O exemplo seguinte é um excerto dos dados devolvidos a partir de um tipo de cluster motores.
    
        "spark-metrics-properties" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        },
        "spark-thrift-fairscheduler" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        },
        "spark-thrift-sparkconf" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        }

    Nesta lista, precisa de copiar o nome do componente (por exemplo, __motores\_outras entidades\_sparkconf__ e o valor de __etiqueta__ .
    
2. Obter a configuração para o componente e etiqueta utilizando o seguinte comando. Substitua __motores-outras entidades-sparkconf__ e __inicial__ a etiqueta que pretende obter a configuração para e o componente.

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=spark-thrift-sparkconf&tag=INITIAL" | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    
    Laço obtém o documento JSON, em seguida, jq é utilizado para efetuar alterações aos dados para criar um modelo. O modelo, em seguida, é utilizado para adicionar/modifique os valores de configuração. Especificamente faz o seguinte:
    
    * Cria um valor exclusivo que contenham a cadeia "versão" e a data, o que é armazenada no __newtag__.
    * Cria um documento de raiz para a nova configuração pretendida.
    * Obtém os conteúdos do `.items[]` de matriz e adiciona-a no elemento __desired_config__ .
    * Elimina a __href__, __versão__e elementos de __configuração__ , estes elementos não são conforme necessário para submeter uma nova configuração.
    * Adiciona um novo elemento de __etiqueta__ e define o valor para __versão # # #__. A parte numérica baseia-se a data atual. Cada configuração tem de ter uma etiqueta exclusiva.
    
    Por fim, os dados são guardados para o documento __newconfig.json__ . Estrutura do documento deverá ser semelhante ao exemplo seguinte:
    
        {
            "Clusters": {
                "desired_config": {
                "tag": "version1459260185774265400",
                "type": "spark-thrift-sparkconf",
                "properties": {
                    ....
                 },
                 "properties_attributes": {
                     ....
                 }
            }
        }

3. Abra os valores de documento e modificar/adicionar __newconfig.json__ o objeto de __Propriedades__ . O exemplo seguinte altera o valor de __"spark.yarn.am.memory"__ de __"1 g"__ para __"3G"__ e e adiciona um novo elemento para __"spark.kryoserializer.buffer.max"__ com um valor de __"256 m"__.

        "spark.yarn.am.memory": "3g",
        "spark.kyroserializer.buffer.max": "256m",

    Guarde o ficheiro depois de ter as modificações.

4. Utilize o seguinte comando para submeter a configuração atualizada para Ambari.

        cat newconfig.json | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME"
        
    Este comando tubos o conteúdo do ficheiro __newconfig.json__ ao pedido de Laço, submete-a para o cluster como nova configuração pretendida. O pedido de Laço devolve um documento JSON. O elemento __versionTag__ neste documento deverá corresponder à versão que utilizou ao submeter e o objeto __configs__ irá conter as alterações à configuração pedidas.

###<a name="example-restart-a-service-component"></a>Exemplo: Reiniciar um componente de serviço

Neste momento, se observe web Ambari IU, o serviço de motores irá indicar que precisa de ser reiniciado antes da configuração da nova pode produzirem efeito. Utilize os passos seguintes para reiniciar o serviço.

1. Utilize o seguinte procedimento para ativar o modo de manutenção para o serviço de motores:

        echo '{"RequestInfo": {"context": "turning on maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

    Este comando envia um documento JSON para o servidor (contidos a `echo` instrução,) que ativa o modo de manutenção.
    Pode verificar que o serviço está agora no modo de manutenção utilizando o pedido seguinte:
    
        curl -u admin:PASSWORD -H "X-Requested-By: ambari" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK" | jq .ServiceInfo.maintenance_state
        
    Isto irá devolver um valor de `"ON"`.

3. Em seguida, utilize o seguinte para desativar o serviço:

        echo '{"RequestInfo": {"context" :"Stopping the Spark service"}, "Body": {"ServiceInfo": {"state": "INSTALLED"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"
        
    Este comando devolver uma resposta semelhante ao seguinte.
    
        {
            "href" : "http://10.0.0.18:8080/api/v1/clusters/CLUSTERNAME/requests/29",
            "Requests" : {
                "id" : 29,
                "status" : "Accepted"
            }
        }
    
    O `href` valor devolvido por este URI está a utilizar o endereço IP interno do nó do cluster. Para utilizá-lo a partir de fora do cluster, substitua a parte '10.0.0.18:8080' com o FQDN do cluster. Por exemplo, o comando seguinte obtém o estado do pedido.
    
        curl -u admin:PASSWORD -H "X-Requested-By: ambari" "https://CLUSTERNAME/api/v1/clusters/CLUSTERNAME/requests/29" | jq .Requests.request_status
    
    Se esta devolve um valor de `"COMPLETED"` , em seguida, o pedido já terminou.

4. Assim que o pedido anterior for concluída, utilize o seguinte procedimento para iniciar o serviço.

        echo '{"RequestInfo": {"context" :"Restarting the Spark service"}, "Body": {"ServiceInfo": {"state": "STARTED"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

    Assim que o serviço reinicia, é as novas definições de configuração.

5. Por fim, utilize o seguinte procedimento para desativar o modo de manutenção.

        echo '{"RequestInfo": {"context": "turning off maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

##<a name="next-steps"></a>Próximos passos

Para uma referência completa da API do resto, consulte o artigo [Ambari API referência V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

> [AZURE.NOTE] Algumas funcionalidades Ambari estiver desativada, tal como é gerido pelo serviço de nuvem HDInsight; Por exemplo, adicionar ou remover anfitriões de cluster ou adicionar novos serviços.
