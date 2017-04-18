<properties
    pageTitle="Descrição geral do Azure AD .NET Protocol | Microsoft Azure"
    description="Este artigo descreve como utilizar mensagens HTTP para autorizar o acesso a aplicações web e web APIs no seu inquilino através do Azure Active Directory e OAuth 2.0."
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


# <a name="authorize-access-to-web-applications-using-oauth-20-and-azure-active-directory"></a>Autorize acesso às aplicações web que utilizam OAuth 2.0 e o Azure Active Directory

Azure Active Directory (Azure AD) utiliza OAuth 2.0 para lhe permitir autorizar o acesso a aplicações web e web APIs no inquilino do Azure AD. Este guia é independente do idioma e descreve como enviar e receber mensagens HTTP sem utilizar qualquer um dos nossos bibliotecas abrir origem.

O fluxo de código de autorização de OAuth 2.0 é descrito na [secção 4.1 da especificação OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.1) . É utilizado para executar a autenticação e autorização na maioria dos tipos de aplicação, incluindo aplicações web e vierem instalado aplicações.

[AZURE.INCLUDE [active-directory-protocols-getting-started](../../includes/active-directory-protocols-getting-started.md)]


## <a name="oauth-20-authorization-flow"></a>Fluxo de autorização de OAuth 2.0

De alto nível, o fluxo de autorização inteira para uma aplicação de um pouco este aspeto:

![Fluxo de código Auth OAuth](media/active-directory-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)


## <a name="request-an-authorization-code"></a>Pedido de um código de autorização

O fluxo de código autorização começa com o cliente que encaminha ao utilizador para o `/authorize` ponto final. Neste pedido, o cliente indica as permissões que necessita para adquirir do utilizador. Pode obter os pontos finais OAuth 2.0 a partir da página da sua aplicação no Portal clássica Azure, botão **Pontos finais de vista** no nível a parte inferior.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&resource=https%3A%2F%2Fservice.contoso.com%2F
&state=12345
```

| Parâmetro | | Descrição |
| ----------------------- | ------------------------------- | --------------- |
| inquilino | obrigatório | O `{tenant}` valor no caminho do pedido de pode ser utilizado para controlar quem pode iniciar sessão na aplicação.  Os valores permitidos são identificadores de inquilino, por exemplo, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` ou `common` inquilino independente tokens de |
| client_id | obrigatório | O Id da aplicação atribuídos a sua aplicação quando que registado com Azure AD. Pode encontrar esta no Portal de gestão do Azure. Clique no **Active Directory**, clique no directório, clique na aplicação e, em seguida, clique em **Configurar** |
| response_type | obrigatório | Tem de incluir `code` para o fluxo de código de autorização. |
| redirect_uri | recomendado | Redirect_uri da sua aplicação, onde as respostas de autenticação podem ser enviadas e recebidas por sua aplicação.  Exatamente tem de corresponder um dos redirect_uris registado no portal do, exceto tem de ser codificada de url.  Para aplicações nativas e dispositivos móveis, deve utilizar o valor predefinido de `urn:ietf:wg:oauth:2.0:oob`. |
| response_mode | recomendado | Especifica o método que deve ser utilizado para enviar a parte detrás token resultante para a sua aplicação.  Pode ser `query` ou `form_post`.  |
| Estado | recomendado | Um valor incluído no pedido de que também será devolvido na resposta token. Um valor único gerado aleatoriamente é normalmente utilizado para [prevenir ataques de falsificação de pedido de publicação em vários sites](http://tools.ietf.org/html/rfc6749#section-10.12).  O estado também é utilizado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorreu, por exemplo, a página ou vista que terem sido no. |
| recurso | opcional | URI de ID da aplicação, da web API (seguro de recurso). Para localizar o URI de ID da aplicação da web API, no Portal de gestão do Azure, clique em **Active Directory**, clique no directório, clique na aplicação e, em seguida, clique em **Configurar**. |
| linha de comandos | opcional |  Indica o tipo de interação do utilizador que é necessário.<p> Os valores válidos são: <p> *início de sessão*: O utilizador deve ser-lhe pedido para autenticar novamente. <p> *consentimento*: consentimento utilizador tenha sido concedido, mas tem de ser atualizados. O utilizador deve ser pedido para consentimento. <p> *admin_consent*: um administrador deve ser-lhe pedido para o seu consentimento em nome de todos os utilizadores na sua organização |
| login_hint | opcional | Pode ser utilizado para preencher previamente o campo nome de utilizador/e-mail endereço da página para o utilizador, iniciar sessão se souber o nome de utilizador antecedência.  Muitas vezes aplicações irão utilizar este parâmetro durante re-autenticação, já está a ter extraídas o nome de utilizador a partir de um anterior iniciar sessão utilizando o `preferred_username` reclamar. |
| domain_hint | opcional | Fornece uma sugestão sobre o inquilino ou domínio que o utilizador deve utilizar para iniciar sessão. O valor do domain_hint é um domínio registado do inquilino. Se o inquilino for federado num diretório no local, AAD redireciona para o servidor de Federação do inquilino especificado. |

> [AZURE.NOTE] Se o utilizador fizer parte de uma organização, um administrador da organização pode consentimento ou recusar em nome do utilizador ou permitir que o utilizador consentimento. O utilizador é dado a opção de consentimento apenas quando o administrador permite-lo.

Neste momento, o utilizador será pedido para introduzir as suas credenciais e consentimento para as permissões indicadas na `scope` parâmetro de consulta. Assim que o utilizador autentica e concede consentimento, Azure AD envia uma resposta para a sua aplicação na `redirect_uri` endereço no seu pedido.

### <a name="successful-response"></a>Resposta com êxito

Uma resposta com êxito poderia ter este aspeto:

```
GET  HTTP/1.1 302 Found
Location: http://localhost/myapp/?code= AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF&state=D79E5777-702E-4260-9A62-37F75FF22CCE
```

| Parâmetro | Descrição |
| -----------------------| --------------- |
| admin_consent | O valor é True se um administrador manifestado acordo numa linha de comandos do pedido de consentimento.|
| código | O código de autorização que a aplicação solicitou. A aplicação pode utilizar o código de autorização para pedir um token de acesso para o recurso de destino. |
| session_state | Um valor exclusivo que identifica a sessão do utilizador actual. Este valor é um GUID, mas deve ser tratada como um valor opaco que lhe é transmitido sem exame. |
| Estado | Se um parâmetro de estado é incluído no pedido, o mesmo valor deverá aparecer na resposta. É aconselhável para a aplicação verificar se os valores de estado no pedidos e respostas são idênticos antes de utilizar a resposta. Isto ajuda a detetar [ataques de publicação em vários sites pedir falsificação (CSRF)](https://tools.ietf.org/html/rfc6749#section-10.12) contra o cliente.  

### <a name="error-response"></a>Resposta de erro

Respostas de erro também podem ser enviadas para o `redirect_uri` para que a aplicação pode processar corretamente.

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
|-----------|-------------|
| erro | Um valor de código de erro definido na secção 5.2 do [Token 2.0 autorização Framework](http://tools.ietf.org/html/rfc6749). A tabela seguinte descreve os códigos de erro que Azure AD devolve. |
| error_description | Uma descrição mais detalhada do erro. Esta mensagem não se destina a ser relevantes do utilizador final. |
| Estado | O valor de estado é um valor que não sejam reutilizados gerado aleatoriamente que é enviado no pedido de e devolvido na resposta para impedir ataques de falsificação (CSRF) do pedido de publicação em vários sites. |

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

## <a name="use-the-authorization-code-to-request-an-access-token"></a>Utilizar o código de autorização para pedir um token de acesso

Agora que tenha adquirido um código de autorização e foi concedida permissão pelo utilizador, pode resgatar o código para um token de acesso para o recurso pretendido, ao enviar um pedido de mensagem para a `/token` ponto final:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded
grant_type=authorization_code
&client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA
&redirect_uri=https%3A%2F%2Flocalhost%2Fmyapp%2F
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=p@ssw0rd

//NOTE: client_secret only required for web apps
```

| Parâmetro | | Descrição |
| ----------------------- | ------------------------------- | --------------------- |
| inquilino | obrigatório |  O `{tenant}` valor no caminho do pedido de pode ser utilizado para controlar quem pode iniciar sessão na aplicação.  Os valores permitidos são identificadores de inquilino, por exemplo, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` ou `common` inquilino independente tokens de |
| client_id | obrigatório | O Id da aplicação atribuídos a sua aplicação quando que registado com Azure AD. Pode encontrar esta no Portal clássica do Azure. Clique no **Active Directory**, clique no directório, clique na aplicação e, em seguida, clique em **Configurar** |
| grant_type | obrigatório | Tem de ser `authorization_code` para o fluxo de código de autorização. |
| código | obrigatório | O `authorization_code` que o utilizador tiver adquirido na secção anterior   |
| redirect_uri | obrigatório | O mesmo `redirect_uri` valor que foi utilizado para adquirir o `authorization_code`. |
| client_secret | necessário para web apps | O segredo de aplicação que criou no portal do registo de aplicação para a sua aplicação.  Não deve ser utilizada numa aplicação nativa, porque client_secrets não pode ser armazenado sujeito em dispositivos.  É necessário para aplicações web e web API, que têm a capacidade para armazenar a `client_secret` em segurança no lado do servidor. |
| recurso | obrigatório se especificado no pedido de código de autorização, de que outra opcional | URI de ID da aplicação, da web API (seguro de recurso).
Para localizar a aplicação ID URI, no Portal de gestão do Azure, clique em **Active Directory**, clique no directório, clique na aplicação e, em seguida, clique em **Configurar**.

### <a name="successful-response"></a>Resposta com êxito

Azure AD devolve um token de acesso relativamente a uma resposta com êxito. Para minimizar a chamadas de rede da aplicação de cliente e os respetivos latência associada, a aplicação de cliente deve ter em cache tokens de acesso para a duração do token especificado na resposta OAuth 2.0. Para determinar a duração do token, utilize o `expires_in` ou `expires_on` valores de parâmetros.

Se um recurso de API web devolve um `invalid_token` código de erro, isto pode indicar que o recurso tem determinado que o token expiraram. Se as horas de relógio do cliente e de recurso diferente (conhecidas como uma "Hora distorção"), o recurso pode considerar o token de ser expiraram antes do token está desmarcado da cache de cliente. Se tal acontecer, desmarque o token da cache, mesmo se for ainda dentro da sua vida útil calculado.

Uma resposta com êxito poderia ter este aspeto:

```
{
  "access_token": " eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1388444763",
  "resource": "https://service.contoso.com/",
  "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA",
  "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
"id_token": " eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ.”
}

```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| access_token | O token de acesso pedida. A aplicação pode utilizar este token para autenticar para o recurso seguro, tal como uma web API. |
| token_type | Indica o valor de tipo de token. O único tipo que suporte do Azure AD é o tipo de ligação. Para mais informações sobre tokens de tipo de ligação, consulte o artigo [OAuth2.0 autorização Framework: a utilização Token de ligação (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt)  |
| expires_in | Quanto tempo o token de acesso é válido (em segundos). |
| expires_on | O tempo quando expira o token de acesso. A data é representada como o número de segundos a partir do 1970-01-01T0:0:0Z UTC até o tempo de expiração. Este valor é utilizado para determinar a duração de tokens em cache. |
| recurso | URI de ID da aplicação, da web API (seguro de recurso).|
| âmbito | Permissões de representação concedidas à aplicação cliente. A permissão predefinido é `user_impersonation`. O proprietário do recurso seguro pode registar valores adicionais no Azure AD.|
| refresh_token |  Um token de atualização OAuth 2.0. A aplicação pode utilizar este token para adquirir tokens de acesso adicionais depois de expira token de acesso actual.  Atualizar tokens são longa vida e podem ser utilizados para reter o acesso aos recursos por adicional períodos de tempo. |
| id_token | Um não assinados JSON Web Token (JWT). O base64Url podem aplicação descodificar segmentos deste token de pedir informações sobre o utilizador tiver sessão iniciada. A aplicação pode os valores em cache e apresentá-los, mas não deve confiar nos mesmos para qualquer autorização ou limites de segurança. |

### <a name="jwt-token-claims"></a>Afirmações de Token de JWT
O token JWT no valor da `id_token` parâmetro pode ser descodificar para as seguintes afirmações:

```
{
 "typ": "JWT",
 "alg": "none"
}.
{
 "aud": "2d4d11a2-f814-46a7-890a-274a72a7309e",
 "iss": "https://sts.windows.net/7fe81447-da57-4385-becb-6de57f21477e/",
 "iat": 1388440863,
 "nbf": 1388440863,
 "exp": 1388444763,
 "ver": "1.0",
 "tid": "7fe81447-da57-4385-becb-6de57f21477e",
 "oid": "68389ae2-62fa-4b18-91fe-53dd109d74f5",
 "upn": "frank@contoso.com",
 "unique_name": "frank@contoso.com",
 "sub": "JWvYdCWPhhlpS1Zsf7yYUxShUwtUm5yzPmw_-jX3fHY",
 "family_name": "Miller",
 "given_name": "Frank"
}.
```

O `id_token` parâmetro inclui os seguintes tipos de afirmação. Para mais informações sobre tokens de web JSON, consulte o artigo a [especificação de rascunho JWT IETF](http://go.microsoft.com/fwlink/?LinkId=392344). Para mais informações sobre os tipos de token e em afirmações, leia [Token suportados e os tipos de afirmações](active-directory-token-and-claims.md)

| Tipo de afirmação | Descrição |
|------------|-------------|
| sociedade mista | Audiência do token de. Quando o token é emitido para uma aplicação de cliente, a audiência é o `client_id` do cliente.
| EXP | Tempo de expiração. O tempo quando o token de expira. Para o token seja válido, a data/hora atual tem de ser menor ou igual ao `exp` valor. A hora é representada como o número de segundos a partir de 1 de Janeiro de 1970 (1970-01-01T0:0:0Z) UTC até ao momento o token foi emitido. |
| family_name | Utilizador apelido ou apelido. A aplicação pode apresentar este valor. |
| given_name | Nome de utilizador. A aplicação pode apresentar este valor. |
| iat | Emitido momento. O tempo quando o JWT foi emitido. A hora é representada como o número de segundos a partir de 1 de Janeiro de 1970 (1970-01-01T0:0:0Z) UTC até ao momento o token foi emitido. |
| ISS | Identifica o emissor de tokens |
| NBF | Não antes de data/hora. O tempo quando o token torna-se eficaz. Para o token seja válido, a data/hora atual tem de ser maior ou igual ao valor Nbf. A hora é representada como o número de segundos a partir de 1 de Janeiro de 1970 (1970-01-01T0:0:0Z) UTC até ao momento o token foi emitido. |
| OID | Identificador de objeto (ID) do objeto do utilizador no Azure AD. |
| sub | Identificador do token de assunto. Este é um identificador persistente e imutáveis para o utilizador que descreva o token. Utilize este valor no colocação em cache lógica. |
| TID | Identificador (ID) do inquilino Azure AD que emitiu o token do inquilino. |
| unique_name | Um identificador exclusivo para que pode ser apresentado ao utilizador. Este nome costuma nome principal de utilizador (UPN). |
| UPN | Nome principal de utilizador do utilizador. |
| ver | Versão. A versão do token JWT, normalmente 1.0. |

### <a name="error-response"></a>Resposta de erro

Os erros de ponto final de emissão de tokens são códigos de erro HTTP, uma vez que o cliente liga para o ponto final de emissão de tokens diretamente. Para além de código de estado HTTP, o ponto final de emissão de tokens de Azure AD também devolve um documento JSON com objetos que descrevem o erro.

Uma resposta de erro de exemplo poderia ter este aspeto:

```
{
  "error": "invalid_grant",
  "error_description": "AADSTS70002: Error validating credentials. AADSTS70008: The provided authorization code or refresh token is expired. Send a new interactive authorization request for this user and resource.\r\nTrace ID: 3939d04c-d7ba-42bf-9cb7-1e5854cdce9e\r\nCorrelation ID: a8125194-2dc8-4078-90ba-7b6592a7f231\r\nTimestamp: 2016-04-11 18:00:12Z",
  "error_codes": [
    70002,
    70008
  ],
  "timestamp": "2016-04-11 18:00:12Z",
  "trace_id": "3939d04c-d7ba-42bf-9cb7-1e5854cdce9e",
  "correlation_id": "a8125194-2dc8-4078-90ba-7b6592a7f231"
}
```
| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| erro | Uma cadeia de código de erro que pode ser utilizada para classificar tipos de erros que ocorrem e pode ser utilizada para responder a erros. |
| error_description | Uma mensagem de erro específico pode ajudar a um programador identificar o problema de raiz de um erro de autenticação.  |
| error_codes | Uma lista de códigos de erro específicos STS que podem ajudar na diagnósticos. |
| data/hora | A hora em que ocorreu o erro. |
| trace_id | Um identificador exclusivo para o pedido que pode ajudar na diagnósticos.  |
| correlation_id | Um identificador exclusivo para o pedido que pode ajudar na diagnósticos através de componentes.|

#### <a name="http-status-codes"></a>Códigos de estado HTTP

A tabela seguinte lista os códigos de estado HTTP que devolve o ponto final de emissão de tokens. Em alguns casos, o código de erro é suficiente para descrever a resposta, mas em caso de erros, terá de analisar o documento de JSON acompanhamento e examinar respectivo código de erro.

| Código de HTTP | Descrição |
|-----------|-------------|
| 400       | Código de HTTP predefinido. Utilizado na maioria dos casos e é normalmente devido a um pedido de mal. Corrigir e submeter novamente o pedido. |
| 401       | Autenticação falhou. Por exemplo, o pedido não é apresentado o parâmetro client_secret.|
| 403       | Falha de autorização. Por exemplo, o utilizador não tem permissão para aceder ao recurso. |
| 500       | Tem Ocorreu um erro interno no serviço de. Repetir o pedido. |

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

## <a name="use-the-access-token-to-access-the-resource"></a>Utilizar o token de acesso para aceder ao recurso

Agora que já adquirida com êxito um `access_token`, pode utilizar o token de nos pedidos para APIs da Web, incluindo-na `Authorization` cabeçalho. A especificação de [RFC 6750](http://www.rfc-editor.org/rfc/rfc6750.txt) explica como utilizar tokens portadores nos pedidos HTTP para aceder a recursos protegidos.

### <a name="sample-request"></a>Pedido de exemplo

```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

### <a name="error-response"></a>Resposta de erro

Recursos protegidos que implementam códigos de estado do RFC 6750 problema HTTP. Se o pedido não inclui as credenciais de autenticação ou não é apresentado o token, a resposta inclui um `WWW-Authenticate` cabeçalho. Quando um pedido de falha, o servidor de recurso responde com a um código de estado HTTP e um código de erro.

Segue-se um exemplo de uma resposta ter êxito quando o pedido de cliente não inclui o OAuth:

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer authorization_uri="https://login.window.net/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

#### <a name="error-parameters"></a>Parâmetros de erro

| Parâmetro | Descrição |
|-----------|-------------|
| authorization_uri | URI (física ponto final) do servidor autorização. Este valor também é utilizado como uma chave de pesquisa para obter mais informações sobre o servidor a partir de um ponto final de deteção. <p><p> O cliente tem de validar se o servidor de autorização é considerado fidedigno. Quando o recurso está protegido por Azure AD, é suficiente verificar se o URL começa por https://login.windows.net ou outro nome do anfitrião que suporte do Azure AD. Um recurso de específicas do inquilino sempre deve devolver uma autorização específicas do inquilino URI. |
| erro | Um valor de código de erro definido na secção 5.2 do [Token 2.0 autorização Framework](http://tools.ietf.org/html/rfc6749).|
| error_description | Uma descrição mais detalhada do erro. Esta mensagem não se destina a ser relevantes do utilizador final.|
| id_de_recurso | Devolve o identificador exclusivo do recurso. A aplicação de cliente pode utilizar este identificador como o valor da `resource` parâmetro quando solicitar um token para o recurso. <p><p> É muito importante para a aplicação de cliente verificar este valor, caso contrário, um serviço malicioso poderá conseguir induzir ataque **elevação de privilégios** <p><p> A estratégia recomendada para prevenir um ataque é confirmar que o `resource_id` corresponde ao nome da base do web API URL a ser acedido. Por exemplo, se está a ser acedido https://service.contoso.com/data, o `resource_id` pode ser htttps://service.contoso.com/. A aplicação de cliente tem rejeitar uma `resource_id` que não começa com o URL de base, a menos que existe uma forma fiável alternativa para verificar o id. |

#### <a name="bearer-scheme-error-codes"></a>Códigos de erro de esquema de cores do tipo de ligação

A especificação de RFC 6750 define os seguintes erros para recursos que utilizar com o cabeçalho autenticar WWW e o esquema de tipo de ligação na resposta.

| Código de estado HTTP | Código de erro | Descrição | Acção de cliente |
|------------------|------------|-------------|---------------|
| 400 | invalid_request | O pedido não está correcto. Por exemplo,-poderá ser falta um parâmetro ou utilizar o mesmo parâmetro duas vezes. | Corrigir o erro e tente novamente o pedido. Este tipo de erro deve ocorrer apenas durante o desenvolvimento e ser detetado na testes iniciais. |
| 401 | invalid_token   | O token de acesso está em falta, inválido, ou foi revogado. O valor do parâmetro error_description fornece detalhes adicionais. |  Pedir um novo token do servidor de autorização. Se o novo token falhar, Ocorreu um erro inesperado. Envie uma mensagem de erro para o utilizador e volte a tentar após atrasos aleatórios. |
| 403 | insufficient_scope | O token de acesso não contém as permissões de representação necessárias para aceder ao recurso. | Envie um novo pedido de autorização para o ponto final de autorização. Se a resposta contém o parâmetro do âmbito, utilize o valor de âmbito no pedido de para o recurso. |
| 403 | insufficient_access | O assunto do token não possui as permissões que são necessários para aceder ao recurso. | Pede ao utilizador para utilizar uma conta diferente ou para solicitar permissões para o recurso especificado. |

## <a name="refreshing-the-access-tokens"></a>Atualizar os tokens de acesso

Tokens de acesso estão curto e tem de ser atualizados depois expiram para continuar a aceder aos recursos. Que pode atualizar o `access_token` , apresentando outro `POST` pedir para o `/token` ponto final, mas este tempo fornecer a `refresh_token` em vez do `code`.

Atualizar tokens não têm durações especificadas. Normalmente, as durações de tokens de atualização de são compridas. No entanto, em alguns casos, atualizar tokens expirarem, são revogados ou possuem privilégios suficientes para a ação pretendida. Tem a aplicação de esperar e processar erros devolvidos pelo ponto final de emissão de tokens corretamente.

Quando recebe uma resposta com um erro de token de atualização, eliminar o token de atualização atual e pedir um novo código de autorização ou token de acesso. Em particular, quando utilizar uma atualização token no fluxo de autorização código conceder, se receber uma resposta com o `interaction_required` ou `invalid_grant` códigos de erro, rejeitar o token de atualização e pedido de código de autorização de novo.

Pedir uma amostra para o ponto final **específicas do inquilino** (também pode utilizar o ponto final **comuns** ) para obter um novo acesso token utilizando um token de atualização tem o seguinte aspeto:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```
| Parâmetro | Descrição |
|-----------|-------------|
| access_token | Novo token de acesso que foi pedido.|
| expires_in   | A duração restante do token em segundos. Um valor normal é 3600 (uma hora). |
| expires_on   | A data e hora em que o token expira. A data é representada como o número de segundos a partir do 1970-01-01T0:0:0Z UTC até o tempo de expiração. |
| refresh_token | Refresh_token OAuth 2.0 nova que podem ser utilizadas para pedir novo tokens de acesso, quando um nesta resposta expira. |
| recurso     | Identifica o recurso protegido que o token de acesso pode ser utilizados para o access. |
| âmbito        | Permissões de representação concedidas à aplicação cliente nativa. A permissão predefinido é **user_impersonation**. O proprietário do recurso destino pode registar valores alternativos no Azure AD. |
| token_type   | O tipo de token. O valor de suportados apenas é o **tipo de ligação**. |

### <a name="successful-response"></a>Resposta com êxito

Uma resposta token com êxito terá o seguinte aspeto:

```
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1460404526",
  "resource": "https://service.contoso.com/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "refresh_token": "AwABAAAAv YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl PM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfmVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA"
}
```

### <a name="error-response"></a>Resposta de erro

Uma resposta de erro de exemplo poderia ter este aspeto:

```
{
  "error": "invalid_resource",
  "error_description": "AADSTS50001: The application named https://foo.microsoft.com/mail.read was not found in the tenant named 295e01fc-0c56-4ac3-ac57-5d0ed568f872.  This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant.  You might have sent your authentication request to the wrong tenant.\r\nTrace ID: ef1f89f6-a14f-49de-9868-61bd4072f0a9\r\nCorrelation ID: b6908274-2c58-4e91-aea9-1f6b9c99347c\r\nTimestamp: 2016-04-11 18:59:01Z",
  "error_codes": [
    50001
  ],
  "timestamp": "2016-04-11 18:59:01Z",
  "trace_id": "ef1f89f6-a14f-49de-9868-61bd4072f0a9",
  "correlation_id": "b6908274-2c58-4e91-aea9-1f6b9c99347c"
}
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| erro | Uma cadeia de código de erro que pode ser utilizada para classificar tipos de erros que ocorrem e pode ser utilizada para responder a erros. |
| error_description | Uma mensagem de erro específico pode ajudar a um programador identificar o problema de raiz de um erro de autenticação.  |
| error_codes | Uma lista de códigos de erro específicos STS que podem ajudar na diagnósticos. |
| data/hora | A hora em que ocorreu o erro. |
| trace_id | Um identificador exclusivo para o pedido que pode ajudar na diagnósticos.  |
| correlation_id | Um identificador exclusivo para o pedido que pode ajudar na diagnósticos através de componentes.|

Para obter uma descrição dos códigos de erro e a ação de cliente recomendado, consulte o artigo [códigos de erro de erros de ponto final token](#error-codes-for-token-endpoint-errors).
