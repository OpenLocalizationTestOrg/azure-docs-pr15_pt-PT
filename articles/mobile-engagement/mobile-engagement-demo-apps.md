<properties
    pageTitle="Aplicação de demonstração Mobile Cativação Azure | Microsoft Azure"
    description="Descreve onde pretende transferir, como utilizar e os benefícios da utilização da aplicação de demonstração Azure Mobile Cativação"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/10/2016"
    ms.author="piyushjo" />

# <a name="azure-mobile-engagement-demo-app"></a>Aplicação de demonstração Mobile Cativação Azure

Vamos já publicadas uma aplicação de demonstração Azure Mobile Cativação para **iOS**, **Android**e **Windows** plataformas para ajudá-lo a localizar recursos úteis e saiba mais sobre Cativação Mobile.

A aplicação é útil para:

- Encontre facilmente útil ligações para recursos de Cativação Mobile como vídeos, documentação, o Fórum de suporte e onde ir para elevar pedidos de funcionalidades.
- Experiência exemplo das notificações que são suportadas pelo Mobile Cativação para obter ideias para o seus próprio aplicações móveis.
- Utilize uma implementação de referência para estudar como implementar o Mobile Cativação para sua própria aplicação. Pode obter para:

    - Recolha dados de análise.
    - Implemente cenários de notificação avançadas dos tipos de como *intersticial de ecrã inteiro* ou *pop-up*.
    - Implementar inquéritos e consultas.
    - Implemente cenários de dados e push push silenciosa.   

## <a name="app-installation"></a>Instalação da aplicação
Esta aplicação está disponível nos seguintes arquivos de aplicação:

- **Aplicação de demonstração Universal do Windows**:

    - Transfira a aplicação na [aplicação Windows armazenar](https://www.microsoft.com/en-us/store/apps/azure-mobile-engagement/9nblggh4qmh2).
    - A aplicação foi desenvolvida como uma aplicação do Windows 10 Universal. O código de origem está disponível no [GitHub](https://github.com/Azure/azure-mobile-engagement-app-windows).

- **iOS demonstração aplicação**:

    - Transfira a aplicação na [Apple armazenar](https://itunes.apple.com/us/app/azure%20mobile%20engagement/id1105090090).
    - A aplicação foi desenvolvida em iOS Swift. O código de origem está disponível no [GitHub](https://github.com/Azure/azure-mobile-engagement-app-ios).

- **Aplicação de demonstração android**:

    - Transfira a aplicação na [Google Play armazenar](https://play.google.com/store/apps/details?id=com.microsoft.azure.engagement).
    - O código de origem está disponível no [GitHub](https://github.com/Azure/azure-mobile-engagement-app-android).

![Aplicação de demonstração Universal do Windows][1]

![iOS demonstração aplicação][2]
![aplicação demonstração Android][3]


## <a name="usage"></a>Utilização

Pode utilizar esta aplicação das seguintes formas:

**Transfira a aplicação no seu dispositivo a partir da aplicação armazenar ligações (fornecidas anteriormente):**

>[AZURE.IMPORTANT] Não precisa de uma conta Azure ou necessidade para ligar a aplicação a um back-end. A aplicação funciona de forma independente.

- Depois de ter a aplicação no seu dispositivo, em seguida, pode aceder nas ligações no menu do lado esquerdo para encontrar os recursos úteis sobre Cativação Mobile.
- Foram adicionadas o [feed RSS do serviço](https://aka.ms/azmerssfeed) para esta aplicação para que sempre atualizadas sobre as atualizações mais recentes do produto.
- Também pode percorrer os cenários de notificação de exemplo para uma experiência de tipo de notificações que são suportadas pelo Mobile Cativação para cada plataforma. Estas notificações podem ser ocorreu ao localmente – que está, pode clicar em botões em ecrãs para confirmar que a experiência de notificações, que é idêntica ao envio de notificações a partir da plataforma de Cativação Mobile.

![Menu da aplicação para Windows][4]

![Menu da aplicação para iOS][5]
![menu da aplicação para Android][6]

**Transfira o código de origem a partir das hiperligações de GitHub (fornecidas anteriormente):**

- Depois de transferir o código de origem, abra-o no ambiente de desenvolvimento respetivos – XCode para iOS, Android Studio para Android e o Visual Studio para Windows.
- Deve seguir seguinte a nossa [passos básicos de integração de SDK](mobile-engagement-windows-store-dotnet-get-started.md) para que não conseguir ligar esta aplicação a sua própria instância de back-end Cativação Mobile.
    - Tem de configurar uma cadeia de ligação na aplicação.
    - Também precisa de configurar a plataforma de notificações push para a sua aplicação.
- Notará que a própria aplicação é implementada com Cativação Mobile. Por conseguinte, tal como, abra a aplicação após ligá-lo para o back-end, poderá ver a sessão do utilizador, atividades, eventos e assim sucessivamente, no separador **Monitor** .
- Também poderá enviar notificações para esta aplicação a partir do seu próprio instância de Cativação Mobile, em vez de utilizar notificações locais.
    - Aqui pode adicionar o seu dispositivo como um dispositivo de teste utilizando o item de menu **obter o ID do dispositivo** na aplicação. Isto dá-lhe um ID de dispositivo, em seguida, registar o como um dispositivo de teste com a instância de back-end plataforma.

    ![ID do dispositivo no Windows][7]

    ![ID do dispositivo IOS][8]
    ![ID do dispositivo Android][9]

## <a name="key-features-of-the-demo-app"></a>Principais funcionalidades da aplicação de demonstração

- Como mencionado anteriormente, com esta aplicação, tem todos os recursos chaves para Mobile Cativação na sua mão. Pode aceder nas ligações no menu à esquerda.

- Que se pode deparar notificações fora da aplicação para cada plataforma. Estas notificações podem ser entregue como **notificação apenas**, onde clicando em notificação simplesmente abre-se para cima um ecrã nativo da aplicação (utilizando **a ligação abrangente**) – ou como um **anúncio da Web**, onde pode entregar conteúdo HTML adicional a partir do Mobile Cativação back-end para ser apresentado quando a notificação é clicada.

    ![Notificações de fora da aplicação][29]

- IOS, tem de fechar a aplicação para ver as notificações push fora da aplicação ou sistema. Pode observe a aplicação para adicionar **botões de ação**, como aqueles que são adicionadas a esta notificação de fora da aplicação para o *comentário* e *partilhar* (para que o utilizador pode demorar direita de ação de notificação de si).

    ![Notificações de fora da aplicação IOS][11] ![Visualização de notificação de fora da aplicação no iOS][14]

- No Android, as opções que são suportadas estão a adicionar texto com várias linhas (**Texto grande**) ou uma imagem de notificação (**Conceito**) à notificação, juntamente com os **botões de ação** (tal como suportados pelo iOS).

    ![Notificações de fora da aplicação no Android][12] ![Visualização de notificação de fora da aplicação no Android][15]

- No Windows 10, pode ver o aspeto das notificações no PC. Esta notificação também mostra no **Centro de notificação**de do Windows 10. Não existe suporte para adicionar **botões de ação** no momento no Windows SDK.

    ![Notificações de fora da aplicação no Windows][10] ![Visualização de fora da aplicação no Windows][13]

- Que se pode deparar notificações de "na aplicação" predefinido para cada plataforma. Esta é uma experiência de dois passos onde **notificação** é apresentada uma janela pela primeira vez. Ao clicar no mesmo, abre-se para cima um ecrã inteiro **anúncio**, tal como apresentado na captura de ecrã seguinte. O conteúdo deste anúncio provém a instância de back-end Cativação Mobile. O SDK tem os modelos para as notificações e anúncios. Pode personalizar facilmente-las, conforme apresentado nesta aplicação demonstração com a adição da nossa logótipo e coloração.  

    ![Notificações de na aplicação no Windows][16]

    ![Notificações de na aplicação IOS][17]  ![Notificações de na aplicação no Android][18]

    **iOS**, **Android**

- Também pode utilizar a funcionalidade de **categoria** de Mobile Cativação para personalizar esta experiência predefinido. Na aplicação demonstração, podemos tenha demonstrado duas formas comuns para alterar a experiência das notificações. Tenha em atenção que a funcionalidade de categoria ainda não é suportada no Windows SDK.

    **Ecrã inteiro intersticial:**

    ![Notificação na aplicação - intersticial categoria][30]

    ![Categoria intersticial IOS][21]  ![Categoria intersticial no Android][22]

    **Notificação de pop-up:**

    ![Notificação na aplicação - categoria de pop-up][31]

    ![Notificação de pop-up IOS][19]   ![Notificação de pop-up no Android][20]

**iOS**, **Android**

- Mobile Cativação também suporta a um tipo de especializadas da notificação de na aplicação denominado **inquéritos**. Esta opção permite-lhe envie a inquéritos rápidos aos seus utilizadores da aplicação segmentado. Pode adicionar perguntas e opções para cada pergunta tal como a captura de ecrã seguinte. Em seguida, irá obter apresentado como uma notificação de na aplicação para o utilizador de aplicação.   

    ![Notificações de inquérito][32]

    ![Inquérito no Windows][26]

    ![Inquérito no iOS][27]   ![Inquérito no Android][28]

**iOS**, **Android**

- Mobile Cativação também suporta o envio de notificações **De emissão dados** silenciosos. Com estas notificações, pode enviar dados a partir do seu serviço (como dados JSON no seguinte exemplo), que pode ser lidar na sua aplicação de efetuar alguns ação. Um exemplo é como podemos está a alterar o preço de um item seletivamente utilizando as notificações push dados.

    ![Notificação de emissão de dados][33]

    ![Notificação de emissão de dados no Windows][23]

    ![Notificação de emissão de dados no iOS][24]  ![Notificação de emissão de dados no Android][25]

**iOS**, **Android**

> [AZURE.NOTE] Pode ver detalhadas instruções passo a passo para qualquer uma destas notificações ao clicar em **clique aqui para obter instruções sobre como enviar estas notificações de plataforma Mobile Cativação** em qualquer ecrã de notificação de exemplo.


[1]: ./media/mobile-engagement-demo-apps/home-windows.png
[2]: ./media/mobile-engagement-demo-apps/home-ios.png
[3]: ./media/mobile-engagement-demo-apps/home-android.png
[4]: ./media/mobile-engagement-demo-apps/menu-windows.png
[5]: ./media/mobile-engagement-demo-apps/menu-ios.png
[6]: ./media/mobile-engagement-demo-apps/menu-android.png
[7]: ./media/mobile-engagement-demo-apps/device-id-windows.png
[8]: ./media/mobile-engagement-demo-apps/device-id-ios.png
[9]: ./media/mobile-engagement-demo-apps/device-id-android.png
[10]: ./media/mobile-engagement-demo-apps/out-of-app-windows.png
[11]: ./media/mobile-engagement-demo-apps/out-of-app-ios.png
[12]: ./media/mobile-engagement-demo-apps/out-of-app-android.png
[13]: ./media/mobile-engagement-demo-apps/out-of-app-display-windows.png
[14]: ./media/mobile-engagement-demo-apps/out-of-app-display-ios.png
[15]: ./media/mobile-engagement-demo-apps/out-of-app-display-android.png
[16]: ./media/mobile-engagement-demo-apps/in-app-windows.png
[17]: ./media/mobile-engagement-demo-apps/in-app-ios.png
[18]: ./media/mobile-engagement-demo-apps/in-app-android.png
[19]: ./media/mobile-engagement-demo-apps/pop-up-ios.png
[20]: ./media/mobile-engagement-demo-apps/pop-up-android.png
[21]: ./media/mobile-engagement-demo-apps/interstitial-ios.png
[22]: ./media/mobile-engagement-demo-apps/interstitial-android.png
[23]: ./media/mobile-engagement-demo-apps/data-push-windows.png
[24]: ./media/mobile-engagement-demo-apps/data-push-ios.png
[25]: ./media/mobile-engagement-demo-apps/data-push-android.png
[26]: ./media/mobile-engagement-demo-apps/survey-windows.png
[27]: ./media/mobile-engagement-demo-apps/survey-ios.png
[28]: ./media/mobile-engagement-demo-apps/survey-android.png
[29]: ./media/mobile-engagement-demo-apps/out-of-app.png
[30]: ./media/mobile-engagement-demo-apps/in-app-interstitial.png
[31]: ./media/mobile-engagement-demo-apps/in-app-pop-up.png
[32]: ./media/mobile-engagement-demo-apps/notification-poll.png
[33]: ./media/mobile-engagement-demo-apps/notification-data-push.png
