<properties
    pageTitle="Analisar dados de sensor utilizando ramo e Hadoop | Microsoft Azure"
    description="Saiba como analisar dados de sensor utilizando a consola de consulta Hive com HDInsight (Hadoop) e, em seguida, visualizar dados no Microsoft Excel com o PowerView."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016" 
    ms.author="larryfr"/>

#<a name="analyze-sensor-data-using-the-hive-query-console-on-hadoop-in-hdinsight"></a>Analisar dados de sensor utilizando a consola de consulta Hive no Hadoop no HDInsight

Saiba como analisar dados de sensor utilizando a consola de consulta Hive com HDInsight (Hadoop) e, em seguida, visualize os dados no Microsoft Excel utilizando o Power View.

> [AZURE.NOTE] Os passos neste documento funcionam apenas com clusters HDInsight baseados no Windows.

Neste exemplo, utilizará Hive para processar dados históricas produzidos pela aquecimento, ventilação e sistemas de ar condicionado (AVAC) para identificar sistemas que não forem capazes de forma fiável manter uma temperatura de conjunto. Irá obter informações sobre como:

- Crie tabelas HIVE para consultar os dados armazenados em ficheiros de valores (CSV) separados por vírgulas.
- Crie consultas HIVE para analisar os dados.
- Utilizar o Microsoft Excel para ligar à HDInsight (utilizando a conectividade de bases de dados abertas (ODBC) para obter os dados analisados.
- Utilize o Power View para visualizar os dados.

![Um diagrama de arquitetura de solução](./media/hdinsight-hive-analyze-sensor-data/hvac-architecture.png)

##<a name="prerequisites"></a>Pré-requisitos

* Um cluster de HDInsight (Hadoop): consulte [clusters de aprovisionar Hadoop HDInsight](hdinsight-provision-clusters.md) para obter informações sobre a criação de um cluster.

* Microsoft Excel 2013

    > [AZURE.NOTE] Microsoft Excel é utilizado para visualização de dados com o [Power View](https://support.office.com/Article/Power-View-Explore-visualize-and-present-your-data-98268d31-97e2-42aa-a52b-a68cf460472e?ui=en-US&rs=en-US&ad=US).

* [Controlador ODBC ramo da Microsoft](http://www.microsoft.com/download/details.aspx?id=40886)

##<a name="to-run-the-sample"></a>Para executar o exemplo

1. A partir do browser, navegue para o seguinte URL. Substituir `<clustername>` com o nome do seu cluster HDInsight.

        https://<clustername>.azurehdinsight.net

    Quando lhe for pedido, autenticar utilizando o nome de utilizador do administrador e a palavra-passe que utilizou quando este cluster de aprovisionamento.

2. Na página web que se abre, clique no separador da **Galeria de introdução de introdução** e, em seguida, na categoria de **soluções com dados de exemplo** , clique a amostra de **Análise de dados Sensor** .

    ![Introdução a imagem da Galeria de introdução](./media/hdinsight-hive-analyze-sensor-data/getting-started-gallery.png)

3. Siga as instruções fornecidas na página web para concluir a amostra.
