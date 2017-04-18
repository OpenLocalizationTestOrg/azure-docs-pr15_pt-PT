

<properties
    pageTitle="Fluxo de código do Azure AD 2.0 OAuth autorização | Microsoft Azure"
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
    ms.date="08/08/2016"
    ms.author="dastrock"/>

# <a name="v20-protocols---oauth-20-authorization-code-flow"></a>um fluxo de protocolos 2.0 - código de autorização de OAuth 2.0

A conceder de código de autorização de OAuth 2.0 pode ser utilizado nas aplicações que são instaladas num dispositivo para aceder à recursos protegidos, como web APIs.  Utilizar a implementação do 2.0 de modelo de aplicação do token 2.0, pode adicionar iniciar sessão e API aceder às suas aplicações de ambiente de trabalho e móveis.  Este guia é idioma independente e descreve como enviar e receber mensagens HTTP sem utilizar qualquer um dos nossos bibliotecas abrir origem.

<!-- TODO: Need link to libraries -->

> [AZURE.NOTE]
    Nem todos os cenários do Azure Active Directory e funcionalidades são suportadas pelo ponto final 2.0.  Para determinar se deve utilizar o ponto final 2.0, leia sobre as [limitações 2.0](active-directory-v2-limitations.md).

O fluxo de código de autorização de OAuth 2.0 é descrito na [secção 4.1 da especificação OAuth 2.0](http://tools.ietf.org/html/rfc6749).  É utilizado para executar a autenticação e autorização na maioria dos tipos de aplicação, incluindo [web apps](active-directory-v2-flows.md#web-apps) e [vierem instalado aplicações](active-directory-v2-flows.md#mobile-and-native-apps).  Permite-aplicações em segurança adquirir access_tokens que podem ser utilizados para aceder a recursos que são protegidos utilizando o ponto final 2.0.  

## <a name="protocol-diagram"></a>Diagrama de protocolo
De alto nível, o fluxo de autenticação inteira para uma aplicação nativo/mobile um pouco este aspeto:

![Fluxo de código Auth OAuth](../media/active-directory-v2-flows/convergence_scenarios_native.png)

## <a name="request-an-authorization-code"></a>Pedido de um código de autorização
O fluxo de código autorização começa com o cliente que encaminha ao utilizador para o `/authorize` ponto final.  No pedido, o cliente indica as permissões que necessita para adquirir do utilizador:

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345
```

> [AZURE.TIP] Clique na ligação abaixo para executar este pedido! Após iniciar sessão, o browser deve ser redirecionado para `https://localhost/myapp/` com um `code` na barra de endereço.
    <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/Authorize...</a>

| Parâmetro | | Descrição |
| ----------------------- | ------------------------------- | --------------- |
| inquilino | obrigatório | O `{tenant}` valor no caminho do pedido de pode ser utilizado para controlar quem pode iniciar sessão na aplicação.  Os valores permitidos são `common`, `organizations`, `consumers`e identificadores de inquilinos.  Para obter mais detalhes, consulte o artigo [Noções básicas de protocolo](active-directory-v2-protocols.md#endpoints). |
| client_id | obrigatório | O Id da aplicação que o portal de registo ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) atribuída a sua aplicação. |
| response_type | obrigatório | Tem de incluir `code` para o fluxo de código de autorização. |
| redirect_uri | recomendado | Redirect_uri da sua aplicação, onde as respostas de autenticação podem ser enviadas e recebidas por sua aplicação.  Exatamente tem de corresponder um dos redirect_uris registado no portal do, exceto tem de ser codificada de url.  Para aplicações nativas e dispositivos móveis, deve utilizar o valor predefinido de `https://login.microsoftonline.com/common/oauth2/nativeclient`. |
| âmbito | obrigatório | Uma lista de [âmbitos](active-directory-v2-scopes.md) que pretende que o utilizador consentimento espaço separados por vírgulas.  |
| response_mode | recomendado | Especifica o método que deve ser utilizado para enviar a parte detrás token resultante para a sua aplicação.  Pode ser `query` ou `form_post`.  |
| Estado | recomendado | Um valor incluído no pedido de que também será devolvido na resposta token.  Pode ser uma cadeia de qualquer conteúdo que pretender.  Um valor único gerado aleatoriamente é normalmente utilizado para [prevenir ataques de falsificação de pedido de publicação em vários sites](http://tools.ietf.org/html/rfc6749#section-10.12).  O estado também é utilizado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorreu, por exemplo, a página ou vista que terem sido no. |
| linha de comandos | opcional | Indica o tipo de interação do utilizador que é necessário.  Os valores válidos apenas neste momento são 'login', 'Nenhum,' e 'consentimento'.  `prompt=login`vai forçar ao utilizador que introduza as respetivas credenciais no pedido, a negação sessão único no.  `prompt=none`é o oposto --lo será Certifique-se de que o utilizador não é apresentado com qualquer pedido interativo qualquer.  Se o pedido não pode ser concluído silenciosamente através de sessão único no, o ponto final 2.0 devolverão um erro.  `prompt=consent`irá acionar a caixa de diálogo do token consentimento depois do utilizador inicia sessão, perguntar ao utilizador para conceder permissões para a aplicação. |
| login_hint | opcional | Pode ser utilizado para preencher previamente o campo nome de utilizador/e-mail endereço da página para o utilizador, iniciar sessão se souber o nome de utilizador antecedência.  Muitas vezes aplicações irão utilizar este parâmetro durante re-autenticação, já está a ter extraídas o nome de utilizador a partir de um anterior iniciar sessão utilizando o `preferred_username` reclamar. |
| domain_hint | opcional | Pode ser um dos `consumers` ou `organizations`.  Se incluído, irá ignorar o processo de deteção baseada em correio eletrónico que o utilizador analise no 2.0 página Iniciar sessão, levando a uma experiência de utilizador ligeiramente mais simplificada.  Muitas vezes aplicações irão utilizar este parâmetro durante re-autenticação, por extrair a `tid` a partir de uma sessão anterior.  Se o `tid` reclamar valor é `9188040d-6c67-4c5b-b112-36a304b66dad`, deve utilizar `domain_hint=consumers`.  Caso contrário, utilize `domain_hint=organizations`. |

Neste momento, o utilizador será pedido para introduzir as suas credenciais e concluir a autenticação.  O ponto final 2.0 também irá garantir que o utilizador deu às permissões indicadas na `scope` parâmetro de consulta.  Se o utilizador não deu a qualquer uma das permissões, irá peça ao utilizador para o seu consentimento para as permissões necessárias.  Detalhes de [permissões, consentimento e apps inquilinos com várias são fornecidos aqui](active-directory-v2-scopes.md).

Assim que o utilizador autentica e concede consentimento, o ponto final 2.0 irá devolver uma resposta para a sua aplicação a indicada na `redirect_uri`, utilizando o método especificado na `response_mode` parâmetro.

#### <a name="successful-response"></a>Resposta com êxito
Utilizar uma resposta com êxito `response_mode=query` é semelhante a:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| código | Authorization_code que a aplicação solicitada. A aplicação pode utilizar o código de autorização para pedir um token de acesso para o recurso de destino.  Authorization_codes são muito curta, normalmente expirem após cerca de 10 minutos. |
| Estado | Se um parâmetro de estado é incluído no pedido, o mesmo valor deverá aparecer na resposta. A aplicação deve verificar que os valores de estado no pedidos e respostas são idênticos. |

#### <a name="error-response"></a>Resposta de erro
Respostas de erro também podem ser enviadas para o `redirect_uri` , de modo a aplicação pode processar corretamente:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| erro | Uma cadeia de código de erro que pode ser utilizada para classificar tipos de erros que ocorrem e pode ser utilizada para responder a erros. |
| error_description | Uma mensagem de erro específico pode ajudar a um programador identificar o problema de raiz de um erro de autenticação.  |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Códigos de erro de erros de ponto final de autorização

A tabela seguinte descreve os vários códigos de erro que podem ser devolvidos na `error` parâmetro da resposta de erro.

| Código de erro | Descrição | Acção de cliente |
|------------|-------------|---------------|
| invalid_request | Erro de protocolo, tal como um parâmetro obrigatório em falta. | Corrigir e submeter novamente o pedido. Este é um desenvolvimento erro é normalmente capturado durante o ensaio inicial.|
| unauthorized_client | A aplicação de cliente não é permitida para pedir um código de autorização. | Normalmente, isto ocorre quando a aplicação de cliente não está registada na Azure AD ou não é adicionada ao inquilino do Azure AD do utilizador. A aplicação pode pedir ao utilizador com instruções para instalar a aplicação e adicioná-lo ao Azure AD. |
| ACCESS_DENIED | Negado consentimento proprietário do recurso | A aplicação de cliente pode notificar o utilizador que não é possível continuar a não ser que o utilizador o autorize. |
| unsupported_response_type | O servidor de autorização não suporta o tipo de resposta no pedido de. | Corrigir e submeter novamente o pedido. Este é um desenvolvimento erro é normalmente capturado durante o ensaio inicial.|
|server_error | O servidor encontrou um erro inesperado. | Repetir o pedido. Estes erros podem resultar de condições temporárias. A aplicação de cliente poderá explicam ao utilizador que a sua resposta estiver atrasada para conclusão de um erro temporário. |
| temporarily_unavailable | O servidor está temporariamente ocupado e processar o pedido. | Repetir o pedido. A aplicação de cliente poderá explicam ao utilizador que a sua resposta estiver atrasada para conclusão de uma condição temporária. |
| invalid_resource |O recurso de destino é inválido porque não existe, Azure AD não consegue localizar algo ou não está configurado corretamente.| Isto indica que o recurso, se existir, não foi configurado no inquilino. A aplicação pode pedir ao utilizador com instruções para instalar a aplicação e adicioná-lo ao Azure AD. |

## <a name="request-an-access-token"></a>Pedir um token de acesso
Agora que tenha adquirido uma authorization_code e foi concedida permissão pelo utilizador, pode resgatar a `code` para um `access_token` para o recurso pretendido, enviando uma `POST` pedir para o `/token` ponto final:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

> [AZURE.TIP] Experimente executar este pedido na Postman! (Não se esqueça substituir o `code`)     [ ![Executar no Postman](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)

| Parâmetro | | Descrição |
| ----------------------- | ------------------------------- | --------------------- |
| inquilino | obrigatório | O `{tenant}` valor no caminho do pedido de pode ser utilizado para controlar quem pode iniciar sessão na aplicação.  Os valores permitidos são `common`, `organizations`, `consumers`e identificadores de inquilinos.  Para obter mais detalhes, consulte o artigo [Noções básicas de protocolo](active-directory-v2-protocols.md#endpoints). |
| client_id | obrigatório | O Id da aplicação que o portal de registo ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) atribuída a sua aplicação. |
| grant_type | obrigatório | Tem de ser `authorization_code` para o fluxo de código de autorização. |
| âmbito | obrigatório | Uma lista de âmbitos espaço separados por vírgulas.  Os âmbitos pedidos neste ramificação tem de ser equivalente a ou um subconjunto dos âmbitos pedidas na primeira ramificação.  Se os âmbitos especificados neste pedido abrangem vários servidores do recurso, o ponto final 2.0 irá devolver um token para o recurso especificado no âmbito da primeira.  Para obter uma explicação mais detalhada dos âmbitos, consulte [as permissões, consentimento e âmbitos](active-directory-v2-scopes.md).  |
| código | obrigatório | Authorization_code que o utilizador tiver adquirido na primeira ramificação do fluxo de.   |
| redirect_uri | obrigatório | O mesmo redirect_uri valor que foi utilizado para adquirir o authorization_code. |
| client_secret | necessário para web apps | O segredo de aplicação que criou no portal do registo de aplicação para a sua aplicação.  Não deve ser utilizada numa aplicação nativa, porque client_secrets não pode ser armazenado sujeito em dispositivos.  É necessário para aplicações web e web API, que têm a capacidade para armazenar a client_secret de forma segura do lado do servidor. |

#### <a name="successful-response"></a>Resposta com êxito
Uma resposta token com êxito terá o seguinte aspeto:

```
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| access_token | O token de acesso pedida. A aplicação pode utilizar este token para autenticar para o recurso seguro, tal como uma web API. |
| token_type | Indica o valor de tipo de token. O único tipo que suporte do Azure AD é o tipo de ligação  |
| expires_in | Quanto tempo o token de acesso é válido (em segundos). |
| âmbito | Os âmbitos de access_token seja válido para. |
| refresh_token |  Um token de atualização OAuth 2.0. A aplicação, pode utilizar este token de adquirir tokens de acesso adicionais depois de expira token de acesso actual.  Refresh_tokens estão longa vida e podem ser utilizados para reter o acesso aos recursos por adicional períodos de tempo.  Para obter mais detalhes, consulte a [referência de token 2.0](active-directory-v2-tokens.md).  |
| id_token | Um não assinados JSON Web Token (JWT). O base64Url podem aplicação descodificar segmentos deste token de pedir informações sobre o utilizador tiver sessão iniciada. A aplicação pode os valores em cache e apresentá-los, mas não deve confiar nos mesmos para qualquer autorização ou limites de segurança.  Para mais informações sobre id_tokens consulte a [referência de token de ponto final de 2.0](active-directory-v2-tokens.md). |

#### <a name="error-response"></a>Resposta de erro
Respostas de erro terá o seguinte aspeto:

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
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

#### <a name="error-codes-for-token-endpoint-errors"></a>Códigos de erro de erros de ponto final token

| Código de erro | Descrição | Acção de cliente |
|------------|-------------|---------------|
| invalid_request | Erro de protocolo, tal como um parâmetro obrigatório em falta. | Corrigir e submeter novamente o pedido |
| invalid_grant | O código de autorização é inválido ou expirou. | Experimente um novo pedido para o `/authorize` ponto final |
| unauthorized_client | O cliente autenticado não está autorizado para utilizar este tipo de conceder autorização. | Normalmente, isto ocorre quando a aplicação de cliente não está registada na Azure AD ou não é adicionada ao inquilino do Azure AD do utilizador. A aplicação pode pedir ao utilizador com instruções para instalar a aplicação e adicioná-lo ao Azure AD. |
| invalid_client | Ocorreu uma falha em autenticação de cliente. | As credenciais do cliente não são válidas. Para corrigir, o administrador da aplicação atualiza as credenciais. |
| unsupported_grant_type | O servidor de autorização não suporta o tipo de conceder autorização. | Altere o tipo de conceder no pedido de. Este tipo de erro deve ocorrer apenas durante o desenvolvimento e ser detetado durante o ensaio inicial. |
| invalid_resource | O recurso de destino é inválido porque não existe, Azure AD não consegue localizar algo ou não está configurado corretamente. | Isto indica que o recurso, se existir, não foi configurado no inquilino. A aplicação pode pedir ao utilizador com instruções para instalar a aplicação e adicioná-lo ao Azure AD. |
| interaction_required | O pedido requer interação do utilizador. Por exemplo, um passo de autenticação adicionais é necessário. | Repetir o pedido com o mesmo recurso. |
| temporarily_unavailable | O servidor está temporariamente ocupado e processar o pedido. | Repetir o pedido. A aplicação de cliente poderá explicam ao utilizador que a sua resposta estiver atrasada para conclusão de uma condição temporária.|

## <a name="use-the-access-token"></a>Utilizar o token de acesso
Agora que já adquirida com êxito um `access_token`, pode utilizar o token de nos pedidos para APIs da Web, incluindo-o na `Authorization` cabeçalho:

> [AZURE.TIP] Execute este pedido no Postman! (Substituir o `Authorization` cabeçalho da primeira)     [ ![Executar no Postman](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>Atualizar o token de acesso
Access_tokens estão breve viveu e tem atualizar após expiram para continuar a aceder aos recursos.  Pode fazê-lo, apresentando outro `POST` pedir para o `/token` ponto final, desta vez fornecendo a `refresh_token` em vez do `code`:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

> [AZURE.TIP] Experimente executar este pedido na Postman! (Não se esqueça substituir o `refresh_token`)     [ ![Executar no Postman](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)

| Parâmetro | | Descrição |
| ----------------------- | ------------------------------- | -------- |
| inquilino | obrigatório | O `{tenant}` valor no caminho do pedido de pode ser utilizado para controlar quem pode iniciar sessão na aplicação.  Os valores permitidos são `common`, `organizations`, `consumers`e identificadores de inquilinos.  Para obter mais detalhes, consulte o artigo [Noções básicas de protocolo](active-directory-v2-protocols.md#endpoints). |
| client_id | obrigatório | O Id da aplicação que o portal de registo ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) atribuída a sua aplicação. |
| grant_type | obrigatório | Tem de ser `refresh_token` para este ramificação do fluxo de código de autorização de. |
| âmbito | obrigatório | Uma lista de âmbitos espaço separados por vírgulas.  Os âmbitos pedidos neste ramificação tem de ser equivalente a ou um subconjunto dos âmbitos pedidas na duração de pedido de authorization_code original.  Se os âmbitos especificados neste pedido abrangem vários servidores do recurso, o ponto final 2.0 irá devolver um token para o recurso especificado no âmbito da primeira.  Para obter uma explicação mais detalhada dos âmbitos, consulte [as permissões, consentimento e âmbitos](active-directory-v2-scopes.md).  |
| refresh_token | obrigatório | Refresh_token que o utilizador tiver adquirido na segunda ramificação do fluxo da.   |
| redirect_uri | obrigatório | O mesmo redirect_uri valor que foi utilizado para adquirir o authorization_code. |
| client_secret | necessário para web apps | O segredo de aplicação que criou no portal do registo de aplicação para a sua aplicação.  Não deve ser utilizada numa aplicação nativa, porque client_secrets não pode ser armazenado sujeito em dispositivos.  É necessário para aplicações web e web API, que têm a capacidade para armazenar a client_secret de forma segura do lado do servidor. |

#### <a name="successful-response"></a>Resposta com êxito
Uma resposta token com êxito terá o seguinte aspeto:

```
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| access_token | O token de acesso pedida. A aplicação pode utilizar este token para autenticar para o recurso seguro, tal como uma web API. |
| token_type | Indica o valor de tipo de token. O único tipo que suporte do Azure AD é o tipo de ligação  |
| expires_in | Quanto tempo o token de acesso é válido (em segundos). |
| âmbito | Os âmbitos de access_token seja válido para. |
| refresh_token |  Um novo token de atualização de OAuth 2.0. Deverá substitua o antigo token de atualizar este token de atualização recentemente adquiridos para assegurar que os tokens de atualização permanecem válidos para tão longas quanto possível.  |
| id_token | Um não assinados JSON Web Token (JWT). O base64Url podem aplicação descodificar segmentos deste token de pedir informações sobre o utilizador tiver sessão iniciada. A aplicação pode os valores em cache e apresentá-los, mas não deve confiar nos mesmos para qualquer autorização ou limites de segurança.  Para mais informações sobre id_tokens consulte a [referência de token de ponto final de 2.0](active-directory-v2-tokens.md). |

#### <a name="error-response"></a>Resposta de erro
```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
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

Para obter uma descrição dos códigos de erro e a ação de cliente recomendado, consulte o artigo [códigos de erro de erros de ponto final token](#error-codes-for-token-endpoint-errors).
