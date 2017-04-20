<properties
    pageTitle="O processo de ciências de dados de equipa em acção: utilizar o SQL Server | Microsoft Azure"
    description="Tecnologia em acção e o processo de Analytics Avançado"  
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
    ms.date="09/19/2016"
    ms.author="fashah;bradsev"/>


# <a name="the-team-data-science-process-in-action-using-sql-server"></a>O processo de ciências de dados de equipa em acção: utilizar o SQL Server

Neste tutorial, é tutorial criar e implementar um modelo de aprendizagem de máquinas utilizando o SQL Server e um conjunto de dados à disposição do público - dataset [NYC táxis Trips](http://www.andresmh.com/nyctaxitrips/) . O procedimento a seguir um fluxo de trabalho de ciências de dados padrão: ingerir esta última e explorar os dados, funcionalidades de engenharia para facilitar a aprendizagem, em seguida, criar e implementar um modelo.


## <a name="dataset"></a>Descrição do conjunto de dados para turismo NYC táxis

Os dados de viagem de táxis NYC são cerca de 20GB de ficheiros CSV comprimidos (~ 48GB descomprimido), que inclua mais de 173 milhões individuais trips e as tarifas pagas por cada viagem. Cada registo de viagem inclui a localização de recolha e remessa e tempo, hack anónimos (controlador) o número da licença e número de medallion (id exclusivo do táxi). Os dados abrange todas as viagens no ano de 2013 e são fornecidos nos seguintes dois conjuntos de dados para cada mês:

1. 'trip_data' CSV contém detalhes de viagem, tais como número de passageiros, recolha e pontos de dropoff, duração de viagem e a duração de viagem. Eis alguns registos como exemplo:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. 'trip_fare' CSV contém detalhes de tarifa paga para cada viagem, tais como o tipo de pagamento, montante do preço da corrida, sobretaxa e impostos, sugestões e portagens e o montante total pago. Eis alguns registos como exemplo:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

A chave exclusiva para aderir a viagem de\_dados e de viagem\_tarifa é composta por campos: medallion, hack\_licença e de recolha\_data/hora.

## <a name="mltasks"></a>Exemplos de tarefas de previsão

Vamos formulará três problemas de previsão baseados a *Sugestão\_valor*, nomeadamente:

1. Classificação binária: prever ou não uma sugestão tiver sido paga para uma viagem, ou seja, um *Sugestão\_valor* que é maior do que $0 é um exemplo positivo, enquanto um *Sugestão\_valor* de $0 é um exemplo negativo.

2. Classificação de multiclass: para prever o intervalo de sugestão de pagamento para a viagem. Vamos dividir o *Sugestão\_valor* em cinco posições ou classes:

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20

3. Tarefa de regressão: para prever o montante da sugestão de pagamento para uma viagem.  


## <a name="setup"></a>Ambiente de ciência de dados de definição para cima o Azure para analytics avançado

Como pode ver o guia de [Plano ao ambiente](machine-learning-data-science-plan-your-environment.md) , existem várias opções para trabalhar com o dataset NYC táxis Trips na Azure:

- Trabalhar com os dados no blob Azure, em seguida, o modelo na aprendizagem de máquinas Azure
- Carregar os dados para uma base de dados do SQL Server, em seguida, o modelo na aprendizagem de máquinas Azure

Neste tutorial vamos vai demonstrar importação em massa paralela de dados para um servidor de SQL, a exploração de dados, a funcionalidade de engenharia e para baixo de amostragem a utilizar o SQL Server Management Studio, bem como utilizar o bloco de notas de IPython. [Scripts de exemplo](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) e [blocos de notas de IPython](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) são partilhados em GitHub. Um bloco de notas de IPython de amostra para trabalhar com os dados no blob Azure também está disponível na mesma localização.

Para configurar o ambiente Azure ciência de dados:

1. [Criar uma conta de armazenamento](../storage/storage-create-storage-account.md)

2. [Criar uma área de trabalho de aprendizagem de máquinas Azure](machine-learning-create-workspace.md)

3. [Prestação de uma máquina de Virtual da ciência de dados](machine-learning-data-science-setup-sql-server-virtual-machine.md), que irá servir como um servidor de SQL, bem como um servidor de bloco de notas de IPython.

    > [AZURE.NOTE] Os scripts de exemplo e blocos de notas de IPython serão transferidos para a máquina virtual de ciências de dados durante o processo de configuração. Quando o script de pós-instalação VM estiver concluída, as amostras serão na biblioteca de documentos a VM:  
    > - Scripts de exemplo:`C:\Users\<user_name>\Documents\Data Science Scripts`  
    > - Blocos de notas de IPython exemplo:`C:\Users\<user_name>\Documents\IPython Notebooks\DataScienceSamples`  
    > em que `<user_name>` é o nome de início de sessão do Windows a VM. A Microsoft irá remeter para as pastas de exemplo como **Scripts de exemplo** e **Blocos de notas de IPython de amostra**.


Baseia o tamanho de conjunto de dados, localização de origem de dados e o ambiente de destino Azure seleccionado, este cenário é semelhante a [cenário \#5: grande conjunto de dados em ficheiros de locais de destino do SQL Server na Azure VM](../machine-learning-data-science-plan-sample-scenarios.md#largelocaltodb).

## <a name="getdata"></a>Obter os dados da origem pública

Para obter o conjunto de dados de [Viagens de táxis NYC](http://www.andresmh.com/nyctaxitrips/) da respectiva localização pública, pode utilizar qualquer um dos métodos descritos no [Mover dados para e do armazenamento de Blob Azure](machine-learning-data-science-move-azure-blob.md) para copiar os dados para a nova máquina virtual.

Para copiar os dados utilizando AzCopy:

1. Iniciar sessão na máquina virtual (VM)

2. Criar um novo directório no disco de dados a VM (Nota: não utilize a disquete temporária que é fornecido com a VM como um disco de dados).

3. Na janela de linha de comandos, execute a seguinte linha de comandos do Azcopy substituir < path_to_data_folder > com a pasta de dados criada em (2):

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

    Quando conclui a AzCopy, um total de 24 em formato zip ficheiros CSV (12 para a viagem de\_dados e 12 para a viagem de\_tarifa) deve estar na pasta dados.

4. Deszipar ficheiros transferidos. Anote a pasta onde residem os ficheiros não comprimidos. Esta pasta será referida como a < caminho\_para\_dados\_ficheiros\>.

## <a name="dbload"></a>Dados de importação em massa na base de dados do SQL Server

O desempenho de carga/transferir grandes quantidades de dados para uma base de dados SQL e consultas subsequentes pode ser melhorado através da utilização de _tabelas de partições e vistas_. Nesta secção, Seguimos as instruções descritas em [Paralelo em massa importação utilizando SQL partição tabelas de dados](machine-learning-data-science-parallel-load-sql-partitioned-tables.md) para criar uma nova base de dados e carregar os dados em tabelas com partições em paralelo.

1. Enquanto tiver sessão iniciada seu VM, inicie o **SQL Server Management Studio**.

2. Ligar utilizando a autenticação do Windows.

    ![Ligar SSMS][12]

3. Se ainda não tiver alterado o modo de autenticação do SQL Server e criou um novo utilizador de início de sessão SQL, abra o ficheiro de script com o nome **alterar\_auth.sql** na pasta **Exemplos de Scripts** . Altere o nome de utilizador predefinido e a palavra-passe. Clique em **! Executar** na barra de ferramentas para executar o script.

    ![Executar o Script][13]

4. Verificar e/ou alterar o SQL Server da base de dados e registo pastas predefinidas para garantir que serão armazenadas num disco de dados recentemente criadas bases de dados. A imagem de SQL Server VM que está optimizada para cargas de datawarehousing está pré-configurado com discos de dados e de registo. Se a VM não incluiu um disco de dados e adicionar novos discos rígidos virtuais durante o processo de configuração VM, altere as pastas predefinidas do seguinte modo:

    - Botão direito do rato no nome do servidor de SQL no painel da esquerda e clique em **Propriedades**.

        ![Propriedades do SQL Server][14]

    - Seleccione **Definições de base de dados** na lista **Seleccione uma página** para a esquerda.

    - Verificar e/ou alterar as **localizações predefinidas de base de dados** para as localizações de **Disco de dados** da sua escolha. Este é onde novas bases de dados residem se criado com as predefinições de localização.

        ![Predefinições de base de dados SQL][15]  

5. Para criar uma nova base de dados e um conjunto dos grupos de ficheiros para manter as tabelas de partição, abra o script de exemplo **criar\_db\_default.sql**. O script irá criar uma nova base de dados denominada **TaxiNYC** e os grupos de 12 ficheiros na localização de dados predefinida. Cada grupo de ficheiros irá conter um mês, da viagem\_dados e de viagem\_dados a importância a pagar. Modificar o nome de base de dados, se assim o pretender. Clique em **! Execute** para executar o script.

6. Em seguida, crie duas tabelas de partição, uma para a viagem\_dados e outra para a viagem\_tarifa. Abra o script de exemplo **criar\_com partições\_table.sql**, que será:

    - Crie uma função de partição para dividir os dados por mês.
    - Crie um esquema de partição para mapear dados de cada mês para um grupo de ficheiros diferente.
    - Crie duas tabelas com partições mapeadas para o esquema de partição: **nyctaxi\_viagem** vai conter a viagem\_dados e **nyctaxi\_tarifa** vai conter a viagem\_dados a importância a pagar.

    Clique em **! Execute** para executar o script e criar as tabelas de partição.

7. Na pasta **Scripts de exemplo** , existem dois scripts de PowerShell de exemplo fornecidos para demonstrar a granel paralelo a importação de dados para tabelas do SQL Server.

    - **bcp\_parallel\_generic.ps1** é um script genérico para dados de importação em massa paralela para uma tabela. Modifique este script para definir as variáveis de entrada e de destino, tal como indicado nas linhas de comentário no script.
    - **bcp\_parallel\_nyctaxi.ps1** é uma versão previamente configurada do script genérico e pode ser utilizado para carregar ambas as tabelas para os dados de viagens de táxis NYC.  

8. Com o botão direito do **bcp\_parallel\_nyctaxi.ps1** nome de script e clique em **Editar** para o abrir no PowerShell. Rever as variáveis predefinidas e modificar em conformidade com o nome de base de dados seleccionada, a pasta de dados de entrada, pasta de registo de destino e caminhos para a amostra formato ficheiros **nyctaxi_trip.xml** e **nyctaxi\_fare.xml** (fornecido na pasta **Scripts de exemplo** ).

    ![Dados de importação em massa][16]

    Também pode seleccionar o modo de autenticação, a predefinição é a autenticação do Windows. Clique na seta verde na barra de ferramentas para executar. O script iniciará 24 operações de importação de em massa em paralelo, 12 para cada tabela de partição. Pode monitorizar o progresso de importação de dados, abrindo a pasta de dados do SQL Server predefinida conforme definido acima.

9. O script PowerShell relatórios inicial e final vezes. Quando todas as importações completa a granel, a hora final é comunicada. Verifique a pasta de registo de destino para verificar que as importações em massa foram bem sucedidas, ou seja, não foram comunicados erros na pasta de registo de destino.

10. A base de dados está agora pronto para a exploração, a engenharia de funcionalidade e a outras operações conforme pretendido. Uma vez que as tabelas são divididos em partições acordo com o **recolha\_data/hora** campo consultas que incluem **recolha\_data/hora** condições na cláusula **WHERE** vão beneficiar do regime de partição.

11. No **SQL Server Management Studio**, explore o script de exemplo fornecido **amostra\_queries.sql**. Para executar um dos exemplos de consulta, realce as linhas de consulta, em seguida, clique em **! Executar** na barra de ferramentas.

12. Os dados de viagens de táxis NYC são carregados em duas tabelas separadas. Para melhorar as operações de associação, recomenda-se vivamente a indexar as tabelas. O script de exemplo **criar\_com partições\_index.sql** cria índices com partições na chave de associação composto **medallion, hack\_licença e recolha\_data/hora**.

## <a name="dbexplore"></a>Exploração de dados e de engenharia de funcionalidade do SQL Server

Nesta secção, irá executamos prospecção de dados e geração de funcionalidade executando consultas SQL directamente no **SQL Server Management Studio** utilizando a base de dados do SQL Server criada anteriormente. Um script de exemplo com o nome **amostra\_queries.sql** é fornecido na pasta **Exemplos de Scripts** . Modificar o script para alterar o nome de base de dados, se for diferente da predefinição: **TaxiNYC**.

Neste exercício, iremos:

- Ligar ao **SQL Server Management Studio** utilizando qualquer autenticação do Windows ou utilizando a autenticação de SQL e o nome de início de sessão SQL e a palavra-passe.
- Explore as distribuições de dados de alguns campos em várias janelas de tempo.
- Investigue a qualidade dos dados dos campos de longitude e latitude norte.
- Gerar etiquetas de classificação binário e multiclass baseadas a **Sugestão\_valor**.
- Gerar funcionalidades e as distâncias de viagem do computador/comparar.
- Associar duas tabelas e extrair uma amostra aleatória, que será utilizada para construir modelos.

Quando estiver pronto para avançar para a aprendizagem de máquinas Azure, poderá:  

1. Guardar a consulta SQL final para extrair os dados de exemplo e copiar colar a consulta directamente para [Importar dados] [ import-data] módulo no Azure aprendizagem de máquinas, ou
2. Manter os dados incluídos na amostra e de engenharia que pretende utilizar para criar uma nova tabela de base de dados de modelo e utilizar a nova tabela nos [Dados de importação] [ import-data] módulo na aprendizagem de máquinas Azure.

Nesta secção vamos irá guardar a consulta final para extrair e os dados de exemplo. O segundo método é demonstrado na secção de [exploração de dados e de funcionalidade de engenharia IPython bloco de notas](#ipnb) .

Para uma verificação rápida do número de linhas e colunas nas tabelas povoadas anterior utilizando a importação de paralelo em massa,

    -- Report number of rows in table nyctaxi_trip without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('nyctaxi_trip')

    -- Report number of columns in table nyctaxi_trip
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'nyctaxi_trip'

#### <a name="exploration-trip-distribution-by-medallion"></a>Exploração: Distribuição de viagem pelo medallion

Neste exemplo identifica o medallion (táxis números) com mais de 100 trips dentro de um determinado período de tempo. A consulta irá beneficiar do acesso de tabela com partições, uma vez que deve ser condicionado pelo esquema de partição de **recolha\_data/hora**. Consultar o conjunto de dados completo também fará com que utiliza a tabela de partição e/ou índice de pesquisa.

    SELECT medallion, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

#### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>Exploração: Distribuição de viagem pelo medallion e hack_license

    SELECT medallion, hack_license, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

#### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Avaliação da qualidade de dados: Verificar os registos com longitude incorrecto e/ou de latitude norte

Neste exemplo investiga se qualquer um dos campos ' de longitude e/ou de latitude norte ou conter um valor inválido (radiano graus devem estar entre -90 e 90), ou ter (0, 0) coordenadas.

    SELECT COUNT(*) FROM nyctaxi_trip
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

#### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Exploração: Distribuição de Tipped vs. não-inclinado viagens

Neste exemplo localiza o número de viagens que foram inclinado vs. não-inclinado num dado tempo período (ou no conjunto de dados completo se abrangendo todo o ano). Esta distribuição reflecte a distribuição de etiqueta binário posteriormente ser utilizadas para modelação de classificação binário.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM nyctaxi_fare
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

#### <a name="exploration-tip-classrange-distribution"></a>Exploração: Sugestão distribuição de classe/intervalo

Este exemplo calcula a distribuição dos intervalos de sugestão de um determinado período de tempo (ou no conjunto de dados completo se abrangendo todo o ano). Esta é a distribuição das classes de etiqueta que será utilizada posteriormente para modelação de classificação de multiclass.

    SELECT tip_class, COUNT(*) AS tip_freq FROM (
        SELECT CASE
            WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tip_class

#### <a name="exploration-compute-and-compare-trip-distance"></a>Exploração: Calcular e comparar a distância de viagem

Este exemplo converte a recolha e de remessa de longitude e latitude geografia SQL pontos, calcula a distância de viagem utilizando SQL geografia pontos diferença e devolve uma amostra aleatória dos resultados para comparação. O exemplo que se limita os resultados coordenadas válido utilizando apenas a consulta de avaliação de qualidade do dados abrangida anteriormente.

    SELECT
    pickup_location=geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326)
    ,dropoff_location=geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326)
    ,trip_distance
    ,computedist=round(geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326).STDistance(geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326))/1000, 2)
    FROM nyctaxi_trip
    tablesample(0.01 percent)
    WHERE CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND   CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'

#### <a name="feature-engineering-in-sql-queries"></a>Funcionalidade de engenharia em consultas de SQL

Os rótulo geração e geografia conversão exploração consultas podem também ser utilizadas para gerar etiquetas/funcionalidades removendo a parte de contagem. Exemplos de SQL de engenharia funcionalidades adicionais são fornecidos na secção de [exploração de dados e de funcionalidade de engenharia IPython bloco de notas](#ipnb) . É mais eficiente para executar a funcionalidade de consultas de geração do conjunto de dados completo ou um extenso subconjunto dos utilizando consultas SQL que são executadas directamente na instância de base de dados do SQL Server. As consultas podem ser executadas no **SQL Server Management Studio**, bloco de notas de IPython ou qualquer ferramenta/ambiente de desenvolvimento que pode aceder à base de dados local ou remotamente.

#### <a name="preparing-data-for-model-building"></a>A preparar dados para criar um modelo

A consulta seguinte associações a **nyctaxi\_viagem** e **nyctaxi\_tarifa** tabelas, gera uma classificação binário etiqueta **-inclinado**, uma etiqueta de classificação de classe com várias **Sugestão\_classe**e extrai de uma amostra aleatória de 1% do dataset associado completo. Esta consulta pode ser copiada, em seguida, colou directamente nos [Azure máquina aprendizagem Studio](https://studio.azureml.net) [Importar dados] [ import-data] módulo para a ingestão directa de dados da instância de base de dados do SQL Server no Azure. A consulta exclui registos com incorrecto (0, 0) coordenadas.

    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,  f.total_amount, f.tip_amount,
        CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
        CASE WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM nyctaxi_trip t, nyctaxi_fare f
    TABLESAMPLE (1 percent)
    WHERE t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'


## <a name="ipnb"></a>Exploração de dados e a funcionalidade de engenharia IPython bloco de notas

Nesta secção, irá executamos exploração de dados e geração de funcionalidade utilizando consultas Python e SQL na base de dados do SQL Server criada anteriormente. É fornecido um bloco de notas de IPython amostra com o nome **machine-Learning-data-science-process-sql-story.ipynb** na pasta **Blocos de notas de IPython de amostra** . Este bloco de notas também está disponível em [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks).

A sequência recomendada quando trabalhar com dados grandes é a seguinte:

- Ler uma pequena amostra dos dados numa moldura de dados em memória.
- Efectue alguns efeitos visuais e explorations utilizando os dados incluídos na amostra.
- Experimente utilizar os dados incluídos na amostra de engenharia de funcionalidade.
- Para a exploração de dados maior, manipulação de dados e engenharia de funcionalidade, utilize o Python para emitir consultas SQL directamente contra a base de dados do SQL Server na Azure VM.
- Decida o tamanho da amostra a utilizar para criar um modelo Azure aprendizagem de máquinas.

Quando estiver preparado para avançar para a aprendizagem de máquinas Azure, poderá:  

1. Guardar a consulta SQL final para extrair os dados de exemplo e copiar colar a consulta directamente para [Importar dados] [ import-data] módulo na aprendizagem de máquinas Azure. Este método é demonstrado na secção [Models do edifício em Azure aprendizagem de máquinas](#mlmodel) .    
2. Manter os dados incluídos na amostra e de engenharia que pretende utilizar para criar uma nova tabela de base de dados de modelo, em seguida, utilize a nova tabela dos [Dados de importação] de[ import-data] módulo.

Seguem-se alguns exploração de dados, visualização de dados e funcionalidade de engenharia exemplos. Para obter mais exemplos, consulte o bloco de notas de IPython de SQL de exemplo na pasta **Blocos de notas de IPython de amostra** .

#### <a name="initialize-database-credentials"></a>Inicializar a base de dados de credenciais

Inicializar as definições de ligação de base de dados nas seguintes variáveis:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database server>

#### <a name="create-database-connection"></a>Criar ligação de base de dados

    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

#### <a name="report-number-of-rows-and-columns-in-table-nyctaxitrip"></a>Número de mapa de linhas e colunas na tabela nyctaxi_trip

    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('nyctaxi_trip')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('nyctaxi_trip')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

- Número total de linhas = 173179759  
- Número total de colunas = 14

#### <a name="read-in-a-small-data-sample-from-the-sql-server-database"></a>Leitura-numa amostra pequenas de dados da base de dados SQL Server

    t0 = time.time()

    query = '''
        SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
            f.tolls_amount, f.total_amount, f.tip_amount
        FROM nyctaxi_trip t, nyctaxi_fare f
        TABLESAMPLE (0.05 PERCENT)
        WHERE t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
    '''

    df1 = pd.read_sql(query, conn)

    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)

    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

Tempo para ler que a tabela de exemplo é 6.492000 segundos  
Número de linhas e colunas obtido = (84952, 21)

#### <a name="descriptive-statistics"></a>Estatística descritiva

Agora está pronto para explorar os dados incluídos na amostra. Vamos começar por observar Estatística descritiva para o **viagem\_distância** (ou qualquer outro) (s) de:

    df1['trip_distance'].describe()

#### <a name="visualization-box-plot-example"></a>Visualização: Exemplo de desenho de caixa

Em seguida iremos observar o desenho de caixa para a distância de viagem visualizar o quantiles

    df1.boxplot(column='trip_distance',return_type='dict')

![Desenhar #1][1]

#### <a name="visualization-distribution-plot-example"></a>Visualização: Exemplo de desenho de distribuição

    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Desenhar #2][2]

#### <a name="visualization-bar-and-line-plots"></a>Efeito visual: Barra e linha parcelas

Neste exemplo, a distância de viagem em cinco posições de posição e visualizar os resultados binning.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

A Microsoft pode desenhar a distribuição de posição acima de uma barra ou parcela, conforme abaixo da linha

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Desenhar #3][3]

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Desenhar #4][4]

#### <a name="visualization-scatterplot-example"></a>Visualização: Exemplo de Scatterplot

Mostramos parcela de dispersão XY entre **viagem\_tempo de\_na\_segs** e **viagem\_distância** para ver se existe qualquer correlação

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Desenhar #6][6]

Do mesmo modo pode verificar a relação entre **taxa\_código** e **viagem\_distância**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Desenhar #8][8]

### <a name="sub-sampling-the-data-in-sql"></a>Colheita de tomas os dados de SQL

Quando preparar dados para o modelo de construção no [Azure máquina aprendizagem Studio](https://studio.azureml.net), pode decidir sobre a **consulta SQL para utilizar directamente o módulo de dados de importação** ou manter os dados com engenharia e incluídos na amostra uma nova tabela, pode utilizar os [Dados de importação] [ import-data] módulo com um simples * *Seleccione* FROM < o\_novo\_tabela\_nome >**.

Esta secção irá criar uma nova tabela para conter os dados incluídos na amostra e engenharia. Um exemplo de uma consulta SQL directa para criar um modelo é fornecido na secção de [exploração de dados e de funcionalidade de engenharia do SQL Server](#dbexplore) .

#### <a name="create-a-sample-table-and-populate-with-1-of-the-joined-tables-drop-table-first-if-it-exists"></a>Criar uma amostra da tabela e preencher com 1% das tabelas associadas. Largar tabela primeiro se existir.

Nesta secção, podemos associar as tabelas **nyctaxi\_viagem** e **nyctaxi\_tarifa de**, recolher uma amostra aleatória de 1%, e a manter os dados incluídos na amostra um novo nome de tabela **nyctaxi\_um\_por cento**:

    cursor = conn.cursor()

    drop_table_if_exists = '''
        IF OBJECT_ID('nyctaxi_one_percent', 'U') IS NOT NULL DROP TABLE nyctaxi_one_percent
    '''

    nyctaxi_one_percent_insert = '''
        SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount
        INTO nyctaxi_one_percent
        FROM nyctaxi_trip t, nyctaxi_fare f
        TABLESAMPLE (1 PERCENT)
        WHERE t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
        AND   pickup_longitude <> '0' AND dropoff_longitude <> '0'
    '''

    cursor.execute(drop_table_if_exists)
    cursor.execute(nyctaxi_one_percent_insert)
    cursor.commit()

### <a name="data-exploration-using-sql-queries-in-ipython-notebook"></a>Exploração de dados utilizando consultas SQL IPython bloco de notas

Nesta secção, vamos explorar as distribuições de dados utilizando os dados de 1% de colheita de amostras que são mantidos na nova tabela que criou acima. Tenha em atenção que explorations semelhantes podem ser efectuadas utilizando as tabelas originais, opcionalmente, utilizar **TABLESAMPLE** para limitar a exploração amostra ou para limitar os resultados para um tempo determinado período utilizando o **recolha\_data/hora** partições, tal como ilustrado na secção de [exploração de dados e de funcionalidade de engenharia do SQL Server](#dbexplore) .

#### <a name="exploration-daily-distribution-of-trips"></a>Exploração: Distribuição diária de viagens

    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Exploração: Distribuição de viagem por medallion

    query = '''
        SELECT medallion,count(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

### <a name="feature-generation-using-sql-queries-in-ipython-notebook"></a>Geração de funcionalidade utilizando consultas SQL IPython bloco de notas

Nesta secção irá gerar novos rótulos e funcionalidades directamente utilizando consultas SQL, a funcionar com a tabela de exemplo de 1% é criado na secção anterior.

#### <a name="label-generation-generate-class-labels"></a>Geração de etiqueta: Gerar etiquetas de classe

No exemplo seguinte, vamos gerar dois conjuntos de rótulos a utilizar para modelação:

1. Binário classe etiquetas **-inclinado** (previsão se será dada uma sugestão)
2. Etiquetas multiclass **Sugestão\_classe** (a posição de sugestão ou um intervalo de previsão)

        nyctaxi_one_percent_add_col = '''
            ALTER TABLE nyctaxi_one_percent ADD tipped bit, tip_class int
        '''

        cursor.execute(nyctaxi_one_percent_add_col)
        cursor.commit()

        nyctaxi_one_percent_update_col = '''
            UPDATE nyctaxi_one_percent
            SET
               tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
               tip_class = CASE WHEN (tip_amount = 0) THEN 0
                                WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                                WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                                WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                                ELSE 4
                            END
        '''

        cursor.execute(nyctaxi_one_percent_update_col)
        cursor.commit()

#### <a name="feature-engineering-count-features-for-categorical-columns"></a>Funcionalidade de engenharia: Contagem de funcionalidades para colunas de categorias

Neste exemplo transforma um campo de categorias num campo numérico, substituindo a cada categoria com a contagem das suas ocorrências nos dados.

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent ADD cmt_count int, vts_count int
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        WITH B AS
        (
            SELECT medallion, hack_license,
                SUM(CASE WHEN vendor_id = 'cmt' THEN 1 ELSE 0 END) AS cmt_count,
                SUM(CASE WHEN vendor_id = 'vts' THEN 1 ELSE 0 END) AS vts_count
            FROM nyctaxi_one_percent
            GROUP BY medallion, hack_license
        )

        UPDATE nyctaxi_one_percent
        SET nyctaxi_one_percent.cmt_count = B.cmt_count,
            nyctaxi_one_percent.vts_count = B.vts_count
        FROM nyctaxi_one_percent A INNER JOIN B
        ON A.medallion = B.medallion AND A.hack_license = B.hack_license
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="feature-engineering-bin-features-for-numerical-columns"></a>Engenharia de funcionalidade: Funcionalidades de posição para colunas numéricas

Neste exemplo transforma num campo numérico contínuo em intervalos de categoria predefinido, ou seja, transformação campo numérico num campo de categorias.

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent ADD trip_time_bin int
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        WITH B(medallion,hack_license,pickup_datetime,trip_time_in_secs, BinNumber ) AS
        (
            SELECT medallion,hack_license,pickup_datetime,trip_time_in_secs,
            NTILE(5) OVER (ORDER BY trip_time_in_secs) AS BinNumber from nyctaxi_one_percent
        )

        UPDATE nyctaxi_one_percent
        SET trip_time_bin = B.BinNumber
        FROM nyctaxi_one_percent A INNER JOIN B
        ON A.medallion = B.medallion
        AND A.hack_license = B.hack_license
        AND A.pickup_datetime = B.pickup_datetime
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="feature-engineering-extract-location-features-from-decimal-latitudelongitude"></a>Funcionalidade de engenharia: Extrair funcionalidades de localização de Decimal Latitude/Longitude

Neste exemplo divide a representação decimal de um campo de latitude norte e/ou de longitude em vários campos de região de granularidade diferente, tais como, país, cidade, cidade, bloquear, etc. Tenha em atenção que os novos campos de geo não estão mapeados para localizações reais. Para obter informações sobre localizações de geocode de mapeamento, consulte [Bing mapas restantes serviços](https://msdn.microsoft.com/library/ff701710.aspx).

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent
        ADD l1 varchar(6), l2 varchar(3), l3 varchar(3), l4 varchar(3),
            l5 varchar(3), l6 varchar(3), l7 varchar(3)
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        UPDATE nyctaxi_one_percent
        SET l1=round(pickup_longitude,0)
            , l2 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 1 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),1,1) ELSE '0' END     
            , l3 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 2 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),2,1) ELSE '0' END     
            , l4 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 3 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),3,1) ELSE '0' END     
            , l5 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 4 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),4,1) ELSE '0' END     
            , l6 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 5 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),5,1) ELSE '0' END     
            , l7 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 6 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),6,1) ELSE '0' END
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Verificar o formato final da tabela featurized

    query = '''SELECT TOP 100 * FROM nyctaxi_one_percent'''
    pd.read_sql(query,conn)

A Microsoft está agora preparadas avançar para criar um modelo e implementação de modelo na [Aprendizagem de máquinas Azure](https://studio.azureml.net). Os dados estão prontos para qualquer um dos problemas de previsão detectados anteriormente, nomeadamente:

1. Classificação binária: prever ou não uma sugestão tiver sido paga para uma viagem.

2. Classificação de multiclass: para prever o intervalo de sugestão paga, de acordo com as classes definidas anteriormente.

3. Tarefa de regressão: para prever o montante da sugestão de pagamento para uma viagem.  


## <a name="mlmodel"></a>Criar modelos na aprendizagem de máquinas Azure

Para iniciar o exercício de modelação, inicie sessão na sua área de trabalho de aprendizagem de máquinas Azure. Se ainda não tiver criado uma máquina de aprendizagem da área de trabalho, consulte [criar uma área de trabalho Azure aprendizagem de máquina](machine-learning-create-workspace.md).

1. Para começar a trabalhar com Azure aprendizagem de máquinas, consulte [o que é Azure máquina aprendizagem Studio?](machine-learning-what-is-ml-studio.md)

2. Iniciar sessão na [máquina Azure aprendizagem Studio](https://studio.azureml.net).

3. Homepage da Studio fornece uma grande quantidade de informações, vídeos, iniciações, hiperligações para a referência de módulos e outros recursos. Para mais informações sobre aprendizagem de máquinas Azure, consulte o [Centro de documentação de aprendizagem de máquina Azure](https://azure.microsoft.com/documentation/services/machine-learning/).

Uma experiência de formação típico consiste no seguinte:

1. Crie uma experiência **+ Novo** .
2. Obter os dados a aprendizagem de máquinas Azure.
3. Pré-processar, transformar e manipular os dados conforme necessário.
4. Gera funcionalidades conforme necessário.
5. Divida os dados em conjuntos de dados de formação / / teste de validação (ou conjuntos de dados separados para cada um).
6. Seleccione um ou mais algoritmos de aprendizagem de máquina dependendo do problema de aprendizagem para resolver. Por exemplo, classificação binária, classificação de multiclass, regressão.
7. Prepare um ou mais modelos utilizando o conjunto de dados de formação.
8. Pontuação base de dados de validação utilizando os modelos de formação.
9. Avalie os modelos para calcular as métricas relevantes para o problema de aprendizagem.
10. Fino optimizar os modelos e seleccione o modelo de melhor implementar.

Neste exercício, vamos ter já explorou e engenharia os dados no SQL Server e decidida a dimensão da amostra para ingerir esta última em Azure aprendizagem de máquinas. Para criar um ou mais dos modelos de previsão decidimos:

1. Obter os dados para a aprendizagem de máquinas de Azure utilizando os [Dados de importação] [ import-data] módulo, disponível na secção de **dados de entrada e saída** . Para mais informações, consulte os [Dados de importação] [ import-data] página de referência de módulo.

    ![Aprendizagem de máquinas Azure importar dados][17]

2. Seleccione **a base de dados do Azure SQL** como a **origem de dados** no painel de **Propriedades** .

3. Introduza o nome DNS de base de dados no campo **nome do servidor de base de dados** . Formato:`tcp:<your_virtual_machine_DNS_name>,1433`

4. Introduza o **nome de base de dados** no campo correspondente.

5. Introduza o **nome de utilizador SQL** na **nome de aqccount de utilizador de servidor e a palavra-passe de **servidor utilizador conta palavra-passe * *.

6. Verifique a opção de **aceitar qualquer certificado de servidor** .

7. Na área de texto de **consulta de base de dados** editar, colar a consulta que extrai os necessários campos (incluindo quaisquer campos calculados, tais como as etiquetas) de base de dados e para baixo amostras os dados para o tamanho da amostra pretendido.

Um exemplo de uma experiência de classificação binário ler dados directamente a partir da base de dados do SQL Server é a figura abaixo. Experiências semelhantes podem ser construídas para a classificação multiclass e problemas de regressão.

![Comboio de aprendizagem máquina Azure][10]

> [AZURE.IMPORTANT] Os dados da modelação de extracção e recolha de amostras de exemplos de consulta fornecidas nas secções anteriores, **todas as etiquetas para os exercícios de três modelação estão incluídas na consulta**. É um passo importante (obrigatório) em cada um dos exercícios modelação para **Excluir** os rótulos desnecessários para os outros dois problemas e quaisquer outras **fugas de destino**. Por exemplo, quando utilizar a classificação de binária, utilize a etiqueta **-inclinado** e excluir os campos **Sugestão\_classe**, **Sugestão\_valor**, e **total\_valor**. Estes forem fugas de destino uma vez que implicam a sugestão de pagamento.
>
> Para excluir colunas desnecessárias e/ou fugas de destino, pode utilizar [Seleccionar colunas no conjunto de dados] [ select-columns] módulo ou [Editar metadados][edit-metadata]. Para mais informações, consulte [Seleccionar colunas no conjunto de dados] [ select-columns] e [Editar metadados] [ edit-metadata] páginas de referência.

## <a name="mldeploy"></a>Implementar modelos de aprendizagem de máquinas Azure

Quando o modelo estiver preparado, pode facilmente implementá-lo como um serviço web directamente a partir de experiência. Para mais informações sobre como implementar serviços web de aprendizagem de máquinas Azure, consulte [Implementar um serviço web de aprendizagem de máquinas Azure](machine-learning-publish-a-machine-learning-web-service.md).

Para implementar um novo serviço web, necessita de:

1. Crie uma experiência de pontuação.
2. Implemente o serviço web.

Para criar uma experiência de pontuação de uma experiência de formação **concluído** , clique em **Criar PONTUAÇÃO EXPERIÊNCIAS** na barra de acção inferior.

![Pontuação Azure][18]

Aprendizagem de máquinas Azure irá tentar criar uma experiência de pontuação baseada nos componentes da experiência de formação. Em especial, será:

1. Guardar o modelo com formação e remova os módulos de formação de modelo.
2. Identifica uma **porta de entrada** lógica para representar o esquema de dados de entrada esperado.
3. Identifica uma **porta de saída** de lógica para representar o esquema de saída de serviço web esperada.

Quando é criada a experiência de pontuação, revê-la e ajustar conforme necessário. É um ajustamento típico substituir o conjunto de dados de entrada e/ou a consulta por um que exclui os campos da etiqueta, que não estão disponíveis quando o serviço é chamado. Também é uma boa prática para reduzir o tamanho do conjunto de dados de entrada e/ou consulta para alguns registos, suficiente para indicar o esquema de teclado. Para a porta de saída, é comum para excluir campos de entrada de todos os e incluir apenas **Contabilizadas rótulos** e **Contabilizadas probabilidades** na saída utilizando a [Seleccionar colunas no conjunto de dados] [ select-columns] módulo.

Um exemplo de experiência de pontuação é na figura abaixo. Quando estiver preparado para implementar, clique no botão de **Publicar serviço WEB** na barra de acção inferior.

![Publicar de aprendizagem de máquinas Azure][11]

Para recap, neste tutorial do tutorial, criou um ambiente de ciência dados Azure, trabalhado com um grande conjunto de dados público desde a aquisição de dados para modelar a formação e implementação de um serviço web de aprendizagem de máquinas Azure.

### <a name="license-information"></a>Informações de licença

Este tutorial de amostra e respectivo acompanhamento scripts e IPython notebook(s) são partilhados pela Microsoft ao abrigo da licença do MIT. Verifique o ficheiro License txt no directório do código exemplo de GitHub para obter mais detalhes.

### <a name="references"></a>Referências

• a [página de transferência de viagens de táxis NYC Andrés Monroy](http://www.andresmh.com/nyctaxitrips/)  
• [FOILing dados de viagem do NYC táxis por Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC Taxi e investigação da Comissão de Limousine e estatísticas](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)


[1]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/machine-learning-data-science-process-sql-walkthrough/azuremltrain.png
[11]: ./media/machine-learning-data-science-process-sql-walkthrough/azuremlpublish.png
[12]: ./media/machine-learning-data-science-process-sql-walkthrough/ssmsconnect.png
[13]: ./media/machine-learning-data-science-process-sql-walkthrough/executescript.png
[14]: ./media/machine-learning-data-science-process-sql-walkthrough/sqlserverproperties.png
[15]: ./media/machine-learning-data-science-process-sql-walkthrough/sqldefaultdirs.png
[16]: ./media/machine-learning-data-science-process-sql-walkthrough/bulkimport.png
[17]: ./media/machine-learning-data-science-process-sql-walkthrough/amlreader.png
[18]: ./media/machine-learning-data-science-process-sql-walkthrough/amlscoring.png


<!-- Module References -->
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
