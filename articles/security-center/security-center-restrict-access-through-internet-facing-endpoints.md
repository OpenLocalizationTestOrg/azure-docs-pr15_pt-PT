<properties
   pageTitle="Restringir o acesso através de pontos finais de acesso à Internet no Centro de segurança do Azure | Microsoft Azure"
   description="Este documento mostra-lhe como implementar o recomendação do Centro de segurança do Azure **restringir o acesso através de ponto final oposta de Internet**."
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
   ms.date="10/26/2016"
   ms.author="terrylan"/>

# <a name="restrict-access-through-internet-facing-endpoints-in-azure-security-center"></a>Restringir o acesso através de pontos finais de acesso à Internet no Centro de segurança do Azure

Centro de segurança do Azure irá Recomendamos que restringir o acesso através de pontos finais de acesso à Internet se qualquer um dos seus grupos de segurança de rede (NSGs) tem um ou mais regras de entrada que permita o acesso de "a qualquer" endereço IP de origem. Abrir o acesso a "qualquer" poderá ativar intrusos aceder aos seus recursos. Centro de segurança irá Recomendamos que editar estas regras de entrada para restringir o acesso a endereços IP de origem que realmente têm acesso.

Esta recomendação é gerada para qualquer porta não web que tem "um" como origem.

> [AZURE.NOTE] Este documento apresenta o serviço utilizando uma implementação de exemplo. Não se trata de um guia passo a passo.

## <a name="implement-the-recommendation"></a>Implementar a recomendação

1. Na **pá recomendações**, selecione **restringir o acesso através de ponto final oposta de Internet**.
![Restringir o acesso através da Internet opostas ponto final][1]

2. Esta ação abre a pá **restringir o acesso através de ponto final oposta de Internet**. Este pá listas máquinas virtuais (VMs) com as regras de entrada que criar um potencial problema de segurança. Selecione uma VM.
![Selecione uma VM][2]

3. O pá **NSG** apresenta informações de grupo de segurança de rede, as regras de entrada relacionadas e a VM associada. Selecione **editar regras de entrada** para continuar com a edição de uma regra de entrada.
![Pá de grupo de segurança de rede][3]

4. Em **regras de entrada segurança** pá, selecione a regra de entrada para editar. Neste exemplo, ao selecionar **AllowWeb**.
![Regras de segurança de entrada][4]

  Tenha em atenção, também pode selecionar **regras predefinidas** para ver o conjunto de regras de predefinido contido por todos os NSGs. As regras de predefinidas não podem ser eliminadas, mas, uma vez que estão atribuídos uma prioridade mais baixa, pode ser substituídas pelas regras que criar. Saiba mais sobre [regras de predefinidas](../virtual-network/virtual-networks-nsg.md#default-rules).
![Regras de predefinidas][5]

5. Na pá **AllowWeb** , edite as propriedades da regra de entrada, para que a **origem** for um endereço IP ou bloco de endereços IP. Para obter mais informações sobre as propriedades da regra de entrada, consulte o artigo [NSG regras](../virtual-network/virtual-networks-nsg.md#nsg-rules).

  ![Editar regra de entrada][6]

## <a name="see-also"></a>Consulte também

Este artigo mostrava como implementar o recomendação do Centro de segurança "Restringir access através de ponto final de opostas Internet". Para saber mais sobre a ativação de NSGs e regras, consulte o seguinte:

- [O que é um grupo de segurança de rede (NSG)?](../virtual-network/virtual-networks-nsg.md)
- [Como gerir NSGs através do portal Azure](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)

Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Definir políticas de segurança no Centro de segurança do Azure](security-center-policies.md)– Saiba como configurar políticas de segurança do seu subscrições Azure e grupos de recursos.
- [Gerir as recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md)– Saiba como recomendações ajudam a proteger os recursos do Azure.
- [Estado de funcionamento de segurança no Centro de segurança do Azure de monitorização](security-center-monitoring.md)– Saiba como monitorizar o estado de funcionamento dos seus recursos Azure.
- [Gerir e a responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md)– Saiba como gerir e responder a alertas de segurança.
- [Soluções de parceiros de monitorização com o Centro de segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento de soluções de parceiros.
- [Perguntas mais frequentes do Centro de segurança do azure](security-center-faq.md)localizar perguntas mais frequentes sobre como utilizar o serviço.
- [Blogue do azure Security](http://blogs.msdn.com/b/azuresecurity/)– obter as notícias mais recentes do Azure segurança e informações.

<!--Image references-->
[1]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/restrict-access-thru-internet-facing-endpoint.png
[2]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/select-a-vm.png
[3]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/network-security-group-blade.png
[4]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/inbound-security-rules.png
[5]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/default-rules.png
[6]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/edit-inbound-rule.png
