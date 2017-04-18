<properties
   pageTitle="Recuperação de falhas de base de dados SQL | Microsoft Azure"
   description="Saiba como recuperar uma base de dados a partir de uma falha do Centro de dados regional do ou falha com Azure SQL da base de dados ativo Geo-replicação e capacidades de Geo restaurar."
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/13/2016"
   ms.author="carlrab"/>

# <a name="restore-an-azure-sql-database-or-failover-to-a-secondary"></a>Restaurar uma base de dados do SQL Azure ou activação pós-falha para um secundário

Base de dados SQL Azure oferece as seguintes capacidades para recuperar a partir de uma falha de:

- [Replicação de Geo ativa](sql-database-geo-replication-overview.md)
- [Restaurar geo](sql-database-recovery-using-backups.md#point-in-time-restore)

Para saber mais sobre cenários de continuidade do negócio e as funcionalidades de suporte estes cenários, consulte o artigo [continuidade do negócio](sql-database-business-continuity.md).

### <a name="prepare-for-the-event-of-an-outage"></a>Preparar-se para o evento de uma falha de

Sucesso com a recuperação para outra região de dados utilizando Geo-a replicação do Active ou geo redundantes cópias de segurança, terá da preparar um servidor no Centro de dados do outro indisponibilidade para se tornar o novo servidor primário deve a necessidade de surgir, bem como bem definiu passos documentados e testado para garantir uma recuperação suave. Estes passos de preparação incluem:

- Identifica o servidor lógico noutra região para se tornar o novo servidor principal. Com o Active Geo replicação, este será pelo menos um e talvez cada um dos servidores secundários. Para restaurar Geo, geralmente será um servidor na [região par](../best-practices-availability-paired-regions.md) região onde se encontra a base de dados.
- Identificar e, opcionalmente, definir, as regras da firewall ao nível do servidor necessárias para os utilizadores acedam a nova base de dados principal.
- Determine como passar para redirecionar utilizadores para o novo servidor principal, tal como alterando as cadeias de ligação ou ao alterar as entradas de DNS.
- Identificar e, opcionalmente, criar, os que tem de estar presentes na base de dados principal no novo servidor principal e certifique-se de que estes inícios de sessão tem as permissões apropriadas na base de dados principal, se qualquer inícios de sessão. Para obter mais informações, consulte o artigo [segurança da base de dados SQL após a recuperação de falhas](sql-database-geo-replication-security-config.md)
- Identifica as regras de alertas que terão de ser atualizadas para mapear para a nova base de dados principal.
- A configuração de auditoria numa base de dados principal atual do documento
- Execute um [nível de detalhe recuperação de falhas](sql-database-disaster-recovery-drills.md). Para simular uma falha para restaurar Geo, pode eliminar ou mudar o nome da base de dados de origem para causar a falha da conectividade de aplicação. Para simular uma falha para Geo-replicação do Active, pode desativar a aplicação web ou máquina virtual ligado à base de dados ou activação pós-falha a base de dados para causar falhas de connectity de aplicações.

## <a name="when-to-initiate-recovery"></a>Quando iniciar recuperação

A operação de recuperação une a aplicação. Que requer a alterar o redirecionamento através de DNS ou a cadeia de ligação SQL e poderá resultar na perda permanente de dados. Por conseguinte, deve ser feita apenas quando a indisponibilidade é provável que dure mais objectivo de tempo de recuperação da sua aplicação. Quando a aplicação é implementada produção deve efetuar o controlo regular do Estado de funcionamento da aplicação e utilize os seguintes pontos de dados para declarar que se justifica a recuperação ter:

1.  Falha de conectividade permanente a partir da camada de aplicação à base de dados.
2.  Portal do Azure mostra um alerta sobre um incidente de na região com impacto abrangente.
3.  O servidor de base de dados do Azure SQL está marcado como degradado.

Dependendo do seu tolerância a aplicação para o tempo de inatividade e possível responsabilidade empresas pode considere as seguintes opções de recuperação.

Utilize a de [Obter recuperáveis da base de dados](https://msdn.microsoft.com/library/dn800985.aspx) (*LastAvailableBackupDate*) para obter o ponto de replicadas Geo restauro mais recente.

## <a name="wait-for-service-recovery"></a>Aguardar recuperação de serviço

O trabalho de equipas Azure diligentemente para restaurar a disponibilidade de serviço conforme rapidamente possível mas dependendo da raiz fazer com que pode demorar horas ou dias.  Se a sua aplicação pode tolerar tempo de inatividade significativo pode simplesmente aguardar que a recuperação concluir. Neste caso, não é necessária nenhuma ação da sua parte. Pode ver o estado atual do serviço no nosso [Dashboard de estado de funcionamento do serviço do Azure](https://azure.microsoft.com/status/). Após a recuperação da região será restaurada disponibilidade da sua aplicação.

## <a name="failover-to-geo-replicated-secondary-database"></a>Activação pós-falha a base de dados secundária replicada geo

Se o tempo de inatividade da sua aplicação pode resultar em responsabilidade empresas devem estar a utilizar bases de dados replicada geo na sua aplicação. -Permitirá a aplicação restaurar rapidamente disponibilidade numa região diferente em caso de uma falha. Saiba como [Configurar a replicação Geo](sql-database-geo-replication-portal.md).

Para restaurar a disponibilidade das bases de dados tem de iniciar a activação pós-falha para secundária replicadas geo utilizando um dos métodos suportados.

Utilize um dos seguintes guias para activação pós-falha numa replicadas geo secundário base de dados:

- [Activação pós-falha para um secundário de replicadas geo através do Portal do Azure](sql-database-geo-replication-portal.md)
- [Activação pós-falha para um secundário de replicadas geo através do PowerShell](sql-database-geo-replication-powershell.md)
- [Activação pós-falha para um secundário de replicadas geo utilizando o T-SQL](sql-database-geo-replication-transact-sql.md)

## <a name="recover-using-geo-restore"></a>Recuperar utilizando Geo restauro

Se o tempo de inatividade da sua aplicação não resultar em responsabilidade empresas pode utilizar Geo restaurar como um método para recuperar a sua bases de dados a aplicação. Cria uma cópia da base de dados a partir da sua cópia de segurança geo redundantes mais recente.

Utilizar um dos seguintes guias de introdução para geo restaurar uma base de dados para uma nova região:

- [Geo-restaurar uma base de dados para uma nova região através do Portal do Azure](sql-database-geo-restore-portal.md)
- [Geo-restaurar uma base de dados para uma nova região através do PowerShell](sql-database-geo-restore-powershell.md)

## <a name="configure-your-database-after-recovery"></a>Configurar a sua base de dados após a recuperação

Se estiver a utilizar geo replicação activação pós-falha ou restaurar geo para recuperar a partir de uma falha, certifique-se de que a ligação para novas bases de dados está corretamente configurada para que a função de aplicação normal pode ser retomada. Esta é uma lista de verificação das tarefas para preparar o seu produção recuperado da base de dados.

### <a name="update-connection-strings"></a>Atualizar as cadeias de ligação

Uma vez que a base de dados recuperado residirão num servidor diferente, tem de atualizar a cadeia de ligação da sua aplicação para apontar para que o servidor.

Para obter mais informações sobre como alterar as cadeias de ligação, consulte o idioma de desenvolvimento adequado para a [biblioteca de ligação](sql-database-libraries.md).

### <a name="configure-firewall-rules"></a>Configurar regras de Firewall

É necessário para se certificar de que as regras de firewall configuradas no servidor e da base de dados corresponderem que tenham sido configurado no servidor principal e base de dados principal. Para obter mais informações, consulte o artigo [como: configurar as definições da Firewall (base de dados do SQL Azure)](sql-database-configure-firewall-settings.md).


### <a name="configure-logins-and-database-users"></a>Configurar os inícios de sessão e utilizadores de base de dados

É necessário para se certificar de que existem no servidor que aloja a base de dados recuperado de todos os inícios de sessão utilizados pela sua aplicação. Para obter mais informações, consulte [Configuração de segurança para a replicação Geo](sql-database-geo-replication-security-config.md).

>[AZURE.NOTE] Deve configurar e testar a sua regras de firewall do servidor e inícios de sessão (e as respetivas permissões) durante uma desagregação de recuperação de falhas. Estes objetos ao nível do servidor e a respectiva configuração poderão não estar disponíveis durante a indisponibilidade.

### <a name="setup-telemetry-alerts"></a>Alertas de telemetria do programa de configuração

É necessário para se certificar de que as definições de alerta regra existentes são atualizadas para mapear para a base de dados recuperado e no servidor diferente.

Para mais informações acerca de regras de alerta de base de dados, consulte o artigo [Controlar estado de funcionamento do serviço](../monitoring-and-diagnostics/insights-service-health.md)e [Receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) .

### <a name="enable-auditing"></a>Activar a auditoria

Se auditoria for necessária para aceder à sua base de dados, tem de ativar auditoria após a recuperação de base de dados. Um bom indicador que é necessário auditoria é que as aplicações de cliente utilizar cadeias de ligação segura num padrão de *. database.secure.windows.net. Para mais informações, consulte o artigo [Introdução ao auditoria de base de dados SQL](sql-database-auditing-get-started.md).


## <a name="next-steps"></a>Próximos passos

- Para saber mais sobre cópias de segurança da base de dados do SQL Azure automatizado, consulte o artigo [da base de dados do SQL automatizado cópias de segurança](sql-database-automated-backups.md)
- Para saber mais sobre cenários de estrutura e recuperação de continuidade do negócio, consulte o artigo [cenários continuidade](sql-database-business-continuity.md)
- Para saber mais sobre utilizar cópias de segurança automatizadas para recuperação, consulte o artigo [Restaurar uma base de dados a partir das cópias de segurança iniciado pelo serviço](sql-database-recovery-using-backups.md)
