<properties
   pageTitle="Proteger as suas máquinas virtuais no Centro de segurança do Azure | Microsoft Azure"
   description="Este endereços de documento recomendações no Centro de segurança do Azure que o ajudam a proteger o seu máquinas virtuais e manter-se em conformidade com as políticas de segurança."
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
   ms.date="09/25/2016"
   ms.author="terrylan"/>

# <a name="protecting-your-virtual-machines-in-azure-security-center"></a>Proteger as suas máquinas virtuais no Centro de segurança do Azure

Centro de segurança do Azure analisa o estado de segurança dos seus recursos Azure. Quando o Centro de segurança identifica potenciais vulnerabilidade de segurança, cria recomendações orientá-lo durante o processo de configuração controlos necessários.  Recomendações aplicam-se para tipos de recursos Azure: máquinas virtuais (VMs), de rede, SQL e aplicações.

Este artigo aborda recomendações que se aplicam a VMs.  Centro de recomendações VM à volta de recolha de dados, aplicar atualizações do sistema, antimalware, o aprovisionamento de encriptação seus discos VM e muito mais.  Utilize a tabela abaixo como uma referência para ajudá-lo a compreender as recomendações de VM disponíveis e o que cada um deles irá fazer se aplicá-lo.

## <a name="available-vm-recommendations"></a>Recomendações de VM disponíveis

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
| [Atualizar a versão do SO](security-center-update-os-version.md) | Recomenda-se de que atualiza a versão do sistema operativo (OS) do seu serviço de nuvem para a versão mais recente disponível para a sua família SO.  Para saber mais sobre os serviços em nuvem, consulte o artigo [Descrição geral de serviços em nuvem](../cloud-services/cloud-services-choose-me.md). |
| [Avaliação vulnerabilidade não instalada](security-center-vulnerability-assessment-recommendations.md) | Recomenda-se de que instalar uma solução de avaliação de vulnerabilidade no seu VM. |
| [Solucionar uma vulnerabilidades](security-center-vulnerability-assessment-recommendations.md#review-recommendation) | Permite-lhe ver sistema e de aplicações vulnerabilidades detectadas pela solução de avaliação vulnerabilidade instalada no seu VM. |

## <a name="see-also"></a>Consulte também

Para saber mais sobre recomendações que se aplicam a outros tipos de recursos Azure, consulte o seguinte:

- [Proteger as suas aplicações no Centro de segurança do Azure](security-center-application-recommendations.md)
- [Proteger a sua rede no Centro de segurança do Azure](security-center-network-recommendations.md)
- [Proteger o seu serviço do Azure SQL no Centro de segurança do Azure](security-center-sql-service-recommendations.md)

Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Definir políticas de segurança no Centro de segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança do seu subscrições Azure e grupos de recursos.
- [Gerir e a responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
- [Perguntas mais frequentes do Centro de segurança do azure](security-center-faq.md) localizar perguntas mais frequentes sobre como utilizar o serviço.
