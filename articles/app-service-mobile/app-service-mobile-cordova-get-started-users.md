<properties
    pageTitle="Adicionar autenticação em Apache Cordova aplicações Mobile | Azure de aplicação de serviço"
    description="Saiba como utilizar aplicações móveis no serviço de aplicação do Azure para autenticar os utilizadores da sua aplicação Apache Cordova através de uma variedade de fornecedores de identidade, incluindo Google, Facebook, Twitter e Microsoft."
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-html"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="add-authentication-to-your-apache-cordova-app"></a>Adicionar autenticação para a sua aplicação Apache Cordova

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]
    
## <a name="summary"></a>Resumo

Neste tutorial, adicione autenticação para o projeto de guia de introdução de fazer no Cordova Apache utilizando um fornecedor de identidades suportados. Neste tutorial é baseado no tutorial [começar a trabalhar com aplicações Mobile] , que tem de concluir primeiro.

##<a name="register"></a>Registe-se a sua aplicação para a autenticação e configurar o serviço de aplicação

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

[Veja um vídeo que mostra os passos semelhantes](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-8-Azure-authentication)

##<a name="permissions"></a>Restringir permissões a utilizadores autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Agora, pode verificar que tenha sido desativado acesso anónimo ao seu back-end. No Visual Studio, abra o projeto que criou quando se tiver concluído o tutorial [começar a trabalhar com aplicações Mobile], em seguida, executar a sua aplicação no **Google Android emulador** e verifique se uma falha de ligação inesperada é apresentada após a aplicação é iniciada.

Em seguida, irá atualizar a aplicação para autenticar os utilizadores antes de pedir recursos a partir da aplicação Mobile back-end.

##<a name="add-authentication"></a>Adicionar a autenticação à aplicação

1. Abra o projeto no **Visual Studio**, em seguida, abra o `www/index.html` ficheiro para edição.

2. Localize o `Content-Security-Policy` tag de seo na secção do cabeçalho.  Terá de adicionar o anfitrião OAuth à lista de origens de permitidos.

  	| Fornecedor               | Nome do fornecedor SDK | OAuth anfitrião                  |
  	| :--------------------- | :---------------- | :-------------------------- |
  	| Azure Active Directory | aad               | https://login.Windows.NET   |
  	| Facebook               | Facebook          | https://www.Facebook.com    |
  	| Google                 | Google            | https://Accounts.google.com |
  	| Microsoft              | MicrosoftAccount  | https://login.Live.com      |
  	| Twitter                | twitter           | https://API.twitter.com     |

    Um exemplo de política de segurança de conteúdo (implementado para o Azure Active Directory) é da seguinte forma:

        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
            data: gap: https://login.windows.net https://yourapp.azurewebsites.net; style-src 'self'">

    Deve substituir `https://login.windows.net` com o anfitrião o OAuth a partir da tabela acima.  Consulte a [documentação da política de segurança de conteúdo] para mais informações sobre este tag de seo.

    Tenha em atenção que alguns fornecedores de autenticação não necessitam de que política de segurança de conteúdo é alterado quando utilizado em dispositivos móveis adequados.  Por exemplo, sem alterações de política de segurança de conteúdo são necessárias para utilizando a autenticação do Google num dispositivo Android.

3. Abrir o `www/js/index.js` para edição de ficheiros, localize o `onDeviceReady()` método, e na criação de cliente código adicione o seguinte:

        // Login to the service
        client.login('SDK_Provider_Name')
            .then(function () {

                // BEGINNING OF ORIGINAL CODE

                // Create a table reference
                todoItemTable = client.getTable('todoitem');

                // Refresh the todoItems
                refreshDisplay();

                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);

                // END OF ORIGINAL CODE

            }, handleError);

    Nota a que este código substituir o código existente que cria a referência de tabela e atualiza a IU.

    O método de login() inicia autenticação com o fornecedor. O método login() é uma função de assíncrona devolve promessa JavaScript.  Os restantes a inicialização são colocado dentro da resposta promessa para que não é executada até que o método de login() esteja concluída.

4. No código que acabou de adicionar, substituir `SDK_Provider_Name` com o nome do seu fornecedor de início de sessão. Por exemplo, para o Azure Active Directory, utilize `client.login('aad')`.

4. Execute o seu projeto.  Quando o projeto tiver terminado a inicialização, a aplicação irá apresentar a página de início de sessão OAuth para o fornecedor de autenticação que selecionou.

##<a name="next-steps"></a>Próximos passos

* Saiba mais [Sobre a autenticação] com a aplicação de serviço de Azure.
* Continue a iniciação adicionando [As notificações de emissão] para a sua aplicação Apache Cordova.

Saiba como utilizar os SDK.

* [Apache Cordova SDK]
* [Servidor de ASP.NET SDK]
* [Servidor de node.js SDK]

<!-- URLs. -->
[Começar a trabalhar com aplicações Mobile]: app-service-mobile-cordova-get-started.md
[Documentação da política de segurança de conteúdo]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[Notificações de emissão]: app-service-mobile-cordova-get-started-push.md
[Sobre a autenticação]: app-service-mobile-auth.md
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md 
[Servidor de ASP.NET SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Servidor de node.js SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
