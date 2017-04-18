<properties
    pageTitle="Gerir a base de dados do SQL Azure utilizando o Portal do Azure | Microsoft Azure"
    description="Saiba como utilizar o Portal do Azure para gerir uma base de dados relacional na nuvem através do Portal Azure."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.date="09/19/2016"
    ms.author="sstein"/>


# <a name="managing-azure-sql-databases-using-the-azure-portal"></a>Gerir bases de dados do SQL Azure através do portal Azure


> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-manage-portal.md)
- [SSMS](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-manage-powershell.md)

O [Azure portal](https://portal.azure.com/) permite-lhe criar, monitorizar e gerir bases de dados Azure SQL e servidores. Este artigo fornece uma descrição rápida e ligações para os detalhes das tarefas mais comuns.

## <a name="view-your-azure-sql-databases-servers-and-pools"></a>Ver as bases de dados Azure SQL, servidores e conjuntos de dados

Para ver os serviços de base de dados SQL disponíveis, clique em **mais serviços**e escreva o **SQL** na caixa de pesquisa:

![Base de dados SQL](./media/sql-database-manage-portal/sql-services.png)


## <a name="how-do-i-create-or-view-azure-sql-databases"></a>Como criar ou ver bases de dados Azure SQL?

Para abrir o pá de **bases de dados SQL** , clique em **bases de dados SQL**e, em seguida, clique em da base de dados que pretende trabalhar com ou clique em **+ Adicionar** para criar uma base de dados do SQL. Para obter detalhes, consulte o artigo [criar uma base de dados do SQL em minutos utilizando o portal do Azure](sql-database-get-started.md).


![Bases de dados SQL](./media/sql-database-manage-portal/sql-databases.png)


## <a name="how-do-i-create-or-view-azure-sql-servers"></a>Como criar ou visualizar os servidores de Azure SQL?

Para abrir o pá **servidores SQL Server** , clique em **servidores SQL**e, em seguida, clique no servidor que pretende trabalhar com ou clique em **+ Adicionar** para criar um servidor SQL. Para obter detalhes, consulte o artigo [criar uma base de dados do SQL em minutos utilizando o portal do Azure](sql-database-get-started.md).

![Servidores de SQL](./media/sql-database-manage-portal/sql-servers.png)


## <a name="how-do-i-create-or-view-sql-elastic-pools"></a>Como criar ou ver agrupamentos flexível SQL?

Para abrir o pá **agrupamentos flexível de SQL** , clique em **conjuntos de dados flexível SQL**e, em seguida, clique em conjunto que pretende trabalhar com ou clique em **+ Adicionar** para criar um conjunto de dados. Para obter detalhes, consulte o artigo [criar um conjunto de base de dados flexível com o portal do Azure](sql-database-elastic-pool-create-portal.md).

![Conjuntos de dados flexível SQL](./media/sql-database-manage-portal/elastic-pools.png)



## <a name="how-do-i-update-or-view-sql-database-settings"></a>Como atualizar ou ver as definições de base de dados SQL

Para ver ou atualizar as definições de base de dados, clique na definição pretendida na pá de base de dados SQL:


![Definições de base de dados SQL](./media/sql-database-manage-portal/settings.png)


## <a name="how-do-i-find-a-sql-databases-fully-qualified-server-name"></a>Como localizo um nome de servidor completamente qualificado de bases de dados do SQL?

Para ver o seu nome de servidor de bases de dados, clique em **Descrição geral** sobre a **base de dados SQL** pá e tenha em atenção o nome do servidor:


![Definições de base de dados SQL](./media/sql-database-manage-portal/server-name.png)


## <a name="how-do-i-manage-firewall-rules-to-control-access-to-my-sql-server-and-database"></a>Como posso gerir regras de firewall para controlar o acesso ao meu do SQL server e a base de dados?

Para ver, criar ou atualizar as regras de firewall, clique em **Definir server firewall** no pá a **base de dados SQL** . Para obter detalhes, consulte o artigo [Configurar uma regra de firewall de nível do servidor de base de dados do SQL Azure através do portal Azure](sql-database-configure-firewall-settings.md).


![regras da firewall](./media/sql-database-manage-portal/sql-database-firewall.png)


## <a name="how-do-i-change-my-sql-database-service-tier-or-performance-level"></a>Como posso alterar a minha SQL da base de dados camada ou desempenho nível de serviço?


Para atualizar o nível de camada ou o desempenho do serviço de uma base de dados do SQL, clique em **níveis de preços (escala DTUs)** no pá a **base de dados SQL** . Para obter detalhes, consulte o artigo [alterar o serviço camada e desempenho nível (preços camada) de uma base de dados do SQL](sql-database-scale-up.md).


![preços camadas](./media/sql-database-manage-portal/pricing-tier.png)


## <a name="how-do-i-configure-auditing-and-threat-detection-for-a-sql-database"></a>Como configurar auditorias e ameaças deteção para uma base de dados do SQL?

Para configurar a deteção de auditoria e ameaça para uma base de dados do SQL, clique em **deteção de auditoria e ameaças** no pá a **base de dados SQL** . Para obter detalhes, consulte o artigo [Introdução ao auditoria de base de dados SQL](sql-database-auditing-get-started.md)e [começar a trabalhar com a deteção de ameaça de base de dados SQL](sql-database-threat-detection-get-started.md).


## <a name="how-do-i-configure-dynamic-data-masking-for-a-sql-database"></a>Como configurar o dados dinâmicos masking para uma base de dados SQL

Para configurar dados dinâmicos masking para uma base de dados do SQL, clique em **dados dinâmicos masking** no pá a **base de dados SQL** . Para obter detalhes, consulte o artigo [Introdução ao SQL da base de dados dinâmicos dados Masking](sql-database-dynamic-data-masking-get-started.md).


## <a name="how-do-i-configure-transparent-data-encryption-tde-for-a-sql-database"></a>Como configurar o encriptação de dados transparente (TDE) para uma base de dados SQL

Para configurar a encriptação de dados transparente para uma base de dados do SQL, clique em **encriptação de dados transparente** no pá a **base de dados SQL** . Para obter detalhes, consulte o artigo [Ativar TDE numa base de dados através do portal](https://msdn.microsoft.com/library/dn948096#Anchor_1).

## <a name="how-do-i-view-or-change-the-max-size-of-a-sql-database"></a>Como posso ver ou alterar o tamanho máximo de uma base de dados do SQL?

Para ver ou alterar o tamanho da base de dados do SQL, clique em **tamanho da base de dados** no pá a **base de dados SQL** . Atualize o tamanho máximo de uma base de dados ao alterar o nível de serviço camada ou desempenho. Para obter detalhes, consulte o artigo [alterar o serviço camada e desempenho nível (preços camada) de uma base de dados do SQL](sql-database-scale-up.md).

## <a name="how-do-i-monitor-and-improve-the-performance-of-a-sql-database"></a>Como monitorizar e melhorar o desempenho da base de dados do SQL?

Monitorizar e melhorar características de desempenho da base de dados do SQL, clique em **Descrição geral de desempenho** no pá a **base de dados SQL** . Para obter detalhes, consulte o artigo [Conhecimentos aprofundados de desempenho de base de dados SQL](sql-database-performance.md).


## <a name="how-do-i-configure-geo-replication"></a>Como configurar o Geo replicação?

Para configurar o Geo replicação para uma base de dados do SQL, clique em **Geo replicação** no pá a **base de dados SQL** . Para obter detalhes, consulte o artigo [Configurar Geo-replicação para a base de dados do SQL Azure com o portal do Azure](sql-database-geo-replication-portal.md).


## <a name="how-do-i-failover-to-a-geo-replicated-sql-database"></a>Como é que activação pós-falha numa base de dados do SQL replicadas geo?

Para falha na ligação para um secundário de replicadas geo, clique em **Geo replicação** no pá a **base de dados SQL** e clique em **activação pós-falha**. Para obter detalhes, consulte o artigo [Iniciar planeado ou não planeado activação pós-de uma falha, para a base de dados do SQL Azure com o portal do Azure](sql-database-geo-replication-failover-portal.md).


## <a name="how-do-i-copy-a-sql-database"></a>Como posso copiar uma base de dados do SQL?

Para copiar uma base de dados do SQL, clique em **Copiar** no pá a **base de dados SQL** . Para obter detalhes, consulte o artigo [copiar uma base de dados do Azure SQL através do portal Azure](sql-database-copy-portal.md).


![Definições de base de dados SQL](./media/sql-database-manage-portal/sql-database-copy.png)

## <a name="how-do-i-archive-an-azure-sql-database-to-a-bacpac-file"></a>Como arquivar a uma base de dados do Azure SQL para um ficheiro BACPAC?

Para criar um BACPAC de uma base de dados do SQL, clique em **Exportar** no pá a **base de dados SQL** . Para obter detalhes, consulte o artigo [arquivo uma base de dados do Azure SQL para um ficheiro BACPAC através do portal Azure](sql-database-export.md).


![Exportar de base de dados SQL](./media/sql-database-manage-portal/sql-database-export.png)



## <a name="how-do-i-restore-a-sql-database-to-a-previous-point-in-time"></a>Como restaurar uma base de dados do SQL para um ponto anterior no tempo?

Para restaurar uma base de dados do SQL, clique em **Restaurar** no pá a **base de dados SQL** . Para obter detalhes, consulte o artigo [Restaurar uma base de dados do SQL Azure para um ponto anterior em tempo com o portal do Azure](sql-database-point-in-time-restore-portal.md).


![Definições de base de dados SQL](./media/sql-database-manage-portal/sql-database-restore.png)


## <a name="how-do-i-create-an-azure-sql-database-from-a-bacpac-file"></a>Como criar uma base de dados do Azure SQL a partir de um ficheiro BACPAC?

Para criar uma base de dados do SQL de um ficheiro BACPAC, clique em **Importar base de dados** no pá **SQL server** . Para obter detalhes, consulte o artigo [Importar um ficheiro BACPAC para criar uma base de dados do Azure SQL](sql-database-import.md).


![Do SQL server](./media/sql-database-manage-portal/server-commands.png)


## <a name="how-do-i-restore-a-deleted-sql-database"></a>Como posso restaurar uma base de dados do SQL eliminado?

Para restaurar uma base de dados do SQL eliminado, clique em **eliminado bases de dados** no pá **SQL server** (o SQL server que continha a base de dados que foi eliminado). Para obter detalhes, consulte o artigo [Restaurar uma base de dados do Azure SQL eliminado através do portal Azure](sql-database-restore-deleted-database-portal.md).

## <a name="how-do-i-delete-a-sql-database"></a>Como posso eliminar uma base de dados do SQL?

Para eliminar uma base de dados do SQL, clique em **Eliminar** no pá a **base de dados SQL** . 

![Definições de base de dados SQL](./media/sql-database-manage-portal/sql-database-delete.png)



## <a name="additional-resources"></a>Recursos adicionais

- [Base de dados SQL](sql-database-technical-overview.md)
- [Monitorizar e gerir um conjunto de base de dados flexível com o portal do Azure](sql-database-elastic-pool-manage-portal.md)
