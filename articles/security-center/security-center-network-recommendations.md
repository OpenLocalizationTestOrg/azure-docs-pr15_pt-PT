<properties
   pageTitle="Proteger a sua rede no Centro de segurança do Azure | Microsoft Azure"
   description="Este endereços de documento recomendações no Centro de segurança do Azure que o ajudam a proteger a sua rede Azure e manter-se em conformidade com as políticas de segurança."
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
   ms.date="08/04/2016"
   ms.author="terrylan"/>

# <a name="protecting-your-network-in-azure-security-center"></a>Proteger a sua rede no Centro de segurança do Azure

Centro de segurança do Azure analisa o estado de segurança dos seus recursos Azure. Quando o Centro de segurança identifica potenciais vulnerabilidade de segurança, cria recomendações orientá-lo durante o processo de configuração controlos necessários.  Recomendações aplicam-se para tipos de recursos Azure: máquinas virtuais (VMs), de rede, SQL e aplicações.

Este artigo aborda recomendações que se aplicam a sua rede.  Centro de recomendações de rede à volta de seguintes geração firewalls, grupos de segurança de rede, configurar regras de tráfego de entrada e mais.  Utilize a tabela abaixo como uma referência para ajudá-lo a compreender as recomendações de rede disponíveis e o que cada um deles irá fazer se aplicá-lo.

## <a name="available-network-recommendations"></a>Recomendações de rede disponíveis

|Recomendação|Descrição|
|-----|-----|
|[Adicionar uma Firewall de geração seguinte](security-center-add-next-generation-firewall.md)|Recomenda-se de que adicione uma Firewall de geração seguinte (NGFW) a partir de um parceiro da Microsoft para aumentar a sua protecção de segurança.|
|[Encaminhar o tráfego através do NGFW apenas](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only)|Recomenda-se de que configurar regras de grupo (NSG) de segurança de rede forçar o tráfego de entrada para a VM através do seu NGFW.|
|[Ativar o grupos de segurança de rede sub-redes ou máquinas virtuais](security-center-enable-network-security-groups.md)|Recomenda-se de que ativar NSGs num sub-redes ou VMs.|
|[Restringir o acesso através da Internet opostas ponto final](security-center-restrict-access-through-internet-facing-endpoints.md)|Recomenda-se de que configurar regras de tráfego de entrada para NSGs.|

## <a name="see-also"></a>Consulte também

Para saber mais sobre recomendações que se aplicam a outros tipos de recursos Azure, consulte o seguinte:

- [Proteger as suas máquinas virtuais no Centro de segurança do Azure](security-center-virtual-machine-recommendations.md)
- [Proteger as suas aplicações no Centro de segurança do Azure](security-center-application-recommendations.md)
- [Proteger o seu serviço do Azure SQL no Centro de segurança do Azure](security-center-sql-service-recommendations.md)

Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Definir políticas de segurança no Centro de segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança do seu subscrições Azure e grupos de recursos.
- [Gerir e a responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
- [Perguntas mais frequentes do Centro de segurança do azure](security-center-faq.md) localizar perguntas mais frequentes sobre como utilizar o serviço.
