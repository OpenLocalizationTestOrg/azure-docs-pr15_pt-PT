<properties
   pageTitle="Instalar Endpoint Protection no Centro de segurança do Azure | Microsoft Azure"
   description="Este documento mostra-lhe como implementar o recomendação do Centro de segurança do Azure **Endpoint Protection instalar**."
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
   ms.date="08/16/2016"
   ms.author="terrylan"/>

# <a name="install-endpoint-protection-in-azure-security-center"></a>Instalar Endpoint Protection no Centro de segurança do Azure

Centro de segurança do Azure irá recomenda-se a aprovisionar um programa de antimalware a sua máquinas virtuais Azure (VMs) se antimalware já não estiver ativado. Esta recomendação aplicável apenas ao Windows VMs.

> [AZURE.NOTE] Este documento apresenta o serviço utilizando uma implementação de exemplo.  Não se trata de um guia passo a passo.

## <a name="implement-the-recommendation"></a>Implementar a recomendação

1. Na pá **recomendações** , selecione **Instalar Endpoint Protection**.
![Selecione instalar Endpoint Protection][1]

2. O pá **Instalar Endpoint Protection** abre-se de apresentar uma lista de VMs sem antimalware ativado. Selecione a partir da lista de VMs que pretende instalar antimalware no e clique em **instalar no VMs**.
![Selecione VMs instalar antimalware em][2]

3. É aberto o pá **Selecione Endpoint Protection** para permitem-lhe selecionar a solução antimalware que pretende utilizar. Neste exemplo, ao selecionar **Antimalware da Microsoft**.
![Selecione Endpoint Protection][3]

4. Informações adicionais sobre a solução antimalware são apresentadas. Selecione **Criar**.
![Criar solução antimalware][4]

5. Introduza as definições de configuração necessários no pá a **Extensão de adicionar** e, em seguida, clique **em OK**. Para saber mais sobre as definições de configuração, consulte o artigo [predefinidos e personalizados Antimalware configuração](../security/azure-security-antimalware.md#default-and-custom-antimalware-configuration).

[Microsoft Antimalware](../azure-security-antimalware.md) está agora ativo nas VMs selecionados.

## <a name="see-also"></a>Consulte também

Este artigo mostrava como implementar o recomendação do Centro de segurança "Instalar Endpoint Protection." Para saber mais sobre ativar um programa de antimalware no Azure, consulte o seguinte:

- [Microsoft Antimalware para serviços em nuvem e máquinas virtuais](../azure-security-antimalware.md) – Saiba como implementar antimalware da Microsoft.

Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Definir políticas de segurança no Centro de segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança.
- [Gerir as recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md) – Saiba como recomendações ajudam a proteger os recursos do Azure.
- [Monitorização de estado de funcionamento de segurança no Centro de segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos Azure.
- [Gerir e a responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
- [Soluções de parceiros de monitorização com o Centro de segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento de soluções de parceiros.
- [Perguntas mais frequentes do Centro de segurança do azure](security-center-faq.md) localizar perguntas mais frequentes sobre como utilizar o serviço.
- Mensagens de [blogue do azure Security](http://blogs.msdn.com/b/azuresecurity/) – localizar blogue sobre Azure segurança e conformidade.

<!--Image references-->
[1]:./media/security-center-install-endpoint-protection/select-install-endpoint-protection.png
[2]:./media/security-center-install-endpoint-protection/install-endpoint-protection-blade.png
[3]:./media/security-center-install-endpoint-protection/select-endpoint-protection.png
[4]:./media/security-center-install-endpoint-protection/create-antimalware-solution.png
