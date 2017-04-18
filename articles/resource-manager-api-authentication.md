<properties 
   pageTitle="Autenticação do Active Directory e o Gestor de recursos | Microsoft Azure"
   description="Guia de um programador para a autenticação com a API do Gestor de recursos do Azure e do Active Directory para integrar uma aplicação com outras subscrições Azure."
   services="azure-resource-manager,active-directory"
   documentationCenter="na"
   authors="dushyantgill"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/31/2016"
   ms.author="dugill;tomfitz" />

# <a name="how-to-use-azure-active-directory-and-resource-manager-to-manage-a-customers-resources"></a>Como utilizar o Azure Active Directory e o Gestor de recursos para gerir os recursos de um cliente

## <a name="introduction"></a>Introdução

Se for um programador de software que necessita para criar uma aplicação que faz a gestão dos recursos Azure do cliente, este tópico mostra-lhe como autenticar com a API do Gestor de recursos do Azure e obter acesso aos recursos no outras subscrições. 

Pode aceder à sua aplicação APIs do Gestor de recursos de duas formas:

1. **Utilizador + app aceda**: para as aplicações que acedem a recursos em nome de um utilizador com sessão iniciada. Esta abordagem funciona para aplicações, tal como web apps e as ferramentas de linha de comandos, que lidar com apenas "interativo gestão" dos Azure recursos.
1. **Acesso só de aplicação**: para as aplicações que execute serviços daemon e as tarefas agendadas. Identidade a aplicação é concedida acesso direto aos recursos. Esta abordagem funciona para as aplicações que necessitam de longo prazo "acesso offline" Azure.

Este tópico fornece instruções passo a passo para criar uma aplicação que utiliza estes dois métodos autorização. Mostra como efetuar cada passo com REST API ou c#. A aplicação completa do ASP.NET MVC está disponível em [https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense](https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense).

Todos os códigos para este tópico está em execução como uma aplicação web que pode experimentar em [http://vipswapper.azurewebsites.net/cloudsense](http://vipswapper.azurewebsites.net/cloudsense). 

## <a name="what-the-web-app-does"></a>O que faz a aplicação web

A aplicação web:

1. Sinais-in um utilizador do Azure.
2. Pede utilizador para conceder acesso de aplicação web para o Gestor de recursos.
3. Obtém o utilizador + token de acesso de aplicação para aceder ao Gestor de recursos.
4. Utiliza token (a partir do passo 3) para ligar para o Gestor de recursos e atribuir principal do serviço a aplicação a uma função na de subscrição, que lhe dá o acesso a longo prazo de aplicação para a subscrição.
5. Obtém acesso só de aplicação token.
6. Utiliza token (a partir do passo 5) para gerir os recursos na subscrição através do Gestor de recursos.

Eis o fluxo de ponto a ponto da aplicação web.

![Fluxo de autenticação de Gestor de recursos](./media/resource-manager-api-authentication/Auth-Swim-Lane.png)

Como um utilizador, forneça o id da subscrição para a subscrição que pretende utilizar:

![fornecer id da subscrição](./media/resource-manager-api-authentication/sample-ux-1.png)

Selecione a conta a utilizar para o início de sessão.

![Selecione a conta](./media/resource-manager-api-authentication/sample-ux-2.png)

Fornece as suas credenciais.

![fornecer credenciais](./media/resource-manager-api-authentication/sample-ux-3.png)

Conceda acesso a aplicação para as suas subscrições Azure:
 
![Conceder acesso](./media/resource-manager-api-authentication/sample-ux-4.png)
 
Gerir as suas subscrições ligadas:

![Ligar a subscrição](./media/resource-manager-api-authentication/sample-ux-7.png)


## <a name="register-application"></a>Registe-se a aplicação

Antes de começar a codificação, registe-se a aplicação web com o Azure Active Directory (AD). O registo de aplicação cria uma identidade central para a sua aplicação no Azure AD. Contém informação básica sobre a sua aplicação como o ID de cliente do token, URLs de resposta e credenciais que a aplicação utiliza para autenticar e aceder a APIs de Gestor de recursos do Azure. O registo de aplicação também regista as permissões de delegado vários que a aplicação necessita ao aceder ao Microsoft APIs em nome de utilizador. 

Uma vez que a aplicação acede outra subscrição, tem de configurá-la como uma aplicação do inquilino com várias. Para passar na validação, forneça um domínio associado com o Active Directory. Para ver os domínios associados com o Active Directory, inicie a sessão [portal clássica](https://manage.windowsazure.com). Selecione o Active Directory e, em seguida, selecione **domínios**.

O exemplo seguinte mostra como registar a aplicação ao utilizar o Azure PowerShell. Tem de ter a versão mais recente (Agosto de 2016) do Azure PowerShell para o comando funcionar. 

    $app = New-AzureRmADApplication -DisplayName "{app name}" -HomePage "https://{your domain}/{app name}" -IdentifierUris "https://{your domain}/{app name}" -Password "{your password}" -AvailableToOtherTenants $true
    
Para iniciar sessão como a aplicação de AD, é necessário o id da aplicação e a palavra-passe. Para ver o id da aplicação que é devolvido a partir do comando anterior, utilize:

    $app.ApplicationId

O exemplo seguinte mostra como registar a aplicação ao utilizar o clip do Azure. 

    azure ad app create --name {app name} --home-page https://{your domain}/{app name} --identifier-uris https://{your domain}/{app name} --password {your password} --available true

Os resultados incluem o AppId que precisam de ser quando autentica como a aplicação.

### <a name="optional-configuration---certificate-credential"></a>Configuração opcional - credencial de certificado

Azure AD também suporta as credenciais de certificado para aplicações: criar um certificado autoassinado, manter a chave privada e adicionar a chave pública para o registo de aplicação do Azure AD. Para autenticação, a aplicação envia uma carga pequenas útil para Azure AD assinado utilizando a sua chave privada e Azure AD valida a assinatura utilizando a chave pública que registou.

Para obter informações sobre como criar uma aplicação do AD com um certificado, consulte o artigo [Utilizar o PowerShell Azure para criar um serviço principal para aceder a recursos](resource-group-authenticate-service-principal.md#create-service-principal-with-certificate) ou [Utilização Azure clip para criar um serviço principal para aceder a recursos](resource-group-authenticate-service-principal-cli.md#create-service-principal-with-certificate).

## <a name="get-tenant-id-from-subscription-id"></a>Obter id do inquilino de id da subscrição

Para pedir um token que pode ser utilizado para ligar o Gestor de recursos, a aplicação precisa de saber o ID do inquilino do inquilino Azure AD que aloja a subscrição Azure. Provavelmente, os utilizadores saber os ids de subscrição, mas poderá não sabem seu inquilino ids do Active Directory. Para obter o id de utilizador inquilino, peça ao utilizador para o id da subscrição. Forneça esse id da subscrição ao enviar um pedido de sobre a subscrição:

    https://management.azure.com/subscriptions/{subscription-id}?api-version=2015-01-01

O pedido irá falhar porque o utilizador não tiver iniciado sessão ainda, mas pode obter o id do inquilino da resposta. Nessa excepção obter o id do inquilino do valor de cabeçalho de resposta para **Autenticar WWW**. Consulte esta implementação no método [GetDirectoryForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L20) .

## <a name="get-user--app-access-token"></a>Obter o utilizador + token de acesso de aplicação

A aplicação redireciona o utilizador para Azure AD com um OAuth 2.0 autorizar pedir - para autenticar as credenciais do utilizador e voltar um código de autorização. A aplicação utiliza o código de autorização para obter um acesso token para Gestor de recursos. O método de [ConnectSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/Controllers/HomeController.cs#L42) cria o pedido de autorização.

Este tópico mostra os pedidos de REST API para autenticar o utilizador. Também pode utilizar bibliotecas helper para efectuar a autenticação no seu código. Para mais informações sobre estas bibliotecas, consulte o artigo [Azure Active Directory autenticação bibliotecas](./active-directory/active-directory-authentication-libraries.md). Para obter orientações sobre a integração de gestão de identidades numa aplicação, consulte o [Guia do programador do Azure Active Directory](./active-directory/active-directory-developers-guide.md).

### <a name="auth-request-oauth-20"></a>Pedido de AUTH (OAuth 2.0)

Emita um Open ID ligar/OAuth2.0 autorizar Request para o ponto final do Azure AD autorizar:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize

Os parâmetros de cadeia de consulta que estão disponíveis para este pedido sejam descritas no tópico de [pedido de um código de autorização](./active-directory/active-directory-protocols-oauth-code.md#request-an-authorization-code) .

O exemplo seguinte mostra como solicitar autorização OAuth2.0:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=query&response_type=code&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&domain_hint=live.com

Azure AD autentica o utilizador e, se necessário, pede ao utilizador para conceder permissão para a aplicação. Devolve o código de autorização para o URL de resposta da sua aplicação. Cálculo consoante a forma a response_mode pedida, Azure AD quer envia os dados numa cadeia de consulta ou como publicar dados.

    code=AAABAAAAiL****FDMZBUwZ8eCAA&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="auth-request-open-id-connect"></a>Pedido de AUTH (ligar abrir ID)

Se pretender não só aceder ao Gestor de recursos do Azure em nome de utilizador, mas também permite ao utilizador iniciar sessão na sua aplicação utilizando a respetiva conta Azure AD, emita um abrir ID ligar autorizar pedido. Com abrir ID ligar, a aplicação também recebe um id_token a partir do Azure AD que pode utilizar a aplicação para o utilizador de início de sessão.

Os parâmetros de cadeia de consulta que estão disponíveis para este pedido sejam descritas no tópico de [enviar o pedido de início de sessão no](./active-directory/active-directory-protocols-openid-connect-code.md#send-the-sign-in-request) .

Um pedido de abrir ID ligar de exemplo é:

     https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=form_post&response_type=code+id_token&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&scope=openid+profile&nonce=63567Dc4MDAw&domain_hint=live.com&state=M_12tMyKaM8

Azure AD autentica o utilizador e, se necessário, pede ao utilizador para conceder permissão para a aplicação. Devolve o código de autorização para o URL de resposta da sua aplicação. Cálculo consoante a forma a response_mode pedida, Azure AD quer envia os dados numa cadeia de consulta ou como publicar dados.

Um exemplo de resposta abrir ID ligar é:

    code=AAABAAAAiL*****I4rDWd7zXsH6WUjlkIEQxIAA&id_token=eyJ0eXAiOiJKV1Q*****T3GrzzSFxg&state=M_12tMyKaM8&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="token-request-oauth20-code-grant-flow"></a>Pedido de token (OAuth2.0 código conceder fluxo)

Agora que a sua aplicação tiver recebido o código de autorização a partir do Azure AD, é hora para aceder a token para o Gestor de recursos do Azure.  Registe um OAuth2.0 código conceder Token pedir para o ponto final do Azure AD Token: 

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

Os parâmetros de cadeia de consulta que estão disponíveis para este pedido sejam descritas no tópico de [utilizar o código de autorização](./active-directory/active-directory-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token) .

O exemplo seguinte mostra um pedido para token de conceder código com as credenciais de palavra-passe:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Quando trabalha com as credenciais do certificado, crie uma JSON Web Token (JWT) e o início de sessão (RSA SHA256) utilizando a chave privada de credenciais de certificado da sua aplicação. Os tipos de afirmação para o token são apresentados na [afirmações de token de JWT](./active-directory/active-directory-protocols-oauth-code.md#jwt-token-claims). Para sua referência, consulte o artigo o [código de biblioteca de Auth do Active Directory (.NET)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/dev/src/ADAL.PCL.Desktop/CryptographyHelper.cs) para iniciar sessão tokens JWT de declaração do cliente.

Consulte o artigo a [especificação de abrir ID ligar](http://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication) para obter detalhes sobre a autenticação de cliente. 

O exemplo seguinte mostra um pedido para token de conceder código com as credenciais de certificado:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1
    
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012
    
    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbG*****Y9cYo8nEjMyA

Uma resposta de exemplo para o código conceder token de: 

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039858","not_before":"1432035958","resource":"https://management.core.windows.net/","access_token":"eyJ0eXAiOiJKV1Q****M7Cw6JWtfY2lGc5A","refresh_token":"AAABAAAAiL9Kn2Z****55j-sjnyYgAA","scope":"user_impersonation","id_token":"eyJ0eXAiOiJKV*****-drP1J3P-HnHi9Rr46kGZnukEBH4dsg"}

#### <a name="handle-code-grant-token-response"></a>Processar resposta token de conceder de código

Uma resposta token com êxito contém (utilizador + aplicação) token de acesso para o Gestor de recursos do Azure. A aplicação utiliza este token de acesso para aceder ao Gestor de recursos em nome de utilizador. A duração de tokens de acesso emitido por Azure AD é uma hora. Não é provável que a sua aplicação web necessita renovar (utilizador + aplicação) token de acesso. Se necessitar de renovar o token de acesso, utilize o token de atualizar a aplicação recebe na resposta token. Registe um OAuth2.0 Token pedir para o ponto final do Azure AD Token: 

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

Os parâmetros para utilizar com o pedido de atualização são descritos nas [atualização o token de acesso](./active-directory/active-directory-protocols-oauth-code.md#refreshing-the-access-tokens).

O exemplo seguinte mostra como utilizar a atualização token:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=refresh_token&refresh_token=AAABAAAAiL9Kn2Z****55j-sjnyYgAA&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Apesar de tokens de atualização podem ser utilizados para obter novo tokens de acesso para o Gestor de recursos do Azure, não são adequados para o acesso offline pela aplicação. O tempo de vida de tokens de atualização é limitado e tokens de atualização estão vinculados ao utilizador. Se o utilizador sai da organização, a aplicação utilizando o token de atualização perde acesso. Não esta abordagem é adequada para as aplicações que são utilizadas pelo equipas para gerir os seus recursos Azure.

## <a name="check-if-user-can-assign-access-to-subscription"></a>Verificar se o utilizador pode atribuir acesso à subscrição

A aplicação agora tem um token para aceder ao Gestor de recursos do Azure em nome de utilizador. O passo seguinte é ligar a sua aplicação para a subscrição. Depois de ligar, a aplicação pode gerir esses subscrições mesmo quando o utilizador não é apresentar (a longo prazo acesso offline). 

Para cada subscrição ligar, ligue as [permissões de lista do Gestor de recursos](https://msdn.microsoft.com/library/azure/dn906889.aspx) API para determinar se o utilizador tem direitos de gestão de acesso para a subscrição.

O método de [UserCanManagerAccessForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L44) da aplicação de exemplo do ASP.NET MVC implementa esta chamada.

O exemplo seguinte mostra como pedir permissões de um utilizador numa subscrição. 83cfe939-2402-4581-b761-4f59b0a041e4 é o id da subscrição.

    GET https://management.azure.com/subscriptions/83cfe939-2402-4581-b761-4f59b0a041e4/providers/microsoft.authorization/permissions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

Um exemplo de resposta para obter as permissões de utilizador no subscrição é:

    HTTP/1.1 200 OK

    {"value":[{"actions":["*"],"notActions":["Microsoft.Authorization/*/Write","Microsoft.Authorization/*/Delete"]},{"actions":["*/read"],"notActions":[]}]}

As permissões API devolve múltiplas permissões. Cada permissão consiste em acções permitidas (acções) e não são permitidas ações (notactions). Se for uma ação presentes na lista de acções permitidas de qualquer permissão e não se encontra presente na lista notactions dessa permissão, o utilizador tem permissão para realizar essa ação. **Microsoft.Authorization/RoleAssignments/Write** é a ação que concede que acesso de gestão de direitos. A aplicação tem de analisar o resultado de permissões para procurar uma correspondência regex nesta cadeia de ação em ações e notactions de cada permissão.

## <a name="get-app-only-access-token"></a>Obter um acesso só de aplicação token

Agora, já sabe se o utilizador pode atribuir acesso para a subscrição Azure. Os passos são:

1. Atribua a função RBAC adequada a identidade da sua aplicação da subscrição.
2. Valide a atribuição de acesso consultando relativamente a permissão a aplicação da subscrição ou acedendo ao Gestor de recursos através de token de aplicação.
1. Grave a ligação na estrutura dos dados aplicações "ligada subscrições" - persistência o id da subscrição.

Vamos analisar mais detalhadamente no primeiro passo. Para atribuir a função RBAC adequada para a identidade da aplicação, tem de determinar:

- O id de objeto de identidade da sua aplicação no Azure Active Directory do utilizador
- O identificador da função RBAC que requer a aplicação da subscrição

Quando a aplicação autentica um utilizador a partir de um Azure AD, cria um objecto principal de serviço para a sua aplicação nesse Azure AD. Azure permite funções RBAC a serem atribuídas à principais de serviço para conceder acesso direto aos aplicações correspondentes no Azure recursos. Esta ação é exatamente o que gostaríamos de fazer. Consulta a API do Azure AD Graph para determinar o identificador do capital de serviço da sua aplicação no utilizador tiver sessão iniciada no estiverem Azure AD.

Só tem um token de acesso para o Gestor de recursos do Azure - precisa de um novo token de acesso para chamar a API do Azure AD Graph. Todas as aplicações no Azure AD tem permissão para consultar os suas próprias objecto principal de serviço, pelo que é suficiente um token de acesso só de aplicação.

<a id="app-azure-ad-graph">
### <a name="get-app-only-access-token-for-azure-ad-graph-api"></a>Obter acesso só de aplicação token para API do Azure AD Graph

Para autenticar a sua aplicação e obter um token para API do Azure AD Graph, emita um pedido de token de fluxo de cliente credencial conceder OAuth2.0 ao ponto final token Azure AD (**https://login.microsoftonline.com/ {directory_domain_name} / oauth2 ainda/Token**).

O método de [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs) da aplicação de exemplo ASP.net MVC obtém um acesso só de aplicação token grafo API utilizando a biblioteca de autenticação do Active Directory para .NET.

Os parâmetros de cadeia de consulta que estão disponíveis para este pedido sejam descritas no tópico de [pedido de um Token de acesso](./active-directory/active-directory-protocols-oauth-service-to-service.md#request-an-access-token) .

Um pedido de exemplo para token de conceder de credenciais de cliente: 

    POST https://login.microsoftonline.com/62e173e9-301e-423e-bcd4-29121ec1aa24/oauth2/token HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 187</pre>
    <pre>grant_type=client_credentials&client_id=a0448380-c346-4f9f-b897-c18733de9394&resource=https%3A%2F%2Fgraph.windows.net%2F &client_secret=olna8C*****Og%3D

Uma resposta de exemplo para token de conceder de credenciais de cliente: 

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039862","not_before":"1432035962","resource":"https://graph.windows.net/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRv****G5gUTV-kKorR-pg"}

### <a name="get-objectid-of-application-service-principal-in-user-azure-ad"></a>Obter o ID de objecto de capital de serviço de aplicação no utilizador Azure AD

Agora, utilize o token de acesso só de aplicação para consultar os [principais de serviço do Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) API para determinar o Id de objeto do serviço a aplicação principal no diretório.

O método de [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#) da aplicação de exemplo ASP.net MVC implementa esta chamada.

O exemplo seguinte mostra como pedir principal de serviço de uma aplicação. a0448380-c346-4f9f-b897-c18733de9394 é o id de cliente da aplicação.

    GET https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/servicePrincipals?api-version=1.5&$filter=appId%20eq%20'a0448380-c346-4f9f-b897-c18733de9394' HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJK*****-kKorR-pg

O exemplo seguinte mostra uma resposta ao pedido de serviço de uma aplicação do principal 

    HTTP/1.1 200 OK

    {"odata.metadata":"https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/$metadata#directoryObjects/Microsoft.DirectoryServices.ServicePrincipal","value":[{"odata.type":"Microsoft.DirectoryServices.ServicePrincipal","objectType":"ServicePrincipal","objectId":"9b5018d4-6951-42ed-8a92-f11ec283ccec","deletionTimestamp":null,"accountEnabled":true,"appDisplayName":"CloudSense","appId":"a0448380-c346-4f9f-b897-c18733de9394","appOwnerTenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24","appRoleAssignmentRequired":false,"appRoles":[],"displayName":"CloudSense","errorUrl":null,"homepage":"http://www.vipswapper.com/cloudsense","keyCredentials":[],"logoutUrl":null,"oauth2Permissions":[{"adminConsentDescription":"Allow the application to access CloudSense on behalf of the signed-in user.","adminConsentDisplayName":"Access CloudSense","id":"b7b7338e-683a-4796-b95e-60c10380de1c","isEnabled":true,"type":"User","userConsentDescription":"Allow the application to access CloudSense on your behalf.","userConsentDisplayName":"Access CloudSense","value":"user_impersonation"}],"passwordCredentials":[],"preferredTokenSigningKeyThumbprint":null,"publisherName":"vipswapper"quot;,"replyUrls":["http://www.vipswapper.com/cloudsense","http://www.vipswapper.com","http://vipswapper.com","http://vipswapper.azurewebsites.net","http://localhost:62080"],"samlMetadataUrl":null,"servicePrincipalNames":["http://www.vipswapper.com/cloudsense","a0448380-c346-4f9f-b897-c18733de9394"],"tags":["WindowsAzureActiveDirectoryIntegratedApp"]}]}

### <a name="get-azure-rbac-role-identifier"></a>Obter o identificador de função Azure RBAC

Para atribuir uma função RBAC adequada ao seu serviço principal, tem de determinar o identificador da função Azure RBAC.

A função RBAC direita para a sua aplicação:

- Se a sua aplicação monitores apenas da subscrição, sem efetuar alterações, requer permissões de leitor da subscrição. Atribua uma função de **leitor** .
- Se gere a sua aplicação Azure da subscrição, criar/modificar/eliminar entidades, requer uma das permissões contribuinte.
  - Para gerir um determinado tipo de recurso, atribuir as funções de contribuinte específicas do recurso (Máquina Virtual contribuinte, Virtual contribuinte de rede, contribuinte de conta de armazenamento, etc.)
  - Para gerir qualquer tipo de recurso, atribua a função **Contribuinte** .

A atribuição de funções para a sua aplicação está visível para os utilizadores, por isso, seleccione o obrigatório menos privilégio.

Ligar a [definição de função API do Gestor de recursos](https://msdn.microsoft.com/library/azure/dn906879.aspx) para listar todas as funções de Azure RBAC e pesquisa, em seguida, iteramos sobre o resultado para localizar a definição de função pretendido pelo nome.

O método de [GetRoleId](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L246) da aplicação de exemplo do ASP.net MVC implementa esta chamada.

O exemplo pedido seguinte mostra como obter o identificador de função Azure RBAC. 09cbd307-aa71-4aca-b346-5f253e6e3ebb é o id da subscrição.

    GET https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV*****fY2lGc5

A resposta é no seguinte formato: 

    HTTP/1.1 200 OK

    {"value":[{"properties":{"roleName":"API Management Service Contributor","type":"BuiltInRole","description":"Lets you manage API Management services, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.ApiManagement/Services/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c","type":"Microsoft.Authorization/roleDefinitions","name":"312a565d-c81f-4fd8-895a-4e21e48d571c"},{"properties":{"roleName":"Application Insights Component Contributor","type":"BuiltInRole","description":"Lets you manage Application Insights components, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.Insights/components/*","Microsoft.Insights/webtests/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e","type":"Microsoft.Authorization/roleDefinitions","name":"ae349356-3a1b-4a5e-921d-050484c6347e"}]}

Não necessita de chamar esta API contínua. Uma vez que tenha determinado o famoso GUID da definição de função, pode construir o id de definição de função como:

    /subscriptions/{subscription_id}/providers/Microsoft.Authorization/roleDefinitions/{well-known-role-guid}

Seguem-se a famoso GUID das funções incorporadas utilizadas mais frequentemente:

| Função | GUID |
| ----- | ------ |
| Leitor | acdd72a7-3385-48ef-bd42-f606fba81ae7
| Contribuinte | b24988ac-6180-42a0-ab88-20f7382dd24c
| Contribuinte máquina virtual | d73bb868-a0df-4d4d-bd69-98a00b01fccb
| Rede virtual contribuinte | b34d265f-36f7-4a0d-a4d4-e158ca92e90f
| Contribuinte de conta de armazenamento | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25
| Web site contribuinte | de139f84-1756-47ae-9be6-808fbbe84772
| Web plano de contribuinte | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b
| SQL Server contribuinte | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437
| SQL DB contribuinte | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec

### <a name="assign-rbac-role-to-application"></a>Atribuir funções RBAC a aplicação

Tem tudo o que precisa para atribuir uma função RBAC adequada ao seu serviço principal ao utilizar o [Gestor de recursos criar atribuição de funções](https://msdn.microsoft.com/library/azure/dn906887.aspx) API.

O método de [GrantRoleToServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L170) da aplicação de exemplo do ASP.net MVC implementa esta chamada.

Um pedido de exemplo para atribuir RBAC função a aplicação: 

    PUT https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/microsoft.authorization/roleassignments/4f87261d-2816-465d-8311-70a27558df4c?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiL*****FlwO1mM7Cw6JWtfY2lGc5
    Content-Type: application/json
    Content-Length: 230

    {"properties": {"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2"}}

No pedido de, são utilizados os seguintes valores:

| GUID | Descrição |
| ------ | --------- |
| 09cbd307-aa71-4aca-b346-5f253e6e3ebb | o id da subscrição
| c3097b31-7309-4c59-b4e3-770f8406bad2 | o id do objeto do capital de serviço da aplicação
| acdd72a7-3385-48ef-bd42-f606fba81ae7 | o id da função leitor
| 4f87261d-2816-465D-8311-70a27558df4c | um novo guid criado para a nova atribuição de funções

A resposta é no seguinte formato: 

    HTTP/1.1 201 Created

    {"properties":{"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2","scope":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb"},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleAssignments/4f87261d-2816-465d-8311-70a27558df4c","type":"Microsoft.Authorization/roleAssignments","name":"4f87261d-2816-465d-8311-70a27558df4c"}

### <a name="get-app-only-access-token-for-azure-resource-manager"></a>Obter acesso só de aplicação token para o Gestor de recursos do Azure

Para validar nessa aplicação tem desejadas aceder da subscrição, efectuar uma tarefa de teste da subscrição através de um token só de aplicação.

Para obter um acesso só de aplicação token, siga as instruções da secção [obter acesso só de aplicação token para API do Azure AD Graph](#app-azure-ad-graph), com um valor diferente para o parâmetro de recurso: 

    https://management.core.windows.net/

O método de [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) da aplicação de exemplo ASP.NET MVC obtém um acesso só de aplicação token para o Gestor de recursos do Azure utilizando a biblioteca de autenticação do Active Directory para .net.

#### <a name="get-applications-permissions-on-subscription"></a>Obter permissões da aplicação na subscrição

Para verificar que a sua aplicação tem o acesso pretendido numa subscrição do Azure, também poderá ligar as [Permissões de Gestor de recursos](https://msdn.microsoft.com/library/azure/dn906889.aspx) API. Esta abordagem é semelhante ao modo como determinados a se o utilizador tem direitos de gestão de acesso para a subscrição. No entanto, desta vez, ligue as permissões API com o token de acesso só de aplicação que recebeu no passo anterior.

O método de [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) da aplicação de exemplo do ASP.NET MVC implementa esta chamada.

## <a name="manage-connected-subscriptions"></a>Gerir as subscrições ligadas

Quando a função RBAC apropriada é atribuída ao serviço da sua aplicação principal da subscrição, a aplicação pode manter monitorização/geri-lo utilizando tokens de acesso só de aplicação para o Gestor de recursos do Azure.

Se um proprietário de subscrição remove a atribuição de funções da sua aplicação utilizando o portal clássico ou ferramentas de linha de comandos, a aplicação já não poderão aceder a esse subscrição. Nesse caso, deverá notificar o utilizador que a ligação com a subscrição tiver sido cortada de fora da aplicação e conceder-lhes uma opção para "reparar" a ligação. "Reparar" simplesmente seria recrie a atribuição de funções que foi eliminada offline.

Tal como ativada ao utilizador ligar subscrições para a sua aplicação, terá de permitir ao utilizador para desligar demasiado subscrições. A partir de um acesso gestão ponto de vista, desligue significa remover a atribuição de funções que tenha principal do serviço a aplicação da subscrição. Opcionalmente, qualquer Estado na aplicação para a subscrição pode ser removido demasiado. Apenas os utilizadores com permissão de gestão de acesso da subscrição que conseguem desligar a subscrição.

O [método de RevokeRoleFromServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L200) da aplicação de exemplo do ASP.net MVC implementa esta chamada.

Já está - os utilizadores podem agora facilmente ligar e gerir as suas subscrições Azure com a aplicação.

