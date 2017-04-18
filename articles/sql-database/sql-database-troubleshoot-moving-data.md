<properties
    pageTitle="Deslocar-se as bases de dados entre servidores, entre subscrições e e terminar Azure."
    description="Passos rápidos para copiar, mover e migrar dados e bases de dados na base de dados do SQL Azure."
    services="sql-database"
    documentationCenter=""
    authors="v-shysun"
    manager="felixwu"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="v-shysun"/>

# <a name="move-databases-between-servers-between-subscriptions-and-in-and-out-of-azure"></a>Mover bases de dados entre servidores, entre subscrições e e terminar Azure

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]
##<a name="to-move-a-database-to-a-different-server-in-the-same-subscription"></a>Para mover uma base de dados para um servidor diferente na mesma subscrição
- No [Portal do Azure](https://portal.azure.com), clique em **bases de dados SQL**, selecione uma base de dados a partir da lista e, em seguida, clique em **Copiar**. Para obter mais detalhes, consulte a [copiar uma base de dados do Azure SQL](sql-database-copy.md) .

## <a name="to-move-a-database-between-subscriptions"></a>Para mover uma base de dados entre subscrições
- No [Portal do Azure](https://portal.azure.com), clique em **servidores de SQL** e, em seguida, selecione servidor que aloja a sua base de dados a partir da lista. Clique em **Mover**e, em seguida, escolha os recursos para mover e deslocar-se para a subscrição.

## <a name="to-migrate-a-sql-database-into-azure"></a>Migrar uma base de dados do SQL Azure
- Determinar a compatibilidade de base de dados e, em seguida, selecione o método de migração direita com base nas suas necessidades. Siga as orientações e opções no [migrar uma base de dados do SQL Server](sql-database-cloud-migrate.md).

## <a name="to-create-a-copy-of-a-database-for-use-outside-of-azure"></a>Para criar uma cópia de uma base de dados para utilização fora do Azure
- [Exporte um ficheiro BACPAC.](sql-database-export.md)
