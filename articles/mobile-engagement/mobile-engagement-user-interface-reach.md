<properties 
   pageTitle="Interface de utilizador do Azure Cativação móvel - alcance" 
   description="Saiba como comunique para os utilizadores da sua aplicação com as notificações push utilizando Azure Mobile Cativação" 
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


# <a name="how-to-reach-out-to-the-users-of-your-application-with-push-notifications"></a>Como comunique para os utilizadores da sua aplicação com as notificações push

Este artigo descreve o separador **atinja** do portal de **Cativação Mobile** . Utilizar o portal de **Mobile Cativação** para monitorizar e gerir as suas aplicações móveis. Tenha em atenção que para começar a utilizar o portal do primeiro tem de criar uma conta **Azure Mobile Cativação** . Para mais informações, consulte o artigo [criar uma conta Azure Mobile Cativação](mobile-engagement-create.md).

A secção alcance da IU é a ferramenta de gestão de campanha Push onde pode criar/editar/ativar/concluir/monitor e obter estatísticas sobre campanhas de notificações Push e funcionalidades que também podem ser acedidas através da API atinja (e alguns elementos do nível de baixo emissão API). Lembre-se de que, se estiver a utilizar as API ou a IU, terá de integrar o Azure Mobile Cativação e alcance para a sua aplicação de cada plataforma com o SDK antes de poder utilizar atinja campanhas.

>[AZURE.NOTE] Muitas secções do portal de **Cativação Mobile** IU contêm no botão **Mostrar ajuda** . Prima neste botão para obter mais informações contextuais sobre uma secção.

## <a name="four-types-of-push-notifications"></a>Quatro tipos de notificações Push
1.    Anúncios - permitem-lhe para enviar mensagens de publicidade para utilizadores que reencaminhar para outra localização no interior da sua aplicação ou enviá-los a uma página Web ou um arquivo de fora da sua aplicação. 
2.    Inquéritos - permitem-lhe reunir informações a partir de utilizadores finais ao solicitando-lhes perguntas.
3.    Dados puxa - permitem-lhe enviar um ficheiro de dados binários ou base64. As informações contidas num push de dados são enviadas para a aplicação para modificar a experiência atual dos seus utilizadores na sua aplicação. A aplicação tem de ser possível processar os dados de um push de dados.

## <a name="campaign-details"></a>Detalhes da campanha

Pode editar, clonar, eliminar ou ativar campanhas não tem sido ativadas ainda ao pairar o rato sobre os seus nomes ou pode clicar para abri-los. Pode clonar campanhas que já tem sido ativadas pelo pairar o rato sobre os seus nomes ou pode clicar para abri-los. No entanto, não é possível alterar uma campanha de assim que foi ativado.
 
![Reach1][18]

## <a name="reach-feedback"></a>Chegar comentários

Clique em **Estatísticas** para ver os detalhes de uma campanha de atingir. A vista **simples** fornece uma representação visual sob a forma de um gráfico de barras de coluna sobre o que aconteceu depois de uma campanha de foi ativada. A vista **Avançadas** fornece mais granulares detalhes acerca da campanha push. Estes detalhes não estará disponíveis se estiver a enviar uma campanha de teste, ou seja, um push enviado para um dispositivo de teste. Eis como deve interpretar-se destes detalhes:

1. **Pushed** - Isto especifica o número de mensagens eliminadas para os dispositivos. Este número será variam consoante o público-alvo especificada durante a criação da campanha push. Se não especificar quaisquer público-alvo, em seguida, este push será enviada para todos os dispositivos registados. Como todos os outros serviços de push, podemos as notificações de emissão não diretamente para os dispositivos, mas em vez disso, emissão-las para a plataforma respetivas serviços de notificação do específicos Push (PNS - GCM/APNS/WNS) para que pode fazer as notificações aos dispositivos. 

2.  **Entregue** - Isto especifica o número de mensagens que são com êxito emitido por PNS para o dispositivo e que confirmado como recebidos por Mobile Cativação SDK. 
        
    *Motivos para entregue contam a ser menor do que contar premido:*
    
    1. Se o utilizador desinstalou a aplicação a partir do dispositivo, mas o PNS não sabe sobre-lo ao tempo de enviarmos o push para o PNS a mensagem será eliminada.
    2. Se o dispositivo tem a aplicação, mas os dispositivos próprios foram offline para longos períodos de tempo, o PNS falhará entregar a mensagem para o dispositivo. 
    3. Se obter entregar a mensagem para o dispositivo, mas o SDK de Cativação Mobile na aplicação não reconhece o conteúdo da mensagem, em seguida, interrompe essa mensagem. Isto pode acontecer se a personalização da notificação de na aplicação gera uma exceção que recomendamos capturas SDK e largue a mensagem. Isto também pode ocorrer se a aplicação no dispositivo está a utilizar uma versão do SDK Cativação Mobile que não é possível compreender a versão mais recente da mensagem push enviada a partir da plataforma, mas este é apenas quando a aplicação foi atualizada depois da notificação foi distribuída a partir da plataforma de serviço. No separador **Avançadas** irá indicar quantas mensagens foram interrompidas. 
    4. Em dispositivos iOS, as mensagens, por vezes, não obter entregues se quer o dispositivo está ligado baterias baixa ou se a aplicação está a consumir significativa quantidade de energia quando está a processar notificações remotas. Esta é uma limitação dos dispositivos iOS.   

3.  **Apresentado** - Isto especifica o número de mensagens que são apresentadas com êxito ao utilizador aplicação no dispositivo sob a forma de uma notificação de push/fora-de-app sistema no Centro de notificação ou uma notificação de na aplicação da aplicação móvel.  No separador **Avançadas** irá indicar quantos foram as notificações do sistema e quantas foram notificações na aplicação. 
    
    *Motivos para apresentado contam a ser menor do que entregue contar (em espera para ser apresentado)*
    
    1. Se a campanha de notificação tinha uma data de fim no mesmo, em seguida, é possível que a notificação foi entregue, mas quando o tempo veio para abrir e apresentá-la para o utilizador de aplicação, esta foi já expirado para que nunca foi apresentado.   
    2. Se a notificação é uma notificação de na aplicação, em seguida, a notificação só é apresentada quando o utilizador app abre a aplicação. Em casos onde o utilizador de aplicação não abriu a aplicação, o SDK apresentará um relatório que a notificação foi entregue, mas ainda não apresentada até que a aplicação é aberta. 
    2. Se a notificação é uma notificação de na aplicação e configurado ser apresentado num ecrã/atividade específico, em seguida, também a notificação vai ser comunicada como entregue, mas ainda não foi entregue até o utilizador abre-se a aplicação num ecrã específico. 
    
4.  **Interações do utilizador** - esta chave especifica o número de mensagens que o utilizador aplicação tem seus serviços contactou com e irá incluir as mensagens que são actioned ou saído. 

    - *O utilizador aplicação pode ação uma notificação de qualquer uma das seguintes formas:*
            
        1. Se estiver a notificação de uma notificação de sistema/fora-de-app ou uma notificação de na aplicação enviados como só de notificação, em seguida, o utilizador de aplicação clica na notificação.
        2. Se a notificação é uma notificação de na aplicação com um texto ou a vista da web ou inquéritos, em seguida, o utilizador de aplicação clica no botão de ação na notificação.
        3. Se a notificação é uma notificação de na aplicação com uma vista da web, em seguida, o aplicação utilizador clica num URL de web apenas na vista de [Android]
    
    - *O utilizador aplicação pode sair uma notificação de qualquer uma das seguintes formas:*
    
        1. Clicar no botão fechar a notificação de diretamente. 
        2. Percorrer ausente ou eliminar a notificação. 
        3. Notificações de na aplicação com o conteúdo de texto/web e consultas normalmente são apresentadas para o utilizador de aplicação no processo de dois passos. Ver uma notificação pela primeira vez e quando clicarem no mesmo, ver o conteúdo da web/texto/inquérito subsequente. O utilizador aplicação pode sair uma notificação num dos seguintes passos e os detalhes na vista avançada captura isto. 

5.  **Actioned** - Isto especifica o número de mensagens que foram explicitamente actioned pelo utilizador aplicação. Este é o número mais interessante como isto indica que o número de utilizadores aplicação foram interessado pela mensagem que seguia na notificação. 
 
> [AZURE.NOTE] No iOS & Windows abrir plataformas, se o utilizador tem a aplicação e campanha foi uma campanha "Sempre", em seguida, é possível que ambos fora da aplicação e notificações de na aplicação são apresentados ao mesmo tempo. Isto pode causar uma contagem de apresentado mais elevada que os entregue. Se o utilizador interage ou ações da notificação, em seguida, mesmo a contagem de utilizador interações/Actioned pode ser maior do que entregue. 


![Reach2][19]

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
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md
 
