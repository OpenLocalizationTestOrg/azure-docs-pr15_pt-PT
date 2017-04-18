<properties
    pageTitle="Autenticação principal do serviço para as aplicações de API na aplicação de serviço de Azure | Microsoft Azure"
    description="Saiba como pode proteger uma aplicação de API na aplicação de serviço de Azure para cenários de serviço de serviço."
    services="app-service\api"
    documentationCenter=".net"
    authors="tdykstra"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-api"
    ms.workload="na"
    ms.tgt_pltfrm="dotnet"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/30/2016" 
    ms.author="rachelap"/>

# <a name="service-principal-authentication-for-api-apps-in-azure-app-service"></a>Autenticação principal do serviço para as aplicações de API na aplicação de serviço do Azure

## <a name="overview"></a>Descrição geral

Este artigo explica como utilizar a aplicação de serviço autenticação para acesso *interno* às aplicações API. Um cenário interno é onde tem uma aplicação de API ao qual pretende ser consumíveis apenas pelo seu próprio código da aplicação. O caminho recomendado para implementar a este cenário na aplicação de serviço é utilizar Azure AD para proteger a aplicação de API denominada. Ligar para a aplicação de API protegida com uma ligação OAuth que obtém a partir do Azure AD ao fornecer a identidade da aplicação credenciais (serviço principal). Para alternativas ao utilizar o Azure AD, consulte a secção de **serviço de serviço de autenticação** de [Descrição geral do Azure aplicação de serviço de autenticação](../app-service/app-service-authentication-overview.md#service-to-service-authentication).

Neste artigo, que vai aprender:

* Como utilizar o Azure Active Directory (Azure AD) para proteger uma aplicação de API a partir do access não autenticados.
* Como consumir uma aplicação de API protegida a partir de uma aplicação de API, web app ou aplicação móvel ao utilizar credenciais do Azure AD serviço capital (identidade da aplicação). Para obter informações sobre como consumir através de uma aplicação de lógica, consulte o artigo [utilizar a API personalizado alojado no serviço de aplicação com aplicações de lógica](../app-service-logic/app-service-logic-custom-hosted-api.md).
* Como se certificar de que a aplicação de API protegida não pode receber uma chamada através do browser pelos utilizadores com sessão iniciada.
* Como para se certificar de que a aplicação de API protegida só pode ser chamada por um específico Azure AD serviço capital.

Este artigo contém duas secções:

* A secção de [como configurar a autenticação principal do serviço na aplicação de serviço de Azure](#authconfig) geral explica como configurar a autenticação para qualquer aplicação do API e como consumir a aplicação de API protegida. Nesta secção aplica-se de forma igual para todos os quadros suportados pelo serviço de aplicação, incluindo .NET, Node.js e Java.

* Começar com a secção [continuar os tutoriais .NET introdução](#tutorialstart) , o tutorial orienta-configurar um cenário de "acesso interno" para uma aplicação do exemplo .NET em execução no serviço de aplicação. 

## <a id="authconfig"></a>Como configurar autenticação principal do serviço na aplicação de serviço do Azure

Esta secção fornece instruções gerais que se aplicam a qualquer aplicação do API. Para obter passos específicos para a aplicação de exemplo para o fazer de .NET de lista, vá para [continuar a série de tutoriais .NET API aplicações](#tutorialstart).

1. No [portal do Azure](https://portal.azure.com/), navegue para a pá **Definições** da aplicação API que pretende proteger e, em seguida, localize a secção de **funcionalidades** e clique em **autenticação / autorização**.

    ![Autenticação/autorização no portal do Azure](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. No **autenticação / autorização** pá, **clique em**.

4. Na lista pendente de **ação a tomar quando não é autenticado pedido** , selecione **Iniciar sessão com o Azure Active Directory** .

5. Em **Fornecedores de autenticação**, selecione o **Azure Active Directory**.

    ![Pá autenticação/autorização no portal do Azure](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. Configurar a **Definições do Azure Active Directory** pá para criar uma nova aplicação do Azure AD ou utilizar uma aplicação do Azure AD existente se já tiver uma que pretende utilizar.

    Cenários internos normalmente envolvem uma aplicação de API chamar uma aplicação de API. Pode utilizar em separado do Azure AD as aplicações para cada aplicação API ou apenas uma aplicação do Azure AD.

    Para obter instruções detalhadas sobre esta pá, consulte [como configurar a sua aplicação de serviço de aplicação para utilizar o início de sessão do Azure Active Directory](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md).

7. Quando tiver terminado a pá de configuração do fornecedor de autenticação, clique em **OK**.

7. No **autenticação / autorização** pá, clique em **Guardar**.

    ![Clique em Guardar](./media/app-service-api-dotnet-service-principal-auth/authsave.png)

Quando este estiver concluída, o serviço de aplicação permite apenas pedidos os autores das chamadas a configurado no inquilino do Azure AD. Código de autenticação ou a autorização de é necessária na aplicação API protegida. A ligação OAuth lhe é transmitido a aplicação de API juntamente com frequentemente utilizadas em afirmações nos cabeçalhos de HTTP e pode ler essas informações no código para validar se pedidos estão a partir de um autor específico, tal como um principal de serviço.

Esta funcionalidade de autenticação funciona da mesma forma para todos os idiomas que suporta a aplicação de serviço, incluindo .NET, Node.js e Java. 

#### <a name="how-to-consume-the-protected-api-app"></a>Como consumir a aplicação de API protegida

O autor da chamada tem de fornecer uma ligação OAuth Azure AD com chamadas à API. Para obter uma ligação OAuth com as credenciais principal de serviço, o autor da chamada utiliza a biblioteca de autenticação do Active Directory (ADAL para [.NET](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory), [Node.js](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs)ou [Java](https://github.com/AzureAD/azure-activedirectory-library-for-java)). Para obter um token, o código que chamadas ADAL fornece para ADAL as seguintes informações:

* O nome do seu inquilino do Azure AD.
* O ID de cliente e o segredo de cliente (tecla de aplicação) da aplicação do Azure AD associado com o autor da chamada.
* O ID de cliente da aplicação do Azure AD associada com a aplicação de API protegida. (Se for utilizada apenas uma aplicação do Azure AD, é o mesmo ID de cliente que aquele para o autor da chamada.)

Estes valores estão disponíveis nas páginas do Azure AD do [Azure portal clássica](https://manage.windowsazure.com/).

Assim que tiver sido adquirido o token, o autor da chamada inclui-lo com pedidos de HTTP no cabeçalho da autorização.  Aplicação de serviço de validar o token e permite que os pedidos alcançar a aplicação de API protegida.

#### <a name="how-to-protect-the-api-app-from-access-by-users-in-the-same-tenant"></a>Como pode proteger a aplicação de API do acesso por utilizadores ao mesmo inquilino

Tokens de tipo de ligação para os utilizadores ao mesmo inquilino são considerados válidos para a aplicação de API protegida.  Se quiser para se certificar de que apenas um principal de serviço pode ligar a aplicação de API protegida, adicione o código na aplicação API protegida para validar as seguintes afirmações do token:

* `appid`deve ser o ID do cliente da aplicação Azure AD que está associado com o autor da chamada. 
* `oid`(`objectidentifier`) devem ser o ID principal de serviço do autor da chamada. 

Aplicação de serviço também fornece o `objectidentifier` reclamar no cabeçalho X-MS-cliente-capital-ID.

### <a name="how-to-protect-the-api-app-from-browser-access"></a>Como pode proteger a aplicação de API do acesso ao browser

Se não validar em afirmações no código na aplicação API protegida e, se utilizar separado uma aplicação do Azure AD para a aplicação de API protegida, certifique-se de que URL a aplicação Azure AD de resposta não é igual URL de base a aplicação de API. Se o URL de resposta pontos diretamente para a aplicação de API protegida, um utilizador ao mesmo inquilino Azure AD poderia navegue para a aplicação de API, inicie sessão e com êxito chamar a API.

## <a id="tutorialstart"></a>Se continuar a série de tutoriais .NET API aplicações

Se está a seguir a série de tutoriais Node.js ou Java para as aplicações de API, avance para a secção [os passos seguintes](#next-steps) . 

O restante deste artigo continua a série de tutoriais .NET API aplicações e assume que tiver concluído o [tutorial de autenticação de utilizador](app-service-api-dotnet-user-principal-auth.md) e tem a aplicação de exemplo em execução no Azure com autenticação de utilizador ativada.

## <a name="set-up-authentication-in-azure"></a>Configurar autenticação no Azure

Nesta secção configura o serviço de aplicação para que os pedidos HTTP apenas permitir para alcançar a aplicação de API de níveis de dados são aqueles que tenham Azure válido tokens de portadores de AD. 

Na secção seguinte, configure a aplicação de API camada para enviar credenciais da aplicação para o Azure AD, voltar uma ligação OAuth e enviar a ligação OAuth para a aplicação de API de níveis de dados. Este processo é ilustrado no diagrama.

![Diagrama de autenticação de serviço](./media/app-service-api-dotnet-service-principal-auth/appdiagram.png)

Se ocorrer problemas ao seguir as instruções de iniciação, consulte a secção de [resolução de problemas](#troubleshooting) no fim do tutorial. 

1. No [portal do Azure](https://portal.azure.com/), navegue para a pá **Definições** da aplicação API que criou para a aplicação de API ToDoListDataAPI (camada de dados) e, em seguida, clique em **Definições**.

2. Na pá **Definições** , localize a secção de **funcionalidades** e, em seguida, clique em **autenticação / autorização**.

    ![Autenticação/autorização no portal do Azure](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. No **autenticação / autorização** pá, **clique em**.

4. Na lista pendente de **ação a tomar quando não é autenticado pedido** , selecione **Iniciar sessão com o Azure Active Directory**.

    Esta é a definição que faz com que a aplicação de serviço para se certificar de que apenas autenticadas pedidos atingir a aplicação de API. Para pedidos que tenham tokens de tipo de ligação válida, de aplicação de serviço de transmite os tokens ao longo para a aplicação de API e preenche os cabeçalhos de HTTP com afirmações utilizadas com frequência para disponibilizar essas informações mais facilmente ao seu código.

5. Em **Fornecedores de autenticação**, clique em **Azure Active Directory**.

    ![Pá autenticação/autorização no portal do Azure](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. No pá **Definições do Azure Active Directory** , clique em **Express**.

    Com o **Express** opção Azure automaticamente pode criar uma aplicação de AAD no seu [inquilino](https://msdn.microsoft.com/en-us/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant)do Azure AD. 

    Não tem de criar um inquilino, uma vez que cada conta Azure automaticamente tem uma.

7. Em **modo de gestão**, clique em **Criar nova aplicação de AD** se ainda não estiver selecionada.

    O portal se ligue à caixa de introdução de **Criar aplicação** com um valor predefinido. Por predefinição, a aplicação do Azure AD chama-se o mesmo que a aplicação de API. Se preferir, pode introduzir um nome diferente.
    
    ![Definições do Azure AD](./media/app-service-api-dotnet-service-principal-auth/aadsettings.png)

    **Nota**: como alternativa, pode utilizar uma única Azure AD aplicação para a aplicação de API chamada e a aplicação de API protegida. Se tiver optado por essa alternativa, não terá a opção **Criar uma nova aplicação de AD** aqui porque já tiver criado uma aplicação do Azure AD anteriormente no tutorial de autenticação de utilizador. Para este tutorial, irá utilizar separar as aplicações do Azure AD para a aplicação de API chamada e a aplicação de API protegida.

8. Tome nota do valor que se encontra na caixa de introdução **Criar aplicação** ; irá procurar esta aplicação AAD no portal do Azure clássica mais tarde.

7. Clique em **OK**.

10. No **autenticação / autorização** pá, clique em **Guardar**.

    ![Clique em Guardar](./media/app-service-api-dotnet-service-principal-auth/saveauth.png)

    Aplicação de serviço cria uma aplicação do Azure Active Directory com **início de sessão do URL** e responder automaticamente definir um **URL** para o URL da sua aplicação API. O último valor permite aos utilizadores no seu inquilino AAD para iniciar sessão e aceder a aplicação de API.

### <a name="verify-that-the-api-app-is-protected"></a>Certifique-se de que a aplicação de API está protegida

1. Num browser, aceda ao URL da aplicação API: na pá de **aplicação API** no portal do Azure, clique na ligação em **URL**. 

    É redirecionado para um ecrã de início de sessão porque não autenticados pedidos não são permitidos para alcançar a aplicação de API. 

    Se o seu browser, vá para a IU Swagger, o browser poderá já ter sessão iniciado no – nesse caso, abra uma janela InPrivate ou janela e ir para o URL de IU Swagger.

18. Inicie sessão com as credenciais de um utilizador no seu inquilino AAD.

    Quando tem sessão iniciada, a página "criada com êxito" é apresentado no browser.

## <a name="configure-the-todolistapi-project-to-acquire-and-send-the-azure-ad-token"></a>Configurar o projecto ToDoListAPI para adquirir e enviar o token do Azure AD

Nesta secção efetuar as seguintes tarefas:

* Adicione código na aplicação API camada que utiliza credenciais da aplicação Azure AD para adquirir um token e enviá-la com pedidos de HTTP para a aplicação de API de níveis de dados.
* Obtenha as credenciais de que precisar do Azure AD.
* Introduza as credenciais nas definições de ambiente de runtime de aplicação de serviço do Azure na camada API aplicação. 

### <a name="configure-the-todolistapi-project-to-acquire-and-send-the-azure-ad-token"></a>Configurar o projecto ToDoListAPI para adquirir e enviar o token do Azure AD

Efetue as seguintes alterações no projeto ToDoListAPI no Visual Studio.

1. Remova os comentários todo o código no ficheiro *ServicePrincipal.cs* .

    Este é o código que utiliza ADAL para .NET para adquirir o OAuth Azure AD.  Utiliza vários valores de configuração, irá configurar o ambiente do Azure runtime mais tarde. Eis o código: 

        public static class ServicePrincipal
        {
            static string authority = ConfigurationManager.AppSettings["ida:Authority"];
            static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
            static string clientSecret = ConfigurationManager.AppSettings["ida:ClientSecret"];
            static string resource = ConfigurationManager.AppSettings["ida:Resource"];
        
            public static AuthenticationResult GetS2SAccessTokenForProdMSA()
            {
                return GetS2SAccessToken(authority, resource, clientId, clientSecret);
            }
        
            static AuthenticationResult GetS2SAccessToken(string authority, string resource, string clientId, string clientSecret)
            {
                var clientCredential = new ClientCredential(clientId, clientSecret);
                AuthenticationContext context = new AuthenticationContext(authority, false);
                AuthenticationResult authenticationResult = context.AcquireToken(
                    resource,
                    clientCredential);
                return authenticationResult;
            }
        }

    **Nota:** Este código requer o ADAL para .NET NuGet pacote (Microsoft.IdentityModel.Clients.ActiveDirectory), que já está instalado no projeto. Se este projeto foram a criar de raiz, teria que instalar este pacote. Este pacote não está instalado automaticamente pelo modelo API aplicação novo projeto.

2. No *Controladores/ToDoListController*, remova os comentários o código na `NewDataAPIClient` método que adiciona o token de HTTP pedidos no cabeçalho da autorização.

        client.HttpClient.DefaultRequestHeaders.Authorization =
            new AuthenticationHeaderValue("Bearer", ServicePrincipal.GetS2SAccessTokenForProdMSA().AccessToken);

3. Implemente o projeto ToDoListAPI. (Com o botão direito do projeto, em seguida, clique em **Publicar > publicar**.)

    Visual Studio implementa o projecto e é aberta num browser para URL base do web app. Isto vai mostrar uma página de 403 erro, que é normal para uma tentativa Ir para um URL de base da Web API a partir de um browser.

4. Feche o browser.

### <a name="get-azure-ad-configuration-values"></a>Obter valores de configuração do Azure AD

11. No [portal clássica Azure](https://manage.windowsazure.com/), aceda a **Azure Active Directory**.

12. No separador do **diretório** , clique em seu inquilino AAD.

14. Clique em **aplicações > aplicações proprietário da minha empresa**e, em seguida, clique na marca de verificação.

15. Na lista de aplicações, clique no nome do que Azure criado por si quando activou autenticação para a aplicação de API ToDoListDataAPI (camada de dados).

16. Clique no separador **Configurar** .

5. Copie o valor de **ID de cliente** e guardá-lo noutro local que pode obtê-lo a partir de mais tarde. 

8. No portal do Azure clássico regresse à lista de **aplicações do proprietário da minha empresa**e, clique na aplicação de AAD que criou para a aplicação de ToDoListAPI API camada (aquele que criou no tutorial anterior, não aquele que criou neste tutorial).

16. Clique no separador **Configurar** .

5. Copie o valor de **ID de cliente** e guardá-lo noutro local que pode obtê-lo a partir de mais tarde.

6. Em **chaves**, selecione **1 ano** a partir da lista de seta de lista pendente **Selecione duração** .

6. Clique em **Guardar**.

    ![Gerar chave da aplicação](./media/app-service-api-dotnet-service-principal-auth/genkey.png)

7. Copie o valor de chave e guardá-lo noutro local que pode obtê-lo a partir de mais tarde.

    ![Copiar a nova chave de aplicação](./media/app-service-api-dotnet-service-principal-auth/genkeycopy.png)

### <a name="configure-azure-ad-settings-in-the-middle-tier-api-apps-runtime-environment"></a>Configurar as definições do Azure AD runtime ambiente a aplicação camada API

1. Aceda ao [portal do Azure](https://portal.azure.com/)e, em seguida, navegue para a **Aplicação de API** pá para a aplicação de API que aloja o projeto TodoListAPI (camada).

2. Clique em **Definições > definições da aplicação**.

3. Na secção **definições de aplicação** , adicione as seguintes chaves e valores:

  	| **Chave** | Autoridade de ida: |
  	|---|---|
  	| **Valor** | https://login.microsoftonline.com/ {seu nome de inquilino do Azure AD} |
  	| **Exemplo** | https://login.microsoftonline.com/contoso.onmicrosoft.com |

  	| **Chave** | IDA: ClientId |
  	|---|---|
  	| **Valor** | ID de cliente da aplicação de chamada (camada - ToDoListAPI) |
  	| **Exemplo** | 960adec2-b74a-484a-960adec2-b74a-484a |

  	| **Chave** | IDA: ClientSecret |
  	|---|---|
  	| **Valor** | Tecla de aplicação da aplicação de chamada (camada - ToDoListAPI) |
  	| **Exemplo** | e65e8fc9-5f6b-48e8-e65e8fc9-5f6b-48e8 |

  	| **Chave** | IDA: recurso |
  	|---|---|
  	| **Valor** | ID de cliente da aplicação denominada (camada de dados - ToDoListDataAPI) |
  	| **Exemplo** | e65e8fc9-5f6b-48e8-e65e8fc9-5f6b-48e8 |

    **Nota**: para `ida:Resource`, certifique-se de utilizar a aplicação denominados **ID de cliente** e não a sua **Aplicação ID URI**.

    `ida:ClientId`e `ida:Resource` são diferentes valores para este tutorial, uma vez que estiver a utilizar o separam applicaations Azure AD para a camada e camadas de dados. Se estivesse a utilizar uma única aplicação do Azure AD para a aplicação de API chamada e a aplicação de API protegida, teria de utilizar o mesmo valor em ambas as `ida:ClientId` e `ida:Resource`.

    O código utiliza ConfigurationManager para obter estes valores, para que pode ser armazenados no ficheiro de Web. config do projeto ou no ambiente do Azure runtime. Enquanto uma aplicação do ASP.NET estiver em execução no serviço de aplicação do Azure, definições de ambiente substituam automaticamente definições a partir da Web. config. Definições de ambiente geralmente são uma [forma mais segura para armazenar informações confidenciais em comparação comparadas um ficheiro de Web. config](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure).

6. Clique em **Guardar**.

    ![Clique em Guardar](./media/app-service-api-dotnet-service-principal-auth/appsettings.png)

### <a name="test-the-application"></a>Testar a aplicação

1. Num browser, aceda ao URL HTTPS do AngularJS front-end web app.

2. Clique no separador **Lista de fazer** e registo com as credenciais para um utilizador no inquilino do Azure AD. 

4. Adicione itens a fazer para verificar que a aplicação está a funcionar.

    ![Para a página de lista](./media/app-service-api-dotnet-service-principal-auth/mvchome.png)

    Se a aplicação não funciona como esperado, verifique todas as definições que introduziu no portal do Azure. Se todas as definições aparecem parece correta, consulte a secção de [resolução de problemas](#troubleshooting) mais tarde neste tutorial.

## <a name="protect-the-api-app-from-browser-access"></a>Proteger a aplicação de API de acesso ao browser

Para este tutorial criou separado uma aplicação do Azure AD para a aplicação de API ToDoListDataAPI (camada de dados). À medida que visualizou, quando a aplicação de serviço cria uma aplicação de AAD, configura a aplicação AAD de uma forma que permite que um utilizador ir para o URL da aplicação API num browser e inicie sessão. Isto significa que é possível para um utilizador de fim no seu inquilino do Azure AD, não apenas um serviço de capital; para aceder à API. 

Se pretende impedir que o acesso ao browser sem escrever qualquer código na aplicação API protegida, pode alterar o **URL de resposta** na aplicação AAD para que seja diferente do URL base a aplicação de API. 

### <a name="disable-browser-access"></a>Desativar o acesso ao browser

1. No separador de **Configurar** o portal clássica para a aplicação de AAD que foi criada para a TodoListService, altere o valor no campo **URL de resposta** para que seja um URL válido, mas não a aplicação de API URL.
 
2. Clique em **Guardar**.

### <a name="verify-browser-access-no-longer-works"></a>Verifique se o acesso ao browser já não funciona

Anteriormente verificado que pode ir para o URL da aplicação de API através do browser, iniciar sessão com as credenciais de um utilizador individual. Nesta secção, verifique o que esta já não é possível. 

1. Numa nova janela do browser, aceda ao URL da aplicação API novamente.

2. Inicie sessão quando lhe for pedido para fazê-lo.

3. Início de sessão é concluída com êxito mas leva a uma página de erro.

    Que configurou a aplicação AAD para que os utilizadores no inquilino AAD não é possível iniciar sessão e aceder a API a partir de um browser. Pode ainda aceder a aplicação de API utilizando um token principal de serviço, que pode verificar ao aceder ao URL do web app e adicionar itens a fazer mais.

## <a name="restrict-access-to-a-particular-service-principal"></a>Restringir o acesso a um principal de serviço em particular  

Botão direito do rato agora, qualquer autor que pode obter um token para um utilizador ou principal do serviço no inquilino do Azure AD pode ligar a aplicação de API TodoListDataAPI (camada de dados). Poderá pretender Certifique-se de que a aplicação de API de níveis de dados aceita apenas chamadas a partir da aplicação de API TodoListAPI (camada) e apenas a partir de um capital serviço em particular. 

Pode adicionar estes restrições ao adicionar o código para validar o `appid` e `objectidentifier` em afirmações em chamadas a receber.

Para este tutorial colocar o código que valida a ID da aplicação e ID principal de serviço diretamente nas suas ações controlador.  Alternativas estão a utilizar um personalizado `Authorize` atributo ou fazer esta validação seu arranque sequências (por exemplo, software intermédio OWIN). Para um exemplo de último, consulte [esta aplicação de exemplo](https://github.com/mohitsriv/EasyAuthMultiTierSample/blob/master/MyDashDataAPI/Startup.cs). 

Efetue as seguintes alterações ao projeto TodoListDataAPI.

2. Abra o ficheiro *Controllers/TodoListController.cs* .

3. Remova os comentários as linhas que definir `trustedCallerClientId` e `trustedCallerServicePrincipalId`.

        private static string trustedCallerClientId = ConfigurationManager.AppSettings["todo:TrustedCallerClientId"];
        private static string trustedCallerServicePrincipalId = ConfigurationManager.AppSettings["todo:TrustedCallerServicePrincipalId"];

4. Remova os comentários o código do método CheckCallerId. Este método chama-se no início de cada método de ação no controlador de. 

        private static void CheckCallerId()
        {
            string currentCallerClientId = ClaimsPrincipal.Current.FindFirst("appid").Value;
            string currentCallerServicePrincipalId = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
            if (currentCallerClientId != trustedCallerClientId || currentCallerServicePrincipalId != trustedCallerServicePrincipalId)
            {
                throw new HttpResponseException(new HttpResponseMessage { StatusCode = HttpStatusCode.Unauthorized, ReasonPhrase = "The appID or service principal ID is not the expected value." });
            }
        }

5. Implementar o projeto ToDoListDataAPI a aplicação de serviço de Azure.

6. No seu browser, aceda ao URL do HTTPS o AngularJS front-end do web app e, na home page clique no separador **Lista de tarefas** .

    A aplicação não funciona porque não conseguem chamadas para o back-end. O novo código está a verificar appid real e objectidentifier mas ainda não tem os valores corretos para dar entrada contra. Browser da consola de ferramentas de programador relatórios que o servidor está a devolver um erro de HTTP 401.

    ![Erro no programador nas ferramentas da consola](./media/app-service-api-dotnet-service-principal-auth/webapperror.png)

    Nos passos seguintes configurar os valores esperados.

8. Utilizar o PowerShell do Azure AD, obter o valor do capital de serviço para a aplicação do Azure AD que criou para o projeto TodoListWebApp.

    um. Para obter instruções sobre como instalar o Azure PowerShell e ligar à sua subscrição, consulte o artigo [Utilizar o PowerShell Azure com o Gestor de recursos do Azure](../powershell-azure-resource-manager.md).

    b. Para obter uma lista das principais de serviço, execute o `Login-AzureRmAccount` comando e, em seguida, o `Get-AzureRmADServicePrincipal` comando.

    c. Localizar o ID de objecto para o capital de serviço da aplicação TodoListAPI e guardá-lo numa localização que pode copiar a partir de mais tarde.

7. No portal do Azure, navegue para a pá da aplicação de API para a aplicação de API implementado o projeto ToDoListDataAPI para.

9. Clique em **Definições > definições da aplicação**.

3. Na secção **definições de aplicação** , adicione as seguintes chaves e valores:

  	| **Chave** | todo:TrustedCallerServicePrincipalId |
  	|---|---|
  	| **Valor** | Id da aplicação que entra em contacto da principal de serviço |
  	| **Exemplo** | 4f4a94a4-6f0d-4072-4f4a94a4-6f0d-4072 |

  	| **Chave** | todo:TrustedCallerClientId |
  	|---|---|
  	| **Valor** | ID do cliente do chamar aplicação - copiada a partir da aplicação TodoListAPI Azure AD |
  	| **Exemplo** | 960adec2-b74a-484a-960adec2-b74a-484a |

6. Clique em **Guardar**.

    ![Clique em Guardar](./media/app-service-api-dotnet-service-principal-auth/trustedcaller.png)

6. No seu browser, regressar ao URL do web app e, na home page clique no separador **Lista de tarefas** .

    Desta vez a aplicação funciona como esperado porque a aplicação de autor fidedigna ID e ID principal de serviço são os valores do esperado.

    ![Para a página de lista](./media/app-service-api-dotnet-service-principal-auth/mvchome.png)

## <a name="building-the-projects-from-scratch"></a>Criar projetos de raiz

Os dois projetos da Web API foram criados utilizando o modelo de projeto de **Aplicação de API do Azure** e substituir o controlador de valores predefinidos com um controlador de fazer. Para adquirir tokens principais do serviço de Azure AD no projeto ToDoListAPI, foi instalado o pacote do [Active Directory autenticação biblioteca (ADAL) para o .NET](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) NuGet.
 
Para obter informações sobre como criar uma aplicação de página única AngularJS com a API do Web back-end como ToDoListAngular, consulte o artigo [mãos no laboratório: criar uma aplicação de página única (SPA) com a API de Web do ASP.NET e Angular.js](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs). Para obter informações sobre como adicionar o código de autenticação do Azure AD, consulte o artigo [Proteger AngularJS única página de aplicações com o Azure AD](../active-directory/active-directory-devquickstarts-angular.md).

## <a name="troubleshooting"></a>Resolução de problemas

[AZURE.INCLUDE [troubleshooting](../../includes/app-service-api-auth-troubleshooting.md)]

* Certifique-se de que não confunda ToDoListAPI (camada) e ToDoListDataAPI (camada de dados). Por exemplo, neste tutorial adicionar autenticação para a aplicação de API de níveis de dados, **mas a tecla de aplicação tem de vir de aplicação do Azure AD que criou para a aplicação de camada API**.

## <a name="next-steps"></a>Próximos passos

Este é o último tutorial na série API aplicações. 

Para obter mais informações sobre o Azure Active Directory, consulte os seguintes recursos.

* [Guia dos Azure AD os programadores](http://aka.ms/aaddev)
* [Cenários do Azure AD](http://aka.ms/aadscenarios)
* [Exemplos do Azure AD](http://aka.ms/aadsamples)

    O exemplo de [Web App-WebAPI-oauth2 ainda-AppIdentity-DotNet](http://github.com/AzureADSamples/WebApp-WebAPI-OAuth2-AppIdentity-DotNet) é semelhante a que é apresentado neste tutorial, mas sem utilizando a autenticação de aplicação de serviço.

Para obter informações sobre outras formas de implementar projetos do Visual Studio para as aplicações do API, utilizando o Visual Studio ou ao [automatizar implementação](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) a partir de um [sistema de controlo de origem](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control), consulte o artigo [como implementar uma aplicação de serviço de aplicação do Azure](../app-service-web/web-sites-deploy.md).
