<properties 
    pageTitle="Exemplo de dados do SQL Server no Azure | Microsoft Azure" 
    description="Dados de exemplo no SQL Server no Azure" 
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
    ms.author="fashah;garye;bradsev" /> 

#<a name="heading"></a>Dados de exemplo no SQL Server no Azure


Este documento mostra como exemplo dados armazenados no SQL Server no Azure SQL ou a linguagem de programação Python a utilizar. Também mostra como mover os dados recolhidos para Azure máquina formação ao guardá-la para um ficheiro, carregá-lo para um BLOBs do Azure e, em seguida, a lê-la para o Azure máquina aprendizagem Studio.

A amostragem Python utiliza a biblioteca ODBC [pyodbc](https://code.google.com/p/pyodbc/) para ligar para o SQL Server no Azure e a biblioteca de [Pandas](http://pandas.pydata.org/) para efetuar a amostragem.

>[AZURE.NOTE] O código SQL de exemplo neste documento assume que os dados são no SQL Server no Azure. Se não for, consulte o tópico de [mover os dados para o SQL Server no Azure](machine-learning-data-science-move-sql-server-virtual-machine.md) para obter instruções sobre como mover os seus dados para o SQL Server no Azure.

**Porque é que os seus dados de exemplo?**
Se o conjunto de dados que pretende analisar for grande, normalmente é uma boa ideia para baixo amostras os dados a reduzi-lo para um tamanho mais pequeno, mas representativo e mais viável. Isto facilita a compreensão de dados, informações detalhadas e engenharia de funcionalidade. Seu papel no [Processo de ciências de dados de equipa (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) é protótipos rápido das funções de processamento de dados e modelos de aprendizagem automática.

No **menu** abaixo ligações para tópicos que descrevem como dados a partir de vários ambientes de armazenamento de exemplo. 

[AZURE.INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

Esta tarefa amostragem é um passo o [Processo de ciências de dados de equipa (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

##<a name="SQL"></a>Utilizar o SQL

Esta secção descreve vários métodos utilizando SQL para efetuar uma amostragem aleatória simples contra os dados na base de dados. Escolha um método baseado no seu tamanho de dados e a sua distribuição.

Dois itens mostram como utilizar newid no SQL Server para executar a amostragem. O método que escolher depende aleatório como pretende que a amostra de ser (pk_id no código de exemplo abaixo é considerada uma chave primária gerados automaticamente).

1. Amostra aleatória menos estrita

        select  * from <table_name> where <primary_key> in 
        (select top 10 percent <primary_key> from <table_name> order by newid())

2. Exemplo mais aleatório 

        SELECT * FROM <table_name>
        WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)

Tablesample pode ser utilizado para amostragem, bem como demonstrado abaixo. Isto pode ser uma abordagem melhor se o tamanho de dados for grande (partindo do princípio que dados em páginas diferentes não relacionados) e para a consulta concluir num período de tempo razoável.

    SELECT *
    FROM <table_name> 
    TABLESAMPLE (10 PERCENT)

>[AZURE.NOTE] Pode explorar e gerar funcionalidades a partir destes dados de amostra guardando-o numa nova tabela


###<a name="sql-aml"></a>Ligar a aprendizagem máquina Azure

Pode utilizar as consultas de exemplo acima diretamente na formação de máquina Azure [Importar dados] [ import-data] módulo para os dados no momento de amostras para baixo e colocá-lo para uma experiência de aprendizagem do Azure máquina. Uma captura de ecrã de utilizar o módulo do leitor para ler os dados recolhidos é mostrada abaixo:
   
![leitor sql][1]

##<a name="python"></a>Utilizar Python linguagem de programação 

Esta secção demonstra utilizando a [biblioteca de pyodbc](https://code.google.com/p/pyodbc/) para estabelecer uma ODBC ligar a uma base de dados do SQL server no Python. A cadeia de ligação de base de dados é da seguinte forma: (substitua nomedoservidor, dbname, nome de utilizador e palavra-passe com a sua configuração):

    #Set up the SQL Azure connection
    import pyodbc   
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

Biblioteca de [Pandas](http://pandas.pydata.org/) no Python fornece um conjunto avançado de estruturas de dados e ferramentas de análise de dados para manipulação de dados para a programação de Python. O código abaixo lê uma amostra de 0,1% dos dados a partir de uma tabela numa base de dados Azure SQL um dados Pandas:

    import pandas as pd

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select column1, cloumn2... from <table_name> tablesample (0.1 percent)''', conn)

Agora pode trabalhar com os dados recolhidos na moldura Pandas dados. 

###<a name="python-aml"></a>Ligar a aprendizagem máquina Azure

Pode utilizar o seguinte código de exemplo para guardar os dados recolhidos para baixo para um ficheiro e carregue-o para uma BLOBs do Azure. Os dados no blob podem ser diretamente lidos para uma experiência de aprendizagem do Azure máquina com os [Dados de importação] [ import-data] módulo. Os passos são da seguinte forma: 

1. Escrever a moldura de dados pandas para um ficheiro local

        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Carregar ficheiro local para BLOBs do Azure

        from azure.storage import BlobService
        import tables

        STORAGEACCOUNTNAME= <storage_account_name>
        LOCALFILENAME= <local_file_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>

        output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
        localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
        
        try:
       
        #perform upload
        output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
        
        except:         
            print ("Something went wrong with uploading blob:"+BLOBNAME)

3. Ler os dados a partir de Blobs do Azure utilizando Azure máquina aprendizagem [Importar dados] [ import-data] módulo, conforme apresentado nas funcionalidades de ecrã abaixo:
 
![blob leitor][2]

## <a name="the-team-data-science-process-in-action-example"></a>O processo de ciências de dados de equipa no exemplo de ação

Para obter um exemplo de ponto a ponto instruções passo a passo do processo de ciências de dados de equipa um utilizando um conjunto de dados público, consulte o artigo [processo de ciências de dados de equipa em ação: utilizar o SQL Server](machine-learning-data-science-process-sql-walkthrough.md).

[1]: ./media/machine-learning-data-science-sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/machine-learning-data-science-sample-sql-server-virtual-machine/reader_blob.png

 [import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
