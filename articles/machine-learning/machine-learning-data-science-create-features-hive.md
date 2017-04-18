<properties
    pageTitle="Criar funcionalidades para os dados num cluster Hadoop através de consultas de ramo | Microsoft Azure"
    description="Exemplos de consultas de ramo que geram funcionalidades em dados armazenados num cluster Azure HDInsight Hadoop."
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
    ms.date="09/19/2016"
    ms.author="hangzh;bradsev" />


#<a name="create-features-for-data-in-an-hadoop-cluster-using-hive-queries"></a>Criar funcionalidades para os dados num cluster Hadoop através de consultas de ramo

Este documento mostra como criar funcionalidades para os dados armazenados num cluster Azure HDInsight Hadoop através de consultas de ramo. Estas consultas ramo utilizam incorporado Hive funções definidas pelo utilizador (UDFs), os scripts para a qual são fornecidos.

As operações necessárias para criar funcionalidades podem ser utiliza muita memória. O desempenho das consultas ramo torna-se mais crítico nestes casos e pode ser melhorado através de sintonização determinados parâmetros. O processo de optimização destes parâmetros é abordado na secção final.

Exemplos de consultas que são apresentados são específicos para os [Dados de viagem de veículo pt](http://chriswhong.com/open-data/foil_nyc_taxi/) cenários também são fornecidos no [repositório de Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Estas consultas já tem o esquema de dados especificada e estão prontas para ser apresentado ao executar. Na secção final, parâmetros que podem ajustar o utilizadores para que o desempenho do ramo de consultas pode ser melhorado são também debatidos.

[AZURE.INCLUDE [cap-create-features-data-selector](../../includes/cap-create-features-selector.md)]Este **menu** contém ligações para tópicos que descrevem como criar funcionalidades para os dados em vários ambientes. Esta tarefa é um passo o [Processo de ciências de dados de equipa (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).


## <a name="prerequisites"></a>Pré-requisitos
Este artigo assume que tem:

* Criar uma conta de armazenamento Azure. Se precisar de instruções, consulte o artigo [criar uma conta de armazenamento do Windows Azure](../storage/storage-create-storage-account.md#create-a-storage-account)
* Aprovisionar um cluster de Hadoop personalizado com o serviço do HDInsight.  Se precisar de instruções, consulte o artigo [Personalizar Azure HDInsight Hadoop Clusters para análise avançadas](machine-learning-data-science-customize-hadoop-cluster.md).
* Foi carregados os dados a tabelas do ramo do Azure HDInsight Hadoop clusters. Se não tiver, siga [criar e carregar dados a tabelas Hive](machine-learning-data-science-move-hive-tables.md) para carregar dados a tabelas ramo pela primeira vez.
* Ativado para cluster de acesso remoto. Se precisar de instruções, consulte o artigo [aceder a cabeça de Hadoop Cluster de nó](machine-learning-data-science-customize-hadoop-cluster.md#headnode).


##<a name="hive-featureengineering"></a>Geração de funcionalidade

Nesta secção, vários exemplos de formas na qual podem gerar funcionalidades através de consultas de ramo são descritos. Assim que tiver gerado funcionalidades adicionais, pode adicioná-los como colunas à tabela já existente ou criar uma nova tabela com as funcionalidades adicionais e a chave primária, que possam ser associado, em seguida, com a tabela original. Eis alguns exemplos apresentados:

1. [Frequência baseado funcionalidade geração](#hive-frequencyfeature)
2. [Riscos de variáveis de categorias na classificação binária](#hive-riskfeature)
3. [Extrair funcionalidades de campo de data/hora](#hive-datefeatures)
4. [Extrair funcionalidades de campo de texto](#hive-textfeatures)
5. [Calcular a distância entre as coordenadas de GPS](#hive-gpsdistance)

###<a name="hive-frequencyfeature"></a>Frequência baseado funcionalidade geração

Muitas vezes é útil para calcular a frequência dos níveis de uma variável de categorias ou a frequência de certas combinações de níveis de múltiplas variáveis de categorias. Os utilizadores podem utilizar o seguinte script para calcular este frequência:

        select
            a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
        from
        (
            select
                <column_name1>,<column_name2>, count(*) as sub_count
            from <databasename>.<tablename> group by <column_name1>, <column_name2>
        )a
        order by frequency desc;


###<a name="hive-riskfeature"></a>Riscos de variáveis de categorias na classificação binária

Classificação binária, precisamos de converter variáveis categorias não numérico funcionalidades numéricas quando os modelos de a ser utilizados apenas tomar funcionalidades numéricas. Isto é feito substituindo cada nível de não numérico por um risco numérico. Nesta secção, mostramos-algumas consultas ramo genéricas que calculam os valores de risco (registo hipóteses) de uma variável de categorias.


        set smooth_param1=1;
        set smooth_param2=20;
        select
            <column_name1>,<column_name2>,
            ln((sum_target+${hiveconf:smooth_param1})/(record_count-sum_target+${hiveconf:smooth_param2}-${hiveconf:smooth_param1})) as risk
        from
            (
            select
                <column_nam1>, <column_name2>, sum(binary_target) as sum_target, sum(1) as record_count
            from
                (
                select
                    <column_name1>, <column_name2>, if(target_column>0,1,0) as binary_target
                from <databasename>.<tablename>
                )a
            group by <column_name1>, <column_name2>
            )b

Neste exemplo, variáveis `smooth_param1` e `smooth_param2` estão definidos para ajustar os valores de risco calculados a partir dos dados. Riscos de ter um intervalo entre -Inf e Inf. Um riscos > 0 indica que a probabilidade de que o destino for igual a 1 é maior que 0,5.

Depois do risco é calculado a tabela, os utilizadores podem atribuir valores de risco a uma tabela, associando-lo com a tabela de risco. A consulta de unir ramo foi fornecida na secção anterior.

###<a name="hive-datefeatures"></a>Extrair funcionalidades dos campos de data/hora

Ramo vem com um conjunto de UDFs para processamento de campos de data/hora. Ramo de registo, o formato de data/hora predefinido é ' aaaa-MM-dd 00:00:00 ' ('1970-01-01 12:21:32 ' por exemplo). Nesta secção, podemos apresentar os exemplos que extrair do dia do mês, mês a partir de um campo de data/hora e outros exemplos que converter uma cadeia de data/hora num formato diferente o formato predefinido para uma cadeia de data/hora nas predefinições formatar.

        select day(<datetime field>), month(<datetime field>)
        from <databasename>.<tablename>;

Esta consulta ramo assume que o *& #60; campo de data/hora >* está no formato de data/hora predefinido.

Se não for um campo de data/hora no formato predefinido, tem de converter o campo Data/hora em carimbo de data / hora Unix pela primeira vez e, em seguida, converter o carimbo de hora Unix uma cadeia de data/hora que está no formato predefinido. Quando a data/hora nas predefinições for formato, os utilizadores podem aplicar a UDFs para extrair funcionalidades de data/hora incorporada.

        select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
        from <databasename>.<tablename>;

Nesta consulta, se o *& #60; campo de data/hora >* tem o padrão de como *26/03/2015 12:04:39*, o *' & #60; padrão do campo Data/hora >'* deve ser `'MM/dd/yyyy HH:mm:ss'`. Para testá-la, os utilizadores podem executar

        select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
        from hivesampletable limit 1;

O *hivesampletable* nesta consulta é pré-instalado em todos os Azure HDInsight Hadoop clusters por predefinição quando forem aprovisionados clusters.


###<a name="hive-textfeatures"></a>Extrair funcionalidades de campos de texto

Quando a tabela Hive tem um campo de texto que contém uma cadeia de palavras que estão delimitado por espaços, a seguinte consulta extrai o comprimento da cadeia e o número de palavras na cadeia.

        select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
        from <databasename>.<tablename>;

###<a name="hive-gpsdistance"></a>Calcular a distância entre conjuntos de coordenadas GPS

A consulta fornecida nesta secção pode ser aplicada diretamente para os dados de viagem de veículo pt. É o objetivo desta consulta Mostrar como aplicar uma funções matemáticas incorporado no ramo para gerar funcionalidades.

Os campos que são utilizados nesta consulta são as coordenadas GPS de recolha e dropoff localizações, com o nome *recolha\_longitude*, *recolha\_latitude*, *dropoff\_longitude*, e *dropoff\_latitude*. As consultas que calculam a distância entre as coordenadas recolha e dropoff direta são:

        set R=3959;
        set pi=radians(180);
        select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude,
            ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
            *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
            *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
            /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
            +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
            pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
        from nyctaxi.trip
        where pickup_longitude between -90 and 0
        and pickup_latitude between 30 and 90
        and dropoff_longitude between -90 and 0
        and dropoff_latitude between 30 and 90
        limit 10;

As equações matemáticas que calculam a distância entre dois coordenadas GPS podem encontrar no site de <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">Scripts de tipo de móveis</a> , criados por Pedro Lapisu. No seu Javascript, a função `toRad()` é de apenas *lat_or_lon*pi/180*, que converte graus em radianos. Aqui, *lat_or_lon * é o latitude ou longitude. Uma vez que ramo não fornece a função `atan2`, mas fornece a função `atan`, o `atan2` função é implementada por `atan` função na consulta ramo acima utilizando a definição descrita na <a href="http://en.wikipedia.org/wiki/Atan2" target="_blank">Wikipédia</a>.

![Criar a área de trabalho](./media/machine-learning-data-science-create-features-hive/atan2new.png)

Uma lista completa de ramo UDFs incorporados podem ser encontrados na secção **Funções incorporadas** no <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">Apache Hive wiki</a>).  

## <a name="tuning"></a>Tópicos de avançadas: parâmetros de sintonização Hive para melhorar a velocidade de consulta

As predefinições de parâmetro de ramo cluster poderão não ser adequadas para as consultas de ramo e os dados que estão a processar as consultas. Nesta secção, discutimos alguns parâmetros que podem ajustar o utilizadores que melhorar o desempenho do ramo de consultas. Os utilizadores têm de adicionar o parâmetro de sintonização consultas antes das consultas de processamento de dados.

1. **Espaço de pilha de Java**: para consultas que envolvam participar grandes conjuntos de dados ou o processamento registos de tempo, **a ficar sem espaço de pilha** é uma do erro comum. Isto pode ser optimizado ao definir parâmetros *mapreduce.map.java.opts* e *mapreduce.task.io.sort.mb* para valores pretendidos. Eis um exemplo:

        set mapreduce.map.java.opts=-Xmx4096m;
        set mapreduce.task.io.sort.mb=-Xmx1024m;


    Este parâmetro atribui 4GB de memória para o espaço de pilha de Java e também facilita a ordenação mais eficientes atribuindo mais memória para a mesma. É uma boa ideia para reproduzir com estas atribuições se existirem qualquer tarefa erros de falha relacionados com o espaço de pilha.

2. **Tamanho do bloco DFS** : este parâmetro define a unidade de dados que armazena o sistema de ficheiros. Por exemplo, se o tamanho do bloco DFS for 128MB, em seguida, quaisquer dados do tamanho menos e até ao 128MB é armazenado num único bloco, enquanto os dados que são maiores do que 128MB é atribuído blocos extra. Escolher um tamanho de muito pequeno bloco faz com que transparências grandes no Hadoop uma vez que o nó do nome tem que processar muitas mais pedidos para encontrar o bloco relevante relativos ao ficheiro. Uma definição recomendada quando matéria de gigabytes (ou maior) os dados são:

        set dfs.block.size=128m;

3. **Optimizar aderir a operação em ramo** : enquanto operações de associação no quadro mapa/reduzir normalmente executada na fase de reduzir, por vezes, ganhos enormes podem ser realizados pelos agendamento associações na fase de mapa (também denominada "mapjoins"). Para direcionar Hive para fazer isto sempre que possível, podemos pode definir:

        set hive.auto.convert.join=true;

4. **Especifica o número de mappers ramo** : Hadoop enquanto permite ao utilizador definir o número de redução, o número de mappers é normalmente não definido pelo utilizador. Um truque que lhe permite que é algumas grau de controlo neste número escolher variáveis Hadoop, *mapred.min.split.size* e *mapred.max.split.size* como o tamanho de cada tarefa mapa é determinado por:

        num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))

    Normalmente, o valor predefinido de *mapred.min.split.size* for 0, que *mapred.max.split.size* é **Long.MAX** , sendo que *dfs.block.size* 64 MB. Como é possível ver, tendo em conta o tamanho dos dados, estes parâmetros por "configuração" de sintonização-los permite-nos para o número de mappers utilizado o gráfico.

5. Algumas outras mais **Opções avançadas** para otimizar o desempenho do ramo é mencionado abaixo. Estes permitem-lhe definir a memória alocada para mapear e reduzir tarefas e podem ser útil em optimização desempenho. Fórum tenha em atenção que a *mapreduce.reduce.memory.mb* não pode ser maior do que o tamanho de memória física de cada nó de trabalho no Hadoop cluster.

        set mapreduce.map.memory.mb = 2048;
        set mapreduce.reduce.memory.mb=6144;
        set mapreduce.reduce.java.opts=-Xmx8192m;
        set mapred.reduce.tasks=128;
        set mapred.tasktracker.reduce.tasks.maximum=128;
