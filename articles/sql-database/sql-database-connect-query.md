<properties
    pageTitle="Ligar à base de dados SQL com uma consulta c# | Microsoft Azure"
    description="Escreva um programa no c# para da consulta e ligar à base de dados SQL. Informações sobre os endereços IP, as cadeias de ligação, início de sessão seguro e Visual Studio gratuito."
    services="sql-database"
    keywords="c# consulta de base de dados, c# consulta, ligar-se a base de dados, C de SQL#"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="08/17/2016"
    ms.author="stevestein"/>



# <a name="connect-to-a-sql-database-with-visual-studio"></a>Ligar a uma base de dados SQL com o Visual Studio

> [AZURE.SELECTOR]
- [Visual Studio](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Saiba como ligar a uma base de dados do Azure SQL no Visual Studio. 

## <a name="prerequisites"></a>Pré-requisitos


Para ligar a uma base de dados do SQL utilizando o Visual Studio, precisa do seguinte: 


- Base de dados SQL para ligar à. Este artigo utiliza a base de dados de exemplo **AdventureWorks** . Para obter a base de dados de exemplo AdventureWorks, consulte o artigo [criar a base de dados de demonstração](sql-database-get-started.md).


- Atualização de 2013 Studio Visual 4 (ou posterior). A Microsoft fornece agora Visual Studio Comunidade *gratuitamente*.
 - [Comunidade do Visual Studio, transferência](http://www.visualstudio.com/products/visual-studio-community-vs)
 - [Mais opções para libertar Visual Studio](http://www.visualstudio.com/products/free-developer-offers-vs.aspx)




## <a name="open-visual-studio-from-the-azure-portal"></a>Abrir o Visual Studio a partir do portal do Azure


1. Inicie a sessão [portal do Azure](https://portal.azure.com/).

2. Clique em **Mais serviços** > **bases de dados SQL**
3. Abra o pá de base de dados **AdventureWorks** localizar e clicando em base de dados *AdventureWorks* .

6. Clique no botão **Ferramentas** no topo da pá a base de dados:

    ![Nova consulta. Ligar ao servidor de base de dados SQL: SQL Server Management Studio](./media/sql-database-connect-query/tools.png)

7. Clique em **Abrir no Visual Studio** (se precisar de Visual Studio, clique na ligação de transferência):

    ![Nova consulta. Ligar ao servidor de base de dados SQL: SQL Server Management Studio](./media/sql-database-connect-query/open-in-vs.png)


8. Visual Studio abre-se com a janela de **ligar ao servidor** já definida para ligar ao servidor e base de dados seleccionada no portal.  (Clique em **Opções** para verificar que a ligação está definida para a base de dados correto.) Escreva a palavra-passe de administrador do servidor e clique em **Ligar**.


    ![Nova consulta. Ligar ao servidor de base de dados SQL: SQL Server Management Studio](./media/sql-database-connect-query/connect.png)


8. Se não tiver um conjunto de regras de firewall para o endereço IP do seu computador, recebe uma mensagem *não é possível ligar* aqui. Para criar uma regra de firewall, consulte o artigo [Configurar uma regra da firewall ao nível do servidor de base de dados do SQL Azure](sql-database-configure-firewall-settings.md).


9. Depois de ligar com êxito, é aberta a janela do **Explorador de objeto do SQL Server** com uma ligação à sua base de dados.

    ![Nova consulta. Ligar ao servidor de base de dados SQL: SQL Server Management Studio](./media/sql-database-connect-query/sql-server-object-explorer.png)


## <a name="run-a-sample-query"></a>Executar uma consulta de exemplo

Agora que recomendamos estiver ligados à base de dados, os seguintes passos mostram como executar uma consulta simples:

2. Com o botão direito na base de dados e, em seguida, selecione **Nova consulta**.

    ![Nova consulta. Ligar ao servidor de base de dados SQL: SQL Server Management Studio](./media/sql-database-connect-query/new-query.png)

3. Na janela da consulta, copie e cole o código seguinte.

        SELECT
        CustomerId
        ,Title
        ,FirstName
        ,LastName
        ,CompanyName
        FROM SalesLT.Customer;

4. Clique no botão **Executar** para executar a consulta:

    ![Sucesso. Ligar ao servidor de base de dados SQL: SVisual Studio](./media/sql-database-connect-query/run-query.png)

## <a name="next-steps"></a>Próximos passos

- Abrir bases de dados SQL no Visual Studio utiliza as ferramentas de dados do SQL Server. Para obter mais detalhes, consulte [As ferramentas de dados do SQL Server](https://msdn.microsoft.com/library/hh272686.aspx).
- Para ligar a uma base de dados do SQL utilizando código, consulte o artigo [ligar a base de dados do SQL utilizando o .NET (c#)](sql-database-develop-dotnet-simple.md).



