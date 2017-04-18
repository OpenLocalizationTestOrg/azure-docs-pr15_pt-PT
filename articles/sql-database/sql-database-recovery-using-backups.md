<properties
   pageTitle="Continuidade de negócios em nuvem - restaurar uma base de dados eliminado - base de dados SQL | Microsoft Azure"
   description="Saiba mais sobre restaurar em qualquer altura, que permite-lhe recuperar uma base de dados do SQL Azure um ponto anterior no tempo (até 35 dias)."
   services="sql-database"
   documentationCenter=""
   authors="stevestein"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/01/2016"
   ms.author="sstein"/>

# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>Recuperar uma base de dados do Azure SQL com cópias de segurança da base de dados automática

Base de dados SQL fornece três opções para utilizar a [que base de dados SQL automatizado cópias de segurança](sql-database-automated-backups.md)de recuperação de base de dados. Pode restaurar uma base de dados a partir das cópias de segurança iniciado pelo serviço durante o [período de retenção](sql-database-service-tiers.md) para:

- Uma nova base de dados no mesmo servidor lógico recuperado para um ponto especificado em tempo dentro do período de retenção. 
- Uma base de dados no mesmo servidor lógico recuperado para o tempo de eliminação para uma base de dados eliminado.
- Uma nova base de dados em qualquer servidor lógico no qualquer região recuperada para as mais recentes cópias de segurança diárias no armazenamento de BLOBs de replicadas geo (GRS RT).

Também pode utilizar a [base de dados SQL automatizado cópias de segurança](sql-database-automated-backups.md) para criar uma [base de dados copiar](sql-database-copy.md) em qualquer servidor lógico qualquer região em que está consistentes com a base de dados do SQL atual. Pode utilizar a cópia da base de dados e [Exportar para um BACPAC](sql-database-export.md) para arquivar uma cópia consistente de uma base de dados para a longo prazo armazenamento além do período de retenção ou para transferir uma cópia da base de dados para um local ou Azure VM instância do SQL Server.

## <a name="recovery-time"></a>Tempo de recuperação

O tempo de recuperação para restaurar uma base de dados com cópias de segurança da base de dados automática é afectado por um número de fatores: 
 - O tamanho da base de dados
 - O nível de desempenho da base de dados
 - O número de registos de transações envolvidos
 - A quantidade de atividade que precisa de ser reproduzidos para recuperar para o ponto de restauro
 - A largura de banda de rede se for o restauro para uma região diferente 
 - O número de pedidos em simultâneo restaurar a ser processadas na região de destino. 
 
 Para uma base de dados muito grande e/ou ativa o restauro poderá demorar algumas horas. Se existir indisponibilidade prolongada numa região, é possível que vai ser grandes quantidades de pedidos de Geo restaurar a ser processadas pelos noutras regiões. Se existirem um grande número de pedidos de Isto pode aumentar o tempo de recuperação de bases de dados nesse região. A maioria das bases de dados restaura concluída dentro de 12 horas.

 Não existe sem a funcionalidade incorporada para em volume restaurar. O [base de dados do SQL Azure: recuperação de servidor completo](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) script é um exemplo de uma forma de realizar esta tarefa.

> [AZURE.IMPORTANT] Para recuperar utilizando cópias de segurança automatizadas, tem de ser um membro da função Contribuinte do SQL Server na subscrição ou ser o proprietário da subscrição. Pode recuperar utilizando o portal Azure PowerShell ou REST API. Não é possível utilizar Transact-SQL. 

## <a name="point-in-time-restore"></a>Restaurar em qualquer altura

Em qualquer altura restaurar permite-lhe restaurar uma base de dados existente como uma nova base de dados para um ponto anterior no tempo no mesmo servidor lógico utilizando a [que base de dados SQL automatizado cópias de segurança](sql-database-automated-backups.md). Não é possível substituir a base de dados existente. Pode restaurar um ponto anterior na hora utilizando o [Azure portal](sql-database-point-in-time-restore-portal.md), [PowerShell](sql-database-point-in-time-restore-powershell.md) ou a [REST API](https://msdn.microsoft.com/library/azure/mt163685.aspx).

> [AZURE.SELECTOR]
- [Restauro no momento: Portal Azure](sql-database-point-in-time-restore-portal.md)
- [Restaurar em qualquer altura: PowerShell](sql-database-point-in-time-restore-powershell.md)

A base de dados pode ser restaurado a qualquer nível de desempenho ou elásticos agrupamento. É necessário para se certificar de que tem uma quota DTU suficiente no conjunto de flexível ou do servidor lógicos. Tenha em atenção que o restauro cria uma nova base de dados e de que o nível de camadas e o desempenho do serviço da base de dados restaurado pode ser diferente que não seja o estado atual da base de dados dinâmico. Depois de concluído, a base de dados restaurado é uma normal completamente acessível online base de dados cobrada taxas normais com base no seu nível de camadas e o desempenho do serviço. Não assumir taxas até o restauro de base de dados está concluído.

É geralmente restaurar uma base de dados a um ponto de earler para fins de recuperação. Ao fazê-lo, pode trate a base de dados restaurado como substituição para a base de dados original ou utilizá-la para obter dados a partir de e, em seguida, atualize a base de dados original. 

- ***Substituição de base de dados:*** Se a base de dados restaurado destina-se como substituição para a base de dados original, deve verificar o nível de desempenho e/ou camada de serviços são adequada e dimensionar a base de dados se for necessário. Pode mudar o nome da base de dados original e, em seguida, atribuir a base de dados restaurada o nome original utilizando o comando ALTER DATABASE na T-SQL. 
- ***Recuperação de dados:*** Se planeia obter os dados da base de dados restaurado para recuperar a partir de um erro de utilizador ou a aplicação, separadamente terá de escrever e executar qualquer scripts de recuperação de dados que precisa para extrair dados de base de dados restaurada à base de dados original. Apesar da operação de restauro poderá demorar muito tempo a concluir, a base de dados restaurar estarão visível na lista de base de dados ao longo. Se eliminar a base de dados durante o restauro, irá cancele a operação e não será cobrada pelo base de dados que não foi concluída a restaurar. 

Para obter informações detalhadas sobre como utilizar em qualquer altura restaurar para recuperar a partir de erros de aplicações e de utilizador, consulte o artigo [ponto-in-Time restaurar](sql-database-recovery-using-backups.md#point-in-time-restore)

## <a name="deleted-database-restore"></a>Restauro de base de dados eliminada

Restauro de base de dados eliminados permite-lhe restaurar uma base de dados eliminado para o tempo de eliminação para uma base de dados eliminada no mesmo servidor lógico utilizando a [que base de dados SQL automatizado cópias de segurança](sql-database-automated-backups.md). 

> [AZURE.IMPORTANT] Se eliminar uma instância de servidor de base de dados do Azure SQL, todos das suas bases de dados são também eliminados e não podem ser recuperados. Não existe suporte para restaurar a um servidor eliminado neste momento.

Pode utilizar o mesmo ou um novo nome de base de dados para a base de dados restaurado. Pode utilizar o [Azure portal](sql-database-restore-deleted-database-portal.md), [PowerShell](sql-database-restore-deleted-database-powershell.md) ou a [resto (createMode = restaurar)](https://msdn.microsoft.com/library/azure/mt163685.aspx). 

> [AZURE.SELECTOR]
- [Eliminados restauro de base de dados: Azure portal](sql-database-restore-deleted-database-portal.md)
- [Eliminados restauro de base de dados: PowerShell](sql-database-restore-deleted-database-powershell.md)

## <a name="geo-restore"></a>Restaurar geo

Restaurar geo permite-lhe restaurar uma base de dados do SQL em qualquer servidor no qualquer região Azure a partir de replicadas geo mais recente [cópia de segurança automatizada diária](sql-database-automated-backups.md). Restaurar geo utiliza uma cópia de segurança geo redundantes como respectiva origem e pode ser utilizado para recuperar uma base de dados, mesmo se a base de dados ou o Centro de dados está inacessível devido a uma falha. Pode utilizar o [Azure portal](sql-database-geo-restore-portal.md), [PowerShell](sql-database-geo-restore-powershell.md), ou o [resto (createMode = recuperação)](https://msdn.microsoft.com/library/azure/mt163685.aspx) 

> [AZURE.SELECTOR]
- [Restauro Geo: Portal Azure](sql-database-geo-restore-portal.md)
- [Restaurar Geo: PowerShell](sql-database-geo-restore-powershell.md)

Restaurar GEO é a opção de recuperação predefinida quando a base de dados está disponível devido a um incidente na região onde a base de dados está alojado. Se um incidente de grande escala de uma região resulta num indisponibilidade da sua aplicação de base de dados, pode utilizar Geo restaurar para restaurar uma base de dados da cópia de segurança mais recente para um servidor em qualquer outro região. Todas as cópias de segurança são replicadas geo e que podem ter um atraso entre quando a cópia de segurança é tomadas e geo replicadas para o Azure blob numa região diferente. Este atraso pode ser por excesso para uma hora para um eventualidade podem existir para cima a perda de dados de 1 hora, ou seja, RPO de até 1 hora. A imagem seguinte mostra restauro da base de dados a partir da última cópia de segurança diária.

![restaurar geo](./media/sql-database-geo-restore/geo-restore-2.png)

Para obter informações detalhadas sobre como utilizar Geo restaurar para recuperar a partir de uma falha, consulte o artigo [recuperar a partir de uma falha de](sql-database-disaster-recovery.md)

> [AZURE.IMPORTANT] Enquanto Geo restaurar está disponível com todas as camadas de serviço, é mais básico das soluções de recuperação de falhas disponíveis na base de dados SQL com a célula mais longa RPO e tempo de recuperação de estimativa (ERTER). Para bases de dados básicos com o tamanho máximo de 2 GB Geo-restauro fornece uma solução de DR razoável com um ERTER de 12 horas. Para padrão ou Premium bases de dados maiores, se significativamente mais curtos vezes de recuperação forem pretendidas ou para reduzir a probabilidade de perda de dados deverá tomar em consideração utilizando a replicação-Geo ativo. Geo-a replicação do Active oferece uma muito inferior RPO e ERTER como apenas requer iniciar uma activação pós-falha para um continuamente replicado secundário. Para obter detalhes, consulte o artigo [Geo-a replicação do Active](sql-database-geo-replication-overview.md).

## <a name="programmatically-performing-recovery-using-automated-backups"></a>Efetuar através de programação recuperação utilizando cópias de segurança automatizadas

Como outros fabricantes referidos acima, addiition ao portal do Azure, recuperação de base de dados pode ser efetuada programmically utilizando o PowerShell do Azure e REST API. As tabelas abaixo descrevem o conjunto de comandos disponíveis.

### <a name="powershell"></a>PowerShell

|Cmdlet|Descrição|
|------|-----------|
|[Get-AzureRmSqlDatabase](https://msdn.microsoft.com/en-us/library/azure/mt603648.aspx)|Obtém um ou mais bases de dados.|
|[Get-AzureRMSqlDeletedDatabaseBackup](https://msdn.microsoft.com/en-us/library/azure/mt693387.aspx)|Obtém a uma base de dados eliminado pode restaurar.|
|[Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388.aspx)|Obtém uma cópia de segurança geo redundantes de uma base de dados.|
|[Restaurar AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx)|Restaura uma base de dados do SQL.|
||||

### <a name="rest-api"></a>REST API

|API|Descrição|
|---|-----------|
|[RESTO (createMode = recuperação)](https://msdn.microsoft.com/library/azure/mt163685.aspx)|Restaura uma base de dados|
|[Começar a criar ou actualizar o estado de base de dados](https://msdn.microsoft.com/library/azure/mt643934.aspx)|Devolve o estado durante uma operação de restauro|
||||



## <a name="summary"></a>Resumo

Cópias de segurança automáticas Proteja as bases de dados de utilizador e erros de aplicação, eliminação de base de dados acidentais e prolongada de corrente. Esta solução zero custo zero-administração está disponível com todas as bases de dados do SQL. 

## <a name="next-steps"></a>Próximos passos

- Para obter uma descrição de continuidade do negócio e cenários, consulte o artigo [Descrição geral de continuidade do negócio](sql-database-business-continuity.md)
- Para saber mais sobre cópias de segurança da base de dados do SQL Azure automatizado, consulte o artigo [da base de dados do SQL automatizado cópias de segurança](sql-database-automated-backups.md)
- Para saber mais sobre as opções de recuperação mais rápidas, consulte o artigo [Replicação de Geo ativo](sql-database-geo-replication-overview.md)  
- Para saber como utilizar as cópias de segurança automatizadas para o arquivo, consulte o artigo [Copiar de base de dados](sql-database-copy.md)
