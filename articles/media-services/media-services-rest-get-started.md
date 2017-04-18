<properties 
    pageTitle="Introdução ao fornecer conteúdo a pedido utilizando o resto | Microsoft Azure" 
    description="Neste tutorial orienta-o através dos passos de uma aplicação de entrega de conteúdos no pedido de execução com os serviços de multimédia do Azure utilizando REST API." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/11/2016" 
    ms.author="juliako"/>

#<a name="get-started-with-delivering-content-on-demand-using-rest"></a>Introdução ao fornecer conteúdo a pedido utilizando o resto 

[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]


>[AZURE.NOTE]
> Para concluir este tutorial, é necessária uma conta Azure. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure](/pricing/free-trial/?WT.mc_id=A261C142F). 


Este guia de introdução orienta-o através dos passos de implementar uma aplicação de entrega de conteúdos de invés (VoD) utilizando serviços de multimédia do Azure (MGA) REST APIs. 

O tutorial introduz o fluxo de trabalho dos serviços de multimédia básico e os objetos de programação mais comuns e tarefas necessárias para o desenvolvimento dos serviços de multimédia. Após a conclusão do tutorial, será possível transmitir em fluxo ou gradualmente transferir um ficheiro de multimédia de exemplo que carregou, codificado e transferidos.  

## <a name="prerequisites"></a>Pré-requisitos
Os pré-requisitos seguintes são necessários para iniciar a programação de com os serviços de multimédia com REST APIs.

- Noções básicas sobre como desenvolver com a API de REST de serviços de multimédia. Para mais informações, consulte o artigo [multimédia--resto-descrição geral dos serviços](http://msdn.microsoft.com/library/azure/hh973616.aspx).
- Uma aplicação da sua escolha que pode enviar pedidos e respostas HTTP. Neste tutorial, utiliza [Fiddler](http://www.telerik.com/download/fiddler). 

As seguintes tarefas são apresentadas neste guia de introdução.

1.  Crie uma conta dos serviços de multimédia (utilizando o portal do Azure).
2.  Configure o ponto final de transmissão (utilizando o portal do Azure).
1.  Ligar à conta dos serviços de multimédia com a REST API.
1.  Criar um novo imobilizado e carregue um ficheiro de vídeo com a REST API.
1.  Configure a transmissão de unidades com a REST API.
2.  Codificar o ficheiro de origem para o conjunto de velocidade ajustável MP4 ficheiros com a REST API.
1.  Publica os elementos e obter transmissão e URLs de transferência gradual com a REST API. 
1.  Reproduza o seu conteúdo. 

## <a name="create-an-azure-media-services-account-using-the-azure-portal"></a>Criar uma conta dos serviços de multimédia do Azure utilizando o portal do Azure

Os passos nesta secção Mostrar como criar uma conta de AMS.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Clique em **+ Novo** > **multimédia + CDN** > **dos serviços de multimédia**.

    ![Criar dos serviços de multimédia](./media/media-services-portal-vod-get-started/media-services-new1.png)

3. **Criar conta de serviços de multimédia** introduza valores necessários.

    ![Criar dos serviços de multimédia](./media/media-services-portal-vod-get-started/media-services-new3.png)
    
    1. Em **Nome da conta**, introduza o nome da nova conta de AMS. Um nome de conta dos serviços de multimédia é todos os números em minúsculas ou letras sem espaços e é 3 para 24 carateres de comprimento.
    2. Na sua subscrição, selecione entre as diferentes subscrições Azure que tiver acesso a.
    
    2. No **Grupo de recursos**, selecione o recurso novo ou existente.  Um grupo de recursos é um conjunto de recursos que partilham o ciclo de vida, permissões e políticas de. Saiba mais [aqui](azure-resource-manager/resource-group-overview.md#resource-groups).
    3. **Localização**, selecione a região geográfica é utilizada para armazenar os registos de multimédia e de metadados para a sua conta dos serviços de multimédia. Esta região é utilizada para processar e transmitir em fluxo os ficheiros de multimédia. Apenas as regiões dos serviços de multimédia disponíveis aparecem na caixa de lista pendente. 
    
    3. Na **Conta de armazenamento**, selecione uma conta de armazenamento para fornecer o armazenamento de Blobs do conteúdo multimédia da sua conta dos serviços de multimédia. Pode selecionar uma conta de armazenamento existente na mesma região geográfica como a sua conta dos serviços de multimédia, ou pode criar uma conta de armazenamento. É criada uma nova conta de armazenamento na mesma região. As regras de nomes de conta de armazenamento são iguais para contas dos serviços de multimédia.

        Saiba mais sobre o armazenamento [aqui](storage-introduction.md).

    4. Selecione **Afixar ao dashboard** para ver o progresso de implementação de conta.
    
7. Clique em **Criar** na parte inferior do formulário.

    Assim que a conta estiver criada com êxito, o estado muda para **a executar**. 

    ![Definições de serviços de multimédia](./media/media-services-portal-vod-get-started/media-services-settings.png)

    Para gerir a sua conta AMS (por exemplo, carregar vídeos, codificar elementos, monitorizar o progresso da tarefa) utilize a janela **Definições** .

## <a name="configure-streaming-endpoints-using-the-azure-portal"></a>Configurar os pontos finais transmissão através do portal Azure

Quando estiver a trabalhar com os serviços de multimédia do Azure um dos cenários mais comuns está a entregar vídeo através de velocidade ajustável transmissão os seus clientes. Dos serviços de multimédia suporta a velocidade de bits ajustável seguinte transmissão tecnologias: HTTP Live transmissão (HLS), transmissão suaves, MPEG TRAÇO e HDS (para o licenciamento do Adobe quando for necessária/acesso apenas).

Dos serviços de multimédia fornece embalagem dinâmica, que permite-lhe entregar a sua velocidade ajustável MP4 codificado conteúdo transmissão formatos suportados dos serviços de multimédia (MPEG travessão, HLS, transmissão suaves, HDS) just-in-time, sem ter de armazenar previamente embalados versões de cada um destes transmissão formatos.

Para tirar partido da embalagem dinâmico, terá de fazer o seguinte procedimento:

- Codificar o seu ficheiro mezzanine (origem) para um conjunto de ficheiros de velocidade ajustável MP4 (os passos de codificação são demonstrados mais tarde neste tutorial).  
- Crie, pelo menos, uma unidade de transmissão para o *ponto final de transmissão* a partir do qual planeia entrega o conteúdo. Os passos abaixo Mostrar como alterar o número de unidades transmissão.

Com embalagem dinâmica, necessita apenas armazenar e pagar para os ficheiros no formato de armazenamento única e dos serviços de multimédia constrói e serve a resposta adequada baseada nos pedidos de um cliente.

Para criar e alterar o número de unidades reservadas de transmissão, faça o seguinte:


1. Na janela **Definições** , clique em **pontos finais de transmissão**. 

2. Clique na predefinição transmissão ponto final. 

    A janela de **Detalhes de ponto final de transmissão de predefinido** é apresentada.

3. Para especificar o número de unidades transmissão, controlo de deslize **transmissão unidades** .

    ![Transmissão de unidades](./media/media-services-portal-vod-get-started/media-services-streaming-units.png)

4. Clique no botão **Guardar** para guardar as alterações.

    >[AZURE.NOTE]A alocação de qualquer novas unidades pode demorar até 20 minutos a concluir.

## <a id="connect"></a>Ligar à conta dos serviços de multimédia com a REST API

Duas coisas são necessárias ao aceder dos serviços de multimédia do Azure: um token de acesso fornecido pela serviços de controlo de acesso do Azure (ACS) e URI de serviços de multimédia própria. Pode utilizar qualquer meio que pretende ao criar estes pedidos desde que especificar os valores de cabeçalho correto e passar corretamente no token de acesso ao chamar para dos serviços de multimédia.

Os passos seguintes descrevem o fluxo de trabalho mais comuns quando utilizar os serviços de multimédia REST API para ligar dos serviços de multimédia:

1. Receber um token de acesso. 
2. Estabelecer ligação com os serviços de multimédia URI.  

    Lembre-se de que depois com êxito estabelecer ligação ao https://media.windows.net, receberá um 301 Redireccionamento especificando outra URI de serviços de multimédia. Terá de fazer as chamadas subsequentes para o novo URI. Também poderá receber uma resposta de 200 do HTTP/1.1 que contém a descrição de metadados API de ODATA.
3. Publicar as chamadas subsequentes de API para o novo URL. 
    
    Por exemplo, se, após está a tentar ligar, obteve o seguinte procedimento:
        
        HTTP/1.1 301 Moved Permanently
        Location: https://wamsbayclus001rest-hs.cloudapp.net/api/

    Deve registar as chamadas subsequentes de API para https://wamsbayclus001rest-hs.cloudapp.net/api/.

###<a name="getting-an-access-token"></a>Obter um token de acesso

Para aceder a serviços de multimédia diretamente através da API REST, obter um token de acesso de ACS e utilizá-lo durante a cada pedido de HTTP que transformar o serviço. Não necessita de quaisquer outros pré-requisitos antes de ligar diretamente a dos serviços de multimédia.

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

AccountKey para a sua conta dos serviços de multimédia tem de ser URL codificada quando utilizá-lo como o valor de client_secret no seu pedido de token de acesso.

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
       "access_token":"http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421330840&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=uf69n82KlqZmkJDNxhJkOxpyIpA2HDyeGUTtSnq1vlE%3d",
       "expires_in":"21600",
       "scope":"urn:WindowsAzureMediaServices"
    }
    

>[AZURE.NOTE]
Recomenda-se para os valores "access_token" e "expires_in" para um armazenamento externo em cache. Os dados tokens mais tarde podem obtidos a partir do armazenamento e reutilizados nas chamadas de serviços de multimédia REST API. Este é especialmente útil para cenários onde o token pode segura partilhado entre vários processos ou computadores.

Certifique-se monitorizar o valor de "expires_in" do token de acesso e atualizar as suas chamadas REST API com tokens de novos, conforme necessário.

###<a name="connecting-to-the-media-services-uri"></a>Ligar os serviços de multimédia URI

A raiz URI para dos serviços de multimédia é https://media.windows.net/. Inicialmente deve ligar a este URI e, se obtiver um 301 Redireccionamento novamente na resposta, deve certificar as chamadas subsequentes para o novo URI. Além disso, não utilize qualquer lógica automática-redirect/seguir nos pedidos de. Verbos HTTP e pedido organismos não serão reencaminhados para o novo URI.

A raiz URI para carregar e transferir ficheiros de elementos é https://yourstorageaccount.blob.core.windows.net/ onde o nome da conta de armazenamento é a mesma utilizado durante a configuração de conta dos serviços de multimédia.

O exemplo seguinte demonstra pedido de HTTP na raiz dos serviços de multimédia URI (https://media.windows.net/). O pedido obtém um 301 Redireccionamento novamente na resposta. O pedido subsequente está a utilizar o novo URI (https://wamsbayclus001rest-hs.cloudapp.net/api/).     

**Pedido de HTTP**:
    
    GET https://media.windows.net/ HTTP/1.1
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
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
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
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
     


>[AZURE.NOTE] De agora em novo URI é utilizado neste tutorial.

## <a id="upload"></a>Criar um novo imobilizado e carregar um ficheiro de vídeo com a REST API

Serviços de multimédia, carregar os ficheiros digitais para um ativo. A entidade de **elementos** pode conter vídeo, áudio, imagens, coleções de miniaturas, texto tracks e legenda fechada ficheiros (e os metadados sobre estes ficheiros.)  Assim que os ficheiros são carregados no elemento, o conteúdo está armazenado segura na nuvem para subsequente transformação e transmissão. 

É um dos valores que tem de fornecer aquando da criação de um ativo opções de criação de elementos. A propriedade de **Opções** é um valor de enumeração que descreve as opções de encriptação que pode ser criado um ativo com. Um valor válido é um dos valores a partir da lista abaixo, não uma combinação de valores a partir desta lista:

 
- **Nenhum** = **0** - sem encriptação é utilizado. Ao utilizar esta opção não está protegido o conteúdo em trânsito ou no resto no armazenamento.
    Se planear entregar uma MP4 utilizando transferência gradual, utilize esta opção. 
- **StorageEncrypted** = **1** - encripta o seu conteúdo limpar localmente utilizar encriptação AES 256 a bits e, em seguida, os carregamentos pendentes ao armazenamento do Azure onde foi armazenado encriptados em repouso. Elementos protegidos com encriptação de armazenamento são automaticamente não encriptados colocados num sistema de ficheiros encriptados antes de codificação e, opcionalmente, encriptar novamente antes de carregar novamente como um novo activo de saída. No caso de utilização principal para encriptação de armazenamento é quando que pretende proteger os seus ficheiros de multimédia de entrada de alta qualidade com encriptação forte em repouso no disco.
- **CommonEncryptionProtected** = **2** - Utilize esta opção se o carregamento de conteúdo que já foram encriptado e protegido com encriptação comuns ou DRM PlayReady (por exemplo, suaves transmissão protegido com PlayReady DRM).
- **EnvelopeEncryptionProtected** = **4** – Utilize esta opção se são carregar HLS encriptados com AES. Os ficheiros tem foram codificados e encriptados por transformar gestor.

### <a name="create-an-asset"></a>Criar um ativo

Um ativo é um contentor para vários tipos de ou conjuntos de objetos de serviços de multimédia, incluindo vídeo, áudio, imagens, coleções de miniaturas, faixas de texto e ficheiros de legenda fechada. No REST API, a criação de um ativo requer enviar pedido de mensagem para dos serviços de multimédia e colocar as informações de propriedade sobre os elementos no corpo do pedido.

O exemplo seguinte mostra como criar um ativo.

**Pedido de HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 45
    
    {"Name":"BigBuckBunny.mp4", "Options":"0"}
    

**Resposta de HTTP**

Se tiver êxito, é devolvido o seguinte procedimento:
    
    HTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 452
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    request-id: e98be122-ae09-473a-8072-0ccd234a0657
    x-ms-request-id: e98be122-ae09-473a-8072-0ccd234a0657
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:06:40 GMT
        
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
       "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "State":0,
       "Created":"2015-01-18T22:06:40.6010903Z",
       "LastModified":"2015-01-18T22:06:40.6010903Z",
       "AlternateId":null,
       "Name":"BigBuckBunny2.mp4",
       "Options":0,
       "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StorageAccountName":"storagetestaccount001"
    }
    
### <a name="create-an-assetfile"></a>Criar um AssetFile

A entidade de [AssetFile](http://msdn.microsoft.com/library/azure/hh974275.aspx) representa um ficheiro de áudio ou vídeo que está armazenado num contentor de Blobs. Um ficheiro de elementos sempre está associado um ativo e um ativo pode conter um ou vários AssetFiles. A tarefa de serviços de Media Encoder falha se um objeto de ficheiro de elementos não está associado um ficheiro digital num contentor de Blobs.

Depois de carregar o ficheiro de multimédia para um contentor blob, utilize o pedido de HTTP **impressão em série** para atualizar a AssetFile com informações sobre o seu ficheiro de multimédia (como mostrado mais tarde no tópico). 

**Pedido de HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 164
    
    {  
       "IsEncrypted":"false",
       "IsPrimary":"false",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**Resposta de HTTP**

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 535
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5')
    Server: Microsoft-IIS/8.5
    request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    x-ms-request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 00:34:07 GMT
    
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Files/@Element",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "Name":"BigBuckBunny.mp4",
       "ContentFileSize":"0",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "EncryptionVersion":null,
       "EncryptionScheme":null,
       "IsEncrypted":false,
       "EncryptionKeyId":null,
       "InitializationVector":null,
       "IsPrimary":false,
       "LastModified":"2015-01-19T00:34:08.1934137Z",
       "Created":"2015-01-19T00:34:08.1934137Z",
       "MimeType":"video/mp4",
       "ContentChecksum":null
    }


### <a name="creating-the-accesspolicy-with-write-permission"></a>Criar o AccessPolicy com permissão de escrita. 

Antes de carregar os ficheiros para o armazenamento de BLOBs, defina o acesso de direitos de política para escrever um ativo. Para o fazer, PUBLIQUE um pedido de HTTP para o conjunto de entidade AccessPolicies. Definir um valor de DurationInMinutes durante a criação do ou recebe uma mensagem de erro de servidor interno 500 novamente na resposta. Para mais informações sobre AccessPolicies, consulte o artigo [AccessPolicy](http://msdn.microsoft.com/library/azure/hh974297.aspx).

O exemplo seguinte mostra como criar um AccessPolicy:
        
**Pedido de HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 74
    
    {"Name":"NewUploadPolicy", "DurationInMinutes":"440", "Permissions":"2"} 

**Resposta de HTTP**

    If successful, the following response is returned:
    
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 312
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae')
    Server: Microsoft-IIS/8.5
    request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
    x-ms-request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:18:06 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#AccessPolicies/@Element",
       "Id":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "Created":"2015-01-18T22:18:06.6370575Z",
       "LastModified":"2015-01-18T22:18:06.6370575Z",
       "Name":"NewUploadPolicy",
       "DurationInMinutes":440.0,
       "Permissions":2
    }

### <a name="get-the-upload-url"></a>Obter o URL de carregamento

Para receber o URL de carregamento real, crie um localizador de SA. Locator define a hora de início e o tipo de ponto final de ligação para clientes que pretendem aceder a ficheiros de um ativo. Pode criar várias entidades de Locator para um determinado par AccessPolicy e ativos processar pedidos de cliente diferente e as suas necessidades. Cada um dos seguinte Locator utiliza o valor de hora de início, assim como o valor de DurationInMinutes do AccessPolicy para determinar o comprimento do tempo, que pode ser utilizado um URL. Para mais informações, consulte o artigo [Locator](http://msdn.microsoft.com/library/azure/hh974308.aspx).


Um URL de SA tem o seguinte formato:

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

Algumas considerações aplicam-se:

- Não pode ter mais de cinco Locator exclusivo associado a um determinado activo ao mesmo tempo. Para mais informações, consulte o artigo Locator.
- Se precisar de carregar ficheiros imediatamente, deverá definir o valor de hora de início para cinco minutos antes da hora atual. Isto acontece porque poderão existir relógio distorção entre o seu computador cliente e dos serviços de multimédia. Além disso, tem de ser o valor de hora de início no seguinte formato de data/hora: aaaa-MM-DDTHH:mm:ssZ (por exemplo, "2014-05-23T17:53:50Z").   
- Poderão existir uma segunda 30 40 atrasar depois de um localizador de é criado para quando está disponível para utilização. Este problema se aplica ao URL SA e Locator de origem.

O exemplo seguinte mostra como criar um localizador de URL SA, tal como foi definido pela propriedade tipo no corpo do pedido ("1" para um localizador de SA) e "2" para uma localizador de origem no pedido. A propriedade de **Path** devolvida contém o URL que tem de utilizar para carregar o ficheiro.
    
**Pedido de HTTP**
    
    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Locators HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=f7f09258-6753-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 178
    
    {  
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Type":1
    }


**Resposta de HTTP**

Se tiver êxito, é devolvida a resposta seguinte:
        
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 949
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54')
    Server: Microsoft-IIS/8.5
    request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
    x-ms-request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 03:01:29 GMT
    
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Locators/@Element",
       "Id":"nb:lid:UUID:af57bdd8-6751-4e84-b403-f3c140444b54",
       "ExpirationDateTime":"2015-02-19T00:05:53",
       "Type":1,
       "Path":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
       "BaseUri":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247",
       "ContentAccessComponent":"?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Name":null
    }

### <a name="upload-a-file-into-a-blob-storage-container"></a>Carregar um ficheiro para um contentor de armazenamento de BLOBs
    
Assim que tiver a AccessPolicy e Locator definido, o ficheiro propriamente dito é carregado para um contentor de armazenamento de Blobs do Azure utilizando o Azure armazenamento REST APIs. Pode carregar na página ou bloquear blobs. 

>[AZURE.NOTE] Tem de adicionar o nome do ficheiro para o ficheiro que pretende carregar para o valor de Locator **caminho** recebido na secção anterior. Por exemplo, https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . . 

Para mais informações sobre como trabalhar com blobs do Azure armazenamento, consulte o artigo [Blob serviço REST API](http://msdn.microsoft.com/library/azure/dd135733.aspx).


### <a name="update-the-assetfile"></a>Atualizar a AssetFile 

Agora que carregou o seu ficheiro, Atualize as informações de FileAsset tamanho (e outros). Por exemplo:
    
    MERGE https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5') HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    
    {  
       "ContentFileSize":"1186540",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**Resposta de HTTP**

Se tiver êxito, o seguinte é devolvido: HTTP/1.1 204 sem conteúdo

## <a name="delete-the-locator-and-accesspolicy"></a>Eliminar o localizador e AccessPolicy 

**Pedido de HTTP**


    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net

    
**Resposta de HTTP**

Se tiver êxito, é devolvido o seguinte procedimento:

    HTTP/1.1 204 No Content 
    ...

**Pedido de HTTP**

    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net

**Resposta de HTTP**

Se tiver êxito, é devolvido o seguinte procedimento:

    HTTP/1.1 204 No Content 
    ...

 
## <a id="configure_streaming_units"></a>Configurar a transmissão de unidades com a REST API

Quando estiver a trabalhar com os serviços de multimédia do Azure um dos cenários mais comuns está a entregar velocidade ajustável transmissão os seus clientes. Com a velocidade ajustável streaming, o cliente pode mudar uma sequência de velocidade superiores ou inferiores à medida que o vídeo é apresentado com base em largura de banda de rede actual, a utilização da CPU e outros fatores. Dos serviços de multimédia suporta a velocidade de bits ajustável seguinte transmissão tecnologias: HTTP Live transmissão (HLS), transmissão suaves, MPEG TRAÇO e HDS (para o licenciamento do Adobe quando for necessária/acesso apenas). 

Dos serviços de multimédia fornece embalagem dinâmica, que permite-lhe distribuir o conteúdo de velocidade ajustável MP4 ou transmissão suaves codificado no transmissão formatos suportados dos serviços de multimédia (MPEG travessão, HLS, transmissão suaves, HDS) sem ter de voltar a compactar para estes formatos de transmissão. 

Para tirar partido da embalagem dinâmico, terá de fazer o seguinte procedimento:

- obter, pelo menos, uma unidade de transmissão para o **ponto final de transmissão **a partir do qual pretende distribuir o seu conteúdo (descrito nesta secção).
- codificar ou transcodificar seu mezzanine (origem) ficheiro para um conjunto de velocidade ajustável MP4 ficheiros ou velocidade ajustável transmissão suaves ficheiros (os passos de codificação são demonstrados mais tarde neste tutorial),  

Com embalagem dinâmica, necessita apenas armazenar e pagar para os ficheiros no formato de armazenamento única e dos serviços de multimédia constrói e serve a resposta adequada baseada nos pedidos de um cliente. 


>[AZURE.NOTE] Para obter informações sobre preços detalhes, consulte o artigo [Detalhes de preços de serviços de multimédia](http://go.microsoft.com/fwlink/?LinkId=275107).

Para alterar o número de unidades reservadas de transmissão, faça o seguinte:
    
### <a name="get-the-streaming-endpoint-you-want-to-update"></a>Obter o ponto final de transmissão que quer atualizar

Por exemplo, vamos obter o primeiro ponto final transmissão na sua conta (pode ter até duas transmissão pontos finais a executar o estado ao mesmo tempo.)

**Pedido de HTTP**:

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/StreamingEndpoints()?$top=1 HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421466122&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=TiKGEOTporft4pFGU24sSZRZk5GRAWszFXldl5NXAhY%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net

**Resposta de HTTP**
    
Se tiver êxito, é devolvido o seguinte procedimento:
    
    HTTP/1.1 200 OK
    . . . 
    
### <a name="scale-the-streaming-endpoint"></a>O ponto final de transmissão de escala
 
**Pedido de HTTP**:

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/StreamingEndpoints('nb:oid:UUID:cd57670d-cc1c-0f86-16d8-3ad478bf9486')/Scale HTTP/1.1
    Content-Type: application/json;odata=verbose
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421466122&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=TiKGEOTporft4pFGU24sSZRZk5GRAWszFXldl5NXAhY%3d
    x-ms-version: 2.11
    x-ms-client-request-id: 39f96c93-a4b1-43ce-b97e-b2aaa44ee2dd
    Host: wamsbayclus001rest-hs.cloudapp.net
    
    {"scaleUnits":1}

**Resposta de HTTP**

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 39f96c93-a4b1-43ce-b97e-b2aaa44ee2dd
    request-id: 3c1ba1c7-281c-4b2d-a898-09cb70a3a424
    x-ms-request-id: 3c1ba1c7-281c-4b2d-a898-09cb70a3a424
    operation-id: nb:opid:UUID:1853bcbf-b71f-4ed5-a4c7-a581d4f45ae7
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Fri, 16 Jan 2015 22:16:43 GMT
    Content-Length: 0

    
### <a id="long_running_op_status"></a>Verificar o estado de uma operação de execução longa

A alocação de qualquer novas unidades demora cerca de 20 minutos para concluir. Para verificar o estado da operação, utilize o método de **operações** e especificar o Id da operação de. A operação de Id foi devolvido na resposta ao pedido de **escala** .

    operation-id: nb:opid:UUID:1853bcbf-b71f-4ed5-a4c7-a581d4f45ae7
 
**Pedido de HTTP**:
    
    GET https://wamsbayclus001rest-hs.cloudapp.net/api/Operations('nb:opid:UUID:1853bcbf-b71f-4ed5-a4c7-a581d4f45ae7') HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421466122&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=TiKGEOTporft4pFGU24sSZRZk5GRAWszFXldl5NXAhY%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    
**Resposta de HTTP**
    
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 515
    Content-Type: application/json;odata=verbose;charset=utf-8
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 829e1a89-3ec2-4836-a04d-802b5aeff5e8
    request-id: f7ae8a78-af8d-4881-b9ea-ca072cfe0b60
    x-ms-request-id: f7ae8a78-af8d-4881-b9ea-ca072cfe0b60
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Fri, 16 Jan 2015 22:57:39 GMT
    
    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.cloudapp.net/api/Operations('nb%3Aopid%3AUUID%3Acc339c28-6bba-4f7d-bee5-91ea4a0a907e')",
             "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Operations('nb%3Aopid%3AUUID%3Acc339c28-6bba-4f7d-bee5-91ea4a0a907e')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Operation"
          },
          "Id":"nb:opid:UUID:cc339c28-6bba-4f7d-bee5-91ea4a0a907e",
          "State":"Succeeded",
          "TargetEntityId":"nb:oid:UUID:cd57670d-cc1c-0f86-16d8-3ad478bf9486",
          "ErrorCode":null,
          "ErrorMessage":null
       }
    }


## <a id="encode"></a>Codificar o ficheiro de origem para o conjunto de velocidade ajustável MP4 ficheiros

Depois de ingesting que podem ser codificados activos para serviços de multimédia, multimédia, transmuxed, como a marca de água e assim sucessivamente, antes-é entregue clientes. Estas actividades são agendadas e executar a em várias instâncias de função de fundo para se certificar de alto desempenho e disponibilidade. Estas actividades são denominadas tarefas e cada [tarefa](http://msdn.microsoft.com/library/azure/hh974289.aspx) é composta por atómica tarefas que efetuar o trabalho real no ficheiro ativo. 

Como foi mencionado anteriormente, quando se trabalha com multimédia do Microsoft Azure serviços um dos cenários mais comuns está a entregar velocidade ajustável transmissão os seus clientes. Dos serviços de multimédia dinamicamente pode compactar um conjunto de ficheiros de velocidade ajustável MP4 para um dos seguintes formatos: HTTP Live transmissão (HLS), transmissão suaves, MPEG TRAÇO e HDS (para o licenciamento do Adobe quando for necessária/acesso apenas). 

Para tirar partido da embalagem dinâmico, terá de fazer o seguinte procedimento:

- codificar ou transcodificar seu mezzanine (origem) ficheiro para um conjunto de velocidade ajustável MP4 ficheiros ou velocidade ajustável transmissão suaves,  
- obter, pelo menos, uma unidade de transmissão para o ponto final de transmissão a partir do qual pretende distribuir o conteúdo. 

A secção seguinte mostra como criar uma tarefa que contém uma tarefa de codificação. Especifica a tarefa para transcodificar o ficheiro mezzanine para um conjunto de velocidade ajustável MP4s utilizando **Media Encoder padrão**. A secção também mostra como monitorizar a tarefa de processamento de progresso. Quando a tarefa estiver concluída, que seria poderá criar Locator que é necessários para obter acesso a seus ativos. 

### <a name="get-a-media-processor"></a>Obter um processador de multimédia

Serviços de multimédia, um processador de multimédia é um componente que se trata de uma tarefa de processamento específico, tal como a codificação de conversão do formato, encriptação ou desencriptar conteúdo multimédia. Para a tarefa de codificação mostrada neste tutorial, vamos utilizar a norma de descodificador de multimédia.

O seguinte código de pedidos de id do codificador. 

**Pedido de HTTP**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=f7f09258-6753-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421675491&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=9hUudHYnATpi5hN3cvTfgw%2bL4N3tL0fdsRnQnm6ZYIU%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    

**Resposta de HTTP**

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 273
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
    x-ms-request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 07:54:09 GMT
    
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "Description":"Media Encoder Standard",
             "Name":"Media Encoder Standard",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }

### <a name="create-a-job"></a>Criar uma tarefa

Cada tarefa pode ter uma ou mais tarefas dependendo do tipo de processamento que pretende efetuar. Através da API REST, pode criar tarefas e as suas tarefas relacionadas numa de duas formas: tarefas podem ser definido inline através da propriedade de navegação de tarefas em entidades de tarefa ou através de processamento batch de OData. O SDK de serviços de multimédia utiliza processamento batch. No entanto, para facilitar a leitura dos exemplos de código neste tópico, as tarefas são definido inline. Para obter informações sobre o processamento batch, consulte o artigo [Processamento de lote Open Data Protocol (OData)](http://www.odata.org/documentation/odata-version-3-0/batch-processing/).

O exemplo seguinte mostra-lhe como criar e publicar uma tarefa com um que conjunto de tarefas para codificar um vídeo a uma resolução específica e qualidade. A seguinte secção de documentação contém a lista de todas as [predefinições de tarefa](http://msdn.microsoft.com/library/mt269960) suportado pelo processador Media Encoder padrão.  

**Pedido de HTTP**
    
    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: application/json
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421675491&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=9hUudHYnATpi5hN3cvTfgw%2bL4N3tL0fdsRnQnm6ZYIU%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 482
    
    {  
       "Name":"NewTestJob",
       "InputMediaAssets":[  
          {  
             "__metadata":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')"
             }
          }
       ],
       "Tasks":[  
          {  
             "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset>
                <outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
          }
       ]
    }

**Resposta de HTTP**

Se tiver êxito, é devolvida a resposta seguinte:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 1215
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')
    Server: Microsoft-IIS/8.5
    request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
    x-ms-request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 08:04:35 GMT
        
    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
             "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Job"
          },
          "Tasks":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Tasks"
             }
          },
          "OutputMediaAssets":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets"
             }
          },
          "InputMediaAssets":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')/InputMediaAssets"
             }
          },
          "Id":"nb:jid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
          "Name":"NewTestJob",
          "Created":"2015-01-19T08:04:34.3287057Z",
          "LastModified":"2015-01-19T08:04:34.3287057Z",
          "EndTime":null,
          "Priority":0,
          "RunningDuration":0,
          "StartTime":null,
          "State":0,
          "TemplateId":null,
          "JobNotificationSubscriptions":{  
             "__metadata":{  
                "type":"Collection(Microsoft.Cloud.Media.Vod.Rest.Data.Models.JobNotificationSubscription)"
             },
             "results":[  
    
             ]
          }
       }
    }


Existem alguns aspetos importantes a nota em qualquer pedido de tarefa:

- Propriedades de TaskBody tem de utilizar literal XML para definir o número de entrada ou saída de activos que são utilizados pela tarefa. O tópico da tarefa contém a definição de esquema XML para o XML.
- Na definição do TaskBody, cada interiores valor do <inputAsset> e <outputAsset> tem de estar definida como JobInputAsset(value) ou JobOutputAsset(value).
- Uma tarefa pode ter vários elementos de saída. Um JobOutputAsset(x) só pode ser utilizado uma vez como um resultado de uma tarefa numa tarefa.
- Pode especificar JobInputAsset ou JobOutputAsset como um teclado ativo de uma tarefa.
- Tarefas não tem a formar um ciclo.
- O parâmetro do valor transmitir JobInputAsset ou JobOutputAsset representa o valor de índice de um ativo. Os ativos reais são definidos nas propriedades de navegação InputMediaAssets e OutputMediaAssets sobre a definição da entidade de tarefa. 

>[AZURE.NOTE] Uma vez que dos serviços de multimédia é criada com base em OData v3, os ativos individuais InputMediaAssets e OutputMediaAssets coleções de propriedade navegação são referenciados através de um "__metadata: uri" par valor do nome. 

- Conjunto mapas do InputMediaAssets para um ou mais elementos de ter criado dos serviços de multimédia. OutputMediaAssets são criados pelo sistema. Não faça referência a um ativo existente.
- OutputMediaAssets pode ter o nome utilizando o atributo assetName. Se não existe este atributo, em seguida, o nome da OutputMediaAsset é independentemente do valor de texto interna do <outputAsset> elemento for com um sufixo do valor do nome da tarefa ou o valor de Id da tarefa (neste caso onde não é definida a propriedade nome). Por exemplo, se definir um valor para assetName para "Exemplo", em seguida, a propriedade nome do OutputMediaAsset seria definida para "Exemplo". No entanto, se não tiver definido um valor para assetName, mas tiver definido o nome da tarefa para "NewJob", em seguida, o nome de OutputMediaAsset seria "JobOutputAsset (valor) _NewJob". 

    O exemplo seguinte mostra como definir o atributo assetName:
    
        "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"


- Para ativar a interligação de tarefa:

    - Uma tarefa tem de ter, pelo menos, duas tarefas
    - Tem de ter, pelo menos, uma tarefa cuja entrada é saída do outra tarefa da tarefa.

Para obter mais informações, consulte o artigo [criar uma tarefa de codificação com a API de resto de serviços de multimédia](http://msdn.microsoft.com/library/azure/jj129574.aspx).

### <a name="monitor-processing-progress"></a>Monitorizar o progresso de processamento

Pode obter o estado da tarefa utilizando a propriedade de estado, conforme mostrado no seguinte exemplo. 

**Pedido de HTTP**

    GET https://wamsbayclus001rest-hs.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/State HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=zf84471d-2233-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1336908022&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=RYXOraO6Z%2f7l9whWZQN%2bypeijgHwIk8XyikA01Kx1%2bk%3d
    Host: wamsbayclus001rest-hs.net
    Content-Length: 0


**Resposta de HTTP**

Se tiver êxito, é devolvida a resposta seguinte:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 17
    Content-Type: application/json;odata=verbose;charset=utf-8
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 01324d81-18e2-4493-a3e5-c6186209f0c8
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Sun, 13 May 2012 05:16:53 GMT
    
    {"d":{"State":2}}


### <a name="cancel-a-job"></a>Cancelar uma tarefa

Dos serviços de multimédia permite-lhe cancelar a execução tarefas através da função CancelJob. Esta chamada devolve um código de erro 400 se tentar cancelar uma tarefa ao seu estado é cancelado, cancelar, erro ou tiver terminado.

O exemplo seguinte mostra como CancelJob de chamadas.


**Pedido de HTTP**


    GET https://wamsbayclus001rest-hs.net/API/CancelJob?jobid='nb%3ajid%3aUUID%3a71d2dd33-efdf-ec43-8ea1-136a110bd42c' HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.2
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=2f84471d-b1ae-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1336908753&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=kolAgnFfbQIeRv4lWxKSFa4USyjWXRm15Kd%2bNd5g8eA%3d
    Host: wamsbayclus001rest-hs.net


Se tiver êxito, é devolvido um código de 204 resposta com sem o corpo da mensagem.

>[AZURE.NOTE] Tem de codificação de URL o id da tarefa (normalmente nb:jid:UUID: somevalue) quando transmitir no como um parâmetro para CancelJob.


### <a name="get-the-output-asset"></a>Obter os elementos de saída 

O código seguinte mostra como pedir elemento saída ID. 


**Pedido de HTTP**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets() HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421675491&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=9hUudHYnATpi5hN3cvTfgw%2bL4N3tL0fdsRnQnm6ZYIU%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    

**Resposta de HTTP**

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 445
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
    x-ms-request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 08:28:13 GMT
        
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets",
       "value":[  
          {  
             "Id":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
             "State":0,
             "Created":"2015-01-19T07:52:15.603",
             "LastModified":"2015-01-19T07:52:15.603",
             "AlternateId":null,
             "Name":"Multibitrate MP4s",
             "Options":0,
             "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c",
             "StorageAccountName":"storagetestaccount001"
          }
       ]
    }



## <a id="publish_get_urls"></a>Publicar os elementos e obter transmissão e URLs de transferência gradual com a REST API

Para transmitir em fluxo ou transferir um ativo, tem primeiro de "publicá-lo" criando um localizador. Locator fornece acesso aos ficheiros contidas num elemento. Dos serviços de multimédia suporta dois tipos de Locator: Locator OnDemandOrigin, utilizado para transmitir multimédia em sequência (por exemplo, MPEG travessão, HLS ou transmissão suave) e Locator acesso assinatura (SA), utilizados para transferir ficheiros de multimédia.

Depois de criar o Locator, pode criar os URLs que são utilizados para transmitir em fluxo ou transferir os seus ficheiros. 


Um URL de transmissão para transmissão suaves tem o seguinte formato:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Um URL de transmissão de HLS tem o seguinte formato:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Um URL de transmissão de TRAÇO MPEG tem o seguinte formato:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


Um URL de SA utilizados para transferir ficheiros tem o seguinte formato:

    {blob container name}/{asset name}/{file name}/{SAS signature}

Esta secção mostra como efetuar as seguintes tarefas necessárias para "publicação" seus ativos.  

- Criar o AccessPolicy com permissão de leitura 
- Criar um URL de SA para transferir o conteúdo 
- Criar um URL de origem para o conteúdo transmitido em sequência 

###<a name="creating-the-accesspolicy-with-read-permission"></a>Criar o AccessPolicy com permissão de leitura

Antes de transferir ou qualquer conteúdo de multimédia transmitido em sequência, definir uma AccessPolicy com permissões de leitura e crie a entidade Locator adequada que especifica o tipo do dispositivo de entrega que pretende ativar para os seus clientes. Para mais informações sobre as propriedades disponíveis, consulte o artigo [AccessPolicy entidade propriedades](https://msdn.microsoft.com/library/azure/hh974297.aspx#accesspolicy_properties).

O exemplo seguinte mostra como especificar AccessPolicy para permissões de leitura para um determinado activo.

    POST https://wamsbayclus001rest-hs.net/API/AccessPolicies HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=2f84471d-b1ae-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1337067658&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=dithjGvlXR9HlyAf5DE99N5OCYkPAxsHIcsTSjm9%2fVE%3d
    Host: wamsbayclus001rest-hs.net
    Content-Length: 74
    Expect: 100-continue
    
    {"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}

Se tiver êxito, é devolvido um código de 201 sucesso que descrevem a entidade de AccessPolicy que criou. Em seguida, utilize o Id de AccessPolicy juntamente com o Id de elementos do elemento que contém o ficheiro que pretende para a prestação (tais como de um ativo de saída) para criar a entidade de Locator.

>[AZURE.NOTE]
Este fluxo de trabalho básico é igual um ficheiro a carregar quando ingesting de um ativo (tal como foi abordado anteriormente neste tópico). Além disso, como carregar ficheiros, se precisam de acesso aos seus ficheiros imediatamente (ou os seus clientes), defina o valor de hora de início para cinco minutos antes da hora atual. Esta ação é necessária porque poderão existir relógio distorção entre o cliente e dos serviços de multimédia. O valor de hora de início deve ser no seguinte formato de data/hora: aaaa-MM-DDTHH:mm:ssZ (por exemplo, "2014-05-23T17:53:50Z").


###<a name="creating-a-sas-url-for-downloading-content"></a>Criar um URL de SA para transferir o conteúdo 

O código seguinte mostra como obter um URL que pode ser utilizado para transferir um ficheiro de multimédia criado e carregou anteriormente. O AccessPolicy tem leitura conjunto de permissões e o caminho Locator refere-se para um URL de transferência SA.

    POST https://wamsbayclus001rest-hs.net/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=zf84471d-b1ae-2233-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1337067658&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=dithjGvlXR9HlyAf5DE99N5OCYkPAxsHIcsTSjm9%2fVE%3d
    Host: wamsbayclus001rest-hs.net
    Content-Length: 182
    Expect: 100-continue
    
    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c", "StartTime" : "2014-05-17T16:45:53", "Type":1}

Se tiver êxito, é devolvida a resposta seguinte:

    
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 1150
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 8cfd8556-3064-416a-97f2-67d9e35f0911
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Mon, 14 May 2012 21:41:32 GMT
        
    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
             "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
          },
          "AccessPolicy":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')/AccessPolicy"
             }
          },
          "Asset":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Asset"
             }
          },
          "Id":"nb:lid:UUID:8e5a821d-2194-4d00-8884-adf979856874",
          "ExpirationDateTime":"\/Date(1337049393000)\/",
          "Type":1,
          "Path":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c?st=2012-05-14T21%3A36%3A33Z&se=2012-05-15T02%3A36%3A33Z&sr=c&si=8e5a821d-2194-4d00-8884-adf979856874&sig=y75dViDpC5V8WutrXM%2B%2FGpR3uOtqmlISiNlHU1YUBOg%3D",
          "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
          "AssetId":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
          "StartTime":"\/Date(1337031393000)\/"
       }
    }


A propriedade **Path** devolvida contém o URL de SA.

>[AZURE.NOTE]
Se transferir o conteúdo de armazenamento encriptado, tem manualmente desencriptá-lo antes de compor-lo ou utilize o MediaProcessor desencriptação armazenamento numa tarefa de processamento para ficheiros transformados em claro para um OutputAsset de saída e, em seguida, transferir a partir desse activo. Para obter mais informações sobre o processamento de, consulte Criar uma tarefa de codificação com a API de resto de serviços de multimédia. Além disso, SA URL Locator não podem ser atualizada depois de ter criados. Por exemplo, não é possível reutilizar o localizador de mesmo com um valor de hora de início atualizado. Este é devido a forma como são criadas SA URLs. Se pretender aceder a um ativo para transferência depois de um localizador de expirou, tem de criar um novo com uma nova hora de início.

###<a name="download-files"></a>Transferir ficheiros

Assim que tiver a AccessPolicy e Locator definir, pode transferir ficheiros utilizando o Azure armazenamento REST APIs.  

>[AZURE.NOTE] Tem de adicionar o nome do ficheiro para o ficheiro que pretende transferir para o valor de Locator **caminho** recebido na secção anterior. Por exemplo, https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . . 

Para mais informações sobre como trabalhar com blobs do Azure armazenamento, consulte o artigo [Blob serviço REST API](http://msdn.microsoft.com/library/azure/dd135733.aspx).

Como resultado a tarefa de codificação que fez anterior (codificação para conjunto de ajustável MP4), tem de vários ficheiros MP4 que pode transferir gradualmente. Por exemplo:    
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z


### <a name="creating-a-streaming-url-for-streaming-content"></a>Criar um URL de transmissão de transmissão de conteúdo


O código seguinte mostra como criar um localizador de URL em sequência:

    POST https://wamsbayclus001rest-hs/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=2f84471d-b1ae-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1337067658&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=dithjGvlXR9HlyAf5DE99N5OCYkPAxsHIcsTSjm9%2fVE%3d
    Host: wamsbayclus001rest-hs
    Content-Length: 182
    Expect: 100-continue
    
    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3", "StartTime" : "2014-05-17T16:45:53",, "Type":2}

Se tiver êxito, é devolvida a resposta seguinte:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 981
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 2eac4158-fc78-4fa2-81ee-c9f582dc385f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Mon, 14 May 2012 21:41:39 GMT
        
    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
             "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
          },
          "AccessPolicy":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/AccessPolicy"
             }
          },
          "Asset":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/Asset"
             }
          },
          "Id":"nb:lid:UUID:52034bf6-dfae-4d83-aad3-3bd87dcb1a5d",
          "ExpirationDateTime":"\/Date(1337049395000)\/",
          "Type":2,
          "Path":"http://wamsbayclus001rest-hs.net/52034bf6-dfae-4d83-aad3-3bd87dcb1a5d/",
          "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
          "AssetId":"nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3",
          "StartTime":"\/Date(1337031395000)\/"
       }
    }

Para transmitir em fluxo um URL de origem transmissão suaves num transmissão media Player, tem de acrescentar o caminho propriedade com o nome da transmissão suaves ficheiro, seguido de manifesto "/ manifesto".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

Para transmitir em fluxo HLS, acrescentar (formato = m3u8 aapl) depois do "/ manifesto".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

Para transmitir em fluxo MPEG travessão, acrescentar (formato = mpd-tempo-LCR) depois do "/ manifesto".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)


## <a id="play"></a>Reproduzir o seu conteúdo  

A sequência de vídeo, utilize o [Leitor de serviços de multimédia do Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

Para testar a transferência gradual, cole um URL de um browser (por exemplo, IE, Chrome, Safari).


##<a name="next-steps-media-services-learning-paths"></a>Passos seguintes: Serviços de multimédia caminhos de aprendizagem

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="looking-for-something-else"></a>Está à procura de algo?

Se este tópico não contiverem aquilo que esperava, não é apresentado algo ou numa outra forma não corresponder às suas necessidades, forneça o seu comentário utilizando o tópico Disqus abaixo.



