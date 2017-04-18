<properties
    pageTitle="Criar e gerir dimensionada saída bases de dados do SQL Azure com tarefas flexível | Micosoft Azure"
    description="Guiá-lo através da criação e gestão de uma tarefa de base de dados flexível."
    services="sql-database"
    documentationCenter=""
    manager="jhubbard"
    authors="ddove"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/27/2016"
    ms.author="ddove"/>

# <a name="create-and-manage-scaled-out-azure-sql-databases-using-elastic-jobs-preview"></a>Criar e gerir dimensionadas saída bases de dados do SQL Azure com tarefas flexível (pré-visualização)

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-elastic-jobs-create-and-manage.md)
- [PowerShell](sql-database-elastic-jobs-powershell.md)


**Tarefas de base de dados flexível** simplificar a gestão de grupos de bases de dados através da execução de operações administrativas, tais como alterações de esquema, gestão de credenciais, atualizações de dados de referência, recolha de dados de desempenho ou da coleção de telemetria do inquilino (cliente). Tarefas de base de dados flexível está atualmente disponível através do portal do Azure e os cmdlets do PowerShell. No entanto, as superfícies portais Azure limitada a execução através de todas as bases de dados num [conjunto de base de dados flexível (pré-visualização)](sql-database-elastic-pool.md)de funcionalidade reduzida. Para aceder às funcionalidades adicionais e execução dos scripts através de um grupo de bases de dados incluindo uma coleção de definidos pelo personalizada ou um conjunto de shard (criada utilizando [a biblioteca do cliente da base de dados flexível](sql-database-elastic-scale-introduction.md)), consulte [criar e gerir tarefas através do PowerShell](sql-database-elastic-jobs-powershell.md). Para mais informações acerca de tarefas, consulte o artigo [Descrição geral de tarefas da base de dados flexível](sql-database-elastic-jobs-overview.md). 

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Para uma avaliação gratuita, consulte o artigo [versão de avaliação gratuita um mês](https://azure.microsoft.com/pricing/free-trial/).
* Um agrupamento de base de dados flexível. Consulte o artigo [sobre flexível agrupamentos de base de dados](sql-database-elastic-pool.md)
* Instalação de componentes de serviço de tarefa de base de dados flexível. Consulte o artigo [instalar o serviço de tarefa flexível da base de dados](sql-database-elastic-jobs-service-installation.md).

## <a name="creating-jobs"></a>Criar tarefas

1. Utilizando o [Azure portal](https://portal.azure.com), a partir de um conjunto de tarefa flexível da base de dados existente, clique em **tarefa de criar**.
2. Escrever no nome de utilizador e palavra-passe do administrador da base de dados (criado durante a instalação das tarefas) para a base de dados de controlo de tarefas (armazenamento de metadados para tarefas).

    ![Nome da tarefa, escreva ou cole código e clique em executar][1]
2. Na pá **Tarefa de criar** , escreva um nome para a tarefa.
3. Escreva o nome de utilizador e palavra-passe para ligar para as bases de dados de destino com permissões suficientes para execução de script ser concluída com êxito.
4. Colar ou escreva no script T-SQL.
5. Clique em **Guardar** e, em seguida, clique em **Executar**.

    ![Criar tarefas e executar][5]

## <a name="run-idempotent-jobs"></a>Executar tarefas idempotent

Quando executar um script contra um conjunto de bases de dados, tem de ser-se de que o script é idempotent. Isto é, o script tem de conseguir executar várias vezes, mesmo se falhou antes num estado incompleto. Por exemplo, quando um script falha, a tarefa será automaticamente repetida até ter êxito (dentro dos limites, como a repetir lógica eventualmente deixará da repetir). A forma de fazer é utilizar a uma cláusula "Se existe" e eliminar todas as ocorrências encontradas antes de criar um novo objeto. Um exemplo é mostrado aqui:

    IF EXISTS (SELECT name FROM sys.indexes
            WHERE name = N'IX_ProductVendor_VendorID')
    DROP INDEX IX_ProductVendor_VendorID ON Purchasing.ProductVendor;
    GO
    CREATE INDEX IX_ProductVendor_VendorID
    ON Purchasing.ProductVendor (VendorID);

Em alternativa, utilize uma cláusula "Se não existe" antes de criar uma nova instância:

    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
     CREATE TABLE TestTable(
      TestTableId INT PRIMARY KEY IDENTITY,
      InsertionTime DATETIME2
     );
    END
    GO

    INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
    GO

Este script, em seguida, atualiza a tabela criada anteriormente.

    IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
    BEGIN

    ALTER TABLE TestTable

    ADD AdditionalInformation NVARCHAR(400);
    END
    GO

    INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
    GO


## <a name="checking-job-status"></a>Verificar o estado de tarefa

Depois de estas terem começado uma tarefa, pode verificar no seu progresso.

1. Na página de agrupamento flexível da base de dados, clique em **Gerir projectos**.

    ![Clique em "Gerir projectos"][2]

2. Clique no nome do (um) de uma tarefa. O **Estado** pode ser "Concluído" ou "Não foi possível". Detalhes do projecto aparecem (b) com a sua data e hora de criação e execução. A lista (c) abaixo o que mostra o progresso do script de cada base de dados no conjunto, que lhe dá uma respectivos detalhes de data e hora.

    ![Verificar uma tarefa concluída][3]


## <a name="checking-failed-jobs"></a>Verificar Ocorreu uma falha de tarefas

Se uma tarefa falhar, pode encontrar um registo das sua execução. Clique no nome da tarefa falha para ver os seus detalhes.

![Verificar uma falha na tarefa][4]


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-create-and-manage/screen-1.png
[2]: ./media/sql-database-elastic-jobs-create-and-manage/click-manage-jobs.png
[3]: ./media/sql-database-elastic-jobs-create-and-manage/running-jobs.png
[4]: ./media/sql-database-elastic-jobs-create-and-manage/failed.png
[5]: ./media/sql-database-elastic-jobs-create-and-manage/screen-2.png

 
