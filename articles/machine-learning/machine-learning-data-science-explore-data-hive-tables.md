<properties
    pageTitle="Explorar dados em tabelas do ramo com consultas ramo | Microsoft Azure"
    description="Explore dados em tabelas ramo através de consultas de ramo."
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
    ms.date="09/13/2016"
    ms.author="bradsev" />

# <a name="explore-data-in-hive-tables-with-hive-queries"></a>Explorar dados em tabelas do ramo com ramo de consultas

Este artigo fornece exemplos ramo de scripts que são utilizadas para explorar dados em tabelas ramo num cluster HDInsight Hadoop.

As seguintes **menu** ligações para tópicos que descrevem como utilizar ferramentas para explorar dados a partir de vários ambientes de armazenamento.

[AZURE.INCLUDE [cap-explore-data-selector](../../includes/cap-explore-data-selector.md)]

## <a name="prerequisites"></a>Pré-requisitos
Este artigo assume que tem:

* Criar uma conta de armazenamento Azure. Se precisar de instruções, consulte o artigo [criar uma conta de armazenamento do Windows Azure](../storage/storage-create-storage-account.md#create-a-storage-account)
* Aprovisionar um cluster de Hadoop personalizado com o serviço do HDInsight. Se precisar de instruções, consulte o artigo [Personalizar Azure HDInsight Hadoop Clusters para análise avançadas](machine-learning-data-science-customize-hadoop-cluster.md).
* Foi carregados os dados a tabelas do ramo do Azure HDInsight Hadoop clusters. Se não tiver, siga as instruções em [criar e carregar dados a tabelas ramo](machine-learning-data-science-move-hive-tables.md) para carregar dados a tabelas ramo pela primeira vez.
* Ativado para cluster de acesso remoto. Se precisar de instruções, consulte o artigo [aceder a cabeça de Hadoop Cluster de nó](machine-learning-data-science-customize-hadoop-cluster.md#headnode).
* Se precisar de instruções sobre como submeter ramo de consultas, consulte o artigo [como submeter ramo de consultas](machine-learning-data-science-move-hive-tables.md#submit)

## <a name="example-hive-query-scripts-for-data-exploration"></a>Scripts de consulta de ramo de exemplo para informações detalhadas de dados

1. Obter a contagem de observações por partição `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`

2. Obter a contagem de observações por dia `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`

3. Obter os níveis de uma coluna categorias  
    `SELECT  distinct <column_name> from <databasename>.<tablename>`

4. Obter o número de níveis na combinação de duas colunas de categorias `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`

5. Obter a distribuição para colunas numéricas  
    `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`

6. Extrair registos de participar duas tabelas

        SELECT
            a.<common_columnname1> as <new_name1>,
            a.<common_columnname2> as <new_name2>,
            a.<a_column_name1> as <new_name3>,
            a.<a_column_name2> as <new_name4>,
            b.<b_column_name1> as <new_name5>,
            b.<b_column_name2> as <new_name6>
        FROM
            (
            SELECT <common_columnname1>,
                <common_columnname2>,
                <a_column_name1>,
                <a_column_name2>,
            FROM <databasename>.<tablename1>
            ) a
            join
            (
            SELECT <common_columnname1>,
                <common_columnname2>,
                <b_column_name1>,
                <b_column_name2>,
            FROM <databasename>.<tablename2>
            ) b
            ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>

## <a name="additional-query-scripts-for-taxi-trip-data-scenarios"></a>Scripts de consulta adicionais para cenários de dados de viagem de veículo

Exemplos de consultas que são específicos para os [Dados de viagem de veículo pt](http://chriswhong.com/open-data/foil_nyc_taxi/) cenários também são fornecidos no [repositório de Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Estas consultas já tem o esquema de dados especificada e estão prontas para ser apresentado ao executar.
