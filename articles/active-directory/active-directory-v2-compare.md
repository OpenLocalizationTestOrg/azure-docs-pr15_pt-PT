<properties
    pageTitle="O ponto final do Azure AD 2.0 | Microsoft Azure"
    description="Uma comparação entre o Azure AD original e os pontos finais 2.0."
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
    ms.date="09/16/2016"
    ms.author="dastrock"/>

# <a name="whats-different-about-the-v20-endpoint"></a>Quais são as diferenças sobre o ponto final 2.0?

Se estiver familiarizado com o Azure Active Directory ou se tiver o integrado aplicações com o Azure AD no passado, poderão existir algumas diferenças no ponto final de 2.0 não seria esperado.  Este documento chamadas saída essas diferenças para sua compreensão.

> [AZURE.NOTE]
    Nem todos os cenários do Azure Active Directory e funcionalidades são suportadas pelo ponto final 2.0.  Para determinar se deve utilizar o ponto final 2.0, leia sobre as [limitações 2.0](active-directory-v2-limitations.md).


## <a name="microsoft-accounts-and-azure-ad-accounts"></a>Contas Microsoft e contas do Azure AD
o ponto final 2.0 permitir que os programadores a escrever as aplicações que aceitam iniciar sessão a partir de contas Microsoft Accounts e Azure AD, utilizando um ponto final de auth único.  Isto dá a capacidade de escrever a sua aplicação completamente conta-desconhecido; pode ser ignorant do tipo de conta que o utilizador inicia sessão com.  Obviamente, *pode* efetuar a aplicação em atenção o tipo de conta que está a ser utilizado numa sessão específica, mas não tem de.

Por exemplo, se a sua aplicação chamadas do [Microsoft Graph](https://graph.microsoft.io), algumas funcionalidades adicionais e dados irão estar disponíveis para utilizadores da empresa, tais como os respetivos sites do SharePoint ou dados de diretório.  Mas para várias ações, tais como [Ler correio de um utilizador](https://graph.microsoft.io/docs/api-reference/v1.0/resources/message), o código pode ser escrito exatamente o mesmo para contas Microsoft Accounts e Azure AD.  

Integrar a aplicação com o Microsoft Accounts e contas do Azure AD é agora um processo simples.  Pode utilizar um único conjunto de pontos finais, numa única biblioteca e um registo única aplicação para aceder ao consumidor tanto enterprise mundo.  Para saber mais sobre o ponto final 2.0, consulte [a descrição geral](active-directory-appmodel-v2-overview.md).


## <a name="new-app-registration-portal"></a>Novo portal de registo de aplicação
o ponto final 2.0 só pode ser registado numa nova localização: [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).  Este é o portal de onde pode obter um Id da aplicação, personalizar o aspeto da página de início de sessão na sua aplicação e muito mais.  Tudo o que precisa para aceder ao portal é uma conta Microsoft powered - conta pessoal ou escolar/profissional.  

Vamos irão continuar a adicionar mais funcionalidade para este Portal de registo de aplicação ao longo do tempo.  A intenção é que este portal será a nova localização onde pode aceder ao gerir nada e tudo o que está a ter de fazer com as aplicações do Microsoft.


## <a name="one-app-id-for-all-platforms"></a>Um Id de aplicação para todas as plataformas
No serviço Azure Active Directory original, pode ter registado várias aplicações diferentes para num único projeto.  Foram forçada para utilizar os registos de aplicação separada para os clientes nativos e aplicações web:

![Registo da aplicação antiga IU](../media/active-directory-v2-flows/old_app_registration.PNG)

Por exemplo, se já criado um Web site e uma aplicação do iOS, tinha que registá-los em separado, com dois Ids de aplicação diferente.  Se tiver um Web site e back-end da web api, poderá ter registado cada um deles como uma aplicação separada no Azure AD.  Se tiver uma aplicação do iOS e uma aplicação Android, também poderá ter registado duas aplicações diferentes.  

<!-- You may have even registered different apps for each of your build environments - one for dev, one for test, and one for production. -->

Agora, tudo o que necessita é um registo único aplicação e um Id da aplicação único para cada um dos seus projetos.  Pode adicionar vários "plataformas" a um cada projeto e fornecer dos dados adequados para cada plataforma que adicionar.  Obviamente, pode criar aplicações tantas como que goste, dependendo das suas necessidades, mas para a maioria dos casos, apenas um Id da aplicação deverá ser necessário.

<!-- You can also label a particular platform as "production-ready" when it is ready to be published to the outside world, and use that same Application Id safely in your development environments. -->

É nosso objetivo de resolver isto irá conduzir a uma experiência de desenvolvimento e gestão de aplicação mais simplificada e criar uma vista mais consolidada de num único projeto que poderá estar a trabalhar.


## <a name="scopes-not-resources"></a>Âmbitos, não recursos
No serviço do Azure AD original, uma aplicação pode comportar-se como um **recurso**ou um destinatário de tokens de.  Um recurso pode definir um número de **âmbitos** ou **oAuth2Permissions** que compreende, permitindo aplicações pedir tokens para esse recurso para um determinado conjunto de âmbitos de cliente.  Considere a API do Azure AD gráfico como um exemplo de um recurso:

- Identificador do recurso, ou `AppID URI`:`https://graph.windows.net/`
- Âmbitos, ou `OAuth2Permissions`: `Directory.Read`, `Directory.Write`, etc.  

Tudo isto mantém-se para o o ponto final 2.0.  Ainda pode se comportam de uma aplicação como o recurso, definir âmbitos e ser identificadas por um URI.  Aplicações de cliente ainda podem pedir acesso a esses âmbitos.  No entanto, a forma na qual um cliente pede essas permissões foi alterado.  No passado, um 2.0 OAuth autorizar pedido para Azure AD poderá ter velhos:

```
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https%3A%2F%2Fgraph.windows.net%2F
...
```

Quando o parâmetro do **recurso** indicado qual o recurso a aplicação de cliente está a solicitar autorização para.  Azure AD calculada as permissões necessárias pela aplicação com base na configuração estática no Portal do Azure e emitido tokens em conformidade.  Agora, o mesmo 2.0 de OAuth autorizar parece pedido:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

onde o parâmetro **âmbito** indica qual o recurso e as permissões a aplicação está a solicitar autorização para. O recurso pretendido ainda é muito presente no pedido de - -simplesmente é abrangido em cada um dos valores do parâmetro âmbito.  Utilizar o parâmetro de âmbito desta forma permite que o ponto final 2.0 ser mais compatível com a especificação de OAuth 2.0 e alinha mais atentamente com as práticas de indústria comuns.  Também lhe permite aplicações efetuar [consentimento elementar](#incremental-and-dynamic-consent), que é descrito na secção seguinte.

## <a name="incremental-and-dynamic-consent"></a>Consentimento utilizarão e dinâmico
Aplicações registada no Azure AD ficará disponível necessário para especificar as respetivas permissões OAuth 2.0 necessários no Portal do Azure, a hora de criação da aplicação de serviço:

![Permissões de registo da IU](../media/active-directory-v2-flows/app_reg_permissions.PNG)

As permissões de uma aplicação obrigatória foram configuradas **estática**.  Enquanto esta permitido configuração da aplicação existir no Portal do Azure e mantidos o código totalmente e simples, apresenta alguns problemas para programadores:

- Uma aplicação teve saber todas as permissões seria necessário na hora de criação da aplicação.  Adicionar permissões ao longo do tempo era um processo de difícil acesso.
- Uma aplicação teve saber todos os recursos que nunca teria acesso antecedência.  Era difícil criar aplicações que podem aceder um número arbitrário de recursos.
- Uma aplicação tinha que pedir a todas as permissões seria necessário após primeiro início o utilizador de sessão no.  Em alguns casos isto por um instrutor para uma longa lista de permissões, as quais desencorajadas utilizadores finais de aprovação do access a aplicação no inicial iniciar sessão.

Com o ponto final 2.0, pode especificar as permissões a sua aplicação **dinamicamente**, o tempo de execução, precisa de durante a utilização regular da sua aplicação.  Para fazê-lo, pode especificar os âmbitos a sua aplicação necessidades em qualquer ponto determinado altura pela incluindo-los na `scope` parâmetro de um pedido de autorização:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

A permissão de pedidos acima para a aplicação ler dados do directório de um utilizador Azure AD, bem como escrever dados no seu diretório.  Se o utilizador deu para essas permissões no passado para esta aplicação em particular, eles simplesmente irão introduzir as suas credenciais e de ter sessão iniciados sessão na aplicação.  Se o utilizador não deu a qualquer uma destas permissões, o ponto final 2.0 perguntar ao utilizador para consentimento para essas permissões.  Para saber mais, pode ler sobre [as permissões, consentimento e âmbitos](active-directory-v2-scopes.md).

Permitir que uma aplicação para pedir permissões dinamicamente através de `scope` parâmetro dá-lhe controlo total sobre a sua experiência de utilizador.  Se pretender, pode optar por frontload seu consentimento experiência e peça para todas as permissões num pedido de autorização inicial.  Ou, se a sua aplicação requer um grande número de permissões, pode escolher recolher essas permissões do utilizador incrementada, tal como a tentativa de utilizar determinadas funcionalidades da sua aplicação ao longo do tempo.

## <a name="well-known-scopes"></a>Âmbitos de conhecidos

#### <a name="offline-access"></a>Acesso offline
o ponto final 2.0 poderá exigir a utilização de uma nova permissão famoso para aplicações - a `offline_access` âmbito.  Todas as aplicações terão de pedir esta permissão se precisam de aceder a recursos em nome de um utilizador para um período de tempo prolongado, mesmo quando o utilizador pode não estar ativamente a utilizar a aplicação.  O `offline_access` âmbito será apresentado ao utilizador em caixas de diálogo consentimento como "Aceder aos dados offline", que o utilizador tem de aceitar.  Pedir o `offline_access` permissão permitirá a sua aplicação web receber OAuth 2.0 refresh_tokens a partir do ponto final 2.0.  Refresh_tokens estão longa vida e podem ser trocados para novos OAuth 2.0 access_tokens para períodos adicional do access.  

Se a sua aplicação não pedir a `offline_access` âmbito, não receberá refresh_tokens.  Isto significa que ao resgatar uma authorization_code no [fluxo de código de autorização de OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow), apenas receberá novamente uma access_token a partir do `/token` ponto final.  Esse access_token permanecerá válida para um breve período de tempo (normalmente uma hora), mas são eventualmente irá expirar.  AT altura, a sua aplicação será necessário para redirecionar o utilizador novamente para o `/authorize` ponto final para obter um novo authorization_code.  Durante este redirecionamento, o utilizador pode ou não poderá ter de introduzir novamente as suas credenciais ou voltar consentimento para permissões, consoante o tipo de aplicação.

Para saber mais sobre OAuth 2.0, refresh_tokens e access_tokens, consulte o artigo a [referência de protocolo 2.0](active-directory-v2-protocols.md).

#### <a name="openid-profile--email"></a>OpenID, perfil e e-mail

No serviço Azure Active Directory original, o mais básico OpenID ligar início de sessão no fluxo seria fornecem vastos de informações sobre o utilizador na id_token resultante.  Podem incluir as afirmações num id_token do utilizador nome, nome de utilizador preferido, endereço de e-mail, ID do objeto e mais.

Vamos agora são restringir as informações que o `openid` âmbito oferecem o acesso de aplicação a.  O âmbito de 'openid' só irá permitir a sua aplicação iniciar sessão de utilizador no e receber um identificador de aplicação específicas para o utilizador.  Se pretender obter informação identificativa (PII) sobre o utilizador na sua aplicação, a sua aplicação terão de pedir permissões adicionais do utilizador.  Vamos são introdução aos novos duas âmbitos – a `email` e `profile` âmbitos – que lhe permitem fazê-lo.

O `email` âmbito é bastante simples – permitir o acesso de aplicação para o endereço de e-mail principal do utilizador através de `email` reclamar na id_token.  O `profile` âmbito oferecem o acesso de aplicação a todas as outras informações básicas sobre o utilizador – em seu nome, nome de utilizador preferido, ID do objecto etc.

Esta opção permite-lhe código a sua aplicação de uma forma de mínimas divulgação – apenas pode perguntar ao utilizador para o conjunto de informações que a sua aplicação requer fazer a sua tarefa.  Para mais informações sobre estes âmbitos, consulte [a referência de âmbito 2.0](active-directory-v2-scopes.md). 

## <a name="token-claims"></a>Em afirmações tokens

As afirmações em tokens emitidos pelo ponto final 2.0 não serão idênticas ao tokens emitidos por ficará disponível pontos finais do Azure AD - aplicações migrar para o novo serviço não devem assumir uma nomeadamente afirmação existirão id_tokens ou access_tokens.   Tokens emitidos pelo ponto final 2.0 são compatíveis com as especificações OAuth 2.0 e OpenID ligar, mas pode seguir a semântica de diferentes que ficará disponível serviço Azure AD.

Para saber mais sobre as afirmações específicas emitidas em tokens de 2.0, consulte a [referência de token 2.0](active-directory-v2-tokens.md).

## <a name="limitations"></a>Limitações
Existem algumas restrições a ter em conta ao utilizar o ponto de 2.0.  Consulte o [documento de limitações 2.0](active-directory-v2-limitations.md) para ver se qualquer uma destas restrições se aplicar ao seu cenário em particular.
