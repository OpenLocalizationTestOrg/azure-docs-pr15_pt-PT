<properties
    pageTitle="Azure AD 2.0 âmbitos, permissões e consentimento | Microsoft Azure"
    description="Uma descrição da autorização no Azure AD 2.0 ponto final, incluindo âmbitos, permissões e consentimento."
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

# <a name="scopes-permissions--consent-in-the-v20-endpoint"></a>Âmbitos, permissões e consentimento no ponto final 2.0

As aplicações que se integram no Azure AD siga um modelo de autorização específica que permite aos utilizadores controlar a forma como uma aplicação pode aceder aos respetivos dados.  A aplicação 2.0 deste modelo de autorização foi atualizada, alterar a forma como uma aplicação tem interagem com Azure AD.  Este tópico abrange os conceitos básicos neste modelo de autorização, incluindo âmbitos, permissões e consentimento.

> [AZURE.NOTE]
    Nem todos os cenários do Azure Active Directory e funcionalidades são suportadas pelo ponto final 2.0.  Para determinar se deve utilizar o ponto final 2.0, leia sobre as [limitações 2.0](active-directory-v2-limitations.md).

## <a name="scopes--permissions"></a>Âmbitos & permissões

Azure AD implementa o protocolo de autorização [OAuth 2.0](active-directory-v2-protocols.md) , que é um método para permitir que uma aplicação de terceiros 3º aceder à alojado web recursos em nome de um utilizador.  Qualquer recurso alojado web que integra-se com o Azure AD terá um identificador de recursos ou **URI de ID da aplicação**.  Por exemplo, alguns dos recursos de alojado web da Microsoft incluem:

- O Office 365 Unified correio API:`https://outlook.office.com`
- A API do Azure AD Graph:`https://graph.windows.net`
- Microsoft Graph:`https://graph.microsoft.com`

O mesmo se VERDADEIRO para qualquer 3º recursos de terceiros que tem integrado com o Azure AD.  Qualquer uma destes recursos também pode definir um conjunto de permissões que podem ser utilizados para dividir a funcionalidade desse recurso para blocos mais pequenos.  Por exemplo, o Microsoft Graph tenha definido alguns permissões:

- Leia o calendário de um utilizador
- Escrever ao calendário de um utilizador
- Enviar correio como um utilizador
- [+ mais](https://graph.microsoft.io)

Ao definir estas permissões, o recurso pode ter extensivamente controlo sobre os seus dados e como são exposta para o mundo exterior.  Uma aplicação de terceiros 3º, em seguida, pode pedir estas permissões a partir de um utilizador final - e o utilizador final deve aprovar as permissões antes da aplicação que pode agir em seu nome.  Das secções funcionalidade do recurso conjuntos de permissões mais pequenos, 3º aplicações de terceiros podem ser incorporadas para pedir apenas as permissões específicas que necessitam para realizar os respetivos direito.  Também permite aos utilizadores finais saber exatamente como uma aplicação irá utilizar os respetivos dados, para que fiquem mais certeza de que a aplicação não estiver a funcionar com a intenção de maliciosa.

No Azure AD e OAuth, estas permissões são conhecidos como **âmbitos**.  Também poderá vê-los designados **oAuth2Permissions**.  Um âmbito é representado no Azure AD como um valor de cadeia.  O valor de âmbito para cada permissão continuar com o exemplo do Microsoft Graph, é:

- Leia o calendário de um utilizador:`Calendar.Read`
- Escreva ao calendário de um utilizador:`Mail.ReadWrite`
- Envie correio como um utilizador:`Mail.Send`

Uma aplicação pode pedir estas permissões ao especificar os âmbitos nos pedidos para o ponto final 2.0, conforme descrito abaixo.

## <a name="openid-connect-scopes"></a>Âmbitos de OpenId ligar

A aplicação de 2.0 da OpenID ligar tem alguns âmbitos bem definidos que não se aplicam a qualquer outro recurso específico - `openid`, `email`, `profile`, e `offline_access`.

#### <a name="openid"></a>OpenId

Se uma aplicação executa início de sessão no utilizar [OpenID ligar](active-directory-v2-protocols.md#openid-connect-sign-in-flow), deve pedir a `openid` âmbito.  O `openid` âmbito irá aparecer no ecrã trabalho conta consentimento como a permissão "Sessão neste" e, no ecrã consentimento de conta Microsoft pessoal como a permissão "Ver o seu perfil e ligar a aplicações e serviços com a sua conta Microsoft".  Esta permissão permite uma aplicação receber um identificador exclusivo para o utilizador a forma da `sub` reclamar.  Este ofereça também aceder a aplicação para o ponto final de informações de utilizador.  O `openid` âmbito pode também ser utilizado no ponto final token 2.0 para adquirir id_tokens, que podem ser utilizados para proteger HTTP chamadas entre diferentes componentes de uma aplicação.

#### <a name="email"></a>Mensagem de correio electrónico

O `email` âmbito pode ser incluído juntamente com o `openid` âmbito e quaisquer outros.  Este ofereça aceder a aplicação para o endereço de e-mail principal do utilizador sob a forma da `email` reclamar.  O `email` afirmação só será incluída no tokens se um endereço de e-mail está associado a conta de utilizador, não é sempre as maiúsculas/minúsculas.  Se utilizar o `email` âmbito, a sua aplicação deverá estar preparada para processar as maiúsculas/minúsculas no qual o `email` afirmação não existe no token de.

#### <a name="profile"></a>Perfil

O `profile` âmbito pode ser incluído juntamente com o `openid` âmbito e quaisquer outros.  Este ofereça aceder a aplicação para vastos de informações sobre o utilizador.  Isto inclui, mas não está limitado a, o utilizador nome próprio, apelido, nome de utilizador preferido, ID do objeto e assim sucessivamente.  Para obter uma lista completa dos créditos perfil disponíveis no id_tokens para um determinado utilizador, consulte a [referência de token 2.0](active-directory-v2-tokens.md).

#### <a name="offlineaccess"></a>Offline_access

O [ `offline_access` âmbito](http://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) permite a sua aplicação para aceder a recursos em nome de utilizador para um determinado período de tempo.  No ecrã trabalho conta consentimento, este âmbito será apresentado como a permissão "Aceder aos seus dados em qualquer altura".  Pessoal Microsoft consentimento ecrã conta, esta será apresentada como a permissão "Acesso a qualquer momento suas informações de".  Quando um utilizador aprova a `offline_access` âmbito, a aplicação irão estar ativada para receber tokens de atualização a partir do ponto final token de 2.0.  Atualizar tokens são longa vida e permitir que a sua aplicação adquirir novas tokens de acesso que expiram mais antigos.

Se a sua aplicação não pedir a `offline_access` âmbito, não receberá refresh_tokens.  Isto significa que ao resgatar uma authorization_code no [fluxo de código de autorização de OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow), apenas receberá novamente uma access_token a partir do `/token` ponto final.  Esse access_token permanecerá válida para um breve período de tempo (normalmente uma hora), mas são eventualmente irá expirar.  AT altura, a sua aplicação será necessário para redirecionar o utilizador novamente para o `/authorize` ponto final para obter um novo authorization_code.  Durante este redirecionamento, o utilizador pode ou não poderá ter de introduzir novamente as suas credenciais ou voltar consentimento para permissões, consoante o tipo de aplicação.

Para mais informações sobre como aceder e utilizar atualizar tokens, consulte a [referência de protocolo 2.0](active-directory-v2-protocols.md).


## <a name="requesting-individual-user-consent"></a>Pedir consentimento de utilizador individual

Num pedido de autorização [OpenID ligar ou OAuth 2.0](active-directory-v2-protocols.md) , uma aplicação pode pedir as permissões que necessita utilizando o `scope` parâmetro de consulta.  Por exemplo, quando um utilizador inicia para uma aplicação, a aplicação seria enviar um pedido de como o seguinte (com quebras de linha para legibilidade):

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendar.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

O `scope` parâmetro é uma lista separados por espaço de âmbitos que está a pedir a aplicação.  Cada âmbito individual é indicado por acrescentar o valor do âmbito (URI identifier aplicação ID) do recurso.  O pedido acima indica que a aplicação tem permissão para o calendário do utilizador de ler e enviar correio como o utilizador.

Depois do utilizador introduz as respetivas credenciais, irá verificar o ponto final 2.0 para um registo correspondente da **consentimento do utilizador**.  Se o utilizador não deu a qualquer uma das permissões necessárias no passado, o ponto final 2.0 irá perguntar ao utilizador para conceder as permissões necessárias.  

![Captura de ecrã do trabalho conta consentimento](../media/active-directory-v2-flows/work_account_consent.png)

Quando o utilizador aprova a permissão, será registado o seu consentimento para que o utilizador não tem de voltar consentimento no início de sessão-ins subsequentes.

## <a name="requesting-consent-for-an-entire-tenant"></a>Pedir consentimento para um inquilino inteiro

Muitas vezes, quando uma organização compras uma subscrição uma aplicação ou licença, pretendem totalmente aprovisionar-o para os seus funcionários.  Como parte deste processo, um administrador da empresa pode conceder consentimento para essa aplicação funcionar em nome de qualquer funcionário.  Concedendo consentimento para um inquilino inteiro, empregados da organização, que não experiência do ecrã consentimento para essa aplicação.

Para pedir consentimento para todos os utilizadores num inquilino, a aplicação pode utilizar a **Administração consentimento ponto final**, descrito abaixo.

## <a name="admin-restricted-scopes"></a>Âmbitos de restringido pelo administrador

Determinadas permissões privilégio alta no ecossistema Microsoft podem ser marcadas como **restringido pelo administrador**.  Exemplos de como âmbitos incluem:

- Dados de uma organizaion directório de leitura:`Directory.Read`
- Dados de escrita ao diretório uma organização:`Directory.ReadWrite`
- Grupos de segurança de leitura no diretório de uma organização:`Groups.Read.All`

Enquanto um utilizador do consumidor pode conceder acesso a esses dados de uma aplicação, os utilizadores organizacionais são restringidos a partir de conceder acesso ao mesmo conjunto de dados confidenciais da sua empresa.  Se a sua aplicação solicitar acesso a um destas permissões de um utilizador organizacional, o utilizador irá receber uma mensagem de erro que indica que são não autorizados consentimento às permissões da sua aplicação.

Se a sua aplicação requer o acesso a estes âmbitos restringido pelo administrador para organizações, deverá pedi-las diretamente a partir de um administrador da empresa também a utilizar o **administrador consentimento ponto final**, descrito abaixo.

Quando um administrador concede estas permissões através do ponto final consentimento de administração, consentimento será concedido para todos os utilizadores no inquilino, conforme descrito acima.

## <a name="using-the-admin-consent-endpoint"></a>Utilizar o ponto final consentimento de administrador

Ao seguir estes passos, a aplicação poderão recolher as permissões para todos os utilizadores num inquilino determinado, incluindo âmbitos restringido pelo administrador.  Para ver um exemplo de código que implementa o descritos em pormenor passos abaixo, consulte a [amostra de restrito âmbitos de administrador](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2).

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Pedir as permissões no portal do registo de aplicação

- Se ainda não o fez, navegue para a aplicação na [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)ou [criar uma aplicação](active-directory-v2-app-registration.md) .
- Localize a secção do **Microsoft Graph permissões** e adicione as permissões que requer a sua aplicação.
- Certifique-se para **Guardar** o registo de aplicação

#### <a name="recommended-sign-the-user-into-your-app"></a>Recomendado: iniciar sessão o utilizador na sua aplicação

Normalmente, quando a criação de uma aplicação que utiliza o administrador consentimento ponto final, a aplicação será necessário terem uma/vista de página que permite que o administrador aprovar permissões a aplicação.  Esta página pode ser parte do fluxo de inscrição a aplicação, parte das definições da aplicação ou um fluxo de "ligar" dedicado.  Em muitos casos, faz sentido para a aplicação para o mostrar "ligação" ver apenas depois de um utilizador tem sessão iniciada com uma conta escolar ou profissional Microsoft.

Iniciar sessão do utilizador para a aplicação permite-lhe identificar organziation a que o administrador pertence antes solicitando-lhes para aprovar as permissões necessárias.  Enquanto não estritamente for necessário, pode ajudar a criar uma experiência mais intuitiva para os seus utilizadores organizacionais.  Para iniciar a sessão de utilizador no, siga os nossos [Tutoriais de protocolo 2.0](active-directory-v2-protocols.md).

#### <a name="request-the-permissions-from-a-directory-admin"></a>Pedir as permissões de um administrador de diretório

Quando estiver pronto para pedir permissões de administrador da empresa, pode redirecionar o utilizador para o 2.0 **Administração consentimento ponto final**.

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro Tip: Try pasting the below request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parâmetro | | Descrição |
| ----------------------- | ------------------------------- | --------------- |
| inquilino | obrigatório | O inquilino do diretório que pretende pedir permissão a partir de.  Pode ser fornecido no formato de nome amigável ou guid. |
| client_id | obrigatório | O Id da aplicação que o portal de registo ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) atribuída a sua aplicação. |
| redirect_uri | obrigatório | O redirect_uri onde pretende que a resposta sejam enviadas para a sua aplicação processar.  Exatamente tem de corresponder um dos redirect_uris registado no portal. |
| Estado | recomendado | Um valor incluído no pedido de que também será devolvido na resposta token.  Pode ser uma cadeia de qualquer conteúdo que pretender.  O estado é utilizado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorreu, por exemplo, a página ou vista que terem sido no. |

Neste momento, Azure AD irá impor que apenas um administrador de inquilino possam iniciar sessão concluir o pedido.  Será pedido ao administrador para aprovar todas as permissões que já pediram para a sua aplicação no portal do registo. 

##### <a name="successful-response"></a>Resposta com êxito
Se o administrador aprovar as permissões para a sua aplicação, a resposta bem sucedida será:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- | --------------- |
| inquilino | O inquilino do diretório que concedidas as permissões de aplicação que pedidas, no formato de guid. |
| Estado | Um valor incluído no pedido de que também será devolvido na resposta token.  Pode ser uma cadeia de qualquer conteúdo que pretender.  O estado é utilizado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorreu, por exemplo, a página ou vista que terem sido no. |
| admin_consent | Será definido como `True`. |



##### <a name="error-response"></a>Resposta de erro
Se o administrador não aprovar as permissões para a sua aplicação, a resposta falha será:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- | --------------- |
| erro | Uma cadeia de código de erro que pode ser utilizada para classificar tipos de erros que ocorrem e pode ser utilizada para responder a erros. |
| error_description | Uma mensagem de erro específico pode ajudar a um programador identificar o problema de raiz de um erro.  |

Assim que recebeu uma resposta com êxito a partir do ponto final consentimento de administração, a sua aplicação ganhou as permissões que pedidas.  Agora pode mover para pedir um token para o recurso pretendido, conforme descrito abaixo.

## <a name="using-permissions"></a>Utilização de permissões

Depois do utilizador autorize às permissões para a sua aplicação, a sua aplicação pode adquirir tokens de acesso que representam permissão da sua aplicação para aceder a um recurso em algumas capacidade.  Um token de acesso determinado só pode ser utilizado para uma única resorce, mas codificado dentro da mesma será todas as permissões que tenha sido concedida a sua aplicação para esse recurso.  Para ganhar um token de acesso, a aplicação pode efetuar um pedido para o ponto final token de 2.0:

```
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

O resultante token de acesso, em seguida, pode ser utilizado em pedidos de HTTP ao recurso - -sujeito indicam ao recurso que a sua aplicação tem a permissão adequada para efetuar uma determinada tarefa.  

Para obter mais detalhes sobre o protocolo OAuth 2.0 e como adquirir tokens de acesso, consulte a [referência de protocolo de ponto final 2.0](active-directory-v2-protocols.md).
