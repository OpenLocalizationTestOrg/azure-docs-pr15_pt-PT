<properties 
    pageTitle="Criar ContentKeys com .NET" 
    description="Saiba como criar conteúdas teclas que fornecem acesso seguro para recursos." 
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
    ms.date="09/26/2016"
    ms.author="juliako"/>


#<a name="create-contentkeys-with-net"></a>Criar ContentKeys com .NET

> [AZURE.SELECTOR]
- [RESTO](media-services-rest-create-contentkey.md)
- [.NET](media-services-dotnet-create-contentkey.md)

Dos serviços de multimédia permite-lhe criar e entregar activos encriptados. Um **ContentKey** fornece acesso seguro a sua s **ativo**. 

Quando cria um novo activo (por exemplo, antes de [carregar ficheiros](media-services-dotnet-upload-files.md)), pode especificar as seguintes opções de encriptação: **StorageEncrypted**, **CommonEncryptionProtected**ou **EnvelopeEncryptionProtected**. 

Quando medida que faz a elementos para os seus clientes, pode [configurar para recursos ser dinamicamente encriptados](media-services-dotnet-configure-asset-delivery-policy.md) com um da duas encriptação seguintes: **DynamicEnvelopeEncryption** ou **DynamicCommonEncryption**.

Elementos encriptados tem de ser associadas a **ContentKey**s. Este artigo descreve como criar uma chave de conteúdo.

>[AZURE.NOTE] Ao criar um novo activo **StorageEncrypted** utilizando o SDK de .NET de serviços de multimédia, o **ContentKey** é automaticamente criado e ligadas com elemento.

##<a name="contentkeytype"></a>ContentKeyType

Um dos valores que tem de definir quando criar uma conteúdo de chave é o tipo de conteúdo principais. Escolha uma das seguintes valores. 

    public enum ContentKeyType
    {
        /// <summary>
        /// Specifies a content key for common encryption.
        /// </summary>
        /// <remarks>This is the default value.</remarks>
        CommonEncryption = 0,

        /// <summary>
        /// Specifies a content key for storage encryption.
        /// </summary>
        StorageEncryption = 1,

        /// <summary>
        /// Specifies a content key for configuration encryption.
        /// </summary>
        ConfigurationEncryption = 2,

        /// <summary>
        /// Specifies a content key for Envelope encryption.  Only used internally.
        /// </summary>
        EnvelopeEncryption = 4
    }

##<a id="envelope_contentkey"></a>Criar o tipo de envelope ContentKey

O fragmento de código seguinte cria uma chave de conteúdo do tipo de encriptação envelope. Em seguida, associa a chave de elemento especificado.

    static public IContentKey CreateEnvelopeTypeContentKey(IAsset asset)
    {
        // Create envelope encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.EnvelopeEncryption);

        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int size)
    {
        byte[] randomBytes = new byte[size];
        using (RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider())
        {
            rng.GetBytes(randomBytes);
        }

        return randomBytes;
    }

chamada

    IContentKey key = CreateEnvelopeTypeContentKey(encryptedsset);



##<a id="common_contentkey"></a>Criar tipo comuns ContentKey    

O fragmento de código seguinte cria uma chave de conteúdo do tipo de encriptação comuns. Em seguida, associa a chave de elemento especificado.

    static public IContentKey CreateCommonTypeContentKey(IAsset asset)
    {
        // Create common encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.CommonEncryption);

        // Associate the key with the asset.
        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int length)
    {
        var returnValue = new byte[length];

        using (var rng =
            new System.Security.Cryptography.RNGCryptoServiceProvider())
        {
            rng.GetBytes(returnValue);
        }

        return returnValue;
    }
chamada

    IContentKey key = CreateCommonTypeContentKey(encryptedsset); 


##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
