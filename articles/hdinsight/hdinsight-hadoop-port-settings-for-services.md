<properties
pageTitle="Portas utilizadas pelo HDInsight | Azure"
description="Uma lista de portas utilizado pelos serviços Hadoop em execução no HDInsight."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="10/03/2016"
ms.author="larryfr"/>

# <a name="ports-and-uris-used-by-hdinsight"></a>Portas e protocolos URIs utilizado pelo HDInsight

Este artigo fornece uma lista das portas utilizados pelos serviços Hadoop em execução em clusters baseado em Linux HDInsight. Também fornece informações em portas utilizadas para ligar ao cluster utilizando SSH.

## <a name="public-ports-vs-non-public-ports"></a>Portas públicas vs. portas não público

Baseado em Linux clusters HDInsight expõe apenas três portas publicamente na internet; 22, 23 e 443. Estas são utilizadas para aceder ao cluster utilizando serviços expostos através do protocolo HTTPS seguro e SSH a forma segura.

Internamente, HDInsight é implementada por várias máquinas virtuais do Azure (nós dentro do cluster) usar uma rede Virtual do Azure. A partir de dentro da rede virtual, pode aceder a portas não expostas através da internet. Por exemplo, se ligar a um de nós cabeça utilizando SSH, a partir do nó cabeça pode, em seguida, diretamente aceder services em execução em nós de cluster.

> [AZURE.IMPORTANT] Quando cria um cluster de HDInsight, se não especificar uma rede Virtual Azure como opção de configuração, é criado um; No entanto, não poderá participar noutros computadores (como outras máquinas virtuais do Azure ou computador cliente de desenvolvimento,) para este criado automaticamente rede virtual. 

Para participar máquinas adicionais à rede virtual, tem de criar a rede virtual pela primeira vez e, em seguida, especifique-lo quando criar o seu cluster HDInsight. Para mais informações, consulte o artigo [funcionalidades de expandir HDInsight ao utilizar uma rede Virtual do Azure](hdinsight-extend-hadoop-virtual-network.md)

## <a name="public-ports"></a>Portas públicas

Todos os nós num HDInsight cluster estão localizados numa rede Virtual Azure e não podem ser acedidos diretamente a partir da internet. Um gateway público fornece acesso à internet para as seguintes portas, são comuns em todos os tipos de cluster HDInsight.

| Serviço | Porta | Protocolo | Descrição |
| ---- | ---------- | -------- | ----------- | ----------- |
| sshd | 22 | SSH | Liga clientes ao sshd na headnode principal. Consulte o artigo [utilizar SSH com baseado em Linux HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) |
| sshd | 22 | SSH | Liga clientes ao sshd no nó do limite (apenas para o HDInsight Premium). Consulte o artigo [começar a utilizar o servidor de R na HDInsight](hdinsight-hadoop-r-server-get-started.md) |
| sshd | 23 | SSH | Liga clientes ao sshd na headnode secundário. Consulte o artigo [utilizar SSH com baseado em Linux HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) |
| Ambari | 443 | HTTPS | Ambari IU da web. Consulte o artigo [Gerir HDInsight utilizar Ambari Web IU](hdinsight-hadoop-manage-ambari.md) |
| Ambari | 443 | HTTPS | Ambari REST API. Consulte o artigo [Gerir HDInsight utilizar a API do resto Ambari](hdinsight-hadoop-manage-ambari-rest-api.md) |
| WebHCat | 443 | HTTPS | HCatalog REST API. Consulte o artigo [utilizar ramo com laço](hdinsight-hadoop-use-pig-curl.md), [utilizar porco com laço](hdinsight-hadoop-use-pig-curl.md), [utilizar MapReduce com laço](hdinsight-hadoop-use-mapreduce-curl.md) |
| HiveServer2 | 443 | ODBC | Liga-se ao ramo utilizando ODBC. Consulte o artigo [Ligar o Excel ao HDInsight com o controlador de ODBC da Microsoft](hdinsight-connect-excel-hive-odbc-driver.md). |
| HiveServer2 | 443 | JDBC | Liga-se ao ramo utilizando JDBC. Consulte o artigo [ligar ao ramo no HDInsight a utilizar o controlador de Hive JDBC](hdinsight-connect-hive-jdbc-driver.md) |

Seguem-se disponível para os tipos de cluster específico:

| Serviço | Porta | Protocolo |Tipo de cluster | Descrição |
| ------------ | ---- |  ----------- | --- | ----------- |
| Stargate | 443 | HTTPS | HBase | HBase REST API. Consulte o artigo [começar a utilizar o HBase](hdinsight-hbase-tutorial-get-started-linux.md) |
| Lívio | 443 | HTTPS |  Motores | Motores REST API. Consulte o artigo [Submeter motores tarefas remotamente utilizando Lívio](hdinsight-apache-spark-livy-rest-interface.md) |
| Tempestade | 443 | HTTPS | Tempestade | IU da web tempestade. Consulte o artigo [Implementar e gerir topologias tempestade no HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

### <a name="authentication"></a>Autenticação

Têm de ser autenticados todos os serviços expostos publicamente na internet:

| Porta | Credenciais |
| ---- | ----------- |
| 22 ou 23 | As credenciais do utilizador SSH especificadas durante a criação do cluster |
| 443 | O nome de início de sessão (predefinido: administrador,) e palavra-passe que foram definidas durante a criação de cluster |

## <a name="non-public-ports"></a>Portas não público

> [AZURE.NOTE] Alguns serviços só estão disponíveis nos tipos de cluster específico. Por exemplo, HBase só está disponível nos tipos de cluster HBase.

### <a name="hdfs-ports"></a>Portas HDFS

| Serviço | Nós | Porta | Protocolo | Descrição |
| ------- | ------- | ---- | -------- | ----------- | 
| NameNode web IU | Nós de cabeça | 30070 | HTTPS | Web IU para ver o estado atual |
| Serviço de metadados NameNode | nós de cabeça | 8020 | IPC | Metadados do sistema de ficheiros 
| DataNode | Todos os nós de trabalho | 30075 | HTTPS | IU da Web para ver o estado, registos de início, etc. |
| DataNode | Todos os nós de trabalho | 30010 | &nbsp; | Transferência de dados |
| DataNode | Todos os nós de trabalho | 30020 | IPC | Operações de metadados |
| NameNode secundário | Nós de cabeça | 50090 | HTTP | Ponto de verificação para NameNode metadados |

### <a name="yarn-ports"></a>Portas FIO

| Serviço | Nós | Porta | Protocolo | Descrição |
| ------- | ------- | ---- | -------- | ----------- |
| Gestor de recursos web IU | Nós de cabeça | 8088 | HTTP | Web da IU para Gestor de recursos |
| Gestor de recursos web IU | Nós de cabeça | 8090 | HTTPS | Web da IU para Gestor de recursos |
| Interface da administração de Gestor de recursos | nós de cabeça | 8141 | IPC | Para submissões de aplicação (ramo, ramo de servidor, porco, etc.) |
| Programador do Gestor de recursos | nós de cabeça | 8030 | HTTP | Interface administrativa |
| Interface de aplicações do Gestor de recursos | nós de cabeça | 8050 | HTTP |Endereço da interface de Gestor de aplicações |
| NodeManager | Todos os nós de trabalho | 30050 | &nbsp; | O endereço do Gestor de contentor |
| NodeManager web IU | Todos os nós de trabalho | 30060 | HTTP | Interface do Gestor de recursos |
| Endereço da linha cronológica | Nós de cabeça | 10200 | RPC | O serviço RPC do serviço de linha cronológica. |
| Linha cronológica web IU | Nós de cabeça | 8181 | HTTP | Na web de serviço de linha cronológica IU |

### <a name="hive-ports"></a>Ramo de portas

| Serviço | Nós | Porta | Protocolo | Descrição |
| ------- | ------- | ---- | -------- | ----------- |
| HiveServer2 | Nós de cabeça | 10001 | Outras entidades | Serviço para ligar através de programação a ramo (outras entidades/JDBC) |
| HiveServer | Nós de cabeça | 10000 | Outras entidades | Serviço para ligar através de programação a ramo (outras entidades/JDBC) |
| Ramo Metastore | Nós de cabeça | 9083 | Outras entidades | Serviço para ligar através de programação a ramo de metadados (outras entidades/JDBC) |

### <a name="webhcat-ports"></a>Portas WebHCat

| Serviço | Nós | Porta | Protocolo | Descrição |
| ------- | ------- | ---- | -------- | ----------- |
| Servidor de WebHCat | Nós de cabeça | 30111 | HTTP | Web API na parte superior de HCatalog e outros serviços de Hadoop |

### <a name="mapreduce-ports"></a>Portas MapReduce

| Serviço | Nós | Porta | Protocolo | Descrição |
| ------- | ------- | ---- | -------- | ----------- |
| JobHistory | Nós de cabeça | 19888 | HTTP | MapReduce JobHistory web IU |
| JobHistory | Nós de cabeça | 10020 | &nbsp; | Servidor de MapReduce JobHistory |
| ShuffleHandler | &nbsp; | 13562 | &nbsp; | Exporta mapa intermédio de transferências para pedir a redução |

### <a name="oozie"></a>Oozie

| Serviço | Nós | Porta | Protocolo | Descrição |
| ------- | ------- | ---- | -------- | ----------- |
| Servidor de Oozie | Nós de cabeça | 11000 | HTTP | URL do serviço de Oozie |
| Servidor de Oozie | Nós de cabeça | 11001 | HTTP | Porta para a administração Oozie |

### <a name="ambari-metrics"></a>Métricas Ambari

| Serviço | Nós | Porta | Protocolo | Descrição |
| ------- | ------- | ---- | -------- | ----------- |
| Linha cronológica (histórico de aplicação) | Nós de cabeça | 6188 | HTTP | Na web de serviço de linha cronológica IU |
| Linha cronológica (histórico de aplicação) | Nós de cabeça | 30200 | RPC | Na web de serviço de linha cronológica IU |

### <a name="hbase-ports"></a>Portas HBase

| Serviço | Nós | Porta | Protocolo | Descrição |
| ------- | ------- | ---- | -------- | ----------- |
| HMaster | Nós de cabeça | 16000 | &nbsp; | &nbsp; |
| Informações de HMaster IU da Web | Nós de cabeça | 16010 | HTTP | Porta para a web de modelo global de HBase IU |
| Servidor de região | Todos os nós de trabalho | 16020 | &nbsp; | &nbsp; |
| &nbsp; | &nbsp; | 2181 | &nbsp; | A porta que os clientes utilizar para ligar à ZooKeeper |

### <a name="kafka-ports"></a>Portas Kafka

| Serviço | Nós | Porta | Protocolo | Descrição |
| ------- | ------- | ---- | -------- | ----------- |
| Corretor  | Nós de trabalho | 9092 | [Protocolo de invocação Kafka](http://kafka.apache.org/protocol.html) | Utilizado para comunicação de cliente |
| &nbsp; | Nós zookeeper | 2181 | &nbsp; | A porta que os clientes utilizar para ligar à Zookeeper |
