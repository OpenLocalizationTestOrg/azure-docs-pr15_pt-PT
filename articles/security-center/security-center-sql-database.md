<properties
   pageTitle="Centro de segurança do Azure e base de dados do SQL Azure service | Microsoft Azure"
   description="Este artigo mostra como o Centro de segurança pode ajudá-lo seguro as bases de dados na base de dados do SQL Azure."
   services="sql-database"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="terrylan"/>

# <a name="azure-security-center-and-azure-sql-database-service"></a>Serviço de centro de segurança do Azure e base de dados do SQL Azure

[Centro de segurança do Azure](https://azure.microsoft.com/documentation/services/security-center/) ajuda-o a evitar, detetar e responder a ameaças. Fornece a segurança integrada monitorização e gestão de políticas entre as subscrições do Azure, ajuda a Deteta ameaças caso contrário, aceda não ser e funciona com uma Ecossistema vasto de soluções de segurança.

Este artigo mostra como o Centro de segurança pode ajudá-lo seguro as bases de dados na base de dados do SQL Azure.

## <a name="why-use-security-center"></a>Porquê utilizar o Centro de segurança?

Centro de segurança ajuda-o a proteger os dados na base de dados SQL, fornecendo visibilidade sobre a segurança de todos os servidores e bases de dados. Com o Centro de segurança, pode:

- Defina políticas para auditoria e encriptação da base de dados SQL.
- Monitorize a segurança dos recursos de base de dados SQL através de todas as subscrições.
- Identificar e solucionar uma problemas de segurança rapidamente.
- Integre alertas de [deteção de ameaça de base de dados do SQL Azure](../sql-database/sql-database-threat-detection-get-started.md).

Para além de ajudar a proteger os recursos da base de dados SQL, o Centro de segurança também fornece monitorização de segurança e gestão do Azure máquinas virtuais, serviços em nuvem, serviços de aplicação, redes virtuais e mais. Saiba mais sobre o Centro de segurança [aqui](security-center-intro.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar a utilizar com o Centro de segurança, tem de ter uma subscrição do Microsoft Azure. A camada gratuita do Centro de segurança é activada com a sua subscrição. Para mais informações sobre o Centro de segurança gratuitos e camadas padrão, consulte o artigo [Preços do Centro de segurança](https://azure.microsoft.com/pricing/details/security-center/).

Centro de segurança suporta o acesso baseado em funções. Para saber mais sobre o controlo de acesso baseado em funções (RBCA) no Azure, consulte o artigo [Baseada em Azure Active Directory funções o controlo de acesso](../active-directory/role-based-access-control-configure.md). FAQ do Centro de segurança fornece informações sobre [permissões de que forma são processadas no Centro de segurança](security-center-faq.md#how-are-permissions-handled-in-azure-security-center).

## <a name="access-security-center"></a>Centro de segurança do Access

Aceder ao centro de segurança a partir do [Azure portal](https://azure.microsoft.com/features/azure-portal/). [Inicie sessão portal do](https://portal.azure.com/) e selecione a **opção do Centro de segurança**.

![Opção do Centro de segurança][1]

O **Centro de segurança** pá é aberta.
![Pá do Centro de segurança][2]

## <a name="set-security-policy"></a>Definir a política de segurança

Uma política de segurança define o conjunto de controlos que lhe foram recomendados para recursos numa subscrição especificada ou grupo de recursos. No Centro de segurança, pode definir políticas para a sua subscrições ou grupos de recursos de acordo com as necessidades de segurança da sua empresa e o tipo de aplicações ou confidencialidade dos dados de cada subscrição.

Pode definir uma política para mostrar as recomendações para auditoria SQL e encriptação de dados transparente de SQL (TDE).

- Quando ativa a **ameaça detecção e de auditoria de SQL**, o Centro de segurança recomenda de auditoria de acesso a base de dados Azure ativada para conformidade, deteção e efeitos de inquérito avançadas.
- Quando ativa a **encriptação de dados transparente de SQL**, o Centro de segurança recomenda de que a encriptação em repouso estar ativada para a sua base de dados do SQL Azure, associadas cópias de segurança e ficheiros de registo da transação.

Para definir uma política de segurança, selecione o mosaico de **política** na pá Centro de segurança. Na pá **política de segurança** , selecione a subscrição no qual pretende ativar a política de segurança. Selecione a **política de prevenção** e **ative as recomendações de segurança que pretende utilizar nesta subscrição** .
![Política de segurança][3]

Para saber mais, consulte o artigo [definir políticas de segurança](security-center-policies.md).

## <a name="manage-security-recommendation"></a>Gerir recomendação de segurança

Centro de segurança periodicamente analisa o estado de segurança dos seus recursos Azure. Quando o Centro de segurança identifica potenciais vulnerabilidade de segurança, cria recomendações. As recomendações orientá-lo durante o processo de configuração de controlos necessários.

Depois de definir uma política de segurança, o Centro de segurança analisa o estado de segurança dos seus recursos para identificar vulnerabilidade potencial. As recomendações são apresentadas num formato de tabela em que cada linha representa um recomendação específica. Utilize a tabela seguinte como uma referência para ajudá-lo a compreender as recomendações disponíveis para a base de dados do SQL Azure e o que faz cada recomendação se aplicá-lo. Selecionar uma recomendação leva-o para um artigo que explica como pode implementar a recomendação no Centro de segurança.

| Recomendação | Descrição |
| ----- | ----- |
| [Ativar a deteção de auditoria e ameaças em servidores SQL](security-center-enable-auditing-on-sql-servers.md) | Recomenda-se de que Ativar deteção de auditoria e ameaça para os servidores de base de dados SQL. (Só serviço de base de dados SQL. Não inclui em execução no seu máquinas virtuais do Microsoft SQL Server.) |
| [Ativar a deteção de auditoria e ameaças em bases de dados SQL](security-center-enable-auditing-on-sql-databases.md) | Recomenda-se de que Ativar deteção de auditoria e ameaça para bases de dados da base de dados SQL. (Só serviço de base de dados SQL. Não inclui em execução no seu máquinas virtuais do Microsoft SQL Server.) |
| [Activar a encriptação de dados transparente](security-center-enable-transparent-data-encryption.md) | Recomenda-se de que ativar encriptação para bases de dados SQL. (Só serviço base de dados SQL.) |

Para ver as recomendações para os recursos do Azure, selecione o mosaico de **recomendações** sobre pá o Centro de segurança. No pá **recomendações** , selecione uma recomendação para ver os detalhes. Neste exemplo, ao selecionar **Activar auditoria & deteção ameaça em servidores SQL**.

![Recomendações][4]

Como é mostrado abaixo, o Centro de segurança mostra-lhe os servidores SQL onde auditoria e ameaça deteção não se encontram ativados. Depois de ativar auditoria, pode configurar definições de deteção de ameaça e definições de e-mail para receber alertas de segurança. Deteção de ameaça alerta quando Deteta atividades de base de dados discordantes que indicam potenciais ameaças de segurança para a base de dados. Os alertas de são apresentados no dashboard do Centro de segurança.
![Deteção de auditoria e ameaça][5]

Siga os passos em [Introdução ao deteção de ameaça de base de dados do SQL](../sql-database/sql-database-threat-detection-get-started.md) para ativar e configurar a ameaça detecção e para configurar a lista de mensagens de correio eletrónico que irão receber alertas de segurança de deteção de atividades discordantes.

Para saber mais sobre recomendações, consulte o artigo [Gerir recomendações de segurança](security-center-recommendations.md).

## <a name="monitor-security-health"></a>Estado de funcionamento do monitor segurança

Após ativar [as políticas de segurança](security-center-policies.md) para recursos de uma subscrição, o Centro de segurança irá analisar a segurança dos seus recursos para identificar vulnerabilidade potencial.  Pode ver o estado de segurança dos seus recursos no mosaico do **Estado de funcionamento de segurança de recursos** . Quando clica em **dados** no mosaico do **Estado de funcionamento de segurança de recursos** , a pá de **Recursos de dados** é aberta com recomendações de SQL para problemas, tais como a encriptação de auditoria e transparente dados não ativado. Também não tem recomendações para o estado de funcionamento geral da base de dados.
![Estado de funcionamento de segurança de recursos][6]

Para saber mais, consulte o artigo [monitorização do Estado de funcionamento de segurança](security-center-monitoring.md).

## <a name="manage-and-respond-to-security-alerts"></a>Gerir e responder a alertas de segurança

Centro de segurança recolhe automaticamente, analisa e integra-se os dados do registo de [Deteção de ameaça de SQL Azure](../sql-database/sql-database-threat-detection-get-started.md), bem como outros recursos Azure, para detectar ameaças reais e reduzir falsos. No Centro de segurança, é apresentada uma lista de alertas de segurança com prioridades juntamente com as informações que necessárias para investigar rapidamente o problema e recomendações para como solucionar uma ataque.

Para ver os alertas, selecione o mosaico de **alertas de segurança** no pá Centro de segurança. No pá **alertas de segurança** , selecione um alerta para saber mais sobre os eventos que o acionou alerta e o que, se existirem, passos que precisa de tomar para solucionar uma ataque. Neste exemplo, ao selecionar **SQL potenciais introdução**.
![Alertas de segurança][7]

Como é mostrado abaixo, o Centro de segurança fornece detalhes adicionais, que oferecem visão acionou o que o alerta, o recurso de destino, quando aplicável a endereço IP de origem e recomendações sobre como solucionar uma.
![Potencial introdução de SQL][8]

Para saber mais, consulte o artigo [Gerir e a responder a alertas de segurança](security-center-managing-and-responding-alerts.md).

## <a name="next-steps"></a>Próximos passos

- [Perguntas mais frequentes do Centro de segurança](security-center-faq.md) — localizar perguntas mais frequentes sobre como utilizar o serviço.
- [Guia de planeamento e operações do Centro de segurança](security-center-planning-and-operations-guide.md) - seguir um conjunto de passos e tarefas para otimizar a utilização do Centro de segurança com base em requisitos de segurança e o modelo de gestão de nuvem da sua organização.
- [Segurança de dados do Centro de segurança](security-center-data-security.md) – Saiba como o Centro de segurança recolhe e processa dados sobre os recursos Azure, incluindo informações de configuração, metadados, registos de eventos, ficheiros de informação de falha de sistema e muito mais.
- [Processamento incidentes de segurança](security-center-incident.md) - Saiba como utilizar a funcionalidade de alerta de segurança no Centro de segurança para ajudá-lo na processamento incidentes de segurança.

<!--Image references-->
[1]: ./media/security-center-sql-database/security-center.png
[2]: ./media/security-center-sql-database/security-center-blade.png
[3]: ./media/security-center-sql-database/security-policy.png
[4]: ./media/security-center-sql-database/recommendation.png
[5]: ./media/security-center-sql-database/turn-on-auditing.png
[6]: ./media/security-center-sql-database/monitor-health.png
[7]: ./media/security-center-sql-database/alert.png
[8]: ./media/security-center-sql-database/sql-injection.png
