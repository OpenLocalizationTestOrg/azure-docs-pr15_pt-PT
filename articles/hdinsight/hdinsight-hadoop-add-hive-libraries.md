<properties
pageTitle="Adicionar bibliotecas ramo durante a criação do cluster HDInsight | Azure"
description="Saiba como adicionar bibliotecas ramo (para caixa ficheiros,) a um cluster de HDInsight durante a criação de cluster."
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
ms.date="09/20/2016"
ms.author="larryfr"/>

#<a name="add-hive-libraries-during-hdinsight-cluster-creation"></a>Adicionar bibliotecas ramo durante a criação do cluster HDInsight

Se tiver bibliotecas que utiliza frequentemente com ramo de HDInsight, este documento contém informações sobre como utilizar uma ação de Script para carregar previamente as bibliotecas durante a criação de cluster. Isto faz com que as bibliotecas globalmente disponíveis no ramo (não é necessário utilizar o [Adicionar JAR](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) para carregá-los.)

##<a name="how-it-works"></a>Como funciona

Ao criar um cluster, opcionalmente, pode especificar uma acção de Script que é executada um script em nós de cluster enquanto que são criadas. O script neste documento aceita um único parâmetro, que é uma localização de WASB que contém as bibliotecas (guardadas como ficheiros para caixa,) que serão carregadas previamente.

Durante a criação de cluster, o script enumera os ficheiros, copia-os para o `/usr/lib/customhivelibs/` diretório em nós de cabeça e trabalhador, em seguida, adiciona-os para o `hive.aux.jars.path` propriedade na `core-site.xml` ficheiro. Em baseado em Linux clusters, também actualiza a `hive-env.sh` ficheiro com a localização dos ficheiros.

> [AZURE.NOTE] Utilizar as ações de script descritos neste artigo disponibiliza as bibliotecas nos cenários seguintes:
>
> * __Baseado em Linux HDInsight__ - ao utilizar __Hive da linha de comandos__, __WebHCat__ (Templeton) e __HiveServer2__.
> * __HDInsight baseados no Windows__ - ao utilizar a __Hive da linha de comandos__ e __WebHCat__ (Templeton).

##<a name="the-script"></a>O script

__Localização de script__

Para __clusters baseado em Linux__: [https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh)

Para __clusters baseados no Windows__: [https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

__Requisitos de__

* Os scripts devem ser aplicados a __nós de cabeça__ e __nós de trabalho__.

* Jarros que pretende instalar devem ser armazenados no armazenamento de Blobs do Azure num __único contentor__. 

* A conta de armazenamento que contém a biblioteca de para caixa ficheiros __deve__ ser ligada à HDInsight cluster durante a criação. Isto pode ser feito numa de duas formas:

    * Por que está a ser num contentor da conta de armazenamento predefinida para o cluster.
    
    * Por que está a ser num contentor num contentor armazenamento ligadas. Por exemplo, no portal do pode utilizar __Configuração opcional__, __ligadas armazenamento contas__ para adicionar armazenamento adicional.

* O caminho WASB para o contentor tem de ser especificado como um parâmetro para a ação de Script. Por exemplo, se o jarros são armazenados num contentor de __efectuar__ uma conta de armazenamento denominada __mystorage__com o nome, o parâmetro seria __wasbs://libs@mystorage.blob.core.windows.net/__.

    > [AZURE.NOTE] Este documento assume que já tem uma conta de armazenamento de criação, blob contentor e carregar os ficheiros ao mesmo. 
    >
    > Se não tiver criado uma conta de armazenamento, pode fazê-lo através do [Azure portal](https://portal.azure.com). Em seguida, pode utilizar um utilitário de como o [Explorador de armazenamento do Azure](http://storageexplorer.com/) para criar um novo contentor na conta e carregar ficheiros para o mesmo.

##<a name="create-a-cluster-using-the-script"></a>Criar um cluster utilizando o script

> [AZURE.NOTE] Os passos seguintes criam um novo cluster de baseado em Linux HDInsight. Para criar um novo cluster baseados no Windows, selecione __Windows__ como o sistema operativo cluster quando criar o cluster e utilize o script do Windows (PowerShell) em vez do script de festa.
> 
> Também pode utilizar o PowerShell Azure ou o HDInsight .NET SDK para criar um cluster utilizando este script. Para mais informações sobre como utilizar estes métodos, consulte o artigo [Personalizar HDInsight clusters com ações de Script](hdinsight-hadoop-customize-cluster-linux.md).

1. Iniciar um cluster de aprovisionamento, utilizando os passos em [clusters de aprovisionar HDInsight no Linux](hdinsight-hadoop-provision-linux-clusters.md#portal), mas não concluída de aprovisionamento.

2. No pá **Configuração opcional** , selecione **Ações de Script**e forneça as informações, conforme apresentado abaixo:

    * __Nome__: introduza um nome amigável para a ação de script.
    * __URI de SCRIPT__: https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh
    * __HEAD__: selecione esta opção
    * __TRABALHADOR__: selecione esta opção.
    * __ZOOKEEPER__: deixe este em branco.
    * __Parâmetros__: introduza o endereço WASB para a conta de contentor e armazenamento que contém os jarros. Por exemplo, __wasbs://libs@mystorage.blob.core.windows.net/__.

3. Na parte inferior das **Ações de Script**, utilize o botão **Selecionar** para guardar a configuração.

4. No pá **Configuração opcional** , selecione __Contas ligadas de armazenamento__ e selecione a ligação __Adicionar uma chave de armazenamento__ . Selecione a conta de armazenamento que contém os jarros e, em seguida, utilize os botões __Selecione__ para guardar as definições e regressar a pá __Configuração opcional__ .

5. Utilize o botão **Selecione** na parte inferior da pá a **Configuração opcional** para guardar as informações de configuração opcional.

6. Continue a cluster de aprovisionamento, tal como descrito em [clusters de aprovisionar HDInsight no Linux](hdinsight-hadoop-provision-linux-clusters.md#portal).

Depois de concluir a criação do cluster, poderá utilizar jarros adicionados através da opção este script do ramo sem ter de utilizar o `ADD JAR` declaração.

##<a name="next-steps"></a>Próximos passos

Neste documento aprendeu como adicionar bibliotecas ramo contidas para caixa ficheiros a um cluster de HDInsight durante a criação de cluster. Para obter mais informações sobre como trabalhar com ramo, consulte o artigo [Utilizar Hive com HDInsight](hdinsight-use-hive.md)
