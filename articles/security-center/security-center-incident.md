<properties
   pageTitle="Processamento do incidente de segurança no Centro de segurança do Azure | Microsoft Azure"
   description="Este documento ajuda-o a utilizar o Centro de segurança do Azure capacidades para processar incidentes de segurança."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.topic="hero-article"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="yurid"/>

# <a name="handling-security-incident-in-azure-security-center"></a>Processamento do incidente de segurança no Centro de segurança do Azure 
Triaging e alertas de segurança a investigar podem ser demorados para o mesmo os mais qualificados analistas de segurança e, para muitos é difícil mesmo saber onde pretende começar. Ao utilizar [a análise](security-center-detection-capabilities.md) para ligar as informações entre distintos [alertas de segurança](security-center-managing-and-responding-alerts.md), o Centro de segurança pode fornecer-lhe uma vista individual de uma campanha de ataque e todos os alertas relacionados – rapidamente conseguem compreender que ações intruso demorou e quais os recursos que foram afetados.

Este documento explica como utilizar a funcionalidade de alerta de segurança no Centro de segurança para ajudá-lo processamento incidentes de segurança.


## <a name="what-is-a-security-incident"></a>O que é um incidente de segurança?

No Centro de segurança, um incidente de segurança é uma agregação de todos os alertas de um recurso alinhar com padrões [Eliminar cadeia](https://blogs.technet.microsoft.com/office365security/addressing-your-cxos-top-five-cloud-security-concerns/) . Incidentes apresentado no mosaico de [Alertas de segurança](security-center-managing-and-responding-alerts.md) e pá. Um incidente de revelarem a lista de alertas relacionadas, que permite-lhe obter mais informações sobre cada ocorrência.

## <a name="managing-security-incidents"></a>Gerir incidentes de segurança

Pode rever o seu atual incidentes de segurança verificando o mosaico de alertas de segurança. Aceder ao Portal do Azure e siga os passos abaixo para ver mais detalhes sobre cada incidente de segurança:

1. No dashboard de centro de segurança, irá ver o mosaico de **alertas de segurança** .

    ![Mosaico de alertas de segurança no Centro de segurança](./media/security-center-incident/security-center-incident-fig1.png)

2.  Clique neste mosaico para expandir e se é detetado um incidente de segurança, este irá aparecer em gráfico de alertas de segurança, conforme apresentado abaixo:

    ![Incidente de segurança](./media/security-center-incident/security-center-incident-fig2.png)

3.  Repare que a descrição do incidente de segurança tem um ícone diferente em comparação com outros alertas. Clique na mesma para ver mais detalhes sobre este incidente.

    ![Incidente de segurança](./media/security-center-incident/security-center-incident-fig3.png)

4.  No **incidente** pá irá ver mais detalhes sobre este incidente de segurança, que inclui a respetiva descrição completa, gravidade (que neste caso, é elevada), o estado atual (neste caso ainda está *ativa*, que o utilizador não tomadas uma ação de implica para *dispensar* - isto puder ser processada clicando em botão direito do rato no incidente no pá **alertas de segurança** ) , o recurso intruso (neste *VM1*de maiúsculas e minúsculas), a remediação os passos para o incidente e, no painel inferior tem os alertas que foram incluídos este incidente. Se pretender obter mais informações sobre cada alerta, basta clicar no mesmo e outra pá será aberto, conforme apresentado abaixo:

    ![Incidente de segurança](./media/security-center-incident/security-center-incident-fig4.png)

As informações sobre este pá irão variar de acordo com o alerta. Leia [Gerir e a responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md) para obter mais informações sobre como gerir estes alertas. Algumas considerações importantes sobre esta funcionalidade:

- Um novo filtro permite-lhe personalizar a vista para incidente apenas, só alertas ou ambas. 
- O alerta mesmo pode existir como parte de um incidente (se aplicável) e para que estejam visíveis, como um alerta de autónomo. 
- Dispensar um incidente de não será dispensar os alertas relacionados.

## <a name="see-also"></a>Consulte também

Neste documento, o que aprendeu como utilizar a funcionalidade do incidente de segurança no Centro de segurança. Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Gerir e a responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md)
- [Capacidades de detecção do Centro de segurança do Azure](security-center-detection-capabilities.md)
- [Guia de operações de planeamento do Centro de segurança do Azure e](security-center-planning-and-operations-guide.md)
- [Gerir e a responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md)
- [Perguntas mais frequentes do Centro de segurança do azure](security-center-faq.md)localizar perguntas mais frequentes sobre como utilizar o serviço.
- Mensagens de [blogue do azure Security](http://blogs.msdn.com/b/azuresecurity/)– localizar blogue sobre Azure segurança e conformidade.
