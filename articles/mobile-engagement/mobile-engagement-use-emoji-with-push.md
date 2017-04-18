<properties 
    pageTitle="Utilizar Emoji ícones expressivos dentro Azure Mobile Cativação" 
    description="Como utilizar os ícones expressivos de Emoji dentro as notificações push"     
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-phone" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="use-emoji-emoticon-within-push-notifications"></a>Utilizar o ícone expressivo de Emoji dentro de notificações de emissão

Pode incluir Emoji ícones expressivos nas emissão notificação em alguns passos simples: 

1. Em primeiro tudo o que precisa para localizar o Emoji que pretende enviar a mensagem. Certifique-se de que Emoji estiver a selecionar será suportado pelo dispositivo de destino, tal como fabricantes de dispositivo demoram algum tempo para adicionar Emojis recentemente aprovado para as plataformas de dispositivos. 

2. No **Windows** - pode navegar para esta [ligação](http://apps.timwhitlock.info/emoji/tables/unicode) e copiar o ícone 'Nativo'.

    ![][7] 

3. No **Mac** - pode encontrar que emojis na aplicação de dicionário em Editar -> Emoji & símbolos.

    ![][6] 

4. Agora, aceda ao separador **atinja** no portal do Azure Mobile Cativação. Selecione o tipo da sua notificação de push (anúncio, pesquisa etc). Para este exemplo optamos push um anúncio.

5. Especifique os campos diferentes da notificação de até chegar ao texto na notificação. Este é onde irá adicionar o ícone expressivo Emoji. Pode escolher a colocá-lo no título, a mensagem ou ambos. Terá de arrastar e largar ou copiar a Emoji que pode encontrar a partir de localizações acima. 

    ![][1]

6. Preencha os campos para a notificação e guardá-lo. 

7. Quando executa um teste ou ativar o anúncio irá ver uma notificação com o ícone expressivo conforme especificado.   

    ![][3] ![][4] ![][5]

<!-- Images. -->
[1]: ./media/mobile-engagement-use-emoji-with-push/notification_input.png
[3]: ./media/mobile-engagement-use-emoji-with-push/iOS_Emoji.png
[4]: ./media/mobile-engagement-use-emoji-with-push/Android_Emoji.png
[5]: ./media/mobile-engagement-use-emoji-with-push/WindowsPhone_Emoji.png
[6]: ./media/mobile-engagement-use-emoji-with-push/Mac_SelectEmoji.png
[7]: ./media/mobile-engagement-use-emoji-with-push/Windows_SelectEmoji.png

