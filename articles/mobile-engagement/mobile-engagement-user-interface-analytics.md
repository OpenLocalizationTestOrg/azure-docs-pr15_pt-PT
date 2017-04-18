<properties
   pageTitle="Interface de utilizador do Azure Cativação móvel - Analytics"
   description="Saiba como analisar dados históricos sobre a sua aplicação utilizando Azure Mobile Cativação"
   services="mobile-engagement"
   documentationCenter=""
   authors="piyushjo"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile"
   ms.date="08/19/2016"
   ms.author="piyushjo"/>

# <a name="how-to-analyze-historical-data-about-your-application"></a>Como analisar dados de histórico sobre a aplicação

Este artigo descreve o separador de **análise** do portal de **Cativação Mobile** . Utilizar o portal de **Mobile Cativação** para monitorizar e gerir as suas aplicações móveis. Tenha em atenção que para começar a utilizar o portal do primeiro tem de criar uma conta **Azure Mobile Cativação** .


A secção de análise da IU fornece agregadas informações sobre a aplicação com base nos dados do histórico que são atualizados a cada 24 horas. As informações são apresentadas nos dashboards diferentes composto por gráficos de linha/barra/circular, grelhas e mapas. Também podem ser transferidos os dados como ficheiros. csv. A maior parte das mesmas informações está disponível em tempo real na secção Monitor da IU e também podem ser acedida a partir da API de análise.

>[AZURE.NOTE] Muitas secções do portal de **Cativação Mobile** IU contêm no botão **Mostrar ajuda** . Prima neste botão para obter mais informações contextuais sobre uma secção.

## <a name="standard-and-custom-analytics"></a>Análise de padrão e personalizada

Azure Mobile Cativação fornece um conjunto de informações analíticos de padrão básicos sobre as suas aplicações que pode ser representado em gráfico assim que a sua aplicação integrar o SDK. Azure Mobile Cativação também fornece a capacidade para recolher informações adicionais analytics personalizado que pretende sobre o comportamento dos seus utilizadores finais. Pode fazê-lo através da criação de um plano de etiqueta de "Etiquetas personalizadas (informações app)", criado a partir das **Definições** para que o Azure Mobile Cativação pode recolher estes dados adicionais para si.



## <a name="analytics"></a>Análise
- Dashboard: Mostra informações gerais sobre os seus utilizadores novos e activas e os respetivos tendências.
- Utilizadores: Os utilizadores estão identificados pelo respectivo identificador de dispositivo: este identificador é exclusiva para cada dispositivo (um novo utilizador é um dispositivo novo). Um utilizador é considerado como nova num intervalo de tempo determinado se ele tiver executado a sua primeira sessão durante este intervalo de tempo. Um utilizador é considerado como retidos se tenha cumprido pelo menos uma sessão durante os últimos 7 dias. Utilizadores ativos que os utilizadores que efetuadas pelo menos uma sessão durante um determinado período. Pode ordenar por, mensais, semanais, diária ou períodos de tempo por hora. Todos os gráficos têm um aspeto semelhantes, mas permitem-lhe para filtrar por diferentes funcionalidades, como a versão da aplicação e, em seguida, para ordenar por um período de tempo. As informações padrão recolhidas pela integrar o SDK incluem o seguinte: utilizadores ativos, o novo utilizador, o número de sessões, o comprimento de cada sessão, informações técnicas sobre a país, locais, localização, carrier de idioma, dispositivos, firmware, rede (conta), versões da aplicação e SDK, utilizado pelos clientes. Estas informações podem ser visualizadas em tempo real a partir da secção monitor.

> Nota: O período de tempo é com base na data a partir das definições de dispositivo dos utilizadores, para que um utilizador cuja phone tem a data de forma incorreta definida poderia sejam apresentadas num período de tempo errado.

- Retenção: Um utilizador é considerado como retidos num intervalo de tempo determinado se tenha cumprido dele primeira sessão durante este intervalo de tempo. Pode alterar os intervalos de tempo durante o qual os utilizadores retidos (e novos utilizadores) são contados para horas, dias, semanas ou meses. As análises de retenção de utilizador são criada na parte superior de coortes. Um coorte é o conjunto de todos os novos utilizadores detetado num determinado período (por exemplo, o conjunto de utilizadores efetuar a sua primeira sessão durante este período). Utilizamos coortes do dia de 1, 2 dias, 4-dia, 7 dias ou 1 mês. Dada uma coorte, 1-diariamente, dia 2, 4-dia, 7 dias, ou 1 mês, Azure Cativação móvel fórmula calcula o conjunto de todos os utilizadores que pertencem à coorte e são ainda estiver ativa (por exemplo, o conjunto de utilizadores que executado pelo menos uma sessão durante o período). Este conjunto de utilizadores é designado por uma versão coorte. (Azure Mobile Cativação pode mostrar-lhe como muitos dos seus utilizadores ainda estiver a utilizar a aplicação, mas apenas o arquivo específico de plataforma pode indicar-lhe como muitos dos seus utilizadores desinstalado o iTunes aplicação - por exemplo, GooglePlay, da loja Windows, etc.).
- Sessões: Uma utilização da aplicação por um utilizador. Sessões são gerados da sequência de actividades executadas por utilizadores (uma actividade está normalmente associado para a utilização de um ecrã da aplicação, mas isto pode variar, consoante a forma como o SDK foi integrado na aplicação). Um utilizador só pode realizar uma actividade de cada vez: uma sessão é iniciada assim que o utilizador inicia a sua primeira atividade e deixa de quando concluir a sua última actividade. Se um utilizador permanece mais do que de aguardar alguns segundos sem executar qualquer actividade, em seguida, sua sequência de atividades é dividida em duas sessões distintos.
- Atividades: Os nomes de cada ecrã na sua aplicação e o comprimento de utilizadores de tempo gastam em cada ecrã. As atividades são uma opção de analítica personalizada que corresponderão às etiquetas "informações app" Configurar para a sua própria aplicação:
- Caminho do utilizador: Mostra como os utilizadores navegar pelos atividades da sua aplicação (ecrãs). Pode mover o controlo de deslize para ajustar o nível de detalhes. Azuis nós representam atividades da sua aplicação. Respetivo tamanho é proporcional o tempo gasto utilizadores no mesmo. Branco nós representam início de sessão e de fim. Vermelhos nós representam causa uma falha. Ligações representam transições entre atividades da sua aplicação (ou entre atividades e causa uma falha). Clique num nó ou uma ligação para apresentar uma descrição com mais informações sobre os seus dados: o tempo gasto no ecrã de um determinado, a contagem de transições e a percentagem de uma transição a partir da atividade de origem para a atividade de destino. (Uma---60%---> B significa que os utilizadores a ser na atividade A vai para a atividade B 60% das vezes.) Pode reorganizar o gráfico que desejar esclarecer a posição é guardada sempre que efetuar uma alteração. Pode mostrar ou ocultar a causa uma falha para aclarar o gráfico.
- Eventos: Ações específicas efetuadas por um utilizador na aplicação. A distribuição dos eventos é apresentada como a contagem de eventos por utilizador por sessão. Um evento representa uma ação instantânea, por exemplo, um clique num botão ou a recepção de uma notificação. (O significado dos eventos depende como o SDK foi integrado na aplicação.) Um evento pode ocorrer durante uma sessão ou uma tarefa ou pode ser autonomamente.
- Tarefas: Semelhante a eventos exceto estes concentrar-no comprimento da ação. Por exemplo, tarefas podem indicar-lhe informações técnicas sobre quanto tempo demora conteúdo para carregar ou uma chamada para o serviço web. Também pode mostrar quanto tempo demora um utilizador para preencher um formulário, crie uma conta ou efectuar uma compra. Uma tarefa representa a duração de uma tarefa, por exemplo, a duração de uma tarefa de transferência ou a hora uma faixa é apresentada no ecrã. (O significado das tarefas depende como o SDK foi integrado na aplicação.) Tarefas são normalmente associadas a tarefas em segundo plano que são efetuadas fora do âmbito de uma sessão (por exemplo, sem qualquer actividade de utilizador).
- Technicals: A informações técnicas sobre os dispositivos dos utilizadores da sua aplicação que pode controlar como a região, Carrier, rede, dispositivo, Firmware e tamanho dos dispositivos dos utilizadores e a versão da sua aplicação e a versão SDK utilizado na sua aplicação de ecrã.
- Erros: Informações sobre erros técnicos dentro da aplicação que não causa a aplicação a falha de sistema. Um erro representa um problema instantâneo, por exemplo, uma falha na rede ou uma manipulação incorretas. (O significado dos eventos depende como o SDK foi integrado na aplicação.) Um erro pode ocorrer durante uma sessão ou uma tarefa ou pode ser autonomamente.
- Causa uma falha: Obter informações sobre erros que fazem com que a sua aplicação para uma falha de sistema. Uma falha de sistema é uma condição inesperada onde a aplicação deixa de efetuar as suas funções esperadas e tem de ser interrompida. Uma falha de sistema é normalmente devido a um erro na aplicação.

![Analytics2][11]

## <a name="accessing-the-retention-overview"></a>Aceder a descrição geral de retenção
![Analytics3][12]

A descrição geral de retenção resultou em erro para baixo a meio para cartões de várias, cada a mostrar a descrição geral para um determinado período de retenção. O período de retenção de 2 dias é visualizado no exemplo. Outros cartões de mostram períodos de retenção 4-dia e 7-dia.

## <a name="understanding-the-retention-overview-cards"></a>Noções sobre os cartões de descrição geral de retenção
![Analytics4][13]

### <a name="each-card-is-composed-of-3-main-parts"></a>Cada cartão é composta por 3 partes principais:
1. 1: coorte e o período considerados
2. 2 a 4: retenção para o período atual
3. 5: um gráfico Sparkline da história

### <a name="here-is-detailed-information-about-each-element"></a>Eis as informações detalhadas sobre cada elemento:
1.    Coorte e período: este cabeçalho dá o tipo de coorte. Aqui "2 dias período" significa que irá olharmos para o comportamento de utilizadores mais do que 2 dias, os utilizadores que chegaram durante um período de 2 dias e se ligam nas seguintes blocos de 2 dias. O exemplo acima considera a atividade de utilizadores entre o 21 e 22nd de Novembro.
2.    Isto dá a taxa de retenção sobre a 21 e 22 de Novembro para os utilizadores a chegar na 19 e 20 de Novembro. Aqui tivemos 1 o utilizador ativo entre a 21 e 22nd, sobre o 3 que foram novos utilizadores entre o 19th e 20.
3.    Este indicador visual dá-as mesmas informações acima representada graficamente. (É o terceiro do círculo a partir do número de 33%.) A cor fornece informações adicionais: verde indica que este número está em crescimento dos cálculos anterior. Amarelo significa estáveis e vermelhos meios decrescentes.
4.    Isto indica os valores utilizados para o cálculo.
5.    Este é um gráfico Sparkline da história dos valores de retenção. Permite-lhe ver os valores no passado ter uma vista abrangente de como evolução.


## <a name="see-also"></a>Consulte também

- [Conceitos][Link 6]
- [Serviço de guia de resolução de problemas][Link 24]

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: ../mobile-engagement-how-tos-first-push.md
[Link 28]: ../mobile-engagement-how-tos-test-campaign.md
[Link 29]: ../mobile-engagement-how-tos-personalize-push.md
[Link 30]: ../mobile-engagement-how-tos-differentiate-push.md
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign.md
[Link 32]: ../mobile-engagement-how-tos-text-view.md
[Link 33]: ../mobile-engagement-how-tos-web-view.md
