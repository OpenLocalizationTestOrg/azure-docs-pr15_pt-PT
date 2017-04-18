<properties 
    pageTitle="Configurar a política de autorização da chave conteúdo utilizando serviços de multimédia .NET SDK | Microsoft Azure" 
    description="Saiba como configurar uma política de autorização para uma chave de conteúdo utilizando .NET SDK serviços de multimédia." 
    services="media-services" 
    documentationCenter="" 
    authors="Mingfeiy" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/15/2016"
    ms.author="juliako;mingfeiy"/>



# <a name="dynamic-encryption-configure-content-key-authorization-policy"></a>Encriptação dinâmica: configurar a política de autorização de chave de conteúdo

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]

##<a name="overview"></a>Descrição geral

Serviços de multimédia do Microsoft Azure permite-lhe entregar sequências de TRAÇO MPEG, transmissão suaves e transmissão HTTP Live (HLS) protegidas com encriptação AES (Advanced Standard) (utilizando as chaves de encriptação de 128-bit) ou [Microsoft PlayReady DRM](https://www.microsoft.com/playready/overview/). AMS também permite-lhe entregar sequências de TRAÇO encriptadas com Widevine DRM. PlayReady e Widevine são encriptados pela especificação de encriptação comum (ISO/IEC 23001-7 CENC).

Dos serviços de multimédia também fornece um **Serviço de entrega de chave/licenças** a partir do qual os clientes podem obter chaves AES ou PlayReady/Widevine licenças para reproduzir o conteúdo encriptado.

Se pretender para serviços de multimédia encriptar um ativo, tem de associar uma chave de encriptação (**CommonEncryption** ou **EnvelopeEncryption**) activo (tal como descrito [aqui](media-services-dotnet-create-contentkey.md)) e também de configurar políticas de autorização para a chave (conforme descrito neste artigo).

Quando uma sequência é pedida por um leitor de, dos serviços de multimédia utiliza a chave especificada para encriptar dinamicamente o conteúdo a utilizar encriptação AES ou DRM. Para desencriptar a sequência, o leitor irá pedir a chave de serviço de entrega chave. Para decidir se ou não o utilizador está autorizado para obter a chave, o serviço avalia as políticas de autorização que especificou na chave.

Dos serviços de multimédia suporta várias formas de autenticação de utilizadores que fazem os pedidos de chaves. A política de autorização de chave conteúdo poderia ter uma ou mais restrições de autorização: **Abrir** ou **token** restrição. A política de restrita token tem de ser acompanhar por um token emitido por uma seguro Token serviço (STS). Dos serviços de multimédia suporta tokens no formato de **Tokens de Web simples** ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) e formato **JSON Web Token** ([JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3)).

Dos serviços de multimédia não fornece serviços Token seguro. Pode criar um STS personalizado ou tirar partido do Microsoft Azure ACS aos tokens de problema. O STS tem de ser configurado para criar um token sessão iniciado com a especificado violação de chave e o problema que especificou na configuração do token de restrição (conforme descrito neste artigo). O serviço de entrega chave dos serviços de multimédia irá devolver a chave de encriptação para o cliente se o token for válido e as afirmações no token de correspondem aos configurados para a chave de conteúdo.

Para obter mais informações, consulte o artigo

[JWT token authenitcation](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Integrar o Azure multimédia serviços OWIN MVC com base aplicação com o Azure Active Directory e restringir o conteúdo entrega chave com base em afirmações JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

[Utilizar Azure ACS aos tokens de problema](http://mingfeiy.com/acs-with-key-services).

###<a name="some-considerations-apply"></a>Algumas considerações aplicam-se:

- Para poder utilizar embalagem dinâmica e a encriptação dinâmica, tem de se certificar de ter, pelo menos, uma transmissão unidade reservada. Para mais informações, consulte o artigo [como dimensionar um serviço de multimédia](media-services-portal-manage-streaming-endpoints.md).
- O recurso tem de conter um conjunto de velocidade ajustável MP4s ou velocidade ajustável transmissão suaves ficheiros. Para mais informações, consulte o artigo [codificar um ativo](media-services-encode-asset.md).
- Carregar e descodificar seus ativos com a opção **AssetCreationOptions.StorageEncrypted** .
- Se planeia ter múltiplas teclas conteúdas que requerem a mesma configuração da política, é vivamente recomendado para criar uma política de autorização única e reutilizá-la com múltiplas teclas conteúdas.
- O serviço de entrega de chave coloca em cache ContentKeyAuthorizationPolicy e respectivos relacionados objectos (opções de política e restrições) para 15 minutos.  Se criar uma ContentKeyAuthorizationPolicy e especificar a utilização de uma restrição "Token", testá-lo, em seguida e, em seguida, atualize a política para restrição "Abrir", irá demorar cerca de 15 minutos antes da política muda para a versão da política de "Abrir".
- Se adicionar ou atualizar a política de entrega do seu elementos, tem de eliminar um locator existente (se existir) e criar um novo localizador.
- Atualmente, não é possível encriptar HDS transmissão formato ou progressive transferências.


##<a name="aes-128-dynamic-encryption"></a>Encriptação AES de 128 de dinâmica

###<a name="open-restriction"></a>Abrir uma restrição

Abrir restrição significa que o sistema irá entregar a chave para qualquer pessoa que torna um pedido de chave. Esta restrição poderá ser útil para fins de teste.

O exemplo seguinte cria uma política de autorização de abrir e adiciona-a para a chave de conteúdo.

AddOpenAuthorizationPolicy void público estático (IContentKey contentKey) {/ / criar ContentKeyAuthorizationPolicy com restrições abrir / / e criar a política de autorização de política IContentKeyAuthorizationPolicy = _context.
ContentKeyAuthorizationPolicies.
CreateAsync ("política de autorização abrir"). Resultado;

Lista<ContentKeyAuthorizationPolicyRestriction> restrições = nova lista<ContentKeyAuthorizationPolicyRestriction>();
    
        ContentKeyAuthorizationPolicyRestriction restriction =
            new ContentKeyAuthorizationPolicyRestriction
            {
                Name = "HLS Open Authorization Policy",
                KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                Requirements = null // no requirements needed for HLS
            };
    
        restrictions.Add(restriction);
    
        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create(
            "policy", 
            ContentKeyDeliveryType.BaselineHttp, 
            restrictions, 
            "");
    
        policy.Options.Add(policyOption);
    
        // Add ContentKeyAutorizationPolicy to ContentKey
        contentKey.AuthorizationPolicyId = policy.Id;
        IContentKey updatedKey = contentKey.UpdateAsync().Result;
        Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
    }


###<a name="token-restriction"></a>Restrição token

Esta secção descreve como criar uma política de autorização da chave conteúdo e associá-la com a chave de conteúdo. A política de autorização descreve quais os requisitos de autorização devem ser cumpridos para determinar se o utilizador está autorizado para receber a chave (por exemplo, faz a lista de "chave de verificação" conter a chave que foi assinado o token com).

Para configurar a opção de restrição token, é necessário utilizar um XML para descrever requisitos para o token a autorização. A configuração de restrição token XML tem de estar em conformidade com o esquema XML seguinte.

####<a id="schema"></a>Esquema de restrição token
    
    <?xml version="1.0" encoding="utf-8"?>
    <xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" xmlns:xs="http://www.w3.org/2001/XMLSchema">
      <xs:complexType name="TokenClaim">
        <xs:sequence>
          <xs:element name="ClaimType" nillable="true" type="xs:string" />
          <xs:element minOccurs="0" name="ClaimValue" nillable="true" type="xs:string" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenClaim" nillable="true" type="tns:TokenClaim" />
      <xs:complexType name="TokenRestrictionTemplate">
        <xs:sequence>
          <xs:element minOccurs="0" name="AlternateVerificationKeys" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
          <xs:element name="Audience" nillable="true" type="xs:anyURI" />
          <xs:element name="Issuer" nillable="true" type="xs:anyURI" />
          <xs:element name="PrimaryVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
          <xs:element minOccurs="0" name="RequiredClaims" nillable="true" type="tns:ArrayOfTokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenRestrictionTemplate" nillable="true" type="tns:TokenRestrictionTemplate" />
      <xs:complexType name="ArrayOfTokenVerificationKey">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenVerificationKey" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
      <xs:complexType name="TokenVerificationKey">
        <xs:sequence />
      </xs:complexType>
      <xs:element name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
      <xs:complexType name="ArrayOfTokenClaim">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenClaim" nillable="true" type="tns:TokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenClaim" nillable="true" type="tns:ArrayOfTokenClaim" />
      <xs:complexType name="SymmetricVerificationKey">
        <xs:complexContent mixed="false">
          <xs:extension base="tns:TokenVerificationKey">
            <xs:sequence>
              <xs:element name="KeyValue" nillable="true" type="xs:base64Binary" />
            </xs:sequence>
          </xs:extension>
        </xs:complexContent>
      </xs:complexType>
      <xs:element name="SymmetricVerificationKey" nillable="true" type="tns:SymmetricVerificationKey" />
    </xs:schema>

Quando configurar o **token** restringido a política, tem de especificar os parâmetros de** chave de verificação**, **emissor** e **audiência** principais. A **chave primária verificação **contém a chave que foi assinado o token com, **emissor** é o serviço de tokens seguro que o token de problemas. A **audiência** (por vezes denominada **âmbito**) descreve à intenção do token ou o recurso autorize o token de acesso. O serviço de entrega chave dos serviços de multimédia valida que estes valores no token de acordo com os valores no modelo. 

Quando utiliza o **SDK de serviços de multimédia para .NET**, pode utilizar a classe de **TokenRestrictionTemplate** para gerar o token de restrição.
O exemplo seguinte cria uma política de autorização com uma restrição token. Neste exemplo, o cliente teria que apresentar um token que contém: iniciar sessão chave (VerificationKey), um emissor de tokens e afirmações necessárias.
    
    public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
    {
        string tokenTemplateString = GenerateTokenRequirements();
    
        IContentKeyAuthorizationPolicy policy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("HLS token restricted authorization policy").Result;
    
        List<ContentKeyAuthorizationPolicyRestriction> restrictions =
                new List<ContentKeyAuthorizationPolicyRestriction>();
    
        ContentKeyAuthorizationPolicyRestriction restriction =
                new ContentKeyAuthorizationPolicyRestriction
                {
                    Name = "Token Authorization Policy",
                    KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                    Requirements = tokenTemplateString
                };
    
        restrictions.Add(restriction);
    
        //You could have multiple options 
        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create(
                "Token option for HLS",
                ContentKeyDeliveryType.BaselineHttp,
                restrictions,
                null  // no key delivery data is needed for HLS
                );
    
        policy.Options.Add(policyOption);
    
        // Add ContentKeyAutorizationPolicy to ContentKey
        contentKey.AuthorizationPolicyId = policy.Id;
        IContentKey updatedKey = contentKey.UpdateAsync().Result;
        Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
    
        return tokenTemplateString;
    }
    
    static private string GenerateTokenRequirements()
    {
        TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
    
        template.PrimaryVerificationKey = new SymmetricVerificationKey();
        template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();
    
        template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);
    
        return TokenRestrictionTemplateSerializer.Serialize(template);
    }

####<a id="test"></a>Token de teste

Para obter um token de teste com base na restrição token que foi utilizada para a política de autorização da chave, faça o seguinte.
    
    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate =
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
    
    // Generate a test token based on the the data in the given TokenRestrictionTemplate.
    // Note, you need to pass the key id Guid because we specified 
    // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
    Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
    
    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
    Console.WriteLine();


##<a name="playready-dynamic-encryption"></a>Encriptação PlayReady dinâmicos 

Dos serviços de multimédia permite-lhe configurar restrições que pretende para o runtime PlayReady DRM impor quando um utilizador estiver a tentar reproduzir conteúdo protegido e os direitos. 

Quando a proteger o seu conteúdo com PlayReady, uma das coisas que precisa para especificar a política de autorização é uma cadeia XML que define o [modelo de licença PlayReady](media-services-playready-license-template-overview.md). No SDK de serviços de multimédia para .NET, as classes **PlayReadyLicenseResponseTemplate** e **PlayReadyLicenseTemplate** ajudarão a definir o modelo de licença PlayReady.

[Este tópico](media-services-protect-with-drm.md) mostra como encriptar o seu conteúdo com **PlayReady** e **Widevine**.

###<a name="open-restriction"></a>Abrir uma restrição
    
Abrir restrição significa que o sistema irá entregar a chave para qualquer pessoa que torna um pedido de chave. Esta restrição poderá ser útil para fins de teste.

O exemplo seguinte cria uma política de autorização de abrir e adiciona-a para a chave de conteúdo.

    static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
    {
    
        // Create ContentKeyAuthorizationPolicy with Open restrictions 
        // and create authorization policy          
    
        List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
        {
            new ContentKeyAuthorizationPolicyRestriction 
            { 
                Name = "Open", 
                KeyRestrictionType = (int)ContentKeyRestrictionType.Open, 
                Requirements = null
            }
        };
    
        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
    
        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);
    
        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;
    
    
        contentKeyAuthorizationPolicy.Options.Add(policyOption);
    
        // Associate the content key authorization policy with the content key.
        contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
        contentKey = contentKey.UpdateAsync().Result;
    }

###<a name="token-restriction"></a>Restrição token

Para configurar a opção de restrição token, é necessário utilizar um XML para descrever requisitos para o token a autorização. A configuração de restrição token XML tem de estar em conformidade com o esquema XML apresentado [nesta](#schema) secção.
    
    public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
    {
        string tokenTemplateString = GenerateTokenRequirements();
    
        IContentKeyAuthorizationPolicy policy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("HLS token restricted authorization policy").Result;
    
        List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
        {
            new ContentKeyAuthorizationPolicyRestriction 
            { 
                Name = "Token Authorization Policy", 
                KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                Requirements = tokenTemplateString, 
            }
        };
    
        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
    
        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);
    
        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;
                
        policy.Options.Add(policyOption);
    
        // Add ContentKeyAutorizationPolicy to ContentKey
        contentKeyAuthorizationPolicy.Options.Add(policyOption);
    
        // Associate the content key authorization policy with the content key
        contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
        contentKey = contentKey.UpdateAsync().Result;
    
        return tokenTemplateString;
    }
    
    static private string GenerateTokenRequirements()
    {
    
        TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
    
        template.PrimaryVerificationKey = new SymmetricVerificationKey();
        template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();
    
    
        template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);
    
        return TokenRestrictionTemplateSerializer.Serialize(template);
    } 
    
    static private string ConfigurePlayReadyLicenseTemplate()
    {
        // The following code configures PlayReady License Template using .NET classes
        // and returns the XML string.

        //The PlayReadyLicenseResponseTemplate class represents the template for the response sent back to the end user. 
        //It contains a field for a custom data string between the license server and the application 
        //(may be useful for custom app logic) as well as a list of one or more license templates.
        PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();

        // The PlayReadyLicenseTemplate class represents a license template for creating PlayReady licenses
        // to be returned to the end users. 
        //It contains the data on the content key in the license and any rights or restrictions to be 
        //enforced by the PlayReady DRM runtime when using the content key.
        PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
        //Configure whether the license is persistent (saved in persistent storage on the client) 
        //or non-persistent (only held in memory while the player is using the license).  
        licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;
       
        // AllowTestDevices controls whether test devices can use the license or not.  
        // If true, the MinimumSecurityLevel property of the license
        // is set to 150.  If false (the default), the MinimumSecurityLevel property of the license is set to 2000.
        licenseTemplate.AllowTestDevices = true;


        // You can also configure the Play Right in the PlayReady license by using the PlayReadyPlayRight class. 
        // It grants the user the ability to playback the content subject to the zero or more restrictions 
        // configured in the license and on the PlayRight itself (for playback specific policy). 
        // Much of the policy on the PlayRight has to do with output restrictions 
        // which control the types of outputs that the content can be played over and 
        // any restrictions that must be put in place when using a given output.
        // For example, if the DigitalVideoOnlyContentRestriction is enabled, 
        //then the DRM runtime will only allow the video to be displayed over digital outputs 
        //(analog video outputs won’t be allowed to pass the content).

        //IMPORTANT: These types of restrictions can be very powerful but can also affect the consumer experience. 
        // If the output protections are configured too restrictive, 
        // the content might be unplayable on some clients. For more information, see the PlayReady Compliance Rules document.

        // For example:
        //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);

        responseTemplate.LicenseTemplates.Add(licenseTemplate);

        return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
    }


Para obter um token de teste com base na restrição token que foi utilizada para a autorização chave política Consulte [nesta](#test) secção. 

##<a id="types"></a>Tipos de utilizados quando definir ContentKeyAuthorizationPolicy

###<a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType

    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1,
        IPRestricted = 2,
    }

###<a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

    public enum ContentKeyDeliveryType
    {
      None = 0,
      PlayReadyLicense = 1,
      BaselineHttp = 2,
      Widevine = 3
    }

###<a id="TokenType"></a>TokenType

    public enum TokenType
    {
        Undefined = 0,
        SWT = 1,
        JWT = 2,
    }



##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="next-step"></a>Passo seguinte
Agora que configurou a política de autorização de chave de conteúdo, ir para o tópico de [como configurar a política de entrega de elementos](media-services-dotnet-configure-asset-delivery-policy.md) .
 
