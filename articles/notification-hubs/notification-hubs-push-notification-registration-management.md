<properties
    pageTitle="Gestão de registo"
    description="Este tópico explica como registar dispositivos com concentradores de notificação para receberem notificações push."
    services="notification-hubs"
    documentationCenter=".net"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="registration-management"></a>Gestão de registo

##<a name="overview"></a>Descrição geral

Este tópico explica como registar dispositivos com concentradores de notificação para receberem notificações push. O tópico descreve os registos de alto nível, em seguida, introduz os duas padrões principais para o registo de dispositivos: registar a partir do dispositivo diretamente para o concentrador de notificação e registar o através de um aplicação do back-end. 


##<a name="what-is-device-registration"></a>O que é o registo do dispositivo

Registo do dispositivo com um concentrador de notificação é realizado através de um **registo** ou a **instalação**.

#### <a name="registrations"></a>Registos
Um registo associa a alça de serviço de notificação de plataforma (PNS) para um dispositivo com etiquetas e possivelmente um modelo. A alça de PNS poderá ser uma ChannelURI, token de dispositivo ou id de registo GCM. Etiquetas são utilizadas para encaminhar notificações para o conjunto de dispositivo alças correto. Para mais informações, consulte o artigo [Encaminhamento e expressões de etiqueta](notification-hubs-tags-segment-push-message.md). Modelos são utilizados para implementar o transformação por registo. Para mais informações, consulte o artigo [modelos](notification-hubs-templates-cross-platform-push-messages.md).

#### <a name="installations"></a>Instalações
Uma instalação é um melhorado registo que inclua um saco de push relacionadas com propriedades. É a abordagem melhor e mais recente para registar os seus dispositivos. No entanto, não é suportado ao lado do cliente .NET SDK ([SDK do concentrador de notificação para operações de back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)) partir ainda.  Isto significa que se está a registar a partir do dispositivo cliente propriamente dito, teria utilize a abordagem de [Notificação concentradores REST API](https://msdn.microsoft.com/library/mt621153.aspx) para suportar instalações. Se estiver a utilizar um serviço de back-end, deverá conseguir utilizar o [SDK do concentrador de notificação para operações de back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Seguem-se algumas vantagens chaves para utilizar as instalações:

* Criar ou atualizar uma instalação totalmente é idempotent. Por isso, pode repetir-sem qualquer preocupações sobre registos duplicados.
* O modelo de instalação torna mais fácil fazer puxa individuais - filtragem de dispositivo específico. Uma etiqueta de sistema **"$InstallationId: [installationId]"** é adicionado automaticamente com cada registo de instalação com base. Por isso, pode ligar a um enviar para esta etiqueta para um dispositivo específico de destino sem ter de fazer a codificação adicionais.
* Utilizar as instalações também permite-lhe fazer parcial registration atualiza-se. A atualização parcial de uma instalação é pedida com um método de PATCHES utilizando o [padrão de patches JSON](https://tools.ietf.org/html/rfc6902). Este é particularmente útil quando pretende atualizar etiquetas relativo ao registo. Não tem de pendente do registo de todo e, em seguida, reenviar novamente todas as etiquetas anterior.

Uma instalação pode conter as seguintes propriedades. Para uma lista completa da instalação propriedades, consulte [criar ou substituir uma instalação com a REST API](https://msdn.microsoft.com/library/azure/mt621153.aspx) ou [Propriedades de instalação](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.installation_properties.aspx) para o.

    // Example installation format to show some supported properties
    {
        installationId: "",
        expirationTime: "",
        tags: [],
        platform: "",
        pushChannel: "",
        ………
        templates: {
            "templateName1" : {
                body: "",
                tags: [] },
            "templateName2" : {
                body: "",
                // Headers are for Windows Store only
                headers: {
                    "X-WNS-Type": "wns/tile" }
                tags: [] }
        },
        secondaryTiles: {
            "tileId1": {
                pushChannel: "",
                tags: [],
                templates: {
                    "otherTemplate": {
                        bodyTemplate: "",
                        headers: {
                            ... }
                        tags: [] }
                }
            }
        }
    }

 

É importante ter em atenção que instalações por predefinição e registos já não a expirar.

Registos e instalações tem de conter um identificador PNS válido para cada dispositivo/canal. Uma vez que apenas podem ser obtidas PNS alças numa aplicação cliente no dispositivo, um padrão é registar diretamente no que o dispositivo com a aplicação de cliente. Por outro lado, lógica de negócio relacionado com etiquetas e considerações de segurança poderão implicar gerir o registo de dispositivo de aplicação back-end. 

#### <a name="templates"></a>Modelos

Se pretender utilizar [modelos](notification-hubs-templates-cross-platform-push-messages.md), a instalação do dispositivo também mantenha todos os modelos de associados com que o dispositivo num JSON formatar (consulte exemplo acima). Os nomes de modelo ajudam diferentes modelos de destino para o mesmo dispositivo.

Note que cada nome de modelo de mapas para corpo de um modelo e um conjunto de etiquetas opcional. Além disso, cada plataforma pode conter propriedades do modelo adicionais. Para o da loja Windows (utilizando WNS) e Windows Phone 8 (utilizando MPNS), um conjunto adicional de cabeçalhos pode fazer parte do modelo. No caso de APNs, pode definir uma propriedade de termo para uma constante ou para uma expressão de modelo. Para uma lista completa da instalação propriedades, consulte [criar ou substituir uma instalação com resto](https://msdn.microsoft.com/library/azure/mt621153.aspx) tópico.

#### <a name="secondary-tiles-for-windows-store-apps"></a>Mosaicos secundários para aplicações da loja Windows

Para as aplicações de cliente da loja Windows, enviar notificações para mosaicos secundários é igual a enviar-lhes para principal. Também é suportada em instalações. Tenha em atenção que mosaicos secundários têm um ChannelUri diferente, o qual ao SDK na sua aplicação de cliente processa transparente.

O dicionário de SecondaryTiles utiliza a mesma TileId que é utilizado para criar o objeto SecondaryTiles na sua aplicação da loja Windows.
Tal como acontece com a ChannelUri principal, pode alterar ChannelUris dos mosaicos secundários em qualquer momento. Para manter as instalações no centro do notificação atualizado, o dispositivo tem de actualizá-los com o atual ChannelUris dos mosaicos secundários.


##<a name="registration-management-from-the-device"></a>Gestão de registo do dispositivo

Quando a gerir o registo de dispositivo a partir das aplicações de cliente, o back-end apenas é responsável por envio de notificações. Aplicações de cliente manter PNS alças atualizados e registar etiquetas. A imagem seguinte ilustra este padrão.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-device.png)

O dispositivo pela primeira vez obtém a alça de PNS a partir do PNS, em seguida, regista diretamente com o concentrador de notificação. Depois do registo é efetuada com êxito, o aplicação back-end pode enviar uma notificação de filtragem desse registo. Para obter mais informações sobre como enviar notificações, consulte o artigo [Encaminhamento e expressões de etiqueta](notification-hubs-tags-segment-push-message.md).
Tenha em atenção que neste caso, que irá utilizar escutar apenas direitos para aceder à sua concentradores de notificação do dispositivo. Para mais informações, consulte o artigo [segurança](notification-hubs-push-notification-security.md).

Registar o dispositivo é o método mais simples, mas tem algumas desvantagens.
A primeira desvantagem é que as respectivas tags só pode atualizar uma aplicação de cliente quando a aplicação está ativa. Por exemplo, se um utilizador tem dois dispositivos registar etiquetas relacionados com a equipas de desporto, quando regista o primeiro dispositivo para uma tag adicional (por exemplo, Seahawks), o segundo dispositivo não receberá notificações sobre o Seahawks até que a aplicação no segundo dispositivo é executada uma segunda vez. Forma mais geral, quando etiquetas são afetadas por vários dispositivos, gestão de etiquetas de back-end é uma opção conveniente.
A segunda desvantagem de gestão do registo a partir da aplicação de cliente é que, uma vez que podem ser atacadas aplicações, proteger o registo a etiquetas específicas requer cuidados adicionais, tal como é explicado na secção "segurança de nível de etiqueta."



#### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-an-installation"></a>Exemplo de código para registar com um concentrador de notificação a partir de um dispositivo com uma instalação 

Neste momento, isto é suportado apenas utilizando a [Notificação concentradores REST API](https://msdn.microsoft.com/library/mt621153.aspx).

Também pode utilizar o método de PATCHES utilizando o [padrão de patches JSON](https://tools.ietf.org/html/rfc6902) para atualizar a instalação.

    class DeviceInstallation
    {
        public string installationId { get; set; }
        public string platform { get; set; }
        public string pushChannel { get; set; }
        public string[] tags { get; set; }
    }

    private async Task<HttpStatusCode> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation,
         string hubName, string listenConnectionString)
    {
        if (deviceInstallation.installationId == null)
            return HttpStatusCode.BadRequest;

        // Parse connection string (https://msdn.microsoft.com/library/azure/dn495627.aspx)
        ConnectionStringUtility connectionSaSUtil = new ConnectionStringUtility(listenConnectionString);
        string hubResource = "installations/" + deviceInstallation.installationId + "?";
        string apiVersion = "api-version=2015-04";

        // Determine the targetUri that we will sign
        string uri = connectionSaSUtil.Endpoint + hubName + "/" + hubResource + apiVersion;

        //=== Generate SaS Security Token for Authorization header ===
        // See, https://msdn.microsoft.com/library/azure/dn495627.aspx
        string SasToken = connectionSaSUtil.getSaSToken(uri, 60);

        using (var httpClient = new HttpClient())
        {
            string json = JsonConvert.SerializeObject(deviceInstallation);

            httpClient.DefaultRequestHeaders.Add("Authorization", SasToken);

            var response = await httpClient.PutAsync(uri, new StringContent(json, System.Text.Encoding.UTF8, "application/json"));
            return response.StatusCode;
        }
    }

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    string installationId = null;
    var settings = ApplicationData.Current.LocalSettings.Values;

    // If we have not stored a installation id in application data, create and store as application data.
    if (!settings.ContainsKey("__NHInstallationId"))
    {
        installationId = Guid.NewGuid().ToString();
        settings.Add("__NHInstallationId", installationId);
    }

    installationId = (string)settings["__NHInstallationId"];

    var deviceInstallation = new DeviceInstallation
    {
        installationId = installationId,
        platform = "wns",
        pushChannel = channel.Uri,
        //tags = tags.ToArray<string>()
    };

    var statusCode = await CreateOrUpdateInstallationAsync(deviceInstallation, 
                        "<HUBNAME>", "<SHARED LISTEN CONNECTION STRING>");

    if (statusCode != HttpStatusCode.Accepted)
    {
        var dialog = new MessageDialog(statusCode.ToString(), "Registration failed. Installation Id : " + installationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    else
    {
        var dialog = new MessageDialog("Registration successful using installation Id : " + installationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }

   

#### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration"></a>Exemplo de código para registar com um concentrador de notificação a partir de um dispositivo com um registo


Estes métodos de criar ou actualizar um registo para o dispositivo no qual são chamados. Isto significa que para atualizar a alça de ou as etiquetas, tem de substituir o registo de todo. Lembre-se de que os registos são a breves, por isso devem ter sempre numa loja fiável com os códigos atuais que necessita de um dispositivo específico.


    // Initialize the Notification Hub
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

    // The Device id from the PNS
    var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    // If you are registering from the client itself, then store this registration id in device
    // storage. Then when the app starts, you can check if a registration id already exists or not before
    // creating.
    var settings = ApplicationData.Current.LocalSettings.Values;

    // If we have not stored a registration id in application data, store in application data.
    if (!settings.ContainsKey("__NHRegistrationId"))
    {
        // make sure there are no existing registrations for this push handle (used for iOS and Android)    
        string newRegistrationId = null;
        var registrations = await hub.GetRegistrationsByChannelAsync(pushChannel.Uri, 100);
        foreach (RegistrationDescription registration in registrations)
        {
            if (newRegistrationId == null)
            {
                newRegistrationId = registration.RegistrationId;
            }
            else
            {
                await hub.DeleteRegistrationAsync(registration);
            }
        }

        newRegistrationId = await hub.CreateRegistrationIdAsync();

        settings.Add("__NHRegistrationId", newRegistrationId);
    }
     
    string regId = (string)settings["__NHRegistrationId"];

    RegistrationDescription registration = new WindowsRegistrationDescription(pushChannel.Uri);
    registration.RegistrationId = regId;
    registration.Tags = new HashSet<string>(YourTags);

    try
    {
        await hub.CreateOrUpdateRegistrationAsync(registration);
    }
    catch (Microsoft.WindowsAzure.Messaging.RegistrationGoneException e)
    {
        settings.Remove("__NHRegistrationId");
    }


## <a name="registration-management-from-a-backend"></a>Gestão de registo de back-end

A gestão de registos de back-end requer escrever código adicional. A aplicação a partir do dispositivo tem de fornecer as PNS atualizadas alça para o back-end sempre que inicia a aplicação (juntamente com etiquetas e modelos) e o back-end tem atualizar esta alça do concentrador de notificação. A imagem seguinte ilustra este estrutura.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-backend.png)

As vantagens de gestão de registos de back-end incluem a capacidade para modificar etiquetas para os registos, mesmo quando a aplicação correspondente no dispositivo está inactiva e para autenticar a aplicação de cliente antes de adicionar uma etiqueta para o seu registo.


#### <a name="example-code-to-register-with-a-notification-hub-from-a-backend-using-an-installation"></a>Exemplo de código para registar com um concentrador de notificação de back-end utilizando uma instalação

O dispositivo de cliente ainda obtém respectiva alça PNS e propriedades de instalação pertinentes como antes e chamadas de uma API personalizada no back-end que pode executar o registo e autorizar etiquetas, etc. O back-end pode tirar partido o [SDK do concentrador de notificação para operações de back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Também pode utilizar o método de PATCHES utilizando o [padrão de patches JSON](https://tools.ietf.org/html/rfc6902) para atualizar a instalação.
 

    // Initialize the Notification Hub
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

    // Custom API on the backend
    public async Task<HttpResponseMessage> Put(DeviceInstallation deviceUpdate)
    {

        Installation installation = new Installation();
        installation.InstallationId = deviceUpdate.InstallationId;
        installation.PushChannel = deviceUpdate.Handle;
        installation.Tags = deviceUpdate.Tags;

        switch (deviceUpdate.Platform)
        {
            case "mpns":
                installation.Platform = NotificationPlatform.Mpns;
                break;
            case "wns":
                installation.Platform = NotificationPlatform.Wns;
                break;
            case "apns":
                installation.Platform = NotificationPlatform.Apns;
                break;
            case "gcm":
                installation.Platform = NotificationPlatform.Gcm;
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }


        // In the backend we can control if a user is allowed to add tags
        //installation.Tags = new List<string>(deviceUpdate.Tags);
        //installation.Tags.Add("username:" + username);

        await hub.CreateOrUpdateInstallationAsync(installation);

        return Request.CreateResponse(HttpStatusCode.OK);
    }


#### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration-id"></a>Exemplo de código para registar com um concentrador de notificação a partir de um dispositivo com um id de registo

A partir do seu back-end da aplicação, pode executar operações de CRUDS básicas em registos. Por exemplo:

    var hub = NotificationHubClient.CreateClientFromConnectionString("{connectionString}", "hubName");
            
    // create a registration description object of the correct type, e.g.
    var reg = new WindowsRegistrationDescription(channelUri, tags);

    // Create
    await hub.CreateRegistrationAsync(reg);

    // Get by id
    var r = await hub.GetRegistrationAsync<RegistrationDescription>("id");

    // update
    r.Tags.Add("myTag");

    // update on hub
    await hub.UpdateRegistrationAsync(r);

    // delete
    await hub.DeleteRegistrationAsync(r);


O back-end deve processar simultaneidade entre registration atualiza-se. Serviço Bus oferece controlo de simultaneidade optimista para a gestão de registo. Ao nível HTTP, isto é implementado com a utilização de ETag no operações de gestão de registo. Esta funcionalidade transparente é utilizada pelo Microsoft SDKs, que gerar uma exceção se uma atualização for rejeitada por razões de simultaneidade. O aplicação back-end é responsável por processamento estes exceções e a repetir a atualização, se necessário.