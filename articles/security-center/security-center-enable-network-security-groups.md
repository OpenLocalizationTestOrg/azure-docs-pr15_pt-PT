<properties
   pageTitle="Ativar a rede de grupos de segurança no Centro de segurança do Azure | Microsoft Azure"
   description="Este documento mostra-lhe como implementar o recomendação do Centro de segurança do Azure **Ativar grupos de segurança de rede**."
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

# <a name="enable-network-security-groups-in-azure-security-center"></a>Ativar a rede de grupos de segurança no Centro de segurança do Azure

Centro de segurança do Azure irá Recomendamos que permitem um grupo de segurança de rede (NSG) se uma já não ativada. NSGs conter uma lista de regras de lista de controlo de acesso (ACL) que permitir ou negar o tráfego de rede para o seu instâncias VM numa rede Virtual. NSGs podem ser associados com sub-redes ou instâncias VM individuais dentro desse sub-rede. Quando um NSG está associada uma sub-rede, as regras ACL aplicam a todas as instâncias VM nessa sub-rede. Além disso, o tráfego para um VM individual pode ser restrito mais associando um NSG diretamente para esse VM. Para saber mais, consulte [o que é um grupo de segurança de rede (NSG)?](../virtual-network/virtual-networks-nsg.md)

Se não tiver NSGs ativados, o Centro de segurança irá apresentar duas recomendações para si: Ativar grupos de segurança de rede em sub-redes e ativar grupos de segurança de rede em máquinas virtuais. Escolha quais nível, sub-rede ou VM, para aplicar NSGs.


> [AZURE.NOTE] Este documento apresenta o serviço utilizando uma implementação de exemplo.  Não se trata de um guia passo a passo.

## <a name="implement-the-recommendation"></a>Implementar a recomendação

1. Na pá **recomendações** , selecione **Ativar grupos de segurança de rede** sub-redes ou em máquinas virtuais.
![Ativar a grupos de segurança de rede][1]

2. Esta ação abre pá **Configurar grupos de segurança de rede em falta** sub-redes ou máquinas virtuais, consoante a recomendação que selecionou. Selecione uma sub-rede ou uma máquina virtual para configurar um NSG no.

  ![Configurar NSG para sub-rede][2]

  ![Configurar o NSG para VM][3]
3. No pá **grupo de segurança de rede escolher** selecione um NSG existente ou selecione para criar um novo NSG.

  ![Escolher o grupo de segurança de rede][4]

Se criar uma nova NSG, siga os passos no artigo [como gerir NSGs através do portal Azure](../virtual-network/virtual-networks-create-nsg-arm-pportal.md) para criar um NSG e definir regras de segurança.

## <a name="see-also"></a>Consulte também

Este artigo mostrava como implementar o recomendação do Centro de segurança "Ativar rede grupos de segurança" para sub-redes ou máquinas virtuais. Para saber mais sobre como activar NSGs, consulte o seguinte:

- [O que é um grupo de segurança de rede (NSG)?](../virtual-network/virtual-networks-nsg.md)
- [Como gerir NSGs através do portal Azure](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)

Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Definir políticas de segurança no Centro de segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança do seu subscrições Azure e grupos de recursos.
- [Gerir as recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md) – Saiba como recomendações ajudam a proteger os recursos do Azure.
- [Monitorização de estado de funcionamento de segurança no Centro de segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos Azure.
- [Gerir e a responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
- [Soluções de parceiros de monitorização com o Centro de segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento de soluções de parceiros.
- [Perguntas mais frequentes do Centro de segurança do azure](security-center-faq.md) localizar perguntas mais frequentes sobre como utilizar o serviço.
- [Blogue do azure Security](http://blogs.msdn.com/b/azuresecurity/) – obter as notícias mais recentes do Azure segurança e informações.

<!--Image references-->
[1]: ./media/security-center-enable-nsg/enable-nsg.png
[2]:./media/security-center-enable-nsg/configure-nsg-for-subnet.png
[3]: ./media/security-center-enable-nsg/configure-nsg-for-vm.png
[4]: ./media/security-center-enable-nsg/choose-nsg.png
