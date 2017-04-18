<properties
pageTitle="Utilizar DataFu com porco no HDInsight"
description="DataFu é uma coleção de bibliotecas para utilização com Hadoop. Saiba como pode utilizar DataFu com porco no seu cluster HDInsight."
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
ms.date="08/23/2016"
ms.author="larryfr"/>

#<a name="use-datafu-with-pig-on-hdinsight"></a>Utilizar DataFu com porco no HDInsight

DataFu é uma coleção de bibliotecas abrir origem para utilização com Hadoop. Neste documento, irá Saiba como utilizar DataFu no seu cluster HDInsight e como utilizar funções definidas pelo utilizador DataFu (UDF) com porco.

##<a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure.

* Um cluster de Azure HDInsight (Linux ou baseado no Windows)

* Uma familiaridade básica com a [utilização de porco no HDInsight](hdinsight-use-pig.md)

##<a name="install-datafu-on-linux-based-hdinsight"></a>Instalar o DataFu no baseado em Linux HDInsight

> [AZURE.NOTE] DataFu está instalado versão com base em Linux clusters 3.3 e superior e da clusters baseados no Windows. Não estiver instalado no clusters baseados em Linux anteriores ao 3.3.
>
> Se estiver a utilizar uma versão com base em Linux cluster 3.3 ou superior ou um cluster baseados no Windows, pode ignorar esta secção.

DataFu pode ser transferida e instalada a partir do repositório de Maven. Utilize os seguintes passos para adicionar DataFu ao seu cluster HDInsight:

1. Ligar ao seu cluster de baseado em Linux HDInsight utilizando SSH. Para obter mais informações sobre como utilizar SSH com HDInsight, consulte um dos seguintes documentos:

    * [Utilizar SSH com baseado em Linux Hadoop no HDInsight do Linux, OS X e Unix](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Utilizar SSH com baseado em Linux Hadoop no HDInsight a partir do Windows](hdinsight-hadoop-linux-use-ssh-unix.md)
    
2. Utilize o seguinte comando para transferir o ficheiro de para caixa DataFu utilizando o utilitário wget, ou copie e cole a ligação no seu browser para iniciar a transferência.

        wget http://central.maven.org/maven2/com/linkedin/datafu/datafu/1.2.0/datafu-1.2.0.jar

3. Em seguida, carregar o ficheiro para o armazenamento de predefinida para o seu cluster HDInsight. O ficheiro fica disponível para todos os nós no cluster e o ficheiro irá permanecer na armazenamento mesmo se eliminar e recrie o cluster.

        hdfs dfs -put datafu-1.2.0.jar /example/jars
    
    > [AZURE.NOTE] O exemplo acima armazena para a caixa no `wasbs:///example/jars` uma vez que este directório já existir no armazenamento de cluster. Pode utilizar qualquer localização que deseja no armazenamento de cluster HDInsight.

##<a name="use-datafu-with-pig"></a>Utilizar DataFu com porco

Os passos nesta secção partem do pressuposto de que está familiarizado com porco no HDInsight e fornecer apenas demonstrações porco Latim, não os passos sobre como utilizá-los com o cluster. Para mais informações sobre como utilizar porco com HDInsight, consulte o artigo [Utilizar porco com HDInsight](hdinsight-use-pig.md).

> [AZURE.IMPORTANT] Ao utilizar DataFu a partir do porco num cluster baseado em Linux HDInsight, primeiro tem de registar o ficheiro de para caixa utilizando a seguinte instrução porco Latim:
>
> ```register wasbs:///example/jars/datafu-1.2.0.jar```
>
> DataFu está registado por predefinição nos clusters HDInsight baseados no Windows.

Normalmente, irá definir um alias para DataFu funções. Por exemplo:

    DEFINE SHA datafu.pig.hash.SHA();
    
Isto define um alias com nome `SHA` para o SHA hashing função. Em seguida, pode utilizar esta num script porco Latim para gerar um hash para os dados de entrada. Por exemplo, o seguinte procedimento substitui os nomes nos dados de entrada com um valor hash:

    raw = LOAD '/data/raw/' USING PigStorage(',') AS  
        (name:chararray, 
        int1:int, 
        int2:int,
        int3:int); 
    mask = FOREACH raw GENERATE SHA(name), int1, int2, int3; 
    DUMP mask;

Se este for utilizado com os seguintes dados de entrada:

    Lana Zemljaric,5,9,1
    Qiong Zhong,9,3,6
    Sandor Harsanyi,0,7,3
    Roko Petkovic,2,6,2
    Tibor Rozsa,8,0,0
    Lea Hrastovsek,6,3,6
    Regina Toth,2,1,2
    Eva Makay,8,9,2
    Shi Liao,4,6,0
    Tjasa Zemljaric,0,2,5
    
Irá gerar o seguinte resultado:

    (c1a743b0f34d349cfc2ce00ef98369bdc3dba1565fec92b4159a9cd5de186347,5,9,1)
    (713d030d621ab69aa3737c8ea37a2c7c724a01cd0657a370e103d8cdecac6f99,9,3,6)
    (7a5f5abdd281f68168199319d98a1a662535f988d1443b3a3c497010937bac89,0,7,3)
    (a94818e93807e12079c4b35f8f3c8c8ef8e8acd1954e7f0476bc1a3a86fc96a9,2,6,2)
    (894ead4f48af91df7e088241218a23157bede7c52115272e417e95c046d48902,8,0,0)
    (6f99f163af3448fda672087db306f363e27a98a9e49c1f274a0860e303f8aec4,6,3,6)
    (a03de92a28be3c6a984c7a153fa9ed81c0413f76a9401955b5f7e04a5dd0ab9f,2,1,2)
    (6ceab977c8fb48d9ad0dc413e6bc646cabd89f22e7ab97a6b0133f3d225c6013,8,9,2)
    (fa9c436469096ff1bd297e182831f460501b826272ae97e921f5f6e3f54747e8,4,6,0)
    (bc22db7c238b86c37af79a62c78f61a304b35143f6087eb99c34040325865654,0,2,5)

##<a name="next-steps"></a>Próximos passos

Para mais informações sobre DataFu ou porco, consulte os seguintes documentos:

* [Guia de DataFu porco Apache](http://datafu.incubator.apache.org/docs/datafu/guide.html).

* [Utilizar porco com HDInsight](hdinsight-use-pig.md)
