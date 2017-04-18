<properties
    pageTitle="Guia de técnico para o modelo de solução de informações da empresa Cortana para manutenção aspeto do Office em americano e outras empresas | Microsoft Azure"
    description="Um guia técnico para o modelo de solução com informações da empresa do Microsoft Cortana para de manutenção aspeto do Office no americano, utilitários e transportes."
    services="cortana-analytics"
    documentationCenter=""
    authors="fboylu"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cortana-analytics"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="fboylu" />

# <a name="technical-guide-to-the-cortana-intelligence-solution-template-for-predictive-maintenance-in-aerospace-and-other-businesses"></a>Guia de técnico para o modelo de solução de informações da empresa Cortana para manutenção aspeto do Office em americano e outras empresas

## <a name="acknowledgements"></a>**Confirmações**
Este artigo é criado por cientistas dados Yan Zhang, Gauher Shaheen, Fidan Boylu Uz e engenharia de software Duarte Grecoe na Microsoft.

## <a name="overview"></a>**Descrição geral**

Solução modelos foram concebidos para acelerar o processo de criação de uma demonstração de E2E na parte superior de conjunto de informações da empresa Cortana. Um modelo implementado irá aprovisionar a sua subscrição do componentes Cortana Intelligence necessários e crie a relação entre elas. Também-plantas pipeline de dados com dados de exemplo gerados a partir de uma aplicação de gerador de dados que irá transferir e instalar no seu computador local após implementar o modelo de solução. Os dados gerados pelo gerador de irão hidratado do pipeline de dados e iniciar gerar previsões de formação de máquina que, em seguida, podem ser visualizadas no dashboard do Power BI. O processo de implementação irá guiá-lo através de vários passos para configurar as suas credenciais de solução. Certifique-se de que estas credenciais como o nome da solução, nome de utilizador e palavra-passe que fornece durante a implementação de registo.  

O objetivo deste documento é explicar a arquitetura de referência e diferentes componentes aprovisionados na sua subscrição como parte deste modelo de solução. O documento também fala sobre como substituir os dados de exemplo com dados reais da sua própria consigam ver informações e previsões dos seus próprios dados. Para além disso, o documento aborda as partes do modelo de solução que necessitam de ser modificado se pretendia personalizar a solução com os seus dados. Obter instruções sobre como construir o dashboard do Power BI para este modelo de solução são fornecidas no final.

>[AZURE.TIP] Pode transferir e imprimir uma [versão PDF deste documento](http://download.microsoft.com/download/F/4/D/F4D7D208-D080-42ED-8813-6030D23329E9/cortana-analytics-technical-guide-predictive-maintenance.pdf).

## <a name="big-picture"></a>**Conceito**

![](media/cortana-analytics-technical-guide-predictive-maintenance\predictive-maintenance-architecture.png)

Quando a solução é implementada, são ativadas como vários serviços Azure dentro de conjunto de aplicações de análise de Cortana (*ou seja,* Hub do evento, a análise da cadeia, HDInsight, fábrica de dados, formação de máquina, *etc.*). O diagrama de arquitectura acima mostra como a manutenção aspeto do Office para o modelo de solução Conservadora fórmula foi criada a partir do fim para fim, de alto nível. Irá poderá investigar estes serviços no portal do azure clicando nos mesmos no diagrama de modelo de solução criado com a implementação da solução, à exceção dos HDInsight à medida que este serviço está aprovisionado a pedido quando as atividades de tubagem relacionados são necessárias para executar e eliminados posteriormente.
Pode transferir uma [versão completa do diagrama](http://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png).

As secções seguintes descrevem cada porção.

## <a name="data-source-and-ingestion"></a>**Origem de dados e ingestão**

### <a name="synthetic-data-source"></a>Origem de dados síntese

Para este modelo de origem de dados utilizada é gerada a partir de uma aplicação de ambiente de trabalho que irá transferir e executar localmente após a implementação com êxito. Irá encontrar as instruções para transferir e instalar esta aplicação na barra de propriedades quando seleciona o primeiro nó denominado gerador de dados de manutenção aspeto do Office no diagrama de modelo de solução. Esta aplicação feeds o serviço do [Azure evento concentrador](#azure-event-hub) com pontos de dados ou eventos, que serão utilizados no resto do fluxo de solução. Esta origem de dados é composto por ou derivada de dados publicamente disponíveis a partir do [repositório de dados NASA](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/) utilizando o [motor de turbohélice degradação do conjunto de dados de simulação](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/#turbofan).

A aplicação de geração evento irão povoar o concentrador de evento Azure apenas enquanto está a executar no seu computador.

### <a name="azure-event-hub"></a>Concentrador de evento Azure

O serviço do [Azure evento concentrador](https://azure.microsoft.com/services/event-hubs/) é o destinatário da entrada fornecida pela origem de dados síntese descrito acima.

## <a name="data-preparation-and-analysis"></a>**Preparação de dados e análise**


### <a name="azure-stream-analytics"></a>Análise de sequência Azure

O serviço de [Análise da cadeia de Azure](https://azure.microsoft.com/services/stream-analytics/) é utilizado para fornecer perto de análise em tempo real na sequência de entrada de serviço [Concentrador de eventos do Azure](#azure-event-hub) e publicar resultados de um dashboard do [Power BI](https://powerbi.microsoft.com) , bem como todos os eventos recebidos observou para o serviço de [Armazenamento do Windows Azure](https://azure.microsoft.com/services/storage/) para processamento posterior pelo serviço [Azure dados fábrica](https://azure.microsoft.com/documentation/services/data-factory/) do arquivo.

### <a name="hd-insights-custom-aggregation"></a>Agregação personalizada HD informações

O serviço de conhecimentos aprofundados do Azure HD é utilizado para executar scripts de [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) (orquestradas pela fábrica de dados do Azure) para fornecer agregações sobre os eventos bruto que estavam arquivados utilizando o serviço de análise da cadeia de Azure.

### <a name="azure-machine-learning"></a>Formação de máquina Azure

O serviço de [Aprendizagem do Azure máquina](https://azure.microsoft.com/services/machine-learning/) é utilizado (orquestrada pela fábrica de dados do Azure) para efetuar previsões na vida útil remanescente (RUL) de um motor de avião determinado tendo em conta as entradas recebidas.

## <a name="data-publishing"></a>**Publicação de dados**


### <a name="azure-sql-database-service"></a>Serviço de bases de dados do Azure SQL

O serviço de [Base de dados do SQL Azure](https://azure.microsoft.com/services/sql-database/) é utilizado para armazenar (geridos pelo Azure dados fábrica) previsões recebidas pelo serviço Azure máquina formação que será consumido no dashboard [Do Power BI](https://powerbi.microsoft.com) .

## <a name="data-consumption"></a>**Consumo de dados**

### <a name="power-bi"></a>Power BI

O serviço do [Power BI](https://powerbi.microsoft.com) é utilizado para mostrar um dashboard que contenha agregações e alertas fornecidas pelo serviço do [Azure da cadeia Analytics](https://azure.microsoft.com/services/stream-analytics/) , bem como previsões RUL armazenados numa [Base de dados do Azure SQL](https://azure.microsoft.com/services/sql-database/) que foram criadas utilizando o serviço do [Azure máquina aprendizagem](https://azure.microsoft.com/services/machine-learning/) . Para obter instruções sobre como construir o dashboard do Power BI para este modelo de solução, consulte a secção abaixo.

## <a name="how-to-bring-in-your-own-data"></a>**Como cumprir os seus dados**

Esta secção descreve como colocar os seus próprios dados para Azure e quais são as áreas seriam exigir alterações para os dados que trazer para a esta arquitectura.

Não é provável que qualquer conjunto de dados que trazer iria corresponder o conjunto de dados utilizado pelo [motor de turbohélice degradação do conjunto de dados de simulação](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/#turbofan) utilizado para este modelo de solução. Noções sobre os seus dados e os requisitos de serão crucial como modificar este modelo para trabalhar com os seus dados. Se esta é a sua primeira exposição para o serviço de aprendizagem do Azure máquina, pode obter uma introdução à-lo ao utilizar o exemplo no [como criar a sua primeira experiência](machine-learning-create-experiment.md).

As secções seguintes irão abordam as secções do modelo que irão necessitar modificações quando um novo conjunto de dados é introduzido.

### <a name="azure-event-hub"></a>Concentrador de evento Azure

O serviço do Azure evento concentrador é muito genérico, assim que podem ser registados dados para o centro no formato CSV ou JSON. Sem processamento especial ocorre no Centro de eventos do Azure, mas é importante que compreender os dados que são alimentados na.

Este documento não descrevem como ingerir esta última os seus dados, mas pode enviar facilmente eventos ou dados a um concentrador de evento Azure utilizar a API de concentrador de evento.

### <a name="azure-stream-analytics"></a>Análise de sequência Azure

O serviço Azure da cadeia Analytics é utilizado para fornecer perto de análise em tempo real lendo partir sequências de dados e exportar dados para qualquer número de origens.

Para a manutenção aspeto do Office para o modelo de solução Conservadora, a consulta de análise da cadeia de Azure consiste em quatro subconsultas, cada eventos consome a partir do serviço do concentrador de eventos do Azure e ter saídas de quatro localizações distintas. Estas saídas é constituída por três conjuntos de dados do Power BI e uma localização de armazenamento do Windows Azure.

A consulta de análise da cadeia de Azure pode ser encontrada por:

-   Início de sessão no portal do Azure

-   Localizar as tarefas de análise da cadeia ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-stream-analytics.png) que foram geradas quando a solução foi implementada (*por exemplo*, **maintenancesa02asapbi** e **maintenancesa02asablob** para a solução de manutenção aspeto do Office)

-   Selecionar

    -   ***Entradas do tipo*** para ver a entrada de consulta

    -   ***Consulta*** para ver a própria consulta

    -   ***EXPORTA*** para ver os resultados de diferentes

Pode encontrar informações sobre construção de consulta de análise da cadeia de Azure na [Referência de consulta de análise da cadeia](https://msdn.microsoft.com/library/azure/dn834998.aspx) no MSDN.

Nesta solução, as consultas de saída três conjuntos de dados com perto de análise em tempo real obter informações sobre o fluxo de dados de entrada a um dashboard do Power BI, que é fornecido como parte do modelo solução. Porque não existe implícito dados de conhecimento informações sobre o formato de dados de entrada, estas consultas seriam necessário ser alteradas com base no seu formato dos dados.

A consulta na segunda da cadeia analytics tarefa **maintenancesa02asablob** simplesmente exporta todos os eventos [Concentrador de evento](https://azure.microsoft.com/services/event-hubs/) ao [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) e não requer, portanto, nenhuma alteração, independentemente do formato de dados como as informações de completo do evento são transmitido em sequência ao armazenamento.

### <a name="azure-data-factory"></a>Dados Azure fábrica

O serviço de [Fábrica do Azure dados](https://azure.microsoft.com/documentation/services/data-factory/) orquestra circulação e processamento de dados. Na manutenção aspeto do Office para o modelo de solução Conservadora a fábrica de dados é constituída por três [tubagens](../data-factory/data-factory-create-pipelines.md) que mova e processar dados a utilizar tecnologias de vários.  Pode aceder a fábrica de dados ao abrir o o nó de fábrica de dados na parte inferior do diagrama de modelo solução criado com a implementação da solução. Esta será direcionado para a fábrica de dados no seu portal Azure. Se os erros em seu conjuntos de dados, pode ignorar aqueles como estão devido a fábrica de dados a ser implementada antes do gerador de dados foi iniciado. Esses erros não impede que a fábrica de dados a funcionar.

![](media/cortana-analytics-technical-guide-predictive-maintenance/data-factory-dataset-error.png)

Esta secção descreve os necessárias [tubagens](../data-factory/data-factory-create-pipelines.md) e as [atividades](../data-factory/data-factory-create-pipelines.md) contidas na [Azure fábrica de dados](https://azure.microsoft.com/documentation/services/data-factory/). Segue-se a vista de diagrama da solução.

![](media/cortana-analytics-technical-guide-predictive-maintenance/azure-data-factory.png)

Duas tubagens desta fábrica contêm [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) scripts que são utilizadas para criar partições e agregar os dados. Quando indicado, os scripts serão localizados na conta de [Armazenamento do Windows Azure](https://azure.microsoft.com/services/storage/) criada durante a configuração. A localização será: maintenancesascript\\\\script\\\\ramo\\ \\ (ou https://[Your solução name].blob.core.windows.net/maintenancesascript).

Semelhante ao [Azure sequência de análise](#azure-stream-analytics-1) de consultas, os scripts [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) possuam um conhecimento implícito informações sobre o formato de dados de entrada, estas consultas precisa para ser alteradas com base nos seus requisitos de [engenharia de funcionalidade](machine-learning-feature-selection-and-engineering.md) e o formato de dados.

#### <a name="aggregateflightinfopipeline"></a>*AggregateFlightInfoPipeline*

Este [pipeline](../data-factory/data-factory-create-pipelines.md) contém uma única atividade - uma atividade de [HDInsightHive](../data-factory/data-factory-hive-activity.md) utilizando [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) que executa um script [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para criar a partições dados colocar no [Armazenamento Azure](https://azure.microsoft.com/services/storage/) durante a tarefa de [Análise da cadeia de Azure](https://azure.microsoft.com/services/stream-analytics/) .

O script [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para esta tarefa da criação de partições é ***AggregateFlightInfo.hql***

#### <a name="mlscoringpipeline"></a>*MLScoringPipeline*

Este [pipeline](../data-factory/data-factory-create-pipelines.md) contém várias atividades e cujo resultado final é que experimentar as previsões visita a partir do [Azure máquina aprendizagem](https://azure.microsoft.com/services/machine-learning/) associados com este modelo de solução.

As atividades contidas neste são:

-   Atividade de [HDInsightHive](../data-factory/data-factory-hive-activity.md) utilizando um [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) que executa um script [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para efetuar agregações e engenharia de funcionalidade necessária para a experiência de [Aprendizagem do Azure máquina](https://azure.microsoft.com/services/machine-learning/) .
    O script [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para esta tarefa da criação de partições é ***PrepareMLInput.hql***.

-   Atividade de [cópia](https://msdn.microsoft.com/library/azure/dn835035.aspx) que move os resultados da atividade de [HDInsightHive](../data-factory/data-factory-hive-activity.md) para um único blob de [Armazenamento do Windows Azure](https://azure.microsoft.com/services/storage/) que pode ser acesso pela atividade de [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) .

-   Atividade de [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) que liga para o [Azure máquina aprendizagem](https://azure.microsoft.com/services/machine-learning/) experimentar que resulta nos resultados que está a ser colocados um único blob de [Armazenamento do Windows Azure](https://azure.microsoft.com/services/storage/) .

#### <a name="copyscoredresultpipeline"></a>*CopyScoredResultPipeline*

Este [pipeline](../data-factory/data-factory-create-pipelines.md) contém uma única atividade - uma atividade de [cópia](https://msdn.microsoft.com/library/azure/dn835035.aspx) move-se que os resultados de [Formação de máquina Azure](#azure-machine-learning) experimentar a partir do ***MLScoringPipeline*** para a [Base de dados do SQL Azure](https://azure.microsoft.com/services/sql-database/) aprovisionada como parte da instalação do modelo de solução.

### <a name="azure-machine-learning"></a>Formação de máquina Azure

A experiência de [Aprendizagem do Azure máquina](https://azure.microsoft.com/services/machine-learning/) utilizada para este modelo de solução fornece o restante útil vida (RUL) de um motor de avião. A experiência é específica para o conjunto de dados consumido e, por conseguinte, necessitará de modificação ou substituição específica para os dados que são inseriu.

Para obter informações sobre como a experiência de aprendizagem do Azure máquina foi criada, consulte o artigo [Manutenção aspeto do Office: passo 1 de 3, preparação de dados e engenharia de funcionalidade](http://gallery.cortanaanalytics.com/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2).

## <a name="monitor-progress"></a>**Monitorizar progresso**
 Depois do gerador de dados é iniciado, a tubagem começa a obter hidratado e os diferentes componentes da sua solução iniciam dar toques para seguinte ação os comandos emitidos pela fábrica de dados. Existem duas formas que pode monitorizar as em curso.

1. Uma da tarefa de análise da cadeia escreve os dados não processados recebidos ao armazenamento blob. Se clicar no componente de armazenamento de BLOBs da sua solução no ecrã com êxito implementado a solução e, em seguida, clique em abrir no painel direito, será direcionado para o [portal de gestão](https://portal.azure.com/). Uma vez, clique em Blobs. No painel seguinte, irá ver uma lista de membros em contentores. Clique em **maintenancesadata**. No painel seguinte, irá ver a pasta **rawdata** . Dentro da pasta rawdata, irá ver as pastas com nomes, tais como a hora = 17, hora = 18 etc. Se vir estas pastas, indica que os dados não processados são com êxito a ser gerado no seu computador e armazenados no armazenamento blob. Deverá ver ficheiros csv que devem ter tamanhos finitas em MB nessas pastas.

2. O último passo do pipeline de é escrever dados (por exemplo, previsões de formação de máquina) para a base de dados SQL. Poderá ter de esperar um máximo de três horas para os dados que seja apresentado na base de dados SQL. Uma forma para monitorizar a quantidade de dados está disponível na base de dados SQL é através do [portal de azure](https://manage.windowsazure.com/). No painel da esquerda, localize bases de dados SQL ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-SQL-databases.png) e clique nele. Em seguida, localize a sua base de dados **pmaintenancedb** e clique na mesma. Na página seguinte na parte inferior, clique em GERIR

    ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-manage.png).

    Aqui, pode clicar em nova consulta e query para o número de linhas (por exemplo, selecione count(*) a partir do PMResult). À medida que aumenta a sua base de dados, deverá aumentar o número de linhas na tabela.


## <a name="power-bi-dashboard"></a>**Dashboard do Power BI**

### <a name="overview"></a>Descrição geral

Esta secção descreve como configurar o dashboard do Power BI para visualizar os seus dados em tempo real a partir de análise da cadeia Azure (caminho quente), bem como os resultados de previsão do lote a partir do Azure máquina de formação (caminho frio).

### <a name="setup-cold-path-dashboard"></a>Dashboard de caminho fria de configuração

No pipeline de dados de caminho fria, o objetivo essencial é obter o aspeto do Office RUL (restante vida útil) de cada motor de avião assim que seja concluído um voo (ciclo). O resultado da previsão é atualizado a cada 3 horas para as previsões os motores de avião que tem terminado de um voo durante as horas de 3 anteriores.

Power BI liga a uma base de dados do Azure SQL como origem de dados, onde estão armazenados os resultados da previsão. Nota: 1) relativamente a implementar a solução, uma previsão real irá aparecer na base de dados no prazo de 3 horas.
O ficheiro de pbix que veio incluída com a transferência de gerador contém alguns dados de propagação para que pode criar o dashboard do Power BI imediatamente. 2) neste passo, o pré-requisito é transferir e instalar o software gratuito do [ambiente de trabalho do Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/).

Os passos seguintes irão guiá-lo sobre como ligar o ficheiro de pbix à base de dados SQL que foi fiadas para cima no momento da implementação da solução que contêm dados (*por exemplo*. resultados de previsão) para visualização.

1.  Obter as credenciais de base de dados.

    Terá de **nome de servidor de base de dados, nome da base de dados, nome de utilizador e palavra-passe** antes de passar para os passos seguintes. Eis os passos para orientá-lo como encontrá-las.

    -   Assim que **' base de dados do SQL Azure'** no seu diagrama de modelo de solução fica verde, clique na mesma e, em seguida, clique em **'Abrir'**.

    -   Irá ver uma novo separador/janela do browser que apresenta a página do portal Azure. Clique em **grupos de recursos** no painel da esquerda.

    -   Selecione a subscrição que está a utilizar para implementar a solução e, em seguida, selecione **' YourSolutionName\_ResourceGroup'**.

    -   Na isolar novo painel, clique na ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-sql.png) ícone para aceder à sua base de dados. O seu nome de base de dados é junto a este ícone (*por exemplo*, **'pmaintenancedb'**) e o **nome de servidor de base de dados** está listado na propriedade nome do servidor e deverá ter um aspeto semelhante ao **YourSoutionName.database.windows.net**.

    -   A base de dados de **nome de utilizador** e **palavra-passe** são a mesma como o nome de utilizador e palavra-passe previamente gravadas durante a implementação da solução.

2.  Atualize a origem de dados do ficheiro de relatório caminho fria com o Power BI Desktop.

    -   Na pasta no seu PC onde transferido e descompactados o ficheiro gerador, faça duplo clique sobre a **obter\\PredictiveMaintenanceAerospace.pbix** ficheiro. Se vir todas as mensagens de aviso quando abre o ficheiro, ignorá-los. Na parte superior o ficheiro, clique em **' Editar consultas '**.

        ![](media\cortana-analytics-technical-guide-predictive-maintenance\edit-queries.png)

    -   Verá duas tabelas, **RemainingUsefulLife** e **PMResult**. Selecione a primeira tabela e clique em ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-query-settings.png) junto de **'Origem'** em **Passos aplicados** no painel **Definições da consulta** da direita. Ignore todas as mensagens de aviso que são apresentadas.

    -   Na janela isolar, substituir **'Servidor'** e **'Database'** com o seu próprio server e nomes de base de dados e, em seguida, clique em **'OK'**. Nome do servidor, certifique-se de que especifique a porta 1433 (**YourSoutionName.database.windows.net, 1433**). Deixe o campo de base de dados como **pmaintenancedb**. Ignore as mensagens de aviso que aparecem no ecrã.

    -   A isolar seguinte janela, verá duas opções no painel da esquerda (**Windows** e **base de dados**). Clique em **'Database'**, preencha o **'Username'** e **'Palavra-passe'** (este é o nome de utilizador e palavra-passe introduzida quando implementado a solução e criou uma base de dados do Azure SQL pela primeira vez). Em ***Selecione qual o nível para aplicar estas definições***, selecione a opção de nível de base de dados. Em seguida, clique em **'Ligar'**.

    -   Clique na segunda tabela **PMResult** , em seguida, clique em ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-navigation.png) junto de **'Origem'** em **Passos aplicados** no painel **Definições da consulta** à direita e atualize os nomes de servidor e base de dados, tal como os passos acima e clique em OK.

    -   Assim que estiver a instruções novamente para a página anterior, feche a janela. Será apresentada uma mensagem iniciativas - clique em **Aplicar**. Por fim, clique no botão **Guardar** para guardar as alterações. O ficheiro do Power BI agora estabeleceu ligação ao servidor. Se as suas visualizações estão vazias, certifique-se de que desmarque as seleções nas visualizações para visualizar todos os dados ao clicar no ícone de borracha no canto superior direito das legendas. Utilize o botão de atualização para refletir novos dados sobre as visualizações. Inicialmente, só verá os dados de propagação nas suas visualizações tal como a fábrica de dados está agendada para atualizar a cada 3 horas. Após 3 horas, irá ver as previsões novos refletidas no seu visualizações quando atualizar os dados.

3.  (Opcional) Publica o dashboard do caminho fria ao [Online do Power BI](http://www.powerbi.com/). Tenha em atenção que este passo necessita de uma conta do Power BI (ou conta do Office 365).

    -   Clique em **'Publicar'** e alguns segundos mais tarde é apresentada uma janela apresentar "Publicar no Power BI sucesso!" com uma marca de verificação verde. Clique na ligação abaixo "Abrir PredictiveMaintenanceAerospace.pbix no Power BI". Para obter instruções detalhadas, consulte o artigo [publicar a partir do Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).

    -   Para criar um novo dashboard: clique na **+** sinal junto a secção **Dashboards** no painel da esquerda. Introduza o nome "Demonstração de manutenção aspeto do Office" para este novo dashboard.

    -   Assim que abrir o relatório, clique em ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-pin.png) para afixar todas as visualizações ao dashboard. Para obter instruções detalhadas, consulte o artigo [Afixar um mosaico a um dashboard do Power BI a partir de um relatório](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
    Ir para a página do dashboard e ajustar o tamanho e a localização da sua visualizações e editar os seus títulos. Para obter instruções detalhadas sobre como editar os seus mosaicos, consulte o artigo [Editar um mosaico – redimensionar, mover, mudar o nome, pin, eliminar, Adicionar hiperligação](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Eis um dashboard de exemplo com algumas visualizações de caminho fria afixada à mesma.  Dependendo quanto tempo executar o seu gerador de dados, os seus números de visualizações podem ser diferentes.
    <br/>
    ![](media\cortana-analytics-technical-guide-predictive-maintenance\final-view.png)
<br/>
    -   Para agendar a atualização dos dados, Paire o rato sobre o conjunto de dados **PredictiveMaintenanceAerospace** , clique em ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-elipsis.png) e, em seguida, selecione **Agendar Atualização**.
<br/>
        **Nota:** Se vir uma massagem de aviso, clique em **Editar credenciais** e certifique-se as suas credenciais de base de dados são os mesmos como aqueles descritos no passo 1.
<br/>
    ![](media\cortana-analytics-technical-guide-predictive-maintenance\schedule-refresh.png)
<br/>
    -   Expanda a secção **Agendar Atualização** . Ative a "manter os dados atualizados".
    <br/>
    -   Agende a atualização com base nas suas necessidades. Para obter mais informações, consulte o artigo [da atualização de dados no Power BI](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi).

### <a name="setup-hot-path-dashboard"></a>Dashboard de caminho quente de configuração

Os passos seguintes irão guiá-lo como visualizar a exportação de dados em tempo real a partir de tarefas de análise da cadeia que foram geradas no momento da implementação da solução. Uma conta do [Power BI online](http://www.powerbi.com/) é necessária para efetuar os seguintes passos. Se não tiver uma conta, pode [criar uma](https://powerbi.microsoft.com/pricing).

1.  Adicione saída do Power BI na análise do Azure da cadeia (ASA).

    -  Terá de seguir as instruções no artigo [Azure da cadeia Analytics e do Power BI: um dashboard de análise em tempo real para visibilidade em tempo real de transmissão de dados](stream-analytics-power-bi-dashboard.md) para configurar a saída do seu trabalho Azure da cadeia Analytics como o seu dashboard do Power BI.
    - A consulta ASA tem três saídas que são **aircraftmonitor**, **aircraftalert**e **flightsbyhour**. Pode ver a consulta clicando no separador consulta. Correspondente a cada uma destas tabelas, terá adicionar uma saída ASA. Quando adiciona o resultado da primeira (*por exemplo,* **aircraftmonitor**) certificar-se de que o **Alias de saída**, o **Nome do conjunto de dados** e o **Nome de tabela** estão a mesma (**aircraftmonitor**). Repita os passos para adicionar saídas para **aircraftalert**e **flightsbyhour**. Assim que tiver adicionado todos os três tabelas de saída e a tarefa de ASA a trabalhar, deve obter uma mensagem de confirmação (*por exemplo*, "Inicial da cadeia analytics tarefa maintenancesa02asapbi foi concluída com êxito").

2. Iniciar sessão no [Power BI online](http://www.powerbi.com)

    -   No painel da esquerda secção de conjuntos de dados no meu espaço de trabalho, os nomes **aircraftmonitor**, **aircraftalert**e **flightsbyhour** do ***conjunto de dados*** deverão aparecer. Esta é os dados em tempo real que seguia a partir do Azure da cadeia Analytics no passo anterior. O conjunto de dados **flightsbyhour** podem não aparecer ao mesmo tempo, como os outros dois conjuntos de dados devido a natureza da consulta SQL atrás da mesma. No entanto, deve ser apresentada depois de uma hora.
    -   Certifique-se o painel de ***visualizações*** está aberto e é apresentado no lado direito do ecrã.

3. Assim que tiver os dados a fluir Power BI, pode começar a visualizar os dados em tempo real. Abaixo está um dashboard de exemplo com algumas visualizações de caminho quente afixado à mesma. Pode criar outros mosaicos de dashboard com base em conjuntos de dados adequados. Dependendo quanto tempo executar o seu gerador de dados, os seus números de visualizações podem ser diferentes.


    ![](media\cortana-analytics-technical-guide-predictive-maintenance\dashboard-view.png)

4. Aqui estão alguns passos para criar um dos mosaicos acima – a "da vista de Sensor 11 vs. limiar 48.26" dispor em mosaico:

    -   Clique em **aircraftmonitor** do conjunto de dados no painel da esquerda, secção de conjuntos de dados.

    -   Clique no ícone do **Gráfico de linhas** .

    -   Clique em **processadas** no painel de **campos** para que mostra em "Eixos" no painel de **visualizações** .

    -   Clique em "s11" e "s11\_alerta" para que ambos os aparecem em "Valores". Clique na seta pequena junto ao **s11** e **s11\_alerta**, alterar "Soma" para "Média".

    -   Clique em **Guardar** no canto superior e o nome do relatório "aircraftmonitor". O relatório com o nome "aircraftmonitor" será apresentado na secção **relatórios** no painel **navegador** à esquerda.

    -   Clique no ícone de **Pino Visual** no canto superior direito deste gráfico de linhas. Numa janela de "Pin para Dashboard" poderá aparecer para escolha um dashboard. Selecione "Demonstração de manutenção aspeto do Office", em seguida, clique em "Afixar".

    -   Paire o cursor do rato sobre neste mosaico no dashboard de, clique no ícone de "Editar" no canto superior direito para alterar o título para "Da vista de Sensor 11 vs. limiar 48.26" e subtítulo para "Média através de frota ao longo do tempo".

## <a name="how-to-delete-your-solution"></a>**Como eliminar a sua solução**
Certifique-se de que pare o gerador de dados quando não ativamente utilizando a solução, tal como a executar o gerador de dados serão implicam custos mais elevados. Elimine a solução se não estiver a utilizá-lo. Eliminar a sua solução irá eliminar todos os componentes aprovisionados na sua subscrição, quando tiver implementado a solução. Para eliminar a solução, clique no nome da sua solução no painel da esquerda do modelo de solução e clique em eliminar.

## <a name="cost-estimation-tools"></a>**Ferramentas de custo estimado**

As ferramentas de duas seguintes estão disponíveis para ajudá-lo a compreender melhor os custos totais envolvidos na executar a manutenção de aspeto do Office para o modelo de solução Conservadora na sua subscrição:

-   [Azure custo estimador ferramenta da Microsoft (online)](https://azure.microsoft.com/pricing/calculator/)

-   [Ferramenta de estimador de custo do Microsoft Azure (ambiente de trabalho)](http://www.microsoft.com/download/details.aspx?id=43376)
