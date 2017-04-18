<properties
   pageTitle="Aplicar atualizações de sistema no Centro de segurança do Azure | Microsoft Azure"
   description="Este documento mostra-lhe como implementar o as recomendações de centro de segurança do Azure **Aplicar atualizações de sistema** e **reiniciar o computador após actualizações do sistema**."
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

# <a name="apply-system-updates-in-azure-security-center"></a>Aplicar atualizações de sistema no Centro de segurança do Azure

Centro de segurança do Azure diariamente monitoriza Windows e Linux máquinas de virtuais (VMs) para o sistema operativo actualizações em falta. Centro de segurança obtém uma lista de segurança disponíveis e atualizações críticas a partir do Windows Update ou Windows Server Update Services (WSUS), dependendo de quais serviço está configurado numa VM do Windows.  Centro de segurança também verifica a existência as atualizações mais recentes nos sistemas de Linux. Se a VM é uma atualização de sistema, o Centro de segurança, recomendamos que se aplicam actualizações do sistema

> [AZURE.NOTE] Este documento apresenta o serviço utilizando uma implementação de exemplo.  Não se trata de um guia passo a passo.

## <a name="implement-the-recommendation"></a>Implementar a recomendação

1. Na pá **recomendações** , selecione **Aplicar atualizações de sistema**.
![Aplicar atualizações de sistema][1]

2. O pá **Aplicar atualizações de sistema** é aberta apresenta uma lista de VMs em falta actualizações do sistema. Selecione uma VM.
![Selecione uma VM][2]

3. Uma pá é aberta apresenta uma lista de atualizações em falta para esse VM. Selecione uma atualização de sistema. Neste exemplo, ao selecionar KB3156016.
![Atualizações de segurança em falta][3]

4. Siga os passos a pá **Actualização de segurança** para aplicar a atualização em falta.
![Atualização de segurança][4]

## <a name="reboot-after-system-updates"></a>Reiniciar o computador após actualizações do sistema

5. Regressar à pá **recomendações** . Uma nova entrada foi gerada depois de aplicado actualizações do sistema, denominadas **reiniciar o computador após actualizações do sistema**. Esta entrada permite-lhe que precisa reiniciar a VM para concluir o processo de aplicar atualizações de sistema.
![Reiniciar o computador após actualizações do sistema][5]

6. Selecione **reiniciar o computador após actualizações do sistema**. Esta ação abre pá **que está pendente para concluir actualizações do sistema um reinício** apresentar uma lista de VMs que precisa de reiniciar para concluir o processo de atualizações do sistema de aplicar.
![Reinicie o pendentes][6]

Reinicie a VM a partir do Azure para concluir o processo.

## <a name="see-also"></a>Consulte também

Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Definir políticas de segurança no Centro de segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança do seu subscrições Azure e grupos de recursos.
- [Gerir as recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md) – Saiba como recomendações ajudam a proteger os recursos do Azure.
- [Monitorização de estado de funcionamento de segurança no Centro de segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos Azure.
- [Gerir e a responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
- [Soluções de parceiros de monitorização com o Centro de segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento de soluções de parceiros.
- [Perguntas mais frequentes do Centro de segurança do azure](security-center-faq.md) localizar perguntas mais frequentes sobre como utilizar o serviço.
- Mensagens de [blogue do azure Security](http://blogs.msdn.com/b/azuresecurity/) – localizar blogue sobre Azure segurança e conformidade.

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/recommendation.png
[2]:./media/security-center-apply-system-updates/select-vm.png
[3]: ./media/security-center-apply-system-updates/missing-security-updates.png
[4]: ./media/security-center-apply-system-updates/security-update.png
[5]: ./media/security-center-apply-system-updates/reboot-after-system-updates.png
[6]: ./media/security-center-apply-system-updates/restart-pending.png
