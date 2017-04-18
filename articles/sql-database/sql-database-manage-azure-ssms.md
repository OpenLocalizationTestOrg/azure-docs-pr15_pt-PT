<properties 
    pageTitle="Gerir uma base de dados SQL com SSMS | Microsoft Azure" 
    description="Saiba como utilizar o SQL Server Management Studio para gerir os servidores de base de dados SQL e bases de dados." 
    services="sql-database" 
    documentationCenter=".net" 
    authors="stevestein" 
    manager="jhubbard" 
    editor="tysonn"/>

<tags 
    ms.service="sql-database" 
    ms.workload="data-management" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/29/2016" 
    ms.author="sstein"/>


# <a name="managing-azure-sql-database-using-sql-server-management-studio"></a>Gerir a base de dados do SQL Azure com o SQL Server Management Studio 


> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-manage-portal.md)
- [SSMS](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-manage-powershell.md)

Pode utilizar o SQL Server Management Studio (SSMS) para administrar os servidores de base de dados do Azure SQL e bases de dados. Este tópico irá guiar tarefas comuns com SSMS. Já deve ter um servidor e a base de dados criada na base de dados do SQL Azure antes de começar. Para mais informações, consulte [criar sua primeira base de dados do Azure SQL](sql-database-get-started.md) e [ligar e consulta utilizando SSMS](sql-database-connect-query-ssms.md) .

É recomendável que utilize a versão mais recente do SSMS sempre que trabalha com a base de dados do SQL Azure. 

> [AZURE.IMPORTANT] Utilize sempre a versão mais recente do SSMS porque continuamente melhorada para trabalhar com as atualizações mais recentes para Azure e base de dados SQL. Para obter a versão mais recente, consulte o artigo [Transferir o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).



## <a name="create-and-manage-azure-sql-databases"></a>Criar e gerir bases de dados Azure SQL

Enquanto estiver ligado a base de dados **principal** , pode criar bases de dados no servidor e modificar ou largar bases de dados existentes. Os passos seguintes descrevem como realizar várias tarefas comuns de base de dados gestão através de Management Studio. Para efetuar estas tarefas, certifique-se de que está ligadas à base de dados **principal** com login principal ao nível do servidor que criou quando configurar o seu servidor.

Para abrir uma janela de consulta no Management Studio, abra a pasta de bases de dados, expanda a pasta de **Bases de dados do sistema** , com o botão direito no **modelo global**e, em seguida, clique em **Nova consulta**.

-   Utilize a instrução de **Criar a base de dados** para criar uma base de dados. Para mais informações, consulte o artigo [Criar base de dados (base de dados SQL)](https://msdn.microsoft.com/library/dn268335.aspx). A seguinte instrução cria uma base de dados denominada **myTestDB** e especifica que se trata de uma base de dados Standard S0 Edition com um tamanho máximo predefinido de 250 GB.

        CREATE DATABASE myTestDB
        (EDITION='Standard',
         SERVICE_OBJECTIVE='S0');

Clique em **Executar** para executar a consulta.

-   Utilize a instrução **ALTER DATABASE** para modificar uma base de dados existente, por exemplo, se pretender alterar o nome e a edição da base de dados. Para mais informações, consulte o artigo [Alterar da base de dados (base de dados SQL)](https://msdn.microsoft.com/library/ms174269.aspx). A seguinte instrução modifica a base de dados que criou no passo anterior para alterar edition para S1 padrão.

        ALTER DATABASE myTestDB
        MODIFY
        (SERVICE_OBJECTIVE='S1');

-   Utilize **A base de dados LARGAR** declaração para eliminar uma base de dados existente. Para mais informações, consulte o artigo [Remover base de dados (base de dados SQL)](https://msdn.microsoft.com/library/ms178613.aspx). A seguinte instrução elimina a base de dados **myTestDB** , mas não largue-o agora uma vez que o irá utilizar para criar os inícios de sessão no próximo passo.

        DROP DATABASE myTestBase;

-   Base de dados principal tem na vista de **sys.databases** que pode utilizar para ver os detalhes sobre todas as bases de dados. Para ver todas as bases de dados existentes, execute a seguinte instrução:

        SELECT * FROM sys.databases;

-   Base de dados SQL, a declaração de **utilização** não é suportada para alternar entre bases de dados. Em vez disso, tem de estabelecer uma ligação diretamente para a base de dados de destino.

>[AZURE.NOTE] Muitos das instruções do Transact-SQL que criar ou modificar uma base de dados tem de ser executados dentro dos seus próprios lote e não podem ser agrupados com outros demonstrações Transact-SQL. Para obter mais informações, consulte as informações específicas de declaração.

## <a name="create-and-manage-logins"></a>Criar e gerir os inícios de sessão

Base de dados **principal** contém inícios de sessão e os inícios de sessão de tem permissão para criar bases de dados ou outros inícios de sessão. Geri os inícios de sessão ao ligar à base de dados **principal** com login principal ao nível do servidor que criou quando configurar o seu servidor. Pode utilizar as instruções de **Início de sessão de criar**, **Alterar início de sessão**ou **LARGAR início de sessão** para executar consultas de base de dados principal que faz a gestão dos inícios de sessão em todo o servidor. Para mais informações, consulte o artigo [Gerir bases de dados e inícios de sessão de base de dados SQL](http://msdn.microsoft.com/library/azure/ee336235.aspx). 


-   Utilize a instrução de **Início de sessão criar** para criar um início de sessão ao nível do servidor. Para mais informações, consulte o artigo [Criar início de sessão (base de dados SQL)](https://msdn.microsoft.com/library/ms189751.aspx). A seguinte instrução cria um início de sessão denominado **login1**. Substitua a **Palavra-passe1** com a palavra-passe da sua escolha.

        CREATE LOGIN login1 WITH password='password1';

-   Utilize a instrução **CREATE USER** para conceder permissões ao nível da base de dados. Todos os inícios de sessão tem de ser criados na base de dados **principal** . Para um início de sessão ligar a outra base de dados, tem de conceder-lhe permissões ao nível da base de dados com a instrução **CREATE USER** nessa base de dados. Para mais informações, consulte o artigo [Criar utilizador (base de dados SQL)](https://msdn.microsoft.com/library/ms173463.aspx). 

-   Para atribuir permissões de login1 para uma base de dados denominada **myTestDB**, conclua os passos seguintes:

 1.  Para atualizar o Explorador do objeto para visualizar a base de dados **myTestDB** que criou, com o botão direito no nome do servidor no Explorador do objeto e, em seguida, clique em **Atualizar**.  

     Se tiver fechado a ligação, pode voltar a ligar ao selecionar **Ligar Explorer objeto** no menu ficheiro.

 2. Botão direito do rato **myTestDB** base de dados e selecione **Nova consulta**.

    3.  Execute a seguinte instrução myTestDB na base de dados para criar um utilizador de base de dados denominado **login1User** que corresponde ao nível de servidor de início de sessão **login1**.

            CREATE USER login1User FROM LOGIN login1;

-   Utilizar o **sp\_addrolemember** procedimento para atribuir a conta de utilizador no nível de permissões da base de dados adequado armazenado. Para mais informações, consulte o artigo [sp_addrolemember (Transact-SQL)](http://msdn.microsoft.com/library/ms187750.aspx). A seguinte instrução dá **login1User** permissões só de leitura para a base de dados ao adicionar **login1User** para o **db\_datareader** função.

        exec sp_addrolemember 'db_datareader', 'login1User';    

-   Utilize a instrução **ALTER início de sessão** para modificar um início de sessão existente, por exemplo, se pretender alterar a palavra-passe para o início de sessão. Para mais informações, consulte o artigo [Alterar início de sessão (base de dados SQL)](https://msdn.microsoft.com/library/ms189828.aspx). A instrução **ALTER LOGIN** deve ser executada contra a base de dados **principal** . Mude para a janela de consulta que está ligada à base de dados. A seguinte instrução modifica login **login1** para repor a palavra-passe. Substitua **newPassword** a palavra-passe da sua escolha e **antiga** com a palavra-passe atual para o início de sessão.

        ALTER LOGIN login1
        WITH PASSWORD = 'newPassword'
        OLD_PASSWORD = 'oldPassword';

-   Utilize **O início de sessão LARGAR** declaração para eliminar um início de sessão existente. Eliminar um início de sessão ao nível do servidor também elimina quaisquer contas de utilizador de base de dados associada. Para mais informações, consulte o artigo [Remover base de dados (base de dados SQL)](https://msdn.microsoft.com/library/ms178613.aspx). A declaração de **Início de sessão LARGAR** deve ser executada contra a base de dados **principal** . A instrução elimina **login1** login.

        DROP LOGIN login1;

-   Base de dados principal tenha o **sys.sql\_inícios de sessão** ver que pode utilizar para ver os inícios de sessão. Para ver todos os inícios de sessão existentes, execute a seguinte instrução:

        SELECT * FROM sys.sql_logins;

## <a name="monitor-sql-database-using-dynamic-management-views"></a>Monitorizar a base de dados SQL utilizar vistas dinâmicas de gestão

Base de dados SQL suporta várias vistas de gestão de dinâmica que pode utilizar para monitorizar a uma base de dados individual. Alguns exemplos do tipo de dados do monitor a poderá obter através destas vistas estão a seguir. Para detalhes completos e mais exemplos de utilização, consulte a [monitorização de base de dados do SQL utilizar vistas dinâmicas de gestão](https://msdn.microsoft.com/library/azure/ff394114.aspx).

-   Consultar uma vista de gestão de dinâmica requer permissões de **Estado da base de dados de vista** . Para conceder permissão ao **Estado da base de dados vista** a um utilizador de base de dados específica, ligar à base de dados e execute a seguinte instrução contra a base de dados:

        GRANT VIEW DATABASE STATE TO login1User;

-   Calcular o tamanho da base de dados utilizando o **sys.dm\_db\_partição\_estatística** vista. O **sys.dm\_db\_partição\_estatística** vista devolve informações de página e a contagem de linhas para todas as partições da base de dados, pode utilizar para calcular o tamanho da base de dados. A seguinte consulta devolve o tamanho da base de dados em megabytes:

        SELECT SUM(reserved_page_count)*8.0/1024
        FROM sys.dm_db_partition_stats;   

-   Utilizar o **sys.dm\_execução\_ligações** e **sys.dm\_execução\_sessões** vistas para obter informações sobre ligações do utilizador atual e tarefas internas associadas a base de dados. A seguinte consulta devolve informações sobre a ligação atual:

        SELECT
            e.connection_id,
            s.session_id,
            s.login_name,
            s.last_request_end_time,
            s.cpu_time
        FROM
            sys.dm_exec_sessions s
            INNER JOIN sys.dm_exec_connections e
              ON s.session_id = e.session_id;

-   Utilizar o **sys.dm\_execução\_consulta\_estatística** vista para obter as estatísticas de desempenho de agregação para em cache planos de consulta. A seguinte consulta devolve informações sobre as principais cinco consultas classificados ao tempo médio de CPU.

        SELECT TOP 5 query_stats.query_hash AS "Query Hash",
            SUM(query_stats.total_worker_time), SUM(query_stats.execution_count) AS "Avg CPU Time",
            MIN(query_stats.statement_text) AS "Statement Text"
        FROM
            (SELECT QS.*,
            SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
            ((CASE statement_end_offset
                WHEN -1 THEN DATALENGTH(ST.text)
                ELSE QS.statement_end_offset END
                    - QS.statement_start_offset)/2) + 1) AS statement_text
             FROM sys.dm_exec_query_stats AS QS
             CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
        GROUP BY query_stats.query_hash
        ORDER BY 2 DESC;
 
 
