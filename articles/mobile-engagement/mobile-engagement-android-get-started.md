<properties
    pageTitle="Introdução ao Android aplicações o Azure Mobile Cativação"
    description="Saiba como utilizar o Azure Mobile Cativação com as notificações push e de análise para as aplicações Android."
    services="mobile-engagement"
    documentationCenter="android"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="hero-article"
    ms.date="08/10/2016"
    ms.author="piyushjo;ricksal" />

# <a name="get-started-with-azure-mobile-engagement-for-android-apps"></a>Introdução ao Azure Mobile Cativação para aplicações do Android

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tópico mostra-lhe como utilizar o Azure Mobile Cativação para compreender a utilização da aplicação e como enviar notificações push para segmentado utilizadores de uma aplicação do Android.
Neste tutorial demonstra o cenário de difusão simple utilizando Cativação Mobile. -Lo, pode criar uma aplicação de Android em branco que recolhe dados básico e recebe as notificações push utilizando Google Cloud mensagens (GCM).

## <a name="prerequisites"></a>Pré-requisitos

Concluir este tutorial requer as [Ferramentas de programador Android](https://developer.android.com/sdk/index.html), que inclui o ambiente de desenvolvimento integrado Android Studio e a plataforma Android mais recente.

Também requer o [Mobile Cativação Android SDK](https://aka.ms/vq9mfn).

> [AZURE.IMPORTANT] Para concluir este tutorial, tem uma conta do Azure active. Se não tiver uma conta, pode criar uma conta de avaliação gratuita apenas de duas minutos. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-android-get-started).

## <a name="set-up-mobile-engagement-for-your-android-app"></a>Configurar o Mobile Cativação para a sua aplicação Android

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a name="connect-your-app-to-the-mobile-engagement-backend"></a>Ligar a sua aplicação ao Mobile Cativação back-end

Neste tutorial apresenta uma "básica integração", que é o conjunto mínimo necessário para recolher dados e enviar uma notificação de emissão. Criar uma aplicação básica com Android Studio para demonstrar a integração.

A documentação de integração completa pode ser encontrada na [integração com o Mobile Cativação Android SDK](mobile-engagement-android-sdk-overview.md).

### <a name="create-an-android-project"></a>Criar um projecto Android

1. Inicie o **Android Studio**e, no pop-up, selecione **Iniciar um novo projecto Android Studio**.

    ![][1]

2. Fornece um domínio de nome e a empresa de aplicação. Tome nota das quais que está a preencher, porque necessitar mais tarde. Clique em **seguinte**.

    ![][2]

3. Selecione o nível de API e fator de formulário de destino e, clique em **seguinte**.

    >[AZURE.NOTE] Mobile Cativação requer o nível de API 10 mínimo (Android 2.3.3).

    ![][3]

4. Selecione aqui a **Atividade em branco** , que é o ecrã apenas para esta aplicação e clique em **seguinte**.

    ![][4]

5. Por fim, mantenha as predefinições como está e clique em **Concluir**.

    ![][5]

Android Studio agora cria a aplicação de demonstração nas quais podemos integrar Cativação Mobile.

### <a name="include-the-sdk-library-in-your-project"></a>Incluir a biblioteca SDK no projeto

1. Transferir o [SDK Android Cativação móvel](https://aka.ms/vq9mfn).
2. Extrai o ficheiro de arquivo para uma pasta no seu computador.
3. Identificar a biblioteca de .jar para a versão atual deste SDK e copiá-lo para a área de transferência.

      ![][6]

4. Navegue para a secção do **projecto** (1) e colar a .jar na pasta efectuar (2).

      ![][7]

5. Para carregar a biblioteca, sincronize o projeto.

      ![][8]

### <a name="connect-your-app-to-mobile-engagement-backend-with-the-connection-string"></a>Ligar a sua aplicação para back-end de Cativação Mobile com a cadeia de ligação

1. Copie as linhas seguintes do código para a criação de atividade (tem de ser feito apenas num único local da sua aplicação, normalmente a atividade principal). Para esta aplicação de exemplo, abra o MainActivity em src -> principais -> pasta java e adicione o seguinte:

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
        EngagementAgent.getInstance(this).init(engagementConfiguration);

2. Resolver as referências ao premir Alt + Enter ou ao adicionar as seguintes declarações de importar:

        import com.microsoft.azure.engagement.EngagementAgent;
        import com.microsoft.azure.engagement.EngagementConfiguration;

3. Regresse ao Portal clássica do Azure na página de **Informações de ligação** da sua aplicação e copie a **Cadeia de ligação**.

      ![][9]

4. Colá-lo à `setConnectionString` parâmetro, substituição de toda a cadeia mostrada no seguinte código:

        engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

### <a name="add-permissions-and-a-service-declaration"></a>Adicionar permissões e uma declaração de serviço

1. Adicionar estas permissões a manifest do seu projeto imediatamente antes ou depois do `<application>` etiqueta:

        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

2. Para declarar o serviço do agente, adicione este código entre o `<application>` e `</application>` etiquetas:

        <service
            android:name="com.microsoft.azure.engagement.service.EngagementService"
            android:exported="false"
            android:label="<Your application name>"
            android:process=":Engagement"/>

3. No código colado, substituir `"<Your application name>"` na etiqueta, que é apresentado no menu de **Definições** onde pode ver services em execução no dispositivo. Pode adicionar a palavra "Serviço" no que identificam por exemplo.

### <a name="send-a-screen-to-mobile-engagement"></a>Enviar um ecrã para Mobile Cativação

Para começar a enviar dados e certifique-se de que os utilizadores estão ativos, terá de enviar, pelo menos, um ecrã (actividade) para o back-end Cativação Mobile.

Vá para **MainActivity.java** e adicione o seguinte para substituir a classe de **MainActivity** para **EngagementActivity**base:

    public class MainActivity extends EngagementActivity {

> [AZURE.NOTE] Se sua aula base não for *atividade*, consulte [Avançadas Android elaboração de relatórios](mobile-engagement-android-advanced-reporting.md#modifying-your-codeactivitycode-classes) como herdam a partir de diferentes categorias.


Comentar a linha seguinte para este cenário de exemplo simples:

    // setSupportActionBar(toolbar);

Se pretender manter o `ActionBar` na sua aplicação, consulte o artigo [Avançadas Android elaboração de relatórios](mobile-engagement-android-advanced-reporting.md#modifying-your-codeactivitycode-classes).

## <a name="connect-app-with-real-time-monitoring"></a>Ligar a aplicação com monitorização em tempo real

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a name="enable-push-notifications-and-in-app-messaging"></a>Ativar as notificações push e mensagens de na aplicação

Durante uma campanha, Cativação Mobile permite-lhe interagir com e ALCANÇAR os utilizadores com as notificações push e mensagens de na aplicação. Este módulo chama alcance no portal do Cativação Mobile.
A secção seguinte configura-se a sua aplicação para recebê-las.

### <a name="copy-sdk-resources-in-your-project"></a>Copiar SDK recursos no projeto

1. Navegar de volta para o seu conteúdo de transferência SDK e copie a pasta **resoluções** .

    ![][10]

2. Aceda novamente para Android Studio, selecione a pasta **principal** dos seus ficheiros de projeto e, em seguida, cole-o para adicionar os recursos ao projeto.

    ![][11]

[AZURE.INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[AZURE.INCLUDE [Enable in-app messaging](../../includes/mobile-engagement-android-send-push.md)]

[AZURE.INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

## <a name="next-steps"></a>Próximos passos

Aceda ao [Android SDK](mobile-engagement-android-sdk-overview.md) para obter dados de conhecimento detalhados sobre a integração de SDK.

<!-- Images. -->
[1]: ./media/mobile-engagement-android-get-started/android-studio-new-project.png
[2]: ./media/mobile-engagement-android-get-started/android-studio-project-props.png
[3]: ./media/mobile-engagement-android-get-started/android-studio-project-props2.png
[4]: ./media/mobile-engagement-android-get-started/android-studio-add-activity.png
[5]: ./media/mobile-engagement-android-get-started/android-studio-activity-name.png
[6]: ./media/mobile-engagement-android-get-started/sdk-content.png
[7]: ./media/mobile-engagement-android-get-started/paste-jar.png
[8]: ./media/mobile-engagement-android-get-started/sync-project.png
[9]: ./media/mobile-engagement-android-get-started/app-connection-info-page.png
[10]: ./media/mobile-engagement-android-get-started/copy-resources.png
[11]: ./media/mobile-engagement-android-get-started/paste-resources.png
