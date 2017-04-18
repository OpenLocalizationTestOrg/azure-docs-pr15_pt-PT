<properties 
   pageTitle="Interface de utilizador do Azure Cativação móvel - segmentos" 
   description="Saiba como criar e gerir segmentos de utilizadores a identificar padrões de utilização utilizando Azure Mobile Cativação" 
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

# <a name="how-to-create-and-manage-segments-of-users-to-identify-usage-patterns"></a>Como criar e gerir segmentos de utilizadores a identificar padrões de utilização

Este artigo descreve o separador **segmentos** do portal de **Cativação Mobile** . Utilizar o portal de **Mobile Cativação** para monitorizar e gerir as suas aplicações móveis.

A secção segmentos da IU permite-lhe trabalhar em segmentar os seus utilizadores baseados num comportamento diferente e de análise que pode aceder a partir da aplicação e também pode aceder a através da API segmentos. Segmentos são processados pela primeira vez com 24 horas depois são criadas e estes são recomputed a cada 24 horas com base na informação de análise do mais recente. Assim que um segmento é calculado, apresenta um gráfico de "Dia ao histórico de dia" cada dia.


>[AZURE.NOTE] Muitas secções do portal de **Cativação Mobile** IU contêm no botão **Mostrar ajuda** . Prima neste botão para obter mais informações contextuais sobre uma secção.

## <a name="create-segments"></a>Criar segmentos
Pode criar um segmento com base em critérios até 10 durante um período específico para cima para 60 dias no passado a partir da secção de análise. Por exemplo, pode criar um segmento com base no que as pessoas que têm visualizado determinadas páginas ou pesquisou conteúdo específico na sua aplicação nos últimos 10 dias. Estas informações ficam disponíveis na secção de análise. Sim, pode utilizá-lo para criar um segmento e, em seguida, configurar uma notificação de emissão para este subconjunto dos utilizadores de destino para obtê-las para voltar atrás para a aplicação. 
 
> Nota: Assim que um segmento foi calculado, não pode ser editado; só pode ser clonar (copiada) ou destruída (eliminado). Um segmento pode ser clonar dentro da mesma aplicação (com o mesmo AppID) e, também pode ser Clonar para outras aplicações (com um AppID diferente). 
 
 ![segments1][35] 

## <a name="examples-segments"></a>Segmentos de exemplos
 ![segments2][36]

Segmentos permitem-lhe segmento, os utilizadores finais da sua aplicação.
Segmentar os seus utilizadores é uma estratégia de marketing importante. Azure Mobile Cativação permite-lhe obter dados do histórico e criar segmentos personalizados. Este poderosa ferramenta permite-lhe saber mais sobre experiência os seus clientes na sua aplicação. Pode facilmente analisar os segmentos e utilizar os segmentos como destinos push.
Um caso de utilização comum é que pretende enviar uma push uma notificação para encorajar os seus utilizadores finais para classificar a aplicação na loja. Em vez de enviar uma notificação a todos os seus utilizadores finais, pode criar um segmento que especificam a apenas os utilizadores que tem utilizado a sua aplicação diariamente para o último mês e que tenham tido um excelente experiência de utilizador. Quando enviar notificações push menos, altamente alvo, irá obter uma melhor ROI.
 
 ![segments3][37]

### <a name="segments-you-can-create-based-on-the-major-azure-mobile-engagement-elements"></a>Segmentos que pode criar com base nos elementos principais do Azure Mobile Cativação:
- Evento: Crie um segmento um evento específico que destinos da aplicação que aconteceu mais de duas vezes uma semana. 
- Sessão: Crie um segmento de utilizadores que utilizou a aplicação semana passada mais do que 5 vezes.
- Atividade: Crie um segmento de utilizadores que tenha utilizado uma página ou conteúdo mais ou menos do que tempo 10 último mês.
- Tarefa: Crie um segmento de utilizadores que tem de ser concluída uma tarefa de mais de duas vezes num dia.
- Falha de sistema: Crie um segmento de todos os utilizadores que tiveram uma falha de sistema semana passada mais de 10 vezes. (Poderia push neste segmento com um apology ou até mesmo um cupão!)
- Erro: Crie um segmento de todos os utilizadores que tiveram um erro mais de 100 vezes últimos 3 dias.
- Informações de App: Crie um segmento que destina-se uma informações App personalizado que aconteceu durante o último período de dias 25.
 
 ![segments4][38]

Para utilizar o segmento de uma forma optimizada, tem de ter feito uma integração personalizada do SDK na sua aplicação com um plano de marcação de etiquetas de "Informações App".
Em seguida, aceda à home page da interface de, selecione a aplicação que pretende e clique na secção "Segmentos".

1. Selecione a secção "Segmentos".
2. Clique em "novo segmento" botão para criar um novo segmento.

## <a name="real-life-example-create-a-simple-segment-based-on-session-information"></a>Exemplo de vida real: Criar um segmento simple com base na informação de "Sessão"
Crie um segmento de todos os utilizadores finais que tiver utilizado a sua aplicação, pelo menos, 50 vezes na semana passada. A partir daí, localize apenas os utilizadores finais que gastaram, pelo menos, 30 segundos na sua aplicação por sessão. Isto vai mostrar todos os utilizadores finais que têm uma experiência positiva na sua aplicação. Em seguida, o segmento criado pode ser utilizado para transmitir uma notificação a estes utilizadores finais pedir-lhes para classificar a aplicação na loja.
 
 ![segments5][39]

1. Dê um nome ao seu segmento para localizá-lo rapidamente na lista "Segmento".
2. Clique no botão "Criar".
 
 ![segments6][40]

Selecione a sessão.
 
 ![segments7][41]

1. Selecione o período de "Semana passada".
2. Clique em seguinte.
 
 ![segments8][42]

1. Selecionar o operador que é relevante entre a lista: =; ≥, ≤.
2. Introduza o número que pretende.
3. Selecione a ocorrência que pretende. 
4. Clique em seguinte.
Este exemplo está definido para que correspondam que sobre semana passada, ao utilizadores que tenham efetuado sessões, pelo menos, 50.
 
 ![segments9][43]

Para a segmentação de sessão, pode escolher o comprimento por sessão como um critério.

1. Selecione o operador a partir da lista.
2. Forneça o comprimento por sessão.
3. Clique em seguinte.
Neste exemplo, indica que sobre todas as sessões que tenham sido segmentado, na secção que ocorrência, selecione apenas os utilizadores que gastaram a mais de 30 segundos por sessão.
 
 ![segments10][44]

Nome o critério para poder recuperá-la no funil concluído e clique em Concluir.
 
 ![segments11][45]

Quando tiver acabado de configurar o seu critério, aparecerá no funil de segmento.
Uma vez que um segmento é baseado nos dados de análise, são processados segmentos de uma vez por dia.
Neste exemplo, 47,7% dos utilizadores finais total correspondido o critério. Devem ser os utilizadores que tenham tido uma boa experiência e provável que fornecem-lhe uma classificação superior se emissão uma notificação solicitando-lhes para classificar a aplicação na loja.


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
 
