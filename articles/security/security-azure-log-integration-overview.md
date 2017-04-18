<properties
   pageTitle="Introdução à integração de registo do Microsoft Azure | Microsoft Azure"
   description="Saiba mais sobre a integração do registo Azure, das suas capacidades chaves e como funciona."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TerryLanfear"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/24/2016"
   ms.author="TomSh"/>

# <a name="introduction-to-microsoft-azure-log-integration-preview"></a>Introdução à integração de registo do Microsoft Azure (pré-visualização)

Saiba mais sobre a integração do registo Azure, das suas capacidades chaves e como funciona.

## <a name="overview"></a>Descrição geral

Plataforma como um serviço (PaaS) e infraestrutura como um serviço (IaaS) alojado no Azure gerar uma grande quantidade de dados nos registos de segurança. Estes registos contêm informações essenciais que podem fornecer informações da empresa e poderosas otimizadas para violações de política, ameaças internas e externas, problemas de conformidade de regulamentação e anomalias de rede, anfitrião e actividade do utilizador.

Integração do registo Azure permite-lhe integrar registos não processados por parte dos seus recursos Azure os sistemas de gestão de evento (SIEM) e informações de segurança no local. Integração do registo Azure recolhe diagnósticos do Azure a partir do Windows *(WAD)* máquinas virtuais, bem como diagnósticos de soluções de parceiros tal como uma Firewall de aplicação Web (WAF). Esta integração fornece um dashboard unificado para todos os seus ativos, no local ou na nuvem, para que possa agregar, correlacionar, analisar e alertar para eventos de segurança.

![Integração do registo Azure][1]

## <a name="what-logs-can-i-integrate"></a>O que os registos que pode a integrar

Azure gera registo extenso para cada serviço Azure. Estes registos são categorizados por dois tipos principais:

- **Registos de gestão do controlo**, que dão visibilidade para as operações criar de Gestor de recursos do Azure, ATUALIZAR e eliminar. Registos de auditoria Azure é um exemplo deste tipo de registo.
- **Registos de plano de dados**, que dão visibilidade para os eventos elevado como parte da utilização de um recurso Azure. Alguns exemplos deste tipo de registo são os eventos do Windows sistema, segurança, e registos de aplicações numa máquina virtual.

Integração do registo Azure atualmente suporta a integração de registos de auditoria Azure, registos de máquina virtual e alertas do Centro de segurança do Azure.

Se tiver questões sobre a integração de registo do Azure, envie um e-mail para [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

## <a name="next-steps"></a>Próximos passos

Neste documento, foram introduzidas à integração do Azure registo. Para saber mais sobre a integração do registo Azure e os tipos de registos suportados, consulte o seguinte:

- [Integração de registo do Microsoft Azure para registos Azure (pré-visualização)](https://www.microsoft.com/download/details.aspx?id=53324) – Centro de transferências para obter detalhes, requisitos de sistema e instruções de instalação sobre a integração do Azure registo.
- [Começar a trabalhar com a integração do registo Azure](security-azure-log-integration-get-started.md) - este tutorial orienta durante a instalação de integração de registo Azure e integração de registos do armazenamento Azure, registos de auditoria Azure e alertas de segurança Centro.
- [Passos de configuração de parceiro](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – esta mensagem de blogue mostra-lhe como configurar integração de registo Azure para trabalhar com soluções de parceiros Splunk, HP ArcSight e IBM QRadar.
- [Registo azure integração perguntas mais frequentes (FAQ)](security-azure-log-integration-faq.md) - perguntas mais frequentes sobre esta responde a questões sobre a integração do Azure registo.
- [Alertas do Centro de segurança de integração com o Azure sessão integração](../security-center/security-center-integrating-alerts-with-log-integration.md) – este documento mostra-lhe como sincronizar os alertas do Centro de segurança, juntamente com os eventos de segurança de máquina virtual recolhidos pelo diagnósticos do Azure e registos de auditoria Azure, com o seu registo analytics ou solução SIEM.
- [Funcionalidades novas para diagnósticos do Azure e registos de auditoria Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) – esta mensagem de blogue apresenta para registos de auditoria Azure e outras funcionalidades que o ajudam a obter informações para as operações dos seus recursos Azure.

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png
