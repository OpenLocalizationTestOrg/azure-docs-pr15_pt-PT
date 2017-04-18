<properties
pageTitle="Migrar do HDInsight baseados no Windows para o HDInsight baseado em Linux | Azure"
description="Saiba como migrar a partir de um cluster de HDInsight baseados no Windows para um cluster de baseado em Linux HDInsight."
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
ms.date="10/28/2016"
ms.author="larryfr"/>

#<a name="migrate-from-a-windows-based-hdinsight-cluster-to-a-linux-based-cluster"></a>Migrar a partir de um cluster de HDInsight baseados no Windows para um cluster baseado em Linux

Enquanto HDInsight baseados no Windows fornece uma forma fácil de utilizar Hadoop na nuvem, poderá descobrir que precisa de um cluster baseado em Linux para tirar partido das ferramentas e tecnologias que são necessárias para a sua solução. Muitas coisas no ecossistema Hadoop sejam desenvolvidas nos sistemas de Linux e algumas poderão não estar disponíveis para utilização com o HDInsight baseados no Windows. Para além disso, muitos livros, vídeos e outros materiais de formação partem do princípio de que está a utilizar um sistema de Linux quando trabalha com Hadoop.

Este documento fornece detalhes sobre as diferenças entre HDInsight no Windows e Linux e orientação sobre como migrar das cargas de trabalho existentes para um cluster baseado em Linux.

> [AZURE.NOTE] HDInsight clusters utilizam suporte de longo prazo Ubuntu (LTS) como o sistema operativo para os nós no cluster. Para obter informações sobre a versão do Ubuntu disponível com HDInsight, juntamente com outras informações de controlo de versões componente, consulte o artigo [versões dos componentes HDInsight](hdinsight-component-versioning.md).

## <a name="migration-tasks"></a>Tarefas de migração

Fluxo de trabalho geral para migração é da seguinte forma.

![Diagrama de fluxo de trabalho de migração](./media/hdinsight-migrate-from-windows-to-linux/workflow.png)

1.  Leia cada secção deste documento para compreender as alterações que podem ser necessárias quando migrar o seu fluxo de trabalho existente, tarefas, etc. para um cluster baseado em Linux.

2.  Crie um cluster baseado em Linux como um ambiente do assurance teste/qualidade. Para mais informações sobre como criar um cluster baseado em Linux, consulte o artigo [baseado em criar Linux clusters de HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

3.  Copie tarefas já existentes, origens de dados e sumidouros para o novo ambiente. Ver os dados de cópia para a secção de ambiente de teste para obter mais detalhes.

4.  Efetue testes de validação para se certificar de que as tarefas funcionam como esperado no novo cluster.

Assim que tiver confirmado que tudo está a funcionar como esperado, agende tempo de inatividade para a migração. Durante este tempo de inatividade, efetue as seguintes ações.

1.  Quaisquer dados breves armazenados localmente em nós de cluster de cópia de segurança. Por exemplo, se tiver dados armazenados diretamente num nó de cabeça.

2.  Elimine o cluster baseados no Windows.

3.  Crie um cluster baseado em Linux utilizando o arquivo de dados predefinido mesmo que o cluster baseados no Windows utilizado. Isto permitirá que o novo cluster continuar a trabalhar com os seus dados de produção existentes.

4.  Importe quaisquer dados breves que cópia de segurança.

5.  Iniciar tarefas/continue a utilizar o novo cluster de processamento.

### <a name="copy-data-to-the-test-environment"></a>Copiar dados para o ambiente de teste

Existem vários métodos para copiar os dados e as tarefas, no entanto as duas abordadas nesta secção são os métodos mais simples para diretamente move ficheiros para um cluster de teste.

#### <a name="hdfs-dfs-copy"></a>Copiar HDFS DFS

Pode utilizar o comando Hadoop HDFS diretamente copiar dados a partir do armazenamento para o seu cluster de produção existente, para o armazenamento de um novo cluster de teste através dos seguintes passos.

1. Encontre as informações de contentor de armazenamento conta e predefinidos, para o seu cluster existente. Pode fazê-lo utilizando o seguinte script do Azure PowerShell.

        $clusterName="Your existing HDInsight cluster name"
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        write-host "Storage account name: $clusterInfo.DefaultStorageAccount.split('.')[0]"
        write-host "Default container: $clusterInfo.DefaultStorageContainer"

2. Siga os passos no clusters baseado em criar Linux HDInsight documento para criar um novo ambiente de teste. Parar o antes de criar o cluster e, em vez disso, selecione **Configuração opcional**.

3. Pá configuração opcional, selecione **Contas ligadas de armazenamento**.

4. Selecione **Adicionar uma chave de armazenamento**e, quando lhe for pedido, selecione a conta de armazenamento que foi devolvida pelo script do PowerShell no passo 1. Clique em **Selecionar** cada pá de fechá-las. Por fim, crie o cluster.

5. Depois de ter sido criado cluster, ligar à mesma, utilizando **SSH.** Se não estiver familiarizado com a utilização de SSH com HDInsight, consulte um dos seguintes artigos.

    * [Utilizar SSH com baseado em Linux HDInsight a partir dos clientes do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

    * [Utilizar SSH com baseado em Linux HDInsight a partir dos clientes Linux, Unix e Mac](hdinsight-hadoop-linux-use-ssh-unix.md)

6. A partir do sessão SSH, utilize o seguinte comando para copiar os ficheiros da conta de armazenamento ligada para a nova conta de armazenamento predefinido. Substitua o contentor e conta com o contentor e informações de conta devolvidas pelo script do PowerShell no passo 1. Substitua o caminho para dados com o caminho para um ficheiro de dados.

        hdfs dfs -cp wasbs://CONTAINER@ACCOUNT.blob.core.windows.net/path/to/old/data /path/to/new/location

    [AZURE.NOTE] Se a estrutura do directório que contém os dados não existir no ambiente de teste, pode criá-lo utilizando o seguinte comando.

        hdfs dfs -mkdir -p /new/path/to/create

    O `-p` mudar permite a criação de todos os directórios no caminho.

#### <a name="direct-copy-between-azure-storage-blobs"></a>Copiar direta entre blobs de armazenamento do Windows Azure

Em alternativa, recomendamos que utilize o `Start-AzureStorageBlobCopy` cmdlet do PowerShell do Azure para copiar blobs entre contas de armazenamento fora do HDInsight. Para obter mais informações, consulte o artigo como para gerir a secção de Blobs do Azure de utilizar o PowerShell Azure com armazenamento do Windows Azure.

##<a name="client-side-technologies"></a>Tecnologias do lado do cliente

Em geral, tecnologias de lado do cliente, tais como [os cmdlets do Azure PowerShell](../powershell-install-configure.md), [Azure clip](../xplat-cli-install.md) ou o [.NET SDK para Hadoop](https://hadoopsdk.codeplex.com/) irão continuar a funcionam da mesma com baseado em Linux clusters, tal como dependem de REST APIs que são as mesmas em ambos os tipos de cluster SO.

##<a name="server-side-technologies"></a>Tecnologias do lado do servidor

A tabela seguinte dá-lhe no migrar componentes do lado do servidor que são janelas específicas.

| Se estiver a utilizar esta tecnologia... | Efetuar esta ação... |
| ----- | ----- |
| **PowerShell** (scripts do lado do servidor, incluindo acções de Script utilizado durante a criação de cluster) | Reescrever como scripts de festa. Para ações de Script, consulte o artigo [baseado em Personalizar Linux HDInsight com ações de Script](hdinsight-hadoop-customize-cluster-linux.md) e o [desenvolvimento de ação de Script para baseado em Linux HDInsight](hdinsight-hadoop-script-actions-linux.md). |
| **Clip Azure** (scripts do lado do servidor) | Enquanto o clip do Azure está disponível no Linux, não vêm pré-instaladas em nós de cabeça do cluster HDInsight. Se for necessário para scripting do lado do servidor, consulte o artigo [instalar o clip do Azure](../xplat-cli-install.md) para obter informações sobre como instalar baseado em Linux plataformas. |
| **Componentes .NET** | .NET não é totalmente suportada em clusters baseado em Linux HDInsight. Baseado em Linux tempestade no HDInsight clusters criadas após 28/10/2017 suporte topologias c# tempestade utilizando o framework SCP.NET. Obter suporte adicional para o .NET será adicionado no futuro atualizações. |
| **Componentes de Win32 ou outras tecnologias apenas de Windows** | Orientações depende o componente ou tecnologia; poderá conseguir localizar uma versão que seja compatível com Linux ou poderá ter de encontrar uma solução alternativa ou reescrita este componente. |

##<a name="cluster-creation"></a>Criação de cluster

Esta secção fornece informações sobre as diferenças na criação de cluster.

### <a name="ssh-user"></a>SSH utilizador

Baseado em Linux clusters HDInsight utilizam o protocolo de **Shell seguro (SSH)** para fornecer acesso remoto para os nós de cluster. Ao contrário clusters com base no ambiente de trabalho remoto para Windows, maior parte dos clientes SSH não fornecem uma experiência de utilizador gráficas, mas em vez disso, fornece uma linha de comandos que permite-lhe executar comandos no cluster. Alguns clientes (como [MobaXterm](http://mobaxterm.mobatek.net/),) fornecem um browser de sistema do ficheiro de gráficos para além de um controlo remoto da linha de comandos.

Durante a criação de cluster, tem de fornecer um utilizador SSH e uma **palavra-passe** ou **certificado de chave pública** para autenticação.

Recomendamos que utilize o certificado de chave pública, tal como está mais seguro que utilizando uma palavra-passe. Autenticação de certificado funciona através da criação de um par com a sessão iniciada público/privado, em seguida, fornecer a chave pública quando criar o cluster. Quando se liga ao servidor utilizando SSH, a chave privada no cliente fornece autenticação para a ligação.

Para obter mais informações sobre como utilizar SSH com HDInsight, consulte:

- [Utilizar SSH com HDInsight a partir de clientes do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

- [Utilizar SSH com HDInsight a partir dos clientes Linux, Unix e OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

### <a name="cluster-customization"></a>Personalização de cluster

**Acções de script** utilizada com clusters baseados em Linux tem de ser escritos em script de festa. Enquanto acções de Script pode ser utilizadas durante a criação de cluster, para baseado em Linux clusters também podem ser utilizados para desempenhar personalização depois de um cluster é para cima e a ser executado. Para mais informações, consulte o artigo [baseado em Personalizar Linux HDInsight com ações de Script](hdinsight-hadoop-customize-cluster-linux.md) e o [desenvolvimento de ação de Script para baseado em Linux HDInsight](hdinsight-hadoop-script-actions-linux.md).

Funcionalidade de personalização outra está **arranque**. Para clusters do Windows, esta opção permite-lhe especificar a localização de bibliotecas adicionais para utilização com ramo. Após a criação de cluster, são automaticamente disponíveis para utilização com consultas ramo sem precisar de utilizar estas bibliotecas `ADD JAR`.

Arranque para baseado em Linux clusters não fornece esta funcionalidade. Em alternativa, utilize a ação de script documentada nas [bibliotecas de adicionar Hive durante a criação de cluster](hdinsight-hadoop-add-hive-libraries.md).

### <a name="virtual-networks"></a>Redes virtuais

HDInsight baseados no Windows clusters só funciona com redes virtuais clássico, enquanto baseado em Linux HDInsight clusters requerem redes de virtuais do Gestor de recursos. Se tiver recursos numa rede Virtual clássica que cluster Linux HDInsight tem de ligar a, consulte o artigo [ligar uma rede Virtual clássica a uma rede Virtual do Gestor de recursos](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

Para mais informações sobre os requisitos de configuração para utilizar as redes virtuais do Azure com HDInsight, consulte o artigo [funcionalidades de expandir HDInsight, utilizando uma rede Virtual](hdinsight-extend-hadoop-virtual-network.md).

##<a name="management-and-monitoring"></a>Monitorização e gestão

Muitos dos web IU possa ter utilizado com HDInsight baseados no Windows, como o histórico da tarefa ou fio IU, estão disponíveis através de Ambari. Além disso, a vista de ramo de Ambari fornece uma maneira de executar consultas de ramo utilizando o browser. IU do Web de Ambari está disponível no baseado em Linux conjuntos de sectores https://CLUSTERNAME.azurehdinsight.net.

Para obter mais informações sobre como trabalhar com Ambari, consulte os seguintes documentos:

- [Ambari Web](hdinsight-hadoop-manage-ambari.md)

- [Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)

### <a name="ambari-alerts"></a>Ambari alertas

Ambari tem um sistema de alerta que pode indicar-lhe de potenciais problemas com o cluster. Alertas aparecem como entradas amarelas ou vermelhas na IU do Web Ambari, no entanto, também poderá recuperá-los através da API REST.

> [AZURE.IMPORTANT] Os alertas de Ambari indicam que existem *poderá* ser um problema, que não existem não *é* um problema. Por exemplo, poderá receber um alerta que não podem ser acedidas HiveServer2, mesmo que pode ser acedido normalmente.
>
> Muitos os alertas estão implementados como consultas com base no intervalo relativamente a um serviço e esperar uma resposta um período de tempo específico. Para que o alerta não seja necessariamente significa que o serviço está para baixo, basta que a mesma não devolve resultados o período de tempo esperado.

Em geral, deve avaliar se um alerta tenha sido ocorrer por um determinado período ou reflete problemas de utilizador que anteriormente foram relatados com o cluster antes de efetuar uma ação no mesmo.

##<a name="file-system-locations"></a>Localizações do ficheiro de sistema

O sistema de ficheiros de cluster Linux é apresentado diferente clusters HDInsight baseados no Windows. Utilize a tabela seguinte para encontrar geralmente utilizada os ficheiros.

| Precisa de localizar... | Está localizado... |
| ----- | ----- |
| Configuração | `/etc`. Por exemplo,`/etc/hadoop/conf/core-site.xml` |
| Ficheiros de registo | `/var/logs` |
| Dados de Hortonworks plataforma (HDP) | `/usr/hdp`. Existem dois directórios localizado aqui, que é a versão atual do HDP (por exemplo, `2.2.9.1-1`,) e `current`. O `current` directory contém symbolic ligações para ficheiros e directórios localizados no diretório de número de versão e for fornecida como um meio cómodo como aceder a ficheiros HDP desde a versão número será alterado, tal como a versão HDP é atualizada. |
| hadoop streaming.jar | `/usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar` |

Em geral, se souber o nome do ficheiro, pode utilizar o comando seguinte a partir de uma sessão SSH para encontrar o caminho do ficheiro:

    find / -name FILENAME 2>/dev/null

Também pode utilizar carateres universais com o nome do ficheiro. Por exemplo, `find / -name *streaming*.jar 2>/dev/null` irá devolver o caminho para quaisquer ficheiros de para caixa que contenham a palavra transmissão como parte do nome do ficheiro.

##<a name="hive-pig-and-mapreduce"></a>Ramo, porco e MapReduce

Porco e MapReduce das cargas de trabalho são muito semelhantes em baseado em Linux clusters - a diferença principal é se utilizar o ambiente de trabalho remoto para ligar a um cluster baseados no Windows e executar tarefas, que irá utilizar o SSH com clusters baseados em Linux.

- [Utilizar porco com SSH](hdinsight-hadoop-use-pig-ssh.md)

- [Utilizar MapReduce com SSH](hdinsight-hadoop-use-mapreduce-ssh.md)

### <a name="hive"></a>Ramo

O gráfico seguinte dá-lhe sobre como migrar o seu cargas de trabalho do ramo.

| No baseados no Windows, utilizo... | No baseado em Linux... |
| ----- | ----- |
| **Editor de ramo** | [Ramo de vista no Ambari](hdinsight-hadoop-use-hive-ambari-view.md) |
| `set hive.execution.engine=tez;`Para ativar Tez | Tez é o motor de execução predefinido baseado em Linux clusters, por isso já não é necessária uma instrução set. |
| Ficheiros CMD ou scripts no servidor chamado como parte de uma tarefa de ramo | utilizar scripts de festa |
| `hive`comando a partir do ambiente de trabalho remoto | Utilize [Beeline](hdinsight-hadoop-use-hive-beeline.md) ou [Hive a partir de uma sessão SSH](hdinsight-hadoop-use-hive-ssh.md) |

##<a name="storm"></a>Tempestade

| No baseados no Windows, utilizo... | No baseado em Linux... |
| ----- | ----- |
| Dashboard de tempestade | O Dashboard tempestade não está disponível. Consulte o artigo [Implementar e gerir tempestade topologias no baseado em Linux HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md) para formas de submeter topologias |
| Tempestade IU | IU de tempestade está disponível em https://CLUSTERNAME.azurehdinsight.net/stormui |
| Visual Studio para criar, implementar e gerir topologias c# ou híbrido | Visual Studio pode ser utilizado para criar, implementar e gerir c# (SCP.NET) ou topologias híbrido no baseado em Linux tempestade em clusters HDInsight criadas após 28/10/2017. |

##<a name="hbase"></a>HBase

Em baseado em Linux clusters, é o principal znode HBase `/hbase-unsecure`. Tem de definir esta configuração para qualquer cliente Java aplicações que utilizam nativo HBase Java API.

Consulte o artigo [criar uma aplicação baseada em Java HBase](hdinsight-hbase-build-java-maven.md) para um cliente de exemplo que configura este valor.

##<a name="spark"></a>Motores

Motores clusters foram disponíveis no Windows clusters durante a janela de pré-visualização. No entanto, versão, motores só está disponível com clusters baseado em Linux. Não existe nenhum caminho de migração a partir de um cluster de pré-visualização de motores baseados no Windows para um cluster de baseado em Linux motores de lançamento.

##<a name="known-issues"></a>Problemas conhecidos

### <a name="azure-data-factory-custom-net-activities"></a>Azure atividades .NET personalizadas fábrica de dados

Atualmente, não são suportadas Azure actividades .NET personalizadas fábrica de dados em clusters baseado em Linux HDInsight. Em vez disso, deve utilizar um dos seguintes métodos para implementar o atividades personalizadas como parte do seu pipeline ADF.

-   Execute actividades .NET no conjunto de lote Azure. Consulte a secção de serviço de utilização Azure lote ligadas das [atividades personalizadas de utilização no pipeline fábrica de dados do Azure](../data-factory/data-factory-use-custom-activities.md#AzureBatch)

-   Implemente a atividade como uma atividade de MapReduce. Para mais informações, consulte [Invocar programas MapReduce a partir de dados de fábrica do mesmo](../data-factory/data-factory-map-reduce.md) .

### <a name="line-endings"></a>Terminações das linhas

Em geral, terminações das linhas nos sistemas baseados no Windows utilizam CRLF, enquanto sistemas baseados em Linux utilizam LF. Se produzir ou esperar dados com terminações das linhas CRLF, poderá ter modificar o produtor ou consumidores para trabalhar com o fim da linha de LF.

Por exemplo, através do PowerShell do Azure a consulta HDInsight num cluster baseados no Windows irão devolver dados com CRLF. A mesma consulta com um cluster de Linux irá devolver LF. Em muitos casos, isto é indiferente para o consumidor de dados, no entanto, devem ser pesquisado antes de migrar a um cluster de Linux.

Se tiver scripts que serão executadas diretamente em nós de cluster de Linux (tal como um script Python utilizado com ramo ou uma tarefa de MapReduce), deve utilizar sempre LF como o fim da linha. Se utilizar CRLF, poderá ver erros ao executar os scripts num cluster baseado em Linux.

Se souber que os scripts não contenham cadeias com carateres de CR incorporados, em volume altere respectivo linha utilizando um dos seguintes métodos:

-   **Se tiver scripts que planeia na carregar para o cluster**, utilize as seguintes instruções do PowerShell para alterar o respectivo linha de CRLF para LF antes de carregar o script ao cluster.

        $original_file ='c:\path\to\script.py'
        $text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
        [IO.File]::WriteAllText($original_file, $text)

-   **Se tiver scripts que já sejam encontrem no armazenamento utilizado pelo cluster**, pode utilizar o comando seguinte a partir de uma sessão SSH ao cluster baseado em Linux para modificar o script.

        hdfs dfs -get wasbs:///path/to/script.py oldscript.py
        tr -d '\r' < oldscript.py > script.py
        hdfs dfs -put -f script.py wasbs:///path/to/script.py

##<a name="next-steps"></a>Próximos passos

-   [Saiba como criar clusters baseado em Linux HDInsight](hdinsight-hadoop-provision-linux-clusters.md)

-   [Ligar a um cluster baseado em Linux com SSH a partir de um cliente do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

-   [Ligar a um cluster baseado em Linux com SSH a partir de um cliente Linux, Unix ou Mac](hdinsight-hadoop-linux-use-ssh-unix.md)

-   [Gerir um cluster baseado em Linux utilizando Ambari](hdinsight-hadoop-manage-ambari.md)
