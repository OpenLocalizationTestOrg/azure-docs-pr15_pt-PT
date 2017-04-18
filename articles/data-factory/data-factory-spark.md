<properties 
    pageTitle="Invocar programas de motores a partir do Azure fábrica de dados" 
    description="Saiba como invocar programas de motores de uma fábrica de dados Azure utilizando a atividade de MapReduce." 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/25/2016" 
    ms.author="spelluru"/>

# <a name="invoke-spark-programs-from-data-factory"></a>Invocar motores programas a partir de dados fábrica
## <a name="introduction"></a>Introdução
Pode utilizar a atividade de MapReduce no pipeline fábrica de dados para executar motores programas no seu cluster de motores de HDInsight. Consulte o artigo [MapReduce atividade](data-factory-map-reduce.md) artigo para obter informações detalhadas sobre como utilizar a atividade antes de ler este artigo. 

## <a name="spark-sample-on-github"></a>Exemplo de motores no GitHub
Os [motores - fábrica de dados de exemplo no GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) mostra como utilizar MapReduce atividade para invocar um programa de motores. O programa de motores apenas copia os dados a partir de um contentor de Blobs do Azure para outra. 

## <a name="data-factory-entities"></a>Entidades de dados de fábrica do mesmo
A pasta de **Motores-ADF/src/ADFJsons** contiver ficheiros para entidades fábrica de dados (serviços ligados, conjuntos de dados, pipeline).  

Existem dois **Serviços ligados** neste exemplo: armazenamento do Windows Azure e Azure HDInsight. Especifique o nome do armazenamento Azure e valores chave no **StorageLinkedService.json** e clusterUri, nome de utilizador e palavra-passe no **HDInsightLinkedService.json**.

Existem dois **conjuntos de dados** neste exemplo: **input.json** e **output.json**. Estes ficheiros estão localizados na pasta **conjuntos de dados** .  Estes ficheiros representam conjuntos de dados de entrada e saídos da atividade de MapReduce de

Localize tubagens de exemplo na pasta **ADFJsons/em curso** . Reveja uma tubagem para compreender como invocar um programa de motores utilizando a atividade de MapReduce. 

A atividade de MapReduce está configurada para invocar **com.adf.sparklauncher.jar** no contentor **adflibs** no seu armazenamento Azure (especificado na StorageLinkedService.json). O código de origem para este programa está em motores-ADF/src/principais/java/com/adf/pasta e -chamadas submeter motores e executar motores tarefas. 

> [AZURE.IMPORTANT] 
> Leia através de [Leia-me. TXT](https://github.com/Azure/Azure-DataFactory/blob/master/Samples/Spark/README.txt) para as informações adicionais e mais recentes antes de utilizar a amostra. 
>  
> Utilize o seu próprio cluster de motores de HDInsight com esta abordagem para invocar programas de motores utilizando a atividade de MapReduce. Utilizar um cluster de HDInsight a pedido não é suportada.   


## <a name="see-also"></a>Consulte também
- [Ramo de atividade](data-factory-hive-activity.md)
- [Porco atividade](data-factory-pig-activity.md)
- [Atividade de MapReduce](data-factory-map-reduce.md)
- [Atividade de transmissão de Hadoop](data-factory-hadoop-streaming-activity.md)
- [Invocar R scripts](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)
