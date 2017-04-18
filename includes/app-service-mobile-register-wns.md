
1. No Explorador de solução do Visual Studio, com o botão direito do projeto de aplicação da loja Windows, clique em **loja** > **Associar aplicação com o arquivo de...**.

    ![Associar a aplicação da loja Windows](./media/app-service-mobile-register-wns/notification-hub-associate-win8-app.png)

2. No assistente, clique em **seguinte**, inicie sessão com a sua conta Microsoft, escreva um nome para a sua aplicação na **reserva um novo nome de aplicação**, em seguida, clique em **reserva**.

3. Depois do registo de aplicação é criado com êxito, selecione o novo nome de aplicação, clique em **seguinte**e, em seguida, clique em **associar**. Esta ação adiciona as informações de registo de loja Windows necessárias manifesto da aplicação.

7. Repita os passos 1 e 3 para o projeto de aplicação da loja Windows Phone com o mesmo registo que criou anteriormente para a aplicação da loja Windows.  

7. Navegue para o [Windows Dev Center do](https://dev.windows.com/en-us/overview), iniciar sessão com a sua conta Microsoft, clique em novo registo de aplicação nas **minhas aplicações**, em seguida, expanda **Serviços** > **notificações de emissão**.

8. Na página de **notificações de emissão** , clique em **site de serviços Live** em **Windows emissão notificação serviços (WNS) e aplicações do Microsoft Azure Mobile**e tome nota os valores do **Pacote SID** e o valor *atual* na **Aplicação secreta**. 

    ![Definição de aplicação no Centro de programador](./media/app-service-mobile-register-wns/mobile-services-win8-app-push-auth.png)

    > [AZURE.IMPORTANT] O pacote de SID e secreta aplicação são as credenciais de segurança importantes. Não partilhar estes valores com qualquer pessoa ou distribuí-los com a sua aplicação.
