<properties
    pageTitle="Descrição geral do Azure AD .NET Protocol | Microsoft Azure"
    description="Este artigo descreve como utilizar mensagens HTTP para autorizar o acesso a aplicações web e web APIs no seu inquilino através do Azure Active Directory e OpenID ligar."
    services="active-directory"
    documentationCenter=".net"
    authors="priyamohanram"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="priyamo"/>


# <a name="authorize-access-to-web-applications-using-openid-connect-and-azure-active-directory"></a>Autorize acesso às aplicações web que utilizam OpenID ligar e o Azure Active Directory

[Ligar OpenID](http://openid.net/specs/openid-connect-core-1_0.html) é uma camada de identidade simples incorporada na parte superior do protocolo OAuth 2.0. OAuth 2.0 define mecanismos para obter e utilizar **tokens de acesso** para aceder a recursos protegidos, mas não definir métodos padrão para fornecer informações de identidade. Ligar OpenID implementa a autenticação como uma extensão para o processo de autorização de OAuth 2.0, fornecendo informações sobre o utilizador final sob a forma de um `id_token` que verifica a identidade do utilizador, bem como fornece informações de perfil básicas sobre o utilizador.

Ligar OpenID é a nossa recomendação se estiver a criar uma aplicação web que está alojada num servidor e acedida através de um browser.

## <a name="authentication-flow-using-openid-connect"></a>Fluxo de autenticação utilizando OpenID ligar

O fluxo de início de sessão no mais básico contém os passos seguintes - cada um deles é descrito detalhadamente abaixo.

![OpenId ligar fluxo de autenticação](media/active-directory-protocols-openid-connect-code/active-directory-oauth-code-flow-web-app.png)


## <a name="send-the-sign-in-request"></a>Envie o pedido de início de sessão

Quando a aplicação web necessita autenticar o utilizador,-tem direccionar o utilizador para o `/authorize` ponto final. Este pedido é semelhante a primeira ramificação da de [Fluxo de OAuth 2.0 autorização de código](active-directory-protocols-oauth-code.md), com algumas diferenças importantes:

- O pedido tem de incluir o âmbito `openid` na `scope` parâmetro.
- O `response_type` parâmetro tem de incluir `id_token`.
- O pedido tem de incluir o `nonce` parâmetro.

Por isso, um pedido de exemplo teria o seguinte aspeto:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=7362CAEA-9CA5-4B43-9BA3-34D7C303EBA7
```

| Parâmetro | | Descrição |
| ----------------------- | ------------------------------- | --------------- |
| inquilino | obrigatório | O `{tenant}` valor no caminho do pedido de pode ser utilizado para controlar quem pode iniciar sessão na aplicação.  Os valores permitidos são identificadores de inquilino, por exemplo, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` ou `common` inquilino independente tokens de |
| client_id | obrigatório | O Id da aplicação atribuídos a sua aplicação quando que registado com Azure AD. Pode encontrar esta no Portal clássica do Azure. Clique no **Active Directory**, clique no directório, clique na aplicação e, em seguida, clique em **Configurar** |
| response_type | obrigatório | Tem de incluir `id_token` para ligar OpenID iniciar sessão.  Também pode incluir outros response_types, tais como `code`. |
| âmbito | obrigatório | Uma lista de âmbitos espaço separados por vírgulas.  Para ligar a OpenID, deve incluir o âmbito `openid`, que corresponde a permissão de "Sessão neste" na autorização IU função.  Também podem incluir outros âmbitos neste pedido para pedir consentimento. |
| nonce | obrigatório | Um valor incluído no pedido de gerado pela aplicação, que é incluída em resultante `id_token` como uma afirmação.  A aplicação, em seguida, pode verificar este valor para mitigar ataques de repetição token.  O valor é, normalmente, uma cadeia de aleatório, exclusiva ou GUID que pode ser utilizada para identificar a origem do pedido.  |
| redirect_uri | recomendado | Redirect_uri da sua aplicação, onde as respostas de autenticação podem ser enviadas e recebidas por sua aplicação.  Exatamente tem de corresponder um dos redirect_uris registado no portal do, exceto tem de ser codificada de url. |
| response_mode | recomendado | Especifica o método que deve ser utilizado para enviar a authorization_code resultante para a sua aplicação.  Valores suportados são `form_post` para *publicar o formulário HTTP* ou `fragment` para *fragmento de URL*.  Para aplicações web Recomendamos que utilize `response_mode=form_post` para garantir que a transferência mais segura de tokens para a sua aplicação.  
| Estado | recomendado | Um valor incluído no pedido de que também será devolvido na resposta token.  Pode ser uma cadeia de qualquer conteúdo que pretender.  Um valor único gerado aleatoriamente é normalmente utilizado para [prevenir ataques de falsificação de pedido de publicação em vários sites](http://tools.ietf.org/html/rfc6749#section-10.12).  O estado também é utilizado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorreu, por exemplo, a página ou vista que terem sido no. |
| linha de comandos | opcional | Indica o tipo de interação do utilizador que é necessário.  Os valores válidos apenas neste momento são 'login', 'Nenhum,' e 'consentimento'.  `prompt=login`vai forçar ao utilizador que introduza as respetivas credenciais no pedido, a negação sessão único no.  `prompt=none`é o oposto --lo será Certifique-se de que o utilizador não é apresentado com qualquer pedido interativo qualquer.  Se o pedido não pode ser concluído silenciosamente através de sessão único no, o ponto final irá devolver um erro.  `prompt=consent`irá acionar a caixa de diálogo do token consentimento depois do utilizador inicia sessão, perguntar ao utilizador para conceder permissões para a aplicação. |
| login_hint | opcional | Pode ser utilizado para preencher previamente o campo nome de utilizador/e-mail endereço da página para o utilizador, iniciar sessão se souber o nome de utilizador antecedência.  Muitas vezes aplicações irão utilizar este parâmetro durante re-autenticação, já está a ter extraídas o nome de utilizador a partir de um anterior iniciar sessão utilizando o `preferred_username` reclamar. |


Neste momento, o utilizador será pedido para introduzir as suas credenciais e concluir a autenticação.

### <a name="sample-response"></a>Resposta de exemplo

Uma resposta de exemplo, depois do utilizador autenticado, poderia o seguinte aspeto:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| id_token | O `id_token` que a aplicação solicitada. Pode utilizar o `id_token` para verificar a identidade do utilizador e iniciar uma sessão com o utilizador.  |
| Estado | Um valor incluído no pedido de que também será devolvido na resposta token. Um valor único gerado aleatoriamente é normalmente utilizado para [prevenir ataques de falsificação de pedido de publicação em vários sites](http://tools.ietf.org/html/rfc6749#section-10.12).  O estado também é utilizado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorreu, por exemplo, a página ou vista que terem sido no. |

### <a name="error-response"></a>Resposta de erro
Respostas de erro também podem ser enviadas para o `redirect_uri` , de modo a aplicação pode processar corretamente:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
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

## <a name="validate-the-idtoken"></a>Validar o id_token

Apenas a receber um `id_token` não é suficiente para autenticar o utilizador; tem de validar a assinatura e verificar as afirmações na `id_token` por requisitos da sua aplicação. O ponto final do Azure AD utiliza JSON Web Tokens (JWTs) e criptografia de chave pública para iniciar sessão tokens e certifique-se de que são válidos.

Pode optar por validar o `id_token` no cliente de código, mas uma prática comum é enviar a `id_token` para um servidor de back-end e executar a validação aí. Assim que tiver validado a assinatura do `id_token`, existem alguns em afirmações lhe-á ser pedido para confirmar.

Também pode optar por validar afirmações adicionais, dependendo do seu cenário. Algumas validações comuns incluem:

- Garantindo que o utilizador/organização tiver inscrito para a aplicação.
- Garantindo que o utilizador não tem autorização/privilégios inicial
- Assegurar uma determinada regular a intensidade de autenticação ocorreu, tal como a autenticação multifator.

Assim que tiver removido completamente validadas a `id_token`, pode iniciar uma sessão com o utilizador e utilizar as afirmações na `id_token` para obter informações sobre o utilizador na sua aplicação. Esta informação pode ser utilizada para apresentar, registos, autorizações, etc. Para mais informações sobre os tipos de token e em afirmações, leia [Token suportados e os tipos de afirmações](active-directory-token-and-claims.md).

## <a name="send-a-sign-out-request"></a>Enviar um sinal de pedido de

Quando pretender assinar o utilizador terminar a aplicação, não é suficiente limpar a sua aplicação cookies ou caso contrário fim a sessão com o utilizador.  O utilizador também tem de redirecionar o `end_session_endpoint` para terminar sessão.  Se conseguir fazê-lo, o utilizador será possível autenticar novamente para a sua aplicação sem ter de introduzir as suas credenciais novamente, uma vez que terão de uma única início de sessão sessão válida com o ponto final do Azure AD.

Pode simplesmente redireccionar ao utilizador para o `end_session_endpoint` listado no documento de metadados OpenID ligar:

```
GET https://login.microsoftonline.com/common/oauth2/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F

```

| Parâmetro | | Descrição |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | recomendado | O URL que o utilizador deve ser redirecionado para depois de terminar sessão efetuada com êxito.  Se não incluído, o utilizador será mostrado uma mensagem genérica.  |

## <a name="token-acquisition"></a>Aquisição de imagens do token

Precisa de muitas aplicações web não só assinar o utilizador na, mas também aceder a um serviço web em nome esse utilizador utilizando OAuth. Este cenário combina OpenID ligar-se para a autenticação de utilizador enquanto em simultâneo ao adquirir um `authorization_code` que podem ser utilizados para obter `access_tokens` utilizando o fluxo de OAuth autorização de código.

## <a name="get-access-tokens"></a>Obter Tokens de acesso

Para ganhar tokens de acesso, terá de modificar o pedido de início de sessão dos acima:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e      // Your registered Application Id
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered Redirect Uri, url encoded
&response_mode=form_post                              // form_post', or 'fragment'
&scope=openid
&resource=https%3A%2F%2Fservice.contoso.com%2F                                   
&state=12345                                         // Any value, provided by your app
&nonce=678910                                        // Any value, provided by your app
```

Incluindo âmbitos de permissão no pedido de e utilizando `response_type=code+id_token`, o `authorize` ponto final irá garantir que o utilizador deu às permissões indicadas na `scope` parâmetro de consulta e voltar a sua aplicação um código de autorização para exchange para um token de acesso.

### <a name="successful-response"></a>Resposta com êxito

Utilizar uma resposta com êxito `response_mode=form_post` é semelhante a:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| id_token | O `id_token` que a aplicação solicitada. Pode utilizar o `id_token` para verificar a identidade do utilizador e iniciar uma sessão com o utilizador. |
| código | Authorization_code que a aplicação solicitada. A aplicação pode utilizar o código de autorização para pedir um token de acesso para o recurso de destino. Authorization_codes são muito curta, normalmente expirem após cerca de 10 minutos. |
| Estado | Se um parâmetro de estado é incluído no pedido, o mesmo valor deverá aparecer na resposta. A aplicação deve verificar que os valores de estado no pedidos e respostas são idênticos. |

### <a name="error-response"></a>Resposta de erro

Respostas de erro também podem ser enviadas para o `redirect_uri` , de modo a aplicação pode processam adequadamente:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| erro | Uma cadeia de código de erro que pode ser utilizada para classificar tipos de erros que ocorrem e pode ser utilizada para responder a erros. |
| error_description | Uma mensagem de erro específico pode ajudar a um programador identificar o problema de raiz de um erro de autenticação.  |

Para obter uma descrição dos códigos de erro possíveis e os respetivos ação de cliente recomendado, consulte o artigo [códigos de erro de erros de ponto final de autorização](#error-codes-for-authorization-endpoint-errors).

Assim que tiver obteve autorização `code` e um `id_token`, pode início de sessão do utilizador e obter tokens de acesso em nome dos utilizadores.  Para iniciar a sessão de utilizador no, tem de validar o `id_token` exatamente como descrito acima. Para obter tokens de acesso, pode seguir os passos descritos na nossa [documentação do protocolo OAuth](active-directory-protocols-oauth-code.md#Use-the-Authorization-Code-to-Request-an-Access-Token).
