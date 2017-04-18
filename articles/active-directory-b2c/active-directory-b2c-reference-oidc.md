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

# <a name="azure-active-directory-b2c-web-sign-in-with-openid-connect"></a>Azure B2C diretório ativos: Web iniciar sessão com OpenID ligar

OpenID ligar é um protocolo de autenticação, incorporado na parte superior de OAuth 2.0, que pode ser utilizado para assinar em segurança utilizadores para aplicações web.  Ao utilizar a aplicação do Azure Active Directory (Azure AD) B2C da OpenID ligar, pode deixem inscrição, iniciar sessão e outros gestão de identidades experiências nas aplicações web para Azure AD. Este guia mostrar-lhe como fazê-lo um idioma de forma independente da.. -Lo será descrevem como enviar e receber mensagens HTTP sem utilizar qualquer um dos nossos bibliotecas abrir origem.

[Ligar OpenID](http://openid.net/specs/openid-connect-core-1_0.html) expande o protocolo de *autorização* de OAuth 2.0 para utilização como protocolo de *autenticação* . Esta opção permite-lhe executar o início de sessão único utilizando OAuth. Apresenta-o conceito de um `id_token`, que é um token de segurança que permite que o cliente verificar a identidade do utilizador e obter informações de perfil básicas sobre o utilizador.

Uma vez que expande a OAuth 2.0, também permite aplicações em segurança adquirir **access_tokens**. Pode utilizar access_tokens para aceder aos recursos que são protegidos por um [servidor de autorização](active-directory-b2c-reference-protocols.md#the-basics). Recomendamos que ligue OpenID se estiver a criar uma aplicação web que está alojada num servidor e acedida através de um browser. Se pretender adicionar a gestão de identidades para as suas aplicações de ambiente de trabalho ou telemóveis utilizando Azure AD B2C, deve utilizar [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) em vez de OpenID ligar.

Azure AD B2C expande o protocolo OpenID ligar padrão para fazer mais do que simples autenticação e autorização. Apresenta-o [**parâmetro de política**](active-directory-b2c-reference-policies.md), que permite-lhe utilizar OpenID ligar para adicionar como utilizador experiências para a sua aplicação – inscrição, iniciar sessão e gestão de perfil. Aqui vamos mostrar-lhe como utilizar OpenID ligar e políticas para implementar cada uma das seguintes experiências nas aplicações web. Vamos também mostrar-lhe como pode obter access_tokens para aceder à web APIs.

Irão utilizar os pedidos HTTP de exemplo abaixo diretório de B2C nosso exemplo, **fabrikamb2c.onmicrosoft.com**, bem como nosso exemplo aplicação **https://aadb2cplayground.azurewebsites.net** e políticas de. Estiver livre experimentar os pedidos de si próprio utilizando estes valores, ou pode substituí-los com o seu próprio.
Saiba como [obter o seu próprio B2C inquilino, à aplicação e políticas de](#use-your-own-b2c-directory).

## <a name="send-authentication-requests"></a>Enviar pedidos de autenticação
Quando a aplicação web do necessita autenticar o utilizador e executar uma política,-pode indicar ao utilizador para o `/authorize` ponto final. Esta é a parte interativa de fluxo, onde o utilizador realmente irá demorar a ação, consoante a política.

No pedido, o cliente indica as permissões que necessita para adquirir a partir do utilizador na `scope` parâmetro e a política para executar na `p` parâmetro. Três exemplos são fornecidos abaixo (com quebras de linha para legibilidade), cada uma com uma política diferente. Para ter uma ideia de como funciona a cada pedido, tente colando o pedido de um browser e executá-la.

#### <a name="use-a-sign-in-policy"></a>Utilizar uma política de início de sessão

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-policy"></a>Utilizar uma política de inscrição

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-policy"></a>Utilizar uma política de Editar perfil

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| Parâmetro | Obrigatório? | Descrição |
| ----------------------- | ------------------------------- | ----------------------- |
| client_id | Obrigatório | O ID da aplicação que o [Azure portal](https://portal.azure.com/) atribuídos para a sua aplicação. |
| response_type | Obrigatório | Tipo de resposta, tem de incluir `id_token` para OpenID ligar. Se a sua aplicação web também será necessário tokens para ligar a um web API, pode utilizar `code+id_token`, tal como conseguimos aqui. |
| redirect_uri | Recomendado | Redirect_uri da sua aplicação, onde as respostas de autenticação podem ser enviadas e recebidas por sua aplicação. -Deve corresponder exatamente a um da redirect_uris que registado no portal do, exceto que tem de ser codificada de URL. |
| âmbito | Obrigatório | Uma lista de âmbitos espaço separados por vírgulas. Um valor único âmbito indica ao Azure AD ambos das permissões que estão a ser pedido. O `openid` âmbito indica uma permissão para o utilizador de início de sessão e obter dados sobre o utilizador a forma de **id_tokens** (mais informações em breve nisto posteriormente neste artigo). O `offline_access` âmbito é opcional para web apps. Indica que a aplicação irá precisar de um **refresh_token** para longa vida acesso aos recursos. |
| response_mode | Recomendado | O método que deve ser utilizado para enviar a authorization_code resultante para a sua aplicação. Pode ser um dos 'query', 'form_post' ou 'fragmento'.  'form_post' é recomendado para melhor segurança. |
| Estado | Recomendado | Um valor incluído no pedido de que também será devolvido na resposta token. Pode ser uma cadeia de qualquer conteúdo que pretende. Um valor único gerado aleatoriamente é normalmente utilizado para prevenir ataques de falsificação de pedido de publicação em vários sites. O estado também é utilizado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorreu, por exemplo, a página de que terem sido no. |
| nonce | Obrigatório | Um valor incluído no pedido de (gerado pela aplicação) que será incluído na id_token resultante como uma afirmação. A aplicação, em seguida, pode verificar este valor para mitigar ataques de repetição token. O valor é, normalmente, uma cadeia de aleatório e único que pode ser utilizada para identificar a origem do pedido. |
| p | Obrigatório | A política de que irão ser executada. É o nome de uma política que é criado no seu inquilino B2C. O valor de nome de política deverá começar com "b2c\_1\_". Saiba mais sobre as políticas no [quadro de política Extensible](active-directory-b2c-reference-policies.md). |
| linha de comandos | Opcional | O tipo de interação do utilizador que é necessário. O único valor válido neste momento é 'login', força ao utilizador que introduza as respetivas credenciais no pedido. O início de sessão único não terão efeito. |

Neste momento, será pedido ao utilizador para concluir o fluxo de trabalho a política. Isto pode implicar o utilizador introduzir o seu nome de utilizador e palavra-passe, iniciar sessão com uma identidade de rede social, inscrever-se para o directório ou qualquer outro número dos passos, dependendo de como a política estiver definida.

Quando o utilizador for concluída a política, Azure AD irá devolver uma resposta para a sua aplicação a indicada na `redirect_uri`, utilizando o método especificado na `response_mode` parâmetro. A resposta será exatamente o mesmo para cada um dos casos acima, independentemente da política que foi executada.

Utilizar uma resposta com êxito `response_mode=fragment` teria o seguinte aspeto:

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| id_token | Id_token que a aplicação solicitada. Pode utilizar o id_token para verificar a identidade do utilizador e iniciar uma sessão com o utilizador. Obter mais detalhes sobre id_tokens e os seus conteúdos são incluídos na [referência token do Azure AD B2C](active-directory-b2c-reference-tokens.md). |
| código | O authorization_code que a aplicação solicitada, se tiver utilizado `response_type=code+id_token`. A aplicação pode utilizar o código de autorização para pedir um access_token para um recurso de destino. Authorization_codes são muito curta. Normalmente, expirem, após cerca de 10 minutos. |
| Estado | Se um parâmetro de estado é incluído no pedido, o mesmo valor deverá aparecer na resposta. A aplicação deve verificar que os valores de estado no pedidos e respostas são idênticos. |

Respostas de erro também podem ser enviadas para o `redirect_uri` para que a aplicação pode processar corretamente:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| erro | Uma cadeia de código de erro que pode ser utilizada para classificar tipos de erros que ocorrem e pode ser utilizada para responder a erros. |
| error_description | Uma mensagem de erro específico pode ajudar a um programador identificar o problema de raiz de um erro de autenticação. |
| Estado | Consulte a descrição completa na tabela anterior. Se um parâmetro de estado é incluído no pedido, o mesmo valor deverá aparecer na resposta. A aplicação deve verificar que os valores de estado no pedidos e respostas são idênticos. |


## <a name="validate-the-idtoken"></a>Validar o id_token
Apenas a receber um id_token, não é suficiente para autenticar o utilizador – tem de validar assinatura a id_token e verificar as afirmações no token de acordo com os requisitos da sua aplicação. Azure AD B2C utiliza [JSON Web Tokens (JWTs)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e criptografia de chave pública para iniciar sessão tokens e certifique-se de que são válidos.

Existem várias bibliotecas abrir origem que estão disponíveis para validar JWTs, dependendo do seu idioma de preferência. Recomendamos que explorar estas opções em vez de implementar a sua própria lógica de validação. As informações aqui será útil para perceber como utilizar corretamente nessas bibliotecas.

Azure AD B2C tem um extremo de metadados OpenID ligar, que lhe permite uma aplicação obter informações sobre Azure AD B2C o tempo de execução. Estas informações incluem os pontos finais, conteúdo de tokens e token de chaves de assinatura. Existe um documento de metadados JSON para cada política no seu inquilino B2C. Por exemplo o documento de metadados para o `b2c_1_sign_in` política na `fabrikamb2c.onmicrosoft.com` está localizado em:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Uma das propriedades do documento configuração é o `jwks_uri`, cujo valor para a mesma política seria:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`.

Na ordem para determinar qual a política foi utilizada na assinatura um id_token (e where obter os metadados do), tem duas opções. Em primeiro lugar, o nome da política é incluído na `acr` reclamar na id_token. Para obter informações sobre como analisar as afirmações a partir de um id_token, consulte a [referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md). A outra opção é codificar a política do valor da `state` parâmetro quando emitir o pedido e, em seguida, codificá-lo para determinar qual a política foi utilizada. Qualquer método indicado é perfeitamente válido.

Depois de ter adquirido o documento de metadados a partir do ponto final de metadados OpenID ligar, pode utilizar as chaves públicas RSA 256 (que sejam encontram neste ponto final) para validar a assinatura do id_token. Poderão existir múltiplas teclas listadas neste ponto final em qualquer altura determinada altura, cada uma identificada por um `kid`. O cabeçalho da id_token também contém um `kid` reclamar, que indica que estas teclas foi utilizado para assinar o id_token. Consulte a [referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md) para obter mais informações, incluindo secções sobre [Validar tokens](active-directory-b2c-reference-tokens.md#validating-tokens) e [informações importantes sobre como iniciar sessão chave passagem com o rato](active-directory-b2c-reference-tokens.md#validating-tokens).
<!--TODO: Improve the information on this-->

Após ter validado a assinatura do id_token, existem várias em afirmações que precisa para verificar, por exemplo:

- Deve a validar o `nonce` reclamar impedir que ataques de repetição token. O valor deve ser que especificou no pedido de iniciar sessão.
- Deve a validar o `aud` reclamar para se certificar de que o id_token foi emitido para a sua aplicação. O valor deve ser o ID da aplicação da sua aplicação.
- Deve a validar o `iat` e `exp` afirmações para se certificar de que o id_token não expirou.

Também existem vários validações mais que deve executar, descrito detalhadamente a [Especificação de Core ligar OpenID](http://openid.net/specs/openid-connect-core-1_0.html).  Também poderá querer validar afirmações adicionais, dependendo do seu cenário. Algumas validações comuns incluem:

- Certificar-se de que o utilizador/organização se inscreve para a aplicação.
- Certificar-se de que o utilizador tem autorização/privilégios inicial maiúscula.
- Certificar-se de que um determinada regular a intensidade de autenticação ocorreu, tal como Azure a autenticação Multifator.

Para mais informações sobre as afirmações num id_token, consulte a [referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md).

Após ter completamente validado a id_token, pode iniciar uma sessão com o utilizador e utilize as afirmações a id_token para obter informações sobre o utilizador na sua aplicação. Esta informação pode ser utilizada para apresentar, registos, autorização e assim sucessivamente.

## <a name="get-a-token"></a>Obter um token
Se tudo o que as suas necessidades da aplicação web fazer executar políticas, pode ignorar as secções seguintes alguns. Estas secções só são aplicáveis a web as aplicações que precisa de efetuar autenticados chamadas para um web API e também estão protegidas por Azure AD B2C.

Pode resgatar a authorization_code que o utilizador tiver adquirido (utilizando `response_type=code+id_token`) para obter um token para o recurso pretendido ao enviar um `POST` pedir para o `/token` ponto final. Atualmente, o único recurso pode pedir um token para está a web de back-end da aplicação API. É utilizar o ID de cliente da sua aplicação como o âmbito da Convenção para pedir um token para si mesmo:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>

```

| Parâmetro | Obrigatório? | Descrição |
| ----------------------- | ------------------------------- | --------------------- |
| p | Obrigatório | A política de que foi utilizada para adquirir o código de autorização. Não é possível utilizar uma política diferente este pedido. Repare que adicionar este parâmetro com a **cadeia de consulta**, não no corpo da mensagem. |
| client_id | Obrigatório | O ID da aplicação que o [Azure portal](https://portal.azure.com/) atribuídos para a sua aplicação. |
| grant_type | Obrigatório | O tipo de conceder, que tem de ser `authorization_code` para o fluxo de código de autorização. |
| âmbito | Recomendado | Uma lista de âmbitos espaço separados por vírgulas. Um valor único âmbito indica ao Azure AD ambos das permissões que estão a ser pedido. O `openid` âmbito indica uma permissão para o utilizador de início de sessão e obter dados sobre o utilizador a forma de **id_tokens**. Pode ser utilizado para obter tokens a sua própria aplicação back-end da web API, que é representado pelo mesmo ID da aplicação como o cliente. O `offline_access` âmbito indica que a aplicação irá precisar de um **refresh_token** para longa vida acesso aos recursos. |
| código | Obrigatório | Authorization_code que o utilizador tiver adquirido na primeira ramificação do fluxo de. |
| redirect_uri | Obrigatório | Redirect_uri da aplicação em que recebeu a authorization_code. |
| client_secret | Obrigatório | O segredo de aplicação que gerou no [portal do Azure](https://portal.azure.com/). Esta aplicação o segredo é um artifício segurança importantes. Deve armazená-lo em segurança no servidor. Também deverá tomar cuidado para rodar Este segredo cliente numa base periódica. |

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
| access_token | Com a sessão iniciada token JWT pretendida. |
| âmbito | Os âmbitos de que o token é válido, que podem ser utilizados para colocação em cache tokens para utilizar posteriormente. |
| expires_in | O período de tempo que o access_token é válida (em segundos). |
| refresh_token | Um refresh_token OAuth 2.0. A aplicação pode utilizar este token para adquirir tokens adicionais depois do token actual expira.  Refresh_tokens tempo são viveu e podem ser utilizados para reter o acesso aos recursos por adicional períodos de tempo. Para obter mais detalhes, consulte a [referência de token B2C](active-directory-b2c-reference-tokens.md). Nota que tem de ter utilizado o âmbito `offline_access` na autorização e tokens pedidos para receberem uma refresh_token. |

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

## <a name="use-the-token"></a>Utilizar o token
Agora que já adquirida com êxito um `access_token`, pode utilizar o token nos pedidos para sua back-end web APIs incluindo-o na `Authorization` cabeçalho:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Atualizar o token
O id_tokens são curta. Tem de atualizá-las após expiram para continuar a ser possível aceder à recursos. Pode fazê-lo, apresentando outro `POST` pedir para o `/token` ponto final. Desta vez, fornecer as `refresh_token` em vez do `code`:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| Parâmetro | Obrigatório | Descrição |
| ----------------------- | ------------------------------- | -------- |
| p | Obrigatório | A política de que foi utilizada para adquirir o refresh_token original. Não é possível utilizar uma política diferente este pedido. Repare que adicionar este parâmetro com a **cadeia de consulta**, não no corpo da mensagem. |
| client_id | Obrigatório | O ID da aplicação que o [Azure portal](https://portal.azure.com/) atribuídos para a sua aplicação. |
| grant_type | Obrigatório | O tipo de conceder, que tem de ser `refresh_token` para este ramificação do fluxo de código de autorização de. |
| âmbito | Recomendado | Uma lista de âmbitos espaço separados por vírgulas. Um valor único âmbito indica ao Azure AD ambos das permissões que estão a ser pedido. O `openid` âmbito indica uma permissão para o utilizador de início de sessão e obter dados sobre o utilizador a forma de **id_tokens**. Pode ser utilizado para obter tokens a sua própria aplicação back-end da web API, que é representado pelo mesmo ID da aplicação como o cliente. O `offline_access` âmbito indica que a aplicação irá precisar de um **refresh_token** para longa vida acesso aos recursos. |
| redirect_uri | Recomendado | Redirect_uri da aplicação em que recebeu a authorization_code. |
| refresh_token | Obrigatório | O refresh_token original que o utilizador tiver adquirido na segunda ramificação do fluxo da. Nota que tem de ter utilizado o âmbito `offline_access` na autorização e tokens pedidos para receberem uma refresh_token. |
| client_secret | Obrigatório | O segredo de aplicação que gerou no [portal do Azure](https://portal.azure.com/). Esta aplicação o segredo é um artifício segurança importantes. Deve armazená-lo em segurança no servidor. Também deverá tomar cuidado para rodar Este segredo cliente numa base periódica. |

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
| access_token | Com a sessão iniciada token JWT pretendida. |
| âmbito | Os âmbitos de que o token é válido, que podem ser utilizados para colocação em cache tokens para utilizar posteriormente. |
| expires_in | O período de tempo que o access_token é válida (em segundos). |
| refresh_token | Um refresh_token OAuth 2.0. A aplicação pode utilizar este token para adquirir tokens adicionais depois do token actual expira.  Refresh_tokens tempo são viveu e podem ser utilizados para reter o acesso aos recursos por adicional períodos de tempo. Para obter mais detalhes, consulte a [referência de token B2C](active-directory-b2c-reference-tokens.md). |

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


## <a name="send-a-sign-out-request"></a>Enviar um pedido de terminar sessão

Quando quiser iniciar o utilizador terminar a aplicação, não é suficiente limpar a sua aplicação cookies ou caso contrário fim a sessão com o utilizador. O utilizador também tem de redirecionar Azure AD para terminar sessão. Se conseguir fazê-lo, o utilizador poderá conseguir autenticar novamente para a sua aplicação sem ter de introduzir novamente as suas credenciais. Isto acontece porque terão uma única início de sessão sessão válida com Azure AD.

Pode simplesmente redireccionar ao utilizador para o `end_session_endpoint` que esteja listado no mesmo OpenID ligar metadados documento descrito na secção anterior "Validar o id_token":

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parâmetro | Obrigatório? | Descrição |
| ----------------------- | ------------------------------- | ------------ |
| p | Obrigatório | A política de que pretende utilizar para iniciar a sessão do utilizador fora da sua aplicação. |
| post_logout_redirect_uri | Recomendado | O URL que o utilizador deve ser redirecionado para após bem sucedida terminar sessão. Se não for incluído, o utilizador será apresentado uma mensagem genérica por Azure AD B2C.  |

> [AZURE.NOTE]
    Enquanto que encaminha ao utilizador para o `end_session_endpoint` irá limpar algumas do Estado do utilizador única início de sessão com o Azure AD B2C, não irá iniciar sessão do utilizador terminar a sessão do utilizador identidade social fornecedor (IDP). Se o utilizador seleciona o mesmo IDP durante uma sessão subsequente, estes irão ser novamente autenticados, sem ter de introduzir as suas credenciais. Se pretender que um utilizador terminar a sua aplicação B2C, não seja necessariamente significa que pretendem terminar sessão na sua conta de Facebook completamente. No entanto, no caso de contas locais, a sessão do utilizador será terminada corretamente.

## <a name="use-your-own-b2c-tenant"></a>Utilizar o seu próprio inquilino B2C

Se quiser experimentar estes pedidos para si próprio, tem primeiro executar estes três passos e, em seguida, substitua os valores de exemplo acima pelas suas próprias:

- [Criar um inquilino B2C](active-directory-b2c-get-started.md)e utilize o nome do seu inquilino nos pedidos.
- [Criar uma aplicação](active-directory-b2c-app-registration.md) para obter um ID da aplicação e um redirect_uri. Que pretende incluir uma **app/web da web api** na sua aplicação e, opcionalmente, crie um **secreta de aplicação**.
- [Criar as suas políticas](active-directory-b2c-reference-policies.md) para obter os seus nomes de política.
