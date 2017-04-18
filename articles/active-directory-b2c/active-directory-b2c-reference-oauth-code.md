<properties
    pageTitle="Azure Active Directory B2C | Microsoft Azure"
    description="Criação de aplicações web utilizando a implementação do Azure Active Directory do protocolo de autenticação OpenID ligar."
    services="active-directory-b2c"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/22/2016"
    ms.author="dastrock"/>

# <a name="azure-active-directory-b2c-oauth-20-authorization-code-flow"></a>Azure Active B2C de diretório: Fluxo de código OAuth 2.0 autorização

A conceder de código de autorização de OAuth 2.0 pode ser utilizado nas aplicações que são instaladas num dispositivo para aceder à recursos protegidos, como web APIs. Ao utilizar a implementação do Azure Active Directory (Azure AD) B2C do token 2.0, pode adicionar tarefas de gestão de identidades inscrição, iniciar sessão e outros as suas aplicações de ambiente de trabalho e móvel. Este guia é independente do idioma. Descreve como enviar e receber mensagens HTTP sem utilizar qualquer um dos nossos bibliotecas abrir origem.

<!-- TODO: Need link to libraries -->

O fluxo de código de autorização de OAuth 2.0 é descrito na [secção 4.1 da especificação OAuth 2.0](http://tools.ietf.org/html/rfc6749). Pode utilizá-lo para efetuar a autenticação e autorização na maioria dos tipos de aplicação, incluindo [web apps](active-directory-b2c-apps.md#web-apps) e [vierem instalado aplicações](active-directory-b2c-apps.md#mobile-and-native-apps). Permite-aplicações em segurança adquirir **access_tokens**, que podem ser utilizados para recursos que são protegidos por um [servidor de autorização](active-directory-b2c-reference-protocols.md#the-basics)de acesso.

Este guia irá focar uma versão específica dos OAuth 2.0 autorização código fluxo –**clientes públicos**. Um cliente público é qualquer aplicação de cliente que não pode ser considerado fidedigna para segura manter a integridade de uma palavra-passe secreta. Isto inclui aplicações móveis, aplicações de ambiente de trabalho e muito muito qualquer aplicação que é executado num dispositivo e precisa de obter access_tokens. Se pretender adicionar a gestão de identidades para uma aplicação web utilizando o Azure AD B2C, deve utilizar [OpenID ligar-se](active-directory-b2c-reference-oidc.md) em vez de OAuth 2.0.

Azure AD B2C expande a norma que OAuth 2.0 flua fazer mais do que simples autenticação e autorização. Apresenta-o [**parâmetro de política**](active-directory-b2c-reference-policies.md), que permite-lhe utilizar OAuth 2.0 para adicionar experiências do utilizador para a sua aplicação, tais como inscrição, iniciar sessão e gestão de perfil. Aqui vamos mostrar como utilizar e políticas de OAuth 2.0 para implementar cada uma das seguintes experiências nas suas aplicações nativas. Vamos também mostrar como obter access_tokens para aceder à web APIs.

Irão utilizar os pedidos HTTP de exemplo abaixo diretório de B2C nosso exemplo, **fabrikamb2c.onmicrosoft.com**, bem como nossa aplicação de exemplo e políticas de. Estiver livre experimentar os pedidos de saída-se ao utilizar estes valores, ou pode substituí-los com o seu próprio.
Saiba como [obter o seu próprio B2C diretório, à aplicação e políticas de](#use-your-own-b2c-directory).

## <a name="1-get-an-authorization-code"></a>1. a obter um código de autorização
O fluxo de código autorização começa com o cliente que encaminha ao utilizador para o `/authorize` ponto final. Esta é a parte interativa de fluxo, onde o utilizador realmente a tomar medidas. No pedido, o cliente indica as permissões que necessita para adquirir a partir do utilizador na `scope` parâmetro e a política para executar na `p` parâmetro. Três exemplos são fornecidos abaixo (com quebras de linha para legibilidade), cada uma com uma política diferente.

#### <a name="use-a-sign-in-policy"></a>Utilizar uma política de início de sessão

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-policy"></a>Utilizar uma política de inscrição

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-policy"></a>Utilizar uma política de Editar perfil

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_edit_profile
```

| Parâmetro | Obrigatório? | Descrição |
| ----------------------- | ------------------------------- | ----------------------- |
| client_id | Obrigatório | O ID da aplicação que o [Azure portal](https://portal.azure.com) atribuídos para a sua aplicação. |
| response_type | Obrigatório | Tipo de resposta, tem de incluir `code` para o fluxo de código de autorização. |
| redirect_uri | Obrigatório | Redirect_uri da sua aplicação, onde as respostas de autenticação podem ser enviadas e recebidas por sua aplicação. -Deve corresponder exatamente a um da redirect_uris que registado no portal do, exceto que tem de ser codificada de URL. |
| âmbito | Obrigatório | Uma lista de âmbitos espaço separados por vírgulas. Um valor único âmbito indica ao Azure AD ambas das permissões que estão a ser pedido. Utilizar o ID de cliente, tal como o âmbito indica que a sua aplicação precisa um **token de acesso** que podem ser utilizadas contra a sua própria serviço ou da web API, representado pelo mesmo ID de cliente.  O `offline_access` âmbito indica que a aplicação irá precisar de um **refresh_token** para longa vida acesso aos recursos.  Também pode utilizar o `openid` âmbito pedir um **id_token** a partir do Azure AD B2C. |
| response_mode | Recomendado | O método que deve ser utilizado para enviar a authorization_code resultante para a sua aplicação. Pode ser um dos 'query', 'form_post' ou 'fragmento'.
| Estado | Recomendado | Um valor incluído no pedido de que também será devolvido na resposta token. Pode ser uma cadeia de qualquer conteúdo que pretende. Um valor único gerado aleatoriamente é normalmente utilizado para prevenir ataques de falsificação de pedido de publicação em vários sites. O estado também é utilizado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorreu, por exemplo, a página de que terem sido no ou a política de que está a ser executado. |
| p | Obrigatório | A política de que irão ser executada. É o nome de uma política que é criado no seu diretório B2C. O valor de nome de política deverá começar com "b2c\_1\_". Saiba mais sobre as políticas no [quadro de política Extensible](active-directory-b2c-reference-policies.md). |
| linha de comandos | Opcional | O tipo de interação do utilizador que é necessário. O único valor válido neste momento é 'login', força ao utilizador que introduza as respetivas credenciais no pedido. O início de sessão único não terão efeito. |

Neste momento, será pedido ao utilizador para concluir o fluxo de trabalho a política. Isto pode implicar o utilizador introduzir o seu nome de utilizador e palavra-passe, iniciar sessão com uma identidade de rede social, inscrever-se para o directório ou qualquer outro número dos passos, dependendo de como a política estiver definida.

Quando o utilizador for concluída a política, Azure AD irá devolver uma resposta para a sua aplicação a indicada na `redirect_uri`, utilizando o método especificado na `response_mode` parâmetro. A resposta será exatamente o mesmo para cada um dos casos acima, independentemente da política que foi executada.

Uma resposta com êxito, que utiliza `response_mode=query` é semelhante a:

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| código | Authorization_code que a aplicação solicitada. A aplicação pode utilizar o código de autorização para pedir um access_token para um recurso de destino. Authorization_codes são muito curta. Normalmente, expirem, após cerca de 10 minutos. |
| Estado | Consulte a descrição completa na tabela anterior. Se um parâmetro de estado é incluído no pedido, o mesmo valor deverá aparecer na resposta. A aplicação deve verificar que os valores de estado do pedido e a resposta são idênticos. |

Respostas de erro também podem ser enviadas para o `redirect_uri` para que a aplicação pode processar corretamente:

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| erro | Uma cadeia de código de erro que pode ser utilizada para classificar os tipos de erros que ocorrem e pode ser utilizada para responder a erros. |
| error_description | Uma mensagem de erro específico que o pode ajudar um programador para identificar o problema de raiz de um erro de autenticação. |
| Estado | Consulte a descrição completa na primeira tabela nesta secção. Se um parâmetro de estado é incluído no pedido, o mesmo valor deverá aparecer na resposta. A aplicação deve verificar que os valores de estado do pedido e a resposta são idênticos. |


## <a name="2-get-a-token"></a>2. a obter um token
Agora que tenha adquirido uma authorization_code, pode resgatar a `code` para obter um token para o recurso pretendido ao enviar um `POST` pedir para o `/token` ponto final. No Azure AD B2C, o único recurso pode pedir um token para é web back-end API da aplicação. A Convenção que é utilizada para pedir um token para si mesmo é utilizar o ID de cliente da sua aplicação como o âmbito:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| Parâmetro | Obrigatório? | Descrição |
| ----------------------- | ------------------------------- | --------------------- |
| p | Obrigatório | A política de que foi utilizada para adquirir o código de autorização. Não é possível utilizar uma política diferente este pedido. Repare que adicionar este parâmetro com a *cadeia de consulta*, não no corpo da mensagem. |
| client_id | Obrigatório | O ID da aplicação que o [Azure portal](https://portal.azure.com) atribuídos para a sua aplicação. |
| grant_type | Obrigatório | O tipo de conceder, que tem de ser `authorization_code` para o fluxo de código de autorização. |
| âmbito | Reccommended | Uma lista de âmbitos espaço separados por vírgulas. Um valor único âmbito indica ao Azure AD ambas das permissões que estão a ser pedido. Utilizar o ID de cliente, tal como o âmbito indica que a sua aplicação precisa um **token de acesso** que podem ser utilizadas contra a sua própria serviço ou da web API, representado pelo mesmo ID de cliente.  O `offline_access` âmbito indica que a aplicação irá precisar de um **refresh_token** para longa vida acesso aos recursos.  Também pode utilizar o `openid` âmbito pedir um **id_token** a partir do Azure AD B2C. |
| código | Obrigatório | Authorization_code que o utilizador tiver adquirido na primeira ramificação do fluxo de. |
| redirect_uri | Obrigatório | Redirect_uri da aplicação em que recebeu a authorization_code. |

Uma resposta token com êxito terá o seguinte aspeto:

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| not_before | A hora em que o token considera válido, a altura de época. |
| token_type | O valor de tipo de token. O único tipo que suporte do Azure AD é o tipo de ligação. |
| access_token | O token de JSON Web Token (JWT) com a sessão iniciada pretendida. |
| âmbito | Os âmbitos de que o token é válido, que podem ser utilizados para colocação em cache tokens para utilizar posteriormente. |
| expires_in | O período de tempo que o token é válido (em segundos). |
| refresh_token | Um refresh_token OAuth 2.0. A aplicação pode utilizar este token para adquirir tokens adicionais depois do token actual expira. Refresh_tokens tempo são viveu e podem ser utilizados para reter o acesso aos recursos por adicional períodos de tempo. Para obter mais detalhes, consulte a [referência de token B2C](active-directory-b2c-reference-tokens.md). |

Respostas de erro terá o seguinte aspeto:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| erro | Uma cadeia de código de erro que pode ser utilizada para classificar os tipos de erros que ocorrem e pode ser utilizada para responder a erros. |
| error_description | Uma mensagem de erro específico que o pode ajudar um programador para identificar o problema de raiz de um erro de autenticação. |

## <a name="3-use-the-token"></a>3. utilizar o token de
Agora que já adquirida com êxito um `access_token`, pode utilizar o token nos pedidos para sua back-end web APIs incluindo-o na `Authorization` cabeçalho:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. atualizar o token
Tokens de acesso e Id são curta. Tem de atualizá-las após expiram para continuar a ser possível aceder à recursos. Pode fazê-lo, apresentando outro `POST` pedir para o `/token` ponto final. Desta vez fornecer a `refresh_token` em vez do `code`:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parâmetro | Obrigatório? | Descrição |
| ----------------------- | ------------------------------- | -------- |
| p | Obrigatório | A política de que foi utilizada para adquirir o refresh_token original. Não é possível utilizar uma política diferente este pedido. Repare que adicionar este parâmetro com a *cadeia de consulta*, não no corpo da mensagem. |
| client_id | Recomendado | O ID da aplicação que o [Azure portal](https://portal.azure.com) atribuídos para a sua aplicação. |
| grant_type | Obrigatório | O tipo de conceder, que tem de ser `refresh_token` para este ramificação do fluxo de código de autorização de. |
| âmbito | Recomendado | Uma lista de âmbitos espaço separados por vírgulas. Um valor único âmbito indica ao Azure AD ambas das permissões que estão a ser pedido. Utilizar o ID de cliente, tal como o âmbito indica que a sua aplicação precisa um **token de acesso** que podem ser utilizadas contra a sua própria serviço ou da web API, representado pelo mesmo ID de cliente.  O `offline_access` âmbito indica que a aplicação irá precisar de um **refresh_token** para longa vida acesso aos recursos.  Também pode utilizar o `openid` âmbito pedir um **id_token** a partir do Azure AD B2C. |
| redirect_uri | Opcional | Redirect_uri da aplicação em que recebeu a authorization_code. |
| refresh_token | Obrigatório | O refresh_token original que o utilizador tiver adquirido na segunda ramificação do fluxo da. |

Uma resposta token com êxito terá o seguinte aspeto:

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| not_before | A hora em que o token considera válido, a altura de época. |
| token_type | O valor de tipo de token. O único tipo que suporte do Azure AD é o tipo de ligação. |
| access_token | O token de JSON Web Token (JWT) com a sessão iniciada pretendida. |
| âmbito | Os âmbitos de que o token é válido, que podem ser utilizados para colocação em cache tokens para utilizar posteriormente. |
| expires_in | O período de tempo que o token é válido (em segundos). |
| refresh_token | Um refresh_token OAuth 2.0. A aplicação pode utilizar este token para adquirir tokens adicionais depois do token actual expira. Refresh_tokens tempo são viveu e podem ser utilizados para reter o acesso aos recursos por adicional períodos de tempo. Para obter mais detalhes, consulte a [referência de token B2C](active-directory-b2c-reference-tokens.md). |

Respostas de erro terá o seguinte aspeto:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| erro | Uma cadeia de código de erro que pode ser utilizada para classificar tipos de erros que ocorrem e pode ser utilizada para responder a erros. |
| error_description | Uma mensagem de erro específico pode ajudar a um programador identificar o problema de raiz de um erro de autenticação. |

## <a name="use-your-own-b2c-directory"></a>Utilizar o seu próprio directory B2C

Se quiser experimentar estes pedidos para si próprio, tem primeiro executar estes três passos e, em seguida, substitua os valores de exemplo acima pelas suas próprias:

- [Criar um diretório de B2C](active-directory-b2c-get-started.md) e utilize o nome do seu diretório nos pedidos.
- [Criar uma aplicação](active-directory-b2c-app-registration.md) para obter um ID da aplicação e um redirect_uri. Pretende de incluir um **cliente nativo** na sua aplicação.
- [Criar as suas políticas](active-directory-b2c-reference-policies.md) para obter os seus nomes de política.
