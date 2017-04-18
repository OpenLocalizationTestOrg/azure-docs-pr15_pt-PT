<properties
    pageTitle="Se ligar à base de dados SQL utilizando Python | Microsoft Azure"
    description="Apresenta um exemplo de código Python que pode utilizar para ligar à base de dados do SQL Azure."
    services="sql-database"
    documentationCenter=""
    authors="meet-bhagdev"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="drivers"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="meetb"/>


# <a name="connect-to-sql-database-by-using-python"></a>Se ligar à base de dados SQL utilizando Python


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)] 


Este tópico mostra como ligar e uma base de dados do SQL Azure utilizando Python da consulta. Pode executar este exemplo a partir do Windows, Ubuntu Linux ou Mac plataformas.


## <a name="step-1-create-a-sql-database"></a>Passo 1: Criar uma base de dados SQL

Ver a [página Introdução](sql-database-get-started.md) para saber como criar uma base de dados de exemplo.  É importante que segue o guia para criar um **modelo de base de dados AdventureWorks**. Os exemplos mostrados abaixo só funcionam com o **esquema de AdventureWorks**. Depois de criar efetuada da base de dados-se de que ativar o acesso ao seu endereço de IP ao ativar as regras de firewall, conforme descrito na [página Introdução](sql-database-get-started.md)

## <a name="step-2-configure-development-environment"></a>Passo 2: Configurar o ambiente de desenvolvimento

### <a name="mac-os"></a>**Mac OS**   
### <a name="install-the-required-modules"></a>Instalar os módulos necessários
Abra o terminal e instale

    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
    brew install FreeTDS
    sudo -H pip install pymssql=2.1.1

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**

Abra o terminal e navegue para um diretório onde tenciona sobre como criar o seu python script. Introduza os seguintes comandos para instalar o **FreeTDS** e **pymssql**. pymssql utiliza FreeTDS para ligar a bases de dados SQL.

    sudo apt-get --assume-yes update
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    sudo apt-get --assume-yes install python-dev python-pip
    sudo pip install pymssql=2.1.1
    
### <a name="windows"></a>**Windows**

Instale pymssql a partir de [**aqui**](http://www.lfd.uci.edu/~gohlke/pythonlibs/#pymssql). 

Certifique-se de que escolha o ficheiro de whl correto. Por exemplo: escolher se estiver a utilizar Python 2.7 num computador de 64 bits: pymssql‑2.1.1‑cp27‑none‑win_amd64.whl. Depois de transferir o ficheiro .whl colocá-la na pasta c: / Python27.

Instale agora o controlador de pymssql utilizando pip da linha de comandos. CD para c: / Python27 e execute o seguinte
    
    pip install pymssql‑2.1.1‑cp27‑none‑win_amd64.whl

Instruções para ativar o pip de utilização podem ser encontradas [aqui](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows)

## <a name="step-3-run-sample-code"></a>Passo 3: Executar o código de exemplo

Criar um ficheiro denominado **sql_sample.py** e cole o código seguinte dentro da mesma. Pode executar esta a partir da linha de comandos utilizando:
    
    python sql_sample.py

### <a name="connect-to-your-sql-database"></a>Ligar a sua base de dados SQL

A função [pymssql.connect](http://pymssql.org/en/latest/ref/pymssql.html) é utilizada para ligar à base de dados SQL.

    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')


### <a name="execute-an-sql-select-statement"></a>Executar uma instrução SELECT de SQL

A função de [cursor.execute](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.execute) pode ser utilizada para obter um conjunto de resultados de uma consulta SQL na base de dados. Esta função essencialmente aceita qualquer consulta e devolve que um conjunto de resultados que pode ser sujeita a iteração sobre com a utilização de [cursor.fetchone()](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.fetchone).


    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute('SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;')
    row = cursor.fetchone()
    while row:
        print str(row[0]) + " " + str(row[1]) + " " + str(row[2])   
        row = cursor.fetchone()


### <a name="insert-a-row-pass-parameters-and-retrieve-the-generated-primary-key"></a>Inserir uma linha, passar parâmetros e obter a chave primária gerada

Base de dados SQL a propriedade de [identidade](https://msdn.microsoft.com/library/ms186775.aspx) e o objeto de [sequência](https://msdn.microsoft.com/library/ff878058.aspx) podem ser utilizados para gerar automaticamente os valores de [chave primária](https://msdn.microsoft.com/library/ms179610.aspx) . 


    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)")
    row = cursor.fetchone()
    while row:
        print "Inserted Product ID : " +str(row[0])
        row = cursor.fetchone()


### <a name="transactions"></a>Transações


Este exemplo de código demonstra a utilização de transações na qual pode:

* Começar uma transação
* Inserir uma linha de dados
* Anular a transação para anular a inserir 

Cole o código seguinte dentro sql_sample.py.
    
    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute("BEGIN TRANSACTION")
    cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, CURRENT_TIMESTAMP)")
    cnxn.rollback()

## <a name="next-steps"></a>Próximos passos

* Rever a [Descrição geral do desenvolvimento de base de dados SQL](sql-database-develop-overview.md)
* Obter mais informações sobre o [Microsoft Python controlador para SQL Server](https://msdn.microsoft.com/library/mt652092.aspx)
* Visite o [Centro de programadores do Python](/develop/python/).

## <a name="additional-resources"></a>Recursos adicionais 

* [Padrões de design para aplicações do inquilino com várias SaaS com base de dados Azure SQL](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Explorar todas as [capacidades da base de dados SQL](https://azure.microsoft.com/services/sql-database/)
