<properties
    pageTitle="Interromper e retomar a migração de dados (base de dados esticar) | Microsoft Azure"
    description="Saiba como interromper ou retomar a migração de dados para o Azure."
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
    ms.date="06/14/2016"
    ms.author="douglasl"/>

# <a name="pause-and-resume-data-migration-stretch-database"></a>Interromper e retomar a migração de dados (Esticar base de dados)

Para interromper ou retomar a migração de dados para o Azure, selecione **Esticar** para uma tabela no SQL Server Management Studio e, em seguida, selecione **colocar em pausa** para parar a migração de dados ou **Retomar** para retomar a migração de dados. Também pode utilizar Transact\-SQL para interromper ou retomar a migração de dados.

Migração de dados de pausa no tabelas individuais quando pretender para resolver os problemas no servidor local ou maximizar a largura de banda de rede disponíveis.

## <a name="pause-data-migration"></a>Migração de dados de colocar em pausa

### <a name="use-sql-server-management-studio-to-pause-data-migration"></a>Utilizar o SQL Server Management Studio para interromper a migração de dados

1.  No SQL Server Management Studio, no Explorador do objeto, selecione a opções esticar\-com capacidade de tabela para a qual pretende colocar em pausa migração de dados.

2.  Direita\-clique em e selecione **Esticar**e, em seguida, selecione **colocar em pausa**.

### <a name="use-transact-sql-to-pause-data-migration"></a>Utilizar Transact\-SQL para interromper a migração de dados
Execute o seguinte comando.

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>  
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = PAUSED ) ) ;  
GO
```

## <a name="resume-data-migration"></a>Retomar a migração de dados

### <a name="use-sql-server-management-studio-to-resume-data-migration"></a>Utilizar o SQL Server Management Studio para retomar a migração de dados

1.  No SQL Server Management Studio, no Explorador do objeto, selecione a opções esticar\-com capacidade de tabela para a qual pretende retomar a migração de dados.

2.  Direita\-clique em e selecione **Esticar**e, em seguida, selecione **currículo**.

### <a name="use-transact-sql-to-resume-data-migration"></a>Utilizar Transact\-SQL para retomar a migração de dados
Execute o seguinte comando.

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>   
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = OUTBOUND ) ) ;  
 GO
```

## <a name="check-whether-migration-is-active-or-paused"></a>Verificar se migração está ativo nem em pausa

### <a name="use-sql-server-management-studio-to-check-whether-migration-is-active-or-paused"></a>Utilizar o SQL Server Management Studio para verificar se migração está ativo nem em pausa
No SQL Server Management Studio, abra **Opções esticar Monitor de base de dados** e verifique o valor da coluna de **Estado de migração** . Para obter mais informações, consulte o artigo [Monitor e resolver problemas de migração de dados](sql-server-stretch-database-monitor.md).

### <a name="use-transact-sql-to-check-whether-migration-is-active-or-paused"></a>Utilizar Transact-SQL para verificar se migração está ativo nem em pausa
Consultar o catálogo vista **sys.remote_data_archive_tables** e selecione o valor da coluna **is_migration_paused** . Para obter mais informações, consulte o artigo [sys.remote_data_archive_tables](https://msdn.microsoft.com/library/dn935003.aspx).

## <a name="see-also"></a>Consulte também

[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)
[Monitor e resolver problemas de migração de dados](sql-server-stretch-database-monitor.md)
