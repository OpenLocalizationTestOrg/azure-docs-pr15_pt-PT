<properties
    pageTitle="Como utilizar o SDK JavaScript para aplicações móveis Azure"
    description="Como utilizar v para as aplicações móveis do Azure"
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="html"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="how-to-use-the-javascript-client-library-for-azure-mobile-apps"></a>Como utilizar a biblioteca do cliente de JavaScript para aplicações móveis Azure

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Este guia ensina-lhe executar cenários comuns utilizando o mais recente [SDK JavaScript para as aplicações móveis do Azure]. Se estiver familiarizado com as aplicações móveis do Azure, primeiro conclua [Azure Mobile aplicações guia] para criar um back-end e criar uma tabela. Neste guia, iremos concentrar-nos utilizando o back-end móvel nas aplicações Web HTML/JavaScript.

## <a name="supported-platforms"></a>Plataformas suportadas

Vamos limitar o suporte de browser para as versões atuais e o últimos dos browsers principais: Google Chrome, Microsoft Edge, Microsoft Internet Explorer e Mozilla Firefox.  Vamos esperar o SDK para funcionar com qualquer browser relativamente Moderno.

O pacote é distribuído como um módulo de JavaScript Universal, para que suporta globals, AMD, e CommonJS formatos.

##<a name="Setup"></a>Configuração e pré-requisitos

Este guia assume que criou back-end com uma tabela. Este guia assume que a tabela que tem o mesmo esquema como as tabelas nesses tutoriais.

Instalar o Azure Mobile aplicações JavaScript SDK pode ser feito através de `npm` comando:

```
npm install azure-mobile-apps-client --save
```

Depois de instalado, a biblioteca está localizada no `node_modules/azure-mobile-apps-client/dist/MobileServices.Web.min.js`.  Copie este ficheiro para a sua área web.

```
<script src="path/to/MobileServices.Web.min.js"></script>
```

A biblioteca pode também ser utilizada como um módulo de ES2015, dentro de ambientes de CommonJS como Browserify e Webpack e como uma biblioteca AMD.  Por exemplo:

```
# For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
# For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

[AZURE.INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

##<a name="auth"></a>Como: autenticar os utilizadores

Suporta Azure aplicação de serviço de autenticação e autorização de utilizadores da aplicação utilizar vários fornecedores de identidades externas: Facebook, Google, Account Microsoft e Twitter. Pode definir permissões em tabelas para restringir o acesso operações específicas para apenas utilizadores autenticados. Também pode utilizar a identidade do utilizadores autenticados para implementar regras de autorização nos scripts do servidor. Para obter mais informações, consulte o tutorial [começar com autenticação] .

Dois fluxos de autenticação suportados: um fluxo de servidor e um fluxo de cliente.  O fluxo de servidor fornece a experiência de autenticação mais simples, como baseia-se na interface de autenticação de web do fornecedor. O fluxo de cliente permite a integração mais aprofundada com capacidades específicas de dispositivo tais como single-sign-on como baseia-se no SDK específicas do fornecedor.

[AZURE.INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

###<a name="configure-external-redirect-urls"></a>Como: configurar o serviço de aplicação móvel para redirecionar externos URLs.

Vários tipos de aplicações JavaScript utilizam uma funcionalidade de loopback para processar OAuth IU monetários.  Estas capacidades incluem:

* Executar o seu serviço localmente
* Utilizar o Live recarregar com a arquitetura iónico
* Redirecionar para o serviço de aplicação para autenticação. 

A ser executado localmente pode causar problemas porque, por predefinição, a autenticação de aplicação de serviço apenas está configurada para permitir o acesso a partir do seu back-end aplicação Mobile. Utilize os seguintes passos para alterar as definições de aplicação de serviço para activar a autenticação quando utiliza o server localmente:

1. Inicie a sessão [portal do Azure]
2. Navegue até ao seu back-end aplicação Mobile.
3. Selecione **Explorador de recursos** no menu de **Ferramentas de desenvolvimento** .
4. Clique em **Ir** para abrir o Explorador de recursos para sua back-end aplicação Mobile num novo separador ou janela.
5. Expandir a **config** > **authsettings** nó para a sua aplicação.
6. Clique no botão **Editar** para ativar a edição do recurso.
7. Localize o elemento de **allowedExternalRedirectUrls** , deve ser nulo. Adicione os URLs numa matriz:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Substitua os URLs na matriz os URLs do seu serviço, que neste exemplo é `http://localhost:3000` para o serviço de exemplo Node.js local. Também é possível utilizar `http://localhost:4400` para o serviço de ondulação ou outros URL, dependendo de como a sua aplicação está configurada.

8. Na parte superior da página, clique em **Leitura/escrita**, em seguida, clique em **colocar** para guardar as suas atualizações.

Também terá de adicionar os URLs loopback mesmo para as definições de lista branca CORS:

1. Navegar de volta para o [Azure portal].
2. Navegue até ao seu back-end aplicação Mobile.
3. Clique em **CORS** no menu de **API** .
4. Introduza cada URL na caixa de texto vazia do **Permitido origens diferentes** .  É criada uma nova caixa de texto.
5. Clique em **Guardar**
    
Depois do back-end atualizações, será possível utilizar os novos URLs de loopback na sua aplicação.

<!-- URLs. -->
[Início do Azure rápida de aplicações móveis]: app-service-mobile-cordova-get-started.md
[Começar a trabalhar com autenticação]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Portal do Azure]: https://portal.azure.com/
[SDK JavaScript para aplicações móveis Azure]: https://www.npmjs.com/package/azure-mobile-apps-client
[Query object documentation]: https://msdn.microsoft.com/en-us/library/azure/jj613353.aspx

