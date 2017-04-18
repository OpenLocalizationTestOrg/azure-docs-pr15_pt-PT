<properties
   pageTitle="Capacidades de detecção do Centro de segurança do Azure | Microsoft Azure"
   description="Este documento ajuda-o a compreender como funcionam as capacidades de detecção do Centro de segurança do Azure."
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
   ms.date="09/22/2016"
   ms.author="yurid"/>

# <a name="azure-security-center-detection-capabilities"></a>Capacidades de detecção do Centro de segurança do Azure
Este documento aborda capacidades de detecção avançadas de centro de segurança a Azure, que ajuda a identificar ameaças ativas filtragem os recursos do Microsoft Azure e fornece-lhe as informações necessárias para responder rapidamente.

> [AZURE.NOTE] DLP avançadas está disponíveis no padrão camada do Azure Centro de segurança. Uma avaliação gratuita de 90 dias está disponível. Pode atualizar a partir da selecção de preços camadas na [Política de segurança](security-center-policies.md). Visite a [página do Centro de segurança](https://azure.microsoft.com/pricing/details/security-center/) para saber mais sobre preços. 


## <a name="responding-to-todays-threats"></a>Responder a ameaças de hoje
Não existem sofreram alterações significativas na horizontal a ameaça ao longo dos anos de 20 últimos. No passado, empresas normalmente apenas tinham de se preocupar defacement de web site por intrusos individuais que foram principalmente interessado em ver "o que ele podem fazer". Intrusos hoje são muito mais sofisticados e organizados. Possuem frequentemente objetivos específicos financeiros e estratégicos. Também têm mais recursos disponíveis aos mesmos, tal como podem ser financiados por nacionais Estados-membros ou crime organizado.

Esta abordagem tem por um instrutor para um nível de profissionalismo no classifica intruso sem precedentes. Já não são estes estão interessados em web defacement. São agora está interessado em retire as informações, contas financeiras e dados privados – as quais podem utilizar para gerar dinheiro no mercado aberto ou para tirar partido de um negócio em particular, esquerda ou militar posição. Ainda mais relativo à que essas intrusos com um objectivo financeiro são os intrusos que redes prejudicar a infraestrutura e pessoas.

Em resposta, as organizações com frequência implementar soluções de ponto vários, focar-se em Defender o perímetro da empresa ou os pontos finais ao está à procura de assinaturas ataque conhecidos. Estas soluções tendem para gerar um grande volume de alertas de fidelidade baixa, que requerem um analista de segurança fazer uma triagem e investigar. A maioria das organizações possuem a hora e competências necessárias para responder a estes alertas – ir tantas unaddressed.  Entretanto, intrusos tem evoluiu um respectivos métodos para subvert muitos defesas com base em assinatura e [adaptar à nuvem ambientes](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/). Novas abordagens são necessárias para identificar ameaças emergentes e acelerar deteção e resposta mais rapidamente. 

## <a name="how-azure-security-center-detects-and-responds-to-threats"></a>Como o Centro de segurança do Azure detetar e responde a ameaças

Investigadores de segurança do Microsoft são constantemente atento ameaças. Possuem acesso a um bom conjunto de telemetria adquirida a partir do Estado de presença global da Microsoft na nuvem e no local. Desta coleção chegar wide e diversificada de conjuntos de dados permite à Microsoft descobrir o novo ataque padrões e tendências nos seus produtos de consumidor e enterprise no local, bem como os serviços onlinehttps. Como resultado, Centro de segurança rapidamente pode atualizar os seus algoritmos de deteção como intrusos solte ataques de cada vez mais sofisticadas e novos. Esta abordagem ajuda a manter ritmo com um ambiente do ameaça mover rápida. 

Deteção de ameaça de centro de segurança funciona por automaticamente a recolher informações de segurança os recursos do Azure, da rede e soluções de parceiros ligada. Esta informação, muitas vezes correlacionar informações a partir de várias origens, para identificar ameaças ele analisa. Alertas de segurança são prioridades no Centro de segurança, juntamente com recomendações sobre como solucionar uma a ameaça.

![Recolha de dados do Centro de segurança e apresentação](./media/security-center-detection-capabilities/security-center-detection-capabilities-fig1.png)

Centro de segurança utiliza a análise de segurança avançadas, que vão extremidade para além das abordagens baseado em assinaturas. Descobertas no big data e [máquina de formação](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) tecnologias são utilizadas para avaliar eventos através de ferro a nuvem inteira – detetar ameaças que estaria impossibilita identificar utilizar abordagens manuais e as previsões evolução de ataques. A análise de segurança incluem: 

- **Intelligence ameaça integrado**: procura conhecidos bad intervenientes por tirar partido da análise de global ameaça de produtos Microsoft e serviços, a unidade Microsoft Crimes Digital (DCU), a segurança resposta centro MSRC (Microsoft) e feeds externos.
- **Análise de comportamento**: aplica-se conhecidos padrões para descobrir o comportamento malicioso. 
- **Deteção de anomalia**: utiliza a criação de perfis estatística para criar um histórico do plano base. Apresenta um alerta no desvios estabelecida planos base que está em conformidade com um potencial vector de ataque.


### <a name="threat-intelligence"></a>Análise de ameaça
A Microsoft tem uma grande quantidade de informações da empresa de ameaça global. Fluxos de telemetria de várias origens, tal como Azure, Office 365, Microsoft CRM online, Microsoft Dynamics AX, outlook.com, MSN.com, a Microsoft Digital Crimes unidade (DCU) e a segurança resposta centro MSRC (Microsoft). Investigadores também recebem informações de informações da empresa ameaça que são partilhadas entre fornecedores de serviço de nuvem principais e subscrevem a ameaça feeds de informações da empresa a partir de terceiros. Centro de segurança do Azure pode utilizar estas informações para alertá-lo para ameaças conhecidos bad intervenientes. Alguns exemplos incluem:

- **Comunicação de saída para um endereço IP malicioso**: o tráfego de saída a um botnet conhecido ou darknet provável que indica que o seu recurso está comprometido e intruso-lo a tentar executar comandos no sistema ou exfiltrate dados. Centro de segurança do Azure compara tráfego de rede ameaça global da base de dados da Microsoft e alerta-o se detetar comunicação para um endereço IP malicioso.

## <a name="behavioral-analytics"></a>Comportamento analytics

Análise comportamento é uma técnica que analisa e compara dados a uma colecção de padrões conhecidos. No entanto, estes padrões não estão assinaturas simples. Estes são determinadas através de máquina complexa aprendizagem algoritmos que são aplicados ao grandes conjuntos de dados. Também são determinadas através de análise cuidada comportamentos maliciosos por peritos analistas. Centro de segurança do Azure pode utilizar a análise de comportamento para identificar recursos comprometidos com base na análise de máquina virtual registos, registos de dispositivo de rede virtual, ferro registos, informações de estado da falha de sistema e outras origens. 

Além disso, é correlação com outros sinais para verificar a existência provas de uma campanha executarão. Este correlação ajuda a identificar os eventos que forem consistentes com indicadores estabelecidos de comprometer a. Alguns exemplos incluem:

- **Suspicious execução do processo**: intrusos empregam várias técnicas para executar software malicioso sem deteção. Por exemplo, um intruso poderá dar software maligno os mesmos nomes como ficheiros de sistema legítimos mas coloque estes ficheiros numa localização alternativa, utilize um nome que é muito semelhante a um ficheiro benigno ou máscara a extensão do ficheiro verdadeiro. Comportamentos de processos de modelos de centro de segurança e monitores processam execuções para detetar aberrantes, como estes.  
- **Software maligno de oculto e exploração tentativas**: software maligno sofisticado for capaz de evitarem detecção antimalware tradicional produtos por nunca escrever no disco ou armazenadas no disco de componentes de software de encriptação.  No entanto, essa software maligno pode ser detetado com a análise de memória, tal como o software maligno tem de deixar rastreios na memória para funcionar. Quando falha de software, para uma informação de falha de sistema captura uma parte da memória no momento da falha.  Ao analisar a memória em informação de falha, Centro de segurança do Azure pode detetar técnicas utilizadas para tirar partido vulnerabilidade no software, aceder aos dados confidenciais e sub-repticiamente persistirem numa máquina comprometida sem que afetam o desempenho do seu computador.
- **Laterais movimento e reconhecimento interno**: persistir num dados útil comprometida localize/colheita e de rede, intrusos frequentemente tentam de mover lateralmente a partir do computador comprometido a outras pessoas dentro da mesma rede. Centro de segurança monitoriza atividades processo e inicie sessão para descobrir tentativas para expandir foothold um intruso dentro da rede, como o comando remoto execução rede a pesquisar e enumeração de conta.
- **Scripts de PowerShell maliciosos**: PowerShell está a ser utilizado por intrusos executar código malicioso em máquinas virtuais de destino para uma variedade de efeitos. Centro de segurança inspeciona atividade do PowerShell para prova da atividade suspeita. 
- **Ataques de envio**: intrusos frequentemente de destino na nuvem recursos com o objetivo da utilização desses recursos para montagem ataques adicionais. Máquinas virtuais comprometidas, por exemplo, poderá ser utilizadas para iniciação ataques de força bruta contra restantes máquinas virtuais, enviar SPAM ou analisar portas abertas e outros dispositivos na internet. Aplicando formação de máquina para o tráfego de rede, o Centro de segurança pode detetar quando a norma excedem o comunicações de saída de rede. No caso de SPAM, Centro de segurança também relacionado com tráfego de correio electrónico invulgares com informações da empresa a partir do Office 365 para determinar se o correio é provável que nefarious ou o resultado de uma campanha de correio eletrónico legítimas.  

### <a name="anomaly-detection"></a>Deteção de anomalia

Centro de segurança do Azure também utiliza deteção anomalia para identificar ameaças. Ao contrário de análise comportamento (que depende padrões conhecidos derivados de grandes conjuntos de dados), deteção anomalia é mais "personalizada" e foca-se em linhas de base que sejam específicas nas suas implementações. Formação de máquina é aplicada para determinar a atividade normal para sua implementações e, em seguida, regras são geradas para definir condições de valores atípicos que podem representar um evento de segurança. Eis um exemplo:

- **Entrada RDP/SSH bruta forçar ataques**: seu implementações podem ter ocupadas máquinas virtuais com muitas inícios de sessão de cada máquinas virtuais dia e outras que tenham muito poucos ou qualquer inícios de sessão. Centro de segurança do Azure pode determinar a atividade de início de sessão do plano base para estes máquinas virtuais e utilizar definir o que é fora da atividade de início de sessão normal de aprendizagem automática. Se o número de inícios de sessão ou a hora do dia dos inícios de sessão ou a localização a partir do qual os inícios de sessão forem pedidos ou outras características relacionados com início de sessão serem significativamente diferentes a partir do plano base, em seguida, um alerta poderá ser gerado. Novamente, formação de máquina determina o que é significativo.

## <a name="continuous-threat-intelligence-monitoring"></a>Monitorização de informações da empresa ameaça contínua

Centro de segurança do Azure opera segurança investigação e dados ciência equipas que continuamente monitorizar as alterações na horizontal a ameaça. Isto inclui as seguintes iniciativas:

- **Monitorização de informações da empresa ameaça**: ameaça intelligence inclui mecanismos, indicadores, implicações e acionáveis conselhos sobre existentes ou novas ameaças. Estas informações são partilhadas Comunidade de segurança e Microsoft monitoriza continuamente ameaça feeds de informações da empresa a partir de origens internas e externas.
- **Sinal de partilha**: informações de equipas de segurança Portfólio abrangentes da Microsoft cloud no local serviços, servidores e o cliente de ponto final de dispositivos de e são partilhadas e analisadas. 
- **Especialistas de segurança da Microsoft**: em curso Cativação com equipas ao longo da Microsoft que trabalhar nos campos de segurança especializada, como forensics e deteção de ataque na web.
- **Deteção de sintonização**: algoritmos são executados contra conjuntos de dados do cliente real e investigadores de segurança trabalharem com os clientes para validar os resultados. Verdadeiros e falsos positivos são utilizados para refinar algoritmos de aprendizagem do computador.

Estes esforços combinados como resultado DLP novas e melhoradas, que beneficiar instantaneamente – não haja nenhuma ação para lhe tirar.

## <a name="see-also"></a>Consulte também
Neste documento, o que aprendeu como a deteção de centro de segurança do Azure funcionalidades funcionam. Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Guia de operações de planeamento do Centro de segurança do Azure e](security-center-planning-and-operations-guide.md)
- [Gerir e a responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md)
- [Alertas de segurança por tipo no Centro de segurança do Azure](security-center-alerts-type.md)
- [Estado de funcionamento de segurança no Centro de segurança do Azure de monitorização](security-center-monitoring.md) — Saiba como monitorizar o estado de funcionamento dos seus recursos Azure.
- [Monitorização soluções de parceiros com o Centro de segurança do Azure](security-center-partner-solutions.md) — Saiba como monitorizar o estado de funcionamento de soluções de parceiros.
- [Perguntas mais frequentes do Centro de segurança do Azure](security-center-faq.md) — localizar perguntas mais frequentes sobre como utilizar o serviço.
- [Blogue do Azure Security](http://blogs.msdn.com/b/azuresecurity/) — localizar mensagens de blogue sobre Azure segurança e conformidade.
