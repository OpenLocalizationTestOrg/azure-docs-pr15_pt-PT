<properties
   pageTitle="Encriptação de dados transparente no armazém de dados SQL (T SQL) | Microsoft Azure"
   description="Encriptação de dados transparente (TDE) no armazém de dados SQL (T SQL)"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="09/24/2016"
   ms.author="rortloff;barbkess;sonyama"/>

# <a name="get-started-with-transparent-data-encryption-tde"></a>Começar com transparente dados encriptação (TDE)


> [AZURE.SELECTOR]
- [Descrição geral de segurança](sql-data-warehouse-overview-manage-security.md)
- [Autenticação](sql-data-warehouse-authentication.md)
- [Encriptação (Portal)](sql-data-warehouse-encryption-tde.md)
- [Encriptação (T SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permssions"></a>Permissões necessárias

Para ativar transparente dados encriptação (TDE), tem de ser um administrador ou um membro da função de Gestor de BD.

## <a name="enabling-encryption"></a>Activar a encriptação

Siga estes passos para activar TDE para um armazém de dados SQL:

1. Ligar à base de dados *principal* no servidor que aloja a base de dados utilizando um início de sessão que é um administrador ou um membro da função **Gestor de BD** na base de dados principal
2. Execute a seguinte instrução para encriptar a base de dados.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Desactivar a encriptação

Siga estes passos para desativar o TDE num armazém de dados SQL:

1. Ligar à base de dados *principal* com um início de sessão que é um administrador ou um membro da função **Gestor de BD** na base de dados principal
2. Execute a seguinte instrução para encriptar a base de dados.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [AZURE.NOTE] Um armazém de dados SQL em pausa tem de ser retomado antes de efetuar alterações às definições do TDE.

## <a name="verifying-encryption"></a>Encriptação a verificar

Para verificar o estado de encriptação para um armazém de dados SQL, siga os passos abaixo:

1. Ligar à base de dados *principal* ou instância utilizando um início de sessão que é um administrador ou um membro da função **Gestor de BD** na base de dados principal
2. Execute a seguinte instrução para encriptar a base de dados.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

Um resultado de ```1``` indica uma base de dados encriptada, ```0``` indica uma base de dados não encriptado.

## <a name="encryption-dmvs"></a>Encriptação DMVs  

- [sys.Databases][] 
- [sys.dm_pdw_nodes_database_encryption_keys][]


<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.Databases]: http://msdn.microsoft.com/library/ms178534.aspx  
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx  

<!--Image references-->

<!--Link references-->
