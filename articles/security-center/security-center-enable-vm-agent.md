<properties
   pageTitle="Activar o agente VM no Centro de segurança do Azure | Microsoft Azure"
   description="Este documento mostra-lhe como implementar o recomendação do Centro de segurança do Azure **Agente de VM ativar**."
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
   ms.date="10/17/2016"
   ms.author="terrylan"/>

# <a name="enable-vm-agent-in-azure-security-center"></a>Activar o agente VM no Centro de segurança do Azure

O agente VM tem de estar instalado em máquinas virtuais (VMs) por ordem para [Ativar a recolha de dados](security-center-enable-data-collection.md).  Centro de segurança do Azure permite-lhe ver quais VMs requerem o agente VM e recomendar que permitem o agente de VM nesses VMs.

O agente VM está instalado por predefinição para VMs que são implementadas do Azure Marketplace. O artigo [VM agente e extensões – parte 2](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/) fornece informações sobre como instalar o agente VM.


> [AZURE.NOTE] Este documento apresenta o serviço utilizando uma implementação de exemplo. Não se trata de um guia passo a passo.

## <a name="implement-the-recommendation"></a>Implementar a recomendação

1. Na **pá recomendações**, selecione **Ativar agente VM**.
![Activar VM agente][1]

2. Esta ação abre a pá **VM agente em falta ou não está a responder**. Este pá listas as VMs que requerem o agente VM. Siga as instruções no pá para instalar o agente VM.
![Agente de VM está em falta][2]

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
[1]: ./media/security-center-enable-vm-agent/enable-vm-agent.png
[2]: ./media/security-center-enable-vm-agent/vm-agent-is-missing.png
