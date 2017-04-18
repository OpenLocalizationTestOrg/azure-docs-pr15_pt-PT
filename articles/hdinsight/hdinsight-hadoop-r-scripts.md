<properties
    pageTitle="Utilização R em HDInsight para personalizar clusters | Microsoft Azure"
    description="Saiba como instalar R utilizando Script ação e utilize R sobre clusters de HDInsight."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="jgao"/>

# <a name="install-and-use-r-on-hdinsight-hadoop-clusters"></a>Instalar e utilizar R no HDInsight Hadoop clusters

Saiba como personalizar Windows baseado HDInsight cluster com R utilizando Script ação e clusters de como utilizar R no HDInsight. A [camada premium](https://azure.microsoft.com/pricing/details/hdinsight/) oferta para HDInsight inclui R servidor como parte do seu cluster HDInsight. Isto permite que os scripts de R utilizar MapReduce e motores para executar cálculos distribuídos. Para mais informações, consulte o artigo [começar a utilizar o servidor de R na HDInsight](hdinsight-hadoop-r-server-get-started.md). Para obter informações sobre como utilizar R com um cluster de Linux, consulte o artigo [instalar e utilizar R sobre clusters de HDinsight Hadoop (Linux)](hdinsight-hadoop-r-scripts-linux.md).
 
Pode instalar R em qualquer tipo de cluster (Hadoop, tempestade, HBase, motores) no Azure HDInsight utilizando *Script ação*. Um exemplo de script para instalar R num HDInsight cluster está disponível a partir de um blob armazenamento Azure só de leitura na [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1). 

**Artigos relacionados**

- [Instalar e utilizar R no clusters HDinsight Hadoop (Linux)](hdinsight-hadoop-r-scripts-linux.md)
- [Criar Hadoop clusters no HDInsight](hdinsight-provision-clusters.md): informações gerais sobre a criação de HDInsight clusters
- [Personalizar cluster de HDInsight utilizando a ação de Script][hdinsight-cluster-customize]: informações gerais sobre a personalização de clusters HDInsight utilizando a ação de Script
- [Desenvolver scripts de acção de Script para HDInsight](hdinsight-hadoop-script-actions.md)

## <a name="what-is-r"></a>O que é R?

O <a href="http://www.r-project.org/" target="_blank">Project R para computação estatísticas de</a> é um abrir origem de idioma e ambiente para computação de estatística. R fornece centenas de funções de estatística compilação-in e as suas próprias linguagem de programação que combina aspectos de programação funcional e orientada para objectos. Fornece também capacidades gráficas extensas. R é o ambiente de programação preferido para mais profissional estaticistas e cientistas numa grande variedade de campos.

R é compatível com o armazenamento de Blobs do Azure (WASB) para que possam ser processados dados armazenados aí utilizar R num HDInsight.  

## <a name="install-r"></a>Instalar R

Um [exemplo de script](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1) para instalar R num HDInsight cluster está disponível a partir de um blob só de leitura no armazenamento do Windows Azure. Esta secção fornece instruções sobre como utilizar o script de exemplo ao criar o cluster utilizando o Portal do Azure.

> [AZURE.NOTE] Exemplo de script foi introduzido com a versão de cluster HDInsight 3.1. Para mais informações sobre as versões de cluster HDInsight, consulte o artigo [versões de cluster HDInsight](hdinsight-component-versioning.md).

1. Quando cria um cluster de HDInsight a partir do Portal, clique em **Configuração opcional**e, em seguida, clique em **Ações de Script**.
2. Na página **Acções de Script** , introduza os seguinte valores:

    ![Acção de Script de utilização para personalizar um cluster de] (./media/hdinsight-hadoop-r-scripts/hdi-r-script-action.png "Acção de Script de utilização para personalizar um cluster de")

    <table border='1'>
        <tr><th>Propriedade</th><th>Valor</th></tr>
        <tr><td>Nome</td>
            <td>Especifique um nome para a ação de script, por exemplo, <b>R instalar</b>.</td></tr>
        <tr><td>Script URI</td>
            <td>Especifique o URI para o script que é chamado para personalizar o cluster, por exemplo, <i>https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1</i></td></tr>
        <tr><td>Tipo de nó</td>
            <td>Especifique os nós onde for executado o script de personalização. Pode escolher <b>Todos os nós</b>, <b>apenas nós de cabeça</b>ou <b>nós de trabalho</b> apenas.
        <tr><td>Parâmetros</td>
            <td>Especifique os parâmetros, se necessário pelo script. No entanto, o script para instalar R não requer quaisquer parâmetros, pelo que pode deixar esta em branco.</td></tr>
    </table>

    Pode adicionar mais do que uma ação de script para instalar vários componentes no cluster. Depois de ter adicionado os scripts, clique na marca de verificação para iniciar crating cluster.

Também pode utilizar o script instalar R em HDInsight utilizando o Azure PowerShell ou o .NET SDK HDInsight. São fornecidas instruções para que estes procedimentos neste artigo.

## <a name="run-r-scripts"></a>Executar scripts de R
Esta secção descreve como executar um script R no cluster Hadoop com HDInsight.

1. **Estabelecer uma ligação de ambiente de trabalho remoto para cluster**: a partir do Portal do, ativar o ambiente de trabalho remoto para cluster criada com R instalado e, em seguida, ligar ao cluster. Para obter instruções, consulte o artigo [ligar a clusters HDInsight utilizando o RDP](hdinsight-administer-use-management-portal.md#rdp).

2. **Abra a consola R**: instalação R o coloca uma ligação à consola R no ambiente de trabalho do nó cabeça. Clique na mesma para abrir a consola R.

3. **Executar o script R**: script R o pode ser executado diretamente a partir da consola de R ao colá-lo, selecioná-la e premir ENTER. Eis um script de exemplo simples que gera os números de 1 a 100 e, em seguida, multiplica-los por 2.

        library(rmr2)
        library(rhdfs)
        ints = to.dfs(1:100)
        calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))
        from.dfs(calc)

As duas primeiras linhas ligar as bibliotecas de RHadoop que são instaladas com R. A linha final imprime os resultados da consola do. O resultado deve ter este aspeto:

    [1,]  1 2
    [2,]  2 4
    .
    .
    .
    [98,]  98 196
    [99,]  99 198
    [100,] 100 200


## <a name="install-r-using-aure-powershell"></a>Instalar R através do Aure PowerShell

Consulte o artigo [Personalizar HDInsight clusters utilizando Script ação](hdinsight-hadoop-customize-cluster.md#call_scripts_using_powershell).  O exemplo demonstra como instalar motores através do Azure PowerShell. Tem de personalizar o script para utilizar [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1).

## <a name="install-r-using-net-sdk"></a>Instalar R utilizando .NET SDK

Consulte o artigo [Personalizar HDInsight clusters utilizando Script ação](hdinsight-hadoop-customize-cluster.md#call_scripts_using_azure_powershell). O exemplo demonstra como instalar motores utilizando o SDK .NET. Tem de personalizar o script para utilizar [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps11).


## <a name="see-also"></a>Consulte também

- [Instalar e utilizar R no clusters HDinsight Hadoop (Linux)](hdinsight-hadoop-r-scripts-linux.md)
- [Criar Hadoop clusters no HDInsight](hdinsight-provision-clusters.md): informações gerais sobre a criação de HDInsight clusters
- [Personalizar cluster de HDInsight utilizando a ação de Script][hdinsight-cluster-customize]: informações gerais sobre a personalização de clusters HDInsight utilizando a ação de Script
- [Desenvolver scripts de acção de Script para HDInsight](hdinsight-hadoop-script-actions.md)
- [Instalar e utilizar motores no HDInsight clusters][hdinsight-install-spark]: exemplo de Script ação sobre como instalar motores
- [Instalar Giraph em HDInsight clusters](hdinsight-hadoop-giraph-install.md): exemplo de Script ação sobre como instalar Giraph
- [Instalar Solr em HDInsight clusters](hdinsight-hadoop-solr-install-linux.md): exemplo de Script ação sobre como instalar Solr.

[powershell-install-configure]: powershell-install-configure.md
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
[hdinsight-install-spark]: hdinsight-apache-spark-jupyter-spark-sql.md
