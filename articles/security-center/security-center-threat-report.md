<properties
   pageTitle="Relatório de informações da empresa do Centro de segurança do Azure ameaça | Microsoft Azure"
   description="Este documento ajuda-o para utilizar relatórios de ameaça de centro de segurança do Azure inteligente durante um inquérito para encontrar mais informações sobre um alerta de segurança."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/17/2016"
   ms.author="yurid"/>

# <a name="azure-security-center-threat-intelligence-report"></a>Relatório do Centro de segurança do Azure ameaça informações da empresa
Este documento explica como relatórios de ameaça de centro de segurança do Azure inteligente pode ajudá-lo mais informações sobre uma ameaça que gerou um alerta de segurança.

## <a name="what-is-a-threat-intelligence-report"></a>O que é um relatório de informações da empresa ameaça?
Deteção de ameaça de centro de segurança funciona através da monitorização de informações de segurança da sua recursos Azure, rede e soluções de parceiros ligada. Esta informação, muitas vezes correlacionar informações a partir de várias origens, para identificar ameaças ele analisa. Este processo faz parte das [capacidades de detecção](security-center-detection-capabilities.md)de centro de segurança. 

Quando o Centro de segurança identifica uma ameaça, irá acionar um [alerta de segurança](security-center-managing-and-responding-alerts.md), que contém informações detalhadas acerca de um evento específico, incluindo sugestões para remediação. Para o ajudar a resposta a incidentes equipas investigar e a solucionar uma ameaças, Centro de segurança inclui um relatório de informações da empresa ameaça que contenha informações sobre a ameaça que foi detectada, incluindo informações tais como o: 

- Atacante identidade ou associações (se estas informações ficam disponíveis)
- Objetivos dos intrusos
- Campanhas de ataque atuais e histórico (se estas informações ficam disponíveis)
- Dos intrusos táticas, ferramentas e procedimentos
- Indicadores associados de comprometer (IoC) como URLs e hashes de ficheiro
- Victimology, que é o setor e prevalência geográfica para ajudá-lo para determinar se os recursos do Azure forem risco
- Informações de mitigação e remediação

>[AZURE.NOTE] A quantidade de informações em qualquer determinado relatório variam; o nível de detalhe baseia-se a atividade e prevalência o malware.

Centro de segurança tem três tipos de relatórios de ameaça, que podem variar de acordo com o ataque. Os relatórios disponíveis são:

- **Relatório de grupo de atividade**: fornece aprofundadas para intrusos e dos seus objetivos e táticas.
- **Relatório de campanha**: foca-se em detalhes de campanhas de ataques específicos. 
- **Relatório de resumo ameaça**: abrange todos os itens nos relatórios de duas anterior.

Este tipo de informação é muito útil durante o processo de [resposta do incidente](security-center-incident-response.md) , onde não existe um inquérito em curso para compreender a origem do ataque, motivações o intruso e o que fazer para mitigar este problema instalado para continuarem. 

## <a name="how-to-access-the-threat-intelligence-report"></a>Como aceder ao relatório de informações da empresa ameaça?

Pode rever os alertas do atuais verificando o mosaico de **alertas de segurança** . Abra o Portal do Azure e siga os passos abaixo para ver mais detalhes sobre cada alerta:

1. No dashboard de centro de segurança, irá ver o mosaico de **alertas de segurança** .

2. Clique no mosaico para abrir o pá de **alertas de segurança** que contém mais detalhes sobre os alertas e clique no alerta de segurança que pretende obter mais informações sobre.

    ![Alertas de segurança](./media/security-center-threat-report/security-center-threat-report-fig1.png)

3. Neste caso o **processo suspeito executado** pá mostra os detalhes sobre o alerta conforme apresentado na figura abaixo:

    ![Detais de alerta de segurança](./media/security-center-threat-report/security-center-threat-report-fig2.png)

4.  A quantidade de informações disponíveis para cada alerta de segurança variam consoante o tipo de alerta. No campo **relatórios** tem uma ligação para o relatório de informações da empresa ameaça. Clique na mesma e outra janela do browser serão apresentados com o ficheiro PDF.

    ![Seleção de armazenamento](./media/security-center-threat-report/security-center-threat-report-fig3.png)

A partir daqui pode transferir o PDF para este relatório e de leitura para obter mais informações sobre o problema de segurança foi detetado e realizar ações com base na informação fornecida.

## <a name="see-also"></a>Consulte também

Neste documento, o que aprendeu como relatórios de ameaça de centro de segurança do Azure inteligente pode ajudar a durante um inquérito acerca de alertas de segurança. Para saber mais sobre o Centro de segurança do Azure, consulte o seguinte:

- [Perguntas mais frequentes do Centro de segurança do azure](security-center-faq.md). Localize perguntas mais frequentes sobre como utilizar o serviço.
- [Tirar partido da Centro de segurança do Azure para resposta incidente](security-center-incident-response.md)
- [Capacidades de detecção do Centro de segurança do Azure](security-center-detection-capabilities.md)
- [Guia de planeamento do Centro de segurança do azure e operações](security-center-planning-and-operations-guide.md). Saiba como planear e compreenda as considerações de estrutura para adotar o Centro de segurança do Azure.
- [Gerir e a responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md). Saiba como gerir e responder a alertas de segurança.
- [Processamento do incidente de segurança no Centro de segurança do Azure](security-center-incident.md)
- [Blogue de segurança azure](http://blogs.msdn.com/b/azuresecurity/). Localize mensagens de blogue sobre Azure segurança e conformidade.
