<properties
    pageTitle="Azure AD 2.0 implícito fluxo | Microsoft Azure"
    description="Criar aplicações web com a implementação do Azure AD 2.0 do fluxo implícito para aplicações única página."
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

# <a name="v20-protocols---spas-using-the-implicit-flow"></a>2.0 protocolos - estações termais utilizando o fluxo implícito
Com o ponto final 2.0, pode iniciar sessão utilizadores para as suas aplicações única página com contas pessoais e escolar/profissional da Microsoft.  Única página e outras aplicações de JavaScript que são executados principalmente numa cara de browser algumas interessantes pede quando chegar a autenticação:

- As características de segurança destas aplicações serem significativamente diferentes a partir de aplicações de web baseada no servidor tradicional.
- Muitos autorização servidores & fornecedores de identidades não suportam CORS pedidos.
- Browser de página completa redireciona afastando-o a aplicação tornam-se particularmente invasiva para a experiência do utilizador.

Para estas aplicações (tenha em conta: AngularJS, Ember.js, React.js, etc) Azure AD suporta o fluxo de OAuth 2.0 implícito conceder.  O fluxo implícito é descrito na [Especificação de 2.0 OAuth](http://tools.ietf.org/html/rfc6749#section-4.2).  O benefício principal é permite que a aplicação obter tokens a partir do Azure AD sem executar um servidor de back-end credencial exchange.  Esta opção permite-a aplicação para o utilizador de início de sessão, manter a sessão e obter tokens de outros Web APIs tudo dentro de cliente do código JavaScript.  Existem algumas considerações de segurança importantes a ter em conta ao utilizar o fluxo implícito - especificamente em torno de [cliente](http://tools.ietf.org/html/rfc6749#section-10.3) e [representação de utilizador](http://tools.ietf.org/html/rfc6749#section-10.3).

Se pretender utilizar o fluxo implícito e Azure AD para adicionar a autenticação para a sua aplicação JavaScript, recomendamos que utilize o nossa biblioteca de JavaScript abrir origem, [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js).  Existem alguns AngularJS tutoriais disponível [aqui](active-directory-appmodel-v2-overview.md#getting-started) para ajudar a começar a utilizar.  

No entanto, se preferir não utiliza uma biblioteca na sua aplicação única página e enviar mensagens de protocolo a próprio, siga os passos gerais abaixo.

> [AZURE.NOTE]
    Nem todos os cenários do Azure Active Directory e funcionalidades são suportadas pelo ponto final 2.0.  Para determinar se deve utilizar o ponto final 2.0, leia sobre as [limitações 2.0](active-directory-v2-limitations.md).
    
## <a name="protocol-diagram"></a>Diagrama de protocolo
O inteiro implícito sessão fluxo aspeto - cada um dos passos descritos no detalhadamente abaixo.

![OpenId ligar as pistas de diagrama](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## <a name="send-the-sign-in-request"></a>Envie o pedido de início de sessão

Para assinar inicialmente o utilizador na sua aplicação, pode enviar um pedido de autorização [OpenID ligar](active-directory-v2-protocols-oidc.md) e obter um `id_token` partir do ponto final 2.0:

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token+token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&response_mode=fragment
&state=12345
&nonce=678910
```

> [AZURE.TIP] Clique na ligação abaixo para executar este pedido! Após iniciar sessão, o browser deve ser redirecionado para `https://localhost/myapp/` com um `id_token` na barra de endereço.
    <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token+token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/Authorize...</a>

| Parâmetro | | Descrição |
| ----------------------- | ------------------------------- | --------------- |
| inquilino | obrigatório | O `{tenant}` valor no caminho do pedido de pode ser utilizado para controlar quem pode iniciar sessão na aplicação.  Os valores permitidos são `common`, `organizations`, `consumers`e identificadores de inquilinos.  Para obter mais detalhes, consulte o artigo [Noções básicas de protocolo](active-directory-v2-protocols.md#endpoints). |
| client_id | obrigatório | O Id da aplicação que o portal de registo ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) atribuída a sua aplicação. |
| response_type | obrigatório | Tem de incluir `id_token` para ligar OpenID iniciar sessão.  Também poderá incluir a response_type `token`. Utilizar `token` aqui permitirá que a sua aplicação receber um token de acesso imediatamente a partir do ponto final autorizar sem ter de fazer um segundo pedido para o ponto final autorizar.  Se utilizar o `token` response_type, o `scope` parâmetro tem de conter um âmbito que indica qual a emitir o token para o recurso. |
| redirect_uri | recomendado | Redirect_uri da sua aplicação, onde as respostas de autenticação podem ser enviadas e recebidas por sua aplicação.  Exatamente tem de corresponder um dos redirect_uris registado no portal do, exceto tem de ser codificada de url. |
| âmbito | obrigatório | Uma lista de âmbitos espaço separados por vírgulas.  Para ligar a OpenID, deve incluir o âmbito `openid`, que corresponde a permissão de "Sessão neste" na autorização IU função.  Opcionalmente, também poderá querer incluir a `email` ou `profile` [âmbitos](active-directory-v2-scopes.md) para obter acesso a dados de utilizador adicionais.  Também podem incluir outros âmbitos neste pedido para pedir consentimento para vários recursos.  |
| response_mode | recomendado | Especifica o método que deve ser utilizado para enviar a parte detrás token resultante para a sua aplicação.  Deve ser `fragment` para o fluxo implícito.  |
| Estado | recomendado | Um valor incluído no pedido de que também será devolvido na resposta token.  Pode ser uma cadeia de qualquer conteúdo que pretender.  Um valor único gerado aleatoriamente é normalmente utilizado para [prevenir ataques de falsificação de pedido de publicação em vários sites](http://tools.ietf.org/html/rfc6749#section-10.12).  O estado também é utilizado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorreu, por exemplo, a página ou vista que terem sido no. |
| nonce | obrigatório | Um valor incluído no pedido de gerado pela aplicação, que será incluída na id_token resultante como uma afirmação.  A aplicação, em seguida, pode verificar este valor para mitigar ataques de repetição token.  O valor é, normalmente, uma cadeia de aleatório e único que pode ser utilizada para identificar a origem do pedido.  |
| linha de comandos | opcional | Indica o tipo de interação do utilizador que é necessário.  Os valores válidos apenas neste momento são 'login', 'Nenhum,' e 'consentimento'.  `prompt=login`vai forçar ao utilizador que introduza as respetivas credenciais no pedido, a negação sessão único no.  `prompt=none`é o oposto --lo será Certifique-se de que o utilizador não é apresentado com qualquer pedido interativo qualquer.  Se o pedido não pode ser concluído silenciosamente através de sessão único no, o ponto final 2.0 devolverão um erro.  `prompt=consent`irá acionar a caixa de diálogo do token consentimento depois do utilizador inicia sessão, perguntar ao utilizador para conceder permissões para a aplicação. |
| login_hint | opcional | Pode ser utilizado para preencher previamente o campo nome de utilizador/e-mail endereço da página para o utilizador, iniciar sessão se souber o nome de utilizador antecedência.  Muitas vezes aplicações irão utilizar este parâmetro durante re-autenticação, já está a ter extraídas o nome de utilizador a partir de um anterior iniciar sessão utilizando o `preferred_username` reclamar. |
| domain_hint | opcional | Pode ser um dos `consumers` ou `organizations`.  Se incluído, irá ignorar o processo de deteção baseada em correio eletrónico que o utilizador analise no 2.0 página Iniciar sessão, levando a uma experiência de utilizador ligeiramente mais simplificada.  Muitas vezes aplicações irão utilizar este parâmetro durante re-autenticação, por extrair a `tid` reclamar a partir do id_token.  Se o `tid` reclamar valor é `9188040d-6c67-4c5b-b112-36a304b66dad`, deve utilizar `domain_hint=consumers`.  Caso contrário, utilize `domain_hint=organizations`. |

Neste momento, o utilizador será pedido para introduzir as suas credenciais e concluir a autenticação.  O ponto final 2.0 também irá garantir que o utilizador deu às permissões indicadas na `scope` parâmetro de consulta.  Se o utilizador não deu a qualquer uma das permissões, irá peça ao utilizador para o seu consentimento para as permissões necessárias.  Detalhes de [permissões, consentimento e apps inquilinos com várias são fornecidos aqui](active-directory-v2-scopes.md).

Assim que o utilizador autentica e concede consentimento, o ponto final 2.0 irá devolver uma resposta para a sua aplicação a indicada na `redirect_uri`, utilizando o método especificado na `response_mode` parâmetro.

#### <a name="successful-response"></a>Resposta com êxito

Utilizar uma resposta com êxito `response_mode=fragment` e `response_type=id_token+token` é semelhante a seguinte, com quebras de linha para legibilidade das mesmas:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope=https%3a%2f%2fgraph.microsoft.com%2fmail.read 
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| access_token | Se incluídos `response_type` inclui `token`. O token de acesso que a aplicação solicitada, neste caso para o Microsoft Graph.  O token de acesso não deve ser descodificar ou inspeccionar caso contrário, pode ser tratada como uma cadeia opaca. |
| token_type | Se incluídos `response_type` inclui `token`.  Será sempre `Bearer`. |
| expires_in | Se incluídos `response_type` inclui `token`.  Indica o número de segundos que o token é válido, para fins de colocação em cache. |
| âmbito | Se incluídos `response_type` inclui `token`.  Indica scope(s) para o qual o access_token será válido. |
| id_token | Id_token que a aplicação solicitada. Pode utilizar o id_token para verificar a identidade do utilizador e iniciar uma sessão com o utilizador.  Obter mais detalhes sobre id_tokens e os seus conteúdos está incluído na [referência de token de ponto final de 2.0](active-directory-v2-tokens.md).  |
| Estado | Se um parâmetro de estado é incluído no pedido, o mesmo valor deverá aparecer na resposta. A aplicação deve verificar que os valores de estado no pedidos e respostas são idênticos. |


#### <a name="error-response"></a>Resposta de erro
Respostas de erro também podem ser enviadas para o `redirect_uri` , de modo a aplicação pode processam adequadamente:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| erro | Uma cadeia de código de erro que pode ser utilizada para classificar tipos de erros que ocorrem e pode ser utilizada para responder a erros. |
| error_description | Uma mensagem de erro específico pode ajudar a um programador identificar o problema de raiz de um erro de autenticação.  |

## <a name="validate-the-idtoken"></a>Validar o id_token
Não é suficiente para autenticar o utilizador; apenas a receber um id_token tem de validar assinatura a id_token e verificar as afirmações no token de por requisitos da sua aplicação.  O ponto final 2.0 utiliza [JSON Web Tokens (JWTs)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e criptografia de chave pública para iniciar sessão tokens e certifique-se de que são válidos.

Pode optar por validar o `id_token` no cliente de código, mas uma prática comum é enviar a `id_token` para um servidor de back-end e executar a validação aí.  Assim que tiver validado a assinatura do id_token, existem alguns em afirmações que lhe-á ser pedido para confirmar.  Consulte a [referência de token 2.0](active-directory-v2-tokens.md) para obter mais informações, incluindo [Tokens de validar](active-directory-v2-tokens.md#validating-tokens) e [Importantes informações sobre como iniciar sessão chave passagem com o rato](active-directory-v2-tokens.md#validating-tokens).  Recomendamos que façam uso de uma biblioteca para análise e validar tokens - há pelo menos um disponível para a maioria dos idiomas e plataformas.
<!--TODO: Improve the information on this-->

Também pode optar por validar afirmações adicionais, dependendo do seu cenário.  Algumas validações comuns incluem:

- Garantindo que o utilizador/organização tiver inscrito para a aplicação.
- Garantindo que o utilizador não tem autorização/privilégios inicial
- Assegurar uma determinada regular a intensidade de autenticação ocorreu, tal como a autenticação multifator.

Para mais informações sobre as afirmações num id_token, consulte a [referência de token de ponto final de 2.0](active-directory-v2-tokens.md).

Assim que tiver completamente validado a id_token, pode iniciar uma sessão com o utilizador e utilize as afirmações a id_token para obter informações sobre o utilizador na sua aplicação.  Esta informação pode ser utilizada para apresentar, registos, autorizações, etc.

## <a name="get-access-tokens"></a>Obter tokens de acesso

Agora que tenha sessão iniciada o utilizador na sua aplicação única página, pode obter tokens de acesso para a web chamada APIs protegidos por Azure AD, tal como do [Microsoft Graph](https://graph.microsoft.io).  Mesmo que já recebeu um token utilizando o `token` response_type, pode utilizar este método para adquirir tokens para recursos adicionais sem ter de redirecionar ao utilizador para iniciar sessão novamente.

No fluxo de OpenID ligar/OAuth normal, que seria fazer isto ao efetuar um pedido para o 2.0 `/token` ponto final.  No entanto, o ponto final 2.0 não suporta pedidos de CORS, para que efetuar chamadas de AJAX para obter e atualizar tokens estiver fora da pergunta.  Em vez disso, pode utilizar o fluxo implícito um iframe oculto para obter tokens de novos para o outro web APIs: 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment
&state=12345&nonce=678910
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
```

> [AZURE.TIP] Experimente copiar e colar a abaixo pedido num separador do browser! (Não se esqueça substituir o `domain_hint` e o `login_hint` valores com os valores corretos para o utilizador)

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&domain_hint={{consumers-or-organizations}}&login_hint={{your-username}}
```

| Parâmetro | | Descrição |
| ----------------------- | ------------------------------- | --------------- |
| inquilino | obrigatório | O `{tenant}` valor no caminho do pedido de pode ser utilizado para controlar quem pode iniciar sessão na aplicação.  Os valores permitidos são `common`, `organizations`, `consumers`e identificadores de inquilinos.  Para obter mais detalhes, consulte o artigo [Noções básicas de protocolo](active-directory-v2-protocols.md#endpoints). |
| client_id | obrigatório | O Id da aplicação que o portal de registo ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) atribuída a sua aplicação. |
| response_type | obrigatório | Tem de incluir `id_token` para ligar OpenID iniciar sessão.  Também pode incluir outros response_types, tais como `code`. |
| redirect_uri | recomendado | Redirect_uri da sua aplicação, onde as respostas de autenticação podem ser enviadas e recebidas por sua aplicação.  Exatamente tem de corresponder um dos redirect_uris registado no portal do, exceto tem de ser codificada de url. |
| âmbito | obrigatório | Uma lista de âmbitos espaço separados por vírgulas.  Para obter tokens, inclua todos os [âmbitos de](active-directory-v2-scopes.md) exigir para o recurso de interesse.  |
| response_mode | recomendado | Especifica o método que deve ser utilizado para enviar a parte detrás token resultante para a sua aplicação.  Pode ser um dos `query`, `form_post`, ou `fragment`.  |
| Estado | recomendado | Um valor incluído no pedido de que também será devolvido na resposta token.  Pode ser uma cadeia de qualquer conteúdo que pretender.  Um valor único gerado aleatoriamente é normalmente utilizado para prevenir ataques de falsificação de pedido de publicação em vários sites.  O estado também é utilizado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorreu, por exemplo, a página ou vista que terem sido no. |
| nonce | obrigatório | Um valor incluído no pedido de gerado pela aplicação, que será incluída na id_token resultante como uma afirmação.  A aplicação, em seguida, pode verificar este valor para mitigar ataques de repetição token.  O valor é, normalmente, uma cadeia de aleatório e único que pode ser utilizada para identificar a origem do pedido.  |
| linha de comandos | obrigatório | Para atualizar e obter o tokens um iframe oculto, deve utilizar `prompt=none` para se certificar de que a iframe não deixar de responder ao 2.0 página Iniciar sessão e devolve imediatamente. |
| login_hint | obrigatório | Para atualizar e obter o tokens um iframe oculto, tem de incluir o nome de utilizador do utilizador esta sugestão para distinguir entre várias sessões, que o utilizador poderá ter num determinado ponto no tempo. Pode extrair o nome de utilizador de um anterior iniciar sessão utilizando o `preferred_username` reclamar. |
| domain_hint | obrigatório | Pode ser um dos `consumers` ou `organizations`.  Para atualizar e obter o tokens um iframe oculto, tem de incluir o domain_hint no pedido de.  Deverão ser extraídas as `tid` reclamar a partir do id_token de um anterior iniciar sessão para determinar qual o valor a utilizar.  Se o `tid` reclamar valor é `9188040d-6c67-4c5b-b112-36a304b66dad`, deve utilizar `domain_hint=consumers`.  Caso contrário, utilize `domain_hint=organizations`. |

Agradecimentos a `prompt=none` parâmetro, este pedido irá quer teve êxito ou falhar imediatamente e regressar à aplicação.  Uma resposta com êxito será enviada para a sua aplicação a indicada na `redirect_uri`, utilizando o método especificado na `response_mode` parâmetro.

#### <a name="successful-response"></a>Resposta com êxito
Utilizar uma resposta com êxito `response_mode=fragment` é semelhante a:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| access_token | O token de que a aplicação solicitada. |
| token_type | Será sempre `Bearer`. |
| Estado | Se um parâmetro de estado é incluído no pedido, o mesmo valor deverá aparecer na resposta. A aplicação deve verificar que os valores de estado no pedidos e respostas são idênticos. |
| expires_in | Quanto tempo o token de acesso é válido (em segundos). |
| âmbito | Os âmbitos que seja válido para o token de acesso. |

#### <a name="error-response"></a>Resposta de erro
Respostas de erro também podem ser enviadas para o `redirect_uri` , de modo a aplicação pode processam adequadamente.  No caso de `prompt=none`, um erro esperado será:

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| erro | Uma cadeia de código de erro que pode ser utilizada para classificar tipos de erros que ocorrem e pode ser utilizada para responder a erros. |
| error_description | Uma mensagem de erro específico pode ajudar a um programador identificar o problema de raiz de um erro de autenticação.  |

Se receber este erro no pedido de iframe, o utilizador tem forma interativa iniciar sessão novamente obter um novo token.  Pode optar por gerir esta situação no sentido qualquer forma para a sua aplicação.

## <a name="refreshing-tokens"></a>Atualizar tokens

Ambos `id_token`s e `access_token`s expirará após um breve período de tempo, para que a sua aplicação tem de ser preparada para atualizar estes tokens periodicamente.  Para atualizar qualquer tipo de token, pode efetuar o mesmo pedido iframe ocultos pontos acima utilizando o `prompt=none` parâmetro para controlar o comportamento do Azure AD.  Se pretende receber uma nova `id_token`, certifique-se de que utilizar `response_type=id_token` e `scope=openid`, bem como um `nonce` parâmetro.


## <a name="send-a-sign-out-request"></a>Enviar um sinal de pedido de

O OpenIdConnect `end_session_endpoint` atualmente não é suportado pelo ponto final 2.0. Isto significa que a sua aplicação não é possível enviar um pedido para o ponto final 2.0 para terminar uma sessão de utilizador e limpar cookies definidos pelo ponto final 2.0.
Para iniciar a sessão de um utilizador saída, a aplicação pode simplesmente terminar as suas próprias sessão com o utilizador e deixe a sessão do utilizador com o 2.0 ponto final no-tact.  Da próxima vez que o utilizador tenta iniciar sessão, verão uma página "Escolher conta", com as respetivas contas tiver sessão iniciada no ativamente listadas.
Nessa página, o utilizador pode optar por terminar a sessão qualquer outra conta, terminar a sessão com o ponto final 2.0.

<!--

When you wish to sign the user out of the  app, it is not sufficient to clear your app's cookies or otherwise end the session with the user.  You must also redirect the user to the v2.0 endpoint for sign out.  If you fail to do so, the user will be able to re-authenticate to your app without entering their credentials again, because they will have a valid single sign-on session with the v2.0 endpoint.

You can simply redirect the user to the `end_session_endpoint` listed in the OpenID Connect metadata document:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parameter | | Description |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | recommended | The URL which the user should be redirected to after successful logout.  If not included, the user will be shown a generic message by the v2.0 endpoint.  |

-->