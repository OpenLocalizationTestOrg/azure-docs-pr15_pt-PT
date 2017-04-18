<properties
    pageTitle="Fornecedor de estado da cache ASP.NET sessão | Microsoft Azure"
    description="Saiba como armazenar ASP.NET estado da sessão utilizando a Cache de Redis Azure"
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
    ms.date="09/01/2016"
    ms.author="sdanie" />

# <a name="aspnet-session-state-provider-for-azure-redis-cache"></a>Fornecedor de estado de sessão ASP.NET para Azure Redis Cache

Azure Redis Cache fornece um fornecedor de estado de sessão que pode utilizar para armazenar o seu estado da sessão numa cache em vez de na memória ou numa base de dados do SQL Server. Para utilizar o fornecedor de estado sessão colocação em cache, configure primeiro a cache e, em seguida, configure a sua aplicação do ASP.NET de cache utilizando o pacote de Redis NuGet de estado de sessão de Cache.

Muitas vezes não for prático numa aplicação reais nuvem para evitar armazenar alguma forma de estado para uma sessão de utilizador, mas algumas abordagens afetar o desempenho e escalabilidade mais do que outras pessoas. Se tiver que armazenar o estado, a melhor solução é manter a quantidade de estado pequenas e guardá-la na cookies. Se o que não viável, a melhor solução seguinte é utilizar o estado da sessão ASP.NET com um fornecedor de cache distribuída, na memória. A solução pior a partir de um desempenho e perspectiva escalabilidade está a utilizar uma base de dados de segurança fornecedor de estado de sessão. Este tópico fornece orientações sobre como utilizar o fornecedor de estado de sessão do ASP.NET para Azure Redis Cache. Para obter informações sobre outras opções de estado de sessão, consulte o artigo [Opções de estado de sessão ASP.NET](#aspnet-session-state-options).

## <a name="store-aspnet-session-state-in-the-cache"></a>Armazenar o estado da sessão ASP.NET na cache

Para configurar uma aplicação de cliente no Visual Studio utilizando o pacote de Redis NuGet de estado de sessão de Cache, com o botão direito do projecto no **Explorador de soluções** e selecione **Gerir pacotes de NuGet**.

![Cache de Azure Redis gerir pacotes de NuGet](./media/cache-aspnet-session-state-provider/redis-cache-manage-nuget-menu.png)

Escreva **RedisSessionStateProvider** na caixa de texto de pesquisa, selecione-a partir dos resultados e clique em **instalar**.

>[AZURE.IMPORTANT] Se estiver a utilizar a funcionalidade clusters a partir da camada premium, tem de utilizar [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 ou superior ou uma exceção é devolvida. Esta é uma alteração de última hora; Para obter mais informações consulte o artigo [v2.0.0 força alterar detalhes](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details).

![Fornecedor de estado de sessão do Azure Redis Cache](./media/cache-aspnet-session-state-provider/redis-cache-session-state-provider.png)

O pacote de Redis NuGet de fornecedor de estado de sessão tem uma dependência no pacote StackExchange.Redis.StrongName. Se o pacote de StackExchange.Redis.StrongName não estiver presente no seu projeto será instalado. Tenha em atenção que para além do pacote de StackExchange.Redis.StrongName forte denominada também há a versão do StackExchange.Redis não-forte-com o nome. Se o projeto é utilizar a versão de StackExchange.Redis não-forte-denominada que tem de desinstalá-lo, antes ou depois de instalar o pacote de Redis NuGet de fornecedor de estado de sessão, caso contrário, irá obter conflitos de nomes no projeto. Para mais informações sobre estes pacotes, consulte o artigo [Configurar .NET clientes de cache](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

O pacote de NuGet transferências e adiciona a assemblagem necessária referências e adiciona que a seguinte adiciona a seguinte secção para o seu ficheiro de Web. config que contém a configuração necessária para a sua aplicação de ASP.NET utilizar o fornecedor Redis Cache sessão de estado.

    <sessionState mode="Custom" customProvider="MySessionStateStore">
        <providers>
        <!--
        <add name="MySessionStateStore"
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            throwOnError = "true" [true|false]
            retryTimeoutInMilliseconds = "0" [number]
            databaseId = "0" [number]
            applicationName = "" [String]
            connectionTimeoutInMilliseconds = "5000" [number]
            operationTimeoutInMilliseconds = "5000" [number]
        />
        -->
        <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false"/>
        </providers>
    </sessionState>

A secção remova fornece um exemplo de atributos e definições de exemplo para cada atributo.

Configurar os atributos com os valores a partir do seu pá cache no Microsoft Azure Portal e configurar os outros valores conforme pretender. Para obter instruções sobre como aceder ao seu propriedades de cache, consulte [Configurar Redis definições da cache](cache-configure.md#configure-redis-cache-settings).

-   **anfitrião** – especificar o seu ponto final de cache.
-   **porta** -utilizar a porta não SSL ou de SSL a porta, dependendo das definições de ssl.
-   **accessKey** – utilizar a chave primária ou secundária para a cache.
-   **ssl** – true se quiser proteger as comunicações de cache/cliente com ssl; caso contrário false. Certifique-se de que especifique a porta correta.
    -   A porta de que não sejam SSL é desativada por predefinição para novas caches. Especifique verdadeiro para que esta definição utilizar a porta SSL. Para obter mais informações sobre como activar a porta de que não sejam SSL, consulte a secção de [Portas de acesso](cache-configure.md#access-ports) no tópico de [Configurar uma cache](cache-configure.md) .
-   **throwOnError** – true se pretender que uma exceção para ser devolvido em caso de falha ou FALSO se pretender que a operação a falha em silêncio. Pode procurar uma falha ao selecionar a propriedade Microsoft.Web.Redis.RedisSessionStateProvider.LastException estática. A predefinição é verdadeiro.
-   **retryTimeoutInMilliseconds** – operações falhar são repetidas durante este intervalo, especificado em milissegundos. À primeira repetição ocorre após a 20 milissegundos e, em seguida, o número de tentativas ocorre cada segundo até que o intervalo de retryTimeoutInMilliseconds expira. Imediatamente após este intervalo, a operação é repetida uma vez final. Se a operação ainda falhar, a exceção é devolvida novamente para o autor da chamada, dependendo na definição de throwOnError. O valor predefinido é 0, que significa que não tentativas.
-   **databaseId** – Especifica a utilizar para a base de dados em cache de saída de dados. Se não for especificado, é utilizado o valor predefinido de 0.
-   **applicationName** – teclas são armazenadas no redis como `{<Application Name>_<Session ID>}_Data`. Permite que várias aplicações partilhar a mesma chave. Este parâmetro é opcional e se não o fornecem um valor predefinido é utilizado.
-   **connectionTimeoutInMilliseconds** – esta definição permite-lhe substituir a definição de connectTimeout no cliente do StackExchange.Redis. Se não for especificado, é utilizada a predefinição de connectTimeout de 5000. Para mais informações, consulte o artigo [StackExchange.Redis modelo de configuração](http://go.microsoft.com/fwlink/?LinkId=398705).
-   **operationTimeoutInMilliseconds** – esta definição permite-lhe substituir a definição de syncTimeout no cliente do StackExchange.Redis. Se não for especificado, é utilizada a predefinição de syncTimeout de 1000. Para mais informações, consulte o artigo [StackExchange.Redis modelo de configuração](http://go.microsoft.com/fwlink/?LinkId=398705).

Para mais informações sobre estas propriedades, consulte o artigo anúncio de mensagem de blogue original fornecedor [anunciar o ASP.NET sessão de estado para Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).

Não se esqueça de comentar a padrão InProc sessão Estado fornecedor secção no seu Web. config.

    <!-- <sessionState mode="InProc"
         customProvider="DefaultSessionProvider">
         <providers>
            <add name="DefaultSessionProvider"
                  type="System.Web.Providers.DefaultSessionStateProvider,
                        System.Web.Providers, Version=1.0.0.0, Culture=neutral,
                        PublicKeyToken=31bf3856ad364e35"
                  connectionStringName="DefaultConnection" />
          </providers>
    </sessionState> -->

Assim que são executados estes passos, a aplicação está configurada para utilizar o fornecedor Redis Cache sessão de estado. Quando utiliza o estado da sessão na sua aplicação, serão armazenado numa instância Azure Redis Cache.

>[AZURE.NOTE] Note que os dados armazenados na cache têm de ser serializáveis, ao contrário os dados que podem ser armazenados numa predefinido na memória ASP.NET sessão Distrito fornecedor. Quando é utilizado o fornecedor de estado de sessão para Redis, certifique-se de que os tipos de dados que estão a ser armazenados num Estado da sessão estão serializáveis.

## <a name="aspnet-session-state-options"></a>Opções de estado de sessão ASP.NET

- No fornecedor de estado da sessão de memória - este fornecedor armazena o estado da sessão na memória. A vantagem de utilizar este fornecedor é simples e rápida. No entanto não consigo dimensionar as suas aplicações Web, se estiver a utilizar no fornecedor de memória uma vez que não é distribuído.

- Fornecedor de estado de sessão do SQL Server - este fornecedor armazena o estado da sessão no Sql Server. Deve utilizar este fornecedor se pretender manter o estado da sessão de um armazenamento persistente. Pode dimensionar a aplicação Web do mas utilizando o Sql Server para sessão irá ter um impacto no desempenho na sua aplicação Web.

- Distribuído na memória sessão Estado fornecedor como Redis Cache Estado fornecedor sessão - este fornecedor dá-lhe o melhor da ambas as mundo. A aplicação Web do pode ter um fornecedor de estado de sessão simples, rápida e dimensionáveis. No entanto tem de ter em consideração que este fornecedor armazena o estado da sessão numa Cache e a aplicação tem de ter em consideração todas as características associadas ao falar com na memória Cache distribuída como falhas de rede breves. Para melhores práticas na utilização do Cache, consulte [orientações de colocação em cache](../best-practices-caching.md) Microsoft Patterns & práticas [orientações de implementação e estrutura de aplicação do Azure na nuvem](https://github.com/mspnp/azure-guidance).

Para mais informações sobre o estado da sessão e outros procedimentos recomendados, consulte o artigo [Práticas recomendadas de desenvolvimento Web (edifício reais nuvem aplicações com o Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices).

## <a name="next-steps"></a>Próximos passos

Consulte o artigo o [Fornecedor de Cache de saída de ASP.NET para Azure Redis Cache](cache-aspnet-output-cache-provider.md).
