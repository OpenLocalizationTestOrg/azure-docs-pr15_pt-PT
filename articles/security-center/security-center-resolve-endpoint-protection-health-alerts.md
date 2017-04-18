<properties
   pageTitle="Resolver alertas de estado de funcionamento de proteção de ponto final no Centro de segurança do Azure | Microsoft Azure"
   description="Este documento mostra-lhe como implementar o recomendação do Centro de segurança do Azure **alertas de estado de funcionamento de resolver Endpoint Protection**."
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

# <a name="resolve-endpoint-protection-health-alerts-in-azure-security-center"></a>Resolver alertas de estado de funcionamento de proteção de ponto final no Centro de segurança do Azure

Centro de segurança do Azure irá Recomendamos que resolva os alertas de estado de funcionamento de proteção de ponto final detectado.  Centro de segurança permite-lhe ver que máquinas virtuais (VMs) tem falhas de proteção de ponto final e quantas falhas.

> [AZURE.NOTE] Este documento apresenta o serviço utilizando uma implementação de exemplo. Não se trata de um guia passo a passo.

## <a name="implement-the-recommendation"></a>Implementar a recomendação

1. Na **pá recomendações**, selecione **alertas de estado de funcionamento de resolver Endpoint Protection**.
![Resolver alertas de estado de funcionamento de proteção de ponto final][1]

2. Esta ação abre a pá **Endpoint Protection falha** listas VMs com falhas e o número de insucessos para cada VM. Selecione uma VM a partir da lista.
![Falha de proteção de ponto final][2]

3. Uma **Lista de falhas** pá abre-se para a VM selecionada, apresentar uma lista de falhas. Selecione uma falha na lista para obter mais informações. Esta ação abre uma pá com informações sobre a falha selecionada.
![Lista de falhas][3]
  ![evento falha][4]

## <a name="see-also"></a>Consulte também

Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Definir políticas de segurança no Centro de segurança do Azure](security-center-policies.md)– Saiba como configurar políticas de segurança do seu subscrições Azure e grupos de recursos.
- [Gerir as recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md)– Saiba como recomendações ajudam a proteger os recursos do Azure.
- [Estado de funcionamento de segurança no Centro de segurança do Azure de monitorização](security-center-monitoring.md)– Saiba como monitorizar o estado de funcionamento dos seus recursos Azure.
- [Gerir e a responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md)– Saiba como gerir e responder a alertas de segurança.
- [Soluções de parceiros de monitorização com o Centro de segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento de soluções de parceiros.
- [Perguntas mais frequentes do Centro de segurança do azure](security-center-faq.md)localizar perguntas mais frequentes sobre como utilizar o serviço.
- [Blogue do azure Security](http://blogs.msdn.com/b/azuresecurity/)– obter as notícias mais recentes do Azure segurança e informações.

<!--Image references-->
[1]: ./media/security-center-resolve-endpoint-protection/resolve-endpoint-protection.png
[2]: ./media/security-center-resolve-endpoint-protection/endpoint-protection-failure.png
[3]: ./media/security-center-resolve-endpoint-protection/failure-list.png
[4]: ./media/security-center-resolve-endpoint-protection/failure-event.png
