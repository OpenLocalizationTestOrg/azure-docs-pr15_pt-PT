<properties
    pageTitle="Localização a relatar sobre Azure Cativação móvel Android SDK"
    description="Descreve como configurar a relatar sobre Azure Mobile Cativação Android SDK de localização"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/12/2016"
    ms.author="piyushjo;ricksal" />

# <a name="location-reporting-for-azure-mobile-engagement-android-sdk"></a>Localização a relatar sobre Azure Cativação móvel Android SDK

> [AZURE.SELECTOR]
- [Android](mobile-engagement-android-integrate-engagement.md)

Este tópico descreve como fazer localização relatórios para a sua aplicação Android.

## <a name="prerequisites"></a>Pré-requisitos

[AZURE.INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="location-reporting"></a>Relatórios de localização

Se pretender localizações de modo a ser comunicado, é necessário adicionar algumas linhas de configuração (entre o `<application>` e `</application>` etiquetas).

### <a name="lazy-area-location-reporting"></a>Área preguiça relatórios de localização

Localização para a área preguiça permite elaboração de relatórios a país, região e Localidade associados com dispositivos de elaboração de relatórios. Este tipo de elaboração de relatórios de localização utiliza apenas localizações de rede (com base na célula ID ou conta). A área de dispositivo é comunicada pelo mais uma vez por sessão. Nunca é utilizado o GPS e, por conseguinte, este tipo de relatório de localização possui baixo impacto de baterias.

Áreas denunciadas são utilizadas para calcular estatísticas geográficas sobre utilizadores, sessões, eventos e erros. Também pode ser utilizados como critério em campanhas de atingir.

Ativar a localização para a área preguiça relatórios utilizando a configuração anteriormente mencionada neste procedimento:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Também precisa de especificar uma permissão de localização. Este código utiliza ``COARSE`` permissão:

    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Se a sua aplicação requerido, pode utilizar ``ACCESS_FINE_LOCATION`` em vez disso.

### <a name="real-time-location-reporting"></a>Relatórios de localização em tempo real

Relatórios de localização em tempo real permite elaboração de relatórios de latitude e longitude associados com dispositivos. Por predefinição, este tipo de elaboração de relatórios de localização utiliza apenas localizações de rede, com base em ID da célula ou conta. O relatório só está ativa quando a aplicação é executado em primeiro plano (por exemplo, durante uma sessão).

Localizações em tempo real são *não* utilizado para calcular as estatísticas. O seu objectivo apenas é permitir a utilização em tempo real geo-vedações \<alcance-audiência-geofencing\> critério em campanhas de atingir.

Para ativar a localização em tempo real elaboração de relatórios, adicione uma linha de código para onde definir a cadeia de ligação de Cativação na atividade de iniciador de aplicações. O resultado tem o seguinte aspeto:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

        You also need to specify a location permission. This code uses ``COARSE`` permission:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

        If your app requires it, you can use ``ACCESS_FINE_LOCATION`` instead.

#### <a name="gps-based-reporting"></a>GPS com base elaboração de relatórios

Por predefinição, relatórios de localização em tempo real só utiliza localizações de rede. Para ativar a utilização de localizações com base em GPS, que são mais extremidade preciso, utilize o objeto de configuração:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Também terá de se em falta, adicione as seguintes permissões:

    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### <a name="background-reporting"></a>Relatórios de fundo

Por predefinição, relatórios de localização em tempo real só está ativa quando a aplicação é executado em primeiro plano (por exemplo, durante uma sessão). Para ativar a denúncia também em segundo plano, utilize este objeto de configuração:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [AZURE.NOTE] Quando a aplicação é executada em segundo plano, apenas localizações baseadas em rede são reportadas, mesmo se activou o GPS.

Se o utilizador for reiniciado o seu dispositivo, o relatório de localização de fundo está parado. Para efetuar reiniciar automaticamente durante o arranque, adicione este código.

    <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
           android:exported="false">
        <intent-filter>
            <action android:name="android.intent.action.BOOT_COMPLETED" />
        </intent-filter>
    </receiver>

Também terá de se em falta, adicione as seguintes permissões:

    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

## <a name="android-m-permissions"></a>Permissões de M Android

Começando Android M, algumas permissões são geridas durante a execução e necessitam de aprovação de utilizador.

Se o destino nível Android API 23, as permissões de runtime são desactivadas por predefinição para novas instalações de aplicação. Caso contrário, estão activadas por predefinição.

Pode pode ativar/desativar essas permissões a partir do menu de definições de dispositivo. Desativar permissões a partir do menu sistema elimina se os processos de fundo da aplicação, o que é um comportamento do sistema e não tem nenhum impacto na capacidade de receber push em segundo plano.

No contexto da localização Mobile Cativação elaboração de relatórios, as permissões que necessitam de aprovação no runtime são:

- `ACCESS_COARSE_LOCATION`
- `ACCESS_FINE_LOCATION`

Solicitar permissões do utilizador com uma caixa de diálogo sistema padrão. Se o utilizador aprova, descobrir ``EngagementAgent`` tirar essa alteração para a conta em tempo real. Caso contrário, a alteração é processada da próxima vez que o utilizador inicia a aplicação.

Eis um exemplo de código para utilizar numa atividade da sua aplicação para solicitar permissões e reencaminhar o resultado se for positivo para ``EngagementAgent``:

    @Override
    public void onCreate(Bundle savedInstanceState)
    {
      /* Other code... */

      /* Request permissions */
      requestPermissions();
    }

    @TargetApi(Build.VERSION_CODES.M)
    private void requestPermissions()
    {
      /* Avoid crashing if not on Android M */
      if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M)
      {
        /*
         * Request location permission, but this doesn't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }
