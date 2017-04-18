<properties
    pageTitle="Como utilizar o plug-in do Apache Cordova para aplicações móveis Azure"
    description="Como utilizar o plug-in do Apache Cordova para aplicações móveis Azure"
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-html"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="how-to-use-apache-cordova-client-library-for-azure-mobile-apps"></a>Como utilizar a biblioteca do cliente de Cordova Apache para aplicações móveis Azure

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Este guia ensina-lhe executar cenários comuns utilizando o [Plug-in do Apache Cordova para as aplicações móveis do Azure]mais recente. Se estiver familiarizado com as aplicações móveis do Azure, primeiro concluída [Azure Mobile aplicações guia] para criar um back-end, criar uma tabela e transferir um projeto Apache Cordova pré-concebidos. Neste guia, iremos concentrar-no plug-in do lado do cliente Apache Cordova.

## <a name="supported-platforms"></a>Plataformas suportadas

Este SDK suporta Apache Cordova v6.0.0 e posterior no iOS, Android e Windows dispositivos.  O suporte técnico da plataforma é da seguinte forma:

* API Android 19-24 (KitKat através de Nougat)
* iOS 8.0 e versões posteriores.
* Windows Phone 8.0
* Windows Phone 8.1
* Plataforma universal Windows

##<a name="Setup"></a>Configuração e pré-requisitos

Este guia assume que criou back-end com uma tabela. Este guia assume que a tabela que tem o mesmo esquema como as tabelas nesses tutoriais. Este guia também assume que adicionou o plug-in do Apache Cordova ao seu código.  Se não o tiver feito, pode adicionar o plug-in Apache Cordova ao seu projeto na linha de comandos:

```
cordova plugin add cordova-plugin-ms-azure-mobile-apps
```

Para mais informações sobre como criar a [sua aplicação Apache Cordova primeira], consulte o artigo sua documentação.

[AZURE.INCLUDE [app-service-mobile-html-js-library.md](../../includes/app-service-mobile-html-js-library.md)]


##<a name="auth"></a>Como: autenticar os utilizadores

Suporta Azure aplicação de serviço de autenticação e autorização de utilizadores da aplicação utilizar vários fornecedores de identidades externas: Facebook, Google, Account Microsoft e Twitter. Pode definir permissões em tabelas para restringir o acesso operações específicas para apenas utilizadores autenticados. Também pode utilizar a identidade do utilizadores autenticados para implementar regras de autorização nos scripts do servidor. Para obter mais informações, consulte o tutorial [começar com autenticação] .

Quando utilizar a autenticação numa aplicação do Apache Cordova, tem de estar disponíveis os plug-ins do Cordova seguintes:

* [dispositivo de plug-in cordova]
* [Plug-in-cordova-inappbrowser]

Dois fluxos de autenticação suportados: um fluxo de servidor e um fluxo de cliente.  O fluxo de servidor fornece a experiência de autenticação mais simples, como baseia-se na interface de autenticação de web do fornecedor. O fluxo de cliente permite a integração mais aprofundada com capacidades específicas de dispositivo tais como single-sign-on como baseia-se no específicas do fornecedor específicas do dispositivo SDK.

[AZURE.INCLUDE [app-service-mobile-html-js-auth-library.md](../../includes/app-service-mobile-html-js-auth-library.md)]

###<a name="configure-external-redirect-urls"></a>Como: configurar o serviço de aplicação móvel para redirecionar externos URLs.

Vários tipos de aplicações Apache Cordova utilizam uma funcionalidade de loopback para processar OAuth IU monetários.  Fluxos de IU OAuth no localhost causam problemas de uma vez que o serviço de autenticação apenas sabe como utilizar o seu serviço por predefinição.  Exemplos de fluxos monetários OAuth IU problemáticos incluem:

- Emulador ondulação.
- Live recarregar com iónicos.
- O back-end móvel a ser executado localmente
- A executar o back-end móvel num serviço de aplicação Azure diferente que a autenticação de fornecer um.

Siga estas instruções para adicionar as definições de locais para a configuração:

1. Inicie a sessão [portal do Azure]
2. Selecione **todos os recursos** ou **Serviços de aplicação** , em seguida, clique no nome da sua aplicação móvel.
3. Clique em **Ferramentas**
4. Clique em **Explorador de recursos** no menu de observar, em seguida, clique em **Ir**.  Abre um separador ou janela de nova.
5. Expanda a **configuração**, **authsettings** nós para o seu site na navegação à esquerda.
6. Clique em **Editar**
7. Procure o elemento "allowedExternalRedirectUrls".  Poderá ser definido como nulo ou como uma matriz de valores.  Altere o valor para o valor que se segue:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Substitua os URLs os URLs do seu serviço.  Alguns exemplos incluem "http://localhost:3000" (para o serviço de exemplo Node.js) ou "http://localhost:4400" (para o serviço de ondulação).  Contudo, estes URLs são exemplos - sua situação, incluindo para os serviços mencionados nos exemplos, podem ser diferentes.
8. Clique no botão de **Leitura/escrita** no canto superior direito do ecrã.
9. Clique na verde botão **colocar** .

As definições são guardadas neste momento.  Não fechar a janela do browser até terminaram das definições de guardar.
Também adicione estes URLs loopback definições CORS de aplicação de serviço:

1. Inicie a sessão [portal do Azure]
2. Selecione **todos os recursos** ou **Serviços de aplicação** , em seguida, clique no nome da sua aplicação móvel.
3. O pá definições é apresentado automaticamente.  Se não tiver, clique em **Todas as definições**.
4. Clique em **CORS** no menu de API.
5. Introduza o URL que pretende adicionar na caixa fornecida e prima Enter.
6. Introduza o URL adicionais, conforme necessário.
7. Clique em **Guardar** para guardar as definições.

Demora cerca de 10 a 15 segundos para as novas definições, entre em vigor.

##<a name="register-for-push"></a>Como: registe-se para as notificações Push

Instale o [Plug-in-phonegap-push] para processar as notificações push.  Este plug-in pode ser adicionado de facilmente a utilizar o `cordova plugin add` comando na linha de comandos ou através do programa de instalação do plug-in Git do Visual Studio.  O código seguinte na sua aplicação Apache Cordova regista o seu dispositivo, para as notificações push:

```
var pushOptions = {
    android: {
        senderId: '<from-gcm-console>'
    },
    ios: {
        alert: true,
        badge: true,
        sound: true
    },
    windows: {
    }
};
pushHandler = PushNotification.init(pushOptions);

pushHandler.on('registration', function (data) {
    registrationId = data.registrationId;
    // For cross-platform, you can use the device plugin to determine the device
    // Best is to use device.platform
    var name = 'gcm'; // For android - default
    if (device.platform.toLowerCase() === 'ios')
        name = 'apns';
    if (device.platform.toLowerCase().substring(0, 3) === 'win')
        name = 'wns';
    client.push.register(name, registrationId);
});

pushHandler.on('notification', function (data) {
    // data is an object and is whatever is sent by the PNS - check the format
    // for your particular PNS
});

pushHandler.on('error', function (error) {
    // Handle errors
});
```

Utilize o SDK concentradores de notificação para enviar notificações push do servidor.  Nunca envie notificações push diretamente a partir dos clientes. Fazê-lo pode ser utilizado para acionar uma negação de ataque service contra concentradores de notificação ou o PNS.  O PNS poderia proibição o tráfego da sua estatístico como resultado desses ataques.

<!-- URLs. -->
[Portal do Azure]: https://portal.azure.com
[Início do Azure rápida de aplicações móveis]: app-service-mobile-cordova-get-started.md
[Começar a trabalhar com autenticação]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Plug-in do Apache Cordova para aplicações móveis Azure]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[a aplicação Apache Cordova primeira]: http://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[phonegap-Plug-in de emissão]: https://www.npmjs.com/package/phonegap-plugin-push
[dispositivo de plug-in cordova]: https://www.npmjs.com/package/cordova-plugin-device
[Plug-in-cordova-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query object documentation]: https://msdn.microsoft.com/en-us/library/azure/jj613353.aspx
