<properties
   pageTitle="Saiba Hadoop no HDInsight com a Galeria de exemplo | Microsoft Azure"
   description="Saiba rapidamente Hadoop executando as aplicações de exemplo a partir da Galeria de introdução de introdução do HDInsight. Utilizar dados de exemplo ou fornecer as suas próprias."
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
   ms.date="10/21/2016"
   ms.author="jgao"/>

# <a name="learn-hadoop-by-using-the-azure-hdinsight-getting-started-gallery"></a>Saiba mais Hadoop ao utilizar a Galeria de introdução de introdução do Azure HDInsight

Galeria de introdução de introdução só está disponível para clusters HDInsight baseados no Windows. A Galeria fornece uma fácil e forma rápida saiba Hadoop executando as aplicações de exemplo no HDInsight. Alguns dos exemplos fornecidos com dados de exemplo. Pode fornecer os seus próprios dados para as amostras restantes. Atualmente, existem os exemplos seguintes seis (com mais provenientes):

- Soluções com os seus dados Azure
    - Análise de registo de Web site do Microsoft Azure
    - Análise de armazenamento do Microsoft Azure
- Soluções com dados de exemplo
    - Análise de dados sensor
    - Análise de tendências twitter
    - Análise de registo do Web site
    - Recomendação de filme mahout

![Soluções de HDInsight Hadoop, tempestade e Galeria de introdução de introdução HBase incluindo dados de exemplo.][hdinsight.sample.gallery]

O vídeo seguinte mostra como executar a amostra de análise de tendências Twitter:

<center><a href="https://www.youtube.com/embed/7ePbHot1SN4">https://www.YouTube.com/EMBED/7ePbHot1SN4></a></center>

O Dashboard pode ser acedido navegando para http://<YourHDInsightClusterName>.azurehdinsight.net/ ou a partir do portal do Azure.

**Para executar um exemplo a partir da Galeria de introdução de introdução**

1. Inicie sessão no [portal do Azure][azure.portal].
2. Clique em **Procurar** no menu à esquerda, clique em **HDInsight Clusters**e, em seguida, clique no seu nome de cluster.
3. A partir do menu superior, clique em **Dashboard** .
4. Introduza o nome de utilizador e palavra-passe para o utilizador HTTP (também designado o utilizador cluster).
6. Clique na **Galeria de introdução de introdução** na parte superior da página.
7. Clique das amostras. Cada amostra fornece os passos detalhados para a sua execução. A imagem seguinte mostra a amostra de análise de tendências Twitter:

    ![Exemplo de análise de tendências HDInsight Twitter][hdinsight.twitter.sample]

## <a name="next-steps"></a>Próximos passos
Outras formas para saber mais sobre HDInsight incluem:

- [Mapa de aprendizagem HDInsight][hdinsight.learn.map]
- [HDInsight infographic][hdinsight.infographic]

<!--Image references-->
[hdinsight.sample.gallery]: ./media/hdinsight-learn-hadoop-use-sample-gallery/HDInsight-Getting-Started-Gallery.png
[hdinsight.twitter.sample]: ./media/hdinsight-learn-hadoop-use-sample-gallery/HDInsight-Twitter-Trend-Analysis-sample.png

<!--Link references-->
[hdinsight.learn.map]: https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/
[hdinsight.infographic]: http://go.microsoft.com/fwlink/?linkid=523960
[azure.portal]:https://portal.azure.com
