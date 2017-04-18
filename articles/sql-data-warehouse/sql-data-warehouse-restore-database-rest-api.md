<properties
   pageTitle="Restaurar um armazém de dados do Azure SQL (REST API) | Microsoft Azure"
   description="Tarefas de REST API para restaurar um armazém de dados do SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="Lakshmi1812"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/21/2016"
   ms.author="lakshmir;barbkess;sonyama"/>

# <a name="restore-an-azure-sql-data-warehouse-rest-api"></a>Restaurar um armazém de dados do Azure SQL (REST API)

> [AZURE.SELECTOR]
- [Descrição geral][]
- [Portal][]
- [PowerShell][]
- [RESTO][]

Este artigo vai aprender restaurar um armazém de dados SQL Azure utilizar a API REST.

## <a name="before-you-begin"></a>Antes de começar

**Verifique se a sua capacidade DTU.** Cada armazém de dados SQL está alojado ao SQL server (por exemplo, myserver.database.windows.net) que tem uma quota DTU predefinida.  Antes de poder restaurar um armazém de dados SQL, certifique-se de que o seu servidor SQL tem suficiente quota DTU restante para a base de dados a ser restaurada. Para saber como calcular DTU necessário ou para pedir DTU mais, consulte o artigo [solicitar uma alteração de quota DTU][].

## <a name="restore-an-active-or-paused-database"></a>Restaurar uma base de dados ativo ou em pausa

Para restaurar uma base de dados:

1. Obter a lista de pontos de restauro de base de dados utilizando a operação de pontos de restauro de base de dados de obter.
2. Inicie o seu restauro utilizando a operação de [pedido de restaurar criar base de dados][] .
3. Controle o estado da sua restaurar utilizando a operação de [Estado da operação de base de dados][] .

>[AZURE.NOTE] Quando tiver concluído a restaurar, pode configurar a sua base de dados recuperado pelos seguintes [Configurar a sua base de dados após a recuperação][].

## <a name="restore-a-deleted-database"></a>Restaurar uma base de dados eliminada

Para restaurar uma base de dados eliminado:

1.  Lista todas as bases de dados eliminados restaurável utilizando a operação de [lista restaurável ignorados as bases de dados][] .
2.  Obter os detalhes para a base de dados eliminada que pretende restaurar utilizando a operação [obter restaurável base de dados ignorado][] .
3.  Inicie o seu restauro utilizando a operação de [pedido de restaurar criar base de dados][] .
4.  Controle o estado da sua restaurar utilizando a operação de [Estado da operação de base de dados][] .

>[AZURE.NOTE] Para configurar a sua base de dados depois de concluído o restauro, consulte o artigo [Configurar a sua base de dados após a recuperação][]. 


## <a name="next-steps"></a>Próximos passos
Para saber mais sobre as funcionalidades de continuidade do negócio de edições de base de dados do Azure SQL, leia a [Descrição geral de continuidade do negócio base de dados do SQL Azure][].

<!--Image references-->

<!--Article references-->
[Azure descrição geral do continuidade de empresas base de dados SQL]: ./sql-database-business-continuity.md
[Solicitar uma alteração de quota DTU]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change
[Configurar a sua base de dados após a recuperação]: ./sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: ./powershell-install-configure.md
[Descrição geral]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[RESTO]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->
[Criar pedido de restauro de base de dados]: https://msdn.microsoft.com/library/azure/dn509571.aspx
[Estado da operação de base de dados]: https://msdn.microsoft.com/library/azure/dn720371.aspx
[Obter a base de dados ignorado restaurável]: https://msdn.microsoft.com/library/azure/dn509574.aspx
[Lista restaurável largadas bases de dados]: https://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
