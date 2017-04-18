<properties 
    pageTitle="Iniciar uma activação pós-falha planeado ou não planeado para uma base de dados do SQL Azure com PowerShell | Microsoft Azure" 
    description="Iniciar uma activação pós-falha planeado ou não planeado para uma base de dados do SQL Azure através do PowerShell" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="08/29/2016"
    ms.author="sstein"/>

# <a name="initiate-a-planned-or-unplanned-failover-for-azure-sql-database-with-powershell"></a>Iniciar uma activação pós-falha planeado ou não planeado para uma base de dados do SQL Azure com PowerShell



> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-geo-replication-failover-portal.md)
- [PowerShell](sql-database-geo-replication-failover-powershell.md)
- [T-SQL](sql-database-geo-replication-failover-transact-sql.md)


Este artigo mostra-lhe como iniciar uma activação pós-falha planeado ou não planeado para uma base de dados SQL com o PowerShell. Para configurar a replicação Geo, consulte o artigo [Geo replicação configurar para a base de dados do SQL Azure](sql-database-geo-replication-powershell.md).



## <a name="initiate-a-planned-failover"></a>Iniciar uma activação planeada pós-falha

Utilize o cmdlet **Set-AzureRmSqlDatabaseSecondary** com o parâmetro **Com falha** para promover uma base de dados secundária para se tornar a base de dados principal novo, despromover primária existente para se tornar um secundário. Esta funcionalidade foi concebida para uma falha na ligação planeada, tal como durante Brocas de recuperação de falhas e requer que a base de dados principal estar disponível.

O comando executa o fluxo de trabalho seguinte:

1. Mude temporariamente replicação para modo síncrono. Isto vai fazer com que todas as operações pendentes ser descarregadas para secundária.

2. Mude as funções das duas bases de dados na parceria de replicação Geo.  

Esta sequência garante que as duas bases de dados são sincronizados antes de mudar as funções e, consequentemente, irá ocorrer sem perda de dados. Existe um período curto durante o qual não ambas as bases de dados estão disponíveis (na ordem de 0 a 25 segundos) enquanto as funções são mudadas. Toda a operação deve ter menos de um minuto para concluir em circunstâncias normais. Para mais informações, consulte o artigo [AzureRmSqlDatabaseSecondary conjunto](https://msdn.microsoft.com/library/mt619393.aspx).




Este cmdlet irá devolver quando o processo de mudar da base de dados secundária para primária é concluído.

O seguinte comando muda as funções da base de dados com o nome "mydb" no servidor "srv2" no grupo de recursos "rg2" para principal. A página principal original ao qual estava ligado "db2" mudará para secundário após as duas bases de dados são totalmente sincronizados.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" –ResourceGroupName "rg2” –ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary -Failover


> [AZURE.NOTE] Em alguns casos raros é possível que a operação não é possível concluir e poderá aparecer não responde. Neste caso, o utilizador pode ligar para o comando de activação pós-falha vigor (não planeado activação pós-falha) e aceitar perda de dados.


## <a name="initiate-an-unplanned-failover-from-the-primary-database-to-the-secondary-database"></a>Iniciar uma não planeado activação pós-falha da base de dados principal para a base de dados secundária


Pode utilizar o cmdlet **Set-AzureRmSqlDatabaseSecondary** com parâmetros **– Activação** e **- AllowDataLoss** para promover uma base de dados secundária para se tornar a nova base de dados principal de uma forma não planeada, forçar a despromoção primária existente para se tornar um secundário, uma vez já não está disponível quando a base de dados principal.

Esta funcionalidade foi concebida para recuperação de falhas quando restaurar disponibilidade da base de dados é fundamental e algumas perda de dados for aceitável. Quando é chamado activação pós-falha forçada, base de dados secundária especificada torna-se a base de dados principal imediatamente e começa a aceitar as transações de escrita. Assim que a base de dados principal original for capaz de restabelecer a ligação com esta nova base de dados principal após a operação de activação pós-falha forçada, uma cópia de segurança utilizarão é disponibilizada na base de dados principal original e a base de dados principal antigo é feita para uma base de dados secundária para a nova base de dados principal; posteriormente, é simplesmente uma réplica da nova página principal.

Mas uma vez que ponto na hora restaurar não é suportada em bases de dados secundárias, se pretender dados de recuperação consolidados à base de dados principal antiga que não tivesse sido replicadas para a nova base de dados principal, deverá contrate CSS para restaurar uma base de dados para a cópia de segurança do registo conhecidos.

> [AZURE.NOTE] Se o comando é emitido quando principal e secundário são online a página principal antiga irão tornar-se secundária nova imediatamente sem sincronização de dados. Se estiver a página principal poderão ocorrer a consolidar transações quando o comando é emitido algumas perda de dados.


Se a base de dados principal tem vários secundários que o comando parcialmente será bem sucedida. Secundário no qual foi executado o comando irão tornar-se principal. A página principal antiga no entanto permanecerá principal, ou seja, os primários duas da terminam estado inconsistente e ligado através de uma ligação de replicação suspenso. O utilizador terá que reparar manualmente esta configuração utilizando uma API "Remover secundário" em qualquer uma destas bases de dados principal.


O seguinte comando muda as funções da base de dados com o nome "mydb" para principal quando a página principal estiver indisponível. A página principal original ao qual estava ligado "mydb" mudará para secundário assim que estiver novamente online. Nesse momento a sincronização poderá resultar na perda de dados.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" –ResourceGroupName "rg2” –ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary –Failover -AllowDataLoss




## <a name="next-steps"></a>Próximos passos   

- Depois de falha na ligação, certifique-se de que os requisitos de autenticação para o seu servidor e a base de dados estão configurados na nova página principal. Para obter detalhes, consulte o artigo [segurança da base de dados SQL após a recuperação de falhas](sql-database-geo-replication-security-config.md).
- Para saber recuperar depois de uma falhas utilizando a replicação Geo ativo, que incluindo pre e registar os passos de recuperação e efetuar uma pesquisa de recuperação de falhas, consulte o artigo [Brocas de recuperação de falhas](sql-database-disaster-recovery.md)
- Para uma mensagem do blogue Sasha Nosov sobre Geo-replicação do Active, consulte o artigo [destaque de novas capacidades Geo replicação](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- Para obter informações acerca da criação de aplicações na nuvem para utilizar a replicação de Geo ativo, consulte o artigo [Estruturar aplicações de nuvem para continuidade do negócio utilizando a replicação Geo](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- Para obter informações sobre como utilizar Geo-a replicação do Active com agrupamentos de base de dados flexível, consulte o artigo [estratégias de recuperação de falhas de agrupamento flexível](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
- Para uma descrição geral do continurity empresas, consulte o artigo [Descrição geral de continuidade do negócio](sql-database-business-continuity.md)
