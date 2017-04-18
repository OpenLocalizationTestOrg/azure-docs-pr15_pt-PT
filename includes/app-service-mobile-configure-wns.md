
1. No [portal do Azure](https://portal.azure.com/), clique em **Procurar tudo** > **Serviços de aplicação** > seu back-end aplicação Mobile. Em **Definições**, clique na **Aplicação de serviço emissão**, em seguida, clique no seu nome de concentrador de notificação.

2. Ir para o **Windows (WNS)**, introduza a **chave de segurança** (secreta de cliente) e o **Pacote SID** obtidos a partir do site Live Services e, em seguida, clique em **Guardar**.

    ![Definir a chave WNS no portal](./media/app-service-mobile-configure-wns/mobile-push-wns-credentials.png)

O back-end agora está configurado para utilizar WNS para enviar notificações push.
