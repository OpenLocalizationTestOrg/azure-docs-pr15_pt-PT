<properties 
   pageTitle="Interface de utilizador do Azure Cativação móvel - alcance como"
   description="Descrição geral da Interface de utilizador para Azure Cativação móvel" 
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

# <a name="how-to-get-started-using-and-managing-pushes-to-reach-out-to-your-end-users"></a>Como começar a utilizar e gerir emite para começar uma conversação aos seus utilizadores finais

Assim que o SDK está totalmente integrado na sua aplicação, pode começar a utilizar a secção de atingir da IU para as notificações Push para os utilizadores da sua aplicação.  

## <a name="do-your-first-push-notification-campaign"></a>Fazer a sua primeira campanha de notificações Push
-    Confirme que o seu alcance está integrado na sua aplicação com o SDK. 
-    Selecione a aplicação
 
![First1][1]

-    Ir para a secção "Alcance" e clique em "novo anúncio"
 
![First2][2]

-    Crie uma nova campanha e o nome
 
 ![First3][3]

-    Selecione a forma como a notificação deve ser entregue, como no-app apenas
 
![First4][4]

-    Criar a mensagem que pretende de emissão
 
![First5][5]

-    Pode escrever um título a notificação de (opcional).
-    Escreva o conteúdo da mensagem push.
-    Pode carregar uma imagem. Tenha em atenção que o tamanho do ficheiro não pode exceder 32.768 bytes.
-    Também tem a capacidade de seleccionar mais opções, mas para o foco neste tutorial, irá Vemos que mais tarde.

-    Selecione o tipo de conteúdo apenas como notificação
 
![First6][6]

-    Criar uma campanha de push e este aparecerá na sua lista de campanha.
 
![First7][7]

## <a name="test-your-push-notification-campaign"></a>Testar a sua campanha de notificações Push
![Teste1][8]

-    Registe-se o seu dispositivo.
-    Clique na caixa de verificação do dispositivo que pretende emissão.
-    Clique no botão "Teste" para enviar a push para o dispositivo.
 
![Teste2][9]

-    Ativar a campanha
 
![Test3][10]

-    Agora que criou a sua campanha que precise de ativá-lo a notificação seguia aos seus utilizadores.
 
## <a name="send-personalized-pushes"></a>Enviar puxa personalizada
-    Este exemplo cria um push sempre que for introduzido um código de desconto personalizado para as notificações push.
 
![Personalize1][11]

Personalização funciona substituindo um marcador à partir de uma etiqueta de informações da aplicação assim, terá de para se certificar de que o utilizador tem a aplicação-info inicial definida pela primeira vez. Neste exemplo, os utilizadores alvo terá uma tag de informações da aplicação rebate_code definido com o nome.
Como ver acima o conteúdo de notificações push inclui o marcador ${rebate_code}, que irá indicar que deve ser substituídos pelo conteúdo em si da etiqueta de informações de aplicação.

> Aviso: Se a etiqueta de informações da aplicação não está definida para o utilizador, o utilizador não vai receber a push.

-    Resultado
 
![Personalize2][12]

### <a name="you-can-further-personalize-the-text-your-notification"></a>Pode ainda mais personalizar o texto sua notificação de
![Personalize3][13]

-    Incluindo o título da notificação de
-    E o conteúdo da mensagem.
-    Selecione o tipo de anúncio (vista de texto ou vista Web)
 
![Personalize4][14]

### <a name="the-body-of-an-announcement-may-also-be-personalized-with"></a>O corpo de um anúncio também pode ser personalizado com:
-    O URL de ação que pretende personalizar a página de destino
-    O título,
-    O corpo da mensagem.
 
 
## <a name="differentiate-your-push-notification-in-or-out-of-app"></a>Diferenciar sua notificação de emissão (ou reduzir aplicação)
-    Selecione o tipo de notificação que irá emissão, selecione a aplicação, aceda à secção "Atinja", selecione ou criar uma campanha de push e aceda à secção "Notificação".
 
-    Clique em "modo de entrega" em que pretende.
-    Clique na caixa de verificação "Restringir atividades" quando pretender que a notificação de ocorre em atividades específicas (ecrãs).

![Differentiate1][15]

### <a name="out-of-app-only-delivery-mode"></a>Modo de entrega de "Fora do aplicação"
![Differentiate2][16]

"Fora do aplicação" modo de entrega fornece notificações push quando a aplicação está fechada. Esta é a notificação de push padrão.
Quando seleciona "fora do aplicação", tem ter fornecido os certificados da plataforma na qual a aplicação está a criar no (APNS ou GCM).

### <a name="see-also"></a>Consulte também
-  [Serviço de notificações – certificados de Push da Apple](http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9), Google Cloud mensagens – certificado] (http://developer.android.com/google/gcm/index.html) 

### <a name="in-app-only-delivery-mode"></a>"no-aplicação apenas" modo de entrega
![Differentiate3][17]

Modo de entrega "No-aplicação apenas" fornece notificações push quando a aplicação estiver em execução.
Esta notificação, não terá de seguir o sistema de APNS e GCM.
Pode utilizar o sistema de entrega na aplicação para alcançar os seus utilizadores finais.
Totalmente pode personalizar a notificação e decidir pela qual atividade (ecrã) a notificação de irão aparecer.

### <a name="anytime-delivery-mode"></a>Modo de entrega "Qualquer momento"
Pode selecionar um modo de entrega "Sempre", assegura que para alcançar o utilizador final se está a executar a aplicação ou não.
Quando seleciona "Sempre", tem ter fornecido os certificados da plataforma na qual a aplicação está a criar relativamente a (APNS ou GCM). 
 
## <a name="schedule-a-push-campaign"></a>Agendar uma campanha de Push
### <a name="plan-to-start-a-campaign"></a>Plano para iniciar uma campanha
![Shedule1][18]

É o 21 de Março e tiver um anúncio para fazer e planeadas para o 22 de Março à meia-noite. Não tem de manter-se à frente a interface de fazer um push! Pode planear com antecedência os minutos exato notificações serão enviadas.
-    Desmarque a opção "Nenhum" caixa de verificação e selecione uma hora de início 
-    Selecione a data e hora que pretende iniciar campanha push.

### <a name="plan-to-end-a-campaign"></a>Plano para terminar uma campanha
![Shedule2][19]

Pretende que o seu campanha para parar no 25º de Março em 3. nove pm mas sabe que não lá estejam para fazê-lo.
Não tem de manter-se à frente a interface de notificações push! Pode planear com antecedência os minutos exato que deixarão de campanha.
-    Clique no "Nenhum" caixa de verificação ou selecione uma hora de fim
-    Selecione a data e hora que pretende para concluir a campanha push.

### <a name="end-a-campaign-manually"></a>Terminar uma campanha de manualmente
![Shedule3][20]

Por predefinição, o "nenhum" caixas de verificação estão selecionadas.
Isto significa que a campanha será iniciado assim que a ativá-lo na secção alcance e termina quando irá pará-lo na secção que atingir.
 
> Nota: Campanhas criadas sem uma data de fim armazenam o push localmente no dispositivo e mostrá-la da próxima vez que a aplicação é aberta, mesmo se campanha é terminada manualmente.

## <a name="enhance-a-push-notification-with-a-text-view"></a>Melhorar uma notificação de emissão com uma vista de texto
### <a name="what-is-a-text-view"></a>O que é uma vista de texto?
![TextView1][21]

Uma vista de texto é um pop-up com o conteúdo de texto. Este pop-up aparece depois do utilizador final tem clicado a notificação de push.
Uma vista de texto permite-lhe apresentar mais conteúdo para o utilizador final. Também é a oportunidade de apresentar uma chamada a ação como saltar para uma página da sua aplicação, redirecionar para um arquivo, abertura de uma página web, enviar mensagem de correio electrónico, iniciar uma pesquisa localizadas geo, etc...

### <a name="example-text-view"></a>Exemplo: Vista de texto
-    Criar uma campanha de notificações Push na secção "Alcance" e dê um nome ao seu campanha
 
![TextView2][22]

-    Escreva a mensagem que irá aparecer a notificação de.
-    Selecione o tipo de conteúdo de anúncio de nascimento de "texto"
 
![TextView3][23]

> Nota: quando prime uma vista de texto,-lo sempre vem com uma notificação pela primeira vez. 

- Definir o texto (depois de ter selecionado o conteúdo de anúncio de nascimento de texto, a sub-secção irão aparecer, permitindo-lhe definir o texto que pretende para que seja apresentado.)
 
![TextView4][24]

-    Escreva o título que será apresentado na parte superior da mensagem.
-    Escreva o conteúdo principal da vista de texto.
-    Escreva o conteúdo que será apresentado no botão de ação (um botão de ação permite que a aplicação tornar uma acção específica como abrir uma página da aplicação, redirecionar para uma loja de aplicações ou qualquer tipo de origens que pode fornecer).
-    Escrever o conteúdo que será apresentado no botão Sair (ao clicar no botão Sair, a vista de texto desaparecem.)
 
-    Criar uma campanha de notificações push e este aparecerá na lista da campanha.
 
![TextView5][25]

-    Ative a sua campanha de notificações push para enviar a visualização do texto para os seus utilizadores.
 
![TextView6][26]

-    Resultado
 
![TextView7][27]

-    O utilizador recebe a notificação e clique no mesmo.
-    A vista de texto é apresentado como um pop-up permitindo que o utilizador interagir com o mesmo.

## <a name="enhance-a-push-notification-with-a-web-view"></a>Melhorar uma notificação de emissão com uma vista da Web
### <a name="what-is-a-web-view"></a>O que é uma vista da Web?
![WebView1][28]

Uma vista da web é um pop-up com o conteúdo da web. Este pop-up é apresentada quando o utilizador final tem clicada a notificação de push.
Uma vista da web permite-lhe ter mais interação com o utilizador final.
Também é a oportunidade de apresentar uma chamada a ação como redirecionamento a App Store, abertura de uma página web, enviar mensagem de correio electrónico, iniciar uma pesquisa localizadas geo, etc...

### <a name="example-web-view"></a>Exemplo: Vista da Web
-    Criar uma campanha de Push na secção "Alcance" e dê um nome ao seu campanha.
 
![WebView2][29]

-    Escreva a mensagem que irá aparecer a notificação de.
-    Selecione o tipo de conteúdo anúncio como "web"
 
![WebView3][30]

### <a name="about-announcement-types"></a>Informações sobre tipos de anúncio:
- Notificação apenas: é uma notificação padrão simple. O que significa que que se um utilizador clica no mesmo, irá aparecer sem vista adicional, mas irá ocorrer apenas a ação associada à mesma.
- Anúncio de nascimento de texto: é uma notificação que realiza o utilizador tenha um aspeto numa vista de texto.
- Anúncio de nascimento de Web: é uma notificação que realiza o utilizador tenha um olhar sobre uma vista da web.
Selecione o conteúdo de "Anúncio Web".

> Nota: Quando prime uma vista da web,-lo sempre vem com uma notificação pela primeira vez.

- Definir o conteúdo da web (depois de ter selecionado o conteúdo da web anúncio, irá aparecer a subsecção, permitindo-lhe definir o conteúdo da vista web que pretende que seja apresentado.)

 
![WebView4][31]

-    Escreva o título que será apresentado na parte superior da mensagem (opcional).
-    Escreva o código HTML aqui.
-    Clique na origem do botão para alternar edition e ver como aspeto do modo de edição.
-    Escreva o conteúdo que será apresentado no botão de ação (um botão de ação permite que a aplicação tornar uma acção específica como abrir uma página da aplicação, redirecionar para um arquivo ou de qualquer tipo de origens que pode fornecer).
-    Escrever o conteúdo que será apresentado no botão Sair (ao clicar no botão Sair, a vista da web desaparecerão).
 
-    Resultado
 
![WebView5][32]

-    O utilizador receber a notificação e clique na mesma.
-    A vista de texto é apresentado como um pop-up permitindo que o utilizador interagir com o mesmo.

<!--Image references-->
[1]: ./media/mobile-engagement-how-tos/First1.png
[2]: ./media/mobile-engagement-how-tos/First2.png
[3]: ./media/mobile-engagement-how-tos/First3.png
[4]: ./media/mobile-engagement-how-tos/First4.png
[5]: ./media/mobile-engagement-how-tos/First5.png
[6]: ./media/mobile-engagement-how-tos/First6.png
[7]: ./media/mobile-engagement-how-tos/First7.png
[8]: ./media/mobile-engagement-how-tos/Test1.png
[9]: ./media/mobile-engagement-how-tos/Test2.png
[10]: ./media/mobile-engagement-how-tos/Test3.png
[11]: ./media/mobile-engagement-how-tos/Personalize1.png
[12]: ./media/mobile-engagement-how-tos/Personalize2.png
[13]: ./media/mobile-engagement-how-tos/Personalize3.png
[14]: ./media/mobile-engagement-how-tos/Personalize4.png
[15]: ./media/mobile-engagement-how-tos/Differentiate1.png
[16]: ./media/mobile-engagement-how-tos/Differentiate2.png
[17]: ./media/mobile-engagement-how-tos/Differentiate3.png
[18]: ./media/mobile-engagement-how-tos/Schedule1.png
[19]: ./media/mobile-engagement-how-tos/Schedule2.png
[20]: ./media/mobile-engagement-how-tos/Schedule3.png
[21]: ./media/mobile-engagement-how-tos/TextView1.png
[22]: ./media/mobile-engagement-how-tos/TextView2.png
[23]: ./media/mobile-engagement-how-tos/TextView3.png
[24]: ./media/mobile-engagement-how-tos/TextView4.png
[25]: ./media/mobile-engagement-how-tos/TextView5.png
[26]: ./media/mobile-engagement-how-tos/TextView6.png
[27]: ./media/mobile-engagement-how-tos/TextView7.png
[28]: ./media/mobile-engagement-how-tos/WebView1.png
[29]: ./media/mobile-engagement-how-tos/WebView2.png
[30]: ./media/mobile-engagement-how-tos/WebView3.png
[31]: ./media/mobile-engagement-how-tos/WebView4.png
[32]: ./media/mobile-engagement-how-tos/WebView5.png

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
 
