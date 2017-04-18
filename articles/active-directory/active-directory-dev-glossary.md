<properties
   pageTitle="Glossário do programador do Azure Active Directory | Microsoft Azure"
   description="Uma lista de termos para utilizadas mais frequentemente conceitos de programador do Azure Active Directory e funcionalidades."
   services="active-directory"
   documentationCenter=""
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/31/2016"
   ms.author="bryanla"/>

# <a name="azure-active-directory-developer-glossary"></a>Glossário de programador do Azure Active Directory
Este artigo contém as definições para algumas das principais Azure Active Directory (AD) programador conceitos, que são úteis quando saber mais sobre o desenvolvimento de aplicações para o Azure AD.

## <a name="access-token"></a>token de acesso
Um tipo de [token de segurança](#security-token) emitido por um [servidor de autorização](#authorization-server)e utilizado por uma [aplicação de cliente](#client-application) para poder aceder a um [protegida servidor de recursos](#resource-server). Normalmente, sob a forma de um [JSON Web Token (JWT)][JWT], o token engloba a autorização concedida para o cliente pelo [proprietário do recurso](#resource-owner), para um nível de pedido de acesso. O token contém todos os aplicável [em afirmações](#claim) sobre o assunto, permitindo-a aplicação de cliente utilizá-lo como um formulário de credenciais quando aceder a um determinado recurso. Isto também elimina a necessidade para o proprietário do recurso para expor credenciais para o cliente.

Tokens de acesso são por vezes designados "Utilizador + aplicação" ou "Aplicação só", consoante as credenciais a ser representadas. Por exemplo, quando uma aplicação de cliente utiliza a:

- O utilizador final [autorização "Código de autorização" conceder](#authorization-grant), autentica primeiro como o proprietário do recurso, delegar a autorização ao cliente para aceder ao recurso. O cliente autentica, mais tarde, quando obter o token de acesso. O token pode, por vezes, ser designado mais especificamente como um token de "Utilizador + aplicação" representa tanto o utilizador autorizados a aplicação de cliente e da aplicação.
- [Conceder a autorização de "Credenciais de cliente"](#authorization-grant), o cliente fornece a autenticação responsável, funcionamento sem autorização/do proprietário recurso autenticação, para que o token de, por vezes, pode ser referido como um token de "Só de aplicação".

Consulte o artigo [referência Azure AD Token] [ AAD-Tokens-Claims] para obter mais detalhes.

## <a name="application-manifest"></a>manifesto de aplicação  
Funcionalidade fornecida pelo [portal clássica Azure][AZURE-classic-portal], que produz uma representação JSON de configuração de identidade da aplicação, utilizada como um mecanismo para atualizar a sua [aplicação] de associado[ AAD-Graph-App-Entity] e [ServicePrincipal] [ AAD-Graph-Sp-Entity] entidades. Consulte o artigo [Compreender manifesto da aplicação Azure Active Directory] [ AAD-App-Manifest] para obter mais detalhes.

## <a name="application-object"></a>objecto de aplicação  
Quando lhe register/atualizar uma aplicação no [portal clássica Azure][AZURE-classic-portal], o portal cria/actualizações de um objeto de aplicação e um correspondente [objecto principal de serviço](#service-principal-object) para esse inquilino. O objecto de aplicação *define* a aplicação de configuração de identidade globalmente (em todos os inquilinos onde tem acesso), fornecer um modelo a partir do qual o correspondente serviço principal torno dos objetos são *derivado* para utilização localmente ao run-tempo (um inquilino específico).

Consulte o artigo [aplicação e objetos de serviço de capital] [ AAD-App-SP-Objects] para obter mais informações.

## <a name="application-registration"></a>registo da aplicação  
Para permitir que uma aplicação integrar e delegar funções de gestão de acesso e de identidade para Azure AD, tem de estar registado com um [inquilino](#tenant)do Azure AD. Quando registar a aplicação com Azure AD, estiver a fornecer uma configuração de identidade para a sua aplicação, permitindo-lhe integrar com o Azure AD e utilizar funcionalidades tais como:

- Robustas de gestão do Single Sign-On utilizando a gestão de identidades do Azure AD e [Ligar OpenID] [ OpenIDConnect] implementação do protocolo
- Acesso juntamente [protegido por recursos](#resource-server) por [aplicações de cliente](#client-application), através de implementação do [servidor de autorização](#authorization-server) do Azure AD OAuth 2.0
- [Consentimento framework](#consent) para gerir o acesso de cliente ao recursos protegidos, com base em autorização de proprietário do recurso.

Consulte [aplicações de integração com o Azure Active Directory] [ AAD-Integrating-Apps] para obter mais detalhes.

## <a name="authentication"></a>autenticação
A lei de um desafio uma festa para introduzir as credenciais legítimas, fornecer a base para a criação de um capital de segurança para ser utilizado de identidade e controlo de acesso. Por exemplo, durante um, [autorização de oauth2 ainda conceder](#authorization-grant) a parte autenticar é de preencher a função do [proprietário do recurso](#resource-owner) ou [aplicação de cliente](#client-application), dependendo de conceder utilizado.

## <a name="authorization"></a>autorização
A lei de conceder a permissão principal uma segurança autenticado, alguma coisa. Existem dois casos de utilização principal no modelo de programação do Azure AD:

- Durante um fluxo de [autorização de oauth2 ainda conceder](#authorization-grant) : quando o [proprietário do recurso](#resource-owner) concede autorização para a [aplicação de cliente](#client-application), permitindo ao cliente aceder a recursos do proprietário de recursos.
- Durante o acesso a recursos pelo cliente: como implementada pelo [servidor de recursos](#resource-server), utilizando o [reclamar](#claim) valores apresentam o [token de acesso](#access-token) para tomar decisões de controlo de acesso baseado no-los.

## <a name="authorization-code"></a>código de autorização
Uma breve viveu "token" fornecido a uma [aplicação de cliente](#client-application) ao [ponto final de autorização](#authorization-endpoint), como parte do fluxo "código de autorização", um da oauth2 ainda quatro [autorização concede](#authorization-grant). O código é devolvido à aplicação cliente em resposta a autenticação de um [proprietário do recurso](#resource-owner), que indica que o proprietário do recurso tem delegado autorização para aceder aos recursos pedidos. Como parte do fluxo da, o código é mais tarde resgatar para um [token de acesso](#access-token).

## <a name="authorization-endpoint"></a>ponto final de autorização
Uma dos pontos finais implementados pelo [servidor de autorização](#authorization-server), utilizadas para interagir com o [proprietário do recurso](#resource-owner) para fornecer um, [autorização de conceder](#authorization-grant) durante uma autorização oauth2 ainda conceder fluxo. Consoante o fluxo de conceder autorização utilizado, a conceder real fornecido pode variar, incluindo um [código de autorização](#authorization-code) ou [token de segurança](#security-token).

Consulte a especificação de oauth2 ainda [autorização conceder tipos] [ OAuth2-AuthZ-Grant-Types] e [ponto final de autorização] [ OAuth2-AuthZ-Endpoint] secções e a [especificação de OpenIDConnect] [ OpenIDConnect-AuthZ-Endpoint] para obter mais detalhes.

## <a name="authorization-grant"></a>conceder autorização
Uma credencial que representa o [proprietário de recurso](#resource-owner) [autorização](#authorization) para aceder aos seus recursos protegidos, concedidos a uma [aplicação de cliente](#client-application). Uma aplicação de cliente pode utilizar um dos [quatro tipos de conceder definidos pela arquitetura de autorização de oauth2 ainda] [ OAuth2-AuthZ-Grant-Types] para obter uma conceder, dependendo do tipo de/requisitos de cliente: "autorização código conceder", "conceder de credenciais do cliente", "conceder implícito" e "conceder as credenciais de palavra-passe do recurso proprietário". A credencial devolvida ao cliente é um [token de acesso](#access-token)ou um [código de autorização](#authorization-code) (trocadas mais tarde para um token de acesso), dependendo do tipo de conceder autorização utilizado.

## <a name="authorization-server"></a>servidor de autorização
Tal como foi definido pelo [Oauth2 ainda autorização Framework][OAuth2-Role-Def], o servidor responsável pela emissão acesso tokens do [cliente](#client-application) depois de autenticar o [proprietário do recurso](#resource-owner) e obter respectiva autorização com êxito. Uma [aplicação de cliente](#client-application) interage com o servidor de autorização no runtime através da sua [autorização](#authorization-endpoint) e pontos finais [token](#token-endpoint) , em conformidade com a oauth2 ainda definida [autorização concede](#authorization-grant).

No caso de integração de aplicações do Azure AD, Azure AD implementa a função de servidor de autorização para as aplicações do Azure AD e serviço Microsoft APIs, por exemplo [Microsoft Graph APIs][Microsoft-Graph].

## <a name="claim"></a>reclamar
Um [token de segurança](#security-token) contém afirmações, que disponibiliza declarações cerca de uma entidade (como uma [aplicação de cliente](#client-application) ou [proprietário de recurso](#resource-owner)) para outra entidade (como o [servidor de recurso](#resource-server)). Em afirmações são pares valor/nome que reencaminhamento factos sobre o assunto token (por exemplo, o capital de segurança foi autenticado pelo [servidor de autorização](#authorization-server)). As apresentadas num determinado token de afirmações são dependentes diversas variáveis, incluindo o tipo de token, o tipo de credenciais utilizadas para autenticar o assunto, a configuração da aplicação, etc.

Consulte o artigo [referência token do Azure AD] [ AAD-Tokens-Claims] para obter mais detalhes.

## <a name="client-application"></a>aplicação de cliente  
Tal como foi definido pelo [Oauth2 ainda autorização Framework][OAuth2-Role-Def], uma aplicação que torna protegida pedidos de recursos em nome do [proprietário do recurso](#resource-owner). O termo "cliente" não implica qualquer características de implementação do hardware específico (por exemplo, se a aplicação executa num servidor, um ambiente de trabalho ou outros dispositivos).  

Uma aplicação de cliente pede [autorização](#authorization) de proprietário de um recurso para participar num fluxo de [autorização de oauth2 ainda conceder](#authorization-grant) e poderão aceder APIs/dados em nome do proprietário de recursos. A oauth2 ainda autorização Framework [define dois tipos de clientes][OAuth2-Client-Types], "Confidencial" e "público", com base na capacidade do cliente para manter a confidencialidade das suas credenciais. Aplicações podem implementar um [cliente da web (confidencial)](#web-client) que é executado num servidor web, um [cliente nativo (pública)](#native-client) instalado num dispositivo ou [utilizador agente baseados no cliente (pública)](#user-agent-based-client) que é executado no browser de um dispositivo.

## <a name="consent"></a>consentimento
O processo de um [proprietário do recurso](#resource-owner) conceder autorização para uma [aplicação de cliente](#client-application), específicas [permissões](#permissions) para aceder a recursos protegidos, em nome do proprietário do recurso. Consoante as permissões pedidas pelo cliente, um administrador ou utilizador será pedido ao consentimento para permitir o acesso aos seus dados de organização/indivíduo respetivamente. Tenha em atenção num cenário de [inquilino com várias](#multi-tenant-application) , a aplicação [principal de serviço](#service-principal-object) também está registado no inquilino do utilizador consenting.

## <a name="id-token"></a>Token de ID
Um [OpenID ligar] [ OpenIDConnect-ID-Token] [token de segurança](#security-token) fornecido por um de [servidor de autorização](#authorization-server) [ponto final de autorização](#authorization-endpoint), que contém [em afirmações](#claim) sobre a autenticação de um utilizador final [proprietário do recurso](#resource-owner). Como um token de acesso tokens de ID também são representados como um assinado digitalmente [JSON Web Token (JWT)][JWT]. Ao contrário de um token de acesso no entanto, créditos um token de ID não é utilizado para fins relacionados com acesso a recursos e controlo de acesso especificamente.

Consulte o artigo [referência token do Azure AD] [ AAD-Tokens-Claims] para obter mais detalhes.

## <a name="multi-tenant-application"></a>aplicação do inquilino com várias
O início de sessão de uma classe de [aplicação de cliente](#client-application) , que permite aos e o [seu consentimento](#consent) por utilizadores configurações para qualquer Azure AD [inquilino](#tenant), incluindo inquilinos que não seja aquele onde está registado o cliente. Por outro lado, uma aplicação registada como inquilino único, irá permitir apenas inícios de sessão a partir de contas de utilizador aprovisionados ao mesmo inquilino como aquele onde a aplicação é registada. Aplicações de [cliente nativo](#native-client) são inquilinos com várias por predefinição, Considerando que aplicações de [cliente da web](#web-client) tem capacidade para selecionar entre único e com várias inquilino.

Consulte o artigo [como iniciar sessão no qualquer utilizador do Azure AD utilizando o padrão de aplicação do inquilino com várias] [ AAD-Multi-Tenant-Overview] para obter mais detalhes.

## <a name="native-client"></a>cliente nativo
Um tipo de [aplicação de cliente](#client-application) que está instalado vierem num dispositivo. Uma vez que todo o código é executado num dispositivo, é considerado um cliente "público" devido ao respetivo incapacidade para armazenar credenciais em privado/confidencialidade. Consulte o artigo [perfis e de tipos de clientes oauth2 ainda] [ OAuth2-Client-Types] para obter mais detalhes.

## <a name="permissions"></a>permissões
Uma [aplicação de cliente](#client-application) acesso ilegítimo a um [servidor de recursos](#resource-server) por declarar pedidos de permissão. Dois tipos estão disponíveis:

- Permissões "Delegadas", as quais pedem acesso [com base no âmbito](#scopes) em delegada autorização tiver sessão iniciada no [proprietário do recurso](#resource-owner), são apresentadas para o recurso de tempo de execução como ["scp" afirmações](#claim) no do cliente [token de acesso](#access-token).
- Permissões de "Pedido", as quais pedir acesso [baseado em funções](#roles) sob credenciais/identidade a aplicação de cliente, são apresentadas para o recurso de tempo de execução como ["funções" afirmações](#claim) no token de acesso do cliente.

Estes também superfície durante o processo de [consentimento](#consent) , que lhe dá uma o administrador ou proprietário de recurso a oportunidade de conceder/negar o acesso de cliente aos recursos no seu inquilino.

Pedidos de permissão estão configurados no "Aplicações" / "Configurar" separador no [portal clássica Azure][AZURE-classic-portal], em "Permissões para outras aplicações", pelas seleccionando pretendida "delegada permissões" e "Permissões de aplicações" (o último necessita de associação numa função de Administrador Global). Uma vez que um [cliente público](#client-application) não é possível manter credenciais,-pode pedir apenas permissões de delegado, enquanto um [cliente confidencial](#client-application) possui a possibilidade de pedido de ambos os delegados e as permissões da aplicação. O cliente [objecto da aplicação](#application-object) armazena as permissões declaradas na respetiva [propriedade requiredResourceAccess][AAD-Graph-App-Entity].

## <a name="resource-owner"></a>proprietário de recursos
Tal como foi definido pelo [Oauth2 ainda autorização Framework][OAuth2-Role-Def], uma entidade capaz de conceder acesso a um recurso protegido. Quando o proprietário do recurso é uma pessoa, é referido como um utilizador final. Por exemplo, quando uma [aplicação de cliente](#client-application) quer ter acesso a caixa de correio de um utilizador através da [API do Microsoft Graph][Microsoft-Graph], necessita de permissão ao proprietário do recurso da caixa de correio.

## <a name="resource-server"></a>servidor de recursos
Tal como foi definido pelo [Oauth2 ainda autorização Framework][OAuth2-Role-Def], protegido por um servidor que aloja protegida recursos, capazes de aceitar e a responder a pedidos de recursos por [aplicações de cliente](#client-application) que apresentam uma [token de acesso](#access-token). Também conhecido como um servidor de recurso protegido ou aplicação de recursos.

Um servidor de recurso expõe APIs e impõe acesso a suas recursos protegidos através de [âmbitos](#scopes) e de [funções](#roles), utilizando a arquitetura de autorização de 2.0 OAuth. Alguns exemplos incluem a API do Azure AD Graph que fornece acesso aos dados do inquilino Azure AD e o Office 365 APIs que fornecem acesso aos dados tal como o correio e calendário. Ambos estas também são acessíveis através do [Microsoft Graph API][Microsoft-Graph].  

Tal como uma aplicação de cliente, configuração de identidade da aplicação de recurso é estabelecida através do [registo](#application-registration) num inquilino Azure AD, fornecer a aplicação e um objecto principal de serviço. Algumas APIs fornecido pela Microsoft, tal como a API do Azure AD Graph, previamente tem registado disponibilizados em todos os inquilinos durante o aprovisionamento de principais de serviço.

## <a name="roles"></a>funções
Como [âmbitos](#scopes), funções fornecem uma forma para um [servidor de recursos](#resource-server) controlar o acesso aos seus recursos protegidos. Existem dois tipos: uma função de "utilizador" implementa o controlo de acesso baseado em funções para os utilizadores/grupos que requerem acesso ao recurso, enquanto uma função de "aplicações" implementa o mesmo para as [aplicações de cliente](#client-application) que requerem acesso.

Funções são definidos pelo recurso cadeias (por exemplo "aprovador da despesas", "Só de leitura", "Directory.ReadWrite.All") geridas no [portal clássica Azure] [ AZURE-classic-portal] através [manifesto da aplicação](#application-manifest)do recurso e armazenados na [appRoles propriedade do recurso][AAD-Graph-Sp-Entity]. Portal clássico do Azure também é utilizado para atribuir utilizadores a funções de "utilizador" e configurar [as permissões da aplicação](#permissions) do cliente para aceder a uma função de "aplicações".

Para informações detalhadas sobre das funções de aplicação exposto pela API do Azure AD Graph, consulte o artigo [Graph API permissão âmbitos][AAD-Graph-Perm-Scopes]. Por exemplo uma implementação passo a passo, consulte o artigo [controlo de acesso no aplicações de nuvem utilizando o Azure AD baseado em funções][Duyshant-Role-Blog].

## <a name="scopes"></a>âmbitos de
Tal como as [funções](#roles), âmbitos fornecem uma forma para um [servidor de recursos](#resource-server) controlar o acesso aos seus recursos protegidos. Âmbitos são utilizados para implementar [baseada no âmbito] [ OAuth2-Access-Token-Scopes] controlo de acesso, para uma [aplicação de cliente](#client-application) que tenha sido dado delegado acesso ao recurso pelo respectivo proprietário.

Âmbitos são definidos pelo recurso cadeias (por exemplo "Mail.Read", "Directory.ReadWrite.All"), gerido no [portal clássica Azure] [ AZURE-classic-portal] através [manifesto da aplicação](#application-manifest)do recurso e armazenados na [oauth2Permissions propriedade do recurso][AAD-Graph-Sp-Entity]. Portal clássico do Azure também é utilizado para configurar o cliente aplicação [permissões de delegado](#permissions) para aceder a um âmbito.

Melhor práticas Convenção de nomenclatura, é utilizar um formato de "resource.operation.constraint". Para informações detalhadas sobre os âmbitos exposto pela API do Azure AD Graph, consulte o artigo [Graph API permissão âmbitos][AAD-Graph-Perm-Scopes]. Para âmbitos expostos pelos serviços do Office 365, consulte o artigo [referência de permissões do Office 365 API][O365-Perm-Ref].

## <a name="security-token"></a>token de segurança
Um documento assinado que contém afirmações, tal como um oauth2 ainda token ou afirmação SAML 2.0. Para um oauth2 ainda [autorização de conceder](#authorization-grant), um [token de acesso](#access-token) (oauth2 ainda) e um [Token de ID](OpenID Connect) estão tipos de tokens de segurança, sendo que ambos estão implementados como um [JSON Web Token (JWT)][JWT].

## <a name="service-principal-object"></a>objecto principal de serviço
Quando lhe register/atualizar uma aplicação no [portal clássica Azure][AZURE-classic-portal], o portal cria/actualizações de um [objecto da aplicação](#application-object) e um objecto principal correspondente de serviço para esse inquilino. O objecto de aplicação *define* a configuração da aplicação identidade globalmente (em todos os inquilinos onde a aplicação associada foi concedida acesso), e é um modelo a partir do qual o correspondente serviço principal torno dos objetos são *derivado* para utilização localmente ao run-tempo (um inquilino específico).

Consulte o artigo [aplicação e objetos de serviço de capital] [ AAD-App-SP-Objects] para obter mais informações.

## <a name="sign-in"></a>iniciar sessão
O processo de uma [aplicação de cliente](#client-application) iniciar a autenticação de utilizador final e capturar relacionado com o estado, para adquirir um [token de segurança](#security-token) e controlar o âmbito da sessão de aplicação para esse Estado. Pode incluir artefactos como informações de perfil de utilizador e informações derivado de afirmações de token de estado.

A função de início de sessão de uma aplicação é normalmente utilizada para implementar o sessão único (SSO). -Pode também ser precedido por uma função "inscrição", como o ponto de entrada para um utilizador de fim aceder à aplicação (após a primeira sessão). A função inscrição é utilizada para recolher e persistirem estado adicional específico do utilizador e pode exigir [consentimento do utilizador](#consent).

## <a name="sign-out"></a>terminar sessão
O processo de autenticação anular um utilizador final, desligamento o estado de utilizador associado com a sessão de [aplicação de cliente](#client-application) durante o [início de sessão no](#sign-in)

## <a name="tenant"></a>inquilino
Uma instância de um diretório do Azure AD é referida como um inquilino do Azure AD. Fornece-lhe uma variedade de funcionalidades, incluindo:

- um serviço de registo para aplicações integradas
- autenticação de contas de utilizador e aplicações registadas
- Pontos finais resto necessários para suportar a vários protocolos incluindo oauth2 ainda e SAML, incluindo o [ponto final de autorização](#authorization-endpoint), [token ponto final](#token-endpoint) e o ponto final de "comuns" utilizadas por [inquilino com várias aplicações](#multi-tenant-application).

Um inquilino também está associado a uma Azure AD ou subscrição do Office 365 durante o aprovisionamento de subscrição, fornecer as funcionalidades de identidade e gestão de acesso para a subscrição. Consulte o artigo [como obter um inquilino do Azure Active Directory] [ AAD-How-To-Tenant] para obter detalhes sobre as várias formas como pode obter um acesso para um inquilino. Consulte o artigo [como Azure subscrições estão associadas a Azure Active Directory] [ AAD-How-Subscriptions-Assoc] para obter detalhes sobre a relação entre subscrições e um inquilino do Azure AD.

## <a name="token-endpoint"></a>ponto final do token
Uma dos pontos finais implementados pelo [servidor de autorização](#authorization-server) para suportar a oauth2 ainda [autorização concede](#authorization-grant). Dependendo de conceder,-pode ser utilizado para adquirir uma [token de acesso](#access-token) (e relacionados token "Atualizar") para um [cliente](#client-application), ou [token de ID](#ID-token) quando utilizado com [OpenID ligar] [ OpenIDConnect] protocolo.

## <a name="user-agent-based-client"></a>Baseado agente de utilizador no cliente
Um tipo de [aplicação cliente](#client-application) de que transfere o código de um servidor web e executa dentro de um agente de utilizador (por exemplo, num web browser), tal como uma única página aplicação (SPA). Uma vez que todo o código é executado num dispositivo, é considerado um cliente "público" devido ao respetivo incapacidade para armazenar credenciais em privado/confidencialidade. Consulte o artigo [perfis e de tipos de clientes oauth2 ainda] [ OAuth2-Client-Types] para obter mais detalhes.

## <a name="user-principal"></a>principal de utilizador
Semelhante à forma como um objecto principal de serviço é utilizado para representar uma instância da aplicação, um objecto principal de utilizador é outro tipo de principal de segurança, que representa um utilizador. Azure AD Graph [entidade utilizador] [ AAD-Graph-User-Entity] define o esquema de um objeto de utilizador, incluindo propriedades relacionadas com o utilizador como nome próprio e apelido, nome de utilizador principal, membros da função diretório, etc. Este procedimento fornece a configuração de identidade do utilizador para o Azure AD estabelecer um principal de utilizador em tempo de execução. O capital de utilizador é utilizado para representar um utilizador autenticado para Single Sign-On, [consentimento](#consent) delegação de gravação, tornando as decisões de controlo de acesso, etc.

## <a name="web-client"></a>cliente da Web
Um tipo de [aplicação de cliente](#client-application) que executa o código de todos os num servidor web e com capacidade para funcionar como um cliente "Confidencial" ao segura armazenar as suas credenciais no servidor. Consulte o artigo [perfis e de tipos de clientes oauth2 ainda] [ OAuth2-Client-Types] para obter mais detalhes.

## <a name="next-steps"></a>Próximos passos
O [Guia do programador do Azure AD] [ AAD-Dev-Guide] é o portal para utilizar para o desenvolvimento do Azure AD todos os tópicos, incluindo uma descrição geral de [integração de aplicações] relacionados[ AAD-How-To-Integrate] e as noções básicas de [autenticação do Azure AD e cenários de autenticação suportados][AAD-Auth-Scenarios].

Utilize a seguinte secção de comentários Disqus para fornecer comentários e ajude-nos refinar e dar forma a nossa conteúdo.

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-Subscriptions-Assoc]: ./active-directory-how-subscriptions-associated-directory.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-How-To-Tenant]: active-directory-howto-tenant.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Multi-Tenant-Overview]: active-directory-devhowto-multi-tenant-overview.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]: ./active-directory-token-and-claims.md
[AZURE-classic-portal]: https://manage.windowsazure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[Microsoft-Graph]: https://graph.microsoft.io
[O365-Perm-Ref]: https://msdn.microsoft.com/en-us/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Endpoint]: https://tools.ietf.org/html/rfc6749#section-3.1
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: http://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-AuthZ-Endpoint]: http://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint
[OpenIDConnect-ID-Token]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken
