<properties
   pageTitle="Como criar uma aplicação que pode iniciar sessão no qualquer utilizador do Azure Active Directory | Microsoft Azure"
   description="Passo a passo as instruções para a criação de uma aplicação que podem iniciar sessão um utilizador a partir de qualquer inquilino do Azure Active Directory, também conhecido como uma aplicação do inquilino com várias."
   services="active-directory"
   documentationCenter=""
   authors="skwan"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/11/2016"
   ms.author="skwan;bryanla"/>

# <a name="how-to-sign-in-any-azure-active-directory-ad-user-using-the-multi-tenant-application-pattern"></a>Como iniciar sessão no qualquer utilizador do Azure Active Directory (AD) utilizando o padrão de aplicação do inquilino com várias
Se oferecer um Software de como uma aplicação de serviço para muitas organizações, pode configurar a aplicação para aceitar inícios de sessão a partir de qualquer inquilino do Azure AD.  No Azure AD esta opção é denominada tornar o seu inquilino com várias aplicações.  Os utilizadores no inquilino qualquer Azure AD poderão iniciar sessão na sua aplicação depois consentir para utilizar a sua conta com a aplicação.  

Se tiver uma aplicação existente que tenha o seu próprio sistema de conta, ou suporta outros tipos de início de sessão a partir de outros fornecedores de nuvem, adicionar Azure AD início de sessão a partir de qualquer inquilino é tão simple como registar a sua aplicação, adicionar o início de sessão no código através de oauth2 ainda, OpenID ligar ou SAML e colocar uma iniciar sessão com o botão do Microsoft na sua aplicação. Clique no botão abaixo para obter mais informações sobre a aplicação de imagem corporativa.

[! [Início de sessão botão] [AAD-início de sessão no]][AAD-App-Branding]


Este artigo assume que já está familiarizado criar uma aplicação do inquilino único para Azure AD.  Se não, cabeça até a [página principal do Guia para programadores] [ AAD-Dev-Guide] e experimente uma das nossos inícios rápidos!

Existem quatro passos simples para converter a aplicação numa aplicação do inquilino com várias Azure AD:

1.  Atualizar o registo de aplicação para ser com várias inquilino
2.  Atualizar o seu código para enviar pedidos para /comum ponto final 
3.  Atualizar o seu código para processar vários valores de emissor
4.  Compreender consentimento de utilizador e o administrador e efetuar alterações de código adequado

Vamos olhar cada passo detalhadamente. Também pode ir diretamente para [Esta lista de inquilinos com várias amostras][AAD-Samples-MT].

## <a name="update-registration-to-be-multi-tenant"></a>Atualizar o registo ser com várias inquilino
Por predefinição, os registos de aplicação/API web no Azure AD são inquilino único.  Pode tornar o registo inquilino com várias por localizar o parâmetro "Aplicação é com várias inquilino" na página Configuração do seu registo de aplicação no [portal clássica Azure] [ AZURE-classic-portal] e defini-la como "Sim".

Nota: Antes de uma aplicação pode ser efetuada com várias inquilino, Azure AD requer o URI de ID da aplicação da aplicação para ser globalmente exclusivos. O URI de ID da aplicação é uma das formas que uma aplicação é identificada em mensagens de protocolo.  Para uma aplicação do inquilino única, é suficiente para o URI de ID de aplicação ser exclusivo esse inquilino.  Para uma aplicação do inquilino com várias, tem de ser exclusivo global para que Azure AD possa localizar a aplicação em todos os inquilinos.  Exclusividade global é aplicada ao exigir o URI de ID da aplicação de ter um nome de anfitrião que corresponde a um domínio verificado do inquilino Azure AD.  Por exemplo, se o nome do seu inquilino foi contoso.onmicrosoft.com, em seguida, um válido aplicação ID URI seria `https://contoso.onmicrosoft.com/myapp`.  Se o inquilino tinha um domínio verificado do `contoso.com`, em seguida, um URI de ID da aplicação válido também seria `https://contoso.com/myapp`.  Definir uma aplicação como o inquilino com várias falhará se o URI de ID da aplicação não siga este padrão.

Registos de cliente nativo estão inquilinos com várias por predefinição.  Não precisa de tomar qualquer ação para fazer um nativo inquilino com várias do registo de aplicação de cliente.

## <a name="update-your-code-to-send-requests-to-common"></a>Atualizar o seu código para enviar pedidos para /Common.
Numa aplicação do inquilino única, pedidos de início de sessão é enviados para início de sessão o inquilino ponto final.   Por exemplo, para contoso.onmicrosoft.com o ponto final seria:

    https://login.microsoftonline.com/contoso.onmicrosoft.com

Pedidos de enviadas para o ponto final de um inquilino podem iniciar sessão utilizadores (ou convidados) nesse inquilino para aplicações nesse inquilino.  Com uma aplicação do inquilino com várias, a aplicação não sabe adiantado que inquilino o utilizador está a partir de, para que não é possível enviar pedidos para ponto final de um inquilino.  Em vez disso, sendo enviados pedidos para um ponto final que multiplexes através de todas as Azure AD inquilinos:

    https://login.microsoftonline.com/common

Quando o Azure AD recebe um pedido de /comum ponto final,-o utilizador a iniciar sessão e como consequência descobre que o utilizador está a partir de inquilino.  A/ponto final comuns funciona com todos os protocolos de autenticação suportados pelo Azure AD: OpenID ligar, OAuth 2.0, SAML 2.0 e WS Federação.

Início de sessão resposta à aplicação, em seguida, contém um token que representa o utilizador.  O valor de emissor no token de indica uma aplicação do que o utilizador está a partir de inquilino.  Quando uma resposta devolve de /comum ponto final, o valor de emissor no token de corresponderá ao inquilino do utilizador.  É importante ter em atenção /comum ponto final não é um inquilino e não é um emissor, é apenas um multiplexador.  Ao utilizar /Common., a lógica na sua aplicação para validar os tokens tem de ser atualizadas para ter em consideração este. 

Como mencionado anteriormente, inquilinos com várias aplicações também devem fornecer uma experiência de início de sessão consistente para os utilizadores, após a aplicação do Azure AD imagem corporativa diretrizes. Clique no botão abaixo para obter mais informações sobre a aplicação de imagem corporativa.

[! [Início de sessão botão] [AAD-início de sessão no]][AAD-App-Branding]

Vamos dar uma a utilização de /comum ponto final e a sua execução de código mais detalhadamente.

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>Atualizar o seu código para processar vários valores de emissor
Aplicações Web e web APIs receber e validam os tokens a partir do Azure AD.  

> [AZURE.NOTE] Enquanto aplicações de cliente nativo pedir e recebem tokens a partir do Azure AD, eles fazem-lo para enviá-las para APIs, onde são validados.  Aplicações nativas não validar os tokens e tem tratá-los como opaca.

Vamos ver no modo como uma aplicação valida tokens recebe a partir do Azure AD.  Uma aplicação de inquilino única normalmente irá demorar um valor de ponto final como:

    https://login.microsoftonline.com/contoso.onmicrosoft.com

e utilizá-la para construir um URL de metadados (neste caso, ligar OpenID) como:

    https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration

Para transferir dois blocos críticos de informações que são utilizados para validar os tokens: o inquilino do assinatura teclas e valor emissor.  Cada inquilino do Azure AD tem um valor exclusivo emissor do formulário:

    https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/

onde o valor GUID é a versão de seguro para mudar o nome do ID de inquilino do inquilino.  Se clicar na ligação metadados acima para `contoso.onmicrosoft.com`, pode ver este valor emissor no documento.

Quando uma aplicação de inquilino única valida um token, verifica a assinatura de tokens contra as teclas de assinatura do documento de metadados e torna-se de que o valor de emissor no token de corresponde ao que foi encontrada no documento de metadados.

Desde /comum ponto final não correspondem a um inquilino e não é um emissor, quando é examinar o valor de emissor nos metadados da/comuns tem um URL transformada em modelo em vez de um valor real:

    https://sts.windows.net/{tenantid}/

Por conseguinte, uma aplicação do inquilino com várias não é possível validar os tokens bastando correspondem ao valor emissor nos metadados com o `issuer` valor no token de.  Uma aplicação do inquilino com várias necessita de lógica de decidir quais os valores de emissor são válidos e que não, com base no inquilino a parte de ID do valor emissor.  

Por exemplo, se uma aplicação do inquilino com várias permite apenas iniciar sessão a partir de inquilinos específicos que se inscreveram dos seus serviços, em seguida,-tem de dar o valor de emissor ou o `tid` reclamar valor no token de para se certificar de que esse inquilino está na sua lista de subscritores.  Se uma aplicação do inquilino com várias apenas lida com indivíduos e não a tomar decisões de acesso com base em inquilinos, em seguida,-pode ignorar o valor de emissor completamente.

Nas amostras inquilinos com várias que encontrará na secção [Conteúdo relacionado](#related-content) no final deste artigo, validação emissor é desativada para ativar a qualquer inquilino do Azure AD iniciar sessão.

Agora vamos ver a experiência do utilizador para utilizadores que iniciar sessão no inquilinos com várias aplicações.

## <a name="understanding-user-and-admin-consent"></a>Utilizador compreensão e consentimento de administrador
Para um utilizador iniciar sessão numa aplicação no Azure AD, a aplicação tem de ser representada no inquilino do utilizador.  Esta opção permite-a organização fazer coisas como aplicar políticas exclusivas quando os utilizadores a partir do seu inquilino iniciar sessão para a aplicação.  Para uma aplicação de inquilino única este registo é simple; for o que acontece quando registar a aplicação no [portal clássica Azure][AZURE-classic-portal].

Para uma aplicação do inquilino com várias, o registo inicial para a aplicação encontra-se no inquilino Azure AD utilizado pelo programador.  Quando um utilizador a partir de um inquilino diferentes inicia sessão para a aplicação pela primeira vez, Azure AD pede-los consentimento para as permissões pedidas pela aplicação.  Se estes consentimento, em seguida, uma representação da aplicação denominada um *principal de serviço* é criada no inquilino do utilizador e pode continuar a iniciar sessão. Também é criada uma delegação no diretório que regista o consentimento do utilizador para a aplicação. Consulte o artigo [aplicação objectos e Principal do serviço] [ AAD-App-SP-Objects] para obter detalhes sobre a aplicação aplicação e ServicePrincipal objetos e como se relacionam umas às outras.

![Consentimento para camada única aplicação][Consent-Single-Tier] 

Esta experiência consentimento é afetada pelas permissões pedidas pela aplicação.  Azure AD suporta dois tipos de permissões, aplicação e só delegadas:

- Uma permissão delegada atribui uma aplicação a capacidade para funcionar como um utilizador com sessão iniciada para um subconjunto das coisas o utilizador pode fazer.  Por exemplo, pode conceder uma aplicação a permissão delegada para ler o com a sessão iniciada no calendário do utilizador.
- É concedida uma permissão só de aplicação diretamente para a identidade da aplicação.  Por exemplo, pode conceder a permissão de aplicação só para ler a lista de utilizadores num inquilino uma aplicação, e -poderão fazer isto, independentemente de quem iniciou sessão para a aplicação.

Algumas permissões podem ser manifestado acordas para por um utilizador habitual, enquanto outras necessitam de autorização de um administrador inquilino. 

### <a name="admin-consent"></a>Consentimento de administrador
Permissões só de aplicação sempre requerem consentimento um administrador inquilino.  Se a sua aplicação os pedidos de permissão de aplicação só e um utilizador normal tenta iniciar sessão para a aplicação, a sua aplicação receberão uma mensagem de erro indicando que o utilizador não conseguir consentimento.

Determinadas permissões delegadas também requerem consentimento um administrador inquilino.  Por exemplo, a capacidade de escrever de volta para Azure AD como o utilizador com sessão iniciada na requer consentimento um administrador inquilino.  Como permissões só de aplicação, se um utilizador normal tenta iniciar sessão para uma aplicação que os pedidos de permissão a um delegado que requer consentimento administrador, a aplicação irão receber um erro.  Se requer uma permissão ou não administração consentimento é determinado pelo programador que publicou o recurso e pode ser encontrado na documentação para o recurso.  Ligações para tópicos que descrevem as permissões disponíveis para a API do Azure AD Graph e a API do Microsoft Graph estão na secção [Relacionados conteúdo](#related-content) deste artigo.

Se a sua aplicação utiliza as permissões que requerem consentimento de administração, tem de ter um gesto na sua aplicação como um botão ou a ligação onde o administrador pode iniciar a ação.  O pedido da aplicação envia para esta ação é um pedido de autorização oauth2 ainda/OpenID ligar-se habitualmente, mas que também inclui a `prompt=admin_consent` parâmetro da cadeia de consulta.  Assim que o administrador deu e o capital de serviço é criado no inquilino do cliente, subsequentes início de sessão pedidos não necessita do `prompt=admin_consent` parâmetro.   Uma vez que o administrador tiver decidido que as permissões necessárias são aceitáveis, outros utilizadores no inquilino não será pedido para consentimento a partir desse momento reencaminhar.

O `prompt=admin_consent` parâmetro também pode ser utilizado por aplicações que pedem as permissões que não necessitam de administração consentimento, mas pretende dar-lhe uma experiência onde o administrador inquilino "se inscreve" para a aplicação de uma vez e outros utilizadores não é pedido para consentimento a partir desse momento no.

Se necessita de uma aplicação consentimento administrador, e o administrador inicia sessão para a aplicação mas o `prompt=admin_consent` parâmetro não é enviado, o administrador poderão com êxito consentimento para a aplicação mas estes serão apenas consentimento para a sua conta de utilizador.  Os utilizadores normais irão ainda não conseguir iniciar sessão e consentimento para a aplicação.  Isto é útil se pretender permitir que o administrador inquilino criem para explorar a aplicação antes de permitir que outros utilizadores do access.

Um administrador inquilino pode desativar a capacidade para os utilizadores normais consentimento para aplicações.  Se estiver desactivada esta capacidade, administração consentimento sempre é necessário para a aplicação de ser configuradas no inquilino.  Se pretender teste a sua aplicação com o utilizador habitual consentimento desativado, pode encontrar o parâmetro de configuração no inquilino Azure AD secção de configuração do [Azure portal clássica][AZURE-classic-portal].

> [AZURE.NOTE] Algumas aplicações que uma experiência de onde os utilizadores normais conseguem consentimento inicialmente e, mais tarde a aplicação pode envolvam as permissões de administrador e pedido que requerem consentimento de administrador.  Não existe nenhuma forma de fazer com um registo única aplicação no Azure AD hoje.  O futuras ponto final do Azure AD v2 irá permitir que as aplicações para pedir permissões o tempo de execução, em vez de momento registo, que permitirá este cenário.  Para obter mais informações, consulte o [Guia de programador do modelo de aplicação do Azure AD v2][AAD-V2-Dev-Guide].

### <a name="consent-and-multi-tier-applications"></a>Aplicações consentimento e várias camadas
A aplicação poderá ter várias camadas, cada representada pelo seu próprio registo no Azure AD.  Por exemplo, uma aplicação nativa que chama um API web ou uma aplicação web que liga-lhe uma web API.  Em ambos os casos, o cliente (aplicação nativa ou do web app) pede permissões para ligar para o recurso (web API).  Para o cliente com êxito ser manifestado o acordo no inquilino de um cliente, já tem de existir à qual os pedidos permissões de todos os recursos no inquilino do cliente.  Se esta condição do não for cumprida, Azure AD devolverão um erro que o recurso tem de ser adicionado pela primeira vez.

Isto pode ser um problema se a sua aplicação lógica consiste em duas ou mais registos de acesso aplicação, por exemplo um cliente separado e um recurso.  Como pode obter o recurso para o inquilino do cliente primeiro?  Azure AD abrange neste caso, ao ativar o cliente e de recursos ser manifestado o acordo num único passo, onde o utilizador vê o total da soma das permissões pedido pelo cliente e recursos na página de autorização.  Para permitir que este comportamento, registo da aplicação do recurso tem de incluir o ID da aplicação do cliente como um `knownClientApplications` no respetivo manifesto de aplicação.  Por exemplo:

    knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]

Esta propriedade pode ser atualizada através do recurso [manifesto da aplicação][AAD-App-Manifest]e é demonstrado num cliente nativo de várias camado chamar exemplo da web API na secção [Conteúdo relacionado](#related-content) no final deste artigo. O diagrama abaixo fornece uma descrição geral do seu consentimento para uma aplicação de várias camada:

![Consentimento à aplicação cliente conhecidos várias camadas][Consent-Multi-Tier-Known-Client] 

Um caso semelhante acontece se estiverem registadas diferentes camadas de uma aplicação no inquilinos diferentes.  Por exemplo, considere as maiúsculas/minúsculas da criação de uma aplicação cliente nativo que liga a API do Office 365 Exchange Online.  Desenvolver o nativo aplicação e versões posterior para a aplicação nativa executar no inquilino de um cliente, tem de ser apresentar o capital de serviço Exchange Online.  Neste caso, o cliente tem que comprar o Exchange Online para o serviço principal criada no seu inquilino.  No caso de uma API criada por uma organização que não seja a Microsoft, o programador da API do tem de fornecer uma maneira dos seus clientes consentimento da sua aplicação para um inquilino do cliente, por exemplo uma página web que unidades consentimento utilizando os mecanismos descritos neste artigo.  Depois do capital de serviço é criado no inquilino, a aplicação nativa poderá obter tokens API.

O diagrama abaixo fornece uma descrição geral do seu consentimento para uma aplicação de várias camada registada no inquilinos diferentes:

![Consentimento para a aplicação com vários participantes várias camada][Consent-Multi-Tier-Multi-Party] 

### <a name="revoking-consent"></a>Revogar a consentimento
Os utilizadores e administradores podem revogar o seu consentimento para a sua aplicação em qualquer altura:

- Os utilizadores revogar o acesso a aplicações individuais, removendo-los das suas [Aplicações do Access painel] [ AAD-Access-Panel] lista.
- Os administradores de revogar o acesso às aplicações removendo-los a partir do Azure AD utilizando a secção de gestão do Azure AD do [Azure portal clássico][AZURE-classic-portal].

Se um administrador autorize para uma aplicação para todos os utilizadores num inquilino, os utilizadores não podem revogar o acesso individualmente.  Apenas o administrador pode revogar o acesso e apenas para a aplicação completa.

### <a name="consent-and-protocol-support"></a>Consentimento e suporte do protocolo
Consentimento é suportado no Azure AD através do token, OpenID ligar, WS Federação e protocolos SAML.  Os protocolos SAML e Federação WS não suportam a `prompt=admin_consent` parâmetro, para que o administrador consentimento só é possível via OAuth e OpenID ligar.

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>Inquilinos com várias aplicações e em cache Tokens de acesso
Aplicações do inquilinos com várias também podem obter tokens de acesso para chamar APIs que estejam protegidos por Azure AD.  Um erro comum ao utilizar o Active Directory autenticação biblioteca (ADAL) com uma aplicação do inquilino com várias é inicialmente a pedir um token para um utilizador utilizando /Common., recebe uma resposta e, em seguida, pedir um token subsequente para esse utilizador mesmo também utilizar /Common..  Uma vez que a resposta a partir do Azure AD provêm de um inquilino, não/comuns, ADAL coloca em cache o token como sendo a partir do inquilino. A chamada subsequente para /Common. para obter um token de acesso do utilizador falha a entrada da cache e é pedido ao utilizador para iniciar sessão novamente.  Para evitar em falta a cache, certifique-se das chamadas subsequentes efetuadas para um utilizador já com a sessão iniciada no são efetuadas a ponto final do inquilino.

## <a name="related-content"></a>Conteúdo relacionado

- [Exemplos de aplicação do inquilino com várias][AAD-Samples-MT]
- [Imagem corporativa diretrizes para aplicações][AAD-App-Branding]
- [Guia do Azure AD para programadores][AAD-Dev-Guide]
- [Objectos de aplicação e Principal de serviço][AAD-App-SP-Objects]
- [Integrar aplicações com o Azure Active Directory][AAD-Integrating-Apps]
- [Descrição geral do quadro consentimento][AAD-Consent-Overview]
- [API do Microsoft Graph âmbitos de permissão][MSFT-Graph-AAD]
- [Âmbitos de permissão do Azure AD Graph API][AAD-Graph-Perm-Scopes]

Utilize a secção de comentários Disqus abaixo para fornecer comentários e ajude-nos refinar e dar forma a nossa conteúdo.

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]: ./active-directory-branding-guidelines.md
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Consent-Overview]: ./active-directory-integrating-applications.md#overview-of-the-consent-framework
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Overview]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-graph-api/
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Samples-MT]: https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multitenant
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-classic-portal]: https://manage.windowsazure.com
[MSFT-Graph-AAD]: https://graph.microsoft.io/en-us/docs/authorization/permission_scopes

<!--Image references-->
[AAD-Sign-In]: ./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png
[Consent-Single-Tier]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-single-tier.png
[Consent-Multi-Tier-Known-Client]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-known-clients.png
[Consent-Multi-Tier-Multi-Party]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-multi-party.png

<!--Reference style links -->
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]: ./active-directory-token-and-claims.md
[AAD-V2-Dev-Guide]: ./active-directory-appmodel-v2-overview.md
[AZURE-classic-portal]: https://manage.windowsazure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[O365-Perm-Ref]: https://msdn.microsoft.com/en-us/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Code-Grant-Flow]: https://msdn.microsoft.com/library/azure/dn645542.aspx
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3 
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: http://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-ID-Token]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken














