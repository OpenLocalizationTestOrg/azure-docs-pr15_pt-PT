<properties
   pageTitle="Versão do SO de atualização no Centro de segurança do Azure | Microsoft Azure"
   description="Este artigo mostra-lhe como implementar o recomendação do Centro de segurança do Azure **versão do SO de atualização**."
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

# <a name="update-os-version-in-azure-security-center"></a>Atualizar a versão do SO no Centro de segurança do Azure

Para máquinas virtuais (VMs) no serviços em nuvem, Centro de segurança do Azure irá Recomendamos que o sistema operativo (OS) ser atualizado se existir uma versão mais recente disponível.  Funções de web e trabalho em execução no produção faixas são monitorizadas dos serviços apenas na nuvem.

> [AZURE.NOTE] Este documento apresenta o serviço utilizando uma implementação de exemplo.  Não se trata de um guia passo a passo.

## <a name="implement-the-recommendation"></a>Implementar a recomendação

1. Na pá **recomendações** , selecione a **versão do SO de atualização**.
![Atualizar a versão do SO][1]

2. Esta ação abre a **versão do SO atualização**pá. Siga os passos neste pá para atualizar a versão do SO.

## <a name="see-also"></a>Consulte também

Este artigo mostrava como implementar o recomendação do Centro de segurança "Versão atualização SO." Para saber mais sobre os serviços em nuvem e atualizar a versão do SO para um serviço na nuvem, consulte o artigo:

- [Descrição geral dos serviços em nuvem](../cloud-services/cloud-services-choose-me.md)
- [Como atualizar um serviço na nuvem](../cloud-services/cloud-services-update-azure-service.md)
- [Como configurar os serviços em nuvem](../cloud-services/cloud-services-how-to-configure-portal.md)

Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Definir políticas de segurança no Centro de segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança do seu subscrições Azure e grupos de recursos.
- [Gerir as recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md) – Saiba como recomendações ajudam a proteger os recursos do Azure.
- [Monitorização de estado de funcionamento de segurança no Centro de segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos Azure.
- [Gerir e a responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
- [Soluções de parceiros de monitorização com o Centro de segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento de soluções de parceiros.
- [Perguntas mais frequentes do Centro de segurança do azure](security-center-faq.md) localizar perguntas mais frequentes sobre como utilizar o serviço.
- [Blogue do azure Security](http://blogs.msdn.com/b/azuresecurity/) – obter as notícias mais recentes do Azure segurança e informações.

<!--Image references-->
[1]: ./media/security-center-update-os-version/update-os-version.png
