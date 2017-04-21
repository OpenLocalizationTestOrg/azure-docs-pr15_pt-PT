

Para registar a aplicação para as notificações push através do serviço de notificação de Push do Apple (APNS), tem de criar um novo certificado de push, ID da aplicação e aprovisionamento perfil para o projeto no portal de programador da Apple. O ID da aplicação irá conter as definições de configuração que permitem a sua aplicação enviar e receber notificações push. Estas definições irão incluir o certificado de notificações push necessário para autenticar com os serviços de notificação a Push da Apple (APNS) ao enviar e receber notificações push. Para mais informações sobre estes conceitos consulte a documentação oficial do [Serviço de notificações Push da Apple](http://go.microsoft.com/fwlink/p/?LinkId=272584) .


####<a name="generate-the-certificate-signing-request-file-for-the-push-certificate"></a>Gerar o ficheiro de certificado de assinatura pedir para o certificado de emissão

Estes passos orientá-lo através da criação do pedido de assinatura do certificado. Será utilizada para gerar um certificado push para ser utilizado com APNS.

1. No seu Mac, execute a ferramenta de acesso de porta-chaves. Pode ser aberto a partir da pasta **utilitários** ou a **outra** pasta no painel de iniciação.

2. Clique em **Acesso porta-chaves**, expanda o **Assistente de certificado**e, em seguida, clique em **Pedir um certificado de uma autoridade de certificação...**.

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

3. Selecione o seu **Endereço de E-Mail de utilizador** e o **Nome comum** , certifique-se de que **guardado no disco** está selecionada e, em seguida, clique em **continuar**. Deixe o campo **Endereço de correio eletrónico AC** em branco como não é necessária.

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-csr-info.png)

4. Escreva um nome para o ficheiro de certificado de assinatura pedir (CSR) em **Guardar como**, selecione a localização no **local onde**e, em seguida, clique em **Guardar**.

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Isto guarda o ficheiro CSR na localização seleccionada; a localização predefinida é no ambiente de trabalho. Lembre-se a localização escolhida para este ficheiro.


####<a name="register-your-app-for-push-notifications"></a>Registe-se a sua aplicação para notificações push

Criar um novo ID explícita de tipos de aplicação para a sua aplicação com o Apple e também configurá-lo para as notificações push.  

1. Navegue para o [iOS aprovisionar Portal](http://go.microsoft.com/fwlink/p/?LinkId=272456) no Apple Developer Center, inicie sessão com o seu ID Apple, clique em **identificadores**, em seguida, clique em **IDs de aplicação**e por fim, clique na **+** iniciar sessão registar uma nova aplicação.

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-ios-appids.png)

2. Atualize os campos seguintes três para a sua nova aplicação e, em seguida, clique em **continuar**:

    * **Nome**: escreva um nome descritivo para a sua aplicação no campo **nome** na secção **Descrição ID da aplicação** .

    * **Identificador do pacote**: na secção **Explícitas ID da aplicação** , introduza um **Identificador de pacote** no formulário de `<Organization Identifier>.<Product Name>` tal como mencionado no [Guia de distribuição de aplicação](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW8). Isto tem de corresponder que também é utilizado no projeto XCode, Xamarin ou Cordova para a sua aplicação.

    * **Notificações de emissão**: selecione a opção de **Notificações de emissão** na secção **Serviços de aplicação** ,.

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

3.  Na página Confirmar ecrã ID da aplicação, reveja a definição e depois de ter verificado-los clique em **Submeter**

4.  Assim que tiver submetido o novo ID da aplicação, irá ver o ecrã de **registo concluído** . Clique em **concluído**.

5. No centro do programador, em IDs de aplicação, localize o ID da aplicação que acabou de criar e clique na sua linha. Clicar na linha ID aplicação irá apresentar os detalhes da aplicação. Clique no botão **Editar** na parte inferior.

6. Desloque-se para a parte inferior do ecrã e clique no botão **Criar certificado …** na secção **Desenvolvimento de emissão um certificado SSL**.

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    Esta ação irá apresentar o Assistente de "Adicionar iOS certificado".

    > [AZURE.NOTE] Neste tutorial, utiliza um certificado de desenvolvimento. O mesmo processo é utilizado quando registar um certificado de produção. Certifique-se de que utilize o mesmo tipo de certificado quando enviar notificações.

7. Clique em **Selecionar ficheiro**, procure a localização onde o guardou o representante de suporte para o certificado de push. Em seguida, clique em **Gerar**.

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

8. Depois do certificado é criado pelo portal, clique no botão **Transferir** .

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    Isto transfere o certificado de assinatura e guarda-lo para o seu computador na sua pasta de transferências.

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [AZURE.NOTE] Por predefinição, o ficheiro transferido um certificado de desenvolvimento é denominado **aps_development.cer**.

9. Faça duplo clique sobre a de certificados push transferido **aps_development.cer**. Este procedimento instala o novo certificado no Keychain do, conforme apresentado abaixo:

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [AZURE.NOTE] O nome do seu certificado poderão ser diferente, mas vai ser prefixo com **desenvolvimento Apple iOS de emissão dos serviços:**.

10. No Access porta-chaves, com o botão direito o novo certificado push que acabou de criar na categoria de **certificados** . Clique em **Exportar**, o nome do ficheiro, selecione o formato **. p12** e, em seguida, clique em **Guardar**.

    Lembre-se de que o nome do ficheiro e a localização do certificado de push. p12 exportado. Será utilizada para activar a autenticação com APNS ao carregá-lo no Portal clássica do Azure.



####<a name="create-a-provisioning-profile-for-the-app"></a>Criar um perfil de aprovisionamento para a aplicação

1. Novamente na <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS aprovisionar Portal</a>, selecione **Os perfis de aprovisionamento**, selecionar **Tudo**e, em seguida, clique na **+** botão para criar um novo perfil. Isto inicia o assistente **Adicionar iOS Provisiong perfil**

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

2. Selecione **iOS programação da aplicações** em **desenvolvimento** como o tipo de perfil provisiong e clique em **continuar**.


3. Em seguida, selecione o ID da aplicação que acabou de criar a partir da lista pendente de **ID da aplicação** e clique em **continuar**

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)


4. No ecrã de **Selecionar certificados** , selecione o certificado de desenvolvimento utilizado para assinar código e clique em **continuar**. Este é um certificado de assinatura, não o certificado de push que acabou de criar.

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)


5. Em seguida, selecione os **dispositivos** a utilizar para testar a ligação e clique em **continuar**

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)


6. Por fim, selecione um nome para o perfil em **Nome do perfil**, clique em **Gerar**.

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)
