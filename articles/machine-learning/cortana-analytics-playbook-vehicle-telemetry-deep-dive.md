<properties 
    pageTitle="Playbook de solução de análise de telemetria veículo: vôo picado abrangente a solução | Microsoft Azure" 
    description="Utilizar as funcionalidades de análise de Cortana para obter informações em tempo real e aspeto do Office no estado de funcionamento do veículo e condução hábitos." 
    services="machine-learning" 
    documentationCenter="" 
    authors="bradsev" 
    manager="jhubbard" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="bradsev" />


# <a name="vehicle-telemetry-analytics-solution-playbook-deep-dive-into-the-solution"></a>Playbook de solução de análise de telemetria veículo: vôo picado abrangente a solução

Este **menu** contém ligações para as secções deste playbook: 

[AZURE.INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]

Brocas esta secção para baixo para cada uma das fases descritas a arquitetura de solução com instruções e ponteiros para uma personalização mais. 

## <a name="data-sources"></a>Origens de dados

A solução utiliza duas origens de dados diferentes:

- **sinais de veículo simulada e conjunto de dados de diagnóstico** e 
- **catálogo de veículo**

Um simulator de telemática veículo é incluído como parte desta solução. Este procedimento emite informações de diagnóstico e sinais correspondente para o estado do veículo e para o padrão de condução num determinado ponto no tempo. Clique em [Veículo telemática Simulator](http://go.microsoft.com/fwlink/?LinkId=717075) para transferir a **Veículo telemática Simulator Visual Studio solução** para personalizações com base nos seus requisitos. O catálogo de veículo contém um conjunto de dados de referência com um VIN para o mapeamento do modelo.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig2-vehicle-telematics-simulator.png)

*Figura 2 – veículo telemática Simulator*

Este é um conjunto de dados formatados como JSON que contém o esquema que se segue.

Coluna | Descrição | Valores 
 ------- | ----------- | --------- 
VIN | Número de identificação de veículo gerado aleatoriamente | Isto é obtido a partir de uma lista de números de identificação de veículo gerado aleatoriamente 10.000 principal.
Temperatura exterior | Temperatura exterior onde está a condicionar veículo | Número gerado aleatoriamente a partir de 0-100
Temperatura motor | A temperatura do motor de veículo | Número gerado aleatoriamente a partir de 0-500
Velocidade | A velocidade do motor no qual está a condicionar veículo | Número gerado aleatoriamente a partir de 0-100
Combustível | O nível de combustível do veículo | Número gerado aleatoriamente a partir de 0-100 (indica a percentagem de nível de combustível)
EngineOil | O nível de Adamastor motores de veículo | Número gerado aleatoriamente a partir de 0-100 (indica a percentagem de nível do motor Adamastor)
Pneus pressão | A pressão pneus do veículo | Número gerado aleatoriamente a partir de 0-50 (indica a percentagem de nível de pressão pneus)
Odométrico | Leitura do conta-quilómetros do veículo | Número gerado aleatoriamente a partir de 0 a 200000
Accelerator_pedal_position | A posição de pedais accelerator do veículo | Número gerado aleatoriamente a partir de 0-100 (indica a percentagem de nível de accelerator)
Parking_brake_status | Indica se o veículo é estacionando ou não | VERDADEIRO ou FALSO
Headlamp_status | Indica onde luz é no ou não | VERDADEIRO ou FALSO
Brake_pedal_status | Indica se a pedais travão são premida ou não | VERDADEIRO ou FALSO
Transmission_gear_position | A posição de engrenagem de transmissão de veículo | Estados: primeiro, segundo, terceiro, quarto, quinto, sexta, sétimo, oitavo
Ignition_status | Indica se o veículo está em execução ou parados | VERDADEIRO ou FALSO
Windshield_wiper_status | Indica se a limpa pára-brisas está ativada ou não | VERDADEIRO ou FALSO
ABS | Indica se ABS é interessada ou não | VERDADEIRO ou FALSO
Data/hora | O carimbo de data/hora quando é criado o ponto de dados | Data
Cidade | A localização do veículo | 4 cidades nesta solução: Belavista, Redmond, Sammamish, Seattle


O conjunto de dados veículo da referência do modelo contém VIN para o mapeamento de modelo. 

VIN | Modelo |
--------------|------------------
FHL3O1SA4IEHB4WU1 | Veículo |
8J0U8XCPRGW4Z3NQE | Híbrido |
WORG68Z2PLTNZDBI7 | Berlina da família |
JTHMYHQTEPP4WBMRN | Veículo |
W9FTHG27LZN1YWO0Y | Híbrido |
MHTP9N792PHK08WJM | Berlina da família |
EI4QXI2AXVQQING4I | Veículo |
5KKR2VB4WHQH97PF8 | Híbrido |
W9NSZ423XZHAONYXB | Berlina da família |
26WJSGHX4MA5ROHNL | Convertible |
GHLUB6ONKMOSI7E77 | Estação meio |
9C2RHVRVLMEJDBXLP | Carro compacto |
BRNHVMZOUJ6EOCP32 | SUV pequenas |
VCYVW0WUZNBTM594J | Carro desportivo |
HNVCE6YFZSA5M82NY | SUV médio |
4R30FOR7NUOBL05GJ | Estação meio |
WYNIIY42VKV6OQS1J | SUV grande |
8Y5QKG27QET1RBK7I | SUV grande |
DF6OX2WSRA6511BVG | Hatchback |
Z2EOZWZBXAEW3E60T | Veículo |
M4TV6IEALD5QDS3IR | Híbrido |
VHRA1Y2TGTA84F00H | Berlina da família |
R0JAUHT1L1R3BIKI0 | Veículo |
9230C202Z60XX84AU | Híbrido |
T8DNDN5UDCWL7M72H | Berlina da família |
4WPYRUZII5YV7YA42 | Veículo |
D1ZVY26UV2BFGHZNO | Híbrido |
XUF99EW9OIQOMV7Q7 | Berlina da família
8OMCL3LGI7XNCC21U | Convertible |
…….  |   |


### <a name="to-generate-simulated-data"></a>Para gerar simulados dados
1.  Para transferir o pacote de simulator de dados, clique na seta no canto superior direito do nó veículo telemática Simulator. Guarde e extrair os ficheiros localmente no seu computador. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig3-vehicle-telemetry-blueprint.png)*Figura 3 – veículo telemetria Analytics solução plano*

2.  No seu computador local, vá para a pasta onde o pacote de veículo telemática Simulator que extraída. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig4-vehicle-telematics-simulator-folder.png)*Figura 4 – veículo telemática Simulator pasta*

3.  Execute a aplicação **CarEventGenerator.exe**.

### <a name="references"></a>Referências

[Solução do Visual Studio veículo telemática Simulator](http://go.microsoft.com/fwlink/?LinkId=717075) 

[Concentrador de evento Azure](https://azure.microsoft.com/services/event-hubs/)

[Dados Azure fábrica](https://azure.microsoft.com/documentation/learning-paths/data-factory/)


## <a name="ingestion"></a>Ingestão
Combinações de Azure evento concentradores, a análise da cadeia e fábrica de dados são utilizadas para ingerir esta última sinais de veículo, os eventos de diagnóstico e em tempo real e batch analytics. Todos estes componentes são criados e configurados como parte da implementação solução. 

### <a name="real-time-analysis"></a>Análise em tempo real
Os eventos gerados pela Simulator telemática veículo são publicados no centro do evento utilizando o SDK do concentrador de evento. A tarefa de análise da cadeia ingests estes eventos a partir do Hub evento e processa os dados em tempo real para analisar o estado de funcionamento de veículo. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig5-vehicle-telematics-event-hub-dashboard.png) 

*Figura 5 - dashboard concentrador de evento*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig6-vehicle-telematics-stream-analytics-job-processing-data.png) 

*Figura 6 - tarefa de análise da cadeia processamento de dados*

Tarefa de análise da cadeia;

- ingests dados a partir do centro do evento 
- executa uma associação com os dados de referência para mapear veículo VIN ao modelo de correspondente 
- persiste-los para o armazenamento de Blobs do Azure para análise lote avançada. 

A seguinte consulta de análise da cadeia é utilizada para manter os dados para o armazenamento de Blobs do Azure. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig7-vehicle-telematics-stream-analytics-job-query-for-data-ingestion.png) 

*Figura 7 - query de tarefa de análise da cadeia para ingestão de dados*

### <a name="batch-analysis"></a>Análise batch
Recomendamos também estão a gerar um volume adicional de sinais de veículo simulada e diagnóstico conjunto de dados para análise de lote mais rica. Isto é necessário para se certificar de um volume de boa dados representativas para processamento batch. Para o efeito, estamos a utilizar uma tubagem com o nome "PrepareSampleDataPipeline" no fluxo de trabalho Azure dados fábrica para gerar património de um ano de sinais de veículo simulada e diagnóstico conjunto de dados. Clique em [dados fábrica de atividade personalizada](http://go.microsoft.com/fwlink/?LinkId=717077) para transferir a atividade de DotNet personalizada dados fábrica solução do Visual Studio para personalizações com base nos seus requisitos. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig8-vehicle-telematics-prepare-sample-data-for-batch-processing.png) 

*Figura 8 - preparar dados de exemplo para o fluxo de trabalho do lote processamento*

Pipeline de for constituída por um personalizado .net ADF atividade, mostrar aqui:

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig9-vehicle-telematics-prepare-sample-data-pipeline.png) 

*Figura 9 - PrepareSampleDataPipeline*

Assim que a tubagem executa com êxito e "RawCarEventsTable" conjunto de dados está marcado como "Pronto", um ano vale de sinais de veículo simulada e diagnóstico são produzidos dados. Ver a pasta e ficheiro criado na sua conta de armazenamento no contentor "connectedcar" seguintes:

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig10-vehicle-telematics-prepare-sample-data-pipeline-output.png) 

*Figura 10 - PrepareSampleDataPipeline saída*

### <a name="references"></a>Referências

[Azure SDK do concentrador de evento para ingestão da cadeia](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

[Capacidades de movimento de dados do Azure dados fábrica](../data-factory/data-factory-data-movement-activities.md)
[Azure dados fábrica DotNet atividade](../data-factory/data-factory-use-custom-activities.md)

[Azure dados fábrica DotNet atividade solução do visual studio para preparar dados de exemplo](http://go.microsoft.com/fwlink/?LinkId=717077) 


## <a name="partition-the-dataset"></a>Partição o conjunto de dados

A sinais de veículo semiestruturados não processados e diagnóstico conjunto de dados são divididos no passo de preparação de dados para um formato de mês/ano. Esta partições promove consultar mais eficientes e armazenamento a longo prazo dimensionável ao ativar de falhas com sobre a partir da conta de um blob para o próximo à medida que a primeira conta preenchida. 

>[AZURE.NOTE] Este passo da solução só é aplicável às processamento batch.

Entrada e saída gestão de dados de dados:

- Os **dados de saída** (assinalada *PartitionedCarEventsTable*) é ser mantido durante um longo período de tempo, como o formulário base destinado / "rawest" de dados do cliente "Lake dados". 
- Os **dados de entrada** para este pipeline faria normalmente eliminadas como os dados de saída tem fidelidade completa para a entrada - apenas está armazenado (divididos) melhor para utilização posterior.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig11-vehicle-telematics-partition-car-events-workflow.png)

*Figura 11 – fluxo de trabalho de eventos de carro partição*

Os dados não processados são divididos utilizando uma atividade de Hive HDInsight no "PartitionCarEventsPipeline". Os dados de exemplo gerados no passo 1 para um ano são divididos por mês/ano. As partições são utilizadas para gerar sinais de veículo e dados de diagnóstico para cada mês (total 12 partições) de um ano. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig12-vehicle-telematics-partition-car-events-pipeline.png)

*Figura 12 - PartitionCarEventsPipeline*

O script ramo seguinte, com o nome "partitioncarevents.hql", é utilizado para criar partições e está localizado na pasta "\demo\src\connectedcar\scripts" da zip transferido. 


    SET hive.exec.dynamic.partition=true;
    SET hive.exec.dynamic.partition.mode = nonstrict;
    set hive.cli.print.header=true;

    DROP TABLE IF EXISTS RawCarEvents; 
    CREATE EXTERNAL TABLE RawCarEvents 
    (
                vin                             string,
                model                           string,
                timestamp                       string,
                outsidetemperature              string,
                enginetemperature               string,
                speed                           string,
                fuel                            string,
                engineoil                       string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position      string,
                parking_brake_status            string,
                headlamp_status                 string,
                brake_pedal_status              string,
                transmission_gear_position      string,
                ignition_status                 string,
                windshield_wiper_status         string,
                abs                             string,
                gendate                         string
                
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RAWINPUT}'; 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents 
    (
                vin                             string,
                model                           string,
                timestamp                       string,
                outsidetemperature              string,
                enginetemperature               string,
                speed                           string,
                fuel                            string,
                engineoil                       string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position      string,
                parking_brake_status            string,
                headlamp_status                 string,
                brake_pedal_status              string,
                transmission_gear_position      string,
                ignition_status                 string,
                windshield_wiper_status         string,
                abs                             string,
                gendate                         string
    ) partitioned by (YearNo int, MonthNo int) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDOUTPUT}';

    DROP TABLE IF EXISTS Stage_RawCarEvents; 
    CREATE TABLE IF NOT EXISTS Stage_RawCarEvents 
    (
                vin                             string,
                model                           string,
                timestamp                       string,
                outsidetemperature              string,
                enginetemperature               string,
                speed                           string,
                fuel                            string,
                engineoil                       string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position      string,
                parking_brake_status            string,
                headlamp_status                 string,
                brake_pedal_status              string,
                transmission_gear_position      string,
                ignition_status                 string,
                windshield_wiper_status         string,
                abs                             string,
                gendate                         string,
                YearNo                          int,
                MonthNo                         int) 
    ROW FORMAT delimited fields terminated by ',' LINES TERMINATED BY '10';

    INSERT OVERWRITE TABLE Stage_RawCarEvents
    SELECT
        vin,            
        model,
        timestamp,
        outsidetemperature,
        enginetemperature,
        speed,
        fuel,
        engineoil,
        tirepressure,
        odometer,
        city,
        accelerator_pedal_position,
        parking_brake_status,
        headlamp_status,
        brake_pedal_status,
        transmission_gear_position,
        ignition_status,
        windshield_wiper_status,
        abs,
        gendate,
        Year(gendate),
        Month(gendate)

    FROM RawCarEvents WHERE Year(gendate) = ${hiveconf:Year} AND Month(gendate) = ${hiveconf:Month}; 

    INSERT OVERWRITE TABLE PartitionedCarEvents PARTITION(YearNo, MonthNo) 
    SELECT
        vin,            
        model,
        timestamp,
        outsidetemperature,
        enginetemperature,
        speed,
        fuel,
        engineoil,
        tirepressure,
        odometer,
        city,
        accelerator_pedal_position,
        parking_brake_status,
        headlamp_status,
        brake_pedal_status,
        transmission_gear_position,
        ignition_status,
        windshield_wiper_status,
        abs,
        gendate,
        YearNo,
        MonthNo
    FROM Stage_RawCarEvents WHERE YearNo = ${hiveconf:Year} AND MonthNo = ${hiveconf:Month};

*Figura 13 - PartitionConnectedCarEvents ramo Script*

Assim que a tubagem é executada com êxito, verá as partições seguintes geradas na sua conta de armazenamento no contentor "connectedcar".

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig14-vehicle-telematics-partitioned-output.png)

*Figura 14 - com partições saída*

Os dados agora está optimizada, é mais fácil e pronto para processamento suplementar para obter informações de lote avançada. 

## <a name="data-analysis"></a>Análise de dados

Nesta secção, consulte como combinar Azure da cadeia Analytics, formação de máquina Azure, fábrica de dados do Azure e Azure HDInsight para RTF avançado analytics no estado de funcionamento do veículo e condução hábitos. Existem três subsecções aqui:

1.  **Formação de máquina**: presente subsecção contém informações sobre a experiência de deteção de anomalia utilizámos esta solução para prever veículos que exige o manutenção de manutenção e que exige o recupera devido a problemas de segurança.
2.  **Análise em tempo real**: presente subsecção contém informações relativas a análise em tempo real com a linguagem de consulta de análise da cadeia e operationalizing a experiência de aprendizagem máquina em tempo real utilizando uma aplicação personalizada.
3.  **Análise batch**: presente subsecção contém informações relativas a transformará e processamento dos dados de lote utilizando a aprendizagem de máquina de Azure operacionalizada pela fábrica de dados do Azure e Azure HDInsight.

### <a name="machine-learning"></a>Formação do computador

Aqui nosso objetivo é prever veículos que requerem manutenção ou resgate com base em determinadas estatísticas de estado de funcionamento. Oferecemos os pressupostos seguintes

- Se um dos três situações seguintes forem VERDADEIRO, veículos exigem **Manutenção de manutenção**:
    - Pneus pressão está baixo
    - Motor Adamastor nível é baixo
    - Temperatura motor é elevada

- Se um dos seguintes condições é verdadeiro, veículos poderão ter um **problema de segurança** e exigir **resgate**:
    - Temperatura motor é elevada mas temperatura exterior está baixa
    - Temperatura motor está baixa mas temperatura exterior é elevada

Basear os requisitos anterior, criámos dois modelos em separado para detetar anomalias, uma para deteção de manutenção de veículo e outra para deteção de resgate veículo. Em ambos os estes modelos, é utilizado o algoritmo de análise de componente Principal (PCA) incorporados para deteção anomalia. 

**Modelo de deteção de manutenção**

Se um dos três indicadores - pneus pressão Adamastor motor de temperatura motor - satisfizer a respetivas condição, o modelo de deteção de manutenção relatórios uma anomalia. Como resultado, precisamos apenas a ter em consideração estes três variáveis na criação do modelo. No nossa experiência no Azure máquina formação, utilizamos primeiro um módulo de **Selecionar colunas no conjunto de dados** para extrair estes três variáveis. Seguinte utilizamos o módulo de detecção com base em PCA anomalia para criar o modelo de deteção de anomalia. 

Análise de componente de capital (PCA) é uma técnica de estabelecimento em aprender máquina que pode ser aplicada a seleção de funcionalidades, classificação e deteção de anomalia. PCA converte um conjunto de maiúsculas/minúsculas que contém possivelmente correlacionadas variáveis, para um conjunto de valores denominados componentes principais. A chave ideia de modelação de PCA é dados do project para um espaço dimensionais para o canto inferior para que as funcionalidades e anomalias podem ser mais facilmente identificadas.
 
Para cada nova introdução ao modelo de deteção, detector anomalia pela primeira vez fórmula calcula a projecções na eigenvectors e, em seguida, calcula o erro reconstrução normalizado. Este erro normalizado é pontuação anomalia. Maior que o erro, mais discordantes a instância está. 

No problema de deteção de manutenção, cada registo pode ser considerado como um ponto num espaço 3 dimensionais definido pela pneus pressão, Adamastor motor e temperatura motor coordenadas. Para capturar estes anomalias, podemos pode projeto os dados originais no espaço 3 dimensionais para um espaço 2 dimensionais utilizando PCA. Assim sendo, vamos defina o parâmetro de número de componentes para utilizar em PCA para ser 2. Este parâmetro é reproduzido um papel importante na aplicação de deteção de anomalia com base em PCA. Depois de dados proeminente utilizando PCA, pode identificamos estes anomalias mais facilmente.

**Recuperar o modelo de deteção de anomalia** No modelo de deteção de resgate anomalia, utilizamos selecionar colunas no conjunto de dados e com base em PCA anomalia módulos de deteção de forma semelhante. Especificamente, podemos extrair primeiro três variáveis - temperatura motor, temperatura exterior e velocidade de-utilizar o módulo **Selecionar colunas no conjunto de dados** . Recomendamos também incluir a variável de velocidade desde a temperatura motor é normalmente associada à velocidade. Seguinte utilizamos módulo de detecção com base em PCA anomalia para os dados a partir do espaço 3 dimensionais do project para um espaço 2 dimensionais. Os critérios de resgate são correspondidos e veículo exija resgate quando a temperatura motor e a temperatura exterior altamente negativa estão associados. Utilizar o algoritmo de deteção de anomalia com base em PCA, podemos pode capturar as anomalias após executar PCA. 

Quando o modelo de formação, é necessário utilizar os dados normais, que não requer manutenção ou resgate como os dados de entrada para formar o modelo de detecção com base em PCA anomalia. A experiência de pontuação, utilizamos o modelo de deteção de anomalia qualificados para detetar se veículo requer manutenção ou resgate ou não. 


### <a name="real-time-analysis"></a>Análise em tempo real

A seguinte consulta de SQL de análise da cadeia é utilizada para obter a média de todos os parâmetros de veículo importantes como velocidade veículo, nível de combustível, temperatura motor, leitura odométrico, pneus pressão, nível do motor de Adamastor e outras pessoas. As médias são utilizadas para detetar anomalias, emitir alertas e determinar as condições de estado de funcionamento geral dos veículos operados na região específico e, em seguida, se relacionam-lo com demografia. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig15-vehicle-telematics-stream-analytics-query-for-real-time-processing.png)

Figura 15 – query de análise da cadeia para processamento em tempo real

Todas as médias são calculadas ao longo de um TumblingWindow 3 segundos. Estamos a utilizar TubmlingWindow neste caso uma vez que necessitamos intervalos de tempo contíguas e que não sejam sobrepostos. 

Para saber mais sobre todas as capacidades de "Sistema baseado em janelas" Azure da cadeia análise, clique em [sistema baseado em janelas (Azure da cadeia Analytics)](https://msdn.microsoft.com/library/azure/dn835019.aspx).

**Previsão em tempo real**

Uma aplicação é incluída como parte da solução a operationalize o modelo de aprendizagem máquina em tempo real. Esta aplicação denominada "RealTimeDashboardApp" é criada e configurada como parte da implementação solução. A aplicação executa o seguinte procedimento:

1.  Recebe uma instância de evento concentrador onde a análise da cadeia é publicar os eventos num padrão continuamente. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig16-vehicle-telematics-stream-analytics-query-for-publishing.png)*Figura 16 – query de análise da cadeia para publicar os dados para um resultado instância do concentrador de evento* 

2.  Para todos os eventos que recebe esta aplicação: 

    - Processa os dados utilizando o ponto final de máquina aprendizagem pedido resposta pontuação (RRS). O ponto final RRS é publicado automaticamente como parte da implementação.
    - O resultado da RRS é publicado um conjunto de obter dados utilizando o push APIs.

Este padrão também se aplica aos cenários na qual pretende integrar uma aplicação de linha de negócio (LoB) com o fluxo de análise em tempo real, para cenários de alertas, notificações e mensagens.

Clique em [RealtimeDashboardApp transferir](http://go.microsoft.com/fwlink/?LinkId=717078) para transferir a solução RealtimeDashboardApp Visual Studio para as personalizações. 

**Para executar a aplicação de Dashboard em tempo real**

1.  Clique no nó obter na vista de diagrama e clique na ligação "Transferir em tempo real a aplicação de Dashboard" no painel de propriedades. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17-vehicle-telematics-powerbi-dashboard-setup.png)*Figura 17 – obter instruções de configuração de dashboard*
2.  Extrair e guardar localmente ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig18-vehicle-telematics-realtimedashboardapp-folder.png) *figura 18 – RealtimeDashboardApp pasta*
3.  Executar a aplicação RealtimeDashboardApp.exe
4.  Fornecer credenciais válidas do Power BI, início de sessão e clique em aceitar ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19a-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png)![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19b-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 

*Figura 19 – RealtimeDashboardApp: Iniciar sessão para obter*

>[AZURE.NOTE] Se pretender, remova o conjunto de dados de obter, execute o RealtimeDashboardApp com o parâmetro "flushdata": 

    RealtimeDashboardApp.exe -flushdata

### <a name="batch-analysis"></a>Análise batch

O objetivo aqui é mostrar como Contoso motores utiliza as capacidades de cluster Azure para combine dados grandes para obter informações formatadas no condução padrão, comportamento de utilização e estado de funcionamento do veículo. Isto torna possível:

- Melhorar a experiência do cliente e torná-lo menos dispendioso fornecendo informações sobre condução hábitos e comportamentos de condução eficiente de combustível
- Saiba mais importante sobre clientes e os respetivos patters condução governem decisões empresariais e fornecer a melhor no classe produtos e serviços

Nesta solução, podemos está a filtrar as métricas seguintes:

1.  **Comportamento de condução agressivo**: identifica a tendência dos modelos, localizações, condução condições e hora do ano para obter informações sobre os padrões de condução agressivas. Motores de contoso pode utilizar estas informações para campanhas de marketing, condução novas funcionalidades personalizadas e seguro com base em utilização.
2.  **Comportamento de condução eficiente de combustível**: identifica a tendência dos modelos, localizações, condução condições e hora do ano para obter informações sobre os padrões de condução eficiente de combustível. Motores de contoso podem utilizar estas informações para campanhas de marketing condução novas funcionalidades e relatórios pro-activos para os controladores para o custo hábitos eficazes e ambiente de condução amigáveis. 
3.  **Recuperar modelos**: identifica modelos que exijam recupera por operationalizing a máquina de deteção de anomalia experiência de formação

Vamos ver os detalhes de cada um dos seguintes métricas,


**Padrão de condução agressiva**

A sinais de veículo com partições e dados de diagnóstico são processados no pipeline de denominada "AggresiveDrivingPatternPipeline" utilizar Hive para determinar os modelos, localização, veículo, das condições de condução e outros parâmetros que exibe agressiva padrão condução.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig20-vehicle-telematics-aggressive-driving-pattern.png) 
*Figura 20 – agressiva faz condução fluxo de trabalho com padrão*

O script ramo denominado "aggresivedriving.hql" utilizados para analisar agressivas padrão de condição condução está localizado na pasta "\demo\src\connectedcar\scripts" do zip transferido. 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents
    (
                vin                             string,
                model                           string,
                timestamp                       string,
                outsidetemperature              string,
                enginetemperature               string,
                speed                           string,
                fuel                            string,
                engineoil                       string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position      string,
                parking_brake_status            string,
                headlamp_status                 string,
                brake_pedal_status              string,
                transmission_gear_position      string,
                ignition_status                 string,
                windshield_wiper_status         string,
                abs                             string,
                gendate                         string
                                
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

    DROP TABLE IF EXISTS CarEventsAggresive; 
    CREATE EXTERNAL TABLE CarEventsAggresive
    (
                vin                         string, 
                model                       string,
                timestamp                   string,
                city                        string,
                speed                       string,
                transmission_gear_position  string,
                brake_pedal_status          string,
                Year                        string,
                Month                       string
                                
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:AGGRESIVEOUTPUT}';



    INSERT OVERWRITE TABLE CarEventsAggresive
    select
    vin,
    model,
    timestamp,
    city,
    speed,
    transmission_gear_position,
    brake_pedal_status,
    "${hiveconf:Year}" as Year,
    "${hiveconf:Month}" as Month
    from PartitionedCarEvents
    where transmission_gear_position IN ('fourth', 'fifth', 'sixth', 'seventh', 'eight') AND brake_pedal_status = '1' AND speed >= '50'

*Figura 21 – agressiva faz condução a consulta ramo padrão*

Utiliza a combinação de transmissão engrenagem posição, estado de pedais travão e velocidade do veículo para detetar o comportamento de condução reckless/agressivas com base em travagem padrão de alta velocidade. 

Assim que a tubagem é executada com êxito, verá as partições seguintes geradas na sua conta de armazenamento no contentor "connectedcar".

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig22-vehicle-telematics-aggressive-driving-pattern-output.png) 

*Figura 22 – AggressiveDrivingPatternPipeline saída*


**Padrão de condução eficiente de combustível**

A sinais de veículo com partições e dados de diagnóstico são processados no pipeline de denominada "FuelEfficientDrivingPatternPipeline". Ramo é utilizado para determinar os modelos, localização, veículo, das condições de condução e outras propriedades que apresentam o padrão de condução eficiente de combustível.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig23-vehicle-telematics-fuel-efficient-driving-pattern.png) 

*Figura 23 – combustível eficiente condução padrão fluxo de trabalho*

O script ramo denominado "fuelefficientdriving.hql" utilizados para analisar agressivas padrão de condição condução está localizado na pasta "\demo\src\connectedcar\scripts" do zip transferido. 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents
    (
                vin                             string,
                model                           string,
                timestamp                       string,
                outsidetemperature              string,
                enginetemperature               string,
                speed                           string,
                fuel                            string,
                engineoil                       string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position      string,
                parking_brake_status            string,
                headlamp_status                 string,
                brake_pedal_status              string,
                transmission_gear_position      string,
                ignition_status                 string,
                windshield_wiper_status         string,
                abs                             string,
                gendate                         string
                                
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

    DROP TABLE IF EXISTS FuelEfficientDriving; 
    CREATE EXTERNAL TABLE FuelEfficientDriving
    (
                vin                         string, 
                model                       string,
                city                        string,
                speed                       string,
                transmission_gear_position  string,                
                brake_pedal_status          string,            
                accelerator_pedal_position  string,                             
                Year                        string,
                Month                       string
                                
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:FUELEFFICIENTOUTPUT}';



    INSERT OVERWRITE TABLE FuelEfficientDriving
    select
    vin,
    model,
    city,
    speed,
    transmission_gear_position,
    brake_pedal_status,
    accelerator_pedal_position,
    "${hiveconf:Year}" as Year,
    "${hiveconf:Month}" as Month
    from PartitionedCarEvents
    where transmission_gear_position IN ('fourth', 'fifth', 'sixth', 'seventh', 'eight') AND parking_brake_status = '0' AND brake_pedal_status = '0' AND speed <= '60' AND accelerator_pedal_position >= '50'


*Figura 24 – combustível eficiente condução padrão ramo de consulta*

Utiliza a combinação de posição de engrenagem de transmissão de veículo, travão pedais Estado, a velocidade e accelerator pedais posição para detectar combustível comportamento eficiente condução com base em aceleração, travagem e padrões de velocidade. 

Assim que a tubagem é executada com êxito, verá as partições seguintes geradas na sua conta de armazenamento no contentor "connectedcar".

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig25-vehicle-telematics-fuel-efficient-driving-pattern-output.png) 

*Figura 25 – FuelEfficientDrivingPatternPipeline saída*


**Recuperar previsões**

A experiência de formação de máquina está aprovisionada e publicada como um serviço web como parte da implementação solução. O lote de ponto final de pontuação é utilizado neste fluxo de trabalho, registado como um serviço de fábrica do mesmo ligada dados e operacionalizada utilizando dados fábrica lote pontuação atividade.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig26-vehicle-telematics-machine-learning-endpoint.png) 

*Figura 26 – ponto final de aprendizagem automática registado como um serviço ligado numa fábrica de dados*

O serviço ligado registado é utilizado na DetectAnomalyPipeline para pontuação os dados utilizando o modelo de deteção de anomalia. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig27-vehicle-telematics-aml-batch-scoring.png) 

*Figura 27 – Azure máquina aprendizagem lote de pontuação atividade na fábrica de dados* 

Existem alguns passos executados neste curso para preparação de dados para que pode ser operacionalizada com o lote de pontuação serviço web. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig28-vehicle-telematics-pipeline-predicting-recalls.png) 

*Figura 28 – DetectAnomalyPipeline para as previsões veículos que exige o recupera* 

Assim que a pontuação estiver concluída, uma actividade de HDInsight é utilizada para processar e agregar os dados que são categorizados como anomalias pelo modelo com uma classificação de probabilidade de 0,60 ou superior.

    DROP TABLE IF EXISTS CarEventsAnomaly; 
    CREATE EXTERNAL TABLE CarEventsAnomaly 
    (
                vin                         string,
                model                       string,
                gendate                     string,
                outsidetemperature          string,
                enginetemperature           string,
                speed                       string,
                fuel                        string,
                engineoil                   string,
                tirepressure                string,
                odometer                    string,
                city                        string,
                accelerator_pedal_position  string,
                parking_brake_status        string,
                headlamp_status             string,
                brake_pedal_status          string,
                transmission_gear_position  string,
                ignition_status             string,
                windshield_wiper_status     string,
                abs                         string,
                maintenanceLabel            string,
                maintenanceProbability      string,
                RecallLabel                 string,
                RecallProbability           string
                                
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:ANOMALYOUTPUT}';

    DROP TABLE IF EXISTS RecallModel; 
    CREATE EXTERNAL TABLE RecallModel 
    (

                vin                         string,
                model                       string,
                city                        string,
                outsidetemperature          string,
                enginetemperature           string,
                speed                       string,
                Year                        string,
                Month                       string              
                                
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RECALLMODELOUTPUT}';

    INSERT OVERWRITE TABLE RecallModel
    select
    vin,
    model,
    city,
    outsidetemperature,
    enginetemperature,
    speed,
    "${hiveconf:Year}" as Year,
    "${hiveconf:Month}" as Month
    from CarEventsAnomaly
    where RecallLabel = '1' AND RecallProbability >= '0.60'


Assim que a tubagem é executada com êxito, verá as partições seguintes geradas na sua conta de armazenamento no contentor "connectedcar".

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig30-vehicle-telematics-detect-anamoly-pipeline-output.png) 

*Figura 30 – figura 30 – DetectAnomalyPipeline saída*


## <a name="publish"></a>Publicar

### <a name="real-time-analysis"></a>Análise em tempo real

Uma das consultas na tarefa de análise da cadeia publica os eventos para um resultado instância do concentrador de evento. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig31-vehicle-telematics-stream-analytics-job-publishes-output-event-hub.png)

*Figura 31 – fluxo de trabalho analytics publica uma saída instância do concentrador de evento*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig32-vehicle-telematics-stream-analytics-query-publish-output-event-hub.png)

*Figura 32 – sequência de análise de consulta para publicar o resultado da instância do concentrador de evento*

Este fluxo de eventos é a média consumido por RealTimeDashboardApp incluído na solução. Esta aplicação tira partido de formação de máquina pedido-resposta serviço web da pontuação em tempo real e publica os dados resultante um conjunto de dados de obter para consumo. 

### <a name="batch-analysis"></a>Análise batch

As tabelas de base de dados do Azure SQL para consumo são publicados os resultados do lote e processamento em tempo real. O SQL Server Azure, base de dados e as tabelas são criadas automaticamente como parte do script de configuração. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig33-vehicle-telematics-batch-processing-results-copy-to-data-mart.png)

*Figura 33 – processamento copiar de resultados para o fluxo de trabalho do dados ser em lotes*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig34-vehicle-telematics-stream-analytics-job-publishes-to-data-mart.png)

*Figura 34 – fluxo de trabalho analytics publica ser de dados*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig35-vehicle-telematics-data-mart-setting-in-stream-analytics-job.png)

*Figura 35 – ser dados definir na tarefa de análise da cadeia*


## <a name="consume"></a>Consumir

Power BI dá-esta solução um dashboard formatado para dados em tempo real e visualizações de análise de aspeto do Office. 

Clique aqui para obter instruções detalhadas sobre como configurar os relatórios de obter e o dashboard. O dashboard final tem o seguinte aspeto:

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig36-vehicle-telematics-powerbi-dashboard.png)

*Figura 36 - obter Dashboard*

## <a name="summary"></a>Resumo

Este documento contém uma desagregação detalhada da solução de análise de telemetria de veículo. Isto demonstra um padrão de arquitectura lambda para em tempo real e batch analytics com previsões e ações. Este padrão aplica-se a uma vasta gama de casos de utilização que requerem caminho quente (em tempo real) e a análise de caminho fria (lote). 
