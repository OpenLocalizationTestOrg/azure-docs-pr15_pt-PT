<properties 
    pageTitle="Ligar à conta de serviços de multimédia utilizando REST API | Microsoft Azure" 
    description="Este tópico demonstra como ligar aos serviços de multimédia uisng REST API." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="09/26/2016"  
    ms.author="juliako"/>


# <a name="connecting-to-media-services-account-using-media-services-rest-api"></a>Ligar à conta de serviços de multimédia utilizando serviços de multimédia REST API

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-connect-programmatically.md)
- [RESTO](media-services-rest-connect-programmatically.md)

Este tópico descreve como obter uma ligação de programação a dos serviços de multimédia do Microsoft Azure quando são programação com a API de resto de serviços de multimédia.

Duas coisas são necessárias ao aceder dos serviços de multimédia do Microsoft Azure: um token de acesso fornecido pela serviços de controlo de acesso do Azure (ACS) e URI de serviços de multimédia própria. Pode utilizar qualquer meio que pretende ao criar estes pedidos desde que especificar os valores de cabeçalho correto e passar corretamente no token de acesso ao chamar para dos serviços de multimédia.

Os passos seguintes descrevem o fluxo de trabalho mais comuns quando utilizar os serviços de multimédia REST API para ligar dos serviços de multimédia:

1. Obter um token de acesso 
2. Ligar os serviços de multimédia URI 

    >[AZURE.NOTE] Após com êxito estabelecer ligação ao https://media.windows.net, receberá um 301 Redireccionamento especificando outra URI de serviços de multimédia. Terá de fazer as chamadas subsequentes para o novo URI.
Também poderá receber uma resposta de 200 do HTTP/1.1 que contém a descrição de metadados API de ODATA.

3. Publicar as chamadas subsequentes de API para o novo URL. 

    Por exemplo, se, após está a tentar ligar, obteve o seguinte procedimento:

        HTTP/1.1 301 Moved Permanently
        Location: https://wamsbayclus001rest-hs.cloudapp.net/api/

    Deve registar as chamadas subsequentes de API para https://wamsbayclus001rest-hs.cloudapp.net/api/.

##<a name="access-control-address"></a>Endereço de controlo de acesso

Endereço de controlo de acesso dos serviços de multimédia é https://wamsprodglobal001acs.accesscontrol.windows.net, exceto região China norte, onde está https://wamsprodglobal001acs.accesscontrol.chinacloudapi.cn.

##<a name="getting-an-access-token"></a>Obter um token de acesso

Para aceder a serviços de multimédia diretamente através da API REST, obter um token de acesso de ACS e utilizá-lo durante a cada pedido de HTTP que transformar o serviço. Este token é semelhante a outros tokens fornecidos pela ACS com base em afirmações acesso fornecidas no cabeçalho de um pedido de HTTP e utilizar o protocolo de v2 OAuth. Não necessita de quaisquer outros pré-requisitos antes de ligar diretamente a dos serviços de multimédia.

O exemplo seguinte mostra o cabeçalho do pedido HTTP e o corpo utilizados para obter um token.

**Cabeçalho**:

    POST https://wamsprodglobal001acs.accesscontrol.windows.net/v2/OAuth2-13 HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Host: wamsprodglobal001acs.accesscontrol.windows.net
    Content-Length: 120
    Expect: 100-continue
    Connection: Keep-Alive
    Accept: application/json

    
**Corpo**:

Terá de provar os valores client_id e client_secret no corpo do pedido deste; client_id e client_secret correspondem os valores AccountName e AccountKey, respetivamente. Estes valores são fornecidos dos serviços de multimédia quando configurar a conta. 

Tenha em atenção que AccountKey para a sua conta dos serviços de multimédia tem de ser URL codificada (consulte [Codificação percentual](http://tools.ietf.org/html/rfc3986#section-2.1) quando utilizá-lo como o valor de client_secret no seu pedido de token de acesso.

    grant_type=client_credentials&client_id=ams_account_name&client_secret=URL_encoded_ams_account_key&scope=urn%3aWindowsAzureMediaServices


Por exemplo: 

    grant_type=client_credentials&client_id=amstestaccount001&client_secret=wUNbKhNj07oqjqU3Ah9R9f4kqTJ9avPpfe6Pk3YZ7ng%3d&scope=urn%3aWindowsAzureMediaServices


O exemplo seguinte mostra a resposta de HTTP que contém o acesso token no corpo da resposta.

    HTTP/1.1 200 OK
    Cache-Control: no-cache, no-store
    Pragma: no-cache
    Content-Type: application/json; charset=utf-8
    Expires: -1
    request-id: a65150f5-2784-4a01-a4b7-33456187ad83
    X-Content-Type-Options: nosniff
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Thu, 15 Jan 2015 08:07:20 GMT
    Content-Length: 670
    
    {  
       "token_type":"http://schemas.xmlsoap.org/ws/2009/11/swt-token-profile-1.0",
       "access_token":"http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421330840&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=uf69n82KlqZmkJDNxhJkOxpyIpA2HDyeGUTtSnq1vlE%3d",
       "expires_in":"21600",
       "scope":"urn:WindowsAzureMediaServices"
    }
    

>[AZURE.NOTE]
Recomenda-se para os valores "access_token" e "expires_in" para um armazenamento externo em cache. Os dados tokens mais tarde podem obtidos a partir do armazenamento e reutilizados nas chamadas de serviços de multimédia REST API. Este é especialmente útil para cenários onde o token pode segura partilhado entre vários processos ou computadores.

Certifique-se monitorizar o valor de "expires_in" do token de acesso e atualizar as suas chamadas REST API com tokens de novos, conforme necessário.

###<a name="connecting-to-the-media-services-uri"></a>Ligar os serviços de multimédia URI

A raiz URI para dos serviços de multimédia é https://media.windows.net/. Inicialmente deve ligar a este URI e, se obtiver um 301 Redireccionamento novamente na resposta, deve certificar as chamadas subsequentes para o novo URI. Além disso, não utilize qualquer lógica automática-redirect/seguir nos pedidos de. Verbos HTTP e pedido organismos não serão reencaminhados para o novo URI.

Tenha em atenção que a raiz URI para carregar e transferir ficheiros de elementos é https://yourstorageaccount.blob.core.windows.net/ onde o nome da conta de armazenamento é a mesma utilizado durante a configuração de conta dos serviços de multimédia.

O exemplo seguinte demonstra pedido de HTTP na raiz dos serviços de multimédia URI (https://media.windows.net/). O pedido obtém um 301 Redireccionamento novamente na resposta. O pedido subsequente está a utilizar o novo URI (https://wamsbayclus001rest-hs.cloudapp.net/api/).     

**Pedido de HTTP**:
    
    GET https://media.windows.net/ HTTP/1.1
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-6753-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
    x-ms-version: 2.11
    Accept: application/json
    Host: media.windows.net


**Resposta de HTTP**:
    
    HTTP/1.1 301 Moved Permanently
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/
    Server: Microsoft-IIS/8.5
    request-id: 987d7652-497a-44e5-b815-f492e02aef97
    x-ms-request-id: 987d7652-497a-44e5-b815-f492e02aef97
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sat, 17 Jan 2015 07:44:53 GMT
    Content-Length: 164
    
    <html><head><title>Object moved</title></head><body>
    <h2>Object moved to <a href="https://wamsbayclus001rest-hs.cloudapp.net/api/">here</a>.</h2>
    </body></html>


**Pedido de HTTP** (utilizando o novo URI):
            
    GET https://wamsbayclus001rest-hs.cloudapp.net/api/ HTTP/1.1
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
    x-ms-version: 2.11
    Accept: application/json
    Host: wamsbayclus001rest-hs.cloudapp.net


**Resposta de HTTP**:
    
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 1250
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 5f52ea9d-177e-48fe-9709-24953d19f84a
    x-ms-request-id: 5f52ea9d-177e-48fe-9709-24953d19f84a
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sat, 17 Jan 2015 07:44:52 GMT
    
    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata","value":[{"name":"AccessPolicies","url":"AccessPolicies"},{"name":"Locators","url":"Locators"},{"name":"ContentKeys","url":"ContentKeys"},{"name":"ContentKeyAuthorizationPolicyOptions","url":"ContentKeyAuthorizationPolicyOptions"},{"name":"ContentKeyAuthorizationPolicies","url":"ContentKeyAuthorizationPolicies"},{"name":"Files","url":"Files"},{"name":"Assets","url":"Assets"},{"name":"AssetDeliveryPolicies","url":"AssetDeliveryPolicies"},{"name":"IngestManifestFiles","url":"IngestManifestFiles"},{"name":"IngestManifestAssets","url":"IngestManifestAssets"},{"name":"IngestManifests","url":"IngestManifests"},{"name":"StorageAccounts","url":"StorageAccounts"},{"name":"Tasks","url":"Tasks"},{"name":"NotificationEndPoints","url":"NotificationEndPoints"},{"name":"Jobs","url":"Jobs"},{"name":"TaskTemplates","url":"TaskTemplates"},{"name":"JobTemplates","url":"JobTemplates"},{"name":"MediaProcessors","url":"MediaProcessors"},{"name":"EncodingReservedUnitTypes","url":"EncodingReservedUnitTypes"},{"name":"Operations","url":"Operations"},{"name":"StreamingEndpoints","url":"StreamingEndpoints"},{"name":"Channels","url":"Channels"},{"name":"Programs","url":"Programs"}]}
     


>[AZURE.NOTE] Depois de receber o novo URI, que é o URI que deve ser utilizado para comunicar com os serviços de multimédia. 


##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
