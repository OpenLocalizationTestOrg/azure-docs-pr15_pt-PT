<properties
   pageTitle="Analisar dados de sensor com Apache tempestade e HBase | Microsoft Azure"
   description="Saiba como ligar ao Apache tempestade com uma rede virtual. Utilizar tempestade com HBase para processar sensor dados a partir de um concentrador de evento e compreendê-lo com D3.js."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/20/2016"
   ms.author="larryfr"/>

# <a name="analyze-sensor-data-with-apache-storm-event-hub-and-hbase-in-hdinsight-hadoop"></a>Analisar dados de sensor com Apache tempestade, concentrador de evento e HBase no HDInsight (Hadoop) 

Saiba como utilizar Apache tempestade HDInsight para processar sensor dados a partir do Hub do Azure evento, armazene-o Apache HBase no HDInsight e compreendê-lo utilizando D3.js em execução como uma aplicação Web do Azure.

O modelo de Gestor de recursos do Azure utilizado neste documento demonstra como criar várias recursos Azure num grupo de recursos. Especificamente, cria uma rede Virtual Azure, dois HDInsight clusters (tempestade e HBase) e uma aplicação Web do Azure. Uma implementação node.js de um dashboard de web em tempo real é implementada automaticamente para a aplicação web.

> [AZURE.NOTE] As informações neste documento e o exemplo fornecido, foram testadas utilizando baseado em Linux 3.3 HDInsight e 3.4 versões de cluster.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Consulte o artigo [obter Azure versão de avaliação gratuita](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

    > [AZURE.IMPORTANT] Não necessita de um cluster de HDInsight existente; os passos neste documento irão criar os seguintes recursos:
    >
    > * Uma rede Virtual Azure
    > * Uma tempestade num cluster de HDInsight (com base em Linux 2 nós de trabalho)
    > * Um HBase num cluster de HDInsight (com base em Linux 2 nós de trabalho)
    > * Uma aplicação Web do Azure que aloja o dashboard da web

* [Node.js](http://nodejs.org/): Isto é utilizado para pré-visualizar o dashboard web localmente no seu ambiente de desenvolvimento.

* [Java e o 1.7 JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html): utilizado para desenvolver a topologia de tempestade.

* [Maven](http://maven.apache.org/what-is-maven.html): utilizados para criar e compilar o projeto.

* [Git](http://git-scm.com/): utilizados para transferir o projeto do GitHub.

* Um cliente __SSH__ : utilizado para ligar ao clusters baseado em Linux HDInsight. Para obter mais informações sobre como utilizar SSH com HDInsight, consulte os seguintes documentos.

    * [Utilizar SSH com HDInsight a partir de um cliente do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

    * [Utilizar SSH com HDInsight a partir de um cliente Linux, Unix ou Mac](hdinsight-hadoop-linux-use-ssh-unix.md)

    > [AZURE.NOTE] Também tem de ter acesso à `scp` comando, que é utilizado para copiar ficheiros entre o seu ambiente de desenvolvimento local e o cluster de HDInsight utilizando SSH.

## <a name="architecture"></a>Arquitetura

![Diagrama de arquitectura](./media/hdinsight-storm-sensor-data-analysis/devicesarchitecture.png)

Este exemplo é constituído pelos seguintes componentes:

* **Azure evento concentradores**: contém dados que são recolhidos a partir de sensores. Neste exemplo, uma aplicação é desde que gera os dados.

* **Tempestade no HDInsight**: fornece o processamento de dados em tempo real a partir do Hub do evento.

* **HBase no HDInsight**: Fornece um arquivo de dados NoSQL persistente para os dados após ter sido processado por tempestade.

* **Serviço de rede Virtual Azure**: permite comunicações seguras entre tempestade no HDInsight e HBase sobre clusters de HDInsight.

    > [AZURE.NOTE] Uma rede virtual é necessária para que possa utilizar a API do cliente Java HBase, tal como não está exposta sobre o gateway para HBase clusters público. Instalar clusters HBase e tempestade sessão na rede virtual mesmo permite cluster tempestade (ou qualquer outro sistema de rede virtual e) aceder diretamente ao HBase utilizando API do cliente.

* **Web site de dashboard**: um dashboard de exemplo gráficos de dados em tempo real.

    * O Web site está implementado Node.js, para que possa ser em qualquer sistema operativo do cliente para testar a ligação ou pode ser implementada para sites públicos do Azure.

    * [Socket.IO](http://socket.io/) é utilizada para a comunicação em tempo real entre a topologia de tempestade e o Web site.

        > [AZURE.NOTE] Este é um detalhe de implementação. Pode utilizar qualquer quadro de comunicações, tal como WebSockets observou ou SignalR.

    * [D3.js](http://d3js.org/) é utilizado para representar em gráfico os dados que são enviados para o Web site.

> [AZURE.IMPORTANT] Dois clusters necessários, porque não existe nenhum método suportado para criar um cluster de HDInsight para tempestade e HBase.

A topologia de lê dados a partir do Hub do evento, utilizando a classe de [org.apache.storm.eventhubs.spout.EventHubSpout](http://storm.apache.org/releases/0.10.1/javadocs/org/apache/storm/eventhubs/spout/class-use/EventHubSpout.html) e escreve dados na HBase utilizando a classe de [org.apache.storm.hbase.bolt.HBaseBolt](https://storm.apache.org/javadoc/apidocs/org/apache/storm/hbase/bolt/class-use/HBaseBolt.html) . Comunicação com o Web site é feita usando [socket.io client.java](https://github.com/nkzawa/socket.io-client.java).

Segue-se um diagrama da topologia.

![Diagrama de topologia](./media/hdinsight-storm-sensor-data-analysis/sensoranalysis.png)

> [AZURE.NOTE] Esta é uma vista muito simplificada da topologia. Em tempo de execução, é criada uma instância de cada componente para cada partição para o concentrador de evento que está a ser lido. Estas instâncias estão distribuídas nós cluster e dados são encaminhados entre eles, da seguinte forma:
>
> * Dados da vareta para o analisador de são balanceada de carga.
> * Dados a partir do analisador de ao Dashboard e HBase estão agrupados por ID de dispositivo, para que as mensagens a partir do mesmo dispositivo sempre fluam para o mesmo componente.

### <a name="topology-components"></a>Componentes de topologia

* **EventHub vareta**: A vareta é fornecida como parte da versão Apache tempestade 0.10.0 e superior.

    > [AZURE.NOTE] A vareta evento concentradores utilizada neste exemplo requer um tempestade na versão de cluster HDInsight 3.3 ou 3.4. Para obter informações sobre como utilizar o evento concentradores com uma versão anterior do tempestade no HDInsight, consulte o artigo [eventos de processo a partir do Azure evento concentradores com tempestade no HDInsight](hdinsight-storm-develop-java-event-hub-topology.md).

* **ParserBolt.java**: os dados que são emitidos pela vareta são JSON não processado e ocasionalmente mais do que um evento é emitido uma vez. Este raio demonstra como ler os dados emitidos pela vareta e emiti-lo para um novo fluxo de como uma cadeia de identificação que contém vários campos.

* **DashboardBolt.java**: demonstra como utilizar a biblioteca de cliente Socket.io para Java para enviar dados em tempo real ao web dashboard.

Este exemplo utiliza a arquitetura de [fluxo, somando](https://storm.apache.org/releases/0.10.0/flux.html) , para que a definição de topologia está contida nos ficheiros do YAML. Existem dois:

* __sem hbase.yaml__ - utilizar este ficheiro quando testar a topologia no seu ambiente de desenvolvimento. Não utilizam os componentes de HBase, uma vez que não é possível aceder a API do Java HBase a partir de fora da rede virtual que cluster reside na.

* __com hbase.yaml__ - utilizar este ficheiro quando implementar a topologia de cluster tempestade. Utiliza HBase componentes desde que é executada na mesma rede virtual como HBase cluster.

## <a name="prepare-your-environment"></a>Preparar o seu ambiente

Antes de utilizar neste exemplo, terá de criar um concentrador de evento Azure, qual a topologia de tempestade lê a partir de.

### <a name="configure-event-hub"></a>Configurar o concentrador de evento

Concentrador de evento é a origem de dados para este exemplo. Utilize os passos seguintes para criar um novo concentrador de evento.

1. A partir do [Azure portal](https://portal.azure.com), selecione **+ Novo** -> __Internet das coisas__ -> __Concentradores do evento__.

2. Na pá __Criar espaço de nomes__ , efetue as seguintes tarefas:

    1. Introduza um __nome__ para o espaço de nomes.
    2. Selecione uma camada comparar. __Básicas__ é suficiente para este exemplo.
    3. Selecione a __subscrição__ do Azure para utilizar.
    4. Selecione um grupo de recursos existente ou crie um novo.
    5. Selecione a __localização__ para o centro do evento.
    6. Selecione __Afixar ao dashboard__e, em seguida, clique em __Criar__.

3. Quando concluir o processo de criação, é apresentada a pá concentradores de evento para o espaço de nomes. A partir daqui, selecione __+ Adicionar evento concentrador__. No pá __Criar concentrador de evento__ , introduza um nome de __sensordata__ e, em seguida, selecione __Criar__. Deixe os outros campos os valores predefinidos.

4. O pá concentradores de evento para o espaço de nomes, selecione __Concentradores do evento__. Selecione a entrada de __sensordata__ .

5. Pá para o sensordata concentrador de evento, selecione __partilhado as políticas de acesso__. Utilize a ligação __+ Adicionar__ para adicionar as seguintes políticas:


  	| Nome da política | Em afirmações |
  	| ----- | ----- |
  	| dispositivos | Enviar |
  	| tempestade | Ouvir |

5. Selecione ambas as políticas e anote o valor de __Chave primária__ . Terá do valor para ambas as políticas nos passos futuras.

## <a name="download-and-configure-the-project"></a>Transferir e configurar o projecto

Utilize o seguinte procedimento para transferir o projeto do GitHub.

    git clone https://github.com/Blackmist/hdinsight-eventhub-example

Após completa o comando, terá a estrutura do directório seguintes:

    hdinsight-eventhub-example/
        TemperatureMonitor/ - this contains the topology
            resources/
                log4j2.xml - set logging to minimal
                no-hbase.yaml - topology definition for local testing
                with-hbase.yaml - topology definition that uses HBase in a virutal network
            src/ - the Java bolts
            dev.properties - contains configuration values for your environment
        dashboard/nodejs/ - this is the node.js web dashboard
        SendEvents/ - utilities to send fake sensor data

> [AZURE.NOTE] Este documento não entrar no detalhes completos de código incluído neste exemplo; No entanto, o código totalmente é comentado.

Abra o ficheiro **hdinsight-eventhub-example/TemperatureMonitor/dev.properties** e adicione as informações do concentrador de evento para as linhas seguintes:

    eventhub.read.policy.name: storm
    eventhub.read.policy.key: KeyForTheStormPolicy
    eventhub.namespace: YourNamespace
    eventhub.name: sensordata

> [AZURE.NOTE] Este exemplo assume que utilizou __tempestade__ como o nome da política de que tem uma afirmação __ouvir__ e que o seu centro de evento se chamar __sensordata__.

 Guarde o ficheiro depois de adicionar estas informações.

## <a name="compile-and-test-locally"></a>Compilar e teste localmente

Antes de testar, tem de iniciar o dashboard para ver o resultado da topologia de e gerar dados para armazenar no Centro de evento.

> [AZURE.IMPORTANT] O componente HBase desta topologia não está ativo quando testes localmente, como a API Java para o cluster HBase não podem ser acedidas a partir de fora do Azure Virtual da rede que contém os clusters.

### <a name="start-the-web-application"></a>Iniciar a aplicação web

1. Abrir uma nova linha de comandos ou terminal e altere directórios para o **hdinsight-eventhub exemplo/dashboard**e, em seguida, utilize o seguinte comando para instalar as dependências necessárias pela aplicação web:

        npm install

2. Utilize o seguinte comando para iniciar a aplicação web:

        node server.js

    Deverá visualizar uma mensagem semelhante ao seguinte:

        Server listening at port 3000

2. Abra um browser e introduza **http://localhost:3000 /** como o endereço. Deverá visualizar uma página semelhante ao seguinte:

    ![dashboard da Web](./media/hdinsight-storm-sensor-data-analysis/emptydashboard.png)

    Deixe esta linha de comandos ou terminal aberto. Depois de a testar, utilize Ctrl-C para parar o servidor web.

### <a name="start-generating-data"></a>Iniciar a gerar dados

> [AZURE.NOTE] Os passos nesta secção utilizam Node.js para que possa ser utilizados em nenhuma plataforma. Para outros exemplos de idioma, consulte o diretório **SendEvents** .

1. Abra uma nova linha de comandos, shell ou terminal e altere directórios para **hdinsight-eventhub-exemplo/SendEvents/nodejs**e, em seguida, utilize o seguinte comando para instalar as dependências necessárias pela aplicação:

        npm install

2. Abra o ficheiro de **app.js** num editor de texto e adicione as informações de evento concentrador que obteve anterior:

        // ServiceBus Namespace
        var namespace = 'YourNamespace';
        // Event Hub Name
        var hubname ='sensordata';
        // Shared access Policy name and key (from Event Hub configuration)
        var my_key_name = 'devices';
        var my_key = 'YourKey';
    
    > [AZURE.NOTE] Este exemplo assume que utilizou __sensordata__ como o nome do seu evento concentrador e __dispositivos__ como o nome da política de que tem um pedido de __Enviar__ .

2. Utilize o seguinte comando para inserir novas entradas no Centro de evento:

        node app.js

    Deverá ver várias linhas de saída que contêm os dados enviados para o evento concentrador. Estes irão aparecer semelhantes ao seguinte:

        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"0","Temperature":7}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"1","Temperature":39}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"2","Temperature":86}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"3","Temperature":29}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"4","Temperature":30}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"5","Temperature":5}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"6","Temperature":24}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"7","Temperature":40}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"8","Temperature":43}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"9","Temperature":84}

### <a name="start-the-topology"></a>Iniciar a topologia

2. Abra uma nova linha de comandos, shell ou directórios terminal e altere a __Hdinsight-eventhub exemplo/TemperatureMonitor__e, em seguida, utilize o seguinte comando para iniciar a topologia de:

        mvn compile exec:java -Dexec.args="--local -R /no-hbase.yaml --filter dev.properties"
    
    Se estiver a utilizar o PowerShell, utilize o seguinte procedimento:

        mvn compile exec:java "-Dexec.args=--local -R /no-hbase.yaml --filter dev.properties"

    > [AZURE.NOTE] Se estiver a um sistema de Unix/Linux/OS X e tiver [instalado tempestade no seu ambiente de desenvolvimento](http://storm.apache.org/releases/0.10.0/Setting-up-development-environment.html), pode utilizar em vez disso, os seguintes comandos:
    >
    > `mvn compile package`
    > `storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /no-hbase.yaml`

    Esta ação inicia a topologia definida no ficheiro __sem hbase.yaml__ no modo de local. Os valores contidos no ficheiro __dev.properties__ fornecem as informações de ligação para concentradores do evento. Uma vez iniciado, a topologia de lê entradas a partir do Hub do evento e envia-os para o dashboard em execução no seu computador local. Deverá visualizar linhas aparecem no dashboard de web, semelhante ao seguinte:

    ![dashboard com dados](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

3. Enquanto o dashboard estiver em execução, utilize o `node app.js` comando dos passos anteriores para enviar novos dados a concentradores do evento. Porque os valores de temperatura aleatoriamente são gerados, o gráfico deve atualizar para apresentar grandes alterações de temperatura.

    > [AZURE.NOTE] Tem de ser no diretório __Hdinsight-eventhub-exemplo/SendEvents/Nodejs__ ao utilizar o `node app.js` comando.

3. Depois de verificar que isto funciona, pare a topologia utilizando Ctrl + C. Pode utilizar Ctrl + C para parar o servidor local web também.

## <a name="create-a-storm-and-hbase-cluster"></a>Criar um cluster tempestade e HBase

Para executar a topologia no HDInsight e para ativar o raio HBase, terá de criar um novo cluster tempestade e HBase cluster. Os passos nesta secção utilizam um [modelo de Gestor de recursos do Azure](../resource-group-template-deploy.md) para criar um novo cluster de rede Virtual do Azure e um tempestade e HBase da rede virtual. O modelo também cria uma aplicação Web do Azure e implementa uma cópia do dashboard para o mesmo.

> [AZURE.NOTE] Uma rede virtual é utilizada para que a topologia executadas no cluster tempestade diretamente possam comunicar com o cluster HBase utilizar a API de Java HBase.

O modelo de Gestor de recursos utilizado neste documento está localizado num contentor de BLOBs público na __https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hbase-storm-cluster-in-vnet.json__.

1. Clique no botão seguinte para iniciar sessão Azure e abra o modelo de Gestor de recursos no portal do Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-storm-cluster-in-vnet.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. A partir do pá **parâmetros** , introduza o seguinte:

    ![Parâmetros de HDInsight](./media/hdinsight-storm-sensor-data-analysis/parameters.png)
    
    * **BASECLUSTERNAME**: este valor será utilizada como o nome base para o tempestade e HBase clusters. Por exemplo, introduzir __hdi__ irá criar um cluster de tempestade __tempestade hdi__ com o nome e um cluster de HBase __hbase hdi__com o nome.
    * __CLUSTERLOGINUSERNAME__: O nome de utilizador do administrador para os clusters tempestade e HBase.
    * __CLUSTERLOGINPASSWORD__: A palavra-passe de utilizador do administrador para os clusters tempestade e HBase.
    * __SSHUSERNAME__: O SSH utilizador para criar para os clusters tempestade e HBase.
    * __SSHPASSWORD__: A palavra-passe para o utilizador SSH para os clusters tempestade e HBase.
    * __Localização__: A região que clusters serão criados na.
    
    Clique em __OK__ para guardar os parâmetros.
    
3. Utilize a secção do __grupo de recursos__ para criar um novo grupo de recursos ou selecione uma existente.

4. No menu pendente __localização do grupo de recursos__ , selecione a mesma localização à medida que selecionado para o parâmetro de __localização__ .

5. Selecionar __jurídicos termos__e, em seguida, selecione __Criar__.

6. Por fim, selecione __Afixar ao dashboard__ e, em seguida, selecione __Criar__. Irá demorar cerca de 20 minutos para criar os clusters.

Assim que os recursos tem sido criados, serão redirecionados para uma pá do grupo de recursos que contém os clusters e dashboard da web.

![Pá de grupo de recursos para vnet e clusters](./media/hdinsight-storm-sensor-data-analysis/groupblade.png)

> [AZURE.IMPORTANT] Repare que os nomes dos conjuntos de sectores HDInsight são __Tempestade BASENAME__ e __hbase BASENAME__, que BASENAME é o nome que forneceu para o modelo. Irá utilizar os seguintes nomes nos passos posteriores quando se liga ao clusters. Tenha em atenção que o nome do site de dashboard é __basename dashboard__. Irá utilizar este mais tarde quando vê o dashboard.

## <a name="configure-the-dashboard-bolt"></a>Configurar o raio de Dashboard

Para enviar dados para o dashboard implementado como uma aplicação web, tem de modificar a linha seguinte no ficheiro __dev.properties__ :

    dashboard.uri: http://localhost:3000

Alterar `http://localhost:3000` para `http://BASENAME-dashboard.azurewebsites.net` e guarde o ficheiro. Substitua __BASENAME__ com o nome da base fornecido no passo anterior. Também pode utilizar o grupo de recursos criado anteriormente para selecionar o dashboard e veja o URL.

## <a name="create-the-hbase-table"></a>Criar a tabela HBase

Para poder armazenar dados HBase, podemos primeiro tem de criar uma tabela. Pretende criar previamente recursos de que necessita de tempestade para escrever geralmente, como a tentar criar recursos a partir do dentro de uma topologia de tempestade podem resultar em várias, distribuídas cópias do código tentar criar o mesmo recurso. Criar os recursos fora da topologia e apenas utilizar tempestade para leitura/escrita e análise.

1. Utilize SSH para ligar ao cluster HBase utilizando o SSH utilizador e palavra-passe fornecida ao modelo durante a criação de cluster. Por exemplo, se ligar utilizando o `ssh` comando, deve utilizar a seguinte sintaxe:

        ssh USERNAME@hbase-BASENAME-ssh.azurehdinsight.net
    
    Este comando, substitua o __nome de utilizador__ com o nome de utilizador SSH que forneceu quando criar o cluster e __BASENAME__ com o nome de base que forneceu. Quando lhe for pedido, introduza a palavra-passe para o utilizador SSH.

2. Da sessão SSH, comece a shell de HBase.

        hbase shell
    
    Assim que a shell de ter sido carregado, irá ver um `hbase(main):001:0>` linha de comandos.

3. A partir da shell de HBase, introduza o seguinte comando para criar uma tabela para armazenar os dados de sensor:

        create 'SensorData', 'cf'

4. Certifique-se de que a tabela que foi criada utilizando o seguinte comando:

        scan 'SensorData'
        
    Isto devolve informações sobre semelhante ao exemplo seguinte, que indica que não existem linhas 0 na tabela.
    
        ROW                   COLUMN+CELL                                       0 row(s) in 0.1900 seconds

5. Introduza o seguinte para sair a shell de HBase:

        exit

## <a name="configure-the-hbase-bolt"></a>Configurar o raio HBase

Para escrever para HBase do cluster tempestade, tem de fornecer o raio HBase com os detalhes de configuração do seu cluster HBase. A forma mais fácil para fazer isto é transferir o __hbase site.xml__ do cluster e inclui-los no seu projeto. Também tem remova os comentários várias dependências no ficheiro de __pom.xml__ , que carregar o componente de tempestade hbase e dependências necessárias.

> [AZURE.IMPORTANT] Também tem de transferir o ficheiro de tempestade hbase.jar fornecido na sua tempestade num cluster de computação 3.3 ou 3.4 HDInsight; Esta versão é compilada para trabalhar com HBase 1.1.x, que é utilizado para HBase no HDInsight 3.3 e 3.4 clusters. Se utilizar um componente de tempestade hbase a partir de qualquer outro lugar, poderá estar compilado contra uma versão anterior do HBase.

### <a name="download-the-hbase-sitexml"></a>Transferir o site.xml hbase

A partir de uma linha de comandos, utilize SCP para transferir o ficheiro de __hbase site.xml__ do cluster. No exemplo seguinte, substitua o __nome de utilizador__ com o utilizador SSH que forneceu quando criar o cluster e __BASENAME__ com o nome da base fornecidas anteriormente. Quando lhe for pedido, introduza a palavra-passe para o utilizador SSH. Substituir o `/path/to/TemperatureMonitor/resources/hbase-site.xml` com o caminho para este ficheiro no projeto TemperatureMonitor.

    scp USERNAME@hbase-BASENAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml /path/to/TemperatureMonitor/resources/hbase-site.xml

Isto irá transferir o __hbase site.xml__ para o caminho especificado.

### <a name="download-and-install-the-storm-hbase-component"></a>Transferir e instalar o componente de tempestade hbase

1. A partir de uma linha de comandos, utilize SCP para transferir o ficheiro de __tempestade hbase.jar__ do cluster tempestade. No exemplo seguinte, substitua o __nome de utilizador__ com o utilizador SSH que forneceu quando criar o cluster e __BASENAME__ com o nome da base fornecidas anteriormente. Quando lhe for pedido, introduza a palavra-passe para o utilizador SSH.

        scp USERNAME@storm-BASENAME-ssh.azurehdinsight.net:/usr/hdp/current/storm-client/contrib/storm-hbase/storm-hbase*.jar .

    Isto irá transferir um ficheiro denominado `storm-hbase-####.jar`, onde # é o número de versão de tempestade para este cluster. Efectuar uma nota deste número, tal como são utilizadas mais tarde.

2. Utilize o seguinte comando para instalar este componente para o repositório Maven local no seu ambiente de desenvolvimento. Permite que avançou localizar o pacote ao compilar o projeto. Substituir __####__ com o número da versão incluído no nome do ficheiro.

        mvn install:install-file -Dfile=storm-hbase-####.jar -DgroupId=org.apache.storm -DartifactId=storm-hbase -Dversion=#### -Dpackaging=jar
    
    Se estiver a utilizar o PowerShell, utilize o seguinte comando:

        mvn install:install-file "-Dfile=storm-hbase-####.jar" "-DgroupId=org.apache.storm" "-DartifactId=storm-hbase" "-Dversion=####" "-Dpackaging=jar"

### <a name="enable-the-storm-hbase-component-in-the-project"></a>Activar o componente de tempestade hbase no projeto

1. Abra o ficheiro __TemperatureMonitor/pom.xml__ e elimine as linhas seguintes:

        <!-- uncomment this section to enable the hbase-bolt
        end comment for hbase-bolt section -->
    
    > [AZURE.IMPORTANT] Eliminar apenas estas duas linhas; Não elimine qualquer uma das linhas entre as mesmas.
    
    Permite que vários componentes que são necessários quando comunicar com HBase utilizando o raio hbase.

2. Localizar as linhas seguintes e, em seguida, substituir __####__ com o número da versão do ficheiro tempestade hbase tenha transferido anteriormente.

        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>storm-hbase</artifactId>
            <version>####</version>
        </dependency>

    > [AZURE.IMPORTANT] O número da versão tem de corresponder a versão que utilizou ao instalar o componente para o repositório Maven local, tal como Maven utiliza estas informações para carregar o componente durante a criação de projeto.

2. Guarde o ficheiro __pom.xml__ .

## <a name="build-package-and-deploy-the-solution-to-hdinsight"></a>Criar, pacote e implementar a solução para HDInsight

No seu ambiente de desenvolvimento, utilize os passos seguintes para implementar a topologia de tempestade cluster tempestade.

1. A partir do diretório __TemperatureMonitor__ , utilize o seguinte comando para executar uma nova compilação e criar um pacote para caixa a partir do seu projeto:

        mvn clean compile package

    Isto irá criar um ficheiro denominado **TemperatureMonitor-1.0-SNAPSHOT.jar** no directório de **destino** do seu projeto.

2. Utilize scp para carregar o ficheiro __TemperatureMonitor-1.0-SNAPSHOT.jar__ para o seu cluster tempestade. No exemplo seguinte, substitua o __nome de utilizador__ com o utilizador SSH que forneceu quando criar o cluster e __BASENAME__ com o nome da base fornecidas anteriormente. Quando lhe for pedido, introduza a palavra-passe para o utilizador SSH.

        scp target\TemperatureMonitor-1.0-SNAPSHOT.jar USERNAME@storm-BASENAME-ssh.azurehdinsight.net:TemperatureMonitor-1.0-SNAPSHOT.jar
    
    > [AZURE.NOTE] Poderá demorar alguns minutos para carregar o ficheiro, como irá estar vários megabytes de tamanho.

    Utilize scp para carregar o ficheiro __dev.properties__ , como este contém as informações utilizadas para ligar ao evento concentradores e o dashboard.

        scp dev.properties USERNAME@storm-BASENAME-ssh.azurehdinsight.net:dev.properties

3. Assim que tenham sido carregados os ficheiros, ligar-se ao cluster utilizando SSH.

        ssh USERNAME@storm-BASENAME-ssh.azurehdinsight.net

4. A partir do sessão SSH, utilize o seguinte comando para iniciar a topologia.

        storm jar TemperatureMonitor-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /with-hbase.yaml --filter dev.properties
    
    Esta ação inicia a topologia de utilizar a definição de topologia no ficheiro __com hbase.yaml__ e os valores de configuração no ficheiro __dev.properties__ .

3. Depois de ter começado a topologia, abra um browser para o Web site publicado no Azure, em seguida, utilize o `node app.js` comando para enviar dados para o evento concentrador. Deverá visualizar o dashboard web atualizar para apresentar as informações.

    ![dashboard](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

## <a name="view-hbase-data"></a>Ver dados HBase

Depois de ter submetido dados para a topologia utilizando `node app.js`, utilize os seguintes passos para ligar ao HBase e certifique-se de que os dados foram escritos para a tabela que criou anteriormente.

1. Utilize SSH para ligar ao HBase cluster.

        ssh USERNAME@hbase-BASENAME-ssh.azurehdinsight.net

2. Da sessão SSH, comece a shell de HBase.

        hbase shell
    
    Assim que a shell de ter sido carregado, irá ver um `hbase(main):001:0>` linha de comandos.

2. Veja as linhas da tabela:

        scan 'SensorData'
        
    Isto deve devolver informações semelhante ao seguinte, que indica que não existem linhas 0 na tabela.
    
        hbase(main):002:0> scan 'SensorData'
        ROW                             COLUMN+CELL
        \x00\x00\x00\x00               column=cf:temperature, timestamp=1467290788277, value=\x00\x00\x00\x04
        \x00\x00\x00\x00               column=cf:timestamp, timestamp=1467290788277, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x01               column=cf:temperature, timestamp=1467290788348, value=\x00\x00\x00M
        \x00\x00\x00\x01               column=cf:timestamp, timestamp=1467290788348, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x02               column=cf:temperature, timestamp=1467290788268, value=\x00\x00\x00R
        \x00\x00\x00\x02               column=cf:timestamp, timestamp=1467290788268, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x03               column=cf:temperature, timestamp=1467290788269, value=\x00\x00\x00#
        \x00\x00\x00\x03               column=cf:timestamp, timestamp=1467290788269, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x04               column=cf:temperature, timestamp=1467290788356, value=\x00\x00\x00>
        \x00\x00\x00\x04               column=cf:timestamp, timestamp=1467290788356, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x05               column=cf:temperature, timestamp=1467290788326, value=\x00\x00\x00\x0D
        \x00\x00\x00\x05               column=cf:timestamp, timestamp=1467290788326, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x06               column=cf:temperature, timestamp=1467290788253, value=\x00\x00\x009
        \x00\x00\x00\x06               column=cf:timestamp, timestamp=1467290788253, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x07               column=cf:temperature, timestamp=1467290788229, value=\x00\x00\x00\x12
        \x00\x00\x00\x07               column=cf:timestamp, timestamp=1467290788229, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x08               column=cf:temperature, timestamp=1467290788336, value=\x00\x00\x00\x16
        \x00\x00\x00\x08               column=cf:timestamp, timestamp=1467290788336, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x09               column=cf:temperature, timestamp=1467290788246, value=\x00\x00\x001
        \x00\x00\x00\x09               column=cf:timestamp, timestamp=1467290788246, value=2015-02-10T14:43.05.00320Z
        10 row(s) in 0.1800 seconds

    > [AZURE.NOTE] Esta operação de pesquisa só devolverá um máximo de 10 linhas da tabela.

## <a name="delete-your-clusters"></a>Eliminar os clusters

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Para eliminar os clusters, armazenamento e web app ao mesmo tempo, eliminar o grupo de recursos que os contém.

## <a name="next-steps"></a>Próximos passos

Aprendeu como utilizar tempestade para ler os dados a partir do evento concentradores, armazene-o HBase e visualizar as informações sobre um dashboard externo utilizando Socket.io e D3.js agora.

* Para obter mais exemplos de topologias tempestade com HDInsight, consulte:

    * [Topologias de exemplo para tempestade no HDInsight](hdinsight-storm-example-topology.md)

* Para mais informações sobre Apache tempestade, consulte o site [Apache tempestade](https://storm.incubator.apache.org/) .

* Para obter mais informações sobre HBase HDInsight, consulte o [HBase com descrição geral de HDInsight](hdinsight-hbase-overview.md).

* Para mais informações sobre Socket.io, consulte o site [socket.io](http://socket.io/) .

* Para mais informações sobre D3.js, consulte o artigo [D3.js - dados condicionada por documentos](http://d3js.org/).

* Para obter informações sobre a criação de topologias em Java, consulte o artigo [desenvolver Java topologias para Apache tempestade no HDInsight](hdinsight-storm-develop-java-topology.md).

* Para obter informações sobre como criar topologias no .NET, consulte o artigo [desenvolver c# topologias para Apache tempestade no HDInsight utilizando o Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

[azure-portal]: https://portal.azure.com
