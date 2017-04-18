<properties 
   pageTitle="Interface de utilizador do Azure Cativação móvel - alcance conteúdo" 
   description="Saiba como gerir o conteúdo exclusivo dos diferentes tipos de campanhas de notificações push no Azure Mobile Cativação" 
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

# <a name="how-to-manage-the-unique-content-of-the-different-types-of-push-notification-campaigns"></a>Como gerir o conteúdo exclusivo dos diferentes tipos de campanhas de notificações push
 
Pode utilizar a secção Conteúda de uma nova campanha de atingir para modificar o conteúdo do seu anúncios, inquéritos, dados emite e mosaicos (apenas para o Windows Phone). A definição de conteúdo de campanhas de Push é específica para o tipo de campanha. 
 
### <a name="content-types"></a>Tipos de conteúdo:
- Anúncios
- Inquéritos
- Puxa de dados
- Mosaicos (apenas para o Windows Phone)
 
## <a name="content-of-announcements"></a>Conteúdo de anúncios
 ![Alcance Content1][30] 

### <a name="choose-the-type-of-your-announcement"></a>Selecione o tipo de seu anúncio:
-    Notificação apenas: é uma notificação padrão simple. O que significa que que se um utilizador clica no mesmo, irá aparecer sem vista adicional, mas irá ocorrer apenas a ação associada à mesma.
-    Anúncio de nascimento de texto: é uma notificação que realiza o utilizador tenha um aspeto numa vista de texto.
-    Anúncio de nascimento de Web: é uma notificação que realiza o utilizador tenha um olhar sobre uma vista da web.

### <a name="see-also"></a>Consulte também
- [Chegar - como apoios - anúncios][Link 3] 

### <a name="about-web-view-announcements"></a>Sobre Web anúncios de vista:
Ocorrências do padrão "{deviceid}" no código HTML ou código JavaScript fornecidos aqui serão automaticamente substituídas pelo identificador de dispositivo apresentando o anúncio. Este é uma forma fácil de obter os identificadores de dispositivo Azure Mobile Cativação num serviço web externo alojado no seu escritório anterior.
Se pretender criar uma vista da web em ecrã inteiro (sem os botões predefinidos da ação e sair fornecemos) pode utilizar as seguintes funções de código do seu anúncio de vista de web JavaScript: 

-    executar a ação de anúncio: ReachContent.actionContent()
-    sair do anúncio de: ReachContent.exitContent()
 
### <a name="choose-your-action"></a>Escolha a sua ação:

### <a name="about-action-urls"></a>Sobre URLs de ação:
Qualquer URL que possa ser interpretada pelo sistema operativo de um dispositivo alvo pode ser utilizado como um URL de ação.
Qualquer dedicado URL que poderão suportar a sua aplicação (por exemplo, para que os utilizadores saltar para um ecrã específico) podem também ser utilizados como um URL de ação.
Cada ocorrência do padrão {deviceid} automaticamente é substituída pelo identificador de dispositivo efetuar a ação. Isto pode ser utilizado para obter facilmente os identificadores de dispositivo Azure Mobile Cativação através de um serviço web externo alojado no seu escritório anterior.

- **Android + iOS ações**
    - Abra uma página web
    - http://\[domínio do site do web\] 
    - Exemplo: http://www.azure.com
    - Enviar um e-mail
    - mailto:\[destinatário do correio electrónico\]?subject =\[assunto\]& corpo =\[mensagem\] 
    - Example:mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
    - Enviar um SMS
    - SMS:\[número de telefone\] 
    - Exemplo: sms:2125551212
    - Marcar um número de telefone
    - Tel:\[número de telefone\] 
    - Exemplo: tel:2125551212
- **Ações apenas Android**
    - Transferir uma aplicação no arquivo de reprodução
    - Market://details?id=\[pacote de aplicações\] 
    - Exemplo: market://details?id=com.microsoft.office.word
    - Iniciar uma pesquisa localizadas geo
    - Geo:0, 0?q =\[consulta de pesquisa\] 
    - Exemplo: geo:0, 0? q = starbucks, Faro
- **ações apenas iOS**
    - Transferir uma aplicação no arquivo de aplicação
    - http://iTunes.Apple.com/ [País] /app/ [nome da aplicação] /id [id da aplicação]? mt = 8 
    - Exemplo: http://itunes.apple.com/fr/app/briquet-virtuel/id430154748?mt=8
    - Ações do Windows
    - Abra uma página web
    - http://\[domínio do site do web\] 
    - Exemplo: http://www.azure.com
    - Enviar um e-mail
    - mailto:\[destinatário do correio electrónico\]?subject =\[assunto\]& corpo =\[mensagem\] 
    - Example:mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
    - Enviar um SMS (aplicação de arquivo de Skype necessário)
    - SMS:\[número de telefone\] 
    - Exemplo: sms:2125551212
    - Marcar um número de telefone (aplicação de arquivo de Skype necessário)
    - Tel:\[número de telefone\] 
    - Exemplo: tel:2125551212
    - Transferir uma aplicação no arquivo de reprodução
    - MS-windows-loja: PDP?PFN =\[ID do pacote de aplicação\] 
    - Exemplo: ms-windows-loja: PDP? PFN = 4d91298a-07cb-40fb-aecc-4cb5615d53c1
    - Iniciar uma pesquisa bingmaps
    - bingmaps:?q =\[consulta de pesquisa\] 
    - Exemplo: bingmaps:? q = starbucks, Faro
    - Utilizar um esquema personalizado
    - \[esquema personalizado\]://\[parâmetros de esquema personalizado\] 
    - Exemplo: myCustomProtocol://myCustomParams
    - Utilizar um pacote de dados (aplicação da loja para a extensão ler necessário)
    - \[pasta\]\[dados\]. \[extensão\] 
    - Example:myfolderdata.txt
 
### <a name="build-a-tracking-url"></a>Crie um URL de controlo:
-    Consulte a secção "Definições de" a <UI Documentation> para instruções sobre a criação de um URL de controlo que permitirá que os utilizadores transferir uma das suas outras aplicações.
 
### <a name="define-the-texts-of-your-announcement"></a>Definir os textos da sua anúncio
Preencha o título, o conteúdo e textos de botão da sua anúncio. Pode direccionar uma audiência de uma campanha de futura com base nos comentários alcance da forma como os utilizadores responderam a esta campanha. Segmentação de conteúdos pode ser com base nos comentários dos se esta campanha foi apenas seguia, respondida, actioned ou saído.

### <a name="see-also"></a>Consulte também
- [IU documentação - alcance - novo Push critério][Link 28]

## <a name="content-of-polls"></a>Conteúdo de consultas
![Alcance Content2][31] preencha o título, descrição e textos de botão da sua anúncio. Em seguida, adicione perguntas e opções para as respostas para as suas questões.
Pode direccionar uma audiência de uma campanha de futura com base nos comentários alcance da forma como os utilizadores responderam a esta campanha. Segmentação de conteúdos pode basear-se desta campanha foi apenas seguia, respondida, actioned ou saído. Segmentação de conteúdos pode também basear comentários de resposta do inquérito, onde a pergunta e resposta escolha são utilizados como critérios.

### <a name="see-also"></a>Consulte também
- [IU documentação - alcance - novo Push critério][Link 28]
 
## <a name="content-of-data-pushes"></a>Envia conteúdo de dados
![Alcance Content3][32] 

### <a name="choose-the-type-of-your-data"></a>Escolha o tipo de dados:
- Texto
- Dados binários
- Dados Base64

### <a name="define-the-content-of-your-data"></a>Definir o conteúdo dos seus dados
- Se tiver selecionado para transmitir dados de texto, copie e cole o texto na caixa "conteúdo".
- Se tiver selecionado para transmitir dados binários ou base64, utilize o botão "carregue o seu ficheiro" para carregar o ficheiro.
- Pode direccionar uma audiência de uma campanha de futura com base nos comentários alcance da forma como os utilizadores responderam a esta campanha. Segmentação de conteúdos pode basear-se desta campanha foi apenas seguia, respondida, actioned ou saído.

### <a name="see-also"></a>Consulte também
- [IU documentação - alcance - novo Push critério][Link 28]

## <a name="content-of-tiles-windows-phone-only"></a>Conteúdos dos mosaicos (apenas para o Windows Phone)
![Alcance Content4][33]

### <a name="define-the-content-of-your-tile"></a>Definir o conteúdo do seu mosaico
A carga mosaico útil é o texto para que seja apresentado no mosaico da sua aplicação em dispositivos Windows Phone.
Um mosaico push é a versão de serviço de notificações Push (MPNS) da Microsoft de um nativa push para Windows Phone. O tipo de push mosaico é o único tipo push que não tem uma resposta e por isso, a audiência de futuras campanhas não pode ser criada nos resultados de uma campanha de push mosaico. 

### <a name="see-also"></a>Consulte também
- [API documentação - alcance API - nativo Push][Link 4]

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
 
