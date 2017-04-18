<properties 
    pageTitle="Iniciar uma activação pós-falha planeado ou não planeado para uma base de dados do SQL Azure com o portal do Azure | Microsoft Azure" 
    description="Iniciar uma activação pós-falha planeado ou não planeado para uma base de dados do SQL Azure através do portal Azure" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management" 
    ms.date="08/29/2016"
    ms.author="sstein"/>

# <a name="initiate-a-planned-or-unplanned-failover-for-azure-sql-database-with-the-azure-portal"></a>Iniciar uma activação pós-falha planeado ou não planeado para uma base de dados do SQL Azure com o portal do Azure


> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-geo-replication-failover-portal.md)
- [PowerShell](sql-database-geo-replication-failover-powershell.md)
- [T-SQL](sql-database-geo-replication-failover-transact-sql.md)


Este artigo mostra-lhe como iniciar activação pós-falha para uma base de dados do SQL secundário com o [Azure portal](http://portal.azure.com). Para configurar a replicação Geo, consulte o artigo [Geo replicação configurar para a base de dados do SQL Azure](sql-database-geo-replication-portal.md).


## <a name="initiate-a-failover"></a>Iniciar uma activação pós-falha

A base de dados secundária pode ser mudado para se tornar a página principal.  

1. No procurar [Azure portal](http://portal.azure.com) para a base de dados principal a parceria de replicação Geo.
2. No pá base de dados SQL, selecione **todas as definições de** > **Geo replicação**.
3. Na lista de **secundários** , selecione a base de dados que pretende tornar a nova página principal e clique em **activação pós-falha**.

    ![activação pós-falha][2]

4. Clique em **Sim** para começar a activação pós-falha.

O comando mudará imediatamente a base de dados secundária para a função principal. 

Existe um período curto durante o qual não ambas as bases de dados estão disponíveis (na ordem de 0 a 25 segundos) enquanto as funções são mudadas. Se a base de dados principal tem várias bases de dados secundárias, o comando será automaticamente reconfigurar os outros secundários para ligar para a nova página principal. Toda a operação deve ter menos de um minuto para concluir em circunstâncias normais. 

>[AZURE.NOTE] Se a página principal está online e quando é emitido o comando algumas perda de dados a consolidar transações poderá ocorrer.


## <a name="next-steps"></a>Próximos passos   

- Depois de falha na ligação, certifique-se de que os requisitos de autenticação para o seu servidor e a base de dados estão configurados na nova página principal. Para obter detalhes, consulte o artigo [segurança da base de dados SQL após a recuperação de falhas](sql-database-geo-replication-security-config.md).
- Para saber recuperar depois de uma falhas utilizando a replicação Geo ativo, que incluindo pre e registar os passos de recuperação e efetuar uma pesquisa de recuperação de falhas, consulte o artigo [Brocas de recuperação de falhas](sql-database-disaster-recovery.md)
- Para uma mensagem do blogue Sasha Nosov sobre Geo-replicação do Active, consulte o artigo [destaque de novas capacidades Geo replicação](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- Para obter informações acerca da criação de aplicações na nuvem para utilizar a replicação de Geo ativo, consulte o artigo [Estruturar aplicações de nuvem para continuidade do negócio utilizando a replicação Geo](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- Para obter informações sobre como utilizar Geo-a replicação do Active com agrupamentos de base de dados flexível, consulte o artigo [estratégias de recuperação de falhas de agrupamento flexível](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
- Para uma descrição geral do continurity empresas, consulte o artigo [Descrição geral de continuidade do negócio](sql-database-business-continuity.md)




<!--Image references-->
[1]: ./media/sql-database-geo-replication-failover-portal/failover.png
[2]: ./media/sql-database-geo-replication-failover-portal/secondaries.png
