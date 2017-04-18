<properties
   pageTitle="Activar a encriptação de dados transparente (TDE) para SQL Server esticar base de dados no Azure TSQL | Microsoft Azure"
   description="Ativar a encriptação de dados transparente (TDE) para SQL Server esticar base de dados num Azure TSQL"
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
   ms.author="douglaslMS"/>

# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure-transact-sql"></a>Activar a encriptação de dados transparente (TDE) para esticar base de dados no Azure (Transact-SQL)
> [AZURE.SELECTOR]
- [Portal do Azure](sql-server-stretch-database-encryption-tde.md)
- [TSQL](sql-server-stretch-database-tde-tsql.md)

Encriptação de dados transparente (TDE) ajuda a proteger contra a ameaça de atividade maliciosa executando encriptação em tempo real e desencriptar de base de dados, associadas cópias de segurança e ficheiros de registo da transação em repouso sem exigir alterações à aplicação.

TDE encripta o armazenamento dos toda uma base de dados utilizando uma chave simétrica denominada a chave de encriptação da base de dados. A chave de encriptação da base de dados está protegida por um certificado de servidor incorporados. O certificado de servidor incorporados é exclusivo para cada server Azure. Microsoft roda automaticamente estes certificados pelo menos a cada cerca de 90 dias. Para obter uma descrição geral de TDE, consulte o artigo [Transparente dados encriptação (TDE)].

##<a name="enabling-encryption"></a>Activar a encriptação

Para ativar TDE para um Azure base de dados que está a armazenar os dados migrados a partir de uma base de dados do SQL Server com capacidade de esticar, efetue as seguintes coisas:

1. Ligar à base de dados *principal* no servidor Azure que aloja a base de dados utilizando um início de sessão que é um administrador ou um membro da função **Gestor de BD** na base de dados principal
2. Execute a seguinte instrução para encriptar a base de dados.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION ON;
```

##<a name="disabling-encryption"></a>Desactivar a encriptação

Para desativar o TDE num Azure base de dados que está a armazenar os dados migrados a partir de uma base de dados do SQL Server com capacidade de opções esticar, efetue as seguintes coisas:

1. Ligar à base de dados *principal* com um início de sessão que é um administrador ou um membro da função **Gestor de BD** na base de dados principal
2. Execute a seguinte instrução para encriptar a base de dados.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION OFF;
```

##<a name="verifying-encryption"></a>Encriptação a verificar

Para verificar o estado de encriptação para uma base de dados Azure que está a armazenar os dados migrados a partir de uma base de dados do SQL Server com capacidade de opções esticar, efetue as seguintes coisas:

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


<!--Anchors-->
[Encriptação de dados transparente (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->

<!--Link references-->
