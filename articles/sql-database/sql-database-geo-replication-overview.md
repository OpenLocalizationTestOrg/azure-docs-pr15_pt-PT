<properties
    pageTitle="Replicação de Geo ativa para a base de dados Azure SQL"
    description="Replicação do Geo Active permite-lhe 4 réplicas da base de dados em qualquer um dos Azure centros de dados de configuração."
    services="sql-database"
    documentationCenter="na"
    authors="stevestein"
    manager="jhubbard"
    editor="monicar" />


<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="NA"
    ms.date="09/26/2016"
    ms.author="sstein" />

# <a name="overview-sql-database-active-geo-replication"></a>Descrição geral: Da base de dados ativa Geo-a replicação SQL

Replicação do Geo Active permite-lhe configurar até quatro legíveis secundários bases de dados nas localizações de centro de dados do mesmo ou diferente (regiões). Bases de dados secundárias estão disponíveis para consultar e para activação pós-falha no caso de uma falha de centro de dados ou a impossibilidade de ligar à base de dados principal.

>[AZURE.NOTE] Geo-a replicação do Active (secundários legíveis) está agora disponível para todas as bases de dados em todas as camadas de serviço. Na de 2017 de Abril de, vai ser foi removido o tipo de secundário que não sejam legíveis e bases de dados que não sejam legíveis existentes serão automaticamente atualizadas para secundários legíveis.

 Pode configurar a ativo replicação de Geo utilizando o [Azure portal](sql-database-geo-replication-portal.md), [PowerShell](sql-database-geo-replication-powershell.md), [Transact-SQL](sql-database-geo-replication-transact-sql.md), ou o [REST API - criar ou actualizar base de dados](https://msdn.microsoft.com/library/azure/mt163685.aspx).

> [AZURE.SELECTOR]
- [Configurar: Portal Azure](sql-database-geo-replication-portal.md)
- [Configurar: PowerShell](sql-database-geo-replication-powershell.md)
- [Configurar: T-SQL](sql-database-geo-replication-transact-sql.md)

Se para qualquer motivo seu falha de base de dados principal, ou simplesmente tem de ser reencaminhado offline, que pode *activação pós-falha* qualquer uma das suas bases de dados secundárias. Quando é activado activação pós-falha a uma das bases de dados secundárias, todos os outros secundários são automaticamente ligados para a nova página principal.

Pode activação pós-falha para um secundário utilizando o [portal do Azure](sql-database-geo-replication-failover-portal.md), [PowerShell](sql-database-geo-replication-failover-powershell.md), [Transact-SQL](sql-database-geo-replication-failover-transact-sql.md), de [API REST - planeado activação pós-falha](https://msdn.microsoft.com/ibrary/azure/mt575007.aspx)ou [REST API - activação pós-falha não planeado](https://msdn.microsoft.com/library/azure/mt582027.aspx).


> [AZURE.SELECTOR]
- [Activação pós-falha: Portal Azure](sql-database-geo-replication-failover-portal.md)
- [Activação pós-falha: PowerShell](sql-database-geo-replication-failover-powershell.md)
- [Activação pós-falha: T-SQL](sql-database-geo-replication-failover-transact-sql.md)

Depois de falha na ligação, certifique-se de que os requisitos de autenticação para o seu servidor e a base de dados estão configurados na nova página principal. Para obter detalhes, consulte o artigo [segurança da base de dados SQL após a recuperação de falhas](sql-database-geo-replication-security-config.md).


A funcionalidade de replicação do Geo Active implementa um mecanismos para fornecer redundância de base de dados dentro da mesma região do Microsoft Azure ou em diferentes regiões (geo redundância). Replicação do Geo Active modo assíncrono replica consolidadas transações a partir de uma base de dados para até quatro cópias da base de dados em servidores diferentes, utilizando instantâneo consolidada isolamento (RCSI) para o isolamento de leitura. Quando ativa Geo-replicação está configurado, é criada uma base de dados secundária no servidor especificado. Base de dados original torna-se a base de dados principal. A base de dados principal replica modo assíncrono transações consolidadas para cada uma das bases de dados secundárias. Apenas as transações completos são replicadas. Enquanto a uma determinada altura, a base de dados secundária poderá ser ligeiramente atrás da base de dados principal e, em seguida, os dados secundários são garantir nunca tenha transações parciais. Os dados específicos do RPO podem ser encontrados em [Descrição geral de continuidade do negócio](sql-database-business-continuity.md).

Uma das vantagens principais do Active Geo replicação é que fornece uma solução de recuperação de falhas de nível de base de dados com o tempo de recuperação baixa. Ao colocar a base de dados secundária num servidor numa região diferente, adicione resistência máxima para a sua aplicação. Publicação em região redundância permite que as aplicações recuperar a partir de uma perda permanente de um centro de dados inteiro ou partes de um centro de dados causados por catástrofes naturais, grave erros humanos ou actos maliciosos. A figura seguinte mostra que um exemplo de Geo-replicação do Active configurado com uma primária na região América do Norte Central-nos e secundário na região Sul Central-nos.

![Relação geo replicação](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

Outra vantagem de chave é que as bases de dados secundárias são legíveis e podem ser utilizados para descarregar cargas de trabalho só de leitura como tarefas de elaboração de relatórios. Se apenas pretender utilizar a base de dados secundária para balanceamento de carga, pode criá-lo na mesma região como a principal. Criar um secundário na mesma região, não aumentar resistência a aplicação a falhas grave.  

Outras situações onde pode ser utilizada Geo-a replicação do Active incluem:

- **Migração de base de dados**: pode utilizar Geo-a replicação do Active para migrar uma base de dados de um servidor para outro online com o tempo de inatividade mínimo.
- **Atualiza os de aplicações**: pode criar um secundário extra como uma cópia de volta falhas durante as actualizações de aplicação.

Para alcançar continuidade do negócio real, adicionar redundância de base de dados entre centros de dados é apenas uma parte da solução. Recuperar uma aplicação (serviço) fim-a-ponto depois de uma falha grave requer recuperação de todos os componentes que constituem o serviço e quaisquer serviços dependentes. Exemplos destes componentes incluem o software de cliente (por exemplo, num browser com um JavaScript personalizado), extremidades frente da web, armazenamento e DNS. É fundamental que todos os componentes são e são para as mesmo falhas e ficam disponíveis no prazo de objectivo de tempo de recuperação (RTO) da sua aplicação. Por isso, tem de identificar todos os serviços dependentes e compreender as garantias e capacidades fornecem. Em seguida, tem de executar passos adequados para se certificar de que as funções de serviço durante a mudança dos serviços que depende. Para mais informações sobre soluções de estruturar para recuperação de falhas, consulte o artigo [Estruturar soluções de nuvem para falhas recuperação utilizando ativo Geo-a replicação](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="active-geo-replication-capabilities"></a>Capacidades de replicação Geo ativas
A funcionalidade de replicação do Geo Active fornece as seguintes capacidades essenciais:

- **A replicação automática assíncrona**: só pode criar uma base de dados secundária adicionando a base de dados existente. Secundária apenas pode ser criada num servidor de base de dados do Azure SQL diferente. Depois de criada, a base de dados secundária é povoada com os dados copiados da base de dados principal. Este processo é conhecido como propagar. Depois de base de dados secundária tenha sido criado e propagada, atualizações à base de dados principal são modo assíncrono replicadas para a base de dados secundária automaticamente. A replicação assíncrona significa que são consolidadas transações da base de dados principal, antes de estes são replicadas para a base de dados secundária. 

- **Várias bases de dados secundárias**: duas ou mais bases de dados secundárias aumentem redundância e o nível de proteção para a base de dados principal e uma aplicação. Se existirem várias bases de dados secundárias, a aplicação permanece protegida, mesmo se uma das bases de dados secundárias falha. Se existir apenas uma base de dados secundária e falhar, a aplicação está exposta risco mais elevado até que seja criado uma nova base de dados secundária.

- **Bases de dados secundárias legíveis**: uma aplicação possa aceder uma base de dados secundária para operações de só de leitura utilizando os principais de segurança do mesmo ou diferente utilizados para aceder a base de dados principal. Trabalhar com as bases de dados secundárias no modo de isolamento instantâneo para garantir a replicação das atualizações do computador primário (a reprodução de registo) não é atrasada por consultas executadas no secundária.

>[AZURE.NOTE] Reprodução de registo estiver atrasada na base de dados secundária se existem atualizações de esquema que está a receber do principal que requerem um cadeado de esquema da base de dados secundária.

- **Ativa geo-replicação de bases de dados do conjunto de dados flexível**: Geo-a replicação do Active pode ser configurada para quaisquer bases de dados no conjunto qualquer flexível da base de dados. Pode ser a base de dados secundária no outro conjunto de dados da base de dados flexível. Para normais bases de dados secundária pode ser um agrupamento e vice de versa flexível da base de dados desde que as camadas de serviço são os mesmos. 

- **Nível de desempenho configuráveis da base de dados secundária**: uma base de dados secundária pode ser criada com o nível de desempenho inferior a página principal. Principais e secundários bases de dados são necessários para ter a mesma camada de serviço. Esta opção não é recomendada para as aplicações com atividade de escrita da base de dados alta uma vez que o tempo de desfasamento aumentar a replicação aumenta o risco de perda de dados substanciais após uma activação pós-falha. Além disso, após activação pós-falha desempenho a aplicação é afectado até a nova página principal é atualizada para um nível superior do desempenho. O gráfico de percentagem do registo IO no Azure portal fornece uma boa forma para estimar o nível de desempenho mínimas da secundária que é necessário para suportar a carga de replicação. Por exemplo, se a base de dados principal for P6 (1000 DTU) e o registo de percentagem IO está 50% secundária tem de estar, pelo menos, E4 (500 DTU). Também pode obter os dados do registo IO utilizar [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) ou [sys.dm_db_resource_stats]( https://msdn.microsoft.com/library/dn800981.aspx) vistas de base de dados.  Para mais informações sobre os níveis de desempenho da base de dados SQL, consulte o artigo [Opções de base de dados SQL e o desempenho](sql-database-service-tiers.md). 

- **Controlado pelo utilizador activação e reposição de recurso**: uma base de dados secundária pode explicitamente mudar para a função primária em qualquer altura pela aplicação ou pelo utilizador. Durante uma falha de real a opção "não planeada" deve ser utilizada, que promove imediatamente um secundário a página principal. Quando a página principal falha recupera e está disponível novamente, o sistema assinala a página principal como um secundário recuperada automaticamente e colocá-lo atualizados com a nova página principal. Devido à natureza assíncrona replicação, uma pequena quantidade de dados pode perder durante activações pós-falha não planeada se um principal falhar antes de-replica as alterações mais recentes para secundária. Quando um principal com vários secundários Falha ao longo do, o sistema automaticamente reconfigura as relações de replicação e ligações os secundários restantes para a página principal recentemente promovida sem necessidade de qualquer intervenção do utilizador. Depois da indisponibilidade que causaram o activação pós-falha é atenuada, pode ser conveniente para devolver a aplicação à região de principal. Para fazê-lo, o comando activação pós-falha deve ser chamado com a opção "planeada". 

- **Manter as credenciais e as regras da firewall sincronizado**: Recomendamos que pode ser replicada utilizar [as regras da firewall da base de dados](sql-database-firewall-configure.md) de replicadas geo por isso, estas regras de bases de dados com a base de dados para garantir que todas as bases de dados secundárias tenham as mesmas regras de firewall como a principal. Esta abordagem elimina a necessidade de clientes para manualmente configurar e manter as regras de firewall nos servidores tanto as bases de dados principais e secundários de alojamento. Da mesma forma, usar [contidas utilizadores de base de dados](sql-database-manage-logins.md) para o acesso a dados garante bases de dados principais e secundários tem sempre o mesmo utilizador credenciais-lo durante uma activação pós-falha, não existe nenhuma interrupções devido a erros de correspondência com os inícios de sessão e palavras-passe. Com a adição do [Azure Active Directory](../active-directory/active-directory-whatis.md), clientes podem gerir o acesso do utilizador para as bases de dados principais e secundários e eliminando a necessidade de gerir credenciais em completamente de bases de dados.

## <a name="upgrading-or-downgrading-a-primary-database"></a>Atualizar ou mudar uma base de dados principal
Pode atualizar ou alterar uma base de dados principal para um nível de desempenho diferentes (dentro da mesma camada de serviço) sem desligar quaisquer bases de dados secundárias. Ao atualizar, recomendamos que actualizar a base de dados secundária pela primeira vez e, em seguida, a página principal. Quando mudar, inverter a ordem: alterar a página principal pela primeira vez e, em seguida, alterar secundária. 

A base de dados secundária tem de ser na camada serviço mesmo como a principal, pelo que migrar a base de dados principal para uma camada de serviços diferente requer que a ligação de replicação geo terminar e mudar o nome da base de dados secundária ou simplesmente largá-lo. Em seguida, migrar a página principal para a nova camada de serviço e reconfigurar geo replicação. O novo secundário será criado automaticamente com o mesmo nível de desempenho como a principal por predefinição.

## <a name="preventing-the-loss-of-critical-data"></a>Impedir a perda de dados críticas
Devido a latência alta das redes alargada, cópia contínua utiliza um dispositivo de replicação assíncrona. Replicação assíncrona faz com perda de alguns dados obrigatório se ocorrer uma falha. No entanto, algumas aplicações poderão requerem sem perda de dados. Para proteger estas atualizações críticas, um programador de aplicação pode ligar o procedimento de sistema [sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx) imediatamente após a consolidar a transação. Chamar **sp_wait_for_database_copy_sync** blocos tópico de chamada até que a última transação consolidada tenha sido replicada à base de dados secundária. O procedimento irá Aguarde até que todas as operações em fila tem sido confirmadas pela base de dados secundária. **sp_wait_for_database_copy_sync** está confinado a uma ligação de cópia contínua específica. Este procedimento pode ligar a qualquer utilizador com os direitos de ligação à base de dados principal.

>[AZURE.NOTE] Atrasos causados por uma chamada de procedimento **sp_wait_for_database_copy_sync** podem ser significativo. O atraso depende do tamanho do comprimento de registo da transação no momento e esta chamada não devolver até que o registo completo é replicado. Evite que entra em contacto este procedimento, a menos que realmente necessário.

## <a name="programmatically-managing-active-geo-replication"></a>Gerir através de programação Geo-a replicação do Active

Tal como é abordado anteriormente, Geo-a replicação do Active também pode ser gerida através de programação utilizando o PowerShell do Azure e REST API. As tabelas seguintes descrevem o conjunto de comandos disponíveis.

- **API do Gestor de recursos do Azure e segurança baseado em funções**: Geo-a replicação do Active inclui um conjunto de [APIs de Gestor de recursos do Azure]( https://msdn.microsoft.com/library/azure/mt163571.aspx) para gestão, incluindo [os cmdlets do PowerShell com base no Gestor de recursos do Azure](sql-database-geo-replication-powershell.md). Estas APIs necessitam da utilização de grupos de recursos e suportam baseado em funções segurança (RBCA). Para obter mais informações sobre como implementar o access funções consulte [Azure Role-Based o controlo de acesso](../active-directory/role-based-access-control-configure.md).

>[AZURE.NOTE] Muitas novas funcionalidades de Geo-replicação do Active só são suportadas com o [Gestor de recursos do Azure](../azure-resource-manager/resource-group-overview.md) com base no [Azure SQL REST API](https://msdn.microsoft.com/library/azure/mt163571.aspx) e [os cmdlets do PowerShell de base de dados do SQL Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx). (Clássico) REST API] (https://msdn.microsoft.com/library/azure/dn505719.aspx) e [cmdlets (clássicos) da base de dados do SQL Azure](https://msdn.microsoft.com/library/azure/dn546723.aspx) são suportadas para compatibilidade com versões anteriores, de modo utilizando as APIs com base no Gestor de recursos do Azure são recomendadas. 


### <a name="transact-sql"></a>O Transact-SQL

|Comando|Descrição|
|-------|-----------|
|[ALTERAR a base de dados (base de dados do Azure SQL)](https://msdn.microsoft.com/library/mt574871.aspx)|Utilizar o argumento de adicionar servidor ligado SECUNDÁRIO para criar uma base de dados secundária para uma base de dados existente e a replicação de dados é iniciado|
|[ALTERAR a base de dados (base de dados do Azure SQL)](https://msdn.microsoft.com/library/mt574871.aspx)|Utilizar activação pós-falha ou FORCE_FAILOVER_ALLOW_DATA_LOSS para mudar uma base de dados secundária para ser principal para iniciar activação pós-falha
|[ALTERAR a base de dados (base de dados do Azure SQL)](https://msdn.microsoft.com/library/mt574871.aspx)|Utilize Remover servidor ligado SECUNDÁRIO para terminar uma replicação de dados entre uma base de dados do SQL e base de dados secundária especificada.|
|[sys.geo_replication_links (base de dados do SQL Azure)](https://msdn.microsoft.com/library/mt575501.aspx)|Devolve informações sobre todas as ligações de replicação existente para cada base de dados do servidor de lógico de base de dados do Azure SQL.|
|[sys.dm_geo_replication_link_status (base de dados do SQL Azure)](https://msdn.microsoft.com/library/mt575504.aspx)|Obtém a última vez replicação, tempo de desfasamento replicação último e outras informações sobre a ligação de replicação para uma determinada base de dados do SQL.|
|[sys.dm_operation_status (base de dados do SQL Azure)](https://msdn.microsoft.com/library/dn270022.aspx)|Mostra o estado para todas as operações de base de dados, incluindo o estado das ligações de replicação.|
|[sp_wait_for_database_copy_sync (base de dados do SQL Azure)](https://msdn.microsoft.com/library/dn467644.aspx)|faz com que a aplicação de esperar até todas as operações consolidadas são replicadas e confirmadas pela base de dados secundária ativo.|
||||

### <a name="powershell"></a>PowerShell

|Cmdlet|Descrição|
|------|-----------|
|[Get-AzureRmSqlDatabase](https://msdn.microsoft.com/en-us/library/azure/mt603648.aspx)|Obtém um ou mais bases de dados.|
|[Novo AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603689.aspx)|Cria uma base de dados secundária para uma base de dados existente e inicia a replicação de dados.|
|[Definir AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/en-us/library/mt619393.aspx)|Muda uma base de dados secundária para ser principal para iniciar activação pós-falha.|
|[Remover AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/en-us/library/mt603457.aspx)|Termina a replicação de dados entre uma base de dados do SQL e base de dados secundária especificada.|
|[Get-AzureRmSqlDatabaseReplicationLink](https://msdn.microsoft.com/library/mt619330.aspx)|Obtém as ligações de replicação geo entre uma base de dados do SQL Azure e um grupo de recursos ou do SQL Server.|
||||

### <a name="rest-api"></a>REST API

|API|Descrição|
|---|-----------|
|[Criar ou actualizar base de dados (createMode = restaurar)](https://msdn.microsoft.com/library/azure/mt163685.aspx)|Cria, actualiza ou restaura uma primária ou uma base de dados secundária.|
|[Começar a criar ou actualizar o estado de base de dados](https://msdn.microsoft.com/library/azure/mt643934.aspx)|Devolve o estado durante a operação de criação.|
|[Definir a base de dados secundária como principal (planeada activação pós-falha)](https://msdn.microsoft.com/ibrary/azure/mt575007.aspx)|Promova uma base de dados secundária numa parceria Geo replicação para se tornar a nova base de dados principal.|
|[Definir a base de dados secundária como principal (não planeado activação pós-falha)](https://msdn.microsoft.com/library/azure/mt582027.aspx)|Para forçar uma activação pós-falha à base de dados secundária e definir secundária como a principal.|
|[Obter ligações de replicação](https://msdn.microsoft.com/library/azure/mt600929.aspx)|Obtém a todas as ligações de replicação para uma base de dados do SQL determinado numa parceria geo replicação. Obtém a informação visível na vista de catálogo sys.geo_replication_links.|
|[Obter a ligação de replicação](https://msdn.microsoft.com/library/azure/mt600778.aspx)|Obtém uma ligação de replicação específico para uma base de dados do SQL determinado numa parceria geo replicação. Obtém a informação visível na vista de catálogo sys.geo_replication_links.|
||||



## <a name="next-steps"></a>Próximos passos

- Para obter uma descrição de continuidade do negócio e cenários, consulte o artigo [Descrição geral de continuidade do negócio](sql-database-business-continuity.md)
- Para saber mais sobre cópias de segurança da base de dados do SQL Azure automatizado, consulte a [base de dados SQL automatizado cópias de segurança](sql-database-automated-backups.md).
- Para saber como utilizar as cópias de segurança automatizadas para a recuperação, consulte o artigo [Restaurar uma base de dados a partir das cópias de segurança iniciado pelo serviço](sql-database-recovery-using-backups.md).
- Para saber como utilizar as cópias de segurança automatizadas para o arquivo, consulte o artigo [Copiar de base de dados](sql-database-copy.md).
- Para saber mais sobre os requisitos de autenticação para um novo servidor principal e a base de dados, consulte o artigo [segurança da base de dados SQL após a recuperação de falhas](sql-database-geo-replication-security-config.md).
