<properties
    pageTitle="O que são aplicações Mobile"
    description="Saiba que vantagens é que o serviço de aplicação trazer para as aplicações móveis do enterprise."
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="yochayk"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="getting-started"> </a>o que é aplicações Mobile?

Azure de aplicação de serviço é uma totalmente geridas [plataforma como um serviço](https://azure.microsoft.com/overview/what-is-paas/) (PaaS) oferta para programadores profissionais que relaciona um conjunto avançado de cenários de integração e capacidades Web, móvel. *Aplicações móveis* na *Aplicação de serviço de Azure* fornecem uma plataforma de desenvolvimento da aplicação móvel altamente dimensionáveis, globalmente disponível para os programadores de empresa e integradores que relaciona um conjunto avançado de funcionalidades para programadores móveis.

![Aplicações móveis](./media/app-service-mobile-value-prop/overview.png)

##<a name="why-mobile-apps"></a>Porque é que aplicações móveis?
*Aplicações móveis* na *Aplicação de serviço de Azure* oferece uma plataforma de desenvolvimento da aplicação móvel altamente dimensionáveis, globalmente disponível para os programadores de empresa e integradores que relaciona um conjunto avançado de funcionalidades para programadores móveis. Com aplicações Mobile, pode:

- **Construir nativas e cruzada aplicações de plataforma** - se estiver a compilar nativo iOS, Android e Windows aplicações ou em diferentes plataformas Xamarin ou Cordova (Phonegap) aplicações, pode tirar partido da aplicação de serviço utilizando SDK nativa.
- **Ligar ao seu sistemas enterprise** - com aplicações móveis, pode adicionar o início de sessão da empresa em minutos e ligar a sua empresa no local ou na nuvem recursos.
- **Criar aplicações offline pronto para com a sincronização de dados** - tornar força de trabalho móvel produtivo pela criação de aplicações que funcionam offline e utilizar aplicações móveis para sincronizar dados em segundo plano quando existe conectividade com qualquer uma das suas origens de dados empresariais ou SaaS APIs.
- **Notificações push para milhões em segundos** - contrate os seus clientes com notificações push instantâneas em qualquer dispositivo, personalizada para as suas necessidades, enviadas quando a hora é à direita.

## <a name="mobile-app-features"></a>Funcionalidades de aplicação móvel
As seguintes funcionalidades são importantes para o desenvolvimento de dispositivos móvel com capacidade de nuvem:

- **Autenticação e autorização** - selecione a partir de uma lista crescente da fornecedores de identidade, incluindo o Azure Active Directory para a autenticação de empresa, assim como fornecedores de rede sociais como o Facebook, Google, Twitter e Account Microsoft.  Aplicações do Azure Mobile fornece um serviço de OAuth 2.0 para cada fornecedor.  Também pode integrar o SDK para o fornecedor de identidade para funcionalidades específicas do fornecedor.

  Descubra mais sobre os nossos [funcionalidades de autenticação].

- O **Acesso a dados** - as aplicações móveis do Azure fornece mobile amigável OData v3 origem de dados ligada a SQL Azure ou um SQL Server no local.  Este serviço pode basear Framework entidade, permitindo-lhe facilmente integrar com outros NoSQL e fornecedores de dados do SQL, incluindo os fornecedores de [Armazenamento de tabela do Windows Azure], MongoDB, [DocumentDB] e SaaS API como o Office 365 e Salesforce.com.
- **Sincronização offline** - torne de Our cliente SDK-lo a criar robustas e da aplicações móveis que funcionam com um de dados offline mais facilmente conjunto que podem ser sincronizadas automaticamente com os dados de back-end, incluindo suporte de resolução de conflitos.

  Descubra mais sobre os nossos [funcionalidades de dados].

- **Notificações push** - Our cliente SDK integrar totalmente com as capacidades de registo do Azure notificação concentradores, permitindo-lhe enviar notificações push para milhões de utilizadores em simultâneo.

  Descubra mais sobre os nossos [emissão funcionalidades de notificação].

- **Cliente SDK** - fornecemos um conjunto completo de SDK do cliente que abrangem desenvolvimento nativo ([iOS], [Android] e [Windows]), em diferentes plataformas desenvolvimento ([Xamarin para iOS e Android], [Xamarin formulários]) e desenvolvimento de aplicações híbridas ([Apache Cordova]).  Cada cliente SDK está disponível com uma licença MIT e é abrir origem.

## <a name="azure-app-service-features"></a>Funcionalidades do Azure de aplicação de serviço.
As seguintes funcionalidades de plataforma são geralmente úteis para sites de produção móvel.

- **Escala automática** - serviço de aplicação permite-lhe rapidamente escala de cópia de segurança ou de saída para processar quaisquer recebidas carga de cliente. Selecione o número e o tamanho da VMs ou configurar o dimensionamento automática para dimensionar a sua aplicação móvel back-end, com base no caso de carga ou agenda manualmente.

  Descubra mais sobre [escala automática].

- **Ambientes de transição** - aplicação de serviço pode executar várias versões do seu site, permitindo-lhe efetuar A / B teste, teste de produção como parte de um plano de DevOps maior e no local transição de uma nova back-end.

  Descubra mais sobre [ambientes de teste].

- **Implementação contínua** - aplicação de serviço pode integrar com os sistemas de SMS comuns, permitindo-lhe implementar automaticamente uma nova versão do seu back-end por conduza a um ramo do seu sistema de SMS.

  Descubra mais sobre as [Opções de implementação].

- **Rede virtual** - aplicação de serviço pode ligar-se aos recursos no local utilizando ligações de rede, ExpressRoute ou híbrido virtuais.

  Descubra [mais sobre como híbrida ligações], [redes virtuais]e [ExpressRoute].

- **Isoladas / dedicada ambientes** -aplicação de serviço podem ser executado num ambiente totalmente isolada e dedicado para segura execução do serviço de aplicação do Azure aplicações alta.  Este é ideal para das cargas de trabalho de aplicação que exige o escala muito alta, isolamento ou acesso seguro à rede.

  Descubra mais sobre [Ambientes de serviço de aplicação].

## <a name="getting-started"></a>Introdução ##
Para começar a utilizar aplicações Mobile, siga o tutorial [Começar] .  Isto vai abordar os princípios básicos para produzir um cliente da sua preferência e back-end móvel, em seguida, integração de autenticação, sincronização offline e as notificações push.  Pode seguir o tutorial [Começar] várias vezes - uma vez para cada aplicação de cliente.

Para mais informações sobre as aplicações móveis do Azure, reveja a nossa [formação mapa].
Para mais informações sobre a plataforma de aplicação de serviço do Azure, consulte o artigo [Aplicação de serviço de Azure].

>[AZURE.NOTE]Se pretender começar a aplicação de serviço de Azure antes de inscrever-se para uma conta do Azure, aceda ao [Tentar aplicação de serviço](https://tryappservice.azure.com/?appServiceName=mobile), onde imediatamente pode criar uma aplicação web do starter curto na aplicação de serviço. Sem cartões de crédito necessários; sem compromissos.

<!-- URLs. -->
[Migrate your Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[Azure de aplicação de serviço]: ../app-service/app-service-value-prop-what-is.md
[Introdução]: app-service-mobile-ios-get-started.md
[Armazenamento de tabela do Azure]: ../storage/storage-getting-started-guide.md
[DocumentDB]: ../documentdb/documentdb-get-started.md
[funcionalidades de autenticação]: ./app-service-mobile-auth.md
[funcionalidades de dados]: ./app-service-mobile-offline-data-sync.md
[funcionalidades de notificação de emissão]: ../notification-hubs/notification-hubs-push-notification-overview.md
[iOS]: ./app-service-mobile-ios-how-to-use-client-library.md
[Android]: ./app-service-mobile-android-how-to-use-client-library.md
[Windows]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin para iOS e Android]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Formulários de Xamarin]: ./app-service-mobile-xamarin-forms-get-started.md
[Apache Cordova]: ./app-service-mobile-cordova-how-to-use-client-library.md
[escala automática]: ../app-service-web/web-sites-scale.md
[ambientes de teste]: ../app-service-web/web-sites-staged-publishing.md
[Opções de implementação]: ../app-service-web/web-sites-deploy.md
[ligações de híbrido]: ../app-service-web/web-sites-hybrid-connection-get-started.md
[redes virtuais]: ../app-service-web/web-sites-integrate-with-vnet.md
[ExpressRoute]: ../app-service-web/app-service-app-service-environment-network-configuration-expressroute.md
[Ambientes de aplicação de serviço]: ../app-service-web/app-service-app-service-environment-intro.md
[mapa de formação]: https://azure.microsoft.com/en-us/documentation/learning-paths/appservice-mobileapps/
