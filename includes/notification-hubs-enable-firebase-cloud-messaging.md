

1. Inicie sessão na [consola Firebase](https://firebase.google.com/console/). Crie um novo projeto de Firebase se ainda não tiver uma.
2. Depois do projeto é criado, clique em **Adicionar Firebase para a sua aplicação Android** e siga as instruções fornecidas.

    ![](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)

3. Na consola do Firebase, clique na roda dentada para o seu projeto e, em seguida, clique em **Definições do Project**.

    ![](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)

4. Clique no separador **Nuvem mensagens** nas definições do project e copie o valor da **chave do servidor** e **O ID do remetente**.  Mais tarde, estes valores serão utilizadas para configurar a política de acesso do concentrador de notificação e o processador de notificação na aplicação.
  