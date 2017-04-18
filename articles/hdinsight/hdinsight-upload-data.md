<properties
    pageTitle="Carregar dados para trabalhos de Hadoop no HDInsight | Microsoft Azure"
    description="Saiba como carregar e aceder aos dados para trabalhos de Hadoop no HDInsight utilizando o clip do Azure, Explorador de armazenamento do Azure, Azure PowerShell, a linha de comandos Hadoop ou Sqoop."
    services="hdinsight,storage"
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
    ms.date="08/10/2016"
    ms.author="jgao"/>



#<a name="upload-data-for-hadoop-jobs-in-hdinsight"></a>Carregar dados para trabalhos de Hadoop no HDInsight

Azure HDInsight fornece um sistema de ficheiros do Hadoop distribuído de completa (HDFS) através de armazenamento de Blobs do Azure. Foi concebido como uma extensão HDFS para fornecer uma experiência totalmente integrada aos clientes. Permite que o conjunto completo de componentes no ecossistema Hadoop para trabalhar com diretamente nos dados que gere. Armazenamento de Blobs do Azure e o HDFS são sistemas de distintos ficheiros que estão otimizados para o armazenamento de dados e cálculos nesses dados. Para obter informações sobre os benefícios da utilização de armazenamento de Blobs do Azure, consulte o artigo [armazenamento de Blobs do Azure de utilização com HDInsight][hdinsight-storage].

**Pré-requisitos**

Tenha em atenção os seguintes requisitos antes de começar:

* Um cluster de Azure HDInsight. Para obter instruções, consulte o artigo [Introdução ao Azure HDInsight] [ hdinsight-get-started] ou [aprovisionar HDInsight clusters][hdinsight-provision].

##<a name="why-blob-storage"></a>Por que motivo blob armazenamento?

Azure HDInsight clusters são normalmente implementados para executar tarefas MapReduce e clusters desaparecem depois de concluir a estas tarefas. Manter os dados na HDFS clusters após cálculos seria uma forma dispendiosa para armazenar estes dados. Armazenamento de Blobs do Azure é uma capacidade altamente disponível, altamente dimensionável, alta, a opção de armazenamento de baixo custo e possa para os dados que está a ser processadas utilizando HDInsight. Armazenar os dados num blob permite clusters HDInsight que são usados para cálculo a ser lançadas com segurança sem perder os dados.

###<a name="directories"></a>Diretórios

Azure contentores de armazenamento de BLOBs armazenam dados como pares valor/chave e não haja nenhuma hierarquia de diretório. No entanto, o caráter "/" pode ser utilizado dentro do nome da chave para que esta apareça como se um ficheiro está armazenado dentro de uma estrutura de diretório. HDInsight vê estes como se fossem directórios reais.

Por exemplo, a chave de um blob poderá *input/log1.txt*. Não existe nenhum real directório "entrado", mas devido a presença de um caráter de "/" no nome da chave, tem o aspeto do caminho do ficheiro.

Por esta razão, se utilizar ferramentas Azure Explorer possível que existam alguns ficheiros de 0 bytes. Estes ficheiros servem para fins de dois:

- Se existirem pastas vazias, eles marcar a existência da pasta. Armazenamento de Blobs do Azure é inteligente saber que se existir um blob denominado foo/barra, existe uma pasta denominada **foo**. Mas é a única forma de significam uma Esvaziar pasta denominada **foo** fazendo com este ficheiro especial 0 bytes no local.

- Detêm metadados especial que são necessário para o sistema de ficheiro Hadoop, nomeadamente as permissões e proprietários para as pastas.

##<a name="command-line-utilities"></a>Utilitários da linha de comandos

A Microsoft fornece os seguintes utilitários para funcionar com o armazenamento de Blobs do Azure:

| Ferramenta | Linux | OS X | Windows |
| ---- |:-----:|:----:|:-------:|
| [Interface de comandos Azure][azurecli] | ✔ | ✔ | ✔ |
| [Azure PowerShell][azure-powershell] | | | ✔ |
| [AzCopy][azure-azcopy] | | | ✔ |
| [Comando Hadoop](#commandline) | ✔ | ✔ | ✔ |

> [AZURE.NOTE] Enquanto o Azure clip, PowerShell do Azure e AzCopy podem todos serão utilizadas a partir do Azure externo, o Hadoop comando só está disponível no HDInsight cluster e só permite carregar os dados a partir do sistema de ficheiros local para o armazenamento de Blobs do Azure.

###<a id="xplatcli"></a>Clip Azure

O clip do Azure é uma ferramenta de em diferentes plataformas que permite-lhe gerir serviços Azure. Utilize os passos seguintes para carregar dados para o armazenamento de Blobs do Azure:

[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

1. [Instalar e configurar o clip do Azure para Mac, Linux e Windows](../xplat-cli-install.md).

2. Abra uma linha de comandos, festa ou outro shell e utilize o seguinte para autenticar à sua subscrição do Azure.

        azure login

    Quando lhe for pedido, introduza o nome de utilizador e palavra-passe para a sua subscrição.

3. Introduza o seguinte comando para a lista de contas de armazenamento para a sua subscrição:

        azure storage account list

4. Selecione a conta de armazenamento que contém o blob que pretende trabalhar e, em seguida, utilize o seguinte comando para obter a chave para esta conta:

        azure storage account keys list <storage-account-name>

    Isto deve devolver chaves **primárias** e **secundário** . Uma vez que será utilizada nos próximos passos, copie o valor de chave **primária** .

5. Utilize o seguinte comando para obter uma lista de membros em contentores de BLOBs dentro da conta de armazenamento:

        azure storage container list -a <storage-account-name> -k <primary-key>

6. Utilize os comandos seguintes para carregar e transferir ficheiros para o blob:

    * Para carregar um ficheiro:

            azure storage blob upload -a <storage-account-name> -k <primary-key> <source-file> <container-name> <blob-name>

    * Para transferir um ficheiro:

            azure storage blob download -a <storage-account-name> -k <primary-key> <container-name> <blob-name> <destination-file>

> [AZURE.NOTE] Se sempre estiver a trabalhar com a mesma conta de armazenamento, pode definir as seguintes variáveis de ambiente em vez de especificar a conta e chave para cada comando:
>
> * **AZURE\_armazenamento\_conta**: O nome da conta de armazenamento
>
> * **AZURE\_armazenamento\_acesso\_chave**: A chave de conta de armazenamento

###<a id="powershell"></a>Azure PowerShell

Azure PowerShell é um ambiente de scripts que pode utilizar para controlar e automatizar a implementação e gestão dos seus das cargas de trabalho no Azure. Para obter informações sobre como configurar a sua estação de trabalho para executar o Azure PowerShell, consulte o artigo [instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell.md)]

**Para carregar um ficheiro local para o armazenamento de Blobs do Azure**

1. Abra a consola do Azure PowerShell, tal como indicado na [instalar e configurar o Azure PowerShell](../powershell-install-configure.md).
2. Defina os valores das variáveis primeiros cinco no script seguinte:

        $resourceGroupName = "<AzureResourceGroupName>"
        $storageAccountName = "<StorageAccountName>"
        $containerName = "<ContainerName>"

        $fileName ="<LocalFileName>"
        $blobName = "<BlobName>"

        # Get the storage account key
        $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
        # Create the storage context object
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        # Copy the file from local workstation to the Blob container
        Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -context $destContext

3. Cole o script na consola do Azure PowerShell executá-la para copiar o ficheiro.

Por exemplo scripts de PowerShell que criou para trabalhar com HDInsight, consulte o artigo [Ferramentas HDInsight](https://github.com/blackmist/hdinsight-tools).

###<a id="azcopy"></a>AzCopy

AzCopy é uma ferramenta de linha de comandos que foi concebida para simplificar a tarefa de transferência de dados para e terminar uma conta de armazenamento do Windows Azure. Pode utilizá-la como uma ferramenta de autónomo ou incorporar esta ferramenta numa aplicação existente. [Transferir AzCopy][azure-azcopy-download].

A sintaxe de AzCopy é:

    AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]

Para obter mais informações, consulte o artigo [AzCopy - carregar/transferir ficheiros para Blobs do Azure][azure-azcopy].


###<a id="commandline"></a>Linha de comandos Hadoop

A linha de comandos Hadoop só se torna útil para armazenar dados para o armazenamento de BLOBs quando os dados já se encontra no nó de cabeça cluster.

Para poder utilizar o comando Hadoop, tem primeiro de ligar headnode utilizando um dos seguintes métodos:

* **HDInsight baseados no Windows**: [Ligar utilizando o ambiente de trabalho remoto](hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp)

* **Baseado em Linux HDInsight**: ligar através do SSH ([o comando SSH](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster) ou [betumes](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster))

Quando estiver ligado, pode utilizar a seguinte sintaxe para carregar um ficheiro para o armazenamento.

    hadoop -copyFromLocal <localFilePath> <storageFilePath>

Por exemplo,`hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Uma vez que é o sistema de ficheiro predefinido para HDInsight no armazenamento de Blobs do Azure, /example/data.txt é realmente no armazenamento de Blobs do Azure. Também pode consultar o ficheiro como:

    wasbs:///example/data/data.txt

ou

    wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Para uma lista dos outros comandos de Hadoop que funcionam com os ficheiros, consulte o artigo [http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html](http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [AZURE.WARNING] Em HBase clusters, a predefinição do tamanho do bloco utilizado quando escrever dados é 256KB. Enquanto trabalha ajustar ao utilizar HBase APIs ou REST APIs, utilizando o `hadoop` ou `hdfs dfs` comandos para escrever dados com mais ~ 12GB resulta num erro. Consulte a secção de [exceção de armazenamento para escrever no blob](#storageexception) abaixo para obter mais informações.

##<a name="graphical-clients"></a>Clientes de gráficos

Também existem várias aplicações que fornecem uma interface gráfica para trabalhar com o armazenamento do Windows Azure. Segue-se uma lista de algumas destas aplicações:

| Cliente | Linux | OS X | Windows |
| ------ |:-----:|:----:|:-------:|
| [Ferramentas do Microsoft Visual Studio para HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md#navigate-the-linked-resources) | ✔ | ✔ | ✔ |
| [Explorador de armazenamento Azure](http://storageexplorer.com/) | ✔ | ✔ | ✔ |
| [Studio de armazenamento na nuvem 2](http://www.cerebrata.com/Products/CloudStorageStudio/) | | | ✔ |
| [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) | | | ✔ |
| [Explorador do Azure](http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | | ✔ |
| [Cyberduck](https://cyberduck.io/) |  | ✔ | ✔ |

###<a name="visual-studio-tools-for-hdinsight"></a>Ferramentas do Visual Studio para HDInsight

Para mais informações, consulte o artigo [Navegar os recursos ligados](hdinsight-hadoop-visual-studio-tools-get-started.md#navigate-the-linked-resources).

###<a id="storageexplorer"></a>Explorador de armazenamento Azure

*Explorador de armazenamento do Azure* é uma ferramenta útil para inspecionar e alterar os dados no blobs. É uma ferramenta de origem gratuito, abra que pode ser transferida das [http://storageexplorer.com/](http://storageexplorer.com/). O código de origem está disponível a partir de, assim esta ligação.

Antes de utilizar a ferramenta, tem de saber a sua chave de nome e a conta da conta de armazenamento Azure. Para obter instruções sobre como obter estas informações, consulte o artigo o "como: as teclas de acesso de vista, copiar e voltar a gerar armazenamento" secção de [criar, gerir, ou eliminar uma conta de armazenamento][azure-create-storage-account].  

1. Execute o Explorador de armazenamento Azure. Se esta for a primeira vez que tiver executou o Explorador de armazenamento, ser-lhe-á pedido para os ___nome da conta de armazenamento__ e a __chave de conta de armazenamento__. Se tiver ficou-lo antes de utilizar o botão __Adicionar__ para adicionar um novo nome de conta de armazenamento e a chave.

    Introduza o nome e chave da conta de armazenamento utilizado pelo seu cluster HDinsight e, em seguida, selecione __Guardar e abrir__.

    ![HDI. AzureStorageExplorer][image-azure-storage-explorer]

5. Na lista de membros em contentores à esquerda da interface de, clique no nome do contentor que está associado ao seu cluster HDInsight. Por predefinição, este é o nome do HDInsight cluster, mas pode ser diferente caso tenha introduzido um nome específico quando criar o cluster.

6. A partir da barra de ferramentas, selecione o ícone de carregamento.

    ![Barra de ferramentas com o ícone de carregamento destacado](./media/hdinsight-upload-data/toolbar.png)

7. Especificar um ficheiro para carregar e, em seguida, clique em **Abrir**. Quando lhe for pedido, selecione __carregar__ para carregar o ficheiro na raiz do contentor de armazenamento. Se pretender que carregue o ficheiro para um caminho específico, introduza o caminho no campo de __destino__ e, em seguida, selecione __carregar__.

    ![Caixa de diálogo de carregamento de ficheiros](./media/hdinsight-upload-data/fileupload.png)
    
    Assim que o ficheiro de terminar a carregar, pode utilizá-lo a partir de tarefas no HDInsight cluster.

##<a name="mount-azure-blob-storage-as-local-drive"></a>Armazenamento de Blobs do Azure montagem como unidade Local

Consulte o artigo [armazenamento de Blobs do Azure montagem como unidade Local](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

##<a name="services"></a>Serviços

###<a name="azure-data-factory"></a>Dados Azure fábrica

O serviço de fábrica do Azure dados é um serviço totalmente gerido para serviços de movimento de armazenamento, processamento de dados e dados de dados de composição para dados simplificada, e dimensionáveis tubagens de produção.

Azure fábrica de dados podem ser utilizada para mover dados para o armazenamento de Blobs do Azure ou para criar tubagens de dados que diretamente utilizam funcionalidades de HDInsight como ramo e porco.

Para mais informações, consulte a [documentação do Azure fábrica de dados](https://azure.microsoft.com/documentation/services/data-factory/).

###<a id="sqoop"></a>Apache Sqoop

Sqoop é uma ferramenta concebida para transferir dados entre Hadoop e bases de dados relacionais. Pode utilizá-lo para importar dados a partir de um sistema de gestão de base de dados relacional (RDBMS), tais como o SQL Server, MySQL ou Oracle para o sistema de ficheiros distribuído de Hadoop (HDFS), transformar os dados no Hadoop com MapReduce ou ramo e, em seguida, exporte os dados novamente para uma RDBMS.

Para obter mais informações, consulte o artigo [Utilizar Sqoop com HDInsight][hdinsight-use-sqoop].

##<a name="development-sdks"></a>SDK do desenvolvimento

Armazenamento de Blobs do Azure também pode ser acedido utilizando um SDK Azure a partir de linguagens de programação seguintes:

* .NET
* Java
* NODE.js
* PHP
* Python
* Rubi

Para obter mais informações sobre como instalar o SDK do Azure, consulte o artigo [transferências do Azure](https://azure.microsoft.com/downloads/)

## <a name="troubleshooting"></a>Resolução de problemas

### <a id="storageexception"></a>Exceção de armazenamento para escrever no blob

__Os sintomas mais__: quando utiliza o `hadoop` ou `hdfs dfs` comandos para escrever os ficheiros que estão ~ 12 GB ou maior num HBase cluster, que poderá encontrar o seguinte erro: 

    ERROR azure.NativeAzureFileSystem: Encountered Storage Exception for write on Blob : example/test_large_file.bin._COPYING_ Exception details: null Error Code : RequestBodyTooLarge
    copyFromLocal: java.io.IOException
            at com.microsoft.azure.storage.core.Utility.initIOException(Utility.java:661)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:366)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:350)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
            at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
            at java.lang.Thread.run(Thread.java:745)
    Caused by: com.microsoft.azure.storage.StorageException: The request body is too large and exceeds the maximum permissible limit.
            at com.microsoft.azure.storage.StorageException.translateException(StorageException.java:89)
            at com.microsoft.azure.storage.core.StorageRequest.materializeException(StorageRequest.java:307)
            at com.microsoft.azure.storage.core.ExecutionEngine.executeWithRetry(ExecutionEngine.java:182)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlockInternal(CloudBlockBlob.java:816)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlock(CloudBlockBlob.java:788)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:354)
            ... 7 more

__Causa__: HBase no HDInsight clusters predefinido para um tamanho de bloco de 256 KB ao escrever ao armazenamento Azure. Enquanto isto funciona para HBase APIs ou REST APIs,-lo irá resultar num erro ao utilizar o `hadoop` ou `hdfs dfs` utilitários da linha de comandos.

__Resolução__: utilizar `fs.azure.write.request.size` para especificar um tamanho de bloco maior. Pode fazê-lo numa base por utilização utilizando o `-D` parâmetro. A seguinte é um exemplo utilizar este parâmetro com o `hadoop` comando:

    hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data

Também pode aumentar o valor de `fs.azure.write.request.size` globalmente utilizando Ambari. Os passos seguintes podem ser utilizados para alterar o valor de IU Ambari Web:

1. No seu browser, aceda a IU Ambari Web para o seu cluster. Este é https://CLUSTERNAME.azurehdinsight.net, onde o __nome de cluster__ é o nome do seu cluster.

    Quando lhe for pedido, introduza o nome de administrador e a palavra-passe para o cluster.

2. A partir do lado esquerdo do ecrã, selecione __HDFS__e, em seguida, selecione o separador __Configs__ .

3. No campo __filtrar...__ , introduza `fs.azure.write.request.size`. Isto vai mostrar o campo e o valor atual no meio da página.

4. Altere o valor de 262144 (256KB) para o novo valor. Por exemplo, 4194304 (4MB).

![Imagem da alteração do valor através de Ambari Web UI](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Para mais informações sobre como utilizar Ambari, consulte o artigo [Gerir HDInsight clusters utilizando a IU da Web de Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>Próximos passos

Agora que a compreender como colocar dados no HDInsight, leia os seguintes artigos para saber como efetuar uma análise de:

* [Introdução ao Azure HDInsight][hdinsight-get-started]
* [Submeter tarefas de Hadoop através de programação][hdinsight-submit-jobs]
* [Utilizar ramo com HDInsight][hdinsight-use-hive]
* [Utilizar porco com HDInsight][hdinsight-use-pig]




[azure-management-portal]: https://porta.azure.com
[azure-powershell]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx

[azure-storage-client-library]: /develop/net/how-to-guides/blob-storage/
[azure-create-storage-account]: ../storage/storage-create-storage-account.md
[azure-azcopy-download]: ../storage/storage-use-azcopy.md
[azure-azcopy]: ../storage/storage-use-azcopy.md

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md

[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[sqldatabase-create-configure]: ../sql-database-create-configure.md

[apache-sqoop-guide]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

[Powershell-install-configure]: ../powershell-install-configure.md

[azurecli]: ../xplat-cli-install.md


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png
