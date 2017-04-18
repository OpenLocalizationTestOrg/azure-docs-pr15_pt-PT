<properties
   pageTitle="Noções sobre manifesto da aplicação Azure Active Directory | Microsoft Azure"
   description="Cobertura detalhada da manifesto da aplicação Azure Active Directory, que representa a configuração de identidade de uma aplicação num inquilino Azure AD e é utilizada para facilitar OAuth autorização, experiência consentimento e mais."
   services="active-directory"
   documentationCenter=""
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/11/2016"
   ms.author="dkershaw;bryanla"/>

# <a name="understanding-the-azure-active-directory-application-manifest"></a>Noções sobre manifesto da aplicação Azure Active Directory

Aplicações que se integram com o Azure Active Directory (AD) tem de estar registadas com um inquilino do Azure AD, fornecer uma configuração de identidade persistente para a aplicação. Esta configuração é consultada no runtime, permitindo-cenários que permitem uma aplicação para deixem e broker autenticação/autorização através do Azure AD. Para obter mais informações sobre o modelo de aplicação do Azure AD, consulte o artigo a [Adicionar, atualizar e remover uma aplicação] [ ADD-UPD-RMV-APP] artigo.

## <a name="updating-an-applications-identity-configuration"></a>Actualizar a configuração de identidade de uma aplicação

Existem realmente várias opções disponíveis para atualizar as propriedades na configuração de identidade de uma aplicação, que variam em capacidades e graus de dificuldade, incluindo o seguinte:

- O ** [do Azure clássica portal] [ AZURE-CLASSIC-PORTAL] interface de utilizador do Web** permite actualizar as propriedades mais comuns de uma aplicação. Esta é a forma de passível de erro de forma mais rápida e menos de atualização de propriedades da sua aplicação, mas não dar-lhe acesso total a todas as propriedades, como os dois métodos.
- Para mais avançados cenários onde precisa de atualizar as propriedades que não são expostas no portal do Azure clássico, pode modificar **manifesto da aplicação**. Este é o foco deste artigo e é abordado mais detalhadamente começando na secção seguinte.
- Também é possível **escrever uma aplicação que utiliza a [Graph API] [ GRAPH-API] ** para atualizar a sua aplicação, que exige o máximo esforço. Isto pode ser uma opção apelativa embora, se estiver a escrever o software de gestão de ou se precisar de atualizar as propriedades da aplicação regularmente de forma automática.

## <a name="using-the-application-manifest-to-update-an-applications-identity-configuration"></a>Utilizar manifesto da aplicação para atualizar a configuração de identidade de uma aplicação
Através do [portal clássica Azure][AZURE-CLASSIC-PORTAL], pode gerir configuração de identidade da sua aplicação, ao transferir e carregar um JSON ficheiro representação, denominada manifesto de uma aplicação. Sem real do ficheiro é armazenado no diretório. Manifesto da aplicação é simplesmente uma HTTP GET operação a entidade de aplicação do Azure AD Graph API e o carregamento é uma operação HTTP correcção a entidade de aplicação.

Como resultado, para poder compreender o formato e propriedades de manifesto da aplicação, terá de fazer referência a Graph API [entidade de aplicação] [ APPLICATION-ENTITY] documentação. Exemplos de atualizações que podem ser executadas apesar de carregamento manifesto da aplicação incluem:

- **Âmbitos de permissão Declare (oauth2Permissions)** exposto pela sua web API. Consulte o tópico "Expor APIs para outras aplicações Web" nas [Aplicações de integração com o Azure Active Directory] [ INTEGRATING-APPLICATIONS-AAD] para obter informações sobre como implementar a representação de utilizador utilizar o oauth2Permissions delegada âmbito de permissão. Tal como mencionado anteriormente, propriedades da aplicação entidade encontram-se documentados na Graph API [entidade e tipo complexo] [ APPLICATION-ENTITY] artigo de referência, incluindo a propriedade oauth2Permissions que é uma coleção de tipo de [OAuth2Permission][APPLICATION-ENTITY-OAUTH2-PERMISSION].
- **Funções da aplicação Declare (appRoles) expostas pela sua aplicação**. Propriedade de appRoles a entidade de aplicação é uma coleção de tipo de [AppRole][APPLICATION-ENTITY-APP-ROLE]. Consulte o [controlo de acesso no aplicações de nuvem utilizando o Azure AD baseado em funções] [ RBAC-CLOUD-APPS-AZUREAD] artigo para obter um exemplo de implementação.
- **Declare conhecidos cliente aplicações (knownClientApplications)**, que lhe permitem logicamente associam o seu consentimento as aplicações de cliente especificado para o recurso/web API.
- **Pedir Azure AD para emitir reclamar membros do grupo** para o utilizador com sessão iniciada na (groupMembershipClaims).  Também pode ser configurado para emitir em afirmações sobre associações de funções de diretório do utilizador. Consulte o artigo [autorização nas aplicações de nuvem utilizar grupos de AD] [ AAD-GROUPS-FOR-AUTHORIZATION] artigo para obter um exemplo de implementação.
- **Permitir a aplicação para suportar OAuth 2.0 implícito conceder** fluxos (oauth2AllowImplicitFlow). Este tipo de fluxo de conceder é utilizado com incorporado JavaScript páginas ou da web única página de aplicações (SPA). Para mais informações sobre a conceder autorização implícito, consulte o artigo [Noções sobre a oauth2 ainda implícito conceder fluxo no Azure Active Directory][IMPLICIT-GRANT].
- **Utilização de ativar das X509 certificados como chave secreta** (keyCredentials). Consulte o artigo [criar aplicações de serviço e daemon no Office 365] [ O365-SERVICE-DAEMON-APPS] [Guia do programador para auth com a API do Gestor de recursos do Azure] e[ DEV-GUIDE-TO-AUTH-WITH-ARM] artigos para obter exemplos de implementação.
- **Adicionar um novo ID URI da aplicação** para a sua aplicação (identifierURIs[]). Aplicação ID URIs são utilizadas para identificar exclusivamente uma aplicação do seu inquilino do Azure AD (ou através de múltiplos inquilinos do Azure AD, para cenários de inquilinos com várias quando qualificados através de domínio personalizado verificado). São utilizadas quando pedir permissões para uma aplicação de recursos ou ao adquirir um token de acesso para uma aplicação do recurso. Quando atualiza este elemento, a mesma actualização é feita para servicePrincipalNames [coleção o capital service correspondente que encontra-se no inquilino principal da aplicação.

Manifesto da aplicação fornece também uma boa forma de controlar o estado do seu registo da aplicação. Uma vez que está disponível no formato JSON, pode ser dada a representação de ficheiro para o controlo de origem, juntamente com código de origem da sua aplicação.

## <a name="step-by-step-example"></a>Passo, por exemplo do passo
Permite agora aos percorrer os passos necessários para atualizar a configuração de identidade da sua aplicação através de manifesto da aplicação. Vamos irá realçar um dos exemplos anteriores, que mostra como declarar um novo âmbito de permissão numa aplicação recurso:

1. Navegue para o [portal clássica Azure] [ AZURE-CLASSIC-PORTAL] e inicie sessão com uma conta que possui privilégios de administrador cocriação ou de serviço.

2. Depois de que tenha autenticado, desloque-se para baixo e selecione o Azure "extensão do Active Directory" no painel de navegação à esquerda (1), em seguida, clique no inquilino do Azure AD onde a aplicação é registada (2).

    ![Selecione o inquilino do Azure AD][SELECT-AZURE-AD-TENANT]

3. Quando a página de diretório surgir, clique em "Aplicações" (1) na parte superior a página para ver uma lista de aplicações registadas no inquilino. Em seguida, localize a aplicação que pretende atualizar na lista e clique na mesma (2).

    ![Selecione o inquilino do Azure AD][SELECT-AZURE-AD-APP]

4. Agora que selecionou página principal da aplicação, repare a funcionalidade "Gerir manifesto" na parte inferior da página (1). Se clicar esta ligação, vai ser-lhe para transferir ou carregar o ficheiro de manifesto JSON. Clique em "Transferir manifesto" (2). Isto serão imediatamente seguido com a caixa de diálogo de confirmação transferir perguntar para confirmar ao clicar em "Transferir manifesto" (3), em seguida, quer abrir ou guardar o ficheiro localmente (4).

    ![Gerir o manifesto, opção de transferência][MANAGE-MANIFEST-DOWNLOAD]

    ![Transferir o manifesto][DOWNLOAD-MANIFEST]

5. Neste exemplo, vamos guardou o ficheiro localmente, permitindo-nos abrir um editor, fazer alterações a JSON e guarde novamente. Eis o aspeto da estrutura JSON dentro do editor do Visual Studio JSON. Nota que segue o esquema da [entidade de aplicação] [ APPLICATION-ENTITY] tal como mencionado anteriormente:

    ![Atualizar a JSON manifesto][UPDATE-MANIFEST]

    Por exemplo, partindo do princípio de que pretendemos implementar/exposição uma nova permissão designado por "Employees.Read.All" nossa aplicação de recurso (API), basta adicionaria um elemento de novo/segundo para a coleção de oauth2Permissions ie:

        "oauth2Permissions": [
        {
        "adminConsentDescription": "Allow the application to access MyWebApplication on behalf of the signed-in user.",
        "adminConsentDisplayName": "Access MyWebApplication",
        "id": "aade5b35-ea3e-481c-b38d-cba4c78682a0",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to access MyWebApplication on your behalf.",
        "userConsentDisplayName": "Access MyWebApplication",
        "value": "user_impersonation"
        },
        {
        "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
        "adminConsentDisplayName": "Read-only access to Employee records",
        "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
        "userConsentDisplayName": "Read-only access to your Employee records",
        "value": "Employees.Read.All"
        }
        ],

    A entrada tem de ser exclusiva e, consequentemente, tem de gerar um novo globalmente exclusivo ID (GUID) para o `"id"` propriedade. Neste caso, uma vez que recomendamos especificado `"type": "User"`, esta permissão pode ser manifestado o acordo para por qualquer conta autenticada pelo Azure AD de inquilinos no qual a aplicação de recurso/API está registada. Isto concede o cliente de permissão de aplicação para aceder à mesma em nome da conta. As cadeias de nome descrição e apresentação são utilizadas durante consentimento e para ser exibida no portal do Azure clássica.

6. Quando tiver terminado de atualizar o manifesto, regressar à página de aplicação do Azure AD no portal clássica do Azure e clique na "Gerir manifesto" funcionalidade novamente (1). Mas, desta vez, selecione a opção "Carregar manifesto" (2). Tal como a transferência, será ser-me novamente com uma segunda caixa de diálogo, solicitar para a localização do ficheiro JSON. Clique em "Procure o ficheiro..." (3), em seguida, utilize a caixa de diálogo "Escolher ficheiro para carregar" para selecionar o ficheiro JSON (4), e prima "Abrir". Assim que a caixa de diálogo desaparece, selecione a marca de verificação "OK" (5) e o manifesto será carregado.  

    ![Gerir o manifesto, carregar opção][MANAGE-MANIFEST-UPLOAD]

    ![Carregar o JSON manifesto][UPLOAD-MANIFEST]

    ![Carregar o JSON manifesto - confirmação][UPLOAD-MANIFEST-CONFIRM]

Agora que o manifesto é guardado, pode dar um registados acesso de aplicação de cliente para a nova permissão adicionámos acima. Desta vez pode utilizar Web IU o Azure clássico do portal em vez de edição manifesto a aplicação de cliente:  

1. Em primeiro lugar aceda à página "Configurar" da aplicação cliente ao qual pretende adicionar o acesso para a nova API e clique no botão "Adicionar aplicação".
2. Em seguida, será apresentada com a lista de aplicações de recursos registado (APIs) no inquilino. Clique no sinal de adição / + símbolo junto ao nome da aplicação de recursos para selecioná-la.  
3. Em seguida, clique na marca de verificação no canto inferior direito.
4. Quando regressar à secção "Adicionar aplicação" da página de configuração do seu cliente, irá ver a nova aplicação de recurso na lista. Se passar o rato sobre a secção "Permissões de delegado" à direita nessa linha, irá ver uma lista pendente aparecer. Clique na lista e, em seguida, selecione a permissão nova para adicionar à lista de pedida do cliente de permissões. Nota: Esta permissão novo será armazenado na configuração de identidade a aplicação de cliente, na propriedade "requiredResourceAccess" coleções de sites.

![Permissões para outras aplicações][PERMS-TO-OTHER-APPS]

![Permissões para outras aplicações][PERMS-SELECT-APP]

![Permissões para outras aplicações][PERMS-SELECT-PERMS]

Já está. Agora as aplicações serão executada utilizando a sua nova configuração de identidade.

## <a name="next-steps"></a>Próximos passos
- Para obter mais detalhes sobre a relação entre torno dos objetos aplicação e Principal do serviço de uma aplicação, consulte o artigo [aplicação e objectos principais de serviço no Azure AD][AAD-APP-OBJECTS].
- Consulte o [Glossário de programador do Azure AD] [ AAD-DEVELOPER-GLOSSARY] para saber as definições de algumas dos conceitos de programador do Azure Active Directory (AD) core.

Utilize a secção de comentários DISQUS abaixo para fornecer comentários e ajude-nos refinar e dar forma a nossa conteúdo.

<!--Image references-->
[DOWNLOAD-MANIFEST]: ./media/active-directory-application-manifest/download-manifest.png
[MANAGE-MANIFEST-DOWNLOAD]: ./media/active-directory-application-manifest/manage-manifest-download.png
[MANAGE-MANIFEST-UPLOAD]: ./media/active-directory-application-manifest/manage-manifest-upload.png
[PERMS-SELECT-APP]: ./media/active-directory-application-manifest/portal-perms-select-app.png
[PERMS-SELECT-PERMS]: ./media/active-directory-application-manifest/portal-perms-select-perms.png
[PERMS-TO-OTHER-APPS]: ./media/active-directory-application-manifest/portal-perms-to-other-apps.png
[SELECT-AZURE-AD-APP]: ./media/active-directory-application-manifest/select-azure-ad-application.png
[SELECT-AZURE-AD-TENANT]: ./media/active-directory-application-manifest/select-azure-ad-tenant.png
[UPDATE-MANIFEST]: ./media/active-directory-application-manifest/update-manifest.png
[UPLOAD-MANIFEST]: ./media/active-directory-application-manifest/upload-manifest.png
[UPLOAD-MANIFEST-CONFIRM]: ./media/active-directory-application-manifest/upload-manifest-confirm.png

<!--article references -->
[AAD-APP-OBJECTS]: active-directory-application-objects.md
[AAD-DEVELOPER-GLOSSARY]: active-directory-dev-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]: active-directory-integrating-applications.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-CLASSIC-PORTAL]: https://manage.windowsazure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]: active-directory-dev-understanding-oauth2-implicit-grant.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

