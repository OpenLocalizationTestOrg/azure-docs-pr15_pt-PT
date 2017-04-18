<properties
   pageTitle="Monitorização e a responder a alertas de segurança na segurança de conjunto de aplicações de gestão de operações e auditoria solução | Microsoft Azure"
   description="Este documento ajuda-o a utilizar a opção de informações da empresa ameaça disponível no OMS segurança e de auditoria para monitorizar e responder a alertas de segurança."
   services="operations-management-suite"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="operations-management-suite"
   ms.topic="article" 
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="yurid"/>

# <a name="monitoring-and-responding-to-security-alerts-in-operations-management-suite-security-and-audit-solution"></a>Monitorização e a responder a alertas de segurança no segurança de conjunto de aplicações de gestão de operações e solução de auditoria

Este documento ajuda-na utilizar a opção de informações da empresa ameaça disponível no OMS segurança e de auditoria para monitorizar e responder a alertas de segurança.

## <a name="what-is-oms"></a>O que é OMS?

Microsoft operações de gestão de conjunto de aplicações (OMS) é a nuvem da Microsoft com base solução de gestão de TI que o ajuda a gerir e proteger o seu no local e na nuvem infraestrutura. Para mais informações sobre OMS, leia o artigo [Conjunto de aplicações de gestão de operações](https://technet.microsoft.com/library/mt484091.aspx).

## <a name="threat-intelligence"></a>Análise de ameaça

Num ambiente empresarial onde os utilizadores têm acesso abrangente à rede e utilizam uma variedade de dispositivos para ligar a dados empresariais, é obrigatório que pode utilizar ativamente monitorizar os recursos e responder rapidamente ao incidentes de segurança. Isto é especialmente importante da perspetiva do ciclo de vida de segurança, uma vez que algumas cybersecurity ameaças não podem elevar o alerta ou suspeitas atividades que podem ser identificadas por controlos técnico de segurança tradicionais. 

Ao utilizar a opção de **Análise de ameaça** disponível no OMS segurança e de auditoria, os administradores de TI pode identificar ameaças de segurança contra ambiente, por exemplo, identificar se um determinado computador fizer parte de um [botnet](https://www.microsoft.com/security/sir/story/default.aspx#!botnetsection). Computadores podem ficar nós um botnet quando intrusos ilicitamente instalar software maligno que liga secretamente este computador para o comando e o controlo. Também poderá identificar potenciais ameaças provenientes de canais de comunicação trabalhos, tal como [darknet](https://www.microsoft.com/security/sir/story/default.aspx#!botnetsection_honeypots_darkents). 

Para criar este intelligence ameaça, OMS segurança e de auditoria utilizam dados provenientes de várias origens no interior da Microsoft. Segurança OMS e de auditoria irão tirar partido estes dados para identificar potenciais ameaças contra o seu ambiente.

O painel de informações da empresa de ameaça é composto por três opções principais:
- Servidores com o tráfego de saída malicioso
- Tipos de ameaças detectado
- Mapa de informações da empresa ameaça

> [AZURE.NOTE] Para obter uma descrição geral de todas as estas opções, leia a [Introdução à segurança de conjunto de aplicações de gestão de operações e solução de auditoria](oms-security-getting-started.md).

### <a name="responding-to-security-alerts"></a>Responder a alertas de segurança

Um dos passos do processo de [resposta do incidente de segurança](https://technet.microsoft.com/library/cc512623.aspx) é identificar gravidade dos sistemas de compromisso. Nesta fase devem executar as seguintes tarefas:

- Determinar a natureza do ataque
- Determinar o ponto de ataque de origem
- Determine a intenção do ataque. Foi homográfico especificamente direcionado na sua organização para obter informações específicas ou -foi aleatórias?
- Identificar os sistemas que tem sido comprometidos
- Identificar os ficheiros que tenham sido acedidos e determinam a sensibilidade desses ficheiros

Pode tirar partido **Ameaça Intelligence** informações no OMS segurança e solução de auditoria para ajudar com estas tarefas. Siga os passos abaixo para aceder a estas opções de **Análise de ameaça** :

1. No dashboard principal de **Conjunto de aplicações do Microsoft operações gestão** , clique em mosaico de **segurança e de auditoria** .

    ![Segurança e de auditoria](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig1.png)

2. No dashboard de **segurança e de auditoria** , verá as opções de **Análise de ameaça** no lado direito, conforme apresentado abaixo:

    ![Ameaça intel](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig2-ga.png)

Estes três mosaicos irão dar-lhe uma descrição geral das ameaças atuais. No **servidor com o tráfego de saída malicioso** que poderá identificar se existir qualquer computador que está a monitorizar (dentro ou fora da sua rede) que está a enviar tráfego malicioso à Internet. 

O mosaico de **tipos de ameaça detectado** apresenta um resumo das ameaças que estão atual "no silvestres", se clicar neste mosaico irá ver mais detalhes sobre estas ameaças conforme mostrado abaixo:

![Tipos de ameaça detectado](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig3.png)

Pode extrair obter mais informações sobre cada ameaça ao clicar no mesmo. O exemplo abaixo apresenta mais detalhes sobre Botnet:

![obter mais detalhes sobre uma ameaça](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig4.png)

Conforme descrito no início desta secção, esta informação pode ser muito útil durante um caso de resposta incidente. Pode também ser importante durante um inquérito forenses, onde precisa localizar a origem do ataque, qual é o sistema foi comprometido e a linha cronológica. Neste relatório-lhe identificar facilmente alguns detalhes chaves sobre o ataque, tais como: a origem do ataque, o IP local que foi comprometido e o estado da sessão atual da ligação. 

O **mapa de informações da empresa ameaça** irá ajudá-lo para identificar as localizações atuais em todo o mundo que tenham o tráfego malicioso. Existem laranja (recebidas) e vermelhas (de envio) nas setas neste mapa que identificam a direcção de tráfego, se clicar dos seguintes setas, esta irá mostrar o tipo de ameaça e a direcção de tráfego conforme apresentado abaixo:

![mapa de intel ameaça](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig5.png)

> [AZURE.NOTE] Pode ver uma demonstração sobre como utilizar esta funcionalidade durante um incidente de processo de resposta ao ver a apresentação [ameaças de segurança do Centro de dados Mitigate com inquérito guiado utilizando o conjunto de aplicações de gestão de operações](https://myignite.microsoft.com/videos/5000) entregue no Microsoft Ignite.

## <a name="see-also"></a>Consulte também

Neste documento, o que aprendeu como utilizar a opção de **Análise de ameaça** no OMS segurança e solução de auditoria para responder a alertas de segurança. Para saber mais sobre a segurança OMS, consulte os artigos seguintes:

- [Descrição geral de gestão conjunto de aplicações (OMS) de operações](operations-management-suite-overview.md)
- [Introdução ao segurança de conjunto de aplicações de gestão de operações e solução de auditoria](oms-security-getting-started.md)
- [Monitorizar recursos na segurança de conjunto de aplicações de gestão de operações e solução de auditoria](oms-security-monitoring-resources.md)
