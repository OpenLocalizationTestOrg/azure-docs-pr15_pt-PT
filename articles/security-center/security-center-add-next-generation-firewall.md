<properties
   pageTitle="Adicionar uma firewall de geração seguinte no Centro de segurança do Azure | Microsoft Azure"
   description="Este documento mostra-lhe como implementar o as recomendações de centro de segurança do Azure **Adicionar uma Firewall de geração seguinte** e **encaminhar traffice através de NGFW apenas**."
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

# <a name="add-a-next-generation-firewall-in-azure-security-center"></a>Adicionar uma Firewall de geração seguinte no Centro de segurança do Azure

Centro de segurança do Azure pode recomendar que adicione uma firewall de geração seguinte (NGFW) a partir de um parceiro da Microsoft para aumentar a sua protecção de segurança. Este documento orienta-o através de um exemplo de como o fazer.

> [AZURE.NOTE] Este documento apresenta o serviço utilizando uma implementação de exemplo.  Não se trata de um guia passo a passo.

## <a name="implement-the-recommendation"></a>Implementar a recomendação

1. No pá **recomendações** , selecione **Adicionar uma Firewall de geração seguinte**.
![Adicionar uma Firewall de geração seguinte][1]

2. Na pá **Adicionar uma Firewall de geração seguinte** , selecione um ponto final.
![Selecione um ponto final][2]

3. É aberta uma segunda pá de **Adicionar uma Firewall de geração seguinte** . Pode optar por utilizar uma solução existente se disponível ou pode criar um novo. Neste exemplo existem sem soluções existentes disponíveis pelo vamos criar uma nova NGFW.
![Criar novo Firewall geração seguinte][3]

4. Para criar um novo NGFW, selecione uma solução a partir da lista de parceiros integradas. Neste exemplo estamos irá selecionar **Ponto de verificação**.
![Selecione seguinte. ª geração Firewall solução][4]

5. O **Ponto de verificação** pá abre fornecendo-lhe informações acerca da solução do parceiro. Selecione **Criar** no pá informações.
![Pá de informações da firewall][5]

6. É aberta a pá **máquina virtual de criar** . Aqui pode introduzir as informações necessárias para giratório para cima uma máquina de virtual (VM) que será executada a NGFW. Siga os passos e forneça as informações de NGFW necessárias. Selecione OK para aplicar.
![Criar máquina virtual para executar NGFW][6]

## <a name="route-traffic-through-ngfw-only"></a>Encaminhar o tráfego através do NGFW apenas

Regressar à pá **recomendações** . Uma nova entrada foi gerada depois de adicionado um NGFW através do Centro de segurança, chamado **encaminhar tráfego através de NGFW apenas**. Esta recomendação é criada apenas se tiver instalado o seu NGFW através do Centro de segurança. Se tiver pontos finais de acesso à Internet, será que o Centro de segurança que configurar regras de grupo de segurança de rede que forçar o tráfego de entrada para a VM através do seu NGFW.

1. Na **pá recomendações**, selecione **encaminhar tráfego através de NGFW apenas**.
![Encaminhar o tráfego através do NGFW apenas][7]

2. Esta ação abre a pá **encaminhar o tráfego através de NGFW apenas** que enumera VMs que podem encaminhar o tráfego para o. Selecione uma VM a partir da lista.
![Selecione uma VM][8]

3. É aberta um pá para a VM selecionada, apresenta as regras de entrada relacionadas. Uma descrição fornece-lhe mais informações sobre como passos possíveis. Selecione **editar regras de entrada** para continuar com a edição de uma regra de entrada. A expetativa é que **origem** não está definido para **qualquer** para os pontos finais de acesso à Internet ligados com o NGFW. Para obter mais informações sobre as propriedades da regra de entrada, consulte o artigo [NSG regras](../virtual-network/virtual-networks-nsg.md#nsg-rules).
![Configurar regras de para limitar o acesso][9]
![Editar regra de entrada][10]

## <a name="see-also"></a>Consulte também

Este documento mostrava como implementar o recomendação do Centro de segurança "Adicionar uma Firewall de geração seguinte". Para saber mais sobre NGFWs e a solução de parceiro do ponto de verificação, consulte o seguinte:

- [Da próxima geração Firewall](https://en.wikipedia.org/wiki/Next-Generation_Firewall)
- [Ponto de verificação vSEC](https://azure.microsoft.com/marketplace/partners/checkpoint/check-point-r77-10/)

Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Definir políticas de segurança no Centro de segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança.
- [Gerir as recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md) – Saiba como recomendações ajudam a proteger os recursos do Azure.
- [Monitorização de estado de funcionamento de segurança no Centro de segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos Azure.
- [Gerir e a responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
- [Soluções de parceiros de monitorização com o Centro de segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento de soluções de parceiros.
- [Perguntas mais frequentes do Centro de segurança do azure](security-center-faq.md) localizar perguntas mais frequentes sobre como utilizar o serviço.
- Mensagens de [blogue do azure Security](http://blogs.msdn.com/b/azuresecurity/) – localizar blogue sobre Azure segurança e conformidade.

<!--Image references-->
[1]: ./media/security-center-add-next-gen-firewall/add-next-gen-firewall.png
[2]: ./media/security-center-add-next-gen-firewall/select-an-endpoint.png
[3]: ./media/security-center-add-next-gen-firewall/create-new-next-gen-firewall.png
[4]: ./media/security-center-add-next-gen-firewall/select-next-gen-firewall.png
[5]: ./media/security-center-add-next-gen-firewall/firewall-solution-info-blade.png
[6]: ./media/security-center-add-next-gen-firewall/create-virtual-machine.png
[7]: ./media/security-center-add-next-gen-firewall/route-traffic-through-ngfw.png
[8]: ./media/security-center-add-next-gen-firewall/select-vm.png
[9]: ./media/security-center-add-next-gen-firewall/configure-rules-to-limit-access.png
[10]: ./media/security-center-add-next-gen-firewall/edit-inbound-rule.png
