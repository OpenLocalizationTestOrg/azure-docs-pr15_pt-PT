<properties
    pageTitle="Descrição geral da ciência de dados com motores no Azure HDInsight | Microsoft Azure"
    description="O toolkit de motores MLlib traz formação de máquina consideráveis modelação capacidades para o ambiente de HDInsight distribuído."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"  />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/07/2016"
    ms.author="deguhath;bradsev;gokuma" />

# <a name="overview-of-data-science-using-spark-on-azure-hdinsight"></a>Descrição geral da ciência de dados com motores no Azure HDInsight

[AZURE.INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

Este conjunto de aplicações de tópicos mostra como utilizar HDInsight motores para concluir tarefas comuns de ciências de dados como ingestão de dados, engenharia de funcionalidade, modelação e avaliação de modelo. Dados utilizados são um exemplo do 2013 pt veículo viagem e tarifa conjunto de dados. Os modelos incorporados incluem regressão linear e logístico, florestas aleatórias e árvores aumentados com gradação de cor. Os tópicos também mostram como armazenar estes modelos em armazenamento de Blobs do Azure (WASB) e como pontuação e avaliar o respetivo desempenho aspeto do Office. Tópicos mais avançados abrangem como modelos podem ser formação utilizando a limpeza de validação de cruz e hyper-o parâmetro. Este tópico Descrição geral também descreve como configurar o cluster de motores que precisa de concluir os passos três tutoriais fornecidos. 

[Motores](http://spark.apache.org/) é uma paralelamente abrir origem processamento framework que suporta o processamento de na memória para aumentar o desempenho das aplicações analíticos grande dados. Motor de processamento de motores baseia-se para a velocidade, facilidade de utilização e a análise de sofisticadas. Capacidades de cálculo distribuído na memória do motores tornam uma boa escolha para algoritmos iterativos nos cálculos máquina de aprendizagem e o gráfico. [MLlib](http://spark.apache.org/mllib/) é máquina dimensionáveis aprendizagem biblioteca de motores que dá capacidades de modelação a este ambiente distribuído. 

[Motores de HDInsight](../hdinsight/hdinsight-apache-spark-overview.md) é a oferta alojada Azure de motores de abrir origem. Também inclui suporte para **Jupyter PySpark blocos de notas** no cluster motores que pode executar consultas de interativas motores SQL para transformar, filtragem e visualizar dados armazenados no Azure Blobs (WASB). PySpark é a API Python para motores. Fragmentos de código que fornecem as soluções e mostra as representações relevantes para visualizar os dados aqui executados nos blocos de notas Jupyter instalados em clusters motores. Os passos de modelação nestes tópicos contenham código que mostra como formar, avaliar, guardar e consumir cada tipo de modelo. 

Os passos de configuração e código fornecido neste tutorial destina-se HDInsight 3.4 motores 1.6. No entanto, o código aqui e nos blocos de notas é genérico e deve trabalhar em qualquer cluster de motores. Se não estiver a utilizar os motores de HDInsight, os passos de configuração e gestão de cluster poderão ser ligeiramente diferentes a partir do que é apresentado aqui.

## <a name="prerequisites"></a>Pré-requisitos

1. tem de ter uma subscrição do Azure. Se não já possui um, consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

2. tem um cluster de HDInsight 3.4 motores 1.6 para concluir este tutorial. Para criar um, consulte as instruções fornecidas na [Introdução: criar Apache motores no Azure HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md). O tipo de cluster e a versão é especificada a partir do menu **Selecionar tipo de Cluster** . 


![](./media/machine-learning-data-science-spark-overview/spark-cluster-on-portal.png)

<!-- -->

> [AZURE.NOTE] Para um tópico que mostra como utilizar Scala em vez de Python para concluir tarefas para um processo de ciências ponto a ponto de dados, consulte o artigo da [ciência de dados utilizando Scala com motores no Azure](machine-learning-data-science-process-scala-walkthrough.md).

<!-- -->

>[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## <a name="the-nyc-2013-taxi-data"></a>Os dados de pt 2013 veículo

Os dados de viagem de veículo pt são de cerca de 20 GB de ficheiros comprimido valores separados por vírgulas (CSV) (não comprimido GB de ~ 48), que inclua mais de 173 milhão individuais viagens e as tarifas pagos para cada viagem. Cada registo de viagem inclui a recolha para cima e a localização de remessa e hora, ataque à anónimos (controlador) licença número e medallion (id exclusivo do veículo). Os dados abrange viagens de todos os do ano 2013 e são fornecidos em conjuntos de dados de duas seguintes para cada mês:

1. Os ficheiros CSV 'trip_data' contêm detalhes de viagem, como o número de passageiros, Pegue e dropoff pontos, e volta a duração e comprimento de viagem. Aqui estão alguns registos de exemplo:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. Os ficheiros CSV 'trip_fare' contêm detalhes de tarifa paga para cada viagem, tal como o tipo de pagamento, quantidade tarifa, sobretaxa e impostos, sugestões e portagens e o montante total pago. Aqui estão alguns registos de exemplo:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5


Vamos ter tirado uma amostra de 0,1% destes ficheiros e associadas a viagem\_dados e viagem\_ficheiros CVS a importância a pagar para um único conjunto de dados para utilizar como o conjunto de dados de entrada para este tutorial. A chave exclusiva para participar viagem\_dados e viagem\_tarifa é composta pelos campos: medallion, ataque à\_licença e recolha\_datetime. Cada registo do conjunto de dados contém os seguintes atributos que representa uma viagem de veículo PT:


|Campo| Breve descrição
|------|---------------------------------
| Medallion |Medallion veículo anónimos (id exclusivo veículo)
| hack_license |    Número de licenças de transporte Hackney anónimos
| vendor_id |   Id do fornecedor de veículo
| rate_code | Taxa de veículo pt de corrida
| store_and_fwd_flag | Armazenar e reencaminhar sinalizador
| pickup_datetime | Pegue data e hora
| dropoff_datetime | Dropoff data e hora
| pickup_hour | Pegue hora
| pickup_week | Pegue semana do ano
| dia da semana | Dia da semana (intervalo 1 a 7)
| passenger_count | Número de passageiros numa viagem de veículo
| trip_time_in_secs | Tempo em segundos
| trip_distance | Distância de viagem passada em milhas
| pickup_longitude | Pegue longitude
| pickup_latitude | Pegue latitude
| dropoff_longitude | Dropoff longitude
| dropoff_latitude | Dropoff latitude
| direct_distance | Direccionar a distância entre recolha para cima e dropoff localizações
| payment_type | Tipo de pagamento (AC, cartão de crédito etc.)
| fare_amount | Montante tarifa na
| sobretaxa | Sobretaxa
| mta_tax | MTA impostos
| tip_amount | Sugestão quantidade
| tolls_amount | Montante portagens
| total_amount | Montante total
| Inclinado | Inclinado (1/0 para não ou Sim)
| tip_class | Dica escolares (0: $0, 1: $0-5, 2: $6-10, 3: $11-20, 4: > $20)


## <a name="execute-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Executar código a partir de um bloco de notas Jupyter no cluster motores 

Pode iniciar o bloco de notas Jupyter a partir do portal Azure. Localizar o seu cluster de motores no seu dashboard e clique nele para introduzir a página de gestão do seu cluster. Para abrir o bloco de notas associado ao cluster de motores, clique em **Cluster Dashboards** -> **Jupyter bloco de notas** .

![](./media/machine-learning-data-science-spark-overview/spark-jupyter-on-portal.png)

Também pode navegar para ***https://CLUSTERNAME.azurehdinsight.net/jupyter*** para aceder a blocos de notas Jupyter. Substitua a parte do nome de cluster deste URL com o nome do seu próprio cluster. Tem a palavra-passe para a sua conta de administrador aceder os blocos de notas.

![](./media/machine-learning-data-science-spark-overview/spark-jupyter-notebook.png)

Selecione PySpark para ver um diretório que contém alguns exemplos de previamente embalados blocos de notas que utilizam a API PySpark. Os blocos de notas que contêm os exemplos de código para este conjunto de tópico motores estão disponíveis no [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark)


Pode carregar os blocos de notas diretamente a partir do Github para o servidor de bloco de notas Jupyter no seu cluster de motores. Na home page do seu Jupyter, clique no botão **carregar** na parte direita do ecrã. Esta é aberta um Explorador de ficheiros. Aqui pode colar o URL de Github (conteúdo não processado) do bloco de notas e clique em **Abrir**. Os blocos de notas PySpark estão disponíveis nos URLs seguintes:

1.  [pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb)
2.  [pySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark-machine-learning-data-science-spark-model-consumption.ipynb)
3.  [pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb)

Ver o nome do ficheiro da sua lista de ficheiro Jupyter com um botão **carregar** novamente. Clique neste botão **carregar** . Agora ter importado o bloco de notas. Repita estes passos para carregar os outros blocos de notas a partir destas instruções.

> [AZURE.TIP] Pode com o botão direito as ligações no seu browser e selecione **Copiar ligação** para obter o URL de conteúdo não processado github. Pode colar este URL na caixa de diálogo Explorador de ficheiros Jupyter carregar.

Agora, pode:

- Ver o código ao clicar em bloco de notas.
- Execute cada célula ao premir **SHIFT ENTER**.
- Executar o bloco de notas inteiro, clicando numa **célula** -> **Executar**.
- Utilize a visualização automática de consultas.

> [AZURE.TIP] O kernel PySpark automaticamente apresenta o resultado de consultas SQL (HiveQL). São apresentadas a opção para selecionar entre vários tipos de visualizações (tabela, circular, linhas, área ou barra) utilizando os botões de menu **tipo** no bloco de notas:

![Curva em forma de regressão logística ROC para abordagem genérica](./media/machine-learning-data-science-spark-overview/pyspark-jupyter-autovisualization.png)

## <a name="whats-next"></a>O que se segue?

Agora que estão configurados com um cluster de motores de HDInsight e carregou os blocos de notas Jupyter, está pronto para resolver os tópicos que correspondem aos blocos de notas de PySpark três. Mostram como explorar os seus dados e, em seguida, como criar e consumir modelos. O bloco de notas do informações detalhadas e modelação de dados avançadas mostra como incluir validação cruzada, hyper-o parâmetro de remoção e avaliação de modelo. 

**Informações detalhadas de dados e a modelação com motores:** Explorar o conjunto de dados e criar, pontuação e avaliar a modelos de aprendizagem ao trabalhar através do tópico [Criar Classificação binária e modelos de regressão para os dados com o toolkit de MLlib motores](machine-learning-data-science-spark-data-exploration-modeling.md) de automática.

**Modelar consumo:** Para saber como pontuação os modelos de classificação e regressão criados neste tópico, consulte o artigo [pontuação e modelos de aprendizagem máquina compilado motores de avaliar](machine-learning-data-science-spark-model-consumption.md).

**Publicação em validação e hyperparameter de remoção**: consulte o artigo [informações detalhadas de dados e a modelação com motores avançadas](machine-learning-data-science-spark-advanced-data-exploration-modeling.md) no como modelos podem ser formação utilizando a limpeza de validação de cruz e hyper-o parâmetro

