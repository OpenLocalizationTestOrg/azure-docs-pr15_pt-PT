<properties 
    pageTitle="Utilizar ramo com Hadoop para análise de registo do Web site | Microsoft Azure" 
    description="Saiba como utilizar ramo com HDInsight para analisar os registos de Web site. Irá utilizar um ficheiro de registo como entrada para uma tabela do HDInsight e utilize HiveQL para consultar os dados." 
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
    ms.date="05/17/2016" 
    ms.author="nitinme"/>

# <a name="use-hive-with-hdinsight-to-analyze-logs-from-websites"></a>Utilizar ramo com HDInsight para analisar os registos de Web sites

Saiba como utilizar HiveQL com HDInsight para analisar os registos de início de um Web site. Análise de registo do Web site pode ser utilizado para o segmento a audiência com base em atividades semelhantes, categorizar os visitantes do site por demografia e para descobrir o conteúdo vista, provêm de Web sites e assim sucessivamente.

Neste exemplo, irá utilizar um cluster de HDInsight para analisar os ficheiros de registo do Web site para obter visão a frequência das visitas ao Web site de Web sites externos num dia. Também irá gerar um resumo dos erros de Web site que os utilizadores uma experiência. Irá obter informações sobre como:

- Ligar a um armazenamento de Blobs do Azure, que contém os ficheiros de registo de Web site.
- Crie tabelas HIVE para consultar esses registos.
- Crie consultas HIVE para analisar os dados.
- Utilizar o Microsoft Excel para ligar à HDInsight (ao utilizar a conectividade de bases de dados abertas (ODBC) para obter os dados analisados.

![HDI. Samples.Website.Log.Analysis][img-hdi-weblogs-sample]

##<a name="prerequisites"></a>Pré-requisitos

- Tem de ter aprovisionada um cluster de Hadoop no Azure HDInsight. Para obter instruções, consulte o artigo [Aprovisionar HDInsight Clusters][hdinsight-provision]. 
- Tem de ter o Microsoft Excel 2013 ou Excel 2010 instalado.
- Tem de ter o [Controlador de ODBC Hive da Microsoft](http://www.microsoft.com/download/details.aspx?id=40886) para importar dados a partir do ramo para Excel.


##<a name="to-run-the-sample"></a>Para executar o exemplo

1. A partir do [Portal do Azure](https://portal.azure.com/), a partir do Startboard (se afixados não existem cluster), clique no mosaico de cluster no qual pretende executar da amostra.

2. Pá cluster, em **Ligações rápidas**, clique em **Cluster Dashboard**e, em seguida, a partir do pá **Cluster de Dashboard** , clique em **Dashboard de Cluster HDInsight**. Em alternativa, pode abrir diretamente do dashboard utilizando o seguinte URL:

        https://<clustername>.azurehdinsight.net
    
    Quando lhe for pedido, autenticar utilizando o nome de utilizador do administrador e a palavra-passe que utilizou quando Aprovisiona o cluster.
  
2. Na página web que se abre, clique no separador da **Galeria de introdução de introdução** e, em seguida, na categoria de **soluções com dados de exemplo** , clique a amostra de **Análise de registo do Web site** .

3. Siga as instruções fornecidas na página web para concluir a amostra.

##<a name="next-steps"></a>Próximos passos
Experimente o seguinte exemplo: [Analisar dados de sensor utilizando ramo com HDInsight](hdinsight-hive-analyze-sensor-data.md).


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-sensor-data-sample]: ../hdinsight-use-hive-sensor-data-analysis.md

[img-hdi-weblogs-sample]: ./media/hdinsight-hive-analyze-website-log/hdinsight-weblogs-sample.png
 
