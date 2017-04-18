<properties
    pageTitle="Desativar as opções esticar base de dados e voltar trazer dados remotos | Microsoft Azure"
    description="Saiba como desativar esticar base de dados para uma tabela e, opcionalmente, reponha dados remotos."
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

# <a name="disable-stretch-database-and-bring-back-remote-data"></a>Desativar as opções esticar base de dados e mostrá novamente remota de dados

Para desativar as opções esticar base de dados para uma tabela, selecione **Esticar** para uma tabela no SQL Server Management Studio. Em seguida, selecione uma das seguintes opções.

-   Desativar **| Trazer os dados novamente a partir do Azure**. Copiar os dados remotos para a tabela a partir do Azure de volta para o SQL Server, em seguida, desative esticar base de dados para a tabela. Esta operação como custos de transferência de dados e não é possível cancelá-lo.

-   Desativar **| Deixar de dados no Azure**. Desative as opções esticar base de dados para a tabela.  Abandone os dados para a tabela no Azure remotos.

Também pode utilizar Transact\-SQL para desativar as opções esticar base de dados para uma tabela ou para uma base de dados.

Depois de desativar esticar base de dados para uma tabela, marcas de migração de dados e os resultados da consulta deixarão de incluir resultados a partir da tabela remoto.

Se pretender simplesmente interromper a migração de dados, consulte o artigo [Interromper e retomar esticar base de dados](sql-server-stretch-database-pause.md).

>   [AZURE.NOTE] Desativar as opções esticar base de dados para uma tabela ou para uma base de dados não elimina o objeto remoto. Se quiser eliminar a tabela remota ou base de dados remota, tem de largue-a utilizando o portal de gestão Azure. Os objetos remotos continuam a implicam custos Azure até que eliminá-los. Para obter mais informações, consulte o artigo [Preços de base de dados do SQL Server esticar](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

## <a name="disable-stretch-database-for-a-table"></a>Desativar as opções esticar base de dados para uma tabela

### <a name="use-sql-server-management-studio-to-disable-stretch-database-for-a-table"></a>Utilizar o SQL Server Management Studio para desativar as opções esticar base de dados para uma tabela

1.  No SQL Server Management Studio, no Explorador do objeto, selecione a tabela para a qual pretende desativar esticar base de dados.

2.  Direita\-clique em e selecione as **Opções esticar**e, em seguida, selecione uma das seguintes opções.

    -   Desativar **| Trazer os dados novamente a partir do Azure**. Copiar os dados remotos para a tabela a partir do Azure de volta para o SQL Server, em seguida, desative esticar base de dados para a tabela. Este comando não pode ser cancelado.

        >   [AZURE.NOTE] Copiar os dados remotos para a tabela a partir do Azure anterior para SQL Server como custos de transferência de dados. Para obter mais informações, consulte o artigo [Detalhes de preços de transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/).

        Depois de todos os dados remotos foi copiados do Azure novamente para SQL Server, esticar é desativada para a tabela.

    -   Desativar **| Deixar de dados no Azure**. Desative as opções esticar base de dados para a tabela.  Abandone os dados para a tabela no Azure remotos.

    >   [AZURE.NOTE] Desativar as opções esticar base de dados para uma tabela não elimina os dados remotos ou da tabela remota. Se quiser eliminar a tabela remota, tem de largue-a utilizando o portal de gestão Azure. A tabela remota continua a implicam custos Azure até eliminá-lo. Para obter mais informações, consulte o artigo [Preços de base de dados do SQL Server esticar](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

### <a name="use-transact-sql-to-disable-stretch-database-for-a-table"></a>Utilizar Transact\-SQL para desativar as opções esticar base de dados para uma tabela

-   Para desativar as opções Esticar para uma tabela e copie o controlo remoto dados para a tabela a partir do Azure novamente o SQL Server, execute o seguinte comando. Depois de todos os dados remotos foi copiados do Azure novamente para SQL Server, esticar é desativada para a tabela.

    Este comando não pode ser cancelado.

    ```tsql
    USE <Stretch-enabled database name>;
    GO
    ALTER TABLE <Stretch-enabled table name>  
       SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = INBOUND ) ) ;
    GO
    ```
    >   [AZURE.NOTE] Copiar os dados remotos para a tabela a partir do Azure anterior para SQL Server como custos de transferência de dados. Para obter mais informações, consulte o artigo [Detalhes de preços de transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/).

-   Para desativar as opções Esticar para uma tabela e abandonar os dados remotos, execute o seguinte comando.

    ```tsql
    ALTER TABLE <table_name>
       SET ( REMOTE_DATA_ARCHIVE = OFF_WITHOUT_DATA_RECOVERY ( MIGRATION_STATE = PAUSED ) ) ;
    ```

>   [AZURE.NOTE] Desativar as opções esticar base de dados para uma tabela não elimina os dados remotos ou da tabela remota. Se quiser eliminar a tabela remota, tem de largue-a utilizando o portal de gestão Azure. A tabela remota continua a implicam custos Azure até eliminá-lo. Para obter mais informações, consulte o artigo [Preços de base de dados do SQL Server esticar](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

## <a name="disable-stretch-database-for-a-database"></a>Desativar esticar base de dados para uma base de dados
Antes de pode desativar esticar base de dados para uma base de dados, tem de desativar esticar base de dados a esticar individual\-com capacidade de tabelas da base de dados.

### <a name="use-sql-server-management-studio-to-disable-stretch-database-for-a-database"></a>Utilizar o SQL Server Management Studio para desativar as opções esticar base de dados para uma base de dados

1.  No SQL Server Management Studio, no Explorador do objeto, selecione a base de dados para o qual pretende desativar esticar base de dados.

2.  Direita\-clique em e selecione **tarefas**e, em seguida, selecione **Esticar**e, em seguida, selecione **desativar**.

>   [AZURE.NOTE] Desativar as opções esticar base de dados para uma base de dados não elimina a base de dados remota. Se quiser eliminar a base de dados remota, tem de largue-a utilizando o portal de gestão Azure. Base de dados remota continua a implicam custos Azure até eliminá-lo. Para obter mais informações, consulte o artigo [Preços de base de dados do SQL Server esticar](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

### <a name="use-transact-sql-to-disable-stretch-database-for-a-database"></a>Utilizar Transact\-SQL para desativar as opções esticar base de dados para uma base de dados
Execute o seguinte comando.

```tsql
ALTER DATABASE <database name>
    SET REMOTE_DATA_ARCHIVE = OFF ;
```

>   [AZURE.NOTE] Desativar as opções esticar base de dados para uma base de dados não elimina a base de dados remota. Se quiser eliminar a base de dados remota, tem de largue-a utilizando o portal de gestão Azure. Base de dados remota continua a implicam custos Azure até eliminá-lo. Para obter mais informações, consulte o artigo [Preços de base de dados do SQL Server esticar](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

## <a name="see-also"></a>Consulte também

[Opções de conjunto de base de dados ALTER (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)

[Interromper e retomar esticar base de dados](sql-server-stretch-database-pause.md)
