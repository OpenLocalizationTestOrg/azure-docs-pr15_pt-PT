<properties
    pageTitle="Exemplo de dados em tabelas Azure HDInsight Hive | Microsoft Azure"
    description="Para baixo de recolha de dados de tabelas de Hive Azure HDInsight (Hadopop)"
    services="machine-learning,hdinsight"
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

# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Dados de exemplo no Azure HDInsight Hive tabelas

Neste artigo, iremos descrevem como dados armazenados em tabelas Azure HDInsight Hive através de consultas de ramo de amostras para baixo. Vamos abordar três métodos de amostragem popularly utilizadas:

* Uniform sondagem
* Amostragem pelos grupos
* Amostragem estratificada

**Porque é que os seus dados de exemplo?**
Se o conjunto de dados que pretende analisar for grande, normalmente é uma boa ideia para baixo amostras os dados a reduzi-lo para um tamanho mais pequeno, mas representativo e mais viável. Isto facilita a compreensão de dados, informações detalhadas e engenharia de funcionalidade. Seu papel no processo de ciências de dados de equipa é protótipos rápido das funções de processamento de dados e modelos de aprendizagem automática.

No **menu** abaixo ligações para tópicos que descrevem como dados a partir de vários ambientes de armazenamento de exemplo.

[AZURE.INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

Esta tarefa amostragem é um passo o [Processo de ciências de dados de equipa (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).


## <a name="how-to-submit-hive-queries"></a>Como pretende submeter ramo de consultas
Ramo de consultas podem ser submetidas a partir da consola de linha de comandos Hadoop no cabeça nó do Hadoop cluster. Para fazer isto, inicie sessão no nó do Hadoop cluster cabeça, abra a consola de linha de comandos Hadoop e submeter as consultas de ramo a partir daí. Para obter instruções sobre como submeter ramo consultas na consola de linha de comandos Hadoop, consulte o artigo [como submeter ramo de consultas](machine-learning-data-science-move-hive-tables.md#submit).

## <a name="uniform"></a>Uniform sondagem
Uniform amostragem significa que cada linha no conjunto de dados tem um hipóteses igual de que está a ser recolhidos. Isto pode ser implementado adicionando um rand (campo extra) para o conjunto de dados na consulta interna "select" e, a consulta externa "select" essa condição nesse campo aleatório.

Eis um exemplo de consulta:

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, …, fieldN
    from
        (
        select
            field1, field2, …, fieldN, rand() as samplekey
        from <hive table name>
        )a
    where samplekey<='${hiveconf:sampleRate}'

Aqui, `<sample rate, 0-1>` Especifica a proporção de registos que pretendem que os utilizadores para de exemplo.

## <a name="group"></a>Amostragem pelos grupos

Quando dados categorias amostragem, poderá incluir ou excluir todas as instâncias de algum valor específico de uma variável de categorias. Este é o que se destinar por "amostragem por grupo".
Por exemplo, se tiver uma variável de categorias "Estado", que tem valores NY, MA, AC, NJ, PA, etc, pretender que os registos do mesmo Estado de estar sempre em conjunto, se são recolhidos ou não.

Eis um exemplo de consulta que exemplos por grupo:

    SET sampleRate=<sample rate, 0-1>;
    select
        b.field1, b.field2, …, b.catfield, …, b.fieldN
    from
        (
        select
            field1, field2, …, catfield, …, fieldN
        from <table name>
        )b
    join
        (
        select
            catfield
        from
            (
            select
                catfield, rand() as samplekey
            from <table name>
            group by catfield
            )a
        where samplekey<='${hiveconf:sampleRate}'
        )c
    on b.catfield=c.catfield

## <a name="stratified"></a>Amostragem estratificada

Amostragem é estratificada relativamente a uma variável de categorias quando as amostras obtidas têm valores de que categorias que se encontram na mesma relação, tal como a população de elemento principal a partir do qual foram obtidos os exemplos. Utilizando o mesmo exemplo como acima, suponha que os seus dados tiverem sub-populações Estados-membros, diga NJ tem 100 observações, NY tem 60 observações e WA tem 300 observações. Se especificar a taxa de amostragem estratificada para ser 0,5, em seguida, amostra obtida deve ter cerca 50, 30 e 150 observações de NJ, NY e WA respetivamente.

Eis um exemplo de consulta:

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, field3, ..., fieldN, state
    from
        (
        select
            field1, field2, field3, ..., fieldN, state,
            count(*) over (partition by state) as state_cnt,
            rank() over (partition by state order by rand()) as state_rank
        from <table name>
        ) a
    where state_rank <= state_cnt*'${hiveconf:sampleRate}'


Para obter informações sobre métodos de amostragem mais avançadas que estão disponíveis no ramo, consulte o artigo [LanguageManual amostragem](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling).
