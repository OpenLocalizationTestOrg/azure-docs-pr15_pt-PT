<properties
    pageTitle="Criar funcionalidades para os dados no SQL Server utilizando SQL e Python | Microsoft Azure"
    description="Processo de dados do SQL Azure"
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="bradsev;fashah;garye" />


# <a name="create-features-for-data-in-sql-server-using-sql-and-python"></a>Criar funcionalidades para os dados no SQL Server utilizando SQL e Python


Este documento mostra como gerar funcionalidades para dados armazenados numa VM de servidor do SQL no Azure que ajudam a algoritmos de forma mais eficiente, aprenda com os dados. Isto pode ser feito utilizando SQL ou ao utilizar uma linguagem de programação como Python, sendo que ambos estão demonstrados aqui.

[AZURE.INCLUDE [cap-create-features-data-selector](../../includes/cap-create-features-selector.md)]Este **menu** contém ligações para tópicos que descrevem como criar funcionalidades para os dados em vários ambientes. Esta tarefa é um passo o [Processo de ciências de dados de equipa (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

> [AZURE.NOTE] Para um exemplo prático, pode consultar o [conjunto de dados de veículo pt](http://www.andresmh.com/nyctaxitrips/) e indique IPNB intitulado [pt dados wrangling utilizando IPython bloco de notas e o SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) para uma guia passo a passo para fim.


## <a name="prerequisites"></a>Pré-requisitos
Este artigo assume que tem:

* Criar uma conta de armazenamento Azure. Se precisar de instruções, consulte o artigo [criar uma conta de armazenamento do Windows Azure](../storage/storage-create-storage-account.md#create-a-storage-account)
* Os dados armazenados no SQL Server. Se não tiver, consulte o artigo [mover os dados para uma base de dados do SQL Azure para Azure máquina aprendizagem](machine-learning-data-science-move-sql-azure.md) para obter instruções sobre como mover os dados aí.


## <a name="sql-featuregen"></a>Geração de funcionalidade com SQL

Nesta secção, descrevemos formas de gerar funcionalidades utilizando SQL:  

1. [Contar com base funcionalidade geração](#sql-countfeature)
2. [Geração de funcionalidade binning](#sql-binningfeature)
3. [Gradual saída as funcionalidades de uma única coluna](#sql-featurerollout)


> [AZURE.NOTE] Assim que gerar funcionalidades adicionais, pode adicioná-los como colunas à tabela já existente ou criar uma nova tabela com as funcionalidades adicionais e a chave primária, que pode ser associado com a tabela original.

### <a name="sql-countfeature"></a>Contar com base funcionalidade geração

Este documento demonstra duas formas de contar funcionalidades a gerar. O primeiro método utiliza somas condicionais e o segundo método utiliza a cláusula 'where'. Estes possam, em seguida, ser associados com a tabela original (utilizando colunas de chave primária) para que funcionalidades de contagem juntamente com os dados originais.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3>

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename>
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2>

### <a name="sql-binningfeature"></a>Geração de funcionalidade binning

O exemplo seguinte mostra como gerar funcionalidades binned por binning (utilizando agrupamentos de 5) uma coluna numérica que pode ser utilizada em vez disso, como uma funcionalidade:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="sql-featurerollout"></a>Gradual saída as funcionalidades de uma única coluna

Nesta secção, podemos demonstram como recuperar saída numa única coluna numa tabela para gerar funcionalidades adicionais. O exemplo assume que existe uma coluna de latitude ou longitude na tabela a partir do qual está a tentar gerar funcionalidades.

Eis uma breve introdução a dados de localização de latitude/longitude (atribuição de recursos a partir do stackoverflow `http://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude`). Isto é útil compreender antes de featurizing no campo localização:

- Iniciar o avisa-nos se estamos a norte ou sul, Leste ou oeste no globo.
- Um centenas diferente de zero dígito indica-nos estamos a utilizar o longitude e não de latitude!
- Ordem das dezenas dígito dá uma posição para cerca de 1.000 quilómetros. Dá-nos informações úteis sobre que continente ou oceano estamos na.
- O dígito de unidades (um grau decimal) dá uma posição até 111 quilómetros (60 milhas náuticas cerca de 69 milhas). Pode indicar--nos aproximadamente que estado grande ou país que tivemos no.
- A primeira casa decimal vale 11.1 km:-lo a posição de uma cidade grande a partir de uma cidade grande vizinho possível distinguir.
- A segunda casa decimal vale 1.1 km:-pode separar um autoridades do próximo.
- A terceira casa decimal vale até 110 m: pode identificar um campo de agrícola grande ou campus institucional.
- A quarta casa decimal vale m até 11:-lhe identificar um lote do terreno. É comparável para a precisão típica de uma unidade GPS não corrigida sem interferência.
- A quinta casa decimal vale m até 1.1-distinguir árvores uns dos outros. Precisão a este nível com unidades GPS comerciais apenas pode ser atingido com a correção de diferença.
- A sexta casa decimal vale m até 0.11: pode utilizar esta opção para esquematizar estruturas em detalhe, para estruturar paisagens, construir estradas. Deve ser mais do que está suficientemente bom para controlar os movimentos do glaciers e rios. Isto pode ser realizado pelos tomar medidas painstaking com GPS, tal como GPS differentially corrigido.

As informações de localização podem pode ser featurized da seguinte forma, separar saída região, localização e informação de localidade. Tenha em atenção que uma vez também pode ligar um ponto final de descanso como o Bing Maps API disponíveis em `https://msdn.microsoft.com/library/ff701710.aspx` obter as informações de distrito/região.

    select
        <location_columnname>
        ,round(<location_columnname>,0) as l1       
        ,l2=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 1 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),1,1) else '0' end     
        ,l3=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 2 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),2,1) else '0' end     
        ,l4=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 3 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),3,1) else '0' end     
        ,l5=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 4 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),4,1) else '0' end     
        ,l6=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 5 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),5,1) else '0' end     
        ,l7=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 6 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),6,1) else '0' end     
    from <tablename>

As funcionalidades de localização com base acima podem ser ainda mais utilizadas para gerar a funcionalidades de contagem adicionais, tal como descrito anteriormente.


> [AZURE.TIP] Pode inserir os registos com o seu idioma escolhido através de programação. Poderá ter de inserir os dados blocos para melhorar a eficiência de escrita [dar saída de exemplo de como o pode fazer com pyodbc aqui](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python).
Outra alternativa consiste em Inserir dados na base de dados utilizando [utilitário BCP](https://msdn.microsoft.com/library/ms162802.aspx)

### <a name="sql-aml"></a>Ligar a aprendizagem máquina Azure

A funcionalidade recentemente criada pode ser adicionada como uma coluna a uma tabela existente ou armazenada numa nova tabela e associada com a tabela original para aprendizagem máquina. Funcionalidades podem ser geradas ou pode ser consultadas se tiver criado, utilizar o módulo de [Importar dados](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) em Azure ML conforme apresentado abaixo:

![leitores azureml](./media/machine-learning-data-science-process-sql-server-virtual-machine/reader_db_featurizedinput.png)

## <a name="python"></a>Utilizar uma linguagem de programação como Python

Utilizar Python para gerar funcionalidades quando os dados são no SQL Server é semelhante de processamento de dados no BLOBs do Azure utilizando Python conforme é documentado nos [dados de Blobs do Azure de processo dados ciência ambiente](machine-learning-data-science-process-data-blob.md). Os dados tem de ser carregados a partir da base de dados numa moldura pandas dados e, em seguida, podem ser processados ainda mais. Se documentados o processo de ligar à base de dados e carregar os dados para a moldura de dados nesta secção.

O formato da cadeia de ligação seguinte pode ser utilizado para ligar a uma base de dados do SQL Server de Python utilizando pyodbc (nomedoservidor substituir, dbname, nome de utilizador e palavra-passe com o seu valores específicos):

    #Set up the SQL Azure connection
    import pyodbc
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

A [biblioteca de Pandas](http://pandas.pydata.org/) no Python fornece um conjunto avançado de estruturas de dados e ferramentas de análise de dados para manipulação de dados para a programação de Python. O código abaixo lê os resultados devolvidos a partir de uma base de dados do SQL Server numa moldura Pandas dados:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Agora pode trabalhar com a moldura de dados Pandas como descrito em tópicos [criar funcionalidades para dados de armazenamento de Blobs do Azure utilizando Panda](machine-learning-data-science-create-features-blob.md).
