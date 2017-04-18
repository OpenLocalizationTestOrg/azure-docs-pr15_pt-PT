<properties
   pageTitle="Restaurar um armazém de dados do Azure SQL (Portal) | Microsoft Azure"
   description="Azure tarefas portais para restaurar um armazém de dados do SQL Azure."
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

# <a name="restore-an-azure-sql-data-warehouse-portal"></a>Restaurar um armazém de dados do Azure SQL (Portal)

> [AZURE.SELECTOR]
- [Descrição geral][]
- [Portal][]
- [PowerShell][]
- [RESTO][]

Este artigo vai aprender restaurar um armazém de dados SQL Azure utilizando o Portal do Azure.

## <a name="before-you-begin"></a>Antes de começar

**Verifique se a sua capacidade DTU.** Cada armazém de dados SQL está alojado ao SQL server (por exemplo, myserver.database.windows.net) que tem uma quota DTU predefinida.  Antes de poder restaurar um armazém de dados SQL, certifique-se de que o seu servidor SQL tem suficiente quota DTU restante para a base de dados a ser restaurada. Para saber como calcular DTU necessário ou para pedir DTU mais, consulte o artigo [solicitar uma alteração de quota DTU][].


## <a name="restore-an-active-or-paused-database"></a>Restaurar uma base de dados ativo ou em pausa

Para restaurar uma base de dados:

1. Inicie a sessão [portal do Azure][]
2. No lado esquerdo do ecrã selecione **Procurar** e, em seguida, selecione **os servidores de SQL**
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
    
3. Navegue até ao seu servidor e selecione-o
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-server.png)

4. Localizar o armazenamento de dados SQL que pretende restaurar a partir de e selecione-o
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-active-dw.png)
5. Na parte superior da pá armazém de dados, clique em **Restaurar**
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-restore-from-active.png)

6. Especificar um novo **nome da base de dados**
7. Selecione o mais recente de **Ponto de restauro**
    1. Certifique-se de que escolher o ponto de restauro mais recente.  Dado que os pontos de restauro são apresentados na UTC, por vezes, a opção predefinida apresentada não é o ponto de restauro mais recente.
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-restore-blade-from-active.png)

8. Clique em **OK**
9. O processo de restauro de base de dados será iniciado e pode ser controlado através de **notificações**

>[AZURE.NOTE] Quando tiver concluído a restaurar, pode configurar a sua base de dados recuperado pelos seguintes [Configurar a sua base de dados após a recuperação][].


## <a name="restore-a-deleted-database"></a>Restaurar uma base de dados eliminada

Para restaurar uma base de dados eliminado:

1. Inicie a sessão [portal do Azure][]
2. No lado esquerdo do ecrã selecione **Procurar** e, em seguida, selecione **os servidores de SQL**
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)

3. Navegue até ao seu servidor e selecione-o
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-server.png)

4. Desloque para baixo para a secção operações no pá do seu servidor
5. Clique no mosaico de **Bases de dados eliminada**
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dws.png)

6. Selecione a base de dados eliminada que pretende restaurar
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dw.png)

7. Especificar um novo **nome da base de dados**
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-restore-blade-from-deleted.png)
    
8. Clique em **OK**
9. O processo de restauro de base de dados será iniciado e pode ser controlado através de **notificações**

>[AZURE.NOTE] Para configurar a sua base de dados depois de concluído o restauro, consulte o artigo [Configurar a sua base de dados após a recuperação][]. 

## <a name="next-steps"></a>Próximos passos
Para saber mais sobre as funcionalidades de continuidade do negócio de edições de base de dados do Azure SQL, leia a [Descrição geral de continuidade do negócio base de dados do SQL Azure][].

<!--Image references-->

<!--Article references-->
[Azure descrição geral do continuidade de empresas base de dados SQL]: ./sql-database-business-continuity.md
[Descrição geral]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[RESTO]: ./sql-data-warehouse-restore-database-rest-api.md
[Configurar a sua base de dados após a recuperação]: ./sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Solicitar uma alteração de quota DTU]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change

<!--MSDN references-->

<!--Blog references-->

<!--Other Web references-->
[Portal do Azure]: https://portal.azure.com/
