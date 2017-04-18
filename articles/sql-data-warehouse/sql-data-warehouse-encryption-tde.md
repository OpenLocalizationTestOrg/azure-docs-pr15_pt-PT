<properties
   pageTitle="Encriptação de dados transparente no armazém de dados SQL (Portal) | Microsoft Azure"
   description="Encriptação de dados transparente (TDE) no armazém de dados SQL"
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

# <a name="get-started-with-transparent-data-encryption-tde-in-sql-data-warehouse"></a>Começar com transparente dados encriptação (TDE) no armazém de dados SQL

> [AZURE.SELECTOR]
- [Descrição geral de segurança](sql-data-warehouse-overview-manage-security.md)
- [Autenticação](sql-data-warehouse-authentication.md)
- [Encriptação (Portal)](sql-data-warehouse-encryption-tde.md)
- [Encriptação (T SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permssions"></a>Permissões necessárias

Para ativar transparente dados encriptação (TDE), tem de ser um administrador ou um membro da função de Gestor de BD.

## <a name="enabling-encryption"></a>Activar a encriptação

Para ativar TDE para um armazém de dados SQL, siga os passos abaixo:

1. Abra a base de dados no [portal do Azure](https://portal.azure.com)
2. No pá da base de dados, clique no botão **Definições**
3. Selecione a opção de **encriptação de dados transparente**![][1]
4. Selecione a definição **no**![][2]
5. Selecione **Guardar**
![][3]  

## <a name="disabling-encryption"></a>Desactivar a encriptação

Para desativar TDE para um armazém de dados SQL, siga os passos abaixo:

1. Abra a base de dados no [portal do Azure](https://portal.azure.com)
2. No pá da base de dados, clique no botão **Definições**
3. Selecione a opção de **encriptação de dados transparente**![][1]
4. Selecione a definição de **desativar**![][4]
5. Selecione **Guardar**
![][5]  

## <a name="encryption-dmvs"></a>Encriptação DMVs

Encriptação pode ser confirmada com DMVs os seguintes:

- [sys.Databases]
- [sys.dm_pdw_nodes_database_encryption_keys]

<!--MSDN references-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.Databases]: http://msdn.microsoft.com/library/ms178534.aspx
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->
