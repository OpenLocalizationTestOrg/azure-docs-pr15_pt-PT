<properties
    pageTitle="Azure Mobile Cativação iOS SDK integração | Microsoft Azure"
    description="Atualizações e procedimentos para iOS SDK para Azure Mobile Cativação mais recentes"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="piyushjo" />

#<a name="how-to-integrate-engagement-on-ios"></a>Como integrar Cativação IOS

> [AZURE.SELECTOR]
- [Universal do Windows](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-integrate-engagement.md)

Este procedimento descreve a forma mais simples para ativar do Cativação e de análise monitorização de funções na aplicação do iOS.

Requer o SDK Cativação iOS6 + e Xcode 8: o destino de implementação da sua aplicação tem de ter, pelo menos, iOS 6.

> [AZURE.NOTE]
> Se dependem XCode 7 na verdade, em seguida, pode utilizar o [iOS Cativação SDK v3.2.4](https://aka.ms/r6oouh). Existe um erro conhecido no módulo de atingir desta versão anterior enquanto em execução em dispositivos iOS 10 consulte [a integração do módulo alcance](mobile-engagement-ios-integrate-engagement-reach.md) para obter mais detalhes. Se optar por utilizar o v3.2.4 SDK, em seguida, basta ignorar o `UserNotifications.framework` importar no próximo passo.

Os passos seguintes são suficiente para ativar o relatório de registos necessários para calcular todas as estatísticas sobre utilizadores, sessões, atividades, falhas e Technicals. O relatório de registos necessários para calcular outras estatísticas como tarefas, erros e eventos têm de ser efetuado manualmente utilizando a API Cativação (consulte o artigo [como utilizar o Cativação Mobile avançadas etiquetagem API na sua aplicação iOS](mobile-engagement-ios-use-engagement-api.md) uma vez que estas estatísticas são dependentes das aplicações.

##<a name="embed-the-engagement-sdk-into-your-ios-project"></a>Incorporar o SDK Cativação no seu projeto do iOS

- Transfira o iOS SDK a partir de [aqui](http://aka.ms/qk2rnj).

- Adicionar o SDK Cativação ao projeto iOS: no Xcode, clique com o botão direito do rato no seu projeto e selecione **"Adicionar ficheiros para..."** e selecione o `EngagementSDK` pasta.

- Necessita de Cativação quadros adicionais para trabalhar: no Explorador do projeto, abra o painel do project e selecione o destino correto. Em seguida, abra o separador **"Criar fases"** e no menu de **"ligação binário com bibliotecas"** , adicione estes quadros:

    -   `UserNotifications.framework`-configurar a ligação como`Optional`
    -   `AdSupport.framework`-configurar a ligação como`Optional`
    -   `SystemConfiguration.framework`
    -   `CoreTelephony.framework`
    -   `CFNetwork.framework`
    -   `CoreLocation.framework`
    -   `libxml2.dylib`

> [AZURE.NOTE] A arquitetura de AdSupport pode ser removida. Cativação tem esta arquitetura de recolher a IDFA. No entanto, pode ser desativada coleção IDFA \<ios sdk-Cativação idfa\> para cumprir a nova política Apple relativamente a este ID.

##<a name="initialize-the-engagement-sdk"></a>Iniciar a Cativação SDK

Tem de modificar o seu delegado de aplicação:

-   Na parte superior do seu ficheiro de implementação, importe o agente de Cativação:

        [...]
        #import "EngagementAgent.h"

-   Iniciar Cativação dentro do método '**applicationDidFinishLaunching:**'ou'**aplicação: didFinishLaunchingWithOptions:**':

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
          [...]
          [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
          [...]
        }

##<a name="basic-reporting"></a>Relatório básico

### <a name="recommended-method-overload-your-uiviewcontroller-classes"></a>Recomendado método: Sobrecarga seu `UIViewController` classes

Para ativar o relatório de todos os registos necessários por Cativação para calcular os utilizadores, sessões, atividades, falhas e estatísticas técnicas, basta fazer com que todas as sua `UIViewController` sub-classes herdam a partir do `EngagementViewController` classes (mesmo regra para `UITableViewController`  - \> `EngagementTableViewController`).

**Sem Cativação:**

    #import <UIKit/UIKit.h>

    @interface Tab1ViewController : UIViewController<UITextFieldDelegate> {
      UITextField* myTextField1;
      UITextField* myTextField2;
    }

    @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
    @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

**Com Cativação:**

    #import <UIKit/UIKit.h>
    #import "EngagementViewController.h"

    @interface Tab1ViewController : EngagementViewController<UITextFieldDelegate> {
      UITextField* myTextField1;
      UITextField* myTextField2;
    }

    @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
    @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

### <a name="alternate-method-call-startactivity-manually"></a>Método alternativo: chamar `startActivity()` manualmente

Se não conseguir ou não pretende que seja sobrecarga seu `UIViewController` classes, em vez disso, pode começar a suas atividades ao contactar o suporte `EngagementAgent`do métodos diretamente.

> [AZURE.IMPORTANT] IOS SDK chama-se automaticamente o `endActivity()` método quando a aplicação está fechada. Assim, é *vivamente* recomendado para ligar para o `startActivity` método sempre que a atividade do utilizador alterar e, para *nunca* ligar o `endActivity` método, uma vez que chamar este método força a sessão actual para ser terminado.

##<a name="location-reporting"></a>Relatórios de localização

Apple os termos de serviço não permitem a aplicações para utilizar o rastreio para apenas a finalidade de estatísticas de localização. Assim sendo, recomenda-se para ativar a localização relatórios apenas se a sua aplicação também utilizar a localização de controlo por qualquer outra razão.

Começar com iOS 8, tem de fornecer uma descrição para a forma como a aplicação utiliza serviços de localização definindo uma cadeia de para a chave [NSLocationWhenInUseUsageDescription] ou [NSLocationAlwaysUsageDescription] no ficheiro de Info.plist da sua aplicação. Se pretender a localização do relatório em segundo plano com Cativação, adicione a chave NSLocationAlwaysUsageDescription. Em todos os outros casos, adicione a chave NSLocationWhenInUseUsageDescription.

### <a name="lazy-area-location-reporting"></a>Área preguiça relatórios de localização

Relatórios de localização de área preguiça permite para comunicar a país, região e Localidade associadas a dispositivos. Este tipo de elaboração de relatórios de localização utiliza apenas localizações de rede (com base na célula ID ou conta). A área de dispositivo é comunicada pelo mais uma vez por sessão. Nunca é utilizado o GPS e, portanto, este tipo de relatório de localização com poucas (não para dizer n) impacto na baterias.

Áreas denunciadas são utilizadas para calcular estatísticas geográficas sobre utilizadores, sessões, eventos e erros. Também pode ser utilizados como critério em campanhas de atingir. A última área conhecida comunicada para um dispositivo pode ser obtida graças ao [API do dispositivo].

Para ativar a área preguiça localização relatórios, adicione a linha seguinte após a inicialização o agente de Cativação:

    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
    {
      [...]
      [[EngagementAgent shared] setLazyAreaLocationReport:YES];
      [...]
    }

### <a name="real-time-location-reporting"></a>Relatórios de localização em tempo real

Relatórios de localização em tempo real permite comunicar latitude e longitude associadas a dispositivos. Por predefinição, este tipo de elaboração de relatórios de localização utiliza apenas localizações de rede (com base na célula ID ou conta), e o reporte só está ativo quando a aplicação é executado em primeiro plano (ou seja, durante uma sessão).

Em tempo real localizações *não* utilizado para calcular as estatísticas estão. O seu objectivo apenas é permitir a utilização de tempo real geo-vedações \<alcance-audiência-geofencing\> critério em campanhas de atingir.

Para ativar a localização em tempo real de elaboração de relatórios, adicione a linha seguinte após a inicialização o agente de Cativação:

    [[EngagementAgent shared] setRealtimeLocationReport:YES];

#### <a name="gps-based-reporting"></a>GPS com base elaboração de relatórios

Por predefinição, relatórios de localização em tempo real só utiliza localizações de rede com base. Para ativar a utilização das localizações GPS com base (que são precisas muito mais), adicione:

    [[EngagementAgent shared] setFineRealtimeLocationReport:YES];

#### <a name="background-reporting"></a>Relatórios de fundo

Por predefinição, relatórios de localização em tempo real só está ativa quando a aplicação é executado em primeiro plano (ou seja, durante uma sessão). Para ativar a denúncia também em segundo plano, adicione:

    [[EngagementAgent shared] setBackgroundRealtimeLocationReport:YES withLaunchOptions:launchOptions];

> [AZURE.NOTE] Quando a aplicação é executada em segundo plano, só de rede com base localizações são reportados, mesmo se activou o GPS.

Pós-implementação desta função ligarem [startMonitoringSignificantLocationChanges] quando a aplicação entra em segundo plano. Tenha em atenção que-lo será automaticamente volte a iniciar a aplicação em segundo plano se chegar um novo evento de localização.

##<a name="advanced-reporting"></a>Avançadas de elaboração de relatórios

Opcionalmente, se pretender comunicar eventos específicos da aplicação, erros e tarefas, terá de utilizar a API Cativação através dos métodos da `EngagementAgent` escolares. Um objeto desta classe pode ser obtido ao contactar o `[EngagementAgent shared]` método estático.

A API Cativação permite-lhe para utilizar todas as funcionalidades avançadas de Cativação e detalhadas mais como utilizar a API Cativação no iOS (bem como na documentação técnica da `EngagementAgent` escolares).

##<a name="disable-idfa-collection"></a>Desativar a coleção de IDFA

Por predefinição, Cativação irá utilizar o [IDFA] para identificar um utilizador de forma exclusiva. Mas, se não estiver a utilizar noutro local publicidade na aplicação, poderá ser rejeitada pelo processo de revisão a App Store. Colecção de IDFA pode ser desativada ao adicionar a macro pré-processador `ENGAGEMENT_DISABLE_IDFA` no seu ficheiro pch (ou na `Build Settings` da sua aplicação). Isto irá garantir que não existe nenhuma referências a `ASIdentifierManager`, `advertisingIdentifier` ou `isAdvertisingTrackingEnabled` na compilação de aplicação.

Integração no ficheiro **prefix.pch** :

    #define ENGAGEMENT_DISABLE_IDFA
    ...

Pode verificar que a coleção de IDFA corretamente está desactivada na sua aplicação ao selecionar os registos de teste de Cativação. Consulte a integração de teste\<ios-sdk-Cativação-teste-idfa\> documentação para obter mais informações.

##<a name="disable-log-reporting"></a>Desactivar o relatório de registo

### <a name="using-a-method-call"></a>Utilizar uma chamada de método

Se pretender Cativação para parar o envio de registos, pode ligar:

    [[EngagementAgent shared] setEnabled:NO];

Esta chamada é persistente: utiliza `NSUserDefaults` para armazenar as informações.

Pode ativar o registo de elaboração de relatórios novamente ao contactar o suporte a mesma função com `YES`.

### <a name="integration-in-your-settings-bundle"></a>Integração no seu pacote de definições

Em vez de chamar esta função, também pode integrar o esta definição diretamente no seu existentes `Settings.bundle` ficheiro. A cadeia `engagement_agent_enabled` deve ser utilizado como uma o identificador de preferência e este tem de ser associadas a um botão de alternar comutador (`PSToggleSwitchSpecifier`).

O exemplo seguinte de `Settings.bundle` mostra como implementá-lo:

    <dict>
        <key>PreferenceSpecifiers</key>
        <array>
            <dict>
                <key>DefaultValue</key>
                <true/>
                <key>Key</key>
                <string>engagement_agent_enabled</string>
                <key>Title</key>
                <string>Log reporting enabled</string>
                <key>Type</key>
                <string>PSToggleSwitchSpecifier</string>
            </dict>
        </array>
        <key>StringsTable</key>
        <string>Root</string>
    </dict>

<!-- URLs. -->
[Dispositivo API]: http://go.microsoft.com/?linkid=9876094
[NSLocationWhenInUseUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26
[NSLocationAlwaysUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18
[startMonitoringSignificantLocationChanges]:http://developer.apple.com/library/IOs/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html#//apple_ref/occ/instm/CLLocationManager/startMonitoringSignificantLocationChanges
[IDFA]:https://developer.apple.com/library/ios/documentation/AdSupport/Reference/ASIdentifierManager_Ref/ASIdentifierManager.html#//apple_ref/occ/instp/ASIdentifierManager/advertisingIdentifier
