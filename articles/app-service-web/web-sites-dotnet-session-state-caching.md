<properties 
    pageTitle="Estado da sessão com a cache Azure Redis na aplicação de serviço do Azure" 
    description="Saiba como utilizar o serviço de Cache Azure para suportar ASP.NET sessão Estado colocação em cache." 
    services="app-service\web" 
    documentationCenter=".net" 
    authors="Rick-Anderson" 
    manager="wpickett" 
    editor="none"/>

<tags 
    ms.service="app-service-web" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="get-started-article" 
    ms.date="06/27/2016" 
    ms.author="riande"/>


# <a name="session-state-with-azure-redis-cache-in-azure-app-service"></a>Estado da sessão com a cache Azure Redis na aplicação de serviço do Azure


Este tópico explica como utilizar o serviço de Cache do Azure Redis para o estado da sessão.

Se a sua aplicação web do ASP.NET utiliza o estado da sessão, terá de configurar um fornecedor de estado sessão externos (o serviço de Cache Redis ou um fornecedor de estado de sessão do SQL Server). Se utilizar o estado da sessão e não utilizar um fornecedor externo, será limitado para uma instância da sua aplicação web. O serviço de Cache Redis é a forma mais rápida e mais simples ativar.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

##<a id="createcache"></a>Criar a Cache
Siga [estas indicações](../cache-dotnet-how-to-use-azure-redis-cache.md#create-cache) para criar a cache.

##<a id="configureproject"></a>Adicionar o pacote de RedisSessionStateProvider NuGet para a sua aplicação web
Instalar o NuGet `RedisSessionStateProvider` pacote.  Utilize o seguinte comando para instalar a partir da consola do Gestor de pacote (**Ferramentas** > **Gestor de pacotes NuGet** > **Consola do Gestor de pacote**):

  `PM> Install-Package Microsoft.Web.RedisSessionStateProvider`
  
Para instalar a partir de **Ferramentas** > **Gestor de pacotes NuGet** > **Gerir pacotes de NugGet para solução**, procure `RedisSessionStateProvider`.

Para mais informações, consulte a [página de NuGet RedisSessionStateProvider](http://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider/ ) e [configurar o cliente de cache](../cache-dotnet-how-to-use-azure-redis-cache.md#NuGet).

##<a id="configurewebconfig"></a>Modificar o ficheiro Web. config.
Para além de fazer referências de assemblagem para a Cache, o pacote de NuGet adiciona stub entradas no ficheiro *Web. config* . 

1. Abra a *Web. config* e localize o elemento de **sessionState** .

1. Introduza os valores para `host`, `accessKey`, `port` (a porta de SSL deve ser 6380) e definir `SSL` para `true`. Podem ser obtidos estes valores a partir do pá [Azure Portal](http://go.microsoft.com/fwlink/?LinkId=529715) para a instância da cache. Para mais informações, consulte o artigo [ligar à cache](../cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-cache). Tenha em atenção que a porta de que não sejam SSL é desactivada por predefinição para novas caches. Para obter mais informações sobre como activar a porta de que não sejam SSL, consulte a secção de [Portas de acesso](https://msdn.microsoft.com/library/azure/dn793612.aspx#AccessPorts) no tópico de [Configurar uma cache no Azure Redis Cache](https://msdn.microsoft.com/library/azure/dn793612.aspx) . A marcação seguinte mostra as alterações ao ficheiro *Web. config* , especificamente as alterações à *porta*, *anfitrião*, accessKey*, e *ssl *.

          <system.web>;
            <customErrors mode="Off" />;
            <authentication mode="None" />;
            <compilation debug="true" targetFramework="4.5" />;
            <httpRuntime targetFramework="4.5" />;
            <sessionState mode="Custom" customProvider="RedisSessionProvider">;
              <providers>;  
                  <!--<add name="RedisSessionProvider" 
                    host = "127.0.0.1" [String]
                    port = "" [number]
                    accessKey = "" [String]
                    ssl = "false" [true|false]
                    throwOnError = "true" [true|false]
                    retryTimeoutInMilliseconds = "0" [number]
                    databaseId = "0" [number]
                    applicationName = "" [String]
                  />;-->;
                 <add name="RedisSessionProvider" 
                      type="Microsoft.Web.Redis.RedisSessionStateProvider" 
                      port="6380"
                      host="movie2.redis.cache.windows.net" 
                      accessKey="m7PNV60CrvKpLqMUxosC3dSe6kx9nQ6jP5del8TmADk=" 
                      ssl="true" />;
              <!--<add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" />;-->;
              </providers>;
            </sessionState>;
          </system.web>;


##<a id="usesessionobject"></a>Utilize o objeto Session no código
O passo final é começar a utilizar o objeto de sessão no seu código ASP.NET. Adicionar objetos ao estado de sessão, utilizando o método de **Session.Add** . Este método utiliza pares valor de chave para armazenar itens na cache de estado da sessão.

    string strValue = "yourvalue";
    Session.Add("yourkey", strValue);

O código seguinte obtém este valor do Estado da sessão.

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)objValue; 

Também pode utilizar a Cache Redis a objetos de cache no seu web app. Para obter mais informações, consulte o artigo [aplicação de filme MVC com Azure Redis Cache dentro de 15 minutos](https://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/).
Para obter mais detalhes sobre como utilizar o estado da sessão ASP.NET, consulte o artigo [Descrição geral de estado do ASP.NET sessão][].

>[AZURE.NOTE] Se pretender começar a aplicação de serviço de Azure antes de inscrever-se para uma conta do Azure, aceda ao [Tentar aplicação de serviço](http://go.microsoft.com/fwlink/?LinkId=523751), onde imediatamente pode criar uma aplicação web do starter curto na aplicação de serviço. Sem cartões de crédito necessários; sem compromissos.

## <a name="whats-changed"></a>O que é alterado
* Para um guia para a alteração de Web sites para a aplicação de serviço Consulte o artigo: [aplicação de serviço de Azure e respectivo impacto na existente dos serviços do Azure](http://go.microsoft.com/fwlink/?LinkId=529714)

  *Por [Almeida RTF](https://twitter.com/RickAndMSFT)*
  
  [installed the latest]: http://www.windowsazure.com/downloads/?sdk=net  
  [Descrição geral do Estado de sessão ASP.NET]: http://msdn.microsoft.com/library/ms178581.aspx

  [NewIcon]: ./media/web-sites-dotnet-session-state-caching/CacheScreenshot_NewButton.png
  [NewCacheDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CreateOptions.png
  [CacheIcon]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheIcon.png
  [NuGetDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_NuGet.png
  [OutputConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_OC_WebConfig.png
  [CacheConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheConfig.png
  [EndpointURL]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_EndpointURL.png
  [ManageKeys]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_ManageAccessKeys.png
 
