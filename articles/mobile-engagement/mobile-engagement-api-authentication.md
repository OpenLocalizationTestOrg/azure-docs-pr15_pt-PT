<properties 
    pageTitle="Autenticar com Cativação móvel REST APIs"
    description="Descreve como autenticar com Azure Mobile Cativação REST APIs" 
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo"
    manager="erikre"
    editor=""/>

<tags
    ms.service="mobile-engagement"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="mobile-multiple"
    ms.workload="mobile" 
    ms.date="10/05/2016"
    ms.author="wesmc;ricksal"/>

# <a name="authenticate-with-mobile-engagement-rest-apis"></a>Autenticar com Cativação móvel REST APIs

## <a name="overview"></a>Descrição geral

Este documento descreve como obter um Oauth AAD válida token para autenticar com o Mobile Cativação REST APIs. 

É considerada que tiver uma subscrição Azure válida e se tiver criado uma aplicação móvel Cativação utilizando um dos nossos [Tutoriais de programador](mobile-engagement-windows-store-dotnet-get-started.md).

## <a name="authentication"></a>Autenticação

Um Microsoft Azure Active Directory com base OAuth token é utilizado para autenticação. 

Ordem de pedido de autenticação uma API, um cabeçalho de autorização deve ser adicionado a cada pedido que é o seguinte formato:

    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGmJlNmV2ZWJPamg2TTNXR1E...

>[AZURE.NOTE] Tokens do Azure Active Directory expirarem dentro de 1 hora.

Existem várias formas de obter um token. Uma vez que as APIs são geralmente denominadas a partir de um serviço na nuvem, que pretende utilizar uma chave de API. Uma chave de API no Azure terminologia chama-se uma palavra-passe principal do serviço. O procedimento seguinte descreve uma forma de configurar manualmente.

### <a name="one-time-setup-using-script"></a>Configuração única (utilizando script)

Deve seguir o conjunto de instruções abaixo para executar a configuração utilizando um script PowerShell que leva-o até o tempo mínimo para a configuração, mas que utiliza as predefinições mais permissíveis. Opcionalmente, também pode siga as instruções na [configuração manual](mobile-engagement-api-authentication-manual.md) para este procedimento diretamente a partir do portal do Azure e configuração mais preciso de fazer. 

1. Obtenha a versão mais recente do Azure PowerShell do [aqui](http://aka.ms/webpi-azps). Para mais informações sobre as instruções de transferência, pode ver esta [ligação](../powershell-install-configure.md).  

2. Quando Azure PowerShell estiver instalado, utilize os seguintes comandos para se certificar de que tem o **módulo Azure** instalado:

    um. Certifique-se de que o módulo Azure PowerShell está disponível na lista de módulos disponíveis. 
    
        Get-Module –ListAvailable 

    ![Módulos Azure disponíveis][1]
        
    b. Se não encontrar o módulo Azure PowerShell na lista acima, em seguida, o que precisa para executar o seguinte:
        
        Import-Module Azure 
        
3. Início de sessão para o Gestor de recursos do Azure a partir do PowerShell executando o seguinte comando e fornecer o seu nome de utilizador e palavra-passe para a sua conta Azure: 
        
        Login-AzureRmAccount

4. Se tiver múltiplas subscrições, em seguida, deverá execute o seguinte:

    um. Obter uma lista de todas as subscrições e copie SubscriptionId da subscrição que pretende utilizar. Certifique-se desta que subscrição é a mesma aquele que tem a aplicação, Cativação Mobile que passar para interagir com utilizando as APIs. 

        Get-AzureRmSubscription

    b. Execute o seguinte comando fornecer a SubscriptionId para configurar a subscrição a ser utilizado.

        Select-AzureRmSubscription –SubscriptionId <subscriptionId>

5. Copie o texto para o script [AzureRmServicePrincipalOwner.ps1 novo](https://raw.githubusercontent.com/matt-gibbs/azbits/master/src/New-AzureRmServicePrincipalOwner.ps1) para o seu computador local e guardá-lo como um cmdlet do PowerShell (por exemplo, `APIAuth.ps1`) e executá-la `.\APIAuth.ps1`. 
    
6. O script irá pedir para fornecer uma entrada de **principalName**. Fornece um nome adequado aqui que pretende utilizar para criar a sua aplicação do Active Directory (por exemplo, APIAuth). 

7. Depois de concluído o script, apresentem os seguintes quatro valores que precisa para autenticar através de programação com AD, por isso, certifique-se para copiá-las. 
        
    **TenantId**, **SubscriptionId**, **ApplicationId**e **palavra-passe**.

    Irá utilizar TenantId como `{TENANT_ID}`, ApplicationId como `{CLIENT_ID}` e secreta como `{CLIENT_SECRET}`.

    > [AZURE.NOTE] A política de segurança predefinidos pode impedir o a executar scripts de PowerShell. Se Sim, temporariamente configurar a política de execução para permitir a execução de script utilizando o seguinte comando:

        > Set-ExecutionPolicy RemoteSigned

8. Eis como o conjunto de PS cmdlets teria o seguinte aspeto. 

    ![][3]

9. Verifique no portal de gestão do Azure que foi criada uma nova aplicação de AD com o nome que forneceu para o script de chamada **principalName** em **que aplicações Mostrar a minha empresa é o proprietário**.

    ![][4]

#### <a name="steps-to-get-a-valid-token"></a>Passos para obter um token válido

1. Chamar a API com os seguintes parâmetros e certifique-se substituir o INQUILINO\_ID, cliente\_ID e cliente\_SECRETA:

    - **Pedido de URL** como *https://login.microsoftonline.com/ {INQUILINO\_ID} / oauth2 ainda/token*
    - **Cabeçalho de tipo de conteúdo de HTTP** como *aplicação/x-www-form-urlencoded*
    - **Corpo de pedido de HTTP** como *conceder\_tipo = client\_credenciais & client_id = {cliente\_ID} & client_secret = {cliente\_SECRETA} & resource=https%3A%2F%2Fmanagement.core.windows.net%2F*

    Um pedido de exemplo é o seguinte:

        POST /{TENANT_ID}/oauth2/token HTTP/1.1
        Host: login.microsoftonline.com
        Content-Type: application/x-www-form-urlencoded
        grant_type=client_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&reso
        urce=https%3A%2F%2Fmanagement.core.windows.net%2F

    Eis uma resposta de exemplo:

        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Content-Length: 1234
    
        {"token_type":"Bearer","expires_in":"3599","expires_on":"1445395811","not_before":"144
        5391911","resource":"https://management.core.windows.net/","access_token":{ACCESS_TOKEN}}

    Este exemplo incluído codificação de URL os parâmetros de mensagem, `resource` valor é realmente `https://management.core.windows.net/`. Tenha o cuidado também URL codificar `{CLIENT_SECRET}` como pode conter carateres especiais.

    > [AZURE.NOTE] Para testar a ligação, pode utilizar uma ferramenta de cliente HTTP como [Fiddler](http://www.telerik.com/fiddler) ou [extensão do Chrome Postman](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop) 

2. Agora chamada cada API, inclua o cabeçalho do pedido de autorização:

        Authorization: Bearer {ACCESS_TOKEN}

    Se obtiver um código de 401 estado devolvido, verifique o corpo da resposta,-pode indicar-lhe o token expirou. Nesse caso, obter um novo token.

##<a name="using-the-apis"></a>Utilizar as API

Agora que tem um token válido, está pronto para efetuar chamadas de API.

1. Em cada pedido API, terá de ser efetuada com um token válido, não expirado que obteve na secção anterior.

2. Terá de plug-in alguns parâmetros no pedido URI que identifica a sua aplicação. O pedido URI aspeto o seguinte

        https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/
        providers/Microsoft.MobileEngagement/appcollections/{app-collection}/apps/{app-resource-name}/

    Para obter os parâmetros, clique no seu nome de aplicação e clique em Dashboard e irá ver uma página como as seguintes ações com os 3 parâmetros.

    - **1**`{subscription-id}`
    - **2**`{app-collection}`
    - **3**`{app-resource-name}`
    - **4** nome do seu grupo de recursos vai ser **MobileEngagement** , a menos que criado um novo. 

    ![Parâmetros de Mobile Cativação API URI][2]

>[AZURE.NOTE] <br/>
>1. Ignore o endereço de raiz API à medida que trata-se para as APIs anterior.<br/>
>2. Se criou a aplicação através do portal Azure clássica, em seguida, terá de utilizar o nome do recurso da aplicação que é diferente do nome da aplicação. Se criou a aplicação no Portal do Azure deve utilizar o nome da aplicação propriamente dito (não existe nenhuma diferenciação entre o nome da aplicação do recurso e o nome da aplicação para aplicações criadas no novo portal).  

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication/azure-module.png
[2]: ./media/mobile-engagement-api-authentication/mobile-engagement-api-uri-params.png
[3]: ./media/mobile-engagement-api-authentication/ps-cmdlets.png
[4]: ./media/mobile-engagement-api-authentication/ad-app-creation.png



