<properties
   pageTitle="Proteger as suas aplicações no Centro de segurança do Azure | Microsoft Azure"
   description="Este endereços de documento recomendações no Centro de segurança do Azure que o ajudam a protegem as suas aplicações Azure e manter-se em conformidade com as políticas de segurança."
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

# <a name="protecting-your-applications-in-azure-security-center"></a>Proteger as suas aplicações no Centro de segurança do Azure

Centro de segurança do Azure analisa o estado de segurança dos seus recursos Azure. Quando o Centro de segurança identifica potenciais vulnerabilidade de segurança, cria recomendações orientá-lo durante o processo de configuração controlos necessários.  Recomendações aplicam-se para tipos de recursos Azure: máquinas virtuais (VMs), de rede, SQL e aplicações.

Este artigo aborda recomendações que se aplicam a aplicações.  Centro de recomendações de aplicação à volta de implementação de uma firewall de aplicação web.  Utilize a tabela abaixo como uma referência para ajudá-lo a compreender as recomendações de aplicações disponíveis e o que cada um deles irá fazer se aplicá-lo.

## <a name="available-application-recommendations"></a>Recomendações de aplicações disponíveis

|Recomendação|Descrição|
|-----|-----|
|[Adicionar uma firewall de aplicação web](security-center-add-web-application-firewall.md)|Recomenda-se de que implemente uma firewall de aplicação web (WAF) para os pontos finais de web. Pode proteger várias aplicações web no Centro de segurança através da adição destas aplicações para o seu implementações WAF existentes. Eletrodomésticos WAF (criados utilizando o modelo de implementação do Gestor de recursos) tem de ser implementada a uma rede virtual em separado. Eletrodomésticos WAF (criados utilizando o modelo de implementação clássico) estão limitados a utilizar um grupo de segurança de rede. Este suporte será expandido para uma implementação totalmente personalizada de um aparelho WAF (clássico) no futuro.|
|[Finalizar protecção da aplicação](security-center-add-web-application-firewall.md#finalize-application-protection)|Para concluir a configuração de um WAF, o tráfego tem de ser reencaminhado para aparelho WAF. Seguir esta recomendação irá concluir as alterações necessárias configuração.|

## <a name="see-also"></a>Consulte também

Para saber mais sobre recomendações que se aplicam a outros tipos de recursos Azure, consulte o seguinte:

- [Proteger as suas máquinas virtuais no Centro de segurança do Azure](security-center-virtual-machine-recommendations.md)
- [Proteger a sua rede no Centro de segurança do Azure](security-center-network-recommendations.md)
- [Proteger o seu serviço do Azure SQL no Centro de segurança do Azure](security-center-sql-service-recommendations.md)

Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Definir políticas de segurança no Centro de segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança do seu subscrições Azure e grupos de recursos.
- [Gerir e a responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
- [Perguntas mais frequentes do Centro de segurança do azure](security-center-faq.md) localizar perguntas mais frequentes sobre como utilizar o serviço.
