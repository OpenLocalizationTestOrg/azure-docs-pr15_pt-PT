<properties 
   pageTitle="Interface de utilizador do Azure Cativação móvel - alcance critério" 
   description="Saiba como utilizar critérios de filtragem para enviar push campanhas para um subconjunto dos seus utilizadores utilizando Azure Mobile Cativação seleccionado" 
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


# <a name="how-to-use-targeting-criteria-to-send-push-campaigns-to-a-select-subset-of-your-users"></a>Como utilizar critérios de filtragem para enviar push campanhas para um subconjunto selecione dos seus utilizadores

Filtrar a sua audiência por critérios específicos com o botão "Novos critérios" é uma dos conceitos mais eficientes Azure Mobile Cativação que ajuda a enviar relevantes notificações de emissão que os clientes irão responder a em vez de correio publicitário não solicitado todos. Pode limitar a sua audiência com base em critérios padrão e simular puxa para determinar quantas pessoas irão receber a notificação.

**Consulte também:**

- [IU documentação - alcance - nova Push campanha][Link 27]

## <a name="audience-criteria-can-include"></a>Os critérios de audiência podem incluir:
- **Technicals:** Possível com base nas mesmas informações técnicas que pode ver nas secções Monitor e de análise. **Consulte também:** [IU documentação - Analytics] [ Link 15], [IU documentação - Monitor][Link 16]
- **Localização:** As aplicações que utilizam "relatórios de localização em Tempo Real" com Geo vedações podem utilizar Geo localização como um critério para uma audiência desde a localização de GPS de destino. "Preguiça área localização relatórios do Reporting" chamada também ser utilizados para uma audiência desde a localização de número de telemóvel-alvo ("em Tempo Real localização elaboração de relatórios" e "Preguiça área localização relatórios" tem de ser activada a partir do SDK). **Consulte também:** [Documentação do SDK - iOS - integração] [ Link 5], [SDK documentação - Android - integração][Link 5]
- **Atinja comentários:** Pode direccionar a audiência com base nos seus comentários anterior alcance as notificações por através de atingir comentários a partir de anúncios, votações e emite de dados. Isto permite-lhe destino melhor a audiência depois de dois ou três atinja campanhas de que foi a primeira vez. Podem também ser utilizado para filtrarem os utilizadores que já recebeu uma notificação com conteúdo semelhante, definindo uma campanha de não ser enviada aos utilizadores que já recebeu uma campanha anterior específica. Pode até mesmo excluir os utilizadores que estão incluídas uma campanha específica que ainda está activa de receber puxa nova. **Consulte também:** [IU documentação - atinja - emissão Conteúda][Link 29]
- **Instalar o controlo:** Pode controlar informações consoante onde os utilizadores instalado a aplicação. **Consulte também:** [IU documentação - definições][Link 20]
- **Perfil de utilizador:** Pode direcionar padrão com base nas informações de utilizador e que pode destino com base nas informações de aplicação personalizada que criou. Isto inclui os utilizadores que tem sessão iniciados no e utilizadores atendeu perguntas específicas que pediram-los para definir a própria aplicação em vez de apenas como ter respondido a campanhas anteriores. Todas as suas informações de aplicação definidos para a sua aplicação aparecem nesta lista.
- Segmentos: Também pode destino com base em segmentos criados por si com base no que contém vários critérios de comportamento de utilizador específico. Todos os segmentos definidos para a sua aplicação que aparecem nesta lista. **Consulte também:** [IU documentação - segmentos][Link 18]
- **Informações app:** Etiquetas de informações da aplicação personalizadas podem ser criadas a partir de "Definições de" para controlar o comportamento do utilizador. **Consulte também:** [IU documentação - definições][Link 20]

## <a name="example"></a>Exemplo: 
Se pretender transmitir um anúncio apenas para o sub-conjunto de os utilizadores que tenham efetuada uma ação de compra de na aplicação.

1. Aceda à sua página de definições de aplicação, selecione o menu de "Informações App" e selecione "Nova aplicação informações"
2. Registe-se uma nova informações de app booleana denominada "inAppPurchase"
3. Tornar a sua aplicação definir este informações app como "true" quando o utilizador com êxito executa uma compra na aplicação (utilizando o sendAppInfo ("inAppPurchase",...) função)
4. Se não quiser fazê-lo a partir da sua aplicação, pode fazê-lo a partir do seu back-end utilizando o dispositivo API)
5. Em seguida, que precise de criar anúncio de nascimento, com um critério de limitar a sua audiência para os utilizadores ter "inAppPurchase" definida como "true")
 
> Nota: A filtrar com base em critérios diferente de etiquetas de informações da aplicação requer Azure Mobile Cativação reunir informações a partir de dispositivos dos seus utilizadores antes do push é enviado e para que pode fazer com que um atraso. Configuração complexa push opções (como atualizar distintivos) também podem atrasar emite. Utilizar uma campanha de "uma captura" a partir da API Push é o método mais rápido de push absolutas no Azure Mobile Cativação. Utilizar etiquetas de informações de aplicação apenas como critérios de push para uma campanha de atingir (quer a partir API chegar ou a IU) é o método mais rápido seguinte uma vez que as etiquetas de informações da aplicação estão armazenadas no lado do servidor. Utilizar outros critérios-alvo para uma campanha de push é o método de push mais flexível mas mais lento uma vez que tem Azure Mobile Cativação para consultar os dispositivos para poder enviar campanha.
 
![Alcance Criterion1][29] 

## <a name="criterion-options-apply-to"></a>Opções de critério aplicam-se para:
- **Technicals**     
- Nome de firmware: nome Firmware
- Versão do firmware: versão Firmware
- Modelo de dispositivo: modelo de dispositivo
- Fabricante do dispositivo: fabricante do dispositivo
- Versão da aplicação: a versão da aplicação
- Nome do operador: nome Carrier, não definido
- País de Carrier: país Carrier, não definido
- Tipo de rede: tipo de rede
- Região: região
- Tamanho de ecrã: tamanho de ecrã
- **Localização**      
- Última conhecida área: país, região, Localidade
- Em tempo real geo-vedações: lista de POIs (nome, ações), apo Circular (nome, Latitude, Longitude, Radius em metros)
- **Chegar comentários**     
- Comentários de anúncio: anúncio, comentários
- As inquérito comentários: inquérito, comentários
- As respostas comentários de inquérito: as respostas comentários, pergunta e escolha de inquérito
- Comentários de Push de dados: Push de dados, comentários
- **Instalar o controlo**     
- Arquivo: Arquivo, não definido
- Origem: Origem, não definida
- **Perfil de utilizador**     
- Género: masculino e ou feminino, não definido
- Parido data: operador, data, não definido
- Optar ativamente por participar no: VERDADEIRO ou FALSO, indefinido
- **Informações de aplicação**      
- Cadeia: Cadeia, não definida
- Data: operador, data, não definido
- : Operador de número inteiro, não definido
- Booleano: VERDADEIRO ou FALSO, não definido
- **Segmento**    
- Nome dos segmentos (a partir da lista pendente), exclusão (utilizadores de destino que não fazem parte neste segmento).

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
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md
 
