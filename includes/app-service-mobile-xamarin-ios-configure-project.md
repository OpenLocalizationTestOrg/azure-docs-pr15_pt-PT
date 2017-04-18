####<a name="configuring-the-ios-project-in-xamarin-studio"></a>Configurar o projecto iOS no Xamarin Studio

1. No Xamarin.Studio, abrir **Info.plist**e atualizar o **Identificador de pacote** com o ID de pacote que criou anteriormente com o seu ID de aplicação nova.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-21.png)

2. Desloque para baixo até **Modos de fundo** e verifique a caixa **Ativar modos de fundo** e a caixa de **notificações remotas** . 

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-22.png)

3. Faça duplo clique em projeto no painel de solução para abrir as **Opções de projeto**.

4.  Selecione **iOS pacote iniciar sessão** em **Criar**e selecione a correspondente **identidade** e a **aprovisionar perfil** que acabou de configurar para este projeto. 

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-20.png)

    Este procedimento assegura que o project utiliza o novo perfil para assinar código. Para o dispositivo Xamarin oficial documentação de aprovisionamento, consulte o artigo [Xamarin aprovisionamento de dispositivo].

####<a name="configuring-the-ios-project-in-visual-studio"></a>Configurar o projecto iOS no Visual Studio

1. No Visual Studio, com o botão direito do projeto e, em seguida, clique em **Propriedades**.

2. Nas páginas de propriedades, clique no separador da **aplicação do iOS** e atualizar o **identificador** com o ID que criou anteriormente.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-23.png)

3. No separador **iOS pacote iniciar sessão** , selecione a correspondente **identidade** e **aprovisionar perfil** que acabou de configurar para este projeto. 

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-24.png)

    Este procedimento assegura que o project utiliza o novo perfil para assinar código. Para o dispositivo Xamarin oficial documentação de aprovisionamento, consulte o artigo [Xamarin aprovisionamento de dispositivo].

4. Faça duplo clique Info.plist para abri-lo e, em seguida, ativar **RemoteNotifications** em modos de fundo. 



[Dispositivo Xamarin de aprovisionamento]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/