<properties 
    pageTitle="Iniciar uma activação pós-falha planeado ou não planeado para uma base de dados do SQL Azure com Transact-SQL | Microsoft Azure" 
    description="Iniciar uma activação pós-falha planeado ou não planeado para uma base de dados do SQL Azure utilizando o Transact-SQL" 
    services="sql-database" 
    documentationCenter="" 
    authors="CarlRabeler" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management"
    ms.date="08/29/2016"
    ms.author="carlrab"/>

# <a name="initiate-a-planned-or-unplanned-failover-for-azure-sql-database-with-transact-sql"></a>Iniciar uma activação pós-falha planeado ou não planeado para uma base de dados do SQL Azure com Transact-SQL


> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-geo-replication-failover-portal.md)
- [PowerShell](sql-database-geo-replication-failover-powershell.md)
- [T-SQL](sql-database-geo-replication-failover-transact-sql.md)


Este artigo mostra-lhe como iniciar activação pós-falha numa base de dados do SQL secundário utilizando o Transact-SQL. Para configurar a replicação Geo, consulte o artigo [Geo replicação configurar para a base de dados do SQL Azure](sql-database-geo-replication-transact-sql.md).



Para iniciar activação pós-falha, precisa do seguinte:

- Um início de sessão que está a página principal, Gestor de BD tem db_ownership da base de dados local que irá geo-replicação e ser Gestor de BD no parceiro servidor (es) para o qual irá configurar Geo replicação.
- SQL Server Management Studio (SSMS)


> [AZURE.IMPORTANT] É recomendado utilizar sempre a versão mais recente do Management Studio para permanecer sincronizados com atualizações para o Microsoft Azure e base de dados SQL. [Atualizar o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).




## <a name="initiate-a-planned-failover-promoting-a-secondary-database-to-become-the-new-primary"></a>Iniciar uma activação planeada de pós-falha promover uma base de dados secundária para se tornar a nova página principal

Pode utilizar a instrução **ALTER DATABASE** para promover uma base de dados secundária para se tornar a nova base de dados principal de uma forma planeada, despromover primária existente para se tornar um secundário. Esta declaração é executada na base de dados principal no servidor lógico de base de dados do Azure SQL em que reside replicadas geo secundário base de dados que está a ser promovido. Esta funcionalidade foi concebida para planeada falha na ligação, tais como durante Brocas DR e requer que a base de dados principal estar disponível.

O comando executa o fluxo de trabalho seguinte:

1. Muda temporariamente replicação para modo síncrono, a causar todas as operações pendentes ser descarregadas para secundária e bloquear todas as novas transações;

2. As funções das duas bases de dados na parceria de replicação Geo parâmetros.  

Esta sequência garante que as duas bases de dados são sincronizados antes de mudar as funções e, consequentemente, irá ocorrer sem perda de dados. Existe um período curto durante o qual não ambas as bases de dados estão disponíveis (na ordem de 0 a 25 segundos) enquanto as funções são mudadas. Se a base de dados principal tem várias bases de dados secundárias, o comando será automaticamente reconfigurar os outros secundários para ligar para a nova página principal.  Toda a operação deve ter menos de um minuto para concluir em circunstâncias normais. Para mais informações, consulte o artigo [Alterar da base de dados (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) e [Camadas de serviços](sql-database-service-tiers.md).


Utilize os passos seguintes para iniciar uma activação planeada pós-falha.

1. No Management Studio, ligar para o servidor de lógico de base de dados do Azure SQL em que reside uma replicadas geo secundário base de dados.

2. Abra a pasta de bases de dados, expanda a pasta de **Bases de dados do sistema** , com o botão direito no **modelo global**e, em seguida, clique em **Nova consulta**.

3. Utilize a seguinte instrução **ALTER DATABASE** para mudar da base de dados secundária para a função principal.

        ALTER DATABASE <MyDB> FAILOVER;

4. Clique em **Executar** para executar a consulta.

>[AZURE.NOTE] Em casos raros, é possível que a operação não é possível concluir e pode aparecer presa. Neste caso, o utilizador pode executar o comando de activação pós-falha forçar e aceitar perda de dados.


## <a name="initiate-an-unplanned-failover-from-the-primary-database-to-the-secondary-database"></a>Iniciar uma não planeado activação pós-falha da base de dados principal para a base de dados secundária

Pode utilizar a instrução **ALTER DATABASE** para promover uma base de dados secundária para se tornar a nova base de dados principal de uma forma não planeada, forçar a despromoção primária existente para se tornar um secundário, uma vez quando o databse primária já não está disponível. Esta declaração é executada na base de dados principal no servidor lógico de base de dados do Azure SQL em que reside replicadas geo secundário base de dados que está a ser promovido.

Esta funcionalidade foi concebida para recuperação de falhas quando restaurar disponibilidade da base de dados é fundamental e algumas perda de dados for aceitável. Quando é chamado activação pós-falha forçada, base de dados secundária especificada torna-se a base de dados principal imediatamente e começa a aceitar as transações de escrita. Assim que a base de dados principal original for capaz de restabelecer a ligação com esta nova base de dados principal, uma cópia de segurança utilizarão é disponibilizada na base de dados principal original e a base de dados principal antigo é feita para uma base de dados secundária para a nova base de dados principal; posteriormente, é simplesmente uma réplica a sincronização da nova página principal.

No entanto, uma vez que ponto na hora restaurar não é suportada nas bases de dados secundárias, se o utilizador pretenda recuperar dados consolidados à base de dados principal antiga que não tivesse sido replicadas para a nova base de dados principal antes da activação forçada pós-falha ocorreu, o utilizador terá participar suporte para recuperar isto dados perdidos.

Se a base de dados principal tem várias bases de dados secundárias, o comando será automaticamente reconfigurar os outros secundários para ligar para a nova página principal.

Utilize os passos seguintes para iniciar uma activação não planeado pós-falha.

1. No Management Studio, ligar para o servidor de lógico de base de dados do Azure SQL em que reside uma replicadas geo secundário base de dados.

2. Abra a pasta de bases de dados, expanda a pasta de **Bases de dados do sistema** , com o botão direito no **modelo global**e, em seguida, clique em **Nova consulta**.

3. Utilize a seguinte instrução **ALTER DATABASE** para mudar da base de dados secundária para a função principal.

        ALTER DATABASE <MyDB>   FORCE_FAILOVER_ALLOW_DATA_LOSS;

4. Clique em **Executar** para executar a consulta.

>[AZURE.NOTE] Se o comando é emitido quando principal e secundário são online a página principal antiga irão tornar-se secundária nova imediatamente sem sincronização de dados. Se estiver a página principal poderão ocorrer a consolidar transações quando o comando é emitido algumas perda de dados.



## <a name="next-steps"></a>Próximos passos   

- Depois de falha na ligação, certifique-se de que os requisitos de autenticação para o seu servidor e a base de dados estão configurados na nova página principal. Para obter detalhes, consulte o artigo [segurança da base de dados SQL após a recuperação de falhas](sql-database-geo-replication-security-config.md).
- Para saber recuperar depois de uma falhas utilizando a replicação Geo ativo, que incluindo pre e registar os passos de recuperação e efetuar uma pesquisa de recuperação de falhas, consulte o artigo [Recuperação de falhas](sql-database-disaster-recovery.md)
- Para uma mensagem do blogue Sasha Nosov sobre Geo-replicação do Active, consulte o artigo [destaque de novas capacidades Geo replicação](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- Para obter informações acerca da criação de aplicações na nuvem para utilizar a replicação de Geo ativo, consulte o artigo [Estruturar aplicações de nuvem para continuidade do negócio utilizando a replicação Geo](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- Para obter informações sobre como utilizar Geo-a replicação do Active com agrupamentos de base de dados flexível, consulte o artigo [estratégias de recuperação de falhas de agrupamento flexível](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
- Para uma descrição geral do continurity empresas, consulte o artigo [Descrição geral de continuidade do negócio](sql-database-business-continuity.md)
