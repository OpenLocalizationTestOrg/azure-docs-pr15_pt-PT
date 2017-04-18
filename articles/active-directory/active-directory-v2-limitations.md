<properties
    pageTitle="Limitações de ponto final 2.0 & restrições | Microsoft Azure"
    description="Uma lista das limitações e restrições com o ponto final do Azure AD 2.0."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="dastrock"/>

# <a name="should-i-use-the-v20-endpoint"></a>Devo utilizar o ponto final 2.0?

Durante a criação de aplicações que se integram com o Azure Active Directory, terá de decidir se os protocolos de autenticação e de ponto final de 2.0 correspondam às suas necessidades.  O ponto final do Azure AD original é ainda totalmente suportado e em algumas aspectos, é mais rich funcionalidade que 2.0.  No entanto, o 2.0 ponto final [apresenta benefícios significativos relacionados](active-directory-v2-compare.md) para programadores podem levar a utilizar o novo modelo de programação.

Neste ponto no momento, a nossa recomendação na utilização do ponto final 2.0 é da seguinte forma:

- Se pretender suportar contas pessoais do Microsoft na sua aplicação, deve utilizar o ponto final 2.0.  Mas certifique-se compreender as limitações listadas neste artigo, especialmente aqueles sobre especificamente a trabalhar e profissional contas.
- Se a sua aplicação requer apenas trabalho secundária e contas escolares, deve utilizar [os pontos finais da Azure AD originais](active-directory-developers-guide.md).

Ao longo do tempo, o ponto final 2.0 aumentará em suprimir as restrições listadas aqui, para que apenas nunca terá de utilizar o ponto final 2.0.  Entretanto, este artigo destina-se para o ajudar a determinar se o ponto final 2.0 é para si.  Vamos irão continuar a atualizar deste artigo ao longo do tempo para refletir o estado atual do ponto final 2.0, pelo que deve verificar novamente a reavaliar seus requisitos das capacidades de 2.0.

Se tiver uma aplicação existente com o Azure AD que não utiliza o ponto final 2.0, não é necessário para iniciar a partir da raiz.  No futuro, podemos estará a fornecer uma forma de ativar as aplicações do Azure AD existentes para utilização com o ponto final 2.0.

## <a name="restrictions-on-apps"></a>Restrições na aplicações
Os seguintes tipos de aplicações não são atualmente suportados pelo 2.0 ponto final.  Para obter uma descrição dos tipos de aplicações suportados, consulte [Este artigo](active-directory-v2-flows.md).

##### <a name="standalone-web-apis"></a>APIs Web autónomo
Com o ponto final 2.0, com a capacidade de [criar uma API Web que é protegida utilizando OAuth 2.0](active-directory-v2-flows.md#web-apis).  No entanto, esse Web API apenas poderão receber tokens a partir de uma aplicação que partilha o mesmo ID da aplicação.  Criar um web API é acedida a partir de um cliente com um Id diferente da aplicação não é suportada.  Que o cliente não poderá pedir ou obter permissões para a web API.

Para ver como construir uma API de Web aceita tokens a partir de um cliente com o mesmo Id da aplicação, consulte os exemplos de Web API do ponto final de 2.0 [Começar](active-directory-appmodel-v2-overview.md#getting-started)a trabalhar.

##### <a name="web-api-on-behalf-of-flow"></a>Web API em nome de fluxo
Muitos arquitecturas incluem uma API Web que necessita para ligar a outra descendentes Web API, ambos protegidos pelo ponto final 2.0.  Este cenário é comuns em nativos clientes que têm um Web API do back-end, que por sua vez chama um serviço online da Microsoft ou outra personalizada incorporada web API que suporta Azure AD.

Este cenário pode ser suportado utilizando a conceder OAuth 2.0 Jwt portadores credenciais, caso contrário, conhecida como fluxo de On-Behalf-Of.  No entanto, o fluxo em nome de atualmente não é suportado para o ponto final 2.0.  Para ver como funciona o deste fluxo no Azure AD ficará disponível de serviço, consulte o artigo [em nome de código de exemplo no GitHub](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).

## <a name="restrictions-on-app-registrations"></a>Restrições em registos de aplicação
Neste ponto específico, todas as aplicações que pretende que sejam integrar com o ponto final 2.0 tem de criar um novo registo de aplicação no [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).  Qualquer Azure AD existente ou aplicações do Microsoft Account não será compatíveis com o ponto final 2.0, nem será registadas no qualquer portal para além do novo registo Portal da aplicação de aplicações.  Planeamos fornece uma maneira de permitem que as aplicações existentes para utilizar como uma aplicação 2.0. Neste momento no entanto, não existe nenhum caminho de migração para uma aplicação para o ponto final 2.0.

Da mesma forma, aplicações registadas no Portal do novo registo de aplicação não irão funcionar contra o ponto final autenticação do Azure AD original.  No entanto, pode utilizar aplicações criadas no Portal do registo de aplicação para integrar com êxito com o Microsoft conta autenticação ponto final, `https://login.live.com`.

Além disso, registos de aplicação criados em [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) tem os seguintes avisos:

- A propriedade da **Home page** , também conhecido como o **URL de início de sessão** não é suportada.  Sem uma home page, estas aplicações de não ser apresentadas no painel de Office Asminhasaplicações.
- Apenas dois segredos de aplicação são permitidos por Id de aplicação neste momento.
- Apenas possam ser visualizado e gerido por uma conta de programador única um registo de aplicação.  Não pode ser partilhado entre múltiplos programadores.
- Existem várias restrições sobre o formato de redirect_uri permitidos.  Consulte a secção seguinte para obter mais detalhes.

## <a name="restrictions-on-redirect-uris"></a>Restrições URIs de redirecionamento
As aplicações que estão registadas no Portal do novo registo de aplicação estão atualmente limitadas a um conjunto de valores de redirect_uri limitado.  Redirect_uri para aplicações web e serviços têm de começar com o esquema de `https`, e todos os valores de redirect_uri têm de partilhar um único domínio DNS.  Por exemplo, não é possível registar uma aplicação web que tem redirect_uris:

`https://login-east.contoso.com`  
`https://login-west.contoso.com`

O sistema de registo compara o nome completo do DNS da redirect_uri existente com o nome DNS do redirect_uri que está a adicionar. O pedido para adicionar o nome DNS falhará se forem cumpridas qualquer das condições seguintes:  

- Se o nome DNS inteiro do novo redirect_uri não corresponder o nome DNS da redirect_uri existente
- Se o nome DNS inteiro do novo redirect_uri não for um subdomínio da redirect_uri existente

Por exemplo, se a aplicação tem atualmente redirect_uri:

`https://login.contoso.com`

Em seguida, é possível adicionar:

`https://login.contoso.com/new`

que corresponde exatamente o nome de DNS, ou:

`https://new.login.contoso.com`

qual é um subdomínio DNS do login.contoso.com.  Se quiser ter uma aplicação que tenha east.contoso.com de início de sessão e início de sessão-west.contoso.com como redirect_uris, em seguida, que tem de adicionar a redirect_uris seguinte ordem:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

As duas último podem ser adicionadas porque são subdomínios de redirect_uri a primeira, contoso.com. Esta limitação será removida uma versão futuras.

Para saber como registar uma aplicação no Portal do novo registo de aplicação, consulte [Este artigo](active-directory-v2-app-registration.md).

## <a name="restrictions-on-services--apis"></a>Restrições em serviços & APIs
O ponto final 2.0 atualmente suporta iniciar sessão para qualquer aplicação registada no novo do Portal de registo de aplicação, desde que descer na lista de [fluxos de autenticação suportados](active-directory-v2-flows.md).  No entanto, estas aplicações apenas poderão adquirir tokens de acesso OAuth 2.0 para um conjunto de recursos muito limitado.  O ponto final 2.0 irão apenas emitir access_tokens para:

- A aplicação que pediram o token.  Uma aplicação pode adquirir um access_token para si próprio, se a aplicação lógica é composta por vários componentes diferentes ou camadas.  Para ver este cenário em ação, consulte o artigo nossos tutoriais de [Introdução](active-directory-appmodel-v2-overview.md#getting-started) .
- O correio do Outlook, calendário e contactos REST APIs, as quais estão localizadas na https://outlook.office.com.  Para saber como escrever uma aplicação que acede estas APIs, consulte nestes tutoriais de [Introdução ao Office](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) .
- API do Microsoft Graph.  Para saber mais sobre o Microsoft Graph e todos os dados que se encontra disponíveis, visite [https://graph.microsoft.io](https://graph.microsoft.io).

Outros serviços não são suportados neste momento.  Mais Microsoft Online services serão adicionados no futuro, assim como suporte para os seus próprios personalizado APIs Web concebidos e serviços.

## <a name="restrictions-on-libraries--sdks"></a>Restrições em bibliotecas e SDK
Suporte de biblioteca para o ponto final 2.0 é bastante limitado neste momento.  Se pretender utilizar o ponto final 2.0 numa aplicação de produção, tem as seguintes opções:

- Se estiver a criar uma aplicação web, pode utilizar com segurança nosso software de intermédio ficará disponível do lado do servidor para executar o início de sessão e token de validação.  Estas incluem o software intermédio OWIN abrir ID ligar-se para ASP.NET e o nosso plug-in NodeJS Passport.  Exemplos de código utilizando o nosso software intermédio estão disponíveis no nossa secção [Introdução](active-directory-appmodel-v2-overview.md#getting-started) também.
- Para outras plataformas e para as aplicações nativas e dispositivos móveis, também pode integrar com o ponto final 2.0 pelas diretamente enviar e receber mensagens de protocolo no seu código da aplicação.  O 2.0 OpenID ligar e OAuth protocolos [explicitamente documentados](active-directory-v2-protocols.md) para o ajudar a executar essa uma integração.
- Por fim, pode utilizar abrir origem abrir ID ligar-se e OAuth de bibliotecas para integrar com o ponto final 2.0.  O protocolo 2.0 deve ser compatível com várias bibliotecas de protocolo de abrir origem sem principais alterações.  A disponibilidade dessas bibliotecas varia por idioma e a plataforma e os sites públicos [Ligue o ID de abrir](http://openid.net/connect/) e [OAuth 2.0](http://oauth.net/2/) mantém uma lista de implementações populares. Consulte o artigo [2.0 e a autenticação de bibliotecas do Azure Active Directory (AD)](active-directory-v2-libraries.md) para obter mais detalhes e a lista de bibliotecas do cliente abrir origem e amostras com o ponto final 2.0 foram testadas.

Foram também disponibilizadas uma pré-visualização inicial da [Biblioteca de autenticação da Microsoft (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) para o .NET apenas.  Está bem-vindo ao experimentar esta biblioteca em aplicações de cliente e servidor .NET, mas como uma biblioteca de pré-visualização que não irão ser completado por qualidade das versões DG suporta.

## <a name="restrictions-on-protocols"></a>Restrições protocolos
O ponto final 2.0 apenas suporta abrir ID ligar & OAuth 2.0.  No entanto, nem todas as funcionalidades e capacidades de cada protocolo foram incorporadas o ponto final 2.0, incluindo:

- Ligar a OpenID `end_session_endpoint`, que permite que uma aplicação terminar a sessão do utilizador com o ponto final 2.0.
- id_tokens emitido pelo ponto final 2.0 conter apenas um identificador par para o utilizador.  Isto significa que duas aplicações diferentes irão receber IDs diferentes para o mesmo utilizador.  Tenha em atenção que consultando Microsoft Graph `/me` ponto final, poderão obter um ID correlatable para o utilizador que pode ser utilizado através de aplicações.
- não contenham id_tokens emitido pelo ponto final 2.0 um `email` reclamar para o utilizador neste momento, mesmo se adquire permissão do utilizador para ver o seu e-mail.
- O ponto final OpenID ligar informações do utilizador. O ponto final de informações de utilizador não está implementado no ponto final 2.0 neste momento.  Contudo, todos os dados de perfil de utilizador potencialmente seria recebe neste ponto final está disponível a partir do Microsoft Graph `/me` ponto final.
- Função & afirmações de grupo.  Neste momento, o ponto final 2.0 não suporta emissão afirmações de grupo ou função id_tokens.

Para compreender melhor o âmbito da funcionalidade do protocolo suportado no ponto final 2.0, leia a nossa [OpenID ligar & OAuth 2.0 protocolo referência](active-directory-v2-protocols.md).

## <a name="restrictions-for-work--school-accounts"></a>Restrições para contas trabalhar & profissional
Existem algumas funcionalidades específicas para utilizadores de organização/empresas da Microsoft que ainda não são suportados pelo 2.0 ponto final.

##### <a name="device-based-conditional-access-native-and-mobile-apps-and-the-microsoft-graph"></a>Acesso condicional com base no dispositivo, aplicações nativas e dispositivos móveis e o Microsoft Graph
O ponto final 2.0 ainda não suporta autenticação do dispositivo para dispositivos móveis e nativas aplicações, como nativas aplicações em execução no iOS ou Android.  Isto pode bloquear a aplicação nativa a partir de contactar a Microsoft Graph para determinados organizações.  Autenticação do dispositivo é necessária quando um administrador configura uma política de acesso condicional com base no dispositivo numa aplicação.  Para o ponto final 2.0, o cenário mais provável para acesso condicional com base no dispositivo é um administrador definir uma política de um recurso no Microsoft Graph, como a API do Outlook.  Se um administrador define esta política e a sua aplicação nativa pede um token para o Microsoft Graph, o pedido, finalmente, irá falhar porque autenticação do dispositivo ainda não é suportada.  No entanto, aplicações Web que pedem tokens para o Microsoft Graph, são suportadas quando estão configuradas políticas baseadas no dispositivo.  Na web é executada autenticação do dispositivo de cenário de aplicação através de browser do utilizador.

Como um programador, é muito provável que têm sem controlo sobre quando políticas estão definidas recursos do Microsoft Graph, ou até mesmo deverá ter em consideração quando isto acontecer.  Se estiver a criar uma aplicação para utilizadores do trabalho e profissional, deve utilizar [o ponto final do Azure AD original](active-directory-developers-guide.md) até o ponto final 2.0 suporta a autenticação de dispositivo.  Para mais informações sobre acesso condicional com base no dispositivo, consulte o artigo [neste artigo](active-directory-conditional-access.md#device-based-conditional-access).

##### <a name="windows-integrated-authentication-for-federated-tenants"></a>Autenticação integrada do Windows para inquilinos federados
Caso tenha utilizado ADAL (e, consequentemente, o ponto final do Azure AD original) nas aplicações do Windows, pode ter trabalhado partido das que é conhecido como a conceder afirmação SAML.  Este conceder permite aos utilizadores do Azure AD federado inquilinos silenciosamente autenticar com a respetiva instância do Active Directory no local, sem ser necessário introduzir as suas credenciais.  A conceder afirmação SAML não é atualmente suportada no ponto final 2.0.
