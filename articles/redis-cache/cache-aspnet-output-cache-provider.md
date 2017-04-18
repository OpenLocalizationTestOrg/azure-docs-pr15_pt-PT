<properties
    pageTitle="Fornecedor de Cache de saída do ASP.NET cache"
    description="Aprenda a cache de saída da página ASP.NET utilizando a Cache de Redis Azure"
    services="redis-cache"
    documentationCenter="na"
    authors="steved0x"
    manager="douge"
    editor="tysonn" />
<tags
    ms.service="cache"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="09/27/2016"
    ms.author="sdanie" />

# <a name="aspnet-output-cache-provider-for-azure-redis-cache"></a>Fornecedor de Cache de saída do ASP.NET para Azure Redis Cache

O fornecedor de Cache de saída Redis é um dispositivo de armazenamento de out of process para dados da cache de saída. Estes dados são especificamente para respostas HTTP completos (a cache de saída da página). O fornecedor ligo o novo saída cache expansão ponto do fornecedor que foi introduzido numa ASP.NET 4.

Para utilizar o fornecedor de Cache de saída Redis, configure primeiro a cache e, em seguida, configure a sua aplicação do ASP.NET utilizando o pacote de Redis NuGet de fornecedor de Cache de saída. Este tópico fornece orientações sobre como configurar a sua aplicação para utilizar o fornecedor de Cache de saída Redis. Para mais informações sobre como criar e configurar uma instância do Azure Redis Cache, consulte o artigo [criar uma cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## <a name="store-aspnet-page-output-in-the-cache"></a>Armazenar saída da página ASP.NET na cache

Para configurar uma aplicação de cliente no Visual Studio utilizando o pacote de Redis NuGet de fornecedor de Cache de saída, com o botão direito do projecto no **Explorador de soluções** e selecione **Gerir pacotes de NuGet**.

![Cache de Azure Redis gerir pacotes de NuGet](./media/cache-aspnet-output-cache-provider/redis-cache-manage-nuget-menu.png)

Escreva **RedisOutputCacheProvider** na caixa de texto de pesquisa, selecione-a partir dos resultados e clique em **instalar**.

![Azure Redis fornecedor de Cache de saída de Cache](./media/cache-aspnet-output-cache-provider/redis-cache-page-output-provider.png)

O pacote de Redis NuGet de fornecedor de Cache de saída tem uma dependência no pacote StackExchange.Redis.StrongName. Se o pacote de StackExchange.Redis.StrongName não estiver presente no seu projeto será instalado. Tenha em atenção que para além do pacote de StackExchange.Redis.StrongName forte denominada também há a versão do StackExchange.Redis não-forte-com o nome. Se o projeto é utilizar a versão de StackExchange.Redis não-forte-denominada que tem de desinstalá-lo, antes ou depois de instalar o pacote de Redis NuGet de fornecedor de Cache de saída, caso contrário, irá obter conflitos de nomes no projeto. Para mais informações sobre estes pacotes, consulte o artigo [Configurar .NET clientes de cache](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

O pacote de NuGet transferências e adiciona as referências de assemblagem necessária e adiciona a seguinte secção para o seu ficheiro de Web. config que contém a configuração necessária para a sua aplicação de ASP.NET utilizar o fornecedor de Cache de saída Redis.

    <caching>
      <outputCachedefault Provider="MyRedisOutputCache">
        <providers>
          <!--
          <add name="MyRedisOutputCache"
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            databaseId = "0" [number]
            applicationName = "" [String]
            connectionTimeoutInMilliseconds = "5000" [number]
            operationTimeoutInMilliseconds = "5000" [number]
          />
          -->
          <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider" host="127.0.0.1" accessKey="" ssl="false"/>
        </providers>
      </outputCache>
    </caching>

A secção remova fornece um exemplo de atributos e definições de exemplo para cada atributo.

Configurar os atributos com os valores a partir do seu pá cache no portal do Microsoft Azure e configurar os outros valores conforme pretender. Para obter instruções sobre como aceder ao seu propriedades de cache, consulte [Configurar Redis definições da cache](cache-configure.md#configure-redis-cache-settings).

-   **anfitrião** – especificar o seu ponto final de cache.
-   **porta** -utilizar a porta não SSL ou de SSL a porta, dependendo das definições de ssl.
-   **accessKey** – utilizar a chave primária ou secundária para a cache.
-   **ssl** – true se quiser proteger as comunicações de cache/cliente com ssl; caso contrário false. Certifique-se de que especifique a porta correta.
    -   A porta de que não sejam SSL é desativada por predefinição para novas caches. Especifique verdadeiro para que esta definição utilizar a porta SSL. Para obter mais informações sobre como activar a porta de que não sejam SSL, consulte a secção de [Portas de acesso](cache-configure.md#access-ports) no tópico de [Configurar uma cache](cache-configure.md) .
-   **databaseId** – especificados a base de dados para utilizar em dados de saída da cache. Se não for especificado, é utilizado o valor predefinido de 0.
-   **applicationName** – teclas são armazenadas no redis como <AppName>_<SessionId>_Data. Permite que várias aplicações partilhar a mesma chave. Este parâmetro é opcional e se não o fornecem um valor predefinido é utilizado.
-   **connectionTimeoutInMilliseconds** – esta definição permite-lhe substituir a definição de connectTimeout no cliente do StackExchange.Redis. Se não for especificado, é utilizada a predefinição de connectTimeout de 5000. Para mais informações, consulte o artigo [StackExchange.Redis modelo de configuração](http://go.microsoft.com/fwlink/?LinkId=398705).
-   **operationTimeoutInMilliseconds** – esta definição permite-lhe substituir a definição de syncTimeout no cliente do StackExchange.Redis. Se não for especificado, é utilizada a predefinição de syncTimeout de 1000. Para mais informações, consulte o artigo [StackExchange.Redis modelo de configuração](http://go.microsoft.com/fwlink/?LinkId=398705).

Adicione uma diretiva OutputCache a cada página para o qual pretende o resultado em cache.

    <%@ OutputCache Duration="60" VaryByParam="*" %>

Neste exemplo os dados da página em cache permanecerá na cache para 60 segundos e uma versão diferente da página é colocada em cache para cada combinação de parâmetro. Para mais informações sobre a diretiva de OutputCache, consulte o artigo [@OutputCache](http://go.microsoft.com/fwlink/?linkid=320837).

Assim que são executados estes passos, a aplicação está configurada para utilizar o fornecedor de Cache de saída Redis.

## <a name="next-steps"></a>Próximos passos

Consulte o artigo o [Fornecedor de estado do ASP.NET sessão para Azure Redis Cache](cache-aspnet-session-state-provider.md).
