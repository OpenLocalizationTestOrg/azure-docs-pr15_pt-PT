

1. No seu Mac, inicie o **Access de porta-chaves**. Abra **Os meus certificados** em **categoria** na barra de navigationn para a esquerda. Localize o certificado SSL que transferiu na secção anterior e divulgar os seus conteúdos. Selecione apenas o certificado (não selecione a chave privada) e [exportá-lo](https://support.apple.com/kb/PH20122?locale=en_US).

2. No [portal do Azure](https://portal.azure.com/), clique em **Procurar tudo** > **Serviços de aplicação** > seu back-end aplicação Mobile. Em **Definições**, clique na **Aplicação de serviço emissão**, em seguida, clique no seu nome de concentrador de notificação. Aceda a **Serviços de notificação de notificações Push da Apple** > **Carregar certificado**. Carregue o ficheiro. p12, selecionar o **modo** de correto (dependendo se o cliente SSL certificado relativamente ao anterior é produção ou Sandbox). Guarde as alterações.

O serviço está agora configurado para trabalhar com as notificações push IOS!

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png
