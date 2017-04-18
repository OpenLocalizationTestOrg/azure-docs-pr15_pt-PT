<properties 
    pageTitle="Explorar dados no SQL Server Virtual Machine no Azure | Microsoft Azure" 
    description="Como explorar dados armazenados numa VM de servidor do SQL no Azure." 
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
    ms.date="09/13/2016" 
    ms.author="bradsev" /> 

#<a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>Explorar dados no SQL Server Virtual Machine no Azure


Este documento aborda como explorar dados armazenados num VM de servidor do SQL no Azure. Isto pode ser feito ao dados wrangling utilizando SQL ou ao utilizar uma linguagem de programação como Python.

As seguintes **menu** ligações para tópicos que descrevem como utilizar ferramentas para explorar dados a partir de vários ambientes de armazenamento. Esta tarefa é um passo no processo de análise de Cortana (REMATE).

[AZURE.INCLUDE [cap-explore-data-selector](../../includes/cap-explore-data-selector.md)]


> [AZURE.NOTE] As instruções SQL de exemplo neste documento partem do pressuposto de que os dados estão no SQL Server. Se não for, consulte o mapa do processo de ciências dados na nuvem para saber como mover os seus dados para o SQL Server.



## <a name="sql-dataexploration"></a>Explorar dados do SQL com scripts SQL

Aqui estão alguns exemplos SQL de scripts que podem ser utilizados para explorar arquivos de dados do SQL Server.

1. Obter a contagem de observações por dia

    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 

2. Obter os níveis de uma coluna categorias

    `select  distinct <column_name> from <databasename>`

3. Obter o número de níveis na combinação de duas colunas de categorias 

    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`

4. Obter a distribuição para colunas numéricas

    `select <column_name>, count(*) from <tablename> group by <column_name>`

> [AZURE.NOTE] Para um exemplo prático, pode utilizar o [conjunto de dados de veículo pt](http://www.andresmh.com/nyctaxitrips/) e indique IPNB intitulado [pt dados wrangling utilizando IPython bloco de notas e o SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) para uma guia passo a passo para fim.

##<a name="python"></a>Explorar dados do SQL com Python

Utilizar Python para explorar dados e gerar funcionalidades quando os dados são no SQL Server é semelhante de processamento de dados no BLOBs do Azure utilizando Python, conforme é documentado [BLOBs do Azure de processo de dados no seu ambiente de ciências de dados](machine-learning-data-science-process-data-blob.md). Os dados tem de ser carregados a partir da base de dados para um pandas DataFrame e, em seguida, podem ser processados ainda mais. Se documentados o processo de ligar à base de dados e carregar os dados na DataFrame nesta secção.

O formato da cadeia de ligação seguinte pode ser utilizado para ligar a uma base de dados do SQL Server de Python utilizando pyodbc (nomedoservidor substituir, dbname, nome de utilizador e palavra-passe com o seu valores específicos):

    #Set up the SQL Azure connection
    import pyodbc   
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

A [biblioteca de Pandas](http://pandas.pydata.org/) no Python fornece um conjunto avançado de estruturas de dados e ferramentas de análise de dados para manipulação de dados para a programação de Python. O código seguinte lê os resultados devolvidos a partir de uma base de dados do SQL Server numa moldura Pandas dados:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Agora pode trabalhar com o DataFrame Pandas como descrito no tópico de [BLOBs do Azure de processo de dados no seu ambiente de ciências de dados](machine-learning-data-science-process-data-blob.md).

## <a name="cortana-analytics-process-in-action-example"></a>Processo de análise de Cortana no exemplo de ação

Para obter um exemplo de ponto a ponto instruções passo a passo do processo de análise de Cortana utilizando um conjunto de dados público, consulte o artigo [o processo de ciências de dados de equipa em ação: utilizar o SQL Server](machine-learning-data-science-process-sql-walkthrough.md).

 
