<properties
   pageTitle="Continuidade de negócios em nuvem - recuperação - base de dados SQL de base de dados | Microsoft Azure"
   description="Saiba como base de dados do SQL Azure suporta continuidade do negócio na nuvem e recuperação de base de dados e ajuda a manter aplicações nuvem críticos em execução."
   keywords="continuidade de negócios, continuidade do negócio na nuvem, recuperação de falhas de base de dados, recuperação de base de dados"
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
   ms.workload="NA"
   ms.date="10/13/2016"
   ms.author="carlrab"/>

# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Descrição geral de continuidade do negócio com base de dados do SQL Azure

Esta descrição geral descreve as capacidades de base de dados do SQL Azure fornece para continuidade do negócio e recuperação de falhas. Fornece opções, recomendações e tutoriais para recuperar de desorganização eventos que podem causar a perda de dados ou fazer com que a sua base de dados e a aplicação para se tornar disponível. Debate inclui o que fazer quando um utilizador ou de erros de aplicações afeta a integridade dos dados, uma região Azure tem uma falha ou a aplicação requer manutenção. 

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Funcionalidades de base de dados SQL que pode utilizar para fornecer continuidade do negócio

Base de dados SQL disponibiliza várias funcionalidades de continuidade do negócio, incluindo cópias de segurança automatizadas e a replicação de bases de dados opcional. Cada possua características diferentes para o tempo de recuperação estimado (ERTER) e a possível perda de dados para transações recentes. Assim que a compreender estas opções, pode escolher entre elas - e, na maioria dos cenários, utilizá-las em conjunto para cenários diferentes. À medida que desenvolver o seu plano de continuidade do negócio, é necessário compreender o tempo máximo de aceitável antes da aplicação totalmente recupera após o evento desorganização - este é o seu objectivo de tempo de recuperação (RTO). Também é necessário compreender a quantidade máxima de dados as atualizações recentes (intervalo de tempo) a aplicação pode tolerar perder quando recuperar após o evento desorganização - o objectivo de ponto de recuperação (RPO). 

A tabela seguinte compara a ERTER e RPO para os três cenários mais comuns.

| Capacidade de |  Camada básica | Camada padrão  | Camadas de Premium |
|---|---|---|---|
| Pontos em restaurar a hora da cópia de segurança | Qualquer ponto de restauro dentro de 7 dias   | Qualquer ponto de restauro dentro de 35 dias  | Qualquer ponto de restauro dentro de 35 dias |
Geo-restaurar a partir de replicadas geo cópias de segurança | ERTER < 12h, RPO < 1H   | ERTER < 12h, RPO < 1H   | ERTER < 12h, RPO < 1H |
|Replicação de Geo ativa | ERTER < 30s, RPO < 5s   | ERTER < 30s, RPO < 5s | ERTER < 30s, RPO < 5s |


### <a name="use-database-backups-to-recover-a-database"></a>Utilizar cópias de segurança da base de dados para recuperar uma base de dados

Base de dados SQL automaticamente executa uma combinação de base de dados completa cópias de segurança semanalmente, diferença de base de dados por hora cópias de segurança e cópias de segurança do registo da transação em cinco minutos para proteger a sua empresa a partir de perda de dados. Estas cópias de segurança são armazenadas no armazenamento redundante localmente para 35 dias para bases de dados em camadas de serviço Standard e Premium e sete dias para bases de dados na camada serviço básico - Consulte [camadas de serviço](sql-database-service-tiers.md) para obter mais detalhes sobre camadas de serviços. Se o período de retenção para a camada de serviço não cumpre os requisitos de negócio, pode aumentar o período de retenção alterando [a camada de serviço](sql-database-scale-up.md). A base de dados completa e diferença cópias de segurança também são replicadas para um [Centro de dados emparelhado](../best-practices-availability-paired-regions.md) para protecção contra uma falha do Centro de dados - Consulte [cópias de segurança da base de dados automática](sql-database-automated-backups.md) mais detalhes.

Pode utilizar estas cópias de segurança da base de dados automática para recuperar uma base de dados a partir de vários desorganização eventos, no seu centro de dados e para outro centro de dados. Utilizar cópias de segurança da base de dados automática, o tempo de recuperação estimado depende diversos fatores, incluindo o número total de bases de dados recuperar na mesma região ao mesmo tempo, o tamanho da base de dados, o tamanho do registo da transação e largura de banda de rede. Na maioria dos casos, o tempo de recuperação é menos de 12 horas. Quando a recuperar para outra região de dados, a possível perda de dados é limitada para 1 hora pelo armazenamento geo redundantes dos cópias de segurança da base de dados diferença preço por hora. 

> [AZURE.IMPORTANT] Para recuperar utilizando cópias de segurança automatizadas, tem de ser um membro da função SQL Server contribuinte ou proprietário da subscrição - consulte [RBAC: funções incorporadas](../active-directory/role-based-access-built-in-roles.md). Pode recuperar utilizando o portal do Azure, PowerShell ou REST API. Não é possível utilizar Transact-SQL.

Utilizar cópias de segurança automatizadas como o mecanismo de recuperação e continuidade do negócio se a aplicação:

- Não é considerado missão crítica.
- Não possui um SLA encadernação, por conseguinte, o tempo de inatividade igual ou superior 24 horas não irá resultar em passivo financeiro.
- Tem uma taxa baixa de alteração de dados (operações baixos por hora) e perder até uma hora de alteração é uma perda de dados aceitável. 
- Custo é sensível. 

Se precisar de recuperação mais rápida, utilize a [Replicação do Geo Active](sql-database-geo-replication-overview.md) (abordado seguinte). Se tem de ser conseguir recuperar dados de um período mais antigo do que 35 dias, considere a sua base de dados regularmente para um ficheiro BACPAC do arquivo (um ficheiro comprimido que contém o esquema da base de dados e os dados associados) armazenados no armazenamento de Blobs do Azure ou noutra localização da sua escolha. Para mais informações sobre como criar um arquivo de base de dados consistentes, consulte o artigo [criar uma cópia da base de dados](sql-database-copy.md) e [exportar a cópia da base de dados](sql-database-export.md). 

### <a name="use-active-geo-replication-to-reduce-recovery-time-and-limit-data-loss-associated-with-a-recovery"></a>Utilizar a replicação de Geo ativo para reduzir o tempo de recuperação e limitar a perda de dados associada a uma recuperação

Além de utilizar cópias de segurança da base de dados para a recuperação de base de dados em caso de uma interrupção de negócio, pode utilizar [Geo-a replicação do Active](sql-database-geo-replication-overview.md) para configurar uma base de dados ter até quatro legíveis secundários bases de dados nas regiões da sua escolha. Estas bases de dados secundárias são mantidas sincronizados com a base de dados principal utilizando um dispositivo de replicação assíncrona. Esta funcionalidade é utilizada para proteger contra interrupção de negócio em caso de uma falha do Centro de dados ou durante uma atualização de aplicação. Replicação do Geo Active pode também ser utilizada para fornecer um melhor desempenho de consulta para consultas só de leitura aos utilizadores geograficamente dispersos.

Se a base de dados principal fica offline inesperadamente ou necessitar colocá-lo offline para atividades de manutenção, pode rapidamente promover um secundário para tornar-se a página principal (também denominada uma activação pós-falha) e configurar aplicações para ligar para a página principal recentemente promovida. Com uma falha na ligação planeada, não existe sem perda de dados. Com uma falha na ligação não planeada, poderá ser algumas pequena quantidade de perda de dados para muito recentes transações devido a natureza de replicação assíncrona. Após uma falha na ligação, pode de reposição de recurso posterior - acordo com um plano ou quando o Centro de dados estiver novamente online. Em todos os casos, os utilizadores uma experiência de uma pequena quantidade de tempo de inatividade em tem de voltar a ligar. 

> [AZURE.IMPORTANT] Para utilizar Geo-a replicação do Active, tem de ser o proprietário da subscrição ou ter permissões administrativas no SQL Server. Pode configurar e activação pós-falha utilizando o portal do Azure, PowerShell ou REST API utilizando as permissões da subscrição ou Transact-SQL utilizando permissões dentro do SQL Server.

Utilize Geo-a replicação do Active se a sua aplicação cumpra qualquer um dos seguintes critérios:

- É missão crítica.
- Tem um contrato de nível de serviço (SLA) que não permitir a 24 horas ou mais dos tempo de inatividade.
- Tempo de inatividade irá resultar em passivo financeiro.
- Tem uma taxa elevada de dados é elevado alterar e não for aceitável perder uma hora de dados.
- Os custos adicionais de replicação do geo active são inferior a responsabilidade financeira potencial e associada perda de empresas.

## <a name="recover-a-database-after-a-user-or-application-error"></a>Recuperar uma base de dados após um erro de utilizador ou a aplicação

* Ninguém é perfeito! Um utilizador poderá eliminar acidentalmente alguns dados de, inadvertidamente largar uma tabela importante ou mesmo largar toda uma base de dados. Em alternativa, uma aplicação poderá substituí-las acidentalmente dados boas com dados danificados devido a um defeito de aplicação. 

Neste cenário, estas são as opções de recuperação.

### <a name="perform-a-point-in-time-restore"></a>Executar um restauro de ponto no tempo

Pode utilizar as cópias de segurança automatizadas para recuperar uma cópia da base de dados para um bom ponto conhecido no momento, desde que tempo está dentro do período de retenção de base de dados. Depois de é restaurado a base de dados, pode substituir a base de dados original com a base de dados restaurado ou copie os dados conforme seja necessários dos dados restaurados para a base de dados original. Se a base de dados utiliza Geo-a replicação do Active, recomendamos que copiar os dados necessários da cópia restaurada para a base de dados original. Se substituir a base de dados original com a base de dados restaurado, terá de reconfigurar e voltar a sincronizar ativo replicação Geo (que pode demorar algum tempo para uma base de dados de grandes dimensões). 

Para mais informações e para obter passos detalhados para restaurar uma base de dados para um ponto de hora através do portal Azure ou através do PowerShell, consulte o artigo [Restaurar em qualquer altura](sql-database-recovery-using-backups.md#point-in-time-restore). Não é possível recuperar utilizando o Transact-SQL.

### <a name="restore-a-deleted-database"></a>Restaurar uma base de dados eliminada

Se a base de dados é eliminada, mas não foi eliminado no servidor lógico, pode restaurar a base de dados eliminado para o ponto no qual foi eliminada. Este procedimento restaura uma cópia de segurança da base de dados para o SQL server lógico mesmo a partir do qual foi eliminada. Pode restaurá-lo utilizando o nome original ou fornecer um novo nome ou base de dados restaurada.

Para mais informações e para obter passos detalhados para restaurar uma base de dados eliminado através do portal Azure ou através do PowerShell, consulte o artigo [Restaurar uma base de dados eliminado](sql-database-recovery-using-backups.md#deleted-database-restore). Não é possível restaurar utilizando o Transact-SQL.

> [AZURE.IMPORTANT] Se o servidor lógico for eliminado, é possível recuperar uma base de dados eliminado. 

### <a name="import-from-a-database-archive"></a>Importar a partir de um arquivo de base de dados

Se a perda de dados ocorreu fora do período de retenção atual para cópias de segurança automatizados e ter sido arquivo a base de dados, pode [Importar um ficheiro BACPAC arquivado](sql-database-import.md) para uma nova base de dados. Neste momento, pode substituir a base de dados original com a base de dados importado ou copie os dados conforme seja necessários dos dados importados para a base de dados original. 

## <a name="recover-a-database-to-another-region-from-an-azure-regional-data-center-outage"></a>Recuperar uma base de dados para outra região de uma falha do Centro de dados regionais Azure

<!-- Explain this scenario -->

Apesar de raros, um centro de dados Azure pode ter uma falha. Quando ocorre uma falha, faz com que uma interrupção de empresas que pode apenas última alguns minutos ou poderá última horas. 

- Uma das opções é de esperar que a base de dados voltar a estar online quando está a falha do Centro de dados acima. Isto funciona para as aplicações que podem perder de forma alguma têm a base de dados offline. Por exemplo, um projeto de desenvolvimento ou a versão de avaliação gratuita não tem de trabalhar em constantemente. Quando um centro de dados tiver uma falha, não saber quanto tempo irá durar a indisponibilidade, para que esta opção só funciona se não precisar da base de dados para o tempo.
- Outra opção é um dos activação pós-falha para outra região de dados se estiver a utilizar Geo-a replicação do Active ou recuperar utilizando cópias de segurança da base de dados redundantes do geo (Geo restauro). Activação pós-falha leva apenas alguns segundos, enquanto a recuperação de cópias de segurança demora horas.

Quando tomar a ação, quanto tempo demora para recuperar e quanto implicam trabalho uma falha do Centro de dados de perda de dados depende como decidir utilizar as funcionalidades de continuidade do negócio outros fabricantes referidas acima, na sua aplicação. Na verdade, poderá optar por utilizar uma combinação de cópias de segurança da base de dados e Geo-replicação do Active consoante aos requisitos da aplicação. Para um debate de considerações de estrutura da aplicação para autónomas bases de dados e para flexível agrupamentos de utilização destas funcionalidades de continuidade do negócio, consulte o artigo [estrutura um pedido de recuperação de falhas na nuvem](sql-database-designing-cloud-solutions-for-disaster-recovery.md) e [estratégias de recuperação de falhas de agrupamento flexível](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

As secções abaixo fornecem uma descrição geral dos passos para recuperar com cópias de segurança da base de dados ou Geo-a replicação do Active. Para obter passos detalhados incluindo planeamento requisitos, os passos de recuperação de mensagem e informações sobre como simular uma falha para efetuar uma pesquisa de recuperação de falhas, consulte o artigo [recuperar uma base de dados do SQL a partir de uma falha](sql-database-disaster-recovery.md).

### <a name="prepare-for-an-outage"></a>Preparar para uma falha de

Independentemente da funcionalidade de continuidade do negócio que utiliza, tem de:

- Identificar e preparar o servidor de destino, incluindo as regras da firewall ao nível do servidor, inícios de sessão e permissões ao nível da base de dados principal.
- Determinar como redirecionar clientes e aplicações de cliente para o novo servidor
- Outras dependências, tal como alertas e definições de auditoria de documentos 
 
Se não planeia e preparar corretamente, colocá suas aplicações online depois de um activação pós-falha ou uma recuperação demora tempo adicional e provável que também necessitam resolução de problemas de cada vez do limite - uma combinação incorretas.

### <a name="failover-to-a-geo-replicated-secondary-database"></a>Activação pós-falha numa replicadas geo secundário base de dados 

Se estiver a utilizar Geo-a replicação do Active como [forçar uma activação pós-falha para um secundário de replicadas geo](sql-database-disaster-recovery.md#failover-to-geo-replicated-secondary-database)o mecanismo de recuperação. Em segundos, secundária promovida para se tornar a nova página principal e está pronta para gravar novas transações e responder a perguntas - com apenas alguns segundos de perda de dados para os dados que não tivessem sido replicados. Para obter informações sobre como automatizar o processo de activação pós-falha, consulte o artigo [estrutura um pedido de recuperação de falhas na nuvem](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

> [AZURE.NOTE] Quando o Centro de dados surgir novamente online, pode reposição de recurso para a página principal original (ou não).

### <a name="perform-a-geo-restore"></a>Executar um restauro Geo 

Se estiver a utilizar automatizado cópias de segurança com replicação de armazenamento geo redundante como o mecanismo de recuperação, [Iniciar uma recuperação de base de dados utilizando Geo restaurar](sql-database-disaster-recovery.md#recover-using-geo-restore). Recuperação normalmente ocorre dentro de 12 horas - com perda de dados de até uma hora determinada pelo quando última hora diferença cópia de segurança com tomadas e replicada. Até que a recuperação for concluída, a base de dados não consegue registar quaisquer transações ou responder a perguntas. 

> [AZURE.NOTE] Se o Centro de dados ficar novamente online antes de mudar a aplicação à base de dados recuperado, pode simplesmente cancelar a recuperação.  

### <a name="perform-post-failover--recovery-tasks"></a>Publicar executar activação pós-falha / tarefas de recuperação 

Após a recuperação de quer dispositivo recuperação, deve efetuar as seguintes tarefas adicionais antes dos seus utilizadores e as aplicações são voltar a trabalhar:

- Os clientes de redirecionamento e aplicações de cliente para o novo servidor e a base de dados restaurado
- Garantir que as regras da firewall do adequados ao nível do servidor estão no local para os utilizadores ligar (ou utilize o [nível de base de dados firewalls](sql-database-firewall-configure.md#creating-database-level-firewall-rules))
- Certifique-se de que os inícios de sessão adequados e permissões ao nível da base de dados principal estão no local (ou utilize [contidas utilizadores](https://msdn.microsoft.com/library/ff929188.aspx))
- Configurar auditoria, conforme adequado
- Configure alertas, conforme adequado

## <a name="upgrade-an-application-with-minimal-downtime"></a>Atualizar uma aplicação com indisponibilidade mínima

Por vezes, uma aplicação tem de ser reencaminhado offline devido a manutenção planeada, tal como uma atualização de aplicação. [Gerir actualizações de aplicação](sql-database-manage-application-rolling-upgrade.md) descreve como utilizar Geo-a replicação do Active para ativar atualiza os graduais da sua aplicação na nuvem para minimizar o tempo de inatividade durante as actualizações e forneça um caminho de recuperação em caso de algo de errado. Este artigo analisa dois métodos diferentes de orquestrar o processo de atualização e fala sobre as vantagens e compromissos de cada opção.

## <a name="next-steps"></a>Próximos passos

Para um debate de considerações de estrutura da aplicação para autónomas bases de dados e para flexível conjuntos de dados, consulte [estrutura um pedido de recuperação de falhas na nuvem](sql-database-designing-cloud-solutions-for-disaster-recovery.md) e [estratégias de recuperação de falhas de agrupamento flexível](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).






