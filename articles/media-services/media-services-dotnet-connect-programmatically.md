<properties 
    pageTitle="Ligar à conta de serviços de multimédia utilizando .NET" 
    description="Este tópico demonstra como ligar aos serviços de multimédia uisng .NET." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
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


# <a name="connecting-to-media-services-account-using-media-services-sdk-for-net"></a>Ligar à conta de serviços de multimédia utilizando SDK de serviços de multimédia para .NET

> [AZURE.SELECTOR]
- [RESTO](media-services-rest-connect-programmatically.md)
- [.NET](media-services-dotnet-connect-programmatically.md)


Este tópico descreve como obter uma ligação de programação a dos serviços de multimédia do Microsoft Azure quando são programação com o SDK de serviços de multimédia para .NET.


## <a name="connecting-to-media-services"></a>Ligar aos serviços de multimédia

Para ligar a dos serviços de multimédia através de programação, pode tem ter anteriormente configurar uma conta Azure, configurou dos serviços de multimédia nessa conta e, em seguida, configurar um projeto do Visual Studio para o desenvolvimento com o SDK de serviços de multimédia para .NET. Para obter mais informações, consulte configuração para o desenvolvimento com o SDK de serviços de multimédia para .NET.

No final do processo de configuração de conta dos serviços de multimédia, obtidos os seguintes valores de ligação necessárias. Utilize estas estabelecer ligações de programação a dos serviços de multimédia.

- O nome da conta dos serviços de multimédia.

- A chave da conta dos serviços de multimédia.

Para localizar estes valores, aceda ao Portal do cmdlet do Azure, selecione a sua conta de serviço de multimédia e clique no ícone do "**GERIR chaves**" na parte inferior da janela do portal. Clicando no ícone junto a cada caixa de texto copia o valor para a área de transferência do sistema.


## <a name="creating-a-cloudmediacontext-instance"></a>Criar uma instância de CloudMediaContext

Para iniciar a programação contra dos serviços de multimédia necessita de criar uma instância de **CloudMediaContext** que representa o contexto de servidor. O **CloudMediaContext** inclui as referências às coleções de importantes, incluindo a tarefas, recursos, ficheiros, as políticas de acesso e Locator.

>[AZURE.NOTE] A classe de **CloudMediaContext** não é tópico seguro. Deve criar uma nova CloudMediaContext por tópico ou por conjunto de operações.


CloudMediaContext tem cinco construtor overloads. Recomenda-se para utilizar construtores que **MediaServicesCredentials** como um parâmetro. Para obter mais informações, consulte **Reutilizar Tokens de serviço de controlo de acesso** que se segue. 

O exemplo seguinte utiliza o construtor de CloudMediaContext(MediaServicesCredentials credentials) público:

    // _cachedCredentials and _context are class member variables. 
    _cachedCredentials = new MediaServicesCredentials(
                    _mediaServicesAccountName,
                    _mediaServicesAccountKey);
    
    _context = new CloudMediaContext(_cachedCredentials);


## <a name="reusing-access-control-service-tokens"></a>Reutilizar Tokens de serviço de controlo de acesso

Esta secção mostra como reutilizar tokens de serviço de controlo de acesso ao utilizar construtores CloudMediaContext que MediaServicesCredentials como um parâmetro.


[Controlo de acesso do Azure Active Directory](https://msdn.microsoft.com/library/hh147631.aspx) (também conhecido como serviço de controlo de acesso ou ACS) é um serviço baseado na nuvem que fornece uma forma fácil de autenticação e utilizadores de autorização para aceder à suas aplicações web. Serviços de multimédia do Microsoft Azure controla o acesso aos seus serviços apesar protocolo OAuth que requer um token de ACS. Dos serviços de multimédia recebe os tokens ACS de um servidor de autorização.

Quando desenvolver aplicações com o SDK de serviços de multimédia, pode optar por não mais fácil lidar com os tokens de uma vez que o SDK código gestores-los por si. No entanto, permitindo que o SDK totalmente gerir os tokens ACS orienta para pedidos de tokens desnecessários. Pedir tokens demora algum tempo e consome recursos de cliente e servidor. Além disso, o servidor de ACS acelera os pedidos de se a frequência for demasiado elevada. O limite é de 30 pedidos por segundo, consulte o artigo [ACS limitações do serviço](https://msdn.microsoft.com/library/gg185909.aspx) para obter mais detalhes.

Começar com o SDK de serviços de multimédia versão 3.0.0.0, pode reutilizar os tokens ACS. Os construtores **CloudMediaContext** que **MediaServicesCredentials** como um parâmetro de ativar a partilha do tokens ACS entre múltiplos contextos. A classe de MediaServicesCredentials contém as credenciais dos serviços de multimédia. Se um token de ACS está disponível e o tempo de expiração é conhecido, pode criar uma nova instância de MediaServicesCredentials com o token e transmiti-lo para o construtor de CloudMediaContext. Tenha em atenção que o SDK de serviços de multimédia atualiza automaticamente tokens sempre que expirem. Existem duas formas de reutilizar tokens de ACS, conforme mostrado nos exemplos abaixo.

- Pode colocar na cache de objecto **MediaServicesCredentials** na memória (por exemplo, uma variável de classe estática). Em seguida, passe o objeto em cache para o construtor de CloudMediaContext. O objecto MediaServicesCredentials contém um token de ACS que pode ser reutilizado se-lo ainda estiver válido. Se o token não for válido, ser atualizado pelo SDK de serviços de multimédia utilizando credenciais indicadas ao construtor de MediaServicesCredentials.

    Tenha em atenção que o objeto **MediaServicesCredentials** obtém um token válido depois do RefreshToken chama. O **CloudMediaContext** chama o método de **RefreshToken** no construtor. Se estiver a planear guardar os valores tokens um armazenamento externo, certifique-se verificar se o valor de TokenExpiration é válido antes de guardar os dados tokens. Se não for válido, chamar RefreshToken antes de colocação em cache.

        // Create and cache the Media Services credentials in a static class variable.
        _cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey);

        
        // Use the cached credentials to create a new CloudMediaContext object.
        if(_cachedCredentials == null)
        {
            _cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey);
        }
        
        CloudMediaContext context = new CloudMediaContext(_cachedCredentials);

- Também pode cache a cadeia de AccessToken e os valores de TokenExpiration. Os valores mais tarde podem ser utilizados para criar um novo objeto MediaServicesCredentials com os dados em cache tokens.  Este é especialmente útil para cenários onde o token pode segura partilhado entre vários processos ou computadores.

    Os seguinte fragmentos de código ligar os métodos de SaveTokenDataToExternalStorage, GetTokenDataFromExternalStorage e UpdateTokenDataInExternalStorageIfNeeded que não estão definidos neste exemplo. Pode definir estes métodos para armazenar, recuperar e atualizar os dados tokens um armazenamento externo. 

        CloudMediaContext context1 = new CloudMediaContext(_mediaServicesAccountName, _mediaServicesAccountKey);
        
        // Get token values from the context.
        var accessToken = context1.Credentials.AccessToken;
        var tokenExpiration = context1.Credentials.TokenExpiration;
        
        // Save token values for later use. 
        // The SaveTokenDataToExternalStorage method should check 
        // whether the TokenExpiration value is valid before saving the token data. 
        // If it is not valid, call MediaServicesCredentials’s RefreshToken before caching.
        SaveTokenDataToExternalStorage(accessToken, tokenExpiration);
        
    Utilize os valores tokens guardados para criar MediaServicesCredentials.


        var accessToken = "";
        var tokenExpiration = DateTime.UtcNow;
        
        // Retrieve saved token values.
        GetTokenDataFromExternalStorage(out accessToken, out tokenExpiration);
        
        // Create a new MediaServicesCredentials object using saved token values.
        MediaServicesCredentials credentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey)
        {
            AccessToken = accessToken,
            TokenExpiration = tokenExpiration
        };
        
        CloudMediaContext context2 = new CloudMediaContext(credentials);

    Actualize a cópia token caso o token foi atualizado pelo SDK de serviços de multimédia. 
    
        if(tokenExpiration != context2.Credentials.TokenExpiration)
        {
            UpdateTokenDataInExternalStorageIfNeeded(accessToken, context2.Credentials.TokenExpiration);
        }
        

- Se tiver várias contas dos serviços de multimédia (por exemplo, para carregar efeitos ou Geo distribuição de partilha) pode cache objetos de MediaServicesCredentials através da coleção de System.Collections.Concurrent.ConcurrentDictionary (a coleção de ConcurrentDictionary representa uma coleção de segurança do tópico de pares valor/chave que possa ser acedida por vários threads em simultâneo). Em seguida, pode utilizar o método de GetOrAdd para obter as credenciais em cache. 

        // Declare a static class variable of the ConcurrentDictionary type in which the Media Services credentials will be cached.  
        private static readonly ConcurrentDictionary<string, MediaServicesCredentials> mediaServicesCredentialsCache = 
            new ConcurrentDictionary<string, MediaServicesCredentials>();
        

        // Cache (or get already cached) Media Services credentials. Use these credentials to create a new CloudMediaContext object.
        static public CloudMediaContext CreateMediaServicesContext(string accountName, string accountKey)
        {
            CloudMediaContext cloudMediaContext;
            MediaServicesCredentials mediaServicesCredentials;
        
            mediaServicesCredentials = mediaServicesCredentialsCache.GetOrAdd(
                accountName,
                valueFactory => new MediaServicesCredentials(accountName, accountKey));
        
            cloudMediaContext = new CloudMediaContext(mediaServicesCredentials);
        
            return cloudMediaContext;
        }
        
## <a name="connecting-to-a-media-services-account-located-in-the-north-china-region"></a>Ligar a uma conta dos serviços de multimédia localizada na região Norte China

Se a sua conta estiver localizada na região China norte, utilize o construtor de seguinte:

    public CloudMediaContext(Uri apiServer, string accountName, string accountKey, string scope, string acsBaseAddress)

Por exemplo:


    _context = new CloudMediaContext(
        new Uri("https://wamsbjbclus001rest-hs.chinacloudapp.cn/API/"),
        _mediaServicesAccountName,
        _mediaServicesAccountKey,
        "urn:WindowsAzureMediaServices",
        "https://wamsprodglobal001acs.accesscontrol.chinacloudapi.cn");


## <a name="storing-connection-values-in-configuration"></a>Armazenamento de valores de ligação na configuração

É uma prática vivamente recomendada para armazenar valores de ligação, especialmente sensíveis a maiúsculas e valores de como o seu nome de conta e palavra-passe, na configuração. Além disso, é uma prática recomendada para encriptar dados sensíveis de configuração. Pode encriptar o ficheiro de configuração inteira ao utilizar o Windows ficheiro (EFS sistema). Para permitir que EFS num ficheiro, com o botão direito no ficheiro, clique em **Propriedades**e activar a encriptação no separador de definições **Avançadas** . Ou pode criar uma solução personalizada para encriptar selecionados partes de um ficheiro de configuração utilizando a configuração protegida. Consulte o artigo [encriptação configuração utilizando protegida a configuração de informações](https://msdn.microsoft.com/library/53tyfkaw.aspx).

O ficheiro de App seguinte contém os valores de ligação necessárias. Os valores na <appSettings> elemento são os valores necessários que recebeu do processo de configuração de conta dos serviços de multimédia.

    <configuration>
      <appSettings>
        <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
        <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
      </appSettings>
    </configuration>


Para obter valores de ligação de configuração, pode utilizar a classe de **ConfigurationManager** e, em seguida, atribuir os valores para campos no seu código:
    
    private static readonly string _accountName = ConfigurationManager.AppSettings["MediaServicesAccountName"];
    private static readonly string _accountKey = ConfigurationManager.AppSettings["MediaServicesAccountKey"];



##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
