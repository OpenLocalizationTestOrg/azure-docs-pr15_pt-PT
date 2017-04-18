<properties
    pageTitle="Suporte de CORS na aplicação de serviço | Microsoft Azure"
    description="Saiba como utilizar o suporte CORS na aplicação de serviço de Azure Azure."
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
    ms.topic="get-started-article"
    ms.date="08/27/2016"
    ms.author="rachelap"/>

# <a name="consume-an-api-app-from-javascript-using-cors"></a>Consumir uma aplicação de API a partir de JavaScript utilizando CORS

Aplicação de serviço oferece suporte incorporado para [Publicação em origem recurso partilha (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing), que permite aos clientes JavaScript efetuar chamadas de domínios para APIs que são alojados nas aplicações de API. Serviço de aplicação permite-lhe configurar o acesso CORS ao seu API sem escrever qualquer código na sua API.

Este artigo contém duas secções:

* A secção de [como configurar CORS](#corsconfig) geral explica como configurar CORS para qualquer aplicação API, web app ou aplicação móvel. Aplica-se uniforme para todos os quadros que são suportados pelo serviço de aplicação, incluindo .NET, Node.js e Java. 

* Começar com a secção [continuar os tutoriais .NET introdução](#tutorialstart) , o artigo é um tutorial que demonstrará que CORS suporte através da criação de no qual fez no [primeiro aplicações API introdução de iniciação](app-service-api-dotnet-get-started.md). 

## <a id="corsconfig"></a>Como configurar CORS na aplicação de serviço do Azure

Pode configurar CORS no portal do Azure ou ao utilizar o [Gestor de recursos do Azure](../azure-resource-manager/resource-group-overview.md) ferramentas.

#### <a name="configure-cors-in-the-azure-portal"></a>Configurar CORS no portal do Azure

8. Num browser, aceda ao [Azure portal](https://portal.azure.com/).

2. Clique em **Serviços de aplicação**e, em seguida, clique no nome da sua aplicação API.

    ![Selecione a aplicação de API no portal](./media/app-service-api-cors-consume-javascript/browseapiapps.png)

10. Na pá **Definições** que é apresentada à direita da pá a **aplicação de API** , localize a secção **API** e, em seguida, clique em **CORS**.

    ![Selecione CORS na pá definições](./media/app-service-api-cors-consume-javascript/clicksettings.png)

11. No texto da caixa Introduza o URL ou URLs que pretende permitir chamadas JavaScript provenientes de.


    Por exemplo, se implementado a aplicação de JavaScript para uma aplicação web todolistangular com o nome, introduza "https://todolistangular.azurewebsites.net". Como alternativa, pode introduzir um asterisco (*) para especificar que todos os domínios de origem são aceites.


13. Clique em **Guardar**.

    ![Clique em Guardar](./media/app-service-api-cors-consume-javascript/corsinportal.png)

    Depois de clicar em **Guardar**, a aplicação de API aceitará JavaScript chamadas a partir dos URLs especificados.

#### <a name="configure-cors-by-using-azure-resource-manager-tools"></a>Configurar CORS utilizando ferramentas de Gestor de recursos do Azure

Também pode configurar CORS para uma aplicação de API com os [modelos de Gestor de recursos do Azure](../resource-group-authoring-templates.md) nas ferramentas de linha de comandos como [PowerShell do Azure](../powershell-install-configure.md) e o [Clip do Azure](../xplat-cli-install.md). 

Para obter um exemplo de um modelo de Gestor de recursos do Azure que define a propriedade CORS, abra o [ficheiro de azuredeploy.json no repositório de para a aplicação de exemplo neste tutorial](https://github.com/azure-samples/app-service-api-dotnet-todo-list/blob/master/azuredeploy.json). Localize a secção do modelo com um aspeto semelhante ao exemplo seguinte:

        "cors": {
            "allowedOrigins": [
                "todolistangular.azurewebsites.net"
            ]
        }

## <a id="tutorialstart"></a>Se continuar a iniciação de introdução ao .NET

Se estiver a seguir a Node.js ou Java introdução ao série para as aplicações de API, tiver concluído a série de introdução ao obter. Avance para a secção de [passos seguintes](#next-steps) para localizar sugestões de ainda mais saber mais sobre aplicações API.

O restante deste artigo é uma continuação da série de introdução ao .NET e assume que concluída com êxito [o tutorial primeiro](app-service-api-dotnet-get-started.md).

## <a name="deploy-the-todolistangular-project-to-a-new-web-app"></a>Implementar o projeto ToDoListAngular para uma nova aplicação web

[O primeiro tutorial](app-service-api-dotnet-get-started.md), criou uma aplicação de API camada e uma aplicação de API de níveis de dados. Neste tutorial criar uma aplicação web do página única aplicação (SPA) nessa aplicação chamadas API camada. Para SPA trabalhar tem de ativar o CORS a aplicação de camada API. 

Na [aplicação de exemplo de fazer](https://github.com/Azure-Samples/app-service-api-dotnet-todo-list), o projeto ToDoListAngular é um cliente de AngularJS simple que liga para o projeto de ToDoListAPI Web API camada. O código JavaScript no ficheiro *app/scripts/todoListSvc.js* chamadas API utilizando o fornecedor de AngularJS HTTP. 

        angular.module('todoApp')
        .factory('todoListSvc', ['$http', function ($http) {

            $http.defaults.useXDomain = true;
            delete $http.defaults.headers.common['X-Requested-With']; 
        
            return {
                getItems : function(){
                    return $http.get(apiEndpoint + '/api/TodoList');
                },

                /* Get by ID, Put, and Delete methods not shown */

                postItem : function(item){
                    return $http.post(apiEndpoint + '/api/TodoList', item);
                }
            };
        }]);

### <a name="create-a-new-web-app-for-the-todolistangular-project"></a>Criar uma nova aplicação web do projeto ToDoListAngular

O procedimento para criar uma nova aplicação web da aplicação de serviço e implementar um projeto para o mesmo é semelhante ao que viu para [criar e implementar uma aplicação de API no tutorial primeiro nesta série](app-service-api-dotnet-get-started.md#createapiapp). A única diferença é que o tipo de aplicação **Web App** em vez de **API aplicação**.  Para capturas de ecrã das caixas de diálogo, consulte o artigo 

1. No **Explorador de soluções**, com o botão direito do projecto ToDoListAngular e, em seguida, clique em **Publicar**.

3.  No separador **perfil** do assistente **Publicar Web** , clique em **Serviço de aplicação do Microsoft Azure**.

5. Na caixa de diálogo **Serviço de aplicação** , clique em **Novo**.

3. No separador da caixa de diálogo **Criar aplicação de serviço de** **alojamento** , introduza um **Nome da aplicação Web** que seja exclusivo no domínio de *azurewebsites.net* . 

5. Selecione o Azure **subscrição** que pretende trabalhar.

6. Na lista pendente **Grupo de recursos** , selecione o grupo de recursos mesmo que criou anteriormente.

4. Na lista pendente de **Plano de serviço de aplicação** , selecione o mesmo plano que criou anteriormente. 

7. Clique em **Criar**.

    Visual Studio cria a aplicação web, cria um perfil de publicar para a mesma e apresenta o **ligação** , passo do assistente **Publicar Web** .

    Não clique em **Publicar** para ainda. Na secção seguinte, configure a nova aplicação web para ligar a aplicação de API camada que está a ser executado na aplicação de serviço. 

### <a name="set-the-middle-tier-url-in-web-app-settings"></a>Definir o URL de camada nas definições de aplicação web

1. Aceda ao [portal do Azure](https://portal.azure.com/)e, em seguida, navegue para a pá **Web App** para a aplicação web que criou para alojar o projeto TodoListAngular (front-end).

2. Clique em **Definições > definições da aplicação**.

3. Na secção **definições de aplicação** , adicione a seguinte chave e valor:

  	|Chave|Valor|Exemplo
  	|---|---|---|
  	|toDoListAPIURL|o nome da aplicação de camada API https://{your} .azurewebsites .net|https://todolistapi0121.azurewebsites.NET|

4. Clique em **Guardar**.

    Quando o código é executado no Azure, este valor substitui o URL de localhost que está no ficheiro *Web. config* . 

    O código que obtém o valor de definição está nos *index.cshtml*:

        <script type="text/javascript">
            var apiEndpoint = "@System.Configuration.ConfigurationManager.AppSettings["toDoListAPIURL"]";
        </script>
        <script src="app/scripts/todoListSvc.js"></script>

    O código *todoListSvc.js* utiliza a definição:

        return {
            getItems : function(){
                return $http.get(apiEndpoint + '/api/TodoList');
            },
            getItem : function(id){
                return $http.get(apiEndpoint + '/api/TodoList/' + id);
            },
            postItem : function(item){
                return $http.post(apiEndpoint + '/api/TodoList', item);
            },
            putItem : function(item){
                return $http.put(apiEndpoint + '/api/TodoList/', item);
            },
            deleteItem : function(id){
                return $http({
                    method: 'DELETE',
                    url: apiEndpoint + '/api/TodoList/' + id
                });
            }
        };

### <a name="deploy-the-todolistangular-web-project-to-the-new-web-app"></a>Implementar o projeto de web ToDoListAngular para a nova aplicação web

*  No Visual Studio, no passo **ligação** do Assistente de **Publicar Web** , clique em **Publicar**.

    Visual Studio implementa o projeto ToDoListAngular a nova aplicação web e é aberta num browser para o URL da aplicação web. 

### <a name="test-the-application-without-cors-enabled"></a>Testar a aplicação sem CORS activado 

2. No seu browser ferramentas de programador, abra a janela da consola.

3. Na janela do browser que apresenta a IU AngularJS, clique na ligação **Para fazer a lista** .

    O código JavaScript tenta ligar para a aplicação de camada API, mas a chamada falha porque o front-end está em execução num domínio diferente do back-end. Janela de consola de ferramentas de programador do browser mostra uma mensagem de erro de publicação em origem.

    ![Mensagem de erro de origem de cruz](./media/app-service-api-cors-consume-javascript/consoleaccessdenied.png)

## <a name="configure-cors-for-the-middle-tier-api-app"></a>Configurar CORS para a aplicação de API camada

Nesta secção, configure a definição de CORS no Azure para a camada de aplicação ToDoListAPI API. Esta definição permite que a camada de aplicação de API para receber chamadas de JavaScript a partir da aplicação web que criou para o projeto ToDoListAngular.

8. Num browser, aceda ao [Azure portal](https://portal.azure.com/).

2. Clique em **Serviços de aplicação**e, em seguida, clique na aplicação de API ToDoListAPI (camada).

    ![Selecione a aplicação de API no portal](./media/app-service-api-cors-consume-javascript/browseapiapps.png)

10. Na pá **Definições** que é apresentada à direita da pá a **aplicação de API** , localize a secção **API** e, em seguida, clique em **CORS**.

    ![Selecione CORS no portal](./media/app-service-api-cors-consume-javascript/clicksettings.png)

12. Na caixa de texto, introduza o URL para a aplicação web do ToDoListAngular (front-end). Por exemplo, se implementado projeto ToDoListAngular para uma aplicação web com o nome todolistangular0121, permitir chamadas a partir do URL `https://todolistangular0121.azurewebsites.net`.

    Como alternativa, pode introduzir um asterisco (*) para especificar que todos os domínios de origem são aceites.

13. Clique em **Guardar**.

    ![Clique em Guardar](./media/app-service-api-cors-consume-javascript/corsinportal.png)

    Depois de clicar em **Guardar**, a aplicação de API aceitará JavaScript chamadas a partir do URL especificado. Nesta captura de ecrã, a aplicação de ToDoListAPI0223 API aceitará chamadas de cliente JavaScript a partir da aplicação web do ToDoListAngular.

### <a name="test-the-application-with-cors-enabled"></a>Testar a aplicação com CORS activado

* Abra um browser para o URL de HTTPS da aplicação web. 

    Desta vez à aplicação permite-lhe ver, adicionar, editar e eliminar itens a fazer. 

    ![Lista de itens pendentes page da aplicação de exemplo](./media/app-service-api-cors-consume-javascript/corssuccess.png)

## <a name="app-service-cors-versus-web-api-cors"></a>Aplicação de serviço de CORS versus Web API CORS

Num projeto Web API, pode instalar o pacote de [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/) NuGet para especificar no código que domínios aceitará a API JavaScript liga a partir de.
 
Suporte de API CORS Web é mais flexível que suporte CORS de serviço de aplicação. Por exemplo, no código pode especificar origens aceites diferentes métodos de acção diferente, enquanto a aplicação de serviço CORS Especifique um conjunto de origens diferentes aceites para todos os métodos de uma aplicação de API de.

> [AZURE.NOTE] Não tente utilizar Web API CORS e aplicação de serviço CORS numa aplicação do API. Aplicação de serviço de CORS terá precedência e Web API CORS não tem qualquer efeito. Por exemplo, se ativar um domínio de origem na aplicação de serviço e ativar todos os domínios de origem no seu código Web API, a aplicação de API do Azure aceitará apenas as chamadas a partir do domínio que especificou no Azure.

### <a name="how-to-enable-cors-in-web-api-code"></a>Como ativar CORS no código da Web API

Os passos seguintes resumem o processo de activar o suporte da Web API CORS. Para mais informações, consulte o artigo [Ativar pedidos de publicação em origem no ASP.NET Web API 2](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api).

1. Num projeto Web API, instale o pacote de NuGet [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/) .

1. Incluir um `config.EnableCors()` linha de código no método **Registe-se** da classe de **WebApiConfig** , tal como no exemplo seguinte. 

        public static class WebApiConfig
        {
            public static void Register(HttpConfiguration config)
            {
                // Web API configuration and services
                
                // The following line enables you to control CORS by using Web API code
                config.EnableCors();
    
                // Web API routes
                config.MapHttpAttributeRoutes();
    
                config.Routes.MapHttpRoute(
                    name: "DefaultApi",
                    routeTemplate: "api/{controller}/{id}",
                    defaults: new { id = RouteParameter.Optional }
                );
            }
        }

1. Na sua controlador da Web API, adicione uma `using` declaração para o `System.Web.Http.Cors` espaço de nomes e adicionar o `EnableCors` atributo para a classe de controlador ou para métodos de ação individuais. No exemplo seguinte, CORS suporte aplica-se para o controlador de todo.

        namespace ToDoListAPI.Controllers 
        {
            [HttpOperationExceptionFilterAttribute]
            [EnableCors(origins:"https://todolistangular0121.azurewebsites.net", headers:"accept,content-type,origin,x-my-header", methods: "get,post")]
            public class ToDoListController : ApiController
 
## <a name="using-azure-api-management-with-api-apps"></a>Utilizar a gestão de Azure API com aplicações API

Se utiliza o Azure API gestão com uma aplicação de API, configure CORS na gestão de API em vez de na aplicação API. Para obter mais informações, consulte os seguintes recursos:

* [Descrição geral de gestão do Azure API (vídeo: CORS começa em 12:10)](https://azure.microsoft.com/documentation/videos/azure-api-management-overview/)
* [Gestão de API cruzada políticas de domínio](https://msdn.microsoft.com/library/azure/dn894084.aspx#CORS)
 
## <a name="troubleshooting"></a>Resolução de problemas

No caso de ocorrer um problema ao percorrer neste tutorial, aqui estão algumas ideias de resolução de problemas.

* Certifique-se de que está a utilizar a versão mais recente do [Azure SDK para .NET para Visual Studio 2015](http://go.microsoft.com/fwlink/?linkid=518003).

* Certifique-se de que introduziu `https` na definição CORS e certifique-se de que está a utilizar `https` para executar a aplicação web front-end.

* Certifique-se de que introduziu a definição de CORS na aplicação de API camada, não na aplicação web front-end.

* Se estiver a configurar CORS no código da aplicação e aplicação de serviço de Azure, tenha em atenção que a definição de aplicação serviço CORS substituirá aquilo que está a fazer no código de aplicação. 

Para saber mais sobre as funcionalidades do Visual Studio que simplificam a resolução de problemas, consulte o artigo [resolução de problemas Azure aplicação de serviço de aplicações no Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md).

## <a name="next-steps"></a>Próximos passos 

Neste artigo, viu como ativar o suporte de CORS de serviço de aplicação para que o código JavaScript cliente pode ligar uma API um domínio diferente. Para saber mais sobre aplicações API, leia a [Introdução à autenticação na aplicação de serviço](../app-service/app-service-authentication-overview.md)e, em seguida, consulte o tutorial de [autenticação de utilizador para as aplicações de API](app-service-api-dotnet-user-principal-auth.md) .
