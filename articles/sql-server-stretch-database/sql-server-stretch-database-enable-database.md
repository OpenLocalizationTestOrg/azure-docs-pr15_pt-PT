<properties
    pageTitle="Ativar opções esticar base de dados para uma base de dados | Microsoft Azure"
    description="Saiba como configurar uma base de dados para esticar base de dados."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/05/2016"
    ms.author="douglasl"/>

# <a name="enable-stretch-database-for-a-database"></a>Ativar opções esticar base de dados para uma base de dados

Para configurar uma base de dados existente para esticar base de dados, selecione **tarefas | Esticar | Ativar** para uma base de dados SQL Server Management Studio para abrir o Assistente de **Ativar a base de dados para esticar** . Também pode utilizar Transact\-SQL para activar esticar base de dados para uma base de dados.

Se selecionar **tarefas | Esticar | Ativar** para uma tabela individual e ainda não tiver activado a base de dados para esticar base de dados, o assistente configura a base de dados para esticar base de dados e permite-lhe selecionar tabelas como parte do processo. Siga os passos neste tópico em vez dos passos em [Ativar esticar base de dados para uma tabela](sql-server-stretch-database-enable-database.md).

A ativação de base de dados Esticar uma base de dados ou uma tabela requer db\_permissões de proprietário. Também é a ativação esticar base de dados de uma base de dados requer permissões de controlo base de dados.

 >   [AZURE.NOTE] Mais tarde, se desativar a base de dados de opções esticar, lembre-se de que a desativação esticar base de dados para uma tabela ou para uma base de dados não elimina o objeto remoto. Se quiser eliminar a tabela remota ou base de dados remota, tem de largue-a utilizando o portal de gestão Azure. Os objetos remotos continuam a implicam custos Azure até eliminá-los manualmente.

## <a name="before-you-get-started"></a>Antes de começar

-   Antes de configurar uma base de dados para as opções esticar, recomendamos que execute a classificação de base de dados do Esticar para identificar bases de dados e tabelas que são elegíveis para esticar. A classificação de base de dados esticar também identifica os problemas de bloqueio. Para obter mais informações, consulte o artigo [Identificar bases de dados e as tabelas para esticar base de dados](sql-server-stretch-database-identify-databases.md).

-   Reveja [limitações para esticar da base de dados](sql-server-stretch-database-limitations.md).

-   Base de dados esticar migra dados para o Azure. Por isso, tem de ter uma conta Azure e uma subscrição de faturação. Para obter uma conta Azure, [clique aqui](http://azure.microsoft.com/pricing/free-trial/).

-   Ter a informação de ligação e início de sessão que precisa para criar um novo servidor Azure ou para selecionar um servidor Azure existente.

## <a name="EnableTSQLServer"></a>Pré-requisito: Activar esticar base de dados no servidor
Antes de poder ativar esticar base de dados numa base de dados ou uma tabela, tem de ativá-lo no servidor local. Esta operação requer permissões sysadmin ou serveradmin.

-   Se tiver as permissões administrativas necessárias, o Assistente de **Ativar a base de dados para esticar** configura o servidor para esticar.

-   Se não tiver as permissões necessárias, um administrador tem de ativar a opção manualmente executando **sp\_configurar** antes de executar o assistente ou um administrador tem de executar o assistente.

Para ativar manualmente esticar base de dados no servidor, execute o **sp\_configurar** e ative a opção de **arquivo de dados remota** . O exemplo seguinte permite que a opção de **arquivo de dados remota** ao definir o valor para 1.

```
EXEC sp_configure 'remote data archive' , '1';
GO

RECONFIGURE;
GO
```
Para obter mais informações, consulte o artigo [configurar os dados remotos arquivar opção de configuração de servidor](https://msdn.microsoft.com/library/mt143175.aspx) e [sp_configure (Transact-SQL)](https://msdn.microsoft.com/library/ms188787.aspx).

## <a name="Wizard"></a>Utilizar o Assistente para ativar as opções esticar base de dados numa base de dados
Para obter informações sobre a base de dados ativar para o Assistente de opções esticar, incluindo as informações que tem de introduzir e as escolhas que tem de fazer, consulte o artigo [Introdução ao executar a base de dados ativar para esticar assistente](sql-server-stretch-database-wizard.md).

## <a name="EnableTSQLDatabase"></a>Utilizar Transact\-SQL para ativar as opções esticar base de dados numa base de dados
Antes de poder ativar esticar base de dados em tabelas individuais, tem de ativá-la na base de dados.

A ativação de base de dados Esticar uma base de dados ou uma tabela requer db\_permissões de proprietário. Também é a ativação esticar base de dados de uma base de dados requer permissões de controlo base de dados.

1.  Antes de começar, selecione um servidor Azure existente para os dados que migra esticar base de dados ou criar um novo servidor Azure.

2.  No servidor do Azure, crie uma regra de firewall com o intervalo de endereços IP do SQL Server que permite do SQL Server comunicar com o servidor remoto.

    Pode encontrar facilmente os valores que precisa e crie a regra de firewall ao tentar ligar-se para o servidor Azure a partir do Explorador do objeto no SQL Server Management Studio (SSMS). SSMS ajuda-o para criar a regra ao abrir a caixa de diálogo seguinte que já inclui os valores de endereço IP necessários.

    ![Criar uma regra de firewall no SSMS][FirewallRule]

3.  Para configurar uma base de dados do SQL Server para esticar base de dados, a base de dados tem de ter uma chave de modelo global de base de dados. A chave de modelo global de base de dados protege as credenciais que utiliza esticar base de dados para ligar à base de dados remota. Eis um exemplo que cria uma nova chave de modelo global de base de dados.

    ```tsql
    USE <database>;
    GO

    CREATE MASTER KEY ENCRYPTION BY PASSWORD ='<password>';
    GO
    ```

    Para obter mais informações sobre a chave de modelo global de base de dados, consulte o artigo [criar o modelo global de chave (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) e [criar uma chave de modelo global de base de dados](https://msdn.microsoft.com/library/aa337551.aspx).

4.  Quando configura uma base de dados para esticar base de dados, tem de fornecer uma credencial para esticar base de dados a utilizar para a comunicação entre o local no SQL Server e o servidor remoto Azure. Tem duas opções.

    -   Pode fornecer uma credencial de administrador.

        -   Se ativar esticar base de dados, executando o assistente, pode criar a credencial nesse momento.

        -   Se planear activar esticar base de dados executando **ALTER DATABASE**, terá de criar a credencial manualmente antes de executar **ALTER DATABASE** para ativar as opções esticar base de dados.

        Eis um exemplo que cria uma nova credencial.

        ```tsql
        CREATE DATABASE SCOPED CREDENTIAL <db_scoped_credential_name>
            WITH IDENTITY = '<identity>' , SECRET = '<secret>';
        GO
        ```

        Para mais informações sobre a credencial, consulte o artigo [Criar base de dados no âmbito das CREDENCIAIS (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx). Criar a credencial necessita de alterar qualquer CREDENCIAL permissões.

    -   Pode utilizar uma conta de serviço federados para o SQL Server para comunicar com o servidor remoto Azure quando as seguintes condições são verdadeiras tudo.

        -   A conta do serviço em qual a instância do SQL Server está a ser executado é uma conta de domínio.

        -   A conta de domínio pertence a um domínio cuja do Active Directory esteja Federado com o Azure Active Directory.

        -   O servidor remoto Azure está configurado para suportar uma autenticação Azure Active Directory.

        -   A conta do serviço em qual a instância do SQL Server está a ser executado tem de ser configurada como uma conta do Gestor de BD ou sysadmin no servidor remoto Azure.

5.  Para configurar uma base de dados para esticar base de dados, execute o comando ALTER DATABASE.

    1.  Para o argumento de servidor, fornecer o nome de servidor Azure existente, incluindo o `.database.windows.net` parte do nome de \- por exemplo, `MyStretchDatabaseServer.database.windows.net`.

    2.  Fornecer uma credencial de administrador existente com o argumento de CREDENCIAIS, ou especificar federados\_serviço\_conta = ON. O exemplo seguinte fornece uma credencial existente.

    ```tsql
    ALTER DATABASE <database name>
        SET REMOTE_DATA_ARCHIVE = ON
            (
                SERVER = '<server_name>',
                CREDENTIAL = <db_scoped_credential_name>
            ) ;
    GO
    ```

## <a name="next-steps"></a>Próximos passos
-   [Ativar opções esticar base de dados para uma tabela](sql-server-stretch-database-enable-table.md) para ativar a tabelas adicionais.

-   [Base de dados do monitor esticar](sql-server-stretch-database-monitor.md) para ver o estado da migração de dados.

-   [Interromper e retomar esticar base de dados](sql-server-stretch-database-pause.md)

-   [Gerir e resolver problemas esticar base de dados](sql-server-stretch-database-manage.md)

-   [Cópia de segurança com capacidade de esticar as bases de dados](sql-server-stretch-database-backup.md)

## <a name="see-also"></a>Consulte também

[Identificar as bases de dados e as tabelas para esticar base de dados](sql-server-stretch-database-identify-databases.md)

[Opções de conjunto de base de dados ALTER (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)

[FirewallRule]: ./media/sql-server-stretch-database-enable-database/firewall.png
