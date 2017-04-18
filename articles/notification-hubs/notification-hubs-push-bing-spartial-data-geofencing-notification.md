<properties
    pageTitle="Notificações push geo vedadas com concentradores de notificação do Azure e dados espacial Bing | Microsoft Azure"
    description="Neste tutorial, irá aprender a entregar notificações push baseadas na localização com concentradores de notificação do Azure e dados espacial do Bing."
    services="notification-hubs"
    documentationCenter="windows"
    keywords="notificações push, notificação de emissão"
    authors="dend"
    manager="yuaxu"
    editor="dend"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-phone"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="05/31/2016"
    ms.author="dendeli"/>
    
# <a name="geo-fenced-push-notifications-with-azure-notification-hubs-and-bing-spatial-data"></a>Notificações push geo vedadas com concentradores de notificação do Azure e dados espacial do Bing
 
 > [AZURE.NOTE] Para concluir este tutorial, tem de ter uma conta do Azure active. Se não tiver uma conta, pode criar uma conta de avaliação gratuita apenas de duas minutos. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02).

Neste tutorial, irá aprender a entregar notificações push baseadas na localização com concentradores de notificação do Azure e dados espacial do Bing, utilizadas a partir de dentro de uma aplicação de plataforma Windows Universal.

##<a name="prerequisites"></a>Pré-requisitos
Em primeiro lugar e mais, é necessário para se certificar de que tem todos os os software e serviços da pré-requisitos:

* [1 de atualização de 2015 do Visual Studio](https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx) ou posterior ([Community Edition](https://go.microsoft.com/fwlink/?LinkId=691978&clcid=0x409) executará também). 
* Versão mais recente do [Azure SDK](https://azure.microsoft.com/downloads/). 
* [Conta Dev Center do mapas Bing](https://www.bingmapsportal.com/) (pode criar um gratuitamente e associá-la com a sua conta Microsoft). 

##<a name="getting-started"></a>Introdução

Vamos começar por criar projeto. No Visual Studio, inicie um novo projeto de tipo de **Aplicação vazia (Universal Windows)**.

![](./media/notification-hubs-geofence/notification-hubs-create-blank-app.png)

Uma vez concluída a criação de projeto, que deve ter feixe para a própria aplicação. Agora vamos configurar tudo para a infraestrutura de geo vedações. Uma vez que vamos para utilizar os serviços do Bing para esta situação, existe um ponto de final de REST API público que lhe permite-nos molduras localização específica de consulta:

    http://spatial.virtualearth.net/REST/v1/data/
    
Terá de especificar os seguintes parâmetros para pôr a funcionar:

* **ID da origem de dados** e o **Nome da origem de dados** – no Bing Maps API, origens de dados contenham várias metadados bucketed, como localizações e o horário de funcionamento de operação. Pode ler mais sobre essas aqui. 
* **Nome da entidade** – a entidade que pretende utilizar como ponto de referência para a notificação. 
* **Tecla de API de mapas Bing** – esta é a chave que obteve anteriormente quando criou a conta do Centro de Dev Center do Bing.
 
Vamos fazer uma aprofundada sobre a configuração para cada um dos elementos acima.

##<a name="setting-up-the-data-source"></a>Configurar a origem de dados

Pode fazê-lo no Centro de Dev Center do Bing Maps. Basta clicar em **origens de dados** na barra de navegação superior e selecione **Gerir origens de dados**.

![](./media/notification-hubs-geofence/bing-maps-manage-data.png)

Se não trabalhou com a API de mapas Bing antes, provavelmente não haverá quaisquer origens de dados apresentar, para que possa criar apenas um novo ao clicar em carregar dados a uma origem de dados. Certifique-se de que preencha todos os campos:

![](./media/notification-hubs-geofence/bing-maps-create-data.png)

Poderá estar a pensar – o que é o ficheiro de dados e o que deve que ser carregar? Para efeitos deste teste, pode apenas utilizamos a amostra com base em pipe que frames uma área da waterfront Castelo Branco:

    Bing Spatial Data Services, 1.0, TestBoundaries
    EntityID(Edm.String,primaryKey)|Name(Edm.String)|Longitude(Edm.Double)|Latitude(Edm.Double)|Boundary(Edm.Geography)
    1|SanFranciscoPier|||POLYGON ((-122.389825 37.776598,-122.389438 37.773087,-122.381885 37.771849,-122.382186 37.777022,-122.389825 37.776598))
    
O que precede representa esta entidade:

![](./media/notification-hubs-geofence/bing-maps-geofence.png)

Basta copiar e colar cadeia acima para um novo ficheiro e guardá-lo como **NotificationHubsGeofence.pipe**e carregue-o no Centro de Dev Center do Bing.

>[AZURE.NOTE]Poderá ser-lhe pedido para especificar uma nova chave para a **chave principal** que é a diferença entre a **Chave de consulta**. Basta criar uma nova chave através do dashboard e atualize a página de carregamento de origem de dados.

Depois de carregar o ficheiro de dados, terá de Certifique-se de que publica a origem de dados. 

Aceda a **Gerir origens de dados**, tal como fizemos acima, encontrar a sua origem de dados na lista e clique em **Publicar** na coluna de **ações** . Num pouco, deverá ver a origem de dados no separador **Origens de dados publicadas** :

![](./media/notification-hubs-geofence/bing-maps-published-data.png)

Se clicar em **Editar**, será capaz de ver rapidamente quais as localizações podemos introduzidos numa-lo:

![](./media/notification-hubs-geofence/bing-maps-data-details.png)

Neste momento, o portal não mostrar-lhe os limites para o geofence que criámos – só precisamos de uma confirmação de que a localização especificada é na proximidade à direita.

Dispõe agora de todos os requisitos da origem de dados. Para obter detalhes sobre o URL do pedido para a chamada, de API no Centro de Dev Center do mapas Bing, clique em **origens de dados** e selecione **Informações de origens de dados**.

![](./media/notification-hubs-geofence/bing-maps-data-info.png)

O **URL de consulta** é o que estamos após aqui. Este é o ponto final contra que recomendamos pode executar consultas para verificar se o dispositivo está atualmente dentro dos limites de uma localização, ou não. Para executar esta verificação, precisamos simplesmente executar uma chamada de obter contra o URL da consulta, com os seguintes parâmetros acrescentados:

    ?spatialFilter=intersects(%27POINT%20LONGITUDE%20LATITUDE)%27)&$format=json&key=QUERY_KEY

Dessa forma que especificar um ponto de destino que recomendamos obter a partir do dispositivo e o Bing Maps automaticamente irá efetuar os cálculos para ver se se encontra dentro de geofence. Depois de executar o pedido através de um browser (ou Laço), irá obter padrão uma resposta JSON:

![](./media/notification-hubs-geofence/bing-maps-json.png)

Esta resposta só acontece quando o ponto de é realmente dentro dos limites do designadas. Se não for, irá obter um balde vazios **resultados** :

![](./media/notification-hubs-geofence/bing-maps-nores.png)

##<a name="setting-up-the-uwp-application"></a>Configurar a aplicação UWP

Agora que temos pronta a origem de dados, podemos pode começar a trabalhar na aplicação UWP que recomendamos bootstrapped anterior.

Em primeiro lugar e mais, podemos tem de ativar serviços de localização para a nossa aplicação. Para executar esta tarefa, faça duplo clique no `Package.appxmanifest` ficheiro no **Explorador de solução**.

![](./media/notification-hubs-geofence/vs-package-manifest.png)

No separador Propriedades de pacote que acabou de abrir, clique em **capacidades** e certifique-se de que seleciona **localização**:

![](./media/notification-hubs-geofence/vs-package-location.png)

Tal como a funcionalidade de localização for declarada, crie uma nova pasta na sua solução denominada `Core`e adicionar um novo ficheiro dentro da mesma, denominado `LocationHelper.cs`:

![](./media/notification-hubs-geofence/vs-location-helper.png)

O `LocationHelper` própria classe é bastante básica neste momento – tudo o que faz é a permitir-nos obter a localização do utilizador através do sistema API:

    using System;
    using System.Threading.Tasks;
    using Windows.Devices.Geolocation;

    namespace NotificationHubs.Geofence.Core
    {
        public class LocationHelper
        {
            private static readonly uint AppDesiredAccuracyInMeters = 10;

            public async static Task<Geoposition> GetCurrentLocation()
            {
                var accessStatus = await Geolocator.RequestAccessAsync();
                switch (accessStatus)
                {
                    case GeolocationAccessStatus.Allowed:
                        {
                            Geolocator geolocator = new Geolocator { DesiredAccuracyInMeters = AppDesiredAccuracyInMeters };

                            return await geolocator.GetGeopositionAsync();
                        }
                    default:
                        {
                            return null;
                        }
                }
            }

        }
    }

Pode ler mais sobre como obter a localização do utilizador nas aplicações UWP no [documento MSDN](https://msdn.microsoft.com/library/windows/apps/mt219698.aspx)oficial.

Para verificar que a aquisição localização esteja realmente a funcionar, abra o lado do código da sua página principal (`MainPage.xaml.cs`). Criar um novo processador de eventos para o `Loaded` evento no calendário a `MainPage` construtor:

    public MainPage()
    {
        this.InitializeComponent();
        this.Loaded += MainPage_Loaded;
    }

A aplicação de processador de eventos seja da seguinte forma:

    private async void MainPage_Loaded(object sender, RoutedEventArgs e)
    {
        var location = await LocationHelper.GetCurrentLocation();

        if (location != null)
        {
            Debug.WriteLine(string.Concat(location.Coordinate.Longitude,
                " ", location.Coordinate.Latitude));
        }
    }

Repare que recomendamos declarado como o processador como assíncronas porque existe `GetCurrentLocation` é awaitable e, por isso, necessita de ser executada num contexto assíncrona. Além disso, uma vez que em determinadas circunstâncias podemos poderá conclui-lo com uma localização nula (por exemplo, a localização que serviços são desativados ou a aplicação foi negada permissões para uma localização de acesso), precisamos de Certifique-se de que é um processado corretamente com uma marca de verificação nula.

Execute a aplicação. Certifique-se de que permite o acesso de localização:

![](./media/notification-hubs-geofence/notification-hubs-location-access.png)

Uma vez lançamentos aplicação, deverá conseguir ver as coordenadas na janela de **saída** :

![](./media/notification-hubs-geofence/notification-hubs-location-output.png)

Agora já sabe que localização aquisição funciona – esteja à vontade remover o processador de eventos de teste para carregado porque podemos não ser utilizá-lo já.

O passo seguinte consiste em capturar alterações de localização. Para que, vamos aceda novamente para o `LocationHelper` classe e adicione o processador de eventos `PositionChanged`:

    geolocator.PositionChanged += Geolocator_PositionChanged;

A aplicação irá apresentar as coordenadas localização na janela de **saída** :

    private static async void Geolocator_PositionChanged(Geolocator sender, PositionChangedEventArgs args)
    {
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
        {
            Debug.WriteLine(string.Concat(args.Position.Coordinate.Longitude, " ", args.Position.Coordinate.Latitude));
        });
    }

##<a name="setting-up-the-backend"></a>Configurar o back-end

Transfira a [Amostra de back-end .NET das GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/NotifyUsers). Assim que a transferência terminar, abra o `NotifyUsers` pasta e subsequentemente – a `NotifyUsers.sln` ficheiro.

Definir o `AppBackend` projecto como o **Projeto de arranque** e iniciá-la.

![](./media/notification-hubs-geofence/vs-startup-project.png)

O projecto já está configurado para enviar notificações push para dispositivos de destino, pelo que terá faz apenas duas coisas – trocar a ligação à direita do concentrador de notificação de cadeia e adicionar a identificação de limite para enviar a notificação de apenas quando o utilizador está dentro de geofence.

Para configurar a cadeia de ligação, na `Models` pasta abrir `Notifications.cs`. O `NotificationHubClient.CreateClientFromConnectionString` função deve contêm as informações sobre o seu centro de notificação que pode obter no [Portal do Azure](https://portal.azure.com) (aspeto dentro de **Políticas de acesso** pá nas **Definições**). Guarde o ficheiro de configuração actualizada.

Agora precisamos de criar um modelo para o resultado de API de mapas Bing. A forma mais fácil para o fazer é o botão direito do rato sobre o `Models` pasta, **Adicionar** > **escolares**. -Lhe o nome `GeofenceBoundary.cs`. Uma vez feito, copie o JSON da resposta à API que debatido na primeira secção e na utilização do Visual Studio **Editar** > **Colar especial** > **JSON Colar como Classes**. 

Dessa forma que podemos Certifique-se de que o objeto serialização irá ser anulado exatamente como se pretendia. O conjunto de classe resultante deve um aspeto semelhante ao seguinte:

    namespace AppBackend.Models
    {
        public class Rootobject
        {
            public D d { get; set; }
        }

        public class D
        {
            public string __copyright { get; set; }
            public Result[] results { get; set; }
        }

        public class Result
        {
            public __Metadata __metadata { get; set; }
            public string EntityID { get; set; }
            public string Name { get; set; }
            public float Longitude { get; set; }
            public float Latitude { get; set; }
            public string Boundary { get; set; }
            public string Confidence { get; set; }
            public string Locality { get; set; }
            public string AddressLine { get; set; }
            public string AdminDistrict { get; set; }
            public string CountryRegion { get; set; }
            public string PostalCode { get; set; }
        }

        public class __Metadata
        {
            public string uri { get; set; }
        }
    }

Em seguida, abra `Controllers`  >  `NotificationsController.cs`. Precisamos de aperfeiçoar a chamada de mensagem para ter em conta a latitude e longitude de destino. Para que, basta adicionar duas cadeias à função assinatura – `latitude` e `longitude`.

    public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag, string latitude, string longitude)

Criar uma nova classe dentro do projecto denominado `ApiHelper.cs` – vamos utilizá-lo para ligar ao Bing para verificar aponte intersecções de limite. Implementar um `IsPointWithinBounds` função, da seguinte forma:

    public class ApiHelper
    {
        public static readonly string ApiEndpoint = "{YOUR_QUERY_ENDPOINT}?spatialFilter=intersects(%27POINT%20({0}%20{1})%27)&$format=json&key={2}";
        public static readonly string ApiKey = "{YOUR_API_KEY}";

        public static bool IsPointWithinBounds(string longitude,string latitude)
        {
            var json = new WebClient().DownloadString(string.Format(ApiEndpoint, longitude, latitude, ApiKey));
            var result = JsonConvert.DeserializeObject<Rootobject>(json);
            if (result.d.results != null && result.d.results.Count() > 0)
            {
                return true;
            }
            else
            {
                return false;
            }
        }
    }

>[AZURE.NOTE] Certifique-se substituir o ponto final API com o URL de consulta que obteve anteriormente a partir do Bing Dev Center do (mesmo aplica-se para a chave de API). 

Se existirem resultados para a consulta, o que significa que o ponto de especificado é dentro dos limites do geofence, para que é devolvido `true`. Se não existem resultados, Bing é informar-nos que é o ponto fora da moldura de pesquisa, para que é devolvido `false`.

Novamente na `NotificationsController.cs`, criar uma verificação para a direita antes da instrução de parâmetro:

    if (ApiHelper.IsPointWithinBounds(longitude, latitude))
    {
        switch (pns.ToLower())
        {
            case "wns":
                //// Windows 8.1 / Windows Phone 8.1
                var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                // Windows 10 specific Action Center support
                toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                break;
        }
    }

Desta forma, a notificação só é enviada quando o ponto de está dentro dos limites.

##<a name="testing-push-notifications-in-the-uwp-app"></a>Testar as notificações push na aplicação UWP

Regressar à aplicação UWP, vamos agora deverá conseguir testar as notificações. Dentro de `LocationHelper` classe, criar uma nova função – `SendLocationToBackend`:

    public static async Task SendLocationToBackend(string pns, string userTag, string message, string latitude, string longitude)
    {
        var POST_URL = "http://localhost:8741/api/notifications?pns=" +
            pns + "&to_tag=" + userTag + "&latitude=" + latitude + "&longitude=" + longitude;

        using (var httpClient = new HttpClient())
        {
            try
            {
                await httpClient.PostAsync(POST_URL, new StringContent("\"" + message + "\"",
                    System.Text.Encoding.UTF8, "application/json"));
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message);
            }
        }
    }

>[AZURE.NOTE] Trocar o `POST_URL` para a localização da sua aplicação web implementado que criámos na secção anterior. Por agora, é executá-la localmente mas, à medida que trabalha no implementar uma versão pública, terá de alojá-lo com um fornecedor externo.

Vamos agora Certifique-se de que estamos registe-se a aplicação UWP para as notificações push. No Visual Studio, clique no **projeto** > **armazenar** > **associar a aplicação na loja**.

![](./media/notification-hubs-geofence/vs-associate-with-store.png)

Assim que iniciar sessão sua conta de programador, certifique-se de que selecione uma aplicação existente ou crie um novo e associar o pacote ao mesmo. 

Aceda ao centro do Dev Center e abra a aplicação que acabou de criar. Clique em **Serviços** > **Notificações de emissão** > **Serviços Live site**.

![](./media/notification-hubs-geofence/ms-live-services.png)

No site, tome nota do **Segredo de aplicação** e o **Pacote SID**. Terá de tanto no Portal do Azure – abra o seu centro de notificação, clique em **Definições** > **Serviços de notificação** > **Windows (WNS)** e introduza as informações nos campos obrigatórios.

![](./media/notification-hubs-geofence/notification-hubs-wns.png)

Clique em **Guardar**.

Clique com o botão direito do rato em **referências** no **Explorador de solução** e selecione **Gerir pacotes de NuGet**. Precisamos adicionar uma referência para a **Microsoft Azure Service Bus gerido biblioteca** – basta procurar `WindowsAzure.Messaging.Managed` e adicioná-lo ao projeto.

![](./media/notification-hubs-geofence/vs-nuget.png)

Para fins de teste, podemos criar a `MainPage_Loaded` mais uma vez, processador de eventos e adicionar-lhe esta fragmento de código:

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    var hub = new NotificationHub("HUB_NAME", "HUB_LISTEN_CONNECTION_STRING");
    var result = await hub.RegisterNativeAsync(channel.Uri);

    // Displays the registration ID so you know it was successful
    if (result.RegistrationId != null)
    {
        Debug.WriteLine("Reg successful.");
    }

O que precede regista a aplicação com o concentrador de notificação. Está pronto para ir! 

No `LocationHelper`, inside a `Geolocator_PositionChanged` processador, pode adicionar uma peça de código de teste que forçada irá colocar a localização no interior de geofence:

    await LocationHelper.SendLocationToBackend("wns", "TEST_USER", "TEST", "37.7746", "-122.3858");

Porque não está a passar as coordenadas reais (que poderão não estar dentro dos limites no momento) e estiver a utilizar o teste predefinida valores, vemos uma notificação que aparecem no atualização:

![](./media/notification-hubs-geofence/notification-hubs-test-notification.png)

##<a name="whats-next"></a>O que se segue?

Existem apenas alguns passos que poderá ter de seguir para além de acima para se certificar de que a solução é pronto para produção.

Em primeiro lugar e mais, poderá ter assegurar que geofences são dinâmicos. Isto irão necessitar algum do trabalho extra com a API do Bing para poder carregar novos limites de dentro da origem de dados existente. Consulte a [documentação do Bing espacial API de serviços de dados](https://msdn.microsoft.com/library/ff701734.aspx) para obter mais detalhes sobre o assunto.

Em segundo lugar, tal como estiver a trabalhar para se certificar de que a entrega está concluída aos participantes da direita, poderá pretender alvo-los através [de marcação](notification-hubs-tags-segment-push-message.md).

A solução mostrada acima descreve um cenário em que poderá ter uma grande variedade de plataformas de destino, para que não podemos limitar geofencing para funcionalidades específicas do sistema. Assim sendo, a plataforma Windows Universal oferece melhoradas para [detetar geofences direita fora-de-de-box](https://msdn.microsoft.com/windows/uwp/maps-and-location/set-up-a-geofence).

Para obter mais detalhes sobre as capacidades de notificação concentradores, consulte o artigo nosso [portal de documentação](https://azure.microsoft.com/documentation/services/notification-hubs/).
