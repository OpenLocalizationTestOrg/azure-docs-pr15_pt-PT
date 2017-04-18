<properties
    pageTitle="Tutorial: Encriptar e desencriptar blobs no armazenamento do Windows Azure utilizando Azure chave Cofre | Microsoft Azure"
    description="Neste tutorial explica como encriptar e desencriptar um blob utilizar a encriptação do lado do cliente para armazenamento do Windows Azure com Azure chave cofre."
    services="storage"
    documentationCenter=""
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="required"
    ms.date="10/18/2016"
    ms.author="lakasa;robinsh"/>

# <a name="tutorial-encrypt-and-decrypt-blobs-in-microsoft-azure-storage-using-azure-key-vault"></a>Tutorial: Encriptar e desencriptar blobs no armazenamento do Windows Azure utilizando Cofre de chave do Azure

## <a name="introduction"></a>Introdução

Neste tutorial abrange como fazer utilizar de encriptação de armazenamento do lado do cliente com Azure chave cofre. -Orienta como encriptar e desencriptar um blob numa aplicação de consola utilizando estas tecnologias.

**Estimativa tempo a concluir:** 20 minutos

Para obter informações gerais sobre Azure chave cofre, consulte o artigo [o que é o Azure chave cofre?](../key-vault/key-vault-whatis.md).

Para obter informações gerais sobre a encriptação do lado do cliente para o armazenamento do Windows Azure, consulte o artigo [encriptação do lado do cliente e Azure chave cofre para armazenamento do Windows Azure](storage-client-side-encryption.md).


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, tem de ter o seguinte procedimento:

- Uma conta de armazenamento do Windows Azure
- Visual Studio 2013 ou posterior
- Azure PowerShell


## <a name="overview-of-client-side-encryption"></a>Descrição geral de encriptação do lado do cliente

Para obter uma descrição geral de encriptação do lado do cliente para o armazenamento do Windows Azure, consulte o artigo [encriptação do lado do cliente e Azure chave cofre para armazenamento do Windows Azure](storage-client-side-encryption.md)

Eis uma breve descrição de como funciona a encriptação do lado do cliente:

1. O cliente de armazenamento do Windows Azure SDK gera uma chave de encriptação conteúdo (CEK), que é uma chave simétrica um tempo utilização.
2. Dados do cliente são encriptados utilizando este CEK.
3. O CEK, em seguida, é moldado (encriptados) utilizando a chave de encriptação de chave (KEK). O KEK está identificado por um identificador de chave e pode ser um par de chaves assimétrico ou uma chave simétrica e pode ser gerido localmente ou armazenado num Azure chave cofre. O próprio cliente armazenamento nunca tem acesso à KEK. -Invoca apenas o algoritmo de moldagem chave que é fornecido pela chave cofre. Os clientes podem optar por utilizar fornecedores personalizados para a chave moldagem/revelação caso pretendam.
4. Os dados encriptados, em seguida, são carregados para o serviço de armazenamento do Windows Azure.


## <a name="set-up-your-azure-key-vault"></a>Configurar o seu Cofre de chave do Azure
Para poder continuar neste tutorial, terá de efetuar os seguintes passos, são destacados tutorial [Introdução ao Azure chave cofre](../key-vault/key-vault-get-started.md):

- Crie uma chave cofre.
- Adicione uma chave ou palavra-passe ao Cofre de palavras chave.
- Registe-se uma aplicação com o Azure Active Directory.
- Autorize a aplicação para utilizar a chave ou palavra-passe.

Tome nota das ClientID e ClientSecret que foram geradas quando registar uma aplicação com o Azure Active Directory.

Crie ambas as chaves no chave cofre. Vamos partem do pressuposto de para o resto do tutorial que utilizou os seguintes nomes: ContosoKeyVault e TestRSAKey1.


## <a name="create-a-console-application-with-packages-and-appsettings"></a>Criar uma aplicação de consola com pacotes e AppSettings

No Visual Studio, crie uma nova aplicação de consola.

Adicione pacotes de nuget necessárias na consola do Gestor de pacotes.

    Install-Package WindowsAzure.Storage

    // This is the latest stable release for ADAL.
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

    Install-Package Microsoft.Azure.KeyVault
    Install-Package Microsoft.Azure.KeyVault.Extensions


Adicione AppSettings para a App.

    <appSettings>
        <add key="accountName" value="myaccount"/>
        <add key="accountKey" value="theaccountkey"/>
        <add key="clientId" value="theclientid"/>
        <add key="clientSecret" value="theclientsecret"/>
        <add key="container" value="stuff"/>
    </appSettings>

Adicione o seguinte `using` demonstrações e certifique-se adicionar uma referência a System.Configuration ao projeto.

    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System.Configuration;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using Microsoft.Azure.KeyVault;
    using System.Threading;     
    using System.IO;


## <a name="add-a-method-to-get-a-token-to-your-console-application"></a>Adicionar um método de obter um token à aplicação consola

O método que se segue é utilizado por classes cofre chave que necessita para autenticar para aceder ao seu Cofre chave.

    private async static Task<string> GetToken(string authority, string resource, string scope)
    {
        var authContext = new AuthenticationContext(authority);
        ClientCredential clientCred = new ClientCredential(
            ConfigurationManager.AppSettings["clientId"],
            ConfigurationManager.AppSettings["clientSecret"]);
        AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

        if (result == null)
            throw new InvalidOperationException("Failed to obtain the JWT token");

        return result.AccessToken;
    }

## <a name="access-storage-and-key-vault-in-your-program"></a>Armazenamento de acesso e cofre chave no seu programa

Na função principal, adicione o seguinte código.

    // This is standard code to interact with Blob storage.
    StorageCredentials creds = new StorageCredentials(
        ConfigurationManager.AppSettings["accountName"],
        ConfigurationManager.AppSettings["accountKey"]);
    CloudStorageAccount account = new CloudStorageAccount(creds, useHttps: true);
    CloudBlobClient client = account.CreateCloudBlobClient();
    CloudBlobContainer contain = client.GetContainerReference(ConfigurationManager.AppSettings["container"]);
    contain.CreateIfNotExists();

    // The Resolver object is used to interact with Key Vault for Azure Storage.
    // This is where the GetToken method from above is used.
    KeyVaultKeyResolver cloudResolver = new KeyVaultKeyResolver(GetToken);


> [AZURE.NOTE] Modelos de objeto do cofre chave
>
>É importante compreender o que não existem realmente dois chave cofre objeto modelos ter em consideração: uma é baseada no API REST (KeyVault espaço de nomes) e o outro é uma extensão para encriptação do lado do cliente.

> O cliente de cofre chave interage com a API REST e compreenda as teclas de Web JSON e segredos para os dois tipos de coisas contida na chave cofre.

> As extensões de cofre chave são classes parecerem especificamente criadas para encriptação do lado do cliente no armazenamento do Windows Azure. Que contêm uma interface para chaves (IKey) e aulas com base no conceito de um solucionador chave. Existem duas implementações do IKey tem de conhecer: RSAKey e SymmetricKey. Agora deverá acontecer ao coincidir com os aspetos que estão contidos num cofre chave mas, neste momento são classes independentes (de modo a tecla e secreta obtidos pelo cliente cofre chave não implementar IKey).


## <a name="encrypt-blob-and-upload"></a>Encriptar blob e carregar
Adicione o seguinte código para encriptar um blob e carregue-o para a sua conta de armazenamento Azure. O método de **ResolveKeyAsync** que é utilizado devolve um IKey.


    // Retrieve the key that you created previously.
    // The IKey that is returned here is an RsaKey.
    // Remember that we used the names contosokeyvault and testrsakey1.
    var rsa = cloudResolver.ResolveKeyAsync("https://contosokeyvault.vault.azure.net/keys/TestRSAKey1", CancellationToken.None).GetAwaiter().GetResult();


    // Now you simply use the RSA key to encrypt by setting it in the BlobEncryptionPolicy.
    BlobEncryptionPolicy policy = new BlobEncryptionPolicy(rsa, null);
    BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

    // Reference a block blob.
    CloudBlockBlob blob = contain.GetBlockBlobReference("MyFile.txt");

    // Upload using the UploadFromStream method.
    using (var stream = System.IO.File.OpenRead(@"C:\data\MyFile.txt"))
        blob.UploadFromStream(stream, stream.Length, null, options, null);


Segue-se uma captura de ecrã a partir do [Azure clássica Portal](https://manage.windowsazure.com) para um blob que tenha sido encriptado utilizando a encriptação do lado do cliente com uma chave de armazenados num chave cofre. A propriedade de **ID da chave** é o URI para a chave no cofre chave que age como o KEK. A propriedade **EncryptedKey** contém a versão encriptada da CEK.

![Captura de ecrã que mostra os metadados de BLOBs que incluem os metadados de encriptação][1]

> [AZURE.NOTE] Se observe o construtor de BlobEncryptionPolicy, verá que pode aceitar uma chave e/ou um solucionador. Tenha em atenção que neste momento não é possível utilizar um solucionador para encriptação porque é que não está suportam uma chave predefinida.



## <a name="decrypt-blob-and-download"></a>Desencriptar blob e transferir
Desencriptação é na verdade quando utilizar as classes solucionador adequadas. O ID da chave de utilizada para encriptação está associado com o blob nos seus metadados, pelo que não existe nenhuma razão para obter a chave e lembre-se a associação entre chave e blob. Tem de Certifique-se de que a tecla permanece na chave cofre.   

A chave privada de uma chave RSA permanece na chave cofre, para que para desencriptação a ocorrer, a tecla encriptados dos metadados blob que contém o CEK é enviada para cofre chave para desencriptação.

Adicione o seguinte para desencriptar o blob que acabei de carregar.

    // In this case, we will not pass a key and only pass the resolver because
    // this policy will only be used for downloading / decrypting.
    BlobEncryptionPolicy policy = new BlobEncryptionPolicy(null, cloudResolver);
    BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

    using (var np = File.Open(@"C:\data\MyFileDecrypted.txt", FileMode.Create))
        blob.DownloadToStream(np, null, options, null);


> [AZURE.NOTE] Existem algumas outros tipos de resoluções para facilitar a gestão de chaves, incluindo: AggregateKeyResolver e CachingKeyResolver.


## <a name="use-key-vault-secrets"></a>Utilizar segredos cofre chave
A forma de utilizar uma palavra-passe com encriptação do lado do cliente é através da classe SymmetricKey porque uma palavra-passe é, essencialmente, uma chave simétrica. No entanto, tal como indicado acima, uma palavra-passe num cofre chave não mapear exatamente para um SymmetricKey. Existem algumas coisas para compreender:


- A chave num SymmetricKey tem de ser um comprimento fixo: 128, 192, 256, 384 ou 512 bits.
- A chave num SymmetricKey deve ser codificada Base64.
- Uma palavra-passe cofre chave que será utilizada como um SymmetricKey tem de ter um tipo de conteúdo de "octeto/aplicação-sequência" na chave cofre.

Eis um exemplo no PowerShell da criação de uma palavra-passe no Cofre de chave que podem ser utilizados como um SymmetricKey.
Nota: O valor codificado, $key, destina-se apenas a finalidade de demonstração sobre. No seu próprio código irá pretende gerar esta chave.

    // Here we are making a 128-bit key so we have 16 characters.
    //  The characters are in the ASCII range of UTF8 so they are
    //  each 1 byte. 16 x 8 = 128.
    $key = "qwertyuiopasdfgh"
    $b = [System.Text.Encoding]::UTF8.GetBytes($key)
    $enc = [System.Convert]::ToBase64String($b)
    $secretvalue = ConvertTo-SecureString $enc -AsPlainText -Force

    // Substitute the VaultName and Name in this command.
    $secret = Set-AzureKeyVaultSecret -VaultName 'ContoseKeyVault' -Name 'TestSecret2' -SecretValue $secretvalue -ContentType "application/octet-stream"

Na aplicação de consola, pode utilizar a mesma chamada como antes de para obter este segredo como um SymmetricKey.

    SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
        "https://contosokeyvault.vault.azure.net/secrets/TestSecret2/",
        CancellationToken.None).GetAwaiter().GetResult();

Já está. Desfrute!

## <a name="next-steps"></a>Próximos passos

Para mais informações sobre como utilizar o armazenamento do Windows Azure com c#, consulte o artigo [Biblioteca de cliente do Microsoft Azure armazenamento para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Para mais informações sobre a API do resto Blob, consulte o artigo [Blob serviço REST API](https://msdn.microsoft.com/library/azure/dd135733.aspx).

Para informações mais recentes sobre o armazenamento do Windows Azure, vá para o [Blogue da equipa do Microsoft Azure armazenamento](http://blogs.msdn.com/b/windowsazurestorage/).


<!--Image references-->
[1]: ./media/storage-encrypt-decrypt-blobs-key-vault/blobmetadata.png
