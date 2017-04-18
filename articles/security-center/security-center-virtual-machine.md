<properties
   pageTitle="Centro de segurança do Azure e Azure máquinas virtuais | Microsoft Azure"
   description="Este documento ajuda-o para compreender como centro de segurança do Azure pode salvaguardar máquinas virtuais do Azure."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/07/2016"
   ms.author="yurid"/>

# <a name="azure-security-center-and-azure-virtual-machines"></a>Centro de segurança do Azure e máquinas virtuais Azure

[Centro de segurança do Azure](https://azure.microsoft.com/services/security-center/) ajuda-o a evitar, detetar e responder a ameaças. Fornece a segurança integrada monitorização e gestão de políticas entre as subscrições do Azure, ajuda a Deteta ameaças caso contrário, aceda não ser e funciona com uma Ecossistema vasto de soluções de segurança.

Este artigo mostra como o Centro de segurança o pode ajudar a proteger o seu máquinas virtuais do Azure (VM).

## <a name="why-use-security-center"></a>Porquê utilizar o Centro de segurança?

Centro de segurança ajuda-o a proteger os dados de máquina virtual no Azure ao fornecer a visibilidade para as definições de segurança do seu computador virtual. Quando o Centro de segurança protege seu VMs, as seguintes capacidades estarão disponíveis:

- Definições de segurança do sistema operativo (OS) com as regras de configuração recomendada
- Segurança do sistema e atualizações críticas que estão em falta
- Recomendações de proteção de ponto final
- Validação de encriptação do disco
- Avaliação de vulnerabilidade e remediação
- Deteção de ameaça

Para além de ajudar a proteger a sua VMs Azure, Centro de segurança também fornece segurança monitorização e gestão de serviços em nuvem, serviços de aplicação, redes virtuais e mais. 

>[AZURE.NOTE] Consulte o artigo [Introdução ao centro de segurança do Azure](security-center-intro.md) para saber mais sobre o Centro de segurança do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar a utilizar com o Centro de segurança do Azure, terá de conhece e considere o seguinte:

- Tem de ter uma subscrição do Microsoft Azure. Consulte o artigo [Preços do Centro de segurança](https://azure.microsoft.com/pricing/details/security-center/) para mais informações sobre camadas de gratuitas e padrão do Centro de segurança.
- Planear a sua adoção do Centro de segurança, consulte o artigo [Guia de planeamento do Centro de segurança do Azure e operações](security-center-planning-and-operations-guide.md) para saber mais sobre considerações de planeamento e operações.
- Para obter informações sobre a compatibilidade de sistema operativo, consulte [Perguntas mais frequentes (FAQ sobre) o Centro de segurança Azure](security-center-faq.md). 

## <a name="set-security-policy"></a>Definir a política de segurança

Recolha de dados tem de estar ativada para que esse centro de segurança do Azure pode recolher informações de que necessita para fornecer recomendações e alertas, que são gerados com base em configurar a política de segurança. Figura abaixo, pode ver que a **recolha de dados** foi **ativado**.

Uma política de segurança define o conjunto de controlos que lhe foram recomendados para recursos numa subscrição especificada ou grupo de recursos. Antes de ativar a política de segurança, tem de ter ativada de recolha de dados, o Centro de segurança recolhe dados a partir do seu máquinas virtuais para avaliar o seu estado de segurança, fornecer as recomendações de segurança e alertá-lo para ameaças. No Centro de segurança, pode definir políticas para a sua subscrições Azure ou os grupos de recursos de acordo com as necessidades de segurança da sua empresa e o tipo de aplicações ou confidencialidade dos dados de cada subscrição. 

![Política de segurança](./media/security-center-virtual-machine/security-center-virtual-machine-fig1.png)

>[AZURE.NOTE] Para saber mais sobre cada **política de prevenção de** disponível, consulte o artigo [definir políticas de segurança](security-center-policies.md) artigo.

## <a name="manage-security-recommendations"></a>Gerir as recomendações de segurança

Centro de segurança analisa o estado de segurança dos seus recursos Azure. Quando o Centro de segurança identifica potenciais vulnerabilidade de segurança, cria recomendações. As recomendações orientá-lo durante o processo de configuração de controlos necessários.

Depois de definir uma política de segurança, o Centro de segurança analisa o estado de segurança dos seus recursos para identificar vulnerabilidade potencial. As recomendações são apresentadas num formato de tabela em que cada linha representa um recomendação específica. A tabela abaixo fornece alguns exemplos de recomendações para Azure VMs e o que cada um deles irá fazer se aplicá-lo. Quando seleciona uma recomendação, serão fornecidos informações que mostra como implementar a recomendação no Centro de segurança.

|Recomendação|Descrição|
|-----|-----|
|[Ativar a recolha de dados para subscrições](security-center-enable-data-collection.md)|Recomenda-se de que ativar recolha de dados na política de segurança para cada uma das suas subscrições e todas as máquinas virtuais (VMs) nas suas subscrições.|
|[Solucionar uma vulnerabilidades SO](security-center-remediate-os-vulnerabilities.md)|Recomenda que alinhar as configurações de SO com as regras de configuração recomendada, por exemplo, não permitem palavras-passe seja guardado.|
|[Aplicar atualizações de sistema](security-center-apply-system-updates.md)|Recomenda-se de que implementar em falta de sistema de segurança e atualizações críticas VMs.|
|[Reiniciar o computador após actualizações do sistema](security-center-apply-system-updates.md#reboot-after-system-updates)|Recomenda-se de que reiniciar uma VM para concluir o processo de aplicar atualizações de sistema.|
|[Instalar Endpoint Protection](security-center-install-endpoint-protection.md)|Recomenda-se de que aprovisionar programas antimalware VMs (apenas para o Windows VMs).|
|[Resolver alertas de estado de funcionamento Endpoint Protection](security-center-resolve-endpoint-protection-health-alerts.md)|Recomenda-se de que resolver falhas de proteção de ponto final.|
|[Activar VM agente](security-center-enable-vm-agent.md)|Permite-lhe ver quais VMs requerem o agente VM. O agente VM tem de ser instalado no VMs para aprovisionar patches a digitalização do plano base a digitalização e antimalware programas. O agente VM está instalado por predefinição para VMs que são implementadas do Azure Marketplace. O artigo [VM agente e extensões – parte 2](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) fornece informações sobre como instalar o agente VM.|
| [Aplicar a encriptação do disco](security-center-apply-disk-encryption.md) |Recomenda-se de que encriptar seus discos VM utilizar encriptação de disco do Azure (Windows e Linux VMs). Encriptação é recomendada para os volumes o sistema operativo e os dados no seu VM.|
| [Avaliação vulnerabilidade não instalada](security-center-vulnerability-assessment-recommendations.md) | Recomenda-se de que instalar uma solução de avaliação de vulnerabilidade no seu VM. |
| [Solucionar uma vulnerabilidades](security-center-vulnerability-assessment-recommendations.md#review-recommendation) | Permite-lhe ver sistema e de aplicações vulnerabilidades detectadas pela solução de avaliação vulnerabilidade instalada no seu VM. |

>[AZURE.NOTE] Para saber mais sobre as recomendações, consulte o artigo [Gerir recomendações de segurança](security-center-recommendations.md) artigo.

## <a name="monitor-security-health"></a>Estado de funcionamento do monitor segurança

Após ativar [as políticas de segurança](security-center-policies.md) para recursos de uma subscrição, o Centro de segurança irá analisar a segurança dos seus recursos para identificar vulnerabilidade potencial.  Pode ver o estado de segurança dos seus recursos, juntamente com quaisquer problemas no pá **Estado de funcionamento de segurança de recursos** . Quando clica em **máquinas virtuais** no mosaico do Estado de funcionamento de **segurança de recursos** , será aberto o pá **máquinas virtuais** com recomendações para sua VMs. 

![Estado de funcionamento de segurança](./media/security-center-virtual-machine/security-center-virtual-machine-fig2.png)

## <a name="manage-and-respond-to-security-alerts"></a>Gerir e responder a alertas de segurança

Centro de segurança recolhe automaticamente, analisa e integra-se os recursos do Azure, da rede e soluções de parceiros ligada (por exemplo, firewall e o ponto final soluções protection), os dados do registo para detectar ameaças reais e reduzir falsos. Por tirar partido de uma agregação diversificada das [capacidades de deteção](security-center-detection-capabilities.md), Centro de segurança é possível gerar alertas de segurança com prioridade alta para o ajudar a investigar o problema e fornecer recomendações para como solucionar uma possíveis ataques rapidamente.

![Alertas de segurança](./media/security-center-virtual-machine/security-center-virtual-machine-fig3.png)

Selecione um alerta de segurança para saber mais sobre o evento (s) que o acionou alerta e o que, se existirem, passos que precisa de tomar para solucionar uma ataque. Alertas de segurança são agrupados por [tipo](security-center-alerts-type.md) e a data.


## <a name="see-also"></a>Consulte também

Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Definir políticas de segurança no Centro de segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança do seu subscrições Azure e grupos de recursos.
- [Gerir e a responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
- [Perguntas mais frequentes do Centro de segurança do azure](security-center-faq.md) localizar perguntas mais frequentes sobre como utilizar o serviço.
