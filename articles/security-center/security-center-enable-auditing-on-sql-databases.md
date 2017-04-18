<properties
   pageTitle="Activar a auditoria num bases de dados do SQL no Centro de segurança do Azure | Microsoft Azure"
   description="Este documento mostra-lhe como implementar o recomendação do Centro de segurança do Azure **Active a auditoria em bases de dados SQL**."
   services="security-center"
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
   ms.date="07/29/2016"
   ms.author="terrylan"/>

# <a name="enable-auditing-on-sql-databases-in-azure-security-center"></a>Activar a auditoria num bases de dados do SQL no Centro de segurança do Azure

Centro de segurança do Azure irá Recomendamos que ativar auditoria para todas as bases de dados do SQL se auditoria não estiver já ativado. Auditoria pode ajudar a manter a conformidade de regulamentação, compreender atividade de base de dados e obter visão discrepâncias e anomalias que indiquem preocupações empresariais ou suspeitas violações de segurança.

Uma vez que ligou auditoria pode configurar definições de deteção de ameaça e mensagens de correio eletrónico para receber alertas de segurança. Deteção de ameaça Deteta atividades de base de dados discordantes indicando potenciais ameaças de segurança para a base de dados. Permite-lhe detetar e responder a potenciais ameaças medida que estes ocorrem.

Esta recomendação aplica-se ao serviço do Azure SQL apenas; não inclui SQL em execução no seu máquinas virtuais.

> [AZURE.NOTE] Este documento apresenta o serviço utilizando uma implementação de exemplo.  Não se trata de um guia passo a passo.

## <a name="implement-the-recommendation"></a>Implementar a recomendação

1. Na pá **recomendações** , selecione **Ativar auditoria em bases de dados SQL**.  Esta ação abre a pá **Ativar auditoria em bases de dados SQL** .
![Activar a auditoria num bases de dados SQL][1]

2. Selecione uma base de dados do SQL para activar a auditoria no. Esta ação abre a **deteção de auditoria & ameaça** pá.
![Deteção de auditoria e ameaça][2]
3. No pá **auditoria & ameaça deteção** , selecione **Sucessivamente** em **auditoria**.
![Ativar a deteção de auditoria e ameaça][3]


5. Siga os passos em [Introdução ao deteção de ameaça de base de dados do SQL](../sql-database/sql-database-threat-detection-get-started.md) para ativar e configurar a ameaça detecção e para configurar a lista de mensagens de correio eletrónico que irão receber alertas de segurança de deteção de atividades discordantes.

## <a name="see-also"></a>Consulte também

Este artigo mostrava como implementar o recomendação do Centro de segurança "Ativar auditoria em bases de dados SQL." Para saber mais sobre como proteger a sua base de dados do SQL, consulte o seguinte:

- [Proteger a sua base de dados SQL](../sql-database/sql-database-security.md)

Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Definir políticas de segurança no Centro de segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança do seu subscrições Azure e grupos de recursos.
- [Gerir as recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md) – Saiba como recomendações ajudam a proteger os recursos do Azure.
- [Monitorização de estado de funcionamento de segurança no Centro de segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos Azure.
- [Gerir e a responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
- [Soluções de parceiros de monitorização com o Centro de segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento de soluções de parceiros.
- [Perguntas mais frequentes do Centro de segurança do azure](security-center-faq.md) localizar perguntas mais frequentes sobre como utilizar o serviço.
- [Blogue do azure Security](http://blogs.msdn.com/b/azuresecurity/) – obter as notícias mais recentes do Azure segurança e informações.

<!--Image references-->
[1]: ./media/security-center-enable-auditing-on-sql-databases/enable-auditing-on-sql-databases.png
[2]:./media/security-center-enable-auditing-on-sql-databases/auditing-threat-detection.png
[3]: ./media/security-center-enable-auditing-on-sql-databases/auditing-threat-detection-blade.png
