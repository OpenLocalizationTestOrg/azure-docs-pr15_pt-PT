<properties
    pageTitle="Introdução às aplicações API e ASP.NET na aplicação de serviço | Microsoft Azure"
    description="Saiba como criar, implementar e consumir uma aplicação do ASP.NET API no Azure aplicação de serviço, utilizando o Visual Studio 2015."
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
    ms.topic="hero-article"
    ms.date="09/20/2016"
    ms.author="rachelap"/>

# <a name="get-started-with-api-apps-aspnet-and-swagger-in-azure-app-service"></a>Introdução às aplicações API, ASP.NET e Swagger na aplicação de serviço do Azure

[AZURE.INCLUDE [selector](../../includes/app-service-api-get-started-selector.md)]

Este é o primeiro numa série de tutoriais Mostrar como utilizar funcionalidades de aplicação de serviço de Azure que são úteis para desenvolver e alojamento RESTful APIs.  Neste tutorial abrange suporte para metadados API no formato de Swagger.

Que vai aprender:

* Como criar e implementar [API aplicações](app-service-api-apps-why-best-platform.md) na aplicação de serviço de Azure utilizando ferramentas incorporadas no Visual Studio 2015.
* Como para automatizar a deteção de API utilizando o pacote de Swashbuckle NuGet para gerar dinamicamente Swagger API metadados.
* Como utilizar a Swagger API metadados para gerar código do cliente para uma aplicação de API automaticamente.

## <a name="sample-application-overview"></a>Descrição geral da aplicação de exemplo

Neste tutorial, trabalhar com uma aplicação de exemplo de lista de acções a fazer simples. A aplicação tem um front-end da página única aplicação (SPA), uma camada de ASP.NET Web API e uma camada de ASP.NET Web API de dados.

![Diagrama de aplicação de exemplo API aplicações](./media/app-service-api-dotnet-get-started/noauthdiagram.png)

Eis uma captura de ecrã de [AngularJS](https://angularjs.org/) front-end.

![API aplicações de exemplo aplicação lista de itens pendentes](./media/app-service-api-dotnet-get-started/todospa.png)

A solução do Visual Studio inclui três projetos:

![](./media/app-service-api-dotnet-get-started/projectsinse.png)

* **ToDoListAngular** - o front-end: uma SPA AngularJS que liga para a camada.

* **ToDoListAPI** - a camada: um projeto de ASP.NET Web API liga para a camada de dados para efetuar operações CRUD no itens a fazer.

* **ToDoListDataAPI** - a camada de dados: um projeto de ASP.NET Web API que executa operações CRUD no itens a fazer.

A arquitetura de três camadas é um dos muitos arquitecturas que pode implementar utilizando aplicações API e aqui é utilizado apenas para fins de demonstração. O código de cada camada é tão simple quanto possível demonstrar as funcionalidades das aplicações de API; Por exemplo, a camada de dados utiliza memória do servidor em vez de uma base de dados como respectivo mecanismo persistente.

Em a concluir este tutorial, terá os dois projectos Web API para cima e a execução de na nuvem nas aplicações de API do serviço de aplicação.

O próximo tutorial na série implementa o front end da SPA na nuvem.

## <a name="prerequisites"></a>Pré-requisitos

* API de Web do ASP.NET - as instruções tutoriais partem do pressuposto de que tem um conhecimento básico de como trabalhar com ASP.NET [Web API 2](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) no Visual Studio.

* Conta Azure - pode [Abrir uma conta Azure gratuitamente](/pricing/free-trial/?WT.mc_id=A261C142F) ou [Ativar o Visual Studio benefícios de subscritor](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

    Se pretender começar com a aplicação de serviço de Azure antes de se inscrever para uma conta do Azure, aceda ao [Serviço de aplicação tente](http://go.microsoft.com/fwlink/?LinkId=523751). Não existem, imediatamente pode criar uma aplicação starter curto na aplicação de serviço — **sem cartão de crédito obrigatório**e não compromissos.

* Visual Studio 2015 com o [Azure SDK para .NET](https://azure.microsoft.com/downloads/archive-net-downloads/) - SDK o Visual Studio 2015 é instalado automaticamente se ainda não tiver.

    * No Visual Studio, clique em Ajuda -> sobre o Microsoft Visual Studio e certifique-se de que tem "Ferramentas do Azure de aplicação de serviço v2.9.1" ou posterior instalado.

    ![Azure vesion de ferramentas de aplicação](./media/app-service-api-dotnet-get-started/apiversion.png)

    >[AZURE.NOTE] Dependendo das dependências SDK quantos já tiver no seu computador, instalar o SDK poderá demorar muito tempo, a partir de alguns minutos para de meia hora ou mais.

## <a name="download-the-sample-application"></a>Transferir a aplicação de exemplo

1. Transferir o repositório [Azure-Samples/app-service-api-dotnet-to-do-list](https://github.com/Azure-Samples/app-service-api-dotnet-todo-list) .

    Pode clicar no botão **Transferir ZIP** ou clonar o repositório no seu computador local.

2. Abra a solução de fazer no Visual Studio 2015 ou 2013.
   1. Terá de todas as soluções de fidedignidade.
        ![Aviso de segurança](./media/app-service-api-dotnet-get-started/securitywarning.png)

3. Crie a solução (CTRL + SHIFT + B) para restaurar os pacotes de NuGet.

    Se pretender ver a aplicação numa operação de antes da implementação, pode executar-localmente. Certifique-se de que ToDoListDataAPI é o seu projeto de arranque e executar a solução. Devo esperar ver um erro de HTTP 403 no seu browser.

## <a name="use-swagger-api-metadata-and-ui"></a>Utilizar a Swagger API metadados e IU

Suporte para [Swagger](http://swagger.io/) 2.0 metadados API incorporado no Azure aplicação de serviço. Cada aplicação API pode especificar um ponto final de URL devolve metadados para a API no formato Swagger JSON. Os metadados devolvido a partir desse ponto final podem ser utilizados para gerar o código do cliente.

Um projeto de ASP.NET Web API dinamicamente pode gerar Swagger metadados utilizando o pacote de NuGet [Swashbuckle](https://www.nuget.org/packages/Swashbuckle) . Já está instalado o pacote de Swashbuckle NuGet nos projectos ToDoListDataAPI e ToDoListAPI que transferiu.

Nesta secção do tutorial, veja os metadados Swagger 2.0 gerado e, em seguida, tente um ensaio IU é baseada os metadados Swagger.

1. Defina o projeto ToDoListDataAPI (**não** o projeto ToDoListAPI) como o projeto de arranque.

    ![Definir ToDoDataAPI como projeto de arranque](./media/app-service-api-dotnet-get-started/startupproject.png)

2. Prima F5 ou clique em **Depurar > Iniciar depuração** para executar o projecto no modo de depuração.

    No browser é aberta e apresenta a página de erro de HTTP 403.

3. Na barra de endereço do browser, adicione `swagger/docs/v1` para o fim da linha e, em seguida, prima ENTER. (O URL é `http://localhost:45914/swagger/docs/v1`.)

    Este é o URL predefinido utilizado pelo Swashbuckle para devolver metadados Swagger 2.0 JSON para a API.

    Se estiver a utilizar o Internet Explorer, o browser pede-lhe para transferir um ficheiro de *v1.json* .

    ![Transferir JSON metadados no IE](./media/app-service-api-dotnet-get-started/iev1json.png)

    Se estiver a utilizar o Chrome, Firefox ou margem, o browser apresenta a JSON na janela do browser. Diferentes browsers lidar JSON de forma diferente e a janela do browser poderá não ter um aspecto exatamente como no exemplo.

    ![Metadados JSON no Chrome](./media/app-service-api-dotnet-get-started/chromev1json.png)

    O exemplo seguinte mostra a primeira secção dos metadados Swagger para a API, com a definição para o método de introdução. Os metadados é o que unidades IU Swagger que utilizar nos seguintes passos e utilize numa secção posterior do tutorial automaticamente gerar o código do cliente.

        {
          "swagger": "2.0",
          "info": {
            "version": "v1",
            "title": "ToDoListDataAPI"
          },
          "host": "localhost:45914",
          "schemes": [ "http" ],
          "paths": {
            "/api/ToDoList": {
              "get": {
                "tags": [ "ToDoList" ],
                "operationId": "ToDoList_GetByOwner",
                "consumes": [ ],
                "produces": [ "application/json", "text/json", "application/xml", "text/xml" ],
                "parameters": [
                  {
                    "name": "owner",
                    "in": "query",
                    "required": true,
                    "type": "string"
                  }
                ],
                "responses": {
                  "200": {
                    "description": "OK",
                    "schema": {
                      "type": "array",
                      "items": { "$ref": "#/definitions/ToDoItem" }
                    }
                  }
                },
                "deprecated": false
              },

4. Feche o browser e parar o Visual Studio depuração.

5. No projeto ToDoListDataAPI no **Explorador de solução**, abra o ficheiro de *App_Start\SwaggerConfig.cs* , em seguida, desloque para baixo para a linha 174 e remova os comentários o código seguinte.

        /*
            })
        .EnableSwaggerUi(c =>
            {
        */

    O ficheiro *SwaggerConfig.cs* é criado quando instalar o pacote de Swashbuckle num projeto. O ficheiro fornece várias maneiras para configurar Swashbuckle.

    O código que tenha uncommented permite que a IU Swagger que utilizar nos passos seguintes. Quando cria um projeto de Web API utilizando o modelo de projeto de aplicação API, este código é colocada como comentário por predefinição como medida de segurança.

6. Execute o projecto novamente.

7. Na barra de endereço do browser, adicione `swagger` para o fim da linha e, em seguida, prima ENTER. (O URL é `http://localhost:45914/swagger`.)

8. Quando a página de IU Swagger for apresentada, clique em **fazer** para ver os métodos de disponíveis.

    ![Métodos de disponíveis swagger IU](./media/app-service-api-dotnet-get-started/methods.png)

9. Clique no botão **obter** primeira na lista.

10. Na secção de **parâmetros** , introduza um asterisco como o valor da `owner` parâmetro e, em seguida, clique em **experimentar saída**.

    Quando adiciona autenticação nos tutoriais posteriores, a camada irá fornecer o ID de utilizador real na camada de dados. Agora, todas as tarefas terá asterisco como o seu ID de proprietário enquanto a aplicação é executada sem autenticação ativada.

    ![Swagger IU experimentar](./media/app-service-api-dotnet-get-started/gettryitout1.png)

    IU de Swagger chamadas introdução fazer método e apresenta o código de resposta e JSON resultados.

    ![Swagger IU experimentar resultados](./media/app-service-api-dotnet-get-started/gettryitout.png)

11. Clique em **Publicar**e, em seguida, clique na caixa em **Esquema do modelo**.

    Clicar em esquema de modelo preenche automaticamente a caixa de entrada, onde pode especificar o valor do parâmetro para o método de mensagem. (Se isto não funcionar no Internet Explorer, utilize um browser diferente ou introduza o valor do parâmetro manualmente no próximo passo.)  

    ![Swagger IU experimentar mensagem](./media/app-service-api-dotnet-get-started/post.png)

12. Alterar JSON na `todo` parâmetro de entrada caixa para que o aspeto do mesmo o exemplo seguinte ou substituir o seu próprio texto de descrição:

        {
          "ID": 2,
          "Description": "buy the dog a toy",
          "Owner": "*"
        }

13. Clique em **experimentar saída**.

    API fazer devolve um código de resposta de HTTP 204 que indica o sucesso.

14. Clique no botão **começar** primeiro e, em seguida, nessa secção da página, clique no botão **experimentar saída** .

    A resposta do método de obter inclui agora o novo item.

15. Opcional: Tente também o local, eliminar e obter por métodos de ID.

16. Feche o browser e parar o Visual Studio depuração.

Swashbuckle funciona com qualquer projecto ASP.NET Web API. Se pretender adicionar Swagger geração de metadados a um projeto existente, tal instale o pacote de Swashbuckle.

>[AZURE.NOTE] Metadados swagger incluem um ID exclusivo para cada operação de API. Por predefinição, Swashbuckle pode gerar duplicada operação Swagger IDs para os métodos de controlador da Web API. Isto acontece se o controlador de tem sobrecarregado métodos de HTTP, tais como `Get()` e `Get(id)`. Para obter informações sobre como gerir overloads, consulte o artigo [definições de API gerados pelo Swashbuckle personalizar](app-service-api-dotnet-swashbuckle-customize.md). Se criar um projeto de Web API no Visual Studio utilizando o modelo de aplicação de API do Azure, código que gera operação exclusiva IDs é automaticamente adicionado ao ficheiro *SwaggerConfig.cs* .  

## <a id="createapiapp"></a>Criar uma aplicação de API no Azure e implementar código

Nesta secção, utilize as ferramentas Azure que sejam integradas no Assistente do Visual Studio **Publicar Web** para criar uma nova aplicação API no Azure. Em seguida, implementar o projeto ToDoListDataAPI para a nova aplicação API e chamar a API ao executar a IU Swagger.

1. No **Explorador de soluções**, com o botão direito do projecto ToDoListDataAPI e, em seguida, clique em **Publicar**.

    ![Clique em publicar no Visual Studio](./media/app-service-api-dotnet-get-started/pubinmenu.png)

2.  No passo do assistente **Publicar Web** **perfil** , clique em **Serviço de aplicação do Microsoft Azure**.

    ![Clique em Azure publicar a aplicação de serviço na Web](./media/app-service-api-dotnet-get-started/selectappservice.png)

3. Inicie sessão na sua conta Azure se ainda não o tiver feito ou atualizar as suas credenciais, se estiver a expiraram.

4. Na caixa de diálogo serviço de aplicação, selecione o Azure **subscrição** que pretende utilizar e, em seguida, clique em **Novo**.

    ![Clique em novo na caixa de diálogo da aplicação de serviço](./media/app-service-api-dotnet-get-started/clicknew.png)

    O separador de **alojamento** da caixa de diálogo **Criar aplicação de serviço de** aparece.

    Uma vez que está a implementar um projeto de Web API tem Swashbuckle instalado, o Visual Studio assume que pretende criar uma aplicação do API. Isto é indicado pelo título do **Nome da aplicação API** e pelo facto da **Alterar tipo de** lista pendente-está definida para **API aplicação**.

    ![Tipo de aplicação na caixa de diálogo da aplicação de serviço](./media/app-service-api-dotnet-get-started/apptype.png)

5. Introduza um **Nome da aplicação API** que seja exclusivo no domínio de *azurewebsites.net* . Pode aceitar o nome predefinido que propõe Visual Studio.

    Se introduzir um nome que outra pessoa já tenha utilizado, verá um ponto de exclamação vermelho para a direita.

    O URL da aplicação API será `{API app name}.azurewebsites.net`.

6. No menu pendente **Grupo de recursos** , clique em **Novo**e, em seguida, introduza "ToDoListGroup" ou outro nome se preferir.

    Um grupo de recursos é um conjunto de recursos Azure, tais como API aplicações, bases de dados, VMs e assim sucessivamente. Para este tutorial, é melhor criar um novo grupo de recursos, porque é que o torna mais fácil eliminar num único passo todos os recursos Azure criados para o tutorial.

    Esta caixa permite-lhe selecionar um [grupo de recursos](../azure-resource-manager/resource-group-overview.md) de existente ou crie um novo ao escrever um nome diferente a partir de qualquer grupo de recursos existente na sua subscrição.

7. Clique no botão de **Novo** junto ao **Plano de serviço de aplicação** pendente.

    A captura de ecrã mostra os valores de exemplo para o **Nome da aplicação API**, **subscrição**e **Grupo de recursos** – os valores serão diferentes.

    ![Criar a caixa de diálogo serviço de aplicação](./media/app-service-api-dotnet-get-started/createas.png)

    Os passos seguintes cria um plano de serviço de aplicação para o novo grupo de recursos. Um plano de serviço de aplicação Especifica os recursos de cluster a sua aplicação API é executada no. Por exemplo, se escolher a camada livre, a aplicação de API é executada no VMs partilhados, enquanto para alguns camadas pagas é executado no VMs dedicadas. Para obter informações sobre os planos de aplicação de serviço, consulte o artigo [Descrição geral de planos do serviço de aplicação](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

8. Na caixa de diálogo **Configurar o plano de serviço de aplicação** , introduza "ToDoListPlan" ou outro nome se preferir.

9. Na lista pendente de **localização** , escolha a localização que esteja mais próxima para si.

    Esta definição especifica que Centro de dados Azure a sua aplicação será executada em. Escolha uma localização perto que para minimizar a [latência](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090).

10. No menu pendente **tamanho** , clique em **grátis**.

    Para este tutorial, a camada comparar gratuita irá fornecer o desempenho suficiente.

11. Na caixa de diálogo **Configurar o plano de serviço de aplicação** , clique em **OK**.

    ![Clique em OK em Configurar plano de serviço de aplicação](./media/app-service-api-dotnet-get-started/configasp.png)

12. Na caixa de diálogo **Criar aplicação de serviço** , clique em **Criar**.

    ![Clique em criar na caixa de diálogo Criar aplicação de serviço](./media/app-service-api-dotnet-get-started/clickcreate.png)

    Visual Studio cria a aplicação de API e um perfil de publicar tem todas as definições necessárias para a aplicação de API. Em seguida, é aberto o Assistente de **Web publicar** , que irá utilizar para implementar o projeto.

    O Assistente de **Publicar Web** abre no separador **ligação** (mostrado abaixo).

    No separador **ligação** , as definições de **servidor** e o **nome do Site** , aponte para a sua aplicação API. O **nome de utilizador** e **palavra-passe** são credenciais de implementação que cria Azure por si. Após a implementação, Visual Studio, é aberta num browser para o **URL de destino** (que é o objetivo apenas para o **URL de destino**).  

13. Clique em **seguinte**.

    ![Clique em seguinte no separador ligação da publicar Web](./media/app-service-api-dotnet-get-started/connnext.png)

    O separador seguinte é o separador de **Definições** (mostrado abaixo). Aqui pode alterar o separador de configuração da compilação para implementar uma compilação de depuração para [depuração remota](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug). No separador também oferece várias **Opções de publicar de ficheiros**:

    * Remover ficheiros adicionais ao destino
    * Pré-compilar durante a publicação
    * Excluir ficheiros a partir da pasta App_Data

    Para este tutorial não precisa de um dos seguintes procedimentos. Para obter explicações detalhadas sobre o que fazer, consulte o artigo [como: implementar um Web utilizando com um clique de publicação do projecto no Visual Studio](https://msdn.microsoft.com/library/dd465337.aspx).

14. Clique em **seguinte**.

    ![Clique em seguinte no separador Definições da publicar Web](./media/app-service-api-dotnet-get-started/settingsnext.png)

    Segue-se o separador **pré-visualização** (mostrado abaixo), que oferece-lhe uma oportunidade para ver quais os ficheiros passar sejam copiados a partir do seu projeto para a aplicação de API. Quando está a implementar um projeto para uma aplicação de API já implementado anterior, são copiados apenas os ficheiros alterados. Se pretender ver uma lista do que será copiado, pode clicar no botão **Começar a pré-visualização** .

15. Clique em **Publicar**.

    ![Clique em publicar no separador pré-visualização de publicar Web](./media/app-service-api-dotnet-get-started/clickpublish.png)

    Visual Studio implementa o projeto ToDoListDataAPI a nova aplicação API. Na janela de **saída** os registos de implementação com êxito e uma página "criado com êxito" aparece numa janela do browser aberta para o URL da aplicação API.

    ![Implementação com êxito de janela de saída](./media/app-service-api-dotnet-get-started/deploymentoutput.png)

    ![Nova página de aplicação criada com êxito API](./media/app-service-api-dotnet-get-started/appcreated.png)

16. Adicionar "swagger" para o URL na barra de endereço do browser e, em seguida, prima Enter. (O URL é `http://{apiappname}.azurewebsites.net/swagger`.)

    O browser apresenta a mesma UI Swagger que viu anteriormente, mas agora estiver em execução na nuvem. Experimentar o método Get e vir está novamente para os itens de acções a fazer 2 predefinido. As alterações que efetuou anteriores foram guardadas na memória no computador local.

17. Abra o [Azure portal](https://portal.azure.com/).

    Portal do Azure é uma interface de web para a gestão de recursos Azure, tais como API aplicações.

18. Clique em **mais serviços > Serviços de aplicação**.

    ![Procure os serviços de aplicação](./media/app-service-api-dotnet-get-started/browseas.png)

19. Na pá **Serviços de aplicação** , localize e clique em sua nova aplicação API. (No portal do Azure, windows que é aberto para a direita são chamados *pás*.)

    ![Pá de serviços de aplicação](./media/app-service-api-dotnet-get-started/choosenewapiappinportal.png)

    Abra o duas pás. Uma pá tem uma descrição geral da aplicação API e uma contém uma longa lista de definições que pode ver e alterar.

20. Na pá **Definições** , localize a secção **API** e **API**números de página.

    ![Definição de API no pá definições](./media/app-service-api-dotnet-get-started/apidefinsettings.png)

    A **Definição de API** pá permite-lhe especificar o URL que devolve Swagger 2.0 metadados no formato JSON. Quando o Visual Studio cria a aplicação de API, define o URL de definição de API para o valor predefinido para gerados pelo Swashbuckle metadados que viu anterior, que é a aplicação de API estiverem base URL sinal de adição `/swagger/docs/v1`.

    ![URL de definição de API](./media/app-service-api-dotnet-get-started/apidefurl.png)

    Quando seleciona uma aplicação de API para gerar o código do cliente para o mesmo, Visual Studio obtém os metadados este URL.

## <a id="codegen"></a>Gerar código do cliente para a camada de dados

Uma das vantagens da integrar Swagger Azure API aplicações é geração de código automática. Cliente gerada classes tornam mais fácil de escrever código que liga uma aplicação de API.

O projeto ToDoListAPI já tem o código do cliente gerado, mas nos passos seguintes irá eliminá-la e gerá-lo para ver como o fazer a geração de código.

1. No Visual Studio **Solução Explorer**, no ToDoListAPI project, elimine a pasta *ToDoListDataAPI* . **Atenção: Elimine apenas a pasta, não o projeto ToDoListDataAPI.**

    ![Eliminar o código do cliente gerada](./media/app-service-api-dotnet-get-started/deletecodegen.png)

    Esta pasta foi criada utilizando o processo de geração de código está prestes a seguir.

2. Com o botão direito do projecto ToDoListAPI e, em seguida, clique em **Adicionar > REST API cliente**.

    ![Adicionar cliente REST API no Visual Studio](./media/app-service-api-dotnet-get-started/codegenmenu.png)

3. Na caixa de diálogo **Adicionar de cliente API REST** , clique em **Swagger URL**e, em seguida, clique em **Seleccionar elementos Azure**.

    ![Selecione elementos Azure](./media/app-service-api-dotnet-get-started/codegenbrowse.png)

4. Na caixa de diálogo **Serviço de aplicação** , expanda o grupo de recursos que está a utilizar para este tutorial e selecione a sua aplicação API e, em seguida, clique em **OK**.

    ![Selecione a aplicação de API para geração de código](./media/app-service-api-dotnet-get-started/codegenselect.png)

    Note que quando regressar à caixa de diálogo **Adicionar de cliente API REST** , a caixa de texto tem sido preenchida com a definição de API valor do URL que viu anteriormente no portal.

    ![URL de definição de API](./media/app-service-api-dotnet-get-started/codegenurlplugged.png)

    >[AZURE.TIP] É uma forma alternativa para obter metadados para geração de código introduzir o URL diretamente em vez de percorrer a caixa de diálogo Procurar. Ou, se pretende gerar código do cliente antes de implementar para Azure, pode executar o project Web API localmente, vá para o URL que fornece o ficheiro Swagger JSON, guarde o ficheiro, utilize a opção **selecionar um ficheiro de metadados Swagger existente** .

5. Na caixa de diálogo **Adicionar de cliente API REST** , clique em **OK**.

    Visual Studio cria uma pasta denominada após a aplicação de API e gera classes de cliente.

    ![Código de ficheiros para o cliente gerado](./media/app-service-api-dotnet-get-started/codegenfiles.png)

6. No ToDoListAPI project, abra *Controllers\ToDoListController.cs* para ver o código na linha 40 que liga a API utilizando o cliente gerado.

    O fragmento que se segue mostra como o código iniciar instância o objeto de cliente e chama o método de introdução.

        private static ToDoListDataAPI NewDataAPIClient()
        {
            var client = new ToDoListDataAPI(new Uri(ConfigurationManager.AppSettings["toDoListDataAPIURL"]));
            return client;
        }

        public async Task<IEnumerable<ToDoItem>> Get()
        {
            using (var client = NewDataAPIClient())
            {
                var results = await client.ToDoList.GetByOwnerAsync(owner);
                return results.Select(m => new ToDoItem
                {
                    Description = m.Description,
                    ID = (int)m.ID,
                    Owner = m.Owner
                });
            }
        }

    O parâmetro construtor obtém o URL de ponto final a partir do `toDoListDataAPIURL` definição de aplicação. No ficheiro Web. config, esse valor está definido para o local Express URL do IIS do projeto API para que pode executar a aplicação localmente. Se omitir o parâmetro construtor, o ponto final predefinido é o URL que gerou o código de.

7. Será gerada sua aula cliente com um nome diferente com base no seu nome de aplicação API; Altere o código na *Controllers\ToDoListController.cs* para que o nome do tipo corresponde ao que foi criado no projeto. Por exemplo, se chamar seu ToDoListDataAPI071316 de aplicação API, é alterar este código:

        private static ToDoListDataAPI NewDataAPIClient()
        {
            var client = new ToDoListDataAPI(new Uri(ConfigurationManager.AppSettings["toDoListDataAPIURL"]));

a esta:

        private static ToDoListDataAPI071316 NewDataAPIClient()
        {
            var client = new ToDoListDataAPI071316(new Uri(ConfigurationManager.AppSettings["toDoListDataAPIURL"]));


## <a name="create-an-api-app-to-host-the-middle-tier"></a>Criar uma aplicação de API para a camada do anfitrião

Anteriores que [criou a aplicação de API de níveis de dados e implementado código à mesma](#createapiapp).  Agora, siga o mesmo procedimento para a aplicação de API camada.

1. No **Explorador de soluções**, botão direito do rato a camada ToDoListAPI project (não a dados camada ToDoListDataAPI) e, em seguida, clique em **Publicar**.

    ![Clique em publicar no Visual Studio](./media/app-service-api-dotnet-get-started/pubinmenu2.png)

2.  No separador **perfil** do assistente **Publicar Web** , clique em **Serviço de aplicação do Microsoft Azure**.

3. Na caixa de diálogo **Serviço de aplicação** , clique em **Novo**.

4. No separador da caixa de diálogo **Criar aplicação de serviço de** **alojamento** , aceitar o **Nome da aplicação API** predefinido ou introduza um nome que seja exclusivo no domínio de *azurewebsites.net* .

5. Selecione o Azure **subscrição** tiver sido a utilizar.

6. No menu pendente **Grupo de recursos** , selecione o grupo de recursos mesmo que criou anteriormente.

7. No menu pendente **Plano de serviço de aplicação** , selecione o mesmo plano que criou anteriormente. Predefinição será por esse valor.

8. Clique em **Criar**.

    Visual Studio cria a aplicação de API, cria um perfil de publicar para a mesma e apresenta o **ligação** , passo do assistente **Publicar Web** .

9.  No passo do assistente **Publicar Web** **ligação** , clique em **Publicar**.

    Visual Studio implementa o projeto ToDoListAPI a nova aplicação API e é aberta num browser para o URL da aplicação API. É apresentada a página "criada com êxito".

## <a name="configure-the-middle-tier-to-call-the-data-tier"></a>Configurar a camada para ligar a camada de dados

Se chamado a aplicação de camada API agora, seria tentar ligar para a camada de dados utilizando o URL de localhost que ainda se encontra no ficheiro da Web. config. Nesta secção introduza o URL de aplicação de API de níveis de dados para uma definição de ambiente na aplicação de API camada. Quando o código de na aplicação camada API obtém a definição de URL da camada de dados, a definição do ambiente irá substituir o que é o ficheiro da Web. config.

1. Aceda ao [portal do Azure](https://portal.azure.com/)e, em seguida, navegue para a **Aplicação de API** pá para a aplicação de API que criou para alojar o projeto TodoListAPI (camada).

2. Na pá de **Definições** da aplicação API, clique em **definições da aplicação**.

3. Em pá de **Definições da aplicação** a aplicação de API, desloque para baixo até à secção **definições de aplicação** e adicione a seguinte chave e valor. O valor será o URL da aplicação API primeiro publicados neste tutorial.

  	| **Chave** | toDoListDataAPIURL |
  	|---|---|
  	| **Valor** | nome da https://{your dados camada API aplicação} .azurewebsites .net |
  	| **Exemplo** | https://todolistdataapi.azurewebsites.NET |

4. Clique em **Guardar**.

    ![Clique em Guardar para as definições da aplicação](./media/app-service-api-dotnet-get-started/asinportal.png)

    Quando o código é executado no Azure, este valor agora irá substituir o URL de localhost que está no ficheiro Web. config.

## <a name="test"></a>Teste

1. Na janela do browser, navegue para o URL da aplicação de API camada nova que acabou de criar para ToDoListAPI. Pode obter aí ao clicar em URL no pá principal da aplicação de API no portal.

2. Adicionar "swagger" para o URL na barra de endereço do browser e, em seguida, prima Enter. (O URL é `http://{apiappname}.azurewebsites.net/swagger`.)

    O browser apresenta a mesma UI Swagger que viu anterior para ToDoListDataAPI, mas agora `owner` não é um campo necessário para a operação de obter, uma vez que a aplicação de camada API enviar esse valor para a aplicação de API de níveis de dados por si. (Quando fizer os tutoriais de autenticação, a camada irá enviar IDs de utilizador real o `owner` parâmetro; para agora-é difícil codificação um asterisco.)

3. Experimente o método de introdução e outros métodos para validar a aplicação de API camada com êxito está a chamar a aplicação de API de níveis de dados.

    ![Método de introdução da IU swagger](./media/app-service-api-dotnet-get-started/midtierget.png)

## <a name="troubleshooting"></a>Resolução de problemas

Caso se depare um problema à medida que avança através deste tutorial aqui estão algumas ideias de resolução de problemas:

* Certifique-se de que está a utilizar a versão mais recente do [Azure SDK para .NET](http://go.microsoft.com/fwlink/?linkid=518003).

* Dois dos nomes de projeto são semelhante (ToDoListAPI, ToDoListDataAPI). Se as coisas não parecem, tal como descrito nas instruções quando está a trabalhar com um projeto, certifique-se de que tenha aberto o projeto correto.

* Se estiver numa rede de empresa e está a tentar implementar a aplicação de serviço de Azure através de uma firewall, certifique-se de que as portas 443 e 8172 estão abertas para implementar Web. Se não conseguir abrir esses portas, pode utilizar outros métodos de implementação.  Consulte o artigo [Implementar a aplicação Azure de aplicação de serviço](../app-service-web/web-sites-deploy.md).

* "Rota nomes têm de ser exclusivos" erros – pode obter estas se acidentalmente implementar o projeto errado para uma aplicação de API e, em seguida, mais tarde a implementar o correto para o mesmo. Para corrigir este problema, o novo lançamento o projeto correto para a aplicação de API e no separador **Definições** do assistente **Publicar Web** selecione **Remover ficheiros adicionais no destino**.

Depois de ter a sua aplicação do ASP.NET API em execução no serviço de aplicação do Azure, poderá querer saber mais sobre as funcionalidades do Visual Studio que simplificam a resolução de problemas. Para obter informações sobre o registo, depuração remota e muito mais, consulte o artigo [resolução de problemas Azure aplicação de serviço de aplicações no Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md).

## <a name="next-steps"></a>Próximos passos

Visualizou como implementar o projectos Web API existentes em aplicações de API, gerar código do cliente para as aplicações de API e consumir API aplicações a partir dos clientes .NET. O próximo tutorial nesta série mostra como [utilizar CORS consumir API aplicações a partir dos clientes JavaScript](app-service-api-cors-consume-javascript.md).

Para mais informações sobre geração de código do cliente, consulte o artigo o repositório [Azure/AutoRest](https://github.com/azure/autorest) GitHub.com. Para obter ajuda com problemas ao utilizar o cliente gerado, abra um [problema no repositório de AutoRest](https://github.com/azure/autorest/issues).

Se pretender criar novos projectos de aplicação API de raiz, utilize o modelo de **Aplicação de API do Azure** .

![Modelo de aplicação de API no Visual Studio](./media/app-service-api-dotnet-get-started/apiapptemplate.png)

O modelo de **Aplicação de API do Azure** do projeto é equivalente ao escolher o **vazia** ASP.NET 4.5.2 modelo, clicando na caixa de verificação para adicionar suporte para Web API e instalar o pacote de Swashbuckle NuGet. Além disso, o modelo adiciona algumas código de configuração de Swashbuckle concebido para impedir a criação de duplicados Swagger operação IDs. Depois de criar um projeto de API aplicação, pode implemente-o para uma aplicação de API da mesma forma que viu neste tutorial.
