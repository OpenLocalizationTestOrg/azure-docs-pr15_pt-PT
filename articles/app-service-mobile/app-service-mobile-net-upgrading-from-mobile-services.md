<properties
    pageTitle="Atualizar a partir de serviços móveis para o Azure de aplicação de serviço"
    description="Saiba como atualizar facilmente a sua aplicação de serviços móveis para uma aplicação do aplicação do serviço móvel"
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="upgrade-your-existing-net-azure-mobile-service-to-app-service"></a>Atualizar o serviço móvel do Azure de .NET existente para o serviço de aplicação

Aplicação de serviço móvel é uma forma nova para criar aplicações móveis através do Microsoft Azure. Para obter mais informações, consulte o artigo [o que são aplicações Mobile?].

Este tópico descreve como atualizar uma aplicação de back-end .NET existente a partir do Azure Mobile Services para uma nova aplicação Serviço móvel aplicações. Enquanto executar esta atualização, a aplicação de serviços móveis existente pode continuar a funcionar.   Se precisar de atualizar uma aplicação de back-end Node.js, consulte [atualizar os seus serviços de Mobile Node.js](./app-service-mobile-node-backend-upgrading-from-mobile-services.md).

Quando é atualizada back-end móvel para a aplicação de serviço de Azure, tem acesso a todas as funcionalidades de aplicação de serviço e são faturada de acordo com a [aplicação de serviço de preços], não os serviços de Mobile preços.

##<a name="migrate-vs-upgrade"></a>Migrar vs. atualização

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

>[AZURE.TIP] É recomendado que [executar uma migração](app-service-mobile-migrating-from-mobile-services.md) antes de entrar através de uma atualização. Desta forma, pode colocar ambas as versões da sua aplicação no mesmo plano de serviço de aplicação e implicam sem custos adicionais.

###<a name="improvements-in-mobile-apps-net-server-sdk"></a>Melhorias no server Mobile aplicações .NET SDK

Atualizar para o novo [Mobile aplicações SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) fornece os seguintes benefícios:

- Mais flexibilidade sobre as dependências de NuGet. Ambiente de alojamento já não fornece as suas próprias versões de pacotes NuGet, para que possa utilizar versões compatíveis alternativos. No entanto, se existirem novas bugfixes crítico ou atualizações de segurança para o Mobile Server SDK ou dependências, tem de atualizar o seu serviço manualmente.

- Mais flexibilidade no SDK móvel. Pode controlar explicitamente quais as funcionalidades e rotas estão configuradas, tais como autenticação, tabela APIs e o ponto final de registo de push. Para saber mais, consulte o artigo [como utilizar o servidor de .NET SDK para as aplicações móveis do Azure](app-service-mobile-net-upgrading-from-mobile-services.md#server-project-setup).

- Suporte para outros tipos de ASP.NET do project e rotas. Agora pode alojar controladores MVC e Web API no mesmo projecto como o seu projeto móvel back-end.

- Suporte para novas funcionalidades de autenticação de aplicação de serviço, que permitem-lhe utilizar uma configuração de autenticação comum nos seus web e aplicações móveis.

##<a name="overview"></a>Descrição geral de atualização básica

Em muitos casos, atualizar será tão simple como mudar para o novo servidor de aplicações Mobile SDK e voltar a publicar o código para uma nova instância de aplicação Mobile. Não existem, no entanto alguns cenários que irão requerer alguma configuração adicional, tal como cenários avançadas de autenticação e como trabalhar com agendada de tarefas. Cada um destes é abrangida nas secções posteriores.

>[AZURE.TIP] É aconselhável que ler e compreender o resto deste tópico completamente antes de iniciar uma atualização. Tome nota das funcionalidades do que utilizar realçados abaixo.

O cliente de serviços de Mobile SDK são **não** é compatível com o servidor de aplicações Mobile SDK novo. Para fornecer continuidade do serviço para a sua aplicação, que não deve publicá alterações a um site atualmente servir clientes publicados. Em vez disso, deverá criar uma nova aplicação móvel que serve um duplicado. Pode colocar esta aplicação no mesmo plano da aplicação de serviço para evitar sempre custo financeiro adicional.

Em seguida, terá de duas versões da aplicação: aquele que permanecer na mesma e serve publicados aplicações numa silvestres e outros que, em seguida, pode atualizar e destino com uma versão nova do cliente. Pode mover e testar o seu código ao seu ritmo, mas deve certificar-se de que quaisquer correções de erros que efetuar são aplicadas ao ambos. Uma vez sentir-se que um número de aplicações no meio natural tem atualizadas para a versão mais recente do cliente pretendido, pode eliminar a aplicação migrada original se que pretende.

O destaque completo para o processo de atualização é da seguinte forma:

1. Criar uma nova aplicação móvel
2. Actualizar o projecto para utilizar o novo SDK do servidor
3. Uma nova versão da sua aplicação de cliente
4. (Opcional) Eliminar a instância migrada original

##<a name="mobile-app-version"></a>Criar uma segunda instância da aplicação
Atualizar o primeiro passo é criar o recurso de aplicação Mobile qual será o anfitrião a nova versão da sua aplicação. Se já tiver migrado um serviço móvel existente, que irá que pretende criar esta versão no plano de alojamento do mesmo. Abra o [portal do Azure] e navegue para a sua aplicação migrada. Tome nota do plano de serviço de aplicação estiver em execução no.

Em seguida, crie a segunda instância da aplicação ao seguir as [instruções de criação de back-end .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app). Quando lhe for pedido para selecionar o plano de serviço de aplicação ou "alojamento plano" Escolher o plano da sua aplicação migrado.

Será provável que pretende utilizar a mesma base de dados e notificação concentrador conforme fez num serviços Mobile. Pode copie estes valores abrindo o [portal do Azure] e navegar para a aplicação original, em seguida, clique em **Definições** > **definições da aplicação**. Em **Cadeias de ligação**, copie `MS_NotificationHubConnectionString` e `MS_TableConnectionString`. Navegue para o novo site de atualização e cole-as no, substituir qualquer valores existentes. Repita este processo para quaisquer outras definições de aplicação às suas necessidades de aplicação. Se não utilizar um serviço migradas, pode ler cadeias de ligação e as definições da aplicação a partir do separador **Configurar** da secção serviços Mobile do [Azure portal clássica].

Fazer uma cópia do projeto ASP.NET para a sua aplicação e publicá-lo para o novo site. Utilizar uma cópia da sua aplicação de cliente atualizada com o novo URL, valide que tudo está a funcionar como esperado.

## <a name="updating-the-server-project"></a>Atualizar o project server

Aplicações móveis fornece um novo [Mobile aplicação Server SDK] que fornece muitas das mesmas funcionalidades que o tempo de execução de serviços móveis. Em primeiro lugar, deve remover todas as referências para os pacotes de serviços móveis. No Gestor de pacote NuGet, procure `WindowsAzure.MobileServices.Backend`. A maioria das aplicações irão ver várias pacotes aqui, incluindo `WindowsAzure.MobileServices.Backend.Tables` e `WindowsAzure.MobileServices.Backend.Entity`. Neste caso, iniciar com o pacote mais baixo na árvore dependência, tal como `Entity`e removê-lo. Quando lhe for pedido, não remova todos os pacotes de dependentes. Repita este processo até ter removido `WindowsAzure.MobileServices.Backend` própria.

Neste momento tem um projeto que já não referencia SDK de serviços móveis.

Seguinte irá adicionar referências o SDK de aplicações móveis. Esta atualização, os programadores a maior parte dos irão pretende transferir e instalar o `Microsoft.Azure.Mobile.Server.Quickstart` compactar, tal como isto puxa todo o conjunto necessário.

Haverá bastante alguns compilador erros resultantes de diferenças entre os SDK, mas estes são fácil para o endereço e que estejam tapados do resto nesta secção.

### <a name="base-configuration"></a>Configuração base

Em seguida, nos WebApiConfig.cs, pode substituir:

        // Use this class to set configuration options for your mobile service
        ConfigOptions options = new ConfigOptions();

        // Use this class to set WebAPI configuration options
        HttpConfiguration config = ServiceConfig.Initialize(new ConfigBuilder(options));

com

        HttpConfiguration config = new HttpConfiguration();
        new MobileAppConfiguration()
            .UseDefaultConfiguration()
        .ApplyTo(config);

>[AZURE.NOTE] Se pretender obter mais informações sobre o novo servidor .NET SDK e como adicionar/remover funcionalidades da sua aplicação, consulte o tópico de [como utilizar o servidor de .NET SDK] .

Se a sua aplicação torna utilizar as funcionalidades de autenticação, também terá de registar um software OWIN intermédio. Neste caso, deverá mover o código de configuração acima para uma nova classe de arranque OWIN.

1. Adicionar o pacote de NuGet `Microsoft.Owin.Host.SystemWeb` se não estiver já incluído no seu projeto.
2. No Visual Studio, clique com o botão direito do rato no seu projeto e selecione **Adicionar** -> **Novo Item**. Selecione **Web** -> **gerais** -> **classe de arranque OWIN**.
3. Mova o código acima para MobileAppConfiguration a partir do `WebApiConfig.Register()` para o `Configuration()` método da sua nova classe de arranque.

Certifique-se a `Configuration()` método termina com:

        app.UseWebApi(config)
        app.UseAppServiceAuthentication(config);

Existem alterações adicionais relacionados com autenticação que estão disponíveis na secção autenticação completa abaixo.

### <a name="working-with-data"></a>Trabalhar com dados

Serviços de Mobile, no nome da aplicação móvel servida como o nome de esquema predefinido na configuração do quadro de entidade.

Para se certificar de que tem o mesmo esquema a ser referenciado como antes, utilize o seguinte procedimento para definir o esquema na DbContext para a sua aplicação:

        string schema = System.Configuration.ConfigurationManager.AppSettings.Get("MS_MobileServiceName");

Certifique-se de que tem MS_MobileServiceName definir se fizer anteriores. Também pode fornecer outro nome do esquema se a sua aplicação tiver personalizado isto anteriormente.

### <a name="system-properties"></a>Propriedades do sistema

#### <a name="naming"></a>Atribuição de nomes

No servidor do Azure Mobile serviços SDK, propriedades do sistema sempre contêm um caráter de sublinhado duplo (`__`) prefixo para as propriedades:

- __createdAt
- __updatedAt
- __deleted
- __version

O cliente de serviços móveis SDK ter lógica especial para analisar as propriedades do sistema neste formato.

Em aplicações do Azure Mobile, propriedades do sistema já não têm um formato especial em tem os seguintes nomes:

- createdAt
- updatedAt
- eliminados
- versão

O cliente de aplicações Mobile SDK utilize os novos nomes de propriedades do sistema, para que não são necessárias ao código do cliente alterações. No entanto, se estiver a fazer chamadas de resto no seu serviço diretamente, em seguida, deverá alterar as suas consultas em conformidade.

#### <a name="local-store"></a>Arquivo local

As alterações aos nomes das propriedades do sistema significam que uma base de dados local de sincronização offline para serviços Mobile não é compatível com aplicações móveis. Se possível, deverá evitar actualizar aplicações a partir de serviços móveis às aplicações móveis até depois de alterações pendentes tem sido enviadas para o servidor de cliente. Em seguida, a aplicação atualizada deve utilizar um novo nome de ficheiro de base de dados.

Se uma aplicação de cliente é atualizada da versão serviços Mobile às aplicações móveis apesar de existirem alterações offline pendentes na fila de operação, em seguida, a base de dados do sistema têm de ser atualizada para utilizar os novos nomes de coluna. IOS, isto é possível utilizar migrações lightweight para alterar os nomes das colunas. No Android e o cliente gerido .NET, deve escrever SQL personalizada para mudar o nome das colunas para as suas tabelas de objeto de dados.

IOS, deverá alterar o esquema de dados principais para as entidades de dados para que correspondam às seguintes opções. Tenha em atenção que as propriedades `createdAt`, `updatedAt` e `version` já não tiver um `ms_` prefixo:

| Atributo |  Tipo   | Nota                                                 |
|---------- |  ------ | -----------------------------------------------------|
| ID        | Cadeia, marcadas como necessária  | chave primária na loja remota         |
| createdAt | Data    | mapas (opcionais) para a propriedade do sistema createdAt         |
| updatedAt | Data    | mapas (opcionais) para a propriedade do sistema updatedAt         |
| versão   | Cadeia  | (opcional) utilizado para detetar conflitos, mapas à versão |

#### <a name="querying-system-properties"></a>Consultar as propriedades do sistema

Serviços de Mobile Azure, propriedades do sistema não lhe forem enviadas por predefinição, mas apenas quando forem pedidas através da cadeia de consulta `__systemProperties`. Em contrapartida, no sistema de aplicações do Azure Mobile propriedades estão **sempre selecionada** uma vez que fazem parte do modelo de objeto SDK do servidor.

Esta alteração principalmente impactos implementações personalizadas dos gestores de domínio, tal como as extensões de `MappedEntityDomainManager`. Serviços de telemóvel, se um cliente pede nunca qualquer propriedades do sistema, é possível utilizar um `MappedEntityDomainManager` que realmente não mapear todas as propriedades. No entanto, nas aplicações do Azure Mobile, estas propriedades mapeadas vai originar um erro em consultas de obter.

A forma mais fácil para resolver o problema é modificar o seu DTOs para que herdam de `ITableData` em vez de `EntityData`. Em seguida, adicione o `[NotMapped]` atributo para os campos que devem ser omitidos.

Por exemplo, define o seguinte `TodoItem` com sem as propriedades do sistema:

    using System.ComponentModel.DataAnnotations.Schema;

    public class TodoItem : ITableData
    {
        public string Text { get; set; }

        public bool Complete { get; set; }

        public string Id { get; set; }

        [NotMapped]
        public DateTimeOffset? CreatedAt { get; set; }

        [NotMapped]
        public DateTimeOffset? UpdatedAt { get; set; }

        [NotMapped]
        public bool Deleted { get; set; }

        [NotMapped]
        public byte[] Version { get; set; }
    }

Nota: se obter erros no `NotMapped`, adicione uma referência para a assemblagem `System.ComponentModel.DataAnnotations`.

### <a name="cors"></a>CORS

Mobile serviços incluídos algumas suporte para CORS por moldagem a solução ASP.NET CORS. Esta camada de moldagem foi removida para dar o programador mais controlo, para que diretamente pode tirar partido [Do ASP.NET CORS de suporte](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api).

São as áreas principais preocupações se utilizar CORS de que o `eTag` e `Location` cabeçalhos tem de ser permitidos por ordem para o cliente SDK funcione corretamente.

### <a name="push-notifications"></a>Notificações de emissão
Para push, o item principal que poderá encontrar em falta a partir do SDK do servidor é a classe de PushRegistrationHandler. Os registos são processados de forma ligeiramente diferente em aplicações Mobile e registos tagless são ativados por predefinição. Gerir as etiquetas pode ser feito usando APIs personalizados. Consulte as instruções [registar-se para etiquetas](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags) para obter mais informações.

### <a name="scheduled-jobs"></a>Tarefas agendadas
Tarefas agendadas não foram criadas para as aplicações do telemóvel, para que quaisquer tarefas existentes que tenha no seu back-end .NET terá de ser actualizado individualmente. É uma das opções criar uma agendada [Web tarefa] no site de código de aplicação Mobile. Também pode configurar um controlador que detém o código de tarefa e configurar o [Scheduler do Azure] para acertar esse ponto final na agenda esperada.

### <a name="miscellaneous-changes"></a>Alterações diversas
Todos os ApiControllers que serão consumidas por um cliente móvel agora tem de ter o `[MobileAppController]` atributo. Esta já não se encontra incluídos por predefinição, por isso, que outros ApiControllers para ir afetados pelos formatters móveis.

O `ApiServices` objeto já não faz parte do SDK. Para aceder às definições de aplicação Mobile, pode utilizar o seguinte procedimento:

    MobileAppSettingsDictionary settings = this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

Da mesma forma, registo é agora obtido através de escrita de rastreio ASP.NET padrão:

    ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
    traceWriter.Info("Hello, World");  

##<a name="authentication"></a>Considerações de autenticação

Os componentes de autenticação de serviços móveis agora tenham sido movidos para a funcionalidade de serviço de aplicação autenticação/autorização. Pode obter informações sobre como activar isto para o seu site, lendo o tópico [Adicionar autenticação para a sua aplicação móvel](app-service-mobile-ios-get-started-users.md) .

Para alguns fornecedores, tal como AAD, Facebook e o Google, deverá conseguir aproveitar o registo existente a partir da sua aplicação de cópia. Basta necessário navegue até ao portal do fornecedor de identidade e adicionar um novo URL de redirecionamento para o registo. Em seguida, configure autenticação/autorização de aplicação de serviço com o ID de cliente e o segredo.

### <a name="controller-action-authorization"></a>Autorização de ação controlador
As ocorrências da `[AuthorizeLevel(AuthorizationLevel.User)]` atributo agora tem de ser alterado para utilizar o ASP.NET padrão `[Authorize]` atributo. Para além disso, controladores estão agora acesso anónimo por predefinição, tal como outras aplicações do ASP.NET.
Se estivesse a utilizar uma das outras opções AuthorizeLevel, tal como administrador ou aplicação, tenha em atenção que estes são descontinuadas. Em vez disso, pode configurar o AuthorizationFilters para segredos partilhados ou configurar um capital de serviço AAD para ativar o serviço de serviço de chamadas em segurança.

### <a name="getting-additional-user-information"></a>Obter informações de utilizador adicionais

Pode obter informações do utilizador adicionais, incluindo tokens de acesso através de `GetAppServiceIdentityAsync()` método:

        FacebookCredentials creds = await this.User.GetAppServiceIdentityAsync<FacebookCredentials>();

Para além disso, se a sua aplicação leva-o até dependências utilizador IDs, tal como armazená-los numa base de dados, é importante ter em atenção que os IDs de utilizador entre os serviços de Mobile e aplicações do serviço de aplicação Mobile são diferentes. Pode continuar a obter o ID de utilizador de serviços móveis, embora. Todas as subclasses ProviderCredentials têm uma propriedade de ID de utilizador. Por isso, continuar a partir de exemplo antes de:

        string mobileServicesUserId = creds.Provider + ":" + creds.UserId;

Se a sua aplicação manter quaisquer dependências de IDs de utilizador, é importante que quiser aproveitar o mesmo registo com um fornecedor de identidades se possível. IDs de utilizador, normalmente, estão limitadas para o registo de aplicação que foi utilizado para que introduzir um novo registo poderia criar problemas com a correspondência de utilizadores para os respetivos dados.

### <a name="custom-authentication"></a>Autenticação personalizada

Se a sua aplicação utilizar uma solução de autenticação personalizada, irá querer para se certificar de que o site atualizado tem acesso ao sistema. Siga as instruções novas para autenticação personalizada na [Descrição geral do .NET server SDK] para integrar a solução. Tenha em atenção que os componentes de autenticação personalizada ainda estão no pré-visualização.

##<a name="updating-clients"></a>Atualizar os clientes
Assim que tiver um operacional back-end de aplicação Mobile, pode trabalhar numa nova versão da sua aplicação de cliente que consome-lo. Aplicações móveis também inclui uma versão nova do cliente SDK e semelhante a atualização do servidor acima, é necessário remover todas as referências para o SDK de serviços móveis antes de instalar as versões de aplicações Mobile.

Uma das principais alterações entre as versões é que os construtores já não requerem uma tecla de aplicação. É agora basta passar o URL da sua aplicação móvel. Por exemplo, nos clientes .NET, o `MobileServiceClient` construtor é agora:

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net", // URL of the Mobile App
        );

Pode ler sobre como instalar os SDK novos e utilizar a nova estrutura através das ligações em baixo:

- [iOS versão 3.0.0 ou posterior](app-service-mobile-ios-how-to-use-client-library.md)
- [.NET (Windows/Xamarin) versão 2.0.0 ou posterior](app-service-mobile-dotnet-how-to-use-client-library.md)

Se a aplicação torna utilizar de notificações push, tome nota das instruções registo específico para cada plataforma, tal como tem ocorrido existem algumas alterações, assim.

Quando tiver a versão nova do cliente pronto, experimente contra o project server atualizado. Após a validação que funciona, pode Liberte uma nova versão da sua aplicação para clientes. Eventualmente, assim que os seus clientes tiveram a oportunidade de receber estas atualizações, pode eliminar a versão de serviços móveis da sua aplicação. Neste momento, tiver atualizado completamente a uma aplicação do aplicação do serviço móvel utilizando o servidor de aplicações Mobile SDK mais recente.

<!-- URLs. -->

[Portal do Azure]: https://portal.azure.com/
[Azure portal clássico]: https://manage.windowsazure.com/
[O que são aplicações Mobile?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /en-us/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Servidor de aplicação móvel SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Programador do Azure]: /en-us/documentation/services/scheduler/
[Tarefa de Web]: ../app-service-web/websites-webjobs-resources.md
[Como utilizar o servidor de .NET SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[Aplicação de serviço de preços]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[Descrição geral do .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
