<properties
    pageTitle="Utilizar a ação de Script para instalar Solr num cluster de Hadoop | Microsoft Azure"
    description="Saiba como personalizar HDInsight cluster com Solr utilizando a ação de Script."
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/05/2016"
    ms.author="nitinme"/>

# <a name="install-and-use-solr-on-hdinsight-hadoop-clusters"></a>Instalar e utilizar Solr no HDInsight Hadoop clusters

Saiba como personalizar cluster do Windows baseada em HDInsight com Solr utilizando Script ação e como utilizar Solr para procurar dados. Para obter informações sobre como utilizar Solr com um cluster de Linux, consulte o artigo [instalar e utilizar Solr em clusters HDinsight Hadoop (Linux)](hdinsight-hadoop-solr-install-linux.md).
 
Pode instalar Solr em qualquer tipo de cluster (Hadoop, tempestade, HBase, motores) no Azure HDInsight utilizando *Script ação*. Um exemplo de script para instalar Solr num HDInsight cluster está disponível a partir de um blob armazenamento Azure só de leitura na [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1). 

Exemplo de script funciona apenas com a versão de cluster de HDInsight 3.1. Para mais informações sobre as versões de cluster HDInsight, consulte o artigo [versões de cluster HDInsight](hdinsight-component-versioning.md).

O script de exemplo utilizado neste tópico cria um cluster de Solr baseados no Windows com uma configuração específica. Se pretender configurar o cluster de Solr com diferentes colecções, shards, esquemas, réplicas, etc., tem de modificar o script e Solr binários em conformidade.

**Artigos relacionados**

- [Instalar e utilizar Solr no clusters HDinsight Hadoop (Linux)](hdinsight-hadoop-solr-install-linux.md)
- [Criar Hadoop clusters no HDInsight](hdinsight-provision-clusters.md): informações gerais sobre a criação de HDInsight clusters.
- [Personalizar cluster de HDInsight utilizando a ação de Script][hdinsight-cluster-customize]: informações gerais sobre a personalização de clusters HDInsight utilizando a ação de Script.
- [Acção de Script desenvolver scripts para HDInsight](hdinsight-hadoop-script-actions.md).


## <a name="what-is-solr"></a>O que é Solr?

<a href="http://lucene.apache.org/solr/features.html" target="_blank">Apache Solr</a> é uma plataforma de pesquisa de empresa que permite que os dados de pesquisa com o texto completo eficientes. Enquanto Hadoop permite armazenar e gerir grandes quantidades de dados, Apache Solr fornece as capacidades de pesquisa para obter rapidamente os dados. 

## <a name="install-solr-using-portal"></a>Instalar Solr através do portal

1. Começar a criar um cluster utilizando a opção **Criar PERSONALIZADO** , tal como descrito em [clusters de criar Hadoop HDInsight](hdinsight-provision-clusters.md#portal).
2. Na página **Acções de Script** do assistente, clique em **Adicionar ação de script** para fornecer detalhes sobre a ação de script, conforme apresentado abaixo:

    ![Acção de Script de utilização para personalizar um cluster de] (./media/hdinsight-hadoop-solr-install/hdi-script-action-solr.png "Acção de Script de utilização para personalizar um cluster de")

    <table border='1'>
        <tr><th>Propriedade</th><th>Valor</th></tr>
        <tr><td>Nome</td>
            <td>Especifique um nome para a ação de script. Por exemplo, <b>Solr instalar</b>.</td></tr>
        <tr><td>Script URI</td>
            <td>Especifique o URI Uniform Resource Identifier () para o script que é chamado para personalizar o cluster. Por exemplo, <i>https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1</i></td></tr>
        <tr><td>Tipo de nó</td>
            <td>Especifique os nós onde for executado o script de personalização. Pode escolher <b>todos os nós</b>, <b>apenas nós de cabeça</b>ou <b>apenas nós de trabalho</b>.
        <tr><td>Parâmetros</td>
            <td>Especifique os parâmetros, se necessário pelo script. O script para instalar o Solr não requer quaisquer parâmetros, pelo que pode deixar esta em branco.</td></tr>
    </table>

    Pode adicionar mais do que uma ação de script para instalar vários componentes no cluster. Depois de ter adicionado os scripts, clique em marca de verificação para começar a criar o cluster.


## <a name="use-solr"></a>Utilizar Solr

Tem de iniciar com a indexação Solr com alguns ficheiros de dados. Em seguida, pode utilizar Solr para executar consultas de pesquisa nos dados indexados. Execute os seguintes passos para utilizar Solr num HDInsight cluster:

1. **Utilizar RDP Remote Desktop Protocol () para remoto para cluster HDInsight com Solr instalado**. A partir do portal do Azure, ativar o ambiente de trabalho remoto para cluster que criou com Solr instalado e, em seguida, remote para cluster. Para obter instruções, consulte o artigo [ligar a clusters HDInsight utilizando o RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

2. **Solr de índice remissivo ao carregar ficheiros de dados**. Quando indexar Solr, coloque documentos no mesmo que poderá ter de pesquisa. Para indexar Solr, utilize o RDP para remoto nos cluster, navegue para o ambiente de trabalho, abra a linha de comandos Hadoop e navegue para **C:\apps\dist\solr-4.7.2\example\exampledocs**. Execute o seguinte comando:

        java -jar post.jar solr.xml monitor.xml

    Verá o seguinte resultado na consola:

        POSTing file solr.xml
        POSTing file monitor.xml
        2 files indexed.
        COMMITting Solr index changes to http://localhost:8983/solr/update..
        Time spent: 0:00:01.624

    O utilitário post.jar índices Solr com documentos de duas amostras, **solr.xml** e **monitor.xml**. Estão disponíveis com a instalação Solr o utilitário post.jar e os documentos de exemplo.

3. **Utilizar o dashboard Solr procurar dentro da documentos indexados**. Na sessão RDP ao HDInsight cluster, abra o Internet Explorer e iniciar o dashboard Solr na **solr/http://headnodehost:8983 / #/**. A partir do painel da esquerda, a partir do menu pendente **Selector principais** , selecione **collection1**e dentro desse, clique em **consulta**. Por exemplo, para selecionar e voltar a todos os de documentos no Solr, forneça os seguintes valores:

    * Na caixa de texto **perguntas** , introduza ** \*:**\*. Isto irá devolver todos os documentos que estão indexados no Solr. Se pretender procurar uma cadeia dentro de documentos específica, pode introduzir essa cadeia aqui.
    
    * Na caixa de texto **wt** , selecione o formato de saída. A predefinição é **json**. Clique em **executar a consulta**.

    ![Acção de Script de utilização para personalizar um cluster de] (./media/hdinsight-hadoop-solr-install/hdi-solr-dashboard-query.png "Executar uma consulta no dashboard de Solr")
    
    O resultado devolve a duas de documentos que é utilizada para a indexação Solr. O resultado é semelhante ao seguinte:

            "response": {
                "numFound": 2,
                "start": 0,
                "maxScore": 1,
                "docs": [
                  {
                    "id": "SOLR1000",
                    "name": "Solr, the Enterprise Search Server",
                    "manu": "Apache Software Foundation",
                    "cat": [
                      "software",
                      "search"
                    ],
                    "features": [
                      "Advanced Full-Text Search Capabilities using Lucene",
                      "Optimized for High Volume Web Traffic",
                      "Standards Based Open Interfaces - XML and HTTP",
                      "Comprehensive HTML Administration Interfaces",
                      "Scalability - Efficient Replication to other Solr Search Servers",
                      "Flexible and Adaptable with XML configuration and Schema",
                      "Good unicode support: héllo (hello with an accent over the e)"
                    ],
                    "price": 0,
                    "price_c": "0,USD",
                    "popularity": 10,
                    "inStock": true,
                    "incubationdate_dt": "2006-01-17T00:00:00Z",
                    "_version_": 1486960636996878300
                  },
                  {
                    "id": "3007WFP",
                    "name": "Dell Widescreen UltraSharp 3007WFP",
                    "manu": "Dell, Inc.",
                    "manu_id_s": "dell",
                    "cat": [
                      "electronics and computer1"
                    ],
                    "features": [
                      "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
                    ],
                    "includes": "USB cable",
                    "weight": 401.6,
                    "price": 2199,
                    "price_c": "2199,USD",
                    "popularity": 6,
                    "inStock": true,
                    "store": "43.17614,-90.57341",
                    "_version_": 1486960637584081000
                  }
                ]
              }


4. **Recomendado: criar uma cópia de segurança os dados indexados da Solr ao armazenamento de Blobs do Azure associado ao cluster HDInsight**. Como aconselhável, deverá criar uma cópia de segurança dos dados indexados partir de nós de cluster Solr para armazenamento de Blobs do Azure. Execute os seguintes passos para fazê-lo:

    1. Sessão RDP, abra o Internet Explorer e aponte para o seguinte URL:

            http://localhost:8983/solr/replication?command=backup

        Deverá visualizar uma resposta da seguinte forma:

            <?xml version="1.0" encoding="UTF-8"?>
            <response>
              <lst name="responseHeader">
                <int name="status">0</int>
                <int name="QTime">9</int>
              </lst>
              <str name="status">OK</str>
            </response>

    2. Na sessão remota, navegue para {SOLR_HOME}\{coleção} \data. Para o cluster criado através de script de exemplo, este deve ser **C:\apps\dist\solr-4.7.2\example\solr\collection1\data**. Esta localização, deverá ver uma pasta de instantâneo criada com um nome semelhante ao * *instantâneo.* carimbo** *.

    3. Código postal a pasta instantâneo e carregue-o para o armazenamento de Blobs do Azure. A partir da linha de comandos Hadoop, navegue para a localização da pasta instantâneo utilizando o seguinte comando:

              hadoop fs -CopyFromLocal snapshot._timestamp_.zip /example/data

        Este comando copia o instantâneo para /example/data/dentro do contentor dentro à conta de armazenamento associada ao cluster predefinida.

## <a name="install-solr-using-aure-powershell"></a>Instalar Solr através do Aure PowerShell

Consulte o artigo [Personalizar HDInsight clusters utilizando Script ação](hdinsight-hadoop-customize-cluster.md#call_scripts_using_powershell).  O exemplo demonstra como instalar motores através do Azure PowerShell. Tem de personalizar o script para utilizar [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).

## <a name="install-solr-using-net-sdk"></a>Instalar Solr utilizando .NET SDK

Consulte o artigo [Personalizar HDInsight clusters utilizando Script ação](hdinsight-hadoop-customize-cluster.md#call_scripts_using_azure_powershell). O exemplo demonstra como instalar motores utilizando o SDK .NET. Tem de personalizar o script para utilizar [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).



## <a name="see-also"></a>Consulte também

- [Instalar e utilizar Solr no clusters HDinsight Hadoop (Linux)](hdinsight-hadoop-solr-install-linux.md)
- [Criar Hadoop clusters no HDInsight](hdinsight-provision-clusters.md): informações gerais sobre a criação de HDInsight clusters.
- [Personalizar cluster de HDInsight utilizando a ação de Script][hdinsight-cluster-customize]: informações gerais sobre a personalização de clusters HDInsight utilizando a ação de Script.
- [Acção de Script desenvolver scripts para HDInsight](hdinsight-hadoop-script-actions.md).
- [Instalar e utilizar motores no HDInsight clusters][hdinsight-install-spark]: exemplo de Script ação sobre como instalar motores.
- [Instalar o R HDInsight clusters][hdinsight-install-r]: exemplo de Script ação sobre como instalar R.
- [Instalar Giraph em HDInsight clusters](hdinsight-hadoop-giraph-install.md): exemplo de Script ação sobre como instalar Giraph.


[powershell-install-configure]: powershell-install-configure.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
