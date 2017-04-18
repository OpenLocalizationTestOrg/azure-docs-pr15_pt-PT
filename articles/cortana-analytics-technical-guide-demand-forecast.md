<properties
    pageTitle="Procura da previsão guia técnico de energia | Microsoft Azure"
    description="Um guia técnico para o modelo de solução com informações da empresa do Microsoft Cortana para a pedido a previsão no energia."
    services="cortana-analytics"
    documentationCenter=""
    authors="yijichen"
    manager="ilanr9"
    editor="yijichen"/>

<tags
    ms.service="cortana-analytics"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/16/2016"
    ms.author="inqiu;yijichen;ilanr9"/>

# <a name="technical-guide-to-the-cortana-intelligence-solution-template-for-demand-forecast-in-energy"></a>Guia técnico para o modelo de solução de informações da empresa Cortana para a pedido a previsão no energética

## <a name="overview"></a>**Descrição geral**

Solução modelos foram concebidos para acelerar o processo de criação de uma demonstração de E2E na parte superior de conjunto de informações da empresa Cortana. Um modelo implementado irá aprovisionar a sua subscrição do componente Cortana Intelligence necessário e crie as relações entre. Também-plantas pipeline de dados com dados de exemplo introdução gerados a partir de uma aplicação de simulação de dados. Transfira o simulator de dados a partir da hiperligação fornecida e instalá-lo no seu computador local, direcione txt para obter instruções sobre como utilizar o simulator. Dados gerados a partir do simulator irão hidratado do pipeline de dados e iniciar gerar previsão de formação de máquina que, em seguida, pode ser visualizada no dashboard do Power BI.

O modelo de solução pode ser encontrado [aqui](https://gallery.cortanaintelligence.com/SolutionTemplate/Demand-Forecasting-for-Energy-1) 

O processo de implementação irá guiá-lo através de vários passos para configurar as suas credenciais de solução. Certifique-se de que estas credenciais como o nome da solução, nome de utilizador e palavra-passe que fornece durante a implementação de registo.

O objetivo deste documento é explicar a arquitetura de referência e diferentes componentes aprovisionados na sua subscrição como parte deste modelo de solução. O documento também fala sobre como substituir os dados de exemplo, com dados reais da sua própria conseguirá ver informações/previsões a partir do que ganharam dados. Para além disso, o documento, aborda as partes do modelo de solução que necessitam de ser modificadas se pretende personalizar a solução com os seus dados. Obter instruções sobre como construir o dashboard do Power BI para este modelo de solução são fornecidas no final.

## <a name="big-picture"></a>**Conceito**

![](media\cortana-analytics-technical-guide-demand-forecast\ca-topologies-energy-forecasting.png)

### <a name="architecture-explained"></a>Arquitetura explicada
Quando a solução é implementada, são ativadas como vários serviços Azure dentro de conjunto de aplicações de análise de Cortana (*ou seja,* Hub do evento, a análise da cadeia, HDInsight, fábrica de dados, formação de máquina, *etc.*). O diagrama de arquitectura acima mostra como o pedido de previsão para modelo de solução de energia fórmula foi criada a partir do fim para fim, de alto nível. Irá poderá investigar estes serviços ao clicar nos mesmos no diagrama de modelo de solução criado com a implementação da solução. As secções seguintes descrevem cada porção.

## <a name="data-source-and-ingestion"></a>**Origem de dados e ingestão**

### <a name="synthetic-data-source"></a>Origem de dados síntese

Para este modelo de origem de dados utilizada é gerada a partir de uma aplicação de ambiente de trabalho que irá transferir e executar localmente após a implementação com êxito. Irá encontrar as instruções para transferir e instalar esta aplicação na barra de propriedades quando seleciona o primeiro nó denominado Simulator de dados de previsão de energia no diagrama de modelo de solução. Esta aplicação feeds o serviço do [Azure evento concentrador](#azure-event-hub) com pontos de dados ou eventos, que serão utilizados no resto do fluxo de solução.

A aplicação de geração evento irão povoar o concentrador de evento Azure apenas enquanto está a executar no seu computador.

### <a name="azure-event-hub"></a>Concentrador de evento Azure

O serviço do [Azure evento concentrador](https://azure.microsoft.com/services/event-hubs/) é o destinatário da entrada fornecida pela origem de dados síntese descrito acima.

## <a name="data-preparation-and-analysis"></a>**Preparação de dados e análise**


### <a name="azure-stream-analytics"></a>Análise de sequência Azure

O serviço de [Análise da cadeia de Azure](https://azure.microsoft.com/services/stream-analytics/) é utilizado para fornecer perto de análise em tempo real na sequência de entrada de serviço [Concentrador de eventos do Azure](#azure-event-hub) e publicar resultados de um dashboard do [Power BI](https://powerbi.microsoft.com) , bem como todos os eventos recebidos observou para o serviço de [Armazenamento do Windows Azure](https://azure.microsoft.com/services/storage/) para processamento posterior pelo serviço [Azure dados fábrica](https://azure.microsoft.com/documentation/services/data-factory/) do arquivo.

### <a name="hd-insights-custom-aggregation"></a>HD informações personalizada agregação

O serviço de conhecimentos aprofundados do Azure HD é utilizado para executar scripts de [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) (orquestradas pela fábrica de dados do Azure) para fornecer agregações sobre os eventos bruto que estavam arquivados utilizando o serviço de análise da cadeia de Azure.

### <a name="azure-machine-learning"></a>Formação de máquina Azure

O serviço de [Aprendizagem do Azure máquina](https://azure.microsoft.com/services/machine-learning/) é utilizado (orquestrada pela fábrica de dados do Azure) para efetuar a previsão no consumo de energia futuras de uma determinada região tendo em conta as entradas recebidas.

## <a name="data-publishing"></a>**Publicação de dados**


### <a name="azure-sql-database-service"></a>Serviço de bases de dados do Azure SQL

O serviço de [Base de dados do SQL Azure](https://azure.microsoft.com/services/sql-database/) é utilizado para armazenar (geridos pelo Azure dados fábrica) previsões recebidas pelo serviço Azure máquina formação que será consumido no dashboard [Do Power BI](https://powerbi.microsoft.com) .

## <a name="data-consumption"></a>**Consumo de dados**

### <a name="power-bi"></a>Power BI

O serviço do [Power BI](https://powerbi.microsoft.com) é utilizado para mostrar um dashboard que contenha agregações desde pelo [Azure sequência de análise](https://azure.microsoft.com/services/stream-analytics/) de serviço, bem como a pedido previsão resultados armazenados numa [Base de dados do SQL Azure](https://azure.microsoft.com/services/sql-database/) que foram criados utilizando o serviço de [Aprendizagem do Azure máquina](https://azure.microsoft.com/services/machine-learning/) . Para obter instruções sobre como construir o dashboard do Power BI para este modelo de solução, consulte a secção abaixo.

## <a name="how-to-bring-in-your-own-data"></a>**Como cumprir os seus dados**

Esta secção descreve como colocar os seus próprios dados para Azure e quais são as áreas seriam exigir alterações para os dados que trazer para a esta arquitectura.

Não é provável que qualquer conjunto de dados que trazer iria corresponder o conjunto de dados utilizado para este modelo de solução. Noções sobre os seus dados e os requisitos de serão crucial como modificar este modelo para trabalhar com os seus dados. Se esta é a sua primeira exposição para o serviço de aprendizagem do Azure máquina, pode obter uma introdução à-lo ao utilizar o exemplo no [como criar a sua primeira experiência](machine-learning\machine-learning-create-experiment.md).

As secções seguintes irão abordam as secções do modelo que irão necessitar modificações quando um novo conjunto de dados é introduzido.

### <a name="azure-event-hub"></a>Concentrador de evento Azure

O serviço do [Azure evento concentrador](https://azure.microsoft.com/services/event-hubs/) é muito genérico, assim que podem ser registados dados para o centro no formato CSV ou JSON. Sem processamento especial ocorre no Centro de eventos do Azure, mas é importante que compreende os dados que são alimentados na.

Este documento não descrevem como ingerir esta última os seus dados, mas um pode enviar facilmente eventos ou dados a um concentrador de evento Azure, utilizar a [API do concentrador de evento](event-hubs\event-hubs-programming-guide.md).

### <a name="azure-stream-analytics"></a>Análise de sequência Azure

O serviço [Azure da cadeia Analytics](https://azure.microsoft.com/services/stream-analytics/) é utilizado para fornecer perto de análise em tempo real lendo partir sequências de dados e exportar dados para qualquer número de origens.

Para o pedido previsão para modelo de solução de energia, a consulta de análise da cadeia de Azure consiste em duas consultas subpastas, cada consumir eventos de serviço Azure evento concentrador como entradas e ter saídas de duas localizações distintas. Estas saídas é constituída por um conjunto de dados do Power BI e uma localização de armazenamento do Windows Azure.

A consulta de [Análise da cadeia de Azure](https://azure.microsoft.com/services/stream-analytics/) pode ser encontrada por:

-   Registo para o [portal de gestão do Azure](https://manage.windowsazure.com/)

-   Localizar as tarefas de análise da cadeia ![](media\cortana-analytics-technical-guide-demand-forecast\icon-stream-analytics.png) que foram geradas quando a solução foi implementada. Uma é para conduza dados para blob armazenamento (por exemplo, mytest1streaming432822asablob) e o outro é para conduza dados ao Power BI (por exemplo, mytest1streaming432822asapbi).


-   Selecionar

    -   ***Entradas do tipo*** para ver a entrada de consulta

    -   ***Consulta*** para ver a própria consulta

    -   ***EXPORTA*** para ver os resultados de diferentes

Pode encontrar informações sobre construção de consulta de análise da cadeia de Azure na [Referência de consulta de análise da cadeia](https://msdn.microsoft.com/library/azure/dn834998.aspx) no MSDN.

Nesta solução, a tarefa de análise da cadeia de Azure que exporta o conjunto de dados com perto de análise em tempo real obter informações sobre o fluxo de dados de entrada a um dashboard do Power BI é fornecida como parte deste modelo de solução. Porque não existe implícito dados de conhecimento informações sobre o formato de dados de entrada, estas consultas seriam necessário ser alteradas com base no seu formato dos dados.

A tarefa de análise da cadeia de Azure exporta todos os eventos [Concentrador de evento](https://azure.microsoft.com/services/event-hubs/) ao [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) e não requer, portanto, nenhuma alteração, independentemente do formato de dados como as informações de completo do evento são transmitido em sequência ao armazenamento.

### <a name="azure-data-factory"></a>Dados Azure fábrica

O serviço de [Fábrica do Azure dados](https://azure.microsoft.com/documentation/services/data-factory/) orquestra circulação e processamento de dados. Na previsão de pedido para modelo de solução de energia a fábrica de dados é constituída doze [tubagens](data-factory\data-factory-create-pipelines.md) que mova e processar dados a utilizar tecnologias de vários.

  Pode aceder a fábrica de dados ao abrir o nó fábrica de dados na parte inferior do diagrama de modelo de solução criado com a implementação da solução. Esta será direcionado para a fábrica de dados no seu portal de gestão Azure. Se os erros em seu conjuntos de dados, pode ignorar aqueles como estão devido a fábrica de dados a ser implementada antes do gerador de dados foi iniciado. Esses erros não impede que a fábrica de dados a funcionar.

Esta secção descreve os necessárias [tubagens](data-factory\data-factory-create-pipelines.md) e as [atividades](data-factory\data-factory-create-pipelines.md) contidas na [Azure fábrica de dados](https://azure.microsoft.com/documentation/services/data-factory/). Segue-se a vista de diagrama da solução.

![](media\cortana-analytics-technical-guide-demand-forecast\ADF2.png)

Cinco das tubagens desta fábrica contêm [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) scripts que são utilizadas para criar partições e agregar os dados. Quando indicado, os scripts serão localizados na conta de [Armazenamento do Windows Azure](https://azure.microsoft.com/services/storage/) criada durante a configuração. A localização será: demandforecasting\\\\script\\\\ramo\\ \\ (ou https://[Your solução name].blob.core.windows.net/demandforecasting).

Semelhante ao [Azure sequência de análise](#azure-stream-analytics-1) de consultas, os scripts [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) possuam um conhecimento implícito informações sobre o formato de dados de entrada, estas consultas precisa para ser alteradas com base nos seus requisitos de [engenharia de funcionalidade](machine-learning\machine-learning-feature-selection-and-engineering.md) e o formato de dados.

#### <a name="aggregatedemanddatato1hrpipeline"></a>*AggregateDemandDataTo1HrPipeline*

Este pipeline de [tubagem](data-factory\data-factory-create-pipelines.md) contém uma única atividade - uma atividade de [HDInsightHive](data-factory\data-factory-hive-activity.md) utilizando [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) que executa um script [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para agregar cada 10 segundos transmitido em sequência nos dados de procura no nível de subestação ao nível da região por hora e coloque na [Armazenamento do Windows Azure](https://azure.microsoft.com/services/storage/) através da tarefa de análise da cadeia de Azure.

O script [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para esta tarefa da criação de partições é ***AggregateDemandRegion1Hr.hql***


#### <a name="loadhistorydemanddatapipeline"></a>*LoadHistoryDemandDataPipeline*

Este [pipeline](data-factory\data-factory-create-pipelines.md) contém duas atividades:
- Atividade de [HDInsightHive](data-factory\data-factory-hive-activity.md) utilizando [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) que executa um script Hive para agregar dados de pedido de histórico horários num nível de subestação ao nível da região por hora e colocar no armazenamento Azure durante a tarefa de análise de sequência do Azure

- Atividade de [cópia](https://msdn.microsoft.com/library/azure/dn835035.aspx) que mova os dados agregados de Blobs do Azure armazenamento à base de dados do SQL Azure aprovisionada como parte da instalação do modelo de solução.

O script [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para esta tarefa é ***AggregateDemandHistoryRegion.hql***.


#### <a name="mlscoringregionxpipeline"></a>*MLScoringRegionXPipeline*

Estes [tubagens](data-factory\data-factory-create-pipelines.md) conter várias atividades e cujo resultado final é as previsões visita a partir do Azure máquina aprendizagem experiência associados com este modelo de solução. São praticamente idênticos, exceto a cada um deles processa apenas a região diferente que está a ser executado por diferentes RegionID passada no pipeline de ADF e o script hive para cada região.  
As atividades contidas neste são:
-   Atividade de [HDInsightHive](data-factory\data-factory-hive-activity.md) utilizando [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) que executa um script do ramo para desempenhar agregações e engenharia de funcionalidade necessária para a experiência de aprendizagem do Azure máquina. Os scripts Hive para esta tarefa são respetivos ***PrepareMLInputRegionX.hql***.

-   Atividade de [cópia](https://msdn.microsoft.com/library/azure/dn835035.aspx) que move os resultados da atividade de [HDInsightHive](data-factory\data-factory-hive-activity.md) para um único blob de armazenamento do Windows Azure que pode ser acesso pela atividade de [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) .

-   Atividade de [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) que liga a formação de máquina Azure experimentar blob que resulta nos resultados de que está a ser colocados uma única armazenamento do Windows Azure.

#### <a name="copyscoredresultregionxpipeline"></a>*CopyScoredResultRegionXPipeline*
Estes [tubagens](data-factory\data-factory-create-pipelines.md) contêm uma única atividade - uma atividade de [cópia](https://msdn.microsoft.com/library/azure/dn835035.aspx) move os resultados da experiência do Azure máquina formação das respetivas ***MLScoringRegionXPipeline*** à base de dados do SQL Azure aprovisionada como parte da instalação do modelo de solução.

#### <a name="copyaggdemandpipeline"></a>*CopyAggDemandPipeline*
Este [tubagens](data-factory\data-factory-create-pipelines.md) contêm uma única atividade - uma atividade de [cópia](https://msdn.microsoft.com/library/azure/dn835035.aspx) move os dados de procura em curso agregado do ***LoadHistoryDemandDataPipeline*** à base de dados do SQL Azure aprovisionada como parte da instalação do modelo de solução.

#### <a name="copyregiondatapipeline-copysubstationdatapipeline-copytopologydatapipeline"></a>*CopyRegionDataPipeline, CopySubstationDataPipeline, CopyTopologyDataPipeline*
Estes [tubagens](data-factory\data-factory-create-pipelines.md) contêm uma única atividade - uma atividade de [cópia](https://msdn.microsoft.com/library/azure/dn835035.aspx) move os dados de referência de região/subestação/Topologygeo que são carregados para BLOBs do Azure armazenamento como parte da instalação do modelo de solução à base de dados do SQL Azure aprovisionada como parte da instalação do modelo de solução.

### <a name="azure-machine-learning"></a>Formação de máquina Azure
A experiência de [Aprendizagem do Azure máquina](https://azure.microsoft.com/services/machine-learning/) utilizada para este modelo de solução fornece predição do pedido da região. A experiência é específica para o conjunto de dados consumido e, por conseguinte, necessitará de modificação ou substituição específica para os dados que são inseriu.

## <a name="monitor-progress"></a>**Monitorizar progresso**
Depois do gerador de dados é iniciado, a tubagem começa a obter hidratado e os diferentes componentes da sua solução iniciam dar toques para seguinte ação os comandos emitidos pela fábrica de dados. Existem duas formas que pode monitorizar as em curso.

1. Verifique os dados a partir do armazenamento de Blobs do Azure.

    Uma da tarefa de análise da cadeia escreve os dados não processados recebidos ao armazenamento blob. Se clicar no componente de **Armazenamento de Blobs do Azure** da sua solução no ecrã com êxito implementado a solução e, em seguida, clique em **Abrir** no painel direito, será direcionado para o [portal de gestão Azure](https://portal.azure.com). Uma vez, clique em **Blobs**. No painel seguinte, irá ver uma lista de membros em contentores. Clique em **"energysadata"**. No painel seguinte, irá ver a pasta **"demandongoing"** . Dentro da pasta rawdata, irá ver as pastas com nomes, tais como data = 2016-01-28 etc. Se vir estas pastas, indica que os dados não processados são com êxito a ser gerado no seu computador e armazenados no armazenamento blob. Deverá visualizar ficheiros que devem ter tamanhos finitas em MB nessas pastas.

2. Verifique os dados a partir da base de dados do SQL Azure.

    O último passo do pipeline de é escrever dados (por exemplo, previsões do learning máquina) para a base de dados SQL. Poderá ter de esperar um máximo de 2 horas para os dados que seja apresentado na base de dados SQL. Uma forma para monitorizar a quantidade de dados está disponível na base de dados SQL é através do [portal de gestão Azure](https://manage.windowsazure.com/). No painel da esquerda, localize bases de dados SQL![](media\cortana-analytics-technical-guide-demand-forecast\SQLicon2.png) e clique nele. Em seguida, localize a base de dados (ou seja, demo123456db) e clique na mesma. Na página seguinte na secção **"Ligar a sua base de dados"** , clique em **"Executar Transact-SQL consultas contra a sua base de dados do SQL"**.

    Aqui, pode clicar na nova consulta e consulta para o número de linhas (por exemplo, "select count(*) a partir do DemandRealHourly)" à medida que aumenta a sua base de dados, deverá aumentar o número de linhas na tabela.)

3. Verifique os dados a partir do dashboard do Power BI.

    Pode configurar o dashboard de caminho quente do Power BI para monitorizar os dados não processados recebidos. Siga as instruções na secção "Power BI Dashboard".



## <a name="power-bi-dashboard"></a>**Dashboard do Power BI**

### <a name="overview"></a>Descrição geral

Esta secção descreve como configurar o dashboard do Power BI para visualizar os seus dados em tempo real a partir de análise da cadeia Azure (caminho quente), bem como a previsão de resultados a partir do Azure máquina de formação (caminho frio).


### <a name="setup-hot-path-dashboard"></a>Dashboard de caminho quente de configuração

Os passos seguintes irão guiá-lo como visualizar a exportação de dados em tempo real a partir de tarefas de análise da cadeia que foram geradas no momento da implementação da solução. Uma conta do [Power BI online](http://www.powerbi.com/) é necessária para efetuar os seguintes passos. Se não tiver uma conta, pode [criar uma](https://powerbi.microsoft.com/pricing).

1.  Adicione saída do Power BI na análise do Azure da cadeia (ASA).

    -  Terá de seguir as instruções no artigo [Azure da cadeia Analytics e do Power BI: um dashboard de análise em tempo real para visibilidade em tempo real de transmissão de dados](stream-analytics-power-bi-dashboard.md) para configurar a saída do seu trabalho Azure da cadeia Analytics como o seu dashboard do Power BI.

    - Localize a tarefa de análise da cadeia no seu [portal de gestão Azure](https://manage.windowsazure.com). O nome da tarefa deve ser: YourSolutionName + "streamingjob" + número aleatório + "asapbi" (ou seja, demostreamingjob123456asapbi).

    - Adicione uma saída obter para a tarefa de ASA. Defina o **Alias de saída** como **'PBIoutput'**. Defina o seu **Nome de conjunto de dados** e o **nome da tabela** como **'EnergyStreamData'**. Uma vez que adicionou a saída, clique em **"Iniciar"** na parte inferior da página para iniciar a tarefa de análise da cadeia. Deve obter uma mensagem de confirmação (*por exemplo*, "Inicial da cadeia analytics tarefa myteststreamingjob12345asablob foi concluída com êxito").

2. Iniciar sessão no [Power BI online](http://www.powerbi.com)

    -   No painel da esquerda secção de conjuntos de dados na área de trabalho do meu, deverá conseguir ver um novo conjunto de dados a mostrar no painel da esquerda do Power BI. Esta é os dados em tempo real que seguia a partir do Azure da cadeia Analytics no passo anterior.

    -   Certifique-se o painel de ***visualizações*** está aberto e é apresentado no lado direito do ecrã.

3. Crie o mosaico "Pedido por data/hora":
    -   Clique em conjunto de dados **'EnergyStreamData'** no painel da esquerda, secção de conjuntos de dados.

    -   Clique em **"gráfico de linhas"** ícone ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic8.png).

    -   Clique em 'EnergyStreamData' no painel de **campos** .

    -   Clique em **"Carimbo"** e certifique-se de que apresenta em "Eixos". Clique em **"Carregar"** e certifique-se de que apresenta em "Valores".

    -   Clique em **Guardar** no canto superior e nome o relatório como "EnergyStreamDataReport". O relatório com o nome "EnergyStreamDataReport" será apresentado na secção de relatórios no painel navegador esquerda.

    -   Clique em **"Pin Visual"** ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic6.png) ícone no canto superior direito deste gráfico de linhas, uma janela de "Pin para Dashboard" poderá aparecer para escolha um dashboard. Selecione "EnergyStreamDataReport", em seguida, clique em "Afixar".

    -   Paire com o rato sobre neste mosaico no dashboard de, clique em "Editar" ícone no canto superior direito para alterar o título como "Pedido por data/hora"

4.  Crie outros mosaicos de dashboard com base em conjuntos de dados adequados. A vista de final dashboard é apresentada abaixo.
        ![](media\cortana-analytics-technical-guide-demand-forecast\PBIFullScreen.png)


### <a name="setup-cold-path-dashboard"></a>Dashboard de caminho fria de configuração
No pipeline de dados de caminho fria, o objetivo essencial é obter a previsão de pedido de cada região. Power BI liga a uma base de dados do Azure SQL como origem de dados, onde estão armazenados os resultados da previsão.

> [AZURE.NOTE] 1) bastam alguns horas para recolher resultados previsão suficiente para o dashboard. Recomendamos que iniciar este processo 2-3 horas depois de almoço o gerador de dados. 2) neste passo, o pré-requisito é transferir e instalar o software gratuito do [ambiente de trabalho do Power BI](https://powerbi.microsoft.com/desktop).



1.  Obter as credenciais de base de dados.

    Terá de **nome de servidor de base de dados, nome da base de dados, nome de utilizador e palavra-passe** antes de passar para os passos seguintes. Eis os passos para orientá-lo como encontrá-las.

    -   Assim que **"base de dados do SQL Azure"** no seu diagrama de modelo de solução fica verde, clique na mesma e, em seguida, clique em **"Abrir"**. Será orientado para o portal de gestão do Azure e será aberta a página de informações de base de dados, assim.

    -   Na página, pode encontrar uma secção "Base de dados". Listas de saída da base de dados que criou. O nome da base de dados deverá ser **"O nome da solução número aleatório + 'db'"** (por exemplo, "mytest12345db").

    -   Clique em base de dados, na isolar novo pannel, que pode encontrar o seu nome de servidor de base de dados na parte superior. A base de dados servidor nome nome deve ser **"O nome da solução número aleatório + 'database.windows.net,1433'"** (por exemplo, "mytest12345.database.windows.net,1433").

    -   A base de dados de **nome de utilizador** e **palavra-passe** são a mesma como o nome de utilizador e palavra-passe previamente gravadas durante a implementação da solução.

2.  Atualizar a origem de dados do ficheiro fria caminho do Power BI
    -  Certifique-se de que instalou a versão mais recente do [ambiente de trabalho do Power BI](https://powerbi.microsoft.com/desktop).

    -   Na pasta **"DemandForecastingDataGeneratorv1.0"** tenha transferido, faça duplo clique no ficheiro de **' Power BI Template\DemandForecastPowerBI.pbix'.** As visualizações iniciais baseadas em dados fictícios. **Nota:** Se vir um erro equipamento Massagens, certifique-se de que instalou a versão mais recente do Power BI Desktop.

        Assim que abrir, na parte superior o ficheiro, clique em **' Editar consultas '**. Na janela isolar, faça duplo clique em **'Origem'** no painel da direita.
    ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic1.png)

    -   Na janela isolar, substitua **"Servidor"** e **"Base de dados"** com o seu próprio server e nomes de base de dados e, em seguida, clique em **"OK"**. Nome do servidor, certifique-se de que especifique a porta 1433 (**YourSolutionName.database.windows.net, 1433**). Ignore as mensagens de aviso que aparecem no ecrã.

    -   A isolar seguinte janela, verá duas opções no painel da esquerda (**Windows** e **base de dados**). Clique em **"Base de dados"**, preencha o **"Username"** e **"Palavra-passe"** (este é o nome de utilizador e palavra-passe introduzida quando implementado a solução e criou uma base de dados do Azure SQL pela primeira vez). Em ***Selecione qual o nível para aplicar estas definições***, selecione a opção de nível de base de dados. Em seguida, clique em **"Ligar"**.

    -   Assim que estiver a instruções novamente para a página anterior, feche a janela. Será apresentada uma mensagem iniciativas - clique em **Aplicar**. Por fim, clique no botão **Guardar** para guardar as alterações. O ficheiro do Power BI agora estabeleceu ligação ao servidor. Se as suas visualizações estão vazias, certifique-se de que desmarque as seleções nas visualizações para visualizar todos os dados ao clicar no ícone de borracha no canto superior direito das legendas. Utilize o botão de atualização para refletir novos dados sobre as visualizações. Inicialmente, só verá os dados de propagação nas suas visualizações tal como a fábrica de dados está agendada para atualizar a cada 3 horas. Após 3 horas, irá ver as previsões novos refletidas no seu visualizações quando atualizar os dados.

3. (Opcional) Publica o dashboard do caminho fria ao [Online do Power BI](http://www.powerbi.com/). Tenha em atenção que este passo necessita de uma conta do Power BI (ou conta do Office 365).

    -   Clique em **"Publicar"** e alguns segundos mais tarde é apresentada uma janela apresentar "Publicar no Power BI sucesso!" com uma marca de verificação verde. Clique na ligação abaixo "Demoprediction.pbix aberto no Power BI". Para obter instruções detalhadas, consulte o artigo [publicar a partir do Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).

    -   Para criar um novo dashboard: clique na **+** sinal junto a secção **Dashboards** no painel da esquerda. Introduza o nome "Pedido previsão demonstração" para este novo dashboard.

    -   Assim que abrir o relatório, clique em ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic6.png) para afixar todas as visualizações ao dashboard. Para obter instruções detalhadas, consulte o artigo [Afixar um mosaico a um dashboard do Power BI a partir de um relatório](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
        Ir para a página do dashboard e ajustar o tamanho e a localização da sua visualizações e editar os seus títulos. Para obter instruções detalhadas sobre como editar os seus mosaicos, consulte o artigo [Editar um mosaico – redimensionar, mover, mudar o nome, pin, eliminar, Adicionar hiperligação](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Eis um dashboard de exemplo com algumas visualizações de caminho fria afixada à mesma.

        ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic7.png)

4. (Opcional) Agendar Atualização da origem de dados.
    -     Para agendar a atualização dos dados, Paire o rato sobre o conjunto de dados **EnergyBPI Final** , clique em ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic3.png) e, em seguida, selecione **Agendar Atualização**.
    **Nota:** Se vir uma massagem de aviso, clique em **Editar credenciais** e certifique-se as suas credenciais de base de dados são os mesmos como aqueles descritos no passo 1.

    ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic4.png)

    -   Expanda a secção **Agendar Atualização** . Ative a "manter os dados atualizados".

    -   Agende a atualização com base nas suas necessidades. Para obter mais informações, consulte o artigo [da atualização de dados no Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/).


## <a name="how-to-delete-your-solution"></a>**Como eliminar a sua solução**
Certifique-se de que pare o gerador de dados quando não ativamente utilizando a solução, tal como a executar o gerador de dados serão implicam custos mais elevados. Elimine a solução se não estiver a utilizá-lo. Eliminar a sua solução irá eliminar todos os componentes aprovisionados na sua subscrição, quando tiver implementado a solução. Para eliminar a solução, clique no nome da sua solução no painel da esquerda do modelo de solução e clique em eliminar.

## <a name="cost-estimation-tools"></a>**Ferramentas de estimativa de custos**

As ferramentas de duas seguintes estão disponíveis para ajudá-lo a compreender melhor os custos totais envolvidos na executar o pedido de previsão para modelo de solução de energia na sua subscrição:

-   [Azure custo estimador ferramenta da Microsoft (online)](https://azure.microsoft.com/pricing/calculator/)

-   [Ferramenta de estimador de custo do Microsoft Azure (ambiente de trabalho)](http://www.microsoft.com/download/details.aspx?id=43376)

## <a name="acknowledgements"></a>**Confirmações**
Este artigo é criado por cientista dados Yijing Chen e engenharia de software Qiu mínimo na Microsoft.
