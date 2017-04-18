<properties
   pageTitle="Consulta armazém de dados do Azure SQL (Visual Studio) | Microsoft Azure"
   description="Consulta SQL armazém de dados com o Visual Studio."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/16/2016"
   ms.author="sonyama;barbkess"/>

# <a name="query-azure-sql-data-warehouse-visual-studio"></a>Consulta Azure SQL dados armazém (Visual Studio)

> [AZURE.SELECTOR]
- [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Formação de máquina Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [SQLCMD](sql-data-warehouse-get-started-connect-sqlcmd.md) 

Utilize o Visual Studio para consultar armazém de dados do SQL Azure em apenas alguns minutos. Este método utiliza a extensão do SQL Server dados Tools (SSDT) no Visual Studio. 

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar este tutorial, é necessário:

+ Um armazém de dados SQL existente. Para criar uma, consulte o artigo [criar um armazém de dados SQL][].
+ SSDT para Visual Studio. Se tiver o Visual Studio, provavelmente já tem esta. Para instruções de instalação e opções, consulte o artigo [instalar o Visual Studio e SSDT][].
+ O nome do servidor SQL completamente qualificado. Para localizar isto, consulte o artigo [ligar ao armazenamento de dados SQL][].

## <a name="1-connect-to-your-sql-data-warehouse"></a>1. Ligue para o seu armazém de dados SQL

1. Abra o Visual Studio 2013 ou 2015.
2. Abra o Explorador de objeto do SQL Server. Para fazer isto, selecione **vista** > **Explorer de objeto do SQL Server**.

    ![Explorador de objeto do SQL Server][1]

3. Clique no ícone de **Adicionar o SQL Server** .

    ![Adicionar do SQL Server][2]

4. Preencha os campos na ligar à janela do servidor.

    ![Ligar ao servidor][3]

    - **Nome do servidor**. Introduza o **nome do servidor** anteriormente identificados.
    - **Autenticação**. Selecione **autenticação do SQL Server** ou **a autenticação integrada do Active Directory**.
    - **Nome de utilizador** e **palavra-passe**. Introduza o nome de utilizador e palavra-passe se autenticação do SQL Server foi seleccionada acima.
    - Clique em **Ligar**.

5. Para explorar, expanda o seu servidor Azure SQL. Pode ver as bases de dados associados ao servidor. Expanda AdventureWorksDW para ver as tabelas na sua base de dados de exemplo.

    ![Explorar AdventureWorksDW][4]

## <a name="2-run-a-sample-query"></a>2. a executar uma consulta de exemplo

Agora que tenha sido estabelecer uma ligação à base de dados, vamos escrever uma consulta.

1. Botão direito do rato a base de dados no Explorador de objeto do SQL Server.

2. Selecione **Nova consulta**. É aberta uma nova janela de consulta.

    ![Nova consulta][5]

3. Copie esta consulta TSQL para a janela da consulta:

    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```

4. Execute a consulta. Para executar esta tarefa, clique na seta verde ou utilize o seguinte atalho: `CTRL` + `SHIFT` + `E`.

    ![Executar consulta][6]

5. Veja os resultados da consulta. Neste exemplo, a tabela de FactInternetSales tem 60398 linhas.

    ![Resultados da consulta][7]

## <a name="next-steps"></a>Próximos passos

Agora que pode ligar e de consulta, experimente [visualizar os dados com obter][].

Para configurar o seu ambiente para autenticação do Azure Active Directory, consulte o artigo [autenticar para armazém de dados do SQL][].

<!--Arcticles-->
[Ligar ao armazenamento de dados SQL]: sql-data-warehouse-connect-overview.md
[Criar um armazém de dados SQL]: sql-data-warehouse-get-started-provision.md
[Instalar o Visual Studio e SSDT]: sql-data-warehouse-install-visual-studio.md
[Autenticar armazém de dados SQL]: sql-data-warehouse-authentication.md
[visualizar os dados com obter]: sql-data-warehouse-get-started-visualize-with-power-bi.md  

<!--Other-->
[Azure portal]: https://portal.azure.com

<!--Image references-->

[1]: media/sql-data-warehouse-query-visual-studio/open-ssdt.png
[2]: media/sql-data-warehouse-query-visual-studio/add-server.png
[3]: media/sql-data-warehouse-query-visual-studio/connection-dialog.png
[4]: media/sql-data-warehouse-query-visual-studio/explore-sample.png
[5]: media/sql-data-warehouse-query-visual-studio/new-query2.png
[6]: media/sql-data-warehouse-query-visual-studio/run-query.png
[7]: media/sql-data-warehouse-query-visual-studio/query-results.png
