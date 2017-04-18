
<properties
    pageTitle="Fluxo de credenciais de cliente do Azure AD 2.0 OAuth | Microsoft Azure"
    description="Criar aplicações web utilizando a implementação do Azure AD do protocolo de autenticação OAuth 2.0."
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
    ms.date="09/26/2016"
    ms.author="dastrock"/>

# <a name="v20-protocols---oauth-20-client-credentials-flow"></a>um fluxo de protocolos 2.0 - OAuth cliente 2.0 credenciais

As [credenciais do cliente OAuth 2.0 conceder](http://tools.ietf.org/html/rfc6749#section-4.4), por vezes designado "com lados duas OAuth", podem ser utilizados para aceder a web alojado recursos utilizando a identidade de uma aplicação.  É geralmente utilizada para o servidor para servidor interações que tem de executado em segundo plano sem o precense imediata de um utilizador final.  Estes tipos de aplicações são, muitas vezes como **daemons** ou **contas de serviço**.

> [AZURE.NOTE]
    Nem todos os cenários do Azure Active Directory e funcionalidades são suportadas pelo ponto final 2.0.  Para determinar se deve utilizar o ponto final 2.0, leia sobre as [limitações 2.0](active-directory-v2-limitations.md).

No mais típica três "com lados três OAuth," uma aplicação de cliente é concedida permissão para aceder a um recurso em nome de um utilizador específico.  A permissão é **delegado** do utilizador a aplicação, normalmente durante o processo de [consentimento](active-directory-v2-scopes.md) .  No entanto, no fluxo de credenciais de cliente, são concedidas permissões **diretamente** para a própria aplicação.  Quando a aplicação apresenta um token para um recurso, o recurso impõe que a própria aplicação tem autorização para executar alguns ação - não que algum utilizador tem autorização.

## <a name="protocol-diagram"></a>Diagrama de protocolo
O fluxo de credenciais de cliente inteira aspeto - cada um dos passos descritos no detalhadamente abaixo.

![Fluxo de credenciais de cliente](../media/active-directory-v2-flows/convergence_scenarios_client_creds.png)

## <a name="get-direct-authorization"></a>Obter autorização direta 
Existem duas formas que uma aplicação normalmente recebe direta autorização para aceder a um recurso: através de uma lista de controlo de acesso no recurso ou atribuição de permissão de aplicação no Azure AD.  Existem várias outras formas de que um recurso pode optar por autorizar os seus clientes e servidor cada recurso pode escolher o método que considera mais coerente para a sua aplicação.  Estes dois métodos são mais comuns no Azure AD e reccommended para clientes e recursos que pretendam executar o fluxo de credenciais de cliente.

### <a name="access-control-lists"></a>Listas de controlo de acesso
Um fornecedor de dado recurso poderá impor uma verificação de autorização com base numa lista de IDs de aplicações que conhece e concede algumas determinado nível de acesso.  Quando o recurso recebe um token do ponto final 2.0, pode codificar o token e extrair a ID da aplicação a partir do cliente a `appid` e `iss` em afirmações.  Pode, em seguida, comparar que a aplicação de algumas lista de controlo de acesso (ACL)-mantém.  A granularidade e o método da lista de controlo de acesso podem variar significativamente a partir do recurso ao recurso.

Um caso de utilização comum para essas ACL é testar corredores para uma aplicação web ou web api.  Web api só pode conceder um subconjunto das permissões completos aos seus vários clientes.  Mas para poder executar testes de ponto a ponto no api, um cliente de teste é criado que adquire tokens a partir do ponto final 2.0 e envia-los à api.  Api fazê-lo, em seguida, ACL ID da aplicação de teste do cliente para acesso total a funcionalidade de toda a api.  Tenha em atenção que se tiver essa lista no seu serviço, deverá-se de que não só validar o autor da chamada `appid`, mas também validar que o `iss` do token é considerado fidedigno também.

Este tipo de autorização é comum daemons e contas de serviço que precisam de aceder aos dados pertencentes consumidor os utilizadores com contas pessoais do Microsoft.  Para os dados pertencentes organizações, é recomendável que adquire a autorização necessária através da aplicação perimssions.

### <a name="application-permissions"></a>Permissões da aplicação
Em vez de utilizar ACL, APIs pode expor um conjunto de **permissões da aplicação** que pode ser concedido para uma aplicação.  Permissão de aplicação é concedida à aplicação por um administrador de uma organização e só pode ser utilizada para aceder aos dados pertencentes a essa organização e os seus empregados.  Por exemplo, o Microsoft Graph expõe várias permissões da aplicação:

- Ler correio em todas as caixas de correio
- Ler e escrever correio em todas as caixas de correio
- Enviar correio como qualquer utilizador
- Dados do directório de leitura
- [+ mais](https://graph.microsoft.io)

Para ganhar estas permissões na sua aplicação, pode executar os passos seguintes.

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Pedir as permissões no portal do registo de aplicação

- Se ainda não o fez, navegue para a aplicação na [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)ou [criar uma aplicação](active-directory-v2-app-registration.md) .  Será necessário para se certificar de que a sua aplicação tenha criado pelo menos uma aplicação secreta.
- Localize a secção **Direta as permissões da aplicação** e adicione as permissões que requer a sua aplicação.
- Certifique-se para **Guardar** o registo de aplicação

#### <a name="recommended-sign-the-user-into-your-app"></a>Recomendado: iniciar sessão o utilizador na sua aplicação

Normalmente, quando a criação de uma aplicação que utiliza as permissões da aplicação, a aplicação será necessário terem uma/vista de página que permite que o administrador aprovar permissões a aplicação.  Esta página pode ser parte do fluxo de inscrição a aplicação, parte das definições da aplicação ou um fluxo de "ligar" dedicado.  Em muitos casos, faz sentido para a aplicação para o mostrar "ligação" ver apenas depois de um utilizador tem sessão iniciada com uma conta escolar ou profissional Microsoft.

Iniciar sessão do utilizador para a aplicação permite-lhe identificar organziation a que o utilizador pertence antes solicitando-lhes para aprovar as permissões da aplicação.  Enquanto não estritamente for necessário, pode ajudar a criar uma experiência mais intuitiva para os seus utilizadores organizacionais.  Para iniciar a sessão de utilizador no, siga os nossos [Tutoriais de protocolo 2.0](active-directory-v2-protocols.md).

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
| inquilino | obrigatório | O inquilino do diretório que pretende pedir permissão a partir de.  Pode ser fornecido no formato de nome amigável ou guid.  Se não souber qual o utilizador pertence de inquilino e pretende avisar as inicie sessão com qualquer inquilino, utilize `common`. |
| client_id | obrigatório | O Id da aplicação que o portal de registo ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) atribuída a sua aplicação. |
| redirect_uri | obrigatório | O redirect_uri onde pretende que a resposta sejam enviadas para a sua aplicação processar.  Exatamente tem de corresponder um dos redirect_uris registado no portal do, exceto terá de ser codificada de url e pode ter segmentos de caminho adicionais. |
| Estado | recomendado | Um valor incluído no pedido de que também será devolvido na resposta token.  Pode ser uma cadeia de qualquer conteúdo que pretender.  O estado é utilizado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorreu, por exemplo, a página ou vista que terem sido no. |

Neste momento, Azure AD irá impor que apenas um administrador de inquilino possam iniciar sessão concluir o pedido.  Será pedido ao administrador para aprovar todas as permissões de aplicações directo que já pediram para a sua aplicação no portal do registo. 

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

Assim que recebeu uma resposta com êxito a partir da aplicação aprovisionamento ponto final, a aplicação tem adquirida com as permissões da aplicação direta que pedidas.  Agora pode mover para pedir um token para o recurso pretendido.

## <a name="get-a-token"></a>Obter um token
Assim que tiver adquirido a autorização necessária para a sua aplicação, pode continuar com adquirir tokens de acesso para APIs.  Para obter um token de utilizar o cliente conceder de credenciais, envie um pedido de mensagem para a `/token` ponto final de 2.0:

```
POST /common/oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials
```

```
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```

| Parâmetro | | Descrição |
| ----------------------- | ------------------------------- | --------------- |
| client_id | obrigatório | O Id da aplicação que o portal de registo ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) atribuída a sua aplicação. |
| âmbito | obrigatório | O valor transmitido o `scope` parâmetro neste pedido deve ser o identificador de recursos (URI de ID da aplicação) do recurso pretendido, afixado com o `.default` sufixo.  Para que do Microsoft Graph exemplo, o valor deve ser `https://graph.microsoft.com/.default`.  Este valor informa o ponto final 2.0 que de todas as aplicações directo permissões que tiver configurado para a sua aplicação, que este deverá emitir um token para aqueles pertencentes ao recurso pretendido. |
| client_secret | obrigatório | O segredo de aplicação que gerou no portal do registo para a sua aplicação. |
| grant_type | obrigatório | Tem de ser `client_credentials`. | 

#### <a name="successful-response"></a>Resposta com êxito
Uma resposta com êxito irá assumir a forma:

```
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| access_token | O token de acesso pedida. A aplicação pode utilizar este token para autenticar para o recurso seguro, tal como uma web API. |
| token_type | Indica o valor de tipo de token. O único tipo que é o Azure AD suporta `Bearer`.  |
| expires_in | Quanto tempo o token de acesso é válido (em segundos). |

#### <a name="error-response"></a>Resposta de erro
Uma resposta do erro serão assumir a forma:

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/.default is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| erro | Uma cadeia de código de erro que pode ser utilizada para classificar tipos de erros que ocorrem e pode ser utilizada para responder a erros. |
| error_description | Uma mensagem de erro específico pode ajudar a um programador identificar o problema de raiz de um erro de autenticação.  |
| error_codes | Uma lista de códigos de erro específicos STS que podem ajudar na diagnósticos.  |
| data/hora | A hora em que ocorreu o erro. |
| trace_id | Um identificador exclusivo para o pedido que pode ajudar na diagnósticos.  |
| correlation_id | Um identificador exclusivo para o pedido que pode ajudar na diagnósticos através de componentes. |

## <a name="use-a-token"></a>Utilizar um token
Agora que tenha adquirido um token, pode utilizar esse token para tornar os pedidos para o recurso.  Quando o token de expira, basta repetir o pedido para o `/token` ponto final para adquirir um token de acesso fresca.

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```
// Pro Tip: Try the below command out! (but replace the token with your own)
```

```
curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q" 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-sample"></a>Exemplo de código
Para ver um exemplo de uma aplicação que implementa que o client_credentials conceder utilizando o administrador consentimento ponto final, consulte a nossa [exemplo de código daemon 2.0](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
