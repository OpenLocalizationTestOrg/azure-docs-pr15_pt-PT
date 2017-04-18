<properties
    pageTitle="Restaurar as bases de dados com capacidade de esticar | Microsoft Azure"
    description="Saiba como restaurar esticar\-com capacidade de bases de dados."
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
    ms.date="08/01/2016"
    ms.author="douglasl"/>

# <a name="restore-stretch-enabled-databases"></a>Restaurar esticar com permissão para bases de dados

Restaure uma cópia de segurança da base de dados quando for necessário para recuperar a partir de vários tipos de falhas, erros e catástrofes.

Para mais informações sobre a cópia de segurança, consulte o artigo [com capacidade de cópia de segurança esticar bases de dados](sql-server-stretch-database-backup.md).

>   [AZURE.NOTE] Cópia de segurança é apenas uma parte de um concluída elevada disponibilidade e a solução de continuidade de negócios. Para obter mais informações sobre a disponibilidade de alta, consulte o artigo [Alto de disponibilidade de soluções](https://msdn.microsoft.com/library/ms190202.aspx).

## <a name="restore-your-sql-server-data"></a>Restaurar dados do SQL Server
Para recuperar a partir de uma falha de hardware ou danos, restaurar a opções esticar\-base de dados do SQL Server a partir de uma cópia de segurança activada. Pode continuar a utilizar os métodos de restaurar do SQL Server que está a utilizar. Para obter mais informações, consulte o artigo [Restaurar descrição geral e recuperação](https://msdn.microsoft.com/library/ms191253.aspx).

Depois de restaurar a base de dados do SQL Server, tem de executar o procedimento armazenado **sys.sp_rda_reauthorize_db** restabelecer a ligação entre a opções esticar\-com capacidade de base de dados do SQL Server e a base de dados remota Azure. Para obter mais informações, consulte o artigo [restaurar a ligação entre a base de dados do SQL Server e a base de dados remota Azure](#restore-the-connection-between-the-sql-server-database-and-the-remote-azure-database).

## <a name="restore-your-remote-azure-data"></a>Restaurar os seus dados Azure remotos

### <a name="recover-a-live-azure-database"></a>Recuperar uma base de dados Azure direto
A base de dados do SQL Server esticar de serviço no Azure instantâneos todos os dados dinâmicos pelo menos a cada 8 horas utilização de instantâneos de armazenamento do Azure. Estes instantâneos são mantidos durante 7 dias. Isto permite-lhe restaurar os dados para um dos, pelo menos, 21 pontos em tempo dentro dos últimos 7 dias por excesso para o tempo quando o último instantâneo foi despendido.

Para restaurar uma base de dados Azure direto para um ponto anterior no tempo ao utilizar o portal do Azure, efetue as seguintes coisas.

1. Inicie sessão no portal do Azure.
2. No lado esquerdo do ecrã selecione **Procurar** e, em seguida, selecione **Bases de dados SQL**.
3. Navegue para a base de dados e selecioná-la.
4. Na parte superior da pá a base de dados, clique em **Restaurar**.
5. Especificar um novo **nome da base de dados**, selecione um **Ponto de restauro** e, em seguida, clique em **Criar**.
6. O processo de restauro de base de dados será iniciado e pode ser monitorizado através de **notificações**.

### <a name="recover-a-deleted-azure-database"></a>Recuperar uma base de dados eliminado Azure
O serviço de base de dados do SQL Server esticar no Azure tira um instantâneo de base de dados antes de uma base de dados é ignorado e mantém-lo durante 7 dias. Depois de tal acontecer, já não mantém instantâneos da base de dados dinâmico. Permite-lhe restaurar uma base de dados eliminado para o ponto de quando foi eliminada.

Para restaurar uma base de dados eliminado Azure para o ponto de quando foi eliminado utilizando o portal do Azure, efetue as seguintes coisas.

1. Inicie sessão no portal do Azure.
2. No lado esquerdo do ecrã selecione **Procurar** e, em seguida, selecione **Os servidores de SQL**.
3. Navegue até ao seu servidor e selecioná-la.
4. Desloque para baixo para operações de pá do seu servidor, clique no mosaico de **Bases de dados eliminados** .
5. Selecione a base de dados eliminada que pretende restaurar.
5. Especificar um novo **nome da base de dados** e clique em **Criar**.
6. O processo de restauro de base de dados será iniciado e pode ser monitorizado através de **notificações**.

## <a name="restore-the-connection-between-the-sql-server-database-and-the-remote-azure-database"></a>Restaurar a ligação entre a base de dados do SQL Server e a base de dados remota Azure

1.  Se pretender ligar a uma restaurado Azure base de dados com um nome diferente ou numa região diferente, execute o procedimento armazenado [sys.sp_rda_deauthorize_db](https://msdn.microsoft.com/library/mt703716.aspx) para desligar a base de dados Azure anterior.  

2.  Executar o procedimento armazenado [sys.sp_rda_reauthorize_db](https://msdn.microsoft.com/library/mt131016.aspx) para voltar a ligar o local esticar\-com capacidade de base de dados para a base de dados Azure.  

    -   Fornecer a credencial de base de dados confinado existente como um sysname ou um varchar\(128\) valor. \(Não utilize varchar\(max\).\) Pode procurar o nome de credenciais na vista **sys.database\_confinados\_credenciais**.  

    -   Especifique se pretende fazer uma cópia dos dados remotos e ligue-se para a cópia (recomendada).  

    ```tsql  
    USE <Stretch-enabled database name>;
    GO
    EXEC sp_rda_reauthorize_db
        @credential = N'<existing_database_scoped_credential_name>',
        @with_copy = 1 ;  
    GO
    ```  

## <a name="see-also"></a>Consulte também

[Gerir e resolver problemas esticar base de dados](sql-server-stretch-database-manage.md)

[sys.sp_rda_reauthorize_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx)

[Criar cópias de segurança e restauro de bases de dados do SQL Server](https://msdn.microsoft.com/library/ms187048.aspx)
