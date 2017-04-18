<properties
    pageTitle="Azure AD Cordova introdução | Microsoft Azure"
    description="Como criar uma aplicação de Cordova que integra-se com o Azure AD para iniciar sessão e chamadas Azure AD protegido APIs utilizando OAuth."
    services="active-directory"
    documentationCenter=""
    authors="vibronet"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="vittorib"/>

# <a name="integrate-azure-ad-with-an-apache-cordova-app"></a>Integrar o Azure AD com um Apache Cordova aplicação

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Apache Cordova torna possíveis para desenvolver aplicações HTML5/JavaScript que podem ser executados em dispositivos móveis como desenvolvidos aplicações nativas.
Com o Azure AD, pode adicionar capacidades de autenticação de classe de empresa para as suas aplicações Cordova. Graças a um plug-in de Cordova moldagem SDK nativo do Azure AD IOS, Android, o da loja Windows e o Windows Phone, pode melhorar a sua aplicação para suportar o início de sessão com contas de AD dos seus utilizadores, aceder ao Office 365 e API do Azure e até mesmo proteger chamadas para o seu próprio personalizado a API do Web.

Neste tutorial vai utilizamos o plug-in Apache Cordova para biblioteca de autenticação de diretório ativo (ADAL) para melhorar uma aplicação simple com as seguintes funcionalidades:

-   Com apenas alguns linhas de código, autenticar um utilizador do AD e obter um token para chamar a API do Azure AD Graph.
-   Utilizar esse token para invocar a API do gráfico para esse directório de consulta e apresentar os resultados  
-   Tirar partido a cache de tokens ADAL para minimizar os pedidos de autenticação para o utilizador.

Para poder fazer isto, terá:

2. Registar uma aplicação do Azure AD
2. Adicionar código para a sua aplicação aos tokens de pedido
3. Adicionar código a utilizar o token para consultar a API do gráfico e apresentar os resultados.
4. Criar o projeto de implementação Cordova com todas as plataformas que alvo pretende e o plug-in Cordova ADAL e teste a solução no emuladores.

## <a name="0--prerequisites"></a>*0. Pré-requisitos de*

Para concluir este tutorial, que é necessário:

- Um inquilino do Azure AD onde tem uma conta com direitos de desenvolvimento de aplicação
- Um ambiente de desenvolvimento configurado para utilizar Apache Cordova  

Se ambos já tiver configurado, prossiga diretamente para o passo 1.

Se não tiver um inquilino do Azure AD, pode encontrar [instruções sobre como obter uma aqui](active-directory-howto-tenant.md).

Se não tiver Cordova Apache configurar no seu computador, instale o seguinte procedimento:

- [Git](http://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
- [NodeJS](https://nodejs.org/download/)
- [Clip Cordova](https://cordova.apache.org/) (pode ser instalado facilmente através do Gestor de pacotes NPM: `npm install -g cordova`)

Tenha em atenção que aqueles deverão funcionar no PC e no Mac.

Cada plataforma de destino tem pré-requisitos diferentes.

- Para criar e executar a versão de aplicação de telefone ou Tablet/PC com Windows
    - [Visual Studio 2013 para Windows com o Update 2 ou posterior](http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-windows-8) (Express ou outra versão).
- Para criar e executar para iOS
    -   Xcode 5. x ou superior. Transfira-o na http://developer.apple.com/downloads ou o [Mac App Store](http://itunes.apple.com/us/app/xcode/id497799835?mt=12)
    -   [ios sim](https://www.npmjs.org/package/ios-sim) – permite-lhe iniciação iOS aplicações para o iOS Simulator da linha de comandos (pode ser instalado facilmente através do terminal: `npm install -g ios-sim`)

- Para criar e executar a aplicação para Android
    - Instalar [Java Development Kit (JDK) 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) ou posterior. Certifique-se `JAVA_HOME` (variável de ambiente) é definida corretamente como de acordo com o caminho de instalação de JDK (por exemplo c:\Programas\Microsoft Files\Java\jdk1.7.0_75).
    - Instalar o [Android SDK](http://developer.android.com/sdk/installing/index.html?pkg=tools) e adicionar `<android-sdk-location>\tools` localização (por exemplo, C:\tools\Android\android-sdk\tools) para sua `PATH` variável de ambiente.
    - Abra o Gestor de SDK Android (por exemplo, através de terminal: `android`) e instalar
    - *Android 5.0.1 (API 21)* plataforma SDK
    - *Ferramentas de criação de android SDK* versão 19.1.0 ou superior
    - *Repositório de suporte Android* (Extras)

  Android sdk não fornece qualquer instância de emulador predefinida. Criar um novo ao executar `android avd` a partir do terminal e, em seguida, selecionar *Criar …* se pretender executar a aplicação para Android no emulador. Recomendado *Api nível* é 19 ou superior, consulte o artigo [AVD Manager] (http://developer.android.com/tools/help/avd-manager.html) para obter mais informações sobre as opções de emulador e criação Android.


## <a name="1--register-an-application-with-azure-ad"></a>*1. registar uma aplicação do Azure AD*

Nota: este __passo é opcional__. O tutorial fornecido previamente aprovisionadas valores que irão permitir-lhe ver a amostra em ação sem ter de fazer qualquer aprovisionamento no seu próprio inquilino. No entanto recomenda-se que execute este passo e familiarizar-se com o processo, tal como a ser necessário quando irá criar o seus próprio aplicações.

Azure AD irão apenas emitir tokens às aplicações conhecidas. Antes de poder utilizar Azure AD da sua aplicação, tem de criar uma entrada para a mesma no seu inquilino.  Para registar a uma nova aplicação no seu inquilino

-   Inicie sessão no [Portal de gestão do Azure](https://manage.windowsazure.com)
-   No painel de navegação esquerdo, clique no **Active Directory**
-   Selecione o inquilino onde pretender registar a aplicação.
-   Clique no separador **aplicações** e clique em **Adicionar** no nível a parte inferior.
-   Siga os pedidos e criar uma nova **Aplicação nativa do cliente** (apesar do que Cordova aplicações estão HTML com base, estamos a criar aplicações de cliente nativo aqui por isso, `Native Client Application` opção deve estar selecionada; caso contrário, a aplicação não irá funcionar).
    -   O **nome** da aplicação irá descrevem a sua aplicação para utilizadores finais
    -   O **Redirecionar URI** é o URI utilizado para devolver tokens para a sua aplicação. Introduza `http://MyDirectorySearcherApp`.

Quando terminar de registo, AAD vai atribuir a aplicação um identificador exclusivo do cliente.  Terá este valor nas secções seguintes: encontrará no separador **Configurar** da aplicação recentemente criado.

Para poder executar `DirSearchClient Sample`, conceder a permissão de aplicação recentemente criado a _API do Azure AD Graph_de consulta:
-   No separador **Configurar** , localize a secção "Permissões para outras aplicações".  Para a aplicação "Azure Active Directory", adicione a permissão de **acesso diretório como o utilizador tiver sessão iniciada** em **Permissões do delegado**.  Isto permitirá a sua aplicação consultar a API do gráfico para os utilizadores.

## <a name="2-clone-the-sample-app-repository-required-for-the-tutorial"></a>*2. clonar do repositório de aplicação de exemplo necessário para o tutorial*

A partir do seu shell ou linha de comandos, escreva o seguinte comando:

    git clone -b skeleton https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova.git

## <a name="3-create-the-cordova-app"></a>*3. criar a aplicação Cordova*

Existem várias formas de criar Cordova aplicações. Neste tutorial utilizamos a interface de linha de comandos Cordova (CLI).
A partir do seu shell ou linha de comandos, escreva o seguinte comando:


     cordova create DirSearchClient --copy-from="NativeClient-MultiTarget-Cordova/DirSearchClient"

Que irá criar a estrutura de pastas e andaimes para o projeto Cordova, copiar o conteúdo do projeto starter numa subpasta www.
Deslocar-se para a nova pasta de DirSearchClient.

    cd .\DirSearchClient

Adicione o lista branca Plug-in, necessário para chamar a API do gráfico.

     cordova plugin add cordova-plugin-whitelist

Em seguida, adicione todas as plataformas que pretende de suporte. Para poder tem uma amostra de trabalho, terá de executar pelo menos um dos comandos abaixo. Tenha em atenção que não conseguir emular iOS no Windows ou Windows/Windows Phone num Mac.

    cordova platform add android
    cordova platform add ios
    cordova platform add windows

Por fim, pode adicionar ADAL para plug-in de Cordova ao seu projeto.

    cordova plugin add cordova-plugin-ms-adal

## <a name="3-add-code-to-authenticate-users-and-obtain-tokens-from-aad"></a>*3. Adicionar código para autenticar os utilizadores e obter tokens de AAD*

A aplicação que está a desenvolver neste tutorial, irá fornecer uma funcionalidade de pesquisa osso bare diretório, onde o utilizador final pode escrever o alias do qualquer utilizador no diretório e visualizar alguns atributos básicos.  O projeto starter contém a definição da interface de utilizador básica da aplicação (www/index.html) e andaime que fios o evento de aplicação básica ciclos enlaces de interface de utilizador e de resultados de lógica de apresentação (em www/js/index.js). A única coisa que escreveu é adicionar lógica de tarefas de identidade de execução.

É a primeira coisa que precisa de fazer apresentar no seu código os valores de protocolo que são utilizados pelo AAD para identificar a sua aplicação e os recursos que de destino. Esses valores serão utilizados para construir os pedidos de tokens mais tarde. Inserir o fragmento de abaixo na parte superior do ficheiro index.js.

```javascript
    var authority = "https://login.windows.net/common",
    redirectUri = "http://MyDirectorySearcherApp",
    resourceUri = "https://graph.windows.net",
    clientId = "a5d92493-ae5a-4a9f-bcbf-9f1d354067d3",
    graphApiVersion = "2013-11-08";
```

O `redirectUri` e `clientId` valores devem corresponder aos valores que descreva a sua aplicação na AAD. Pode encontrar as partir do separador Configurar no portal do Azure, conforme descrito no passo 1 anteriormente no tutorial.
Nota: se optado por não se ter registado uma nova aplicação no seu próprio inquilino, pode simplesmente colar os valores pré-configurada acima tal como está - que lhe permita ver a execução de exemplo, apesar de deve sempre criar a sua própria entrada para as suas aplicações destinam de produção.

Em seguida, precisamos de adicionar o código de pedido de token real. Inserir o fragmento de seguinte entre o `search `e `renderdata `definições.

```javascript
    // Shows user authentication dialog if required.
    authenticate: function (authCompletedCallback) {

        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
            // Attempt to authorize user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials so triggers authentication dialog
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
        });

    },
```
Vamos examinar essa função por separá-la para baixo no respetivos duas partes principais.
Este exemplo foi concebido para funcionar com qualquer inquilino, por oposição a associados para uma determinada. Utiliza o "/ comuns" ponto final, que permite ao utilizador que introduza qualquer conta no momento da autenticação e encaminha o pedido para o inquilino pertence.
A primeira parte do método inspeciona a cache de ADAL para ver se já existe um token armazenado - e, se existir, utiliza os inquilinos-é proveniente de para voltar a iniciar ADAL. Isto é necessário para evitar pedidos adicionais, tal como a utilização de "/ comuns" sempre resulta no perguntar ao utilizador que introduza uma nova conta.
```javascript
        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
```
A segunda parte do método executa o pedido de tokewn inicial maiúscula.
O `acquireTokenSilentAsync` método lhe pede para ADAL para devolver um token para o recurso especificado sem mostrar a qualquer UX. Que pode ocorrer se a cache já tem um token de acesso adequado armazenado, ou se existir um token de atualização que pode ser utilizado para obter um novo token de acesso sem shwoing qualquer pedido.
Se que tentativa falhar, podemos reverter `acquireTokenAsync` -qual irá pedir visível ao utilizador para autenticar.
```javascript
            // Attempt to authorize user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials so triggers authentication dialog
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
```
Agora que temos o token, que possamos finalmente invocar a API do gráfico e executar a consulta de pesquisa que queremos. Inserir o fragmento de seguinte imediatamente abaixo de `authenticate` definição.

```javascript
// Makes Api call to receive user list.
    requestData: function (authResult, searchText) {
        var req = new XMLHttpRequest();
        var url = resourceUri + "/" + authResult.tenantId + "/users?api-version=" + graphApiVersion;
        url = searchText ? url + "&$filter=mailNickname eq '" + searchText + "'" : url + "&$top=10";

        req.open("GET", url, true);
        req.setRequestHeader('Authorization', 'Bearer ' + authResult.accessToken);

        req.onload = function(e) {
            if (e.target.status >= 200 && e.target.status < 300) {
                app.renderData(JSON.parse(e.target.response));
                return;
            }
            app.error('Data request failed: ' + e.target.response);
        };
        req.onerror = function(e) {
            app.error('Data request failed: ' + e.error);
        }

        req.send();
    },

```
Os ficheiros de ponto de partida fornecido um barebone UX para introduzir o alias de um utilizador numa caixa de texto. Este método utiliza esse valor para construir uma consulta, combiná-lo com o token de acesso, enviá-la para o gráfico e analisar os resultados. O método de renderData, já se encontra presente no ficheiro de ponto de partida, leva-o até cuidado para visualizar os resultados.

## <a name="4-run"></a>*4. executar*
A sua aplicação está finalmente pronta para ser executado! Operativo-é muito simple: assim que inicia a aplicação, introduza na caixa de texto o alias do utilizador que pretende procurar -, em seguida, clique no botão. Será pedido para autenticação. Relativamente a autenticação com êxito e pesquisa efetuada com êxito, serão apresentados os atributos do utilizador procurado. Execuções subsequentes irão efetuar a pesquisa sem mostrar a qualquer linha de comandos, graças à presença na cache do token previamente adquirido.
Os passos para executar a aplicação betão variam consoante a plataforma.

####<a name="windows-10"></a>Windows 10:

   Tablet/PC:`cordova run windows --archs=x64 -- --appx=uap`

   Telemóvel (requer o dispositivo móvel Windows10 ligado ao PC):`cordova run windows --archs=arm -- --appx=uap --phone`

   __Nota__: durante a primeira execução poderá ser-lhe iniciar sessão para uma licença de programador. Para obter mais detalhes, consulte [licença de programador](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx) .

####<a name="windows-81-tabletpc"></a>Tablet Windows 8.1/PC:

   `cordova run windows`

   __Nota__: durante a primeira execução poderá ser-lhe iniciar sessão para uma licença de programador. Para obter mais detalhes, consulte [licença de programador](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx) .

####<a name="windows-phone-81"></a>Windows Phone 8.1:

   Para executar no dispositivo ligado:`cordova run windows --device -- --phone`

   Para executar no emulador predefinido:`cordova emulate windows -- --phone`

   Utilizar `cordova run windows --list -- --phone` para ver todos os destinos disponíveis e `cordova run windows --target=<target_name> -- --phone` para executar a aplicação no dispositivo ou específico emulador (por exemplo, `cordova run windows --target="Emulator 8.1 720P 4.7 inch" -- --phone`).

####<a name="android"></a>Android:

   Para executar no dispositivo ligado:`cordova run android --device`

   Para executar no emulador predefinido:`cordova emulate android`

   __Nota__: Certifique-se de que criou instância emulador através do *Gestor de AVD* como-é mostrava na *Pré-requisitos da* secção.

   Utilizar `cordova run android --list` para ver todos os destinos disponíveis e `cordova run android --target=<target_name>` para executar a aplicação no dispositivo ou específico emulador (por exemplo, `cordova run android --target="Nexus4_emulator"`).

####<a name="ios"></a>iOS:

   Para executar no dispositivo ligado:`cordova run ios --device`

   Para executar no emulador predefinido:`cordova emulate ios`

   __Nota__: Certifique-se de que tem `ios-sim` pacote instalada para ser executada no emulador. Consulte a secção de *Pré-requisitos* para obter mais detalhes.

    Use `cordova run ios --list` to see all available targets and `cordova run ios --target=<target_name>` to run application on specific device or emulator (for example,  `cordova run android --target="iPhone-6"`).

Utilizar `cordova run --help` Consulte construir adicional e opções de executar.

Para sua referência, o exemplo concluída (sem os valores de configuração) é fornecido [aqui](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova/tree/complete/DirSearchClient).  Agora pode mover a mais avançados cenários (ok e mais interessantes).  Pretende experimentar:

[Proteger um Node.js da Web API com Azure AD >>](active-directory-devquickstarts-webapi-nodejs.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
