<properties
   pageTitle="Adicionar uma firewall de aplicação web no Centro de segurança do Azure | Microsoft Azure"
   description="Este documento mostra-lhe como implementar o as recomendações de centro de segurança do Azure **Adicionar uma firewall de aplicação web** e **Finalize protecção da aplicação**."
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

# <a name="add-a-web-application-firewall-in-azure-security-center"></a>Adicionar uma firewall de aplicação web no Centro de segurança do Azure

Centro de segurança do Azure pode recomendar que adicione uma firewall de aplicação web (WAF) a partir de um parceiro da Microsoft para proteger as aplicações web. Este documento orienta-o através de um exemplo de como o fazer.

> [AZURE.NOTE] Este documento apresenta o serviço utilizando uma implementação de exemplo.  Não se trata de um guia passo a passo.

## <a name="implement-the-recommendation"></a>Implementar a recomendação

1. Na pá **recomendações** , selecione **a aplicação web utilizar firewall de aplicação web seguro**.
![Proteger a aplicação web][1]

2. Na pá **proteger as aplicações web utilizar firewall de aplicação web** , selecione uma aplicação web. É aberta a pá de **Adicionar uma Firewall de aplicação Web** .
![Adicionar uma firewall de aplicação web][2]
3. Pode optar por utilizar uma firewall de aplicação web existente, se disponível ou pode criar um novo. Neste exemplo existem sem WAFs existentes disponíveis pelo vamos criar uma nova WAF.

4. Para criar um novo WAF, selecione uma solução a partir da lista de parceiros integradas. Neste exemplo estamos irá selecionar **Barracuda Web aplicação Firewall**.
5. O pá **Barracuda Web aplicação Firewall** abre fornecendo-lhe informações acerca da solução do parceiro. Selecione **Criar** no pá informações.
![Pá de informações da firewall][3]

6. Abre a pá **Novo Firewall de aplicação Web** , onde pode efetuar passos de **Configuração de VM** e forneça **Informações WAF**. Selecione **a configuração VM**.

7. A **Configuração de VM** pá pode introduzir as informações necessárias para giratório para cima a máquina virtual que será executada a WAF.
![Configuração de VM][4]
8. Regresse ao pá **Novo Firewall de aplicação Web** e selecione **WAF informações**. A **Informações de WAF** pá irá configurar o WAF própria. Passo 7 permite-lhe configurar a máquina virtual no qual o WAF será executado e passo 8 permite-lhe aprovisionar o WAF própria.

## <a name="finalize-application-protection"></a>Finalizar protecção da aplicação

1. Regressar à pá **recomendações** . Uma nova entrada foi gerada depois de criado o WAF, denominado **Finalize protecção da aplicação**. Esta entrada permite-lhe que precisa concluir o processo de realmente sobre cablagem o WAF dentro da rede Virtual Azure para que pode proteger a aplicação.
![Finalizar protecção da aplicação][5]

2. Selecione **Finalize protecção da aplicação**. É aberta uma nova pá. Pode ver que existe uma aplicação web que tem de ter o respectivo tráfego reencaminhado.
3. Selecione a aplicação web. Uma pá abre-se que lhe fornece passos para finalizar a configuração da firewall aplicação web. Conclua os passos e, em seguida, selecione **tráfego restringir**. Centro de segurança, em seguida, irá fazer eléctricos de segurança para si.
![Restringir o tráfego de][6]

> [AZURE.NOTE] Pode proteger várias aplicações web no Centro de segurança através da adição destas aplicações para o seu implementações WAF existentes. Eletrodomésticos WAF (criados utilizando o modelo de implementação do Gestor de recursos) tem de ser implementada a uma rede virtual em separado. Eletrodomésticos WAF (criados utilizando o modelo de implementação clássico) estão limitados a utilizar um grupo de segurança de rede. Este suporte será expandido para uma implementação totalmente personalizada de um aparelho WAF (clássico) no futuro. Saiba mais sobre a [clássica e modelos de implementação do Gestor de recursos](../azure-classic-rm.md) para recursos Azure.

Os registos a partir desse WAF agora são totalmente integrados. Centro de segurança pode iniciar automaticamente reunir e analisar os registos de modo a que o que pode revelar os alertas de segurança importantes para si.

## <a name="see-also"></a>Consulte também

Este documento mostrava como implementar o recomendação do Centro de segurança "Adicionar uma aplicação web." Para saber mais sobre como configurar uma firewall de aplicação web, consulte o seguinte:

- [Configurar uma Firewall de aplicação Web (WAF) para o ambiente de aplicação de serviço](../app-service-web/app-service-app-service-environment-web-application-firewall.md)

Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Definir políticas de segurança no Centro de segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança do seu subscrições Azure e grupos de recursos.
- [Monitorização de estado de funcionamento de segurança no Centro de segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos Azure.
- [Gerir e a responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
- [Gerir as recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md) – Saiba como recomendações ajudam a proteger os recursos do Azure.
- [Perguntas mais frequentes do Centro de segurança do azure](security-center-faq.md) localizar perguntas mais frequentes sobre como utilizar o serviço.
- Mensagens de [blogue do azure Security](http://blogs.msdn.com/b/azuresecurity/) – localizar blogue sobre Azure segurança e conformidade.

<!--Image references-->
[1]: ./media/security-center-add-web-application-firewall/secure-web-application.png
[2]:./media/security-center-add-web-application-firewall/add-a-waf.png
[3]: ./media/security-center-add-web-application-firewall/info-blade.png
[4]: ./media/security-center-add-web-application-firewall/select-vm-config.png
[5]: ./media/security-center-add-web-application-firewall/finalize-waf.png
[6]: ./media/security-center-add-web-application-firewall/restrict-traffic.png
