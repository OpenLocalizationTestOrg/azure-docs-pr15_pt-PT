<properties
   pageTitle="Descrição geral de gestão conjunto de aplicações (OMS) operações | Microsoft Azure"
   description="Microsoft operações de gestão de conjunto de aplicações (OMS) é baseado na nuvem IT solução de gestão da Microsoft de que o ajuda a gerir e proteger o seu no local e na nuvem infraestrutura.  Este artigo identifica os diferentes serviços incluídos no OMS e fornece ligações para aos respetivos conteúdos detalhadas."
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="bwren" />

# <a name="what-is-operations-management-suite-oms"></a>O que é o conjunto de aplicações de gestão de operações (OMS)?

Microsoft operações de gestão de conjunto de aplicações (OMS) é baseado na nuvem IT solução de gestão da Microsoft de que o ajuda a gerir e proteger o seu no local e na nuvem infraestrutura.  Uma vez que é implementado OMS como um serviço baseado na nuvem, pode tê-lo a trabalhar rapidamente com investimento mínimo nos serviços de infraestrutura.  Novas funcionalidades são entregues automaticamente, poupando-lhe manutenção em curso e actualizar custos.

Para além de fornecer serviços úteis na sua própria, OMS pode integrar com componentes do Centro de sistema como Gestor de operações do Centro de sistema para expandir os investimentos de gestão existente para a nuvem.  Centro de sistema e OMS pode trabalhar em conjunto para fornecer uma experiência de gestão de híbrido completo.

As secções seguintes fornecem uma descrição de alto nível das áreas valor diferente de OMS e os serviços que implementação-las.  Pode consultar a arquitetura OMS para uma descrição geral dos diferentes componentes OMS antes de rever a documentação detalhada para cada um.


## <a name="insight-and-analyticsmediaoperations-management-suite-overviewicon-insight-analyticspng-insight-and-analytics"></a>![Conhecimentos aprofundados e análise](media/operations-management-suite-overview/icon-insight-analytics.png) Conhecimentos aprofundados e análise

[Registo de análise](http://azure.microsoft.com/documentation/services/log-analytics) ajuda-o a recolher, correlacionar, procurar e agir em registo e dados de desempenho gerados pelo sistemas operativos e aplicações. Dá-lhe informações operacionais em tempo real utilizar pesquisa integrada e dashboards personalizados para analisar facilmente milhões de registos através de todas as cargas de trabalho e os servidores, independentemente da sua localização física.

Pode adicionar facilmente soluções para a lógica para a sua análise e de análise de registo que definem dados para serem recolhidos.  As soluções podem incluir funcionalidades adicionais que automaticamente seja entregue agentes com mínimas ou nenhuma configuração.  Além de utilizar ferramentas de análise fornecidas pela soluções individuais, pode executar pesquisas personalizadas ao longo de todo o conjunto de dados para poder correlacionar dados entre os sistemas e aplicações.  


## <a name="automation--controlmediaoperations-management-suite-overviewicon-automation-controlpng-automation--control"></a>![Controlo e de automatização](media/operations-management-suite-overview/icon-automation-control.png) Controlo e de automatização

Automatização Azure automatiza processos administrativos com [runbooks](../automation/automation-runbook-types.md) que são com base em PowerShell e executar na nuvem Azure.  Runbooks pode aceder a qualquer produto ou serviço que pode ser gerido com o PowerShell, incluindo os recursos no outras nuvens tal como o Amazon Web Services (AWS).  Também pode ser executada Runbooks num servidor no Centro de dados locais para gerir recursos locais.

Automatização Azure fornece a gestão de configuração com [PowerShell DSC](../automation/automation-dsc-overview.md).  Pode criar e gerir os recursos DSC alojados no Azure e aplicá-los a sistemas na nuvem e no local para definir e impor automaticamente a configuração dos mesmos.


## <a name="protection-and-recoverymediaoperations-management-suite-overviewicon-protection-recoverypng-protection-and-disaster-recovery"></a>![A proteção e recuperação](media/operations-management-suite-overview/icon-protection-recovery.png) A proteção e recuperação de falhas

[Cópia de segurança do Azure](http://azure.microsoft.com/documentation/services/backup) protege os dados da aplicação e mantém-lo para anos, sem qualquer investimento de capital e com os custos de funcionamento mínimos.  -Pode fazer cópia de segurança dados físicos e virtuais dos servidores do Windows para além das cargas de trabalho de aplicação como SQL Server e o SharePoint.  Também pode ser utilizado pelo sistema Centro de dados Protection Gestor (DPM) para criar uma réplica da dados protegidos para Azure para redundância e armazenamento de longo prazo.

[Recuperação de sites do Azure](http://azure.microsoft.com/documentation/services/site-recovery) contribui para o seu continuidade de negócios e uma estratégia de recuperação (BCDR) falhas por orquestrar replicação, activação pós-falha e recuperação de servidores Windows/Linux físicos, máquinas virtuais de VMware e no local Hyper-V virtual máquinas. Pode criar uma réplica máquinas para um centro de dados secundária ou expandir o seu centro de dados através da replicação-los para Azure. Recuperação de sites também fornece activação simple e recuperação para das cargas de trabalho. Integra-se com mecanismos de recuperação de falhas como SQL Server AlwaysOn e fornece os planos de recuperação para fácil activação pós-falha de cargas de trabalho que são camadas através de múltiplos computadores.


## <a name="oms-security-and-compliancemediaoperations-management-suite-overviewicon-security-compliancepng-security-and-compliance"></a>![OMS segurança e conformidade](media/operations-management-suite-overview/icon-security-compliance.png) Segurança e conformidade
Segurança e conformidade ajuda-o a identificar, avaliar e mitigar riscos de segurança para sua infraestrutura.  Estas funcionalidades do OMS são implementadas várias soluções no registo de análise que analisar os dados do registo e a configuração de sistemas de agente para o ajudar a assegurar a segurança em curso do seu ambiente.

- A [solução de segurança e auditoria](oms-security-getting-started.md ) recolhe e analisa eventos de segurança no sistemas geridos para identificar actividades suspeitas.
- A [solução Antimalware](log-analytics-malware.md ) relatórios sobre o estado de proteção antimalware nos sistemas geridas.  
- A solução actualizações do sistema executa uma análise das atualizações de segurança e outras atualizações no seu sistemas geridos para que lhe identifica facilmente sistemas exigir a aplicação de patches.


## <a name="next-steps"></a>Próximos passos
- Saiba mais sobre [a análise de registo](http://azure.microsoft.com/documentation/services/log-analytics).
- Saiba mais sobre [Automatização Azure](../automation/automation-intro.md).
- Saiba mais sobre a [cópia de segurança do Azure](http://azure.microsoft.com/documentation/services/backup).
- Saiba mais sobre [recuperação Azure de sites](http://azure.microsoft.com/documentation/services/site-recovery).
