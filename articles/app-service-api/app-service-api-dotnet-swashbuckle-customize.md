
<properties 
    pageTitle="Personalizar as definições de API gerados pelo Swashbuckle" 
    description="Saiba como personalizar as definições de Swagger API que são geradas por Swashbuckle para uma aplicação do API na aplicação de serviço de Azure." 
    services="app-service\api" 
    documentationCenter=".net" 
    authors="bradygaster" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service-api" 
    ms.workload="web" 
    ms.tgt_pltfrm="dotnet" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/29/2016" 
    ms.author="rachelap"/>

# <a name="customize-swashbuckle-generated-api-definitions"></a>Personalizar as definições de API gerados pelo Swashbuckle 

## <a name="overview"></a>Descrição geral

Este artigo explica como personalizar Swashbuckle para processar cenários comuns onde poderá pretender alterar o comportamento predefinido:

* Swashbuckle gera identificadores de operação duplicados para overloads dos métodos de controlador
* Swashbuckle assume que a resposta válida apenas a partir de um método é 200 HTTP (OK) 
 
## <a name="customize-operation-identifier-generation"></a>Personalizar geração da operação identificador

Swashbuckle gera identificadores de operação Swagger ao concatenar nome do controlador e o nome do método. Este padrão cria um problema, quando tiver várias overloads de um método: Swashbuckle gera ids duplicados operação, que é JSON Swagger inválido.

Por exemplo, o seguinte código de controlador faz com que Swashbuckle gerar ids de operação Contact_Get três.

![](./media/app-service-api-dotnet-swashbuckle-customize/multiplegetsincode.png)

![](./media/app-service-api-dotnet-swashbuckle-customize/multiplegetsinjson.png)

Pode resolver o problema manualmente atribuindo nomes únicos, tal como o seguinte para este exemplo os métodos:

* Obter
* GetById
* GetPage

A alternativa é alargar Swashbuckle para torná-lo a gerar automaticamente os ids exclusivos operação.

Os seguintes passos mostram como personalizar Swashbuckle utilizando o ficheiro de *SwaggerConfig.cs* que vem incluído no projeto pelo modelo de projeto de pré-visualização de aplicações de API do Visual Studio.  Também pode personalizar Swashbuckle num projeto Web API que configurar para implementação como uma aplicação de API.

1. Criar um personalizado `IOperationFilter` pós-implementação 

    O `IOperationFilter` interface fornece um ponto de expansão para utilizadores de Swashbuckle que pretende personalizar vários aspetos do processo de metadados Swagger. O código seguinte demonstra um método de alterar o comportamento de geração da id de operação. O código acrescenta os nomes dos parâmetros para o nome do id de operação.  

        using Swashbuckle.Swagger;
        using System.Web.Http.Description;
        
        namespace ContactsList
        {
            public class MultipleOperationsWithSameVerbFilter : IOperationFilter
            {
                public void Apply(
                    Operation operation,
                    SchemaRegistry schemaRegistry,
                    ApiDescription apiDescription)
                {
                    if (operation.parameters != null)
                    {
                        operation.operationId += "By";
                        foreach (var parm in operation.parameters)
                        {
                            operation.operationId += string.Format("{0}",parm.name);
                        }
                    }
                }
            }
        }

2. Em ficheiro *App_Start\SwaggerConfig.cs* , ligue o `OperationFilter` método para fazer com que Swashbuckle utilizar os novos `IOperationFilter` pós-implementação.

        c.OperationFilter<MultipleOperationsWithSameVerbFilter>();

    ![](./media/app-service-api-dotnet-swashbuckle-customize/usefilter.png)

    O ficheiro *SwaggerConfig.cs* cair pelo pacote Swashbuckle NuGet contém muitos exemplos de saída comentadas de pontos de expansão. Os comentários adicionais não são apresentados aqui. 

    Depois de efetuar esta alteração, seu `IOperationFilter` pós-implementação é utilizado e faz com que ids exclusivos operação a ser gerado.
 
    ![](./media/app-service-api-dotnet-swashbuckle-customize/uniqueids.png)

<a id="multiple-response-codes" name="multiple-response-codes"></a>
    
## <a name="allow-response-codes-other-than-200"></a>Permitir que os códigos de resposta que não seja 200

Por predefinição, o Swashbuckle assume que uma resposta de HTTP 200 (OK) é a resposta legítimas *apenas* a partir de um método de Web API. Em alguns casos, poderá querer devolver outros códigos de resposta sem a causar o cliente para elevar uma exceção.  Por exemplo, o seguinte código de Web API demonstra um cenário em que iria pretender que o cliente para aceitar uma 200 ou um 404 como respostas válidas.

    [ResponseType(typeof(Contact))]
    public HttpResponseMessage Get(int id)
    {
        var contacts = GetContacts();

        var requestedContact = contacts.FirstOrDefault(x => x.Id == id);

        if (requestedContact == null)
        {
            return Request.CreateResponse(HttpStatusCode.NotFound);
        }
        else
        {
            return Request.CreateResponse<Contact>(HttpStatusCode.OK, requestedContact);
        }
    }

Neste cenário, o Swagger que Swashbuckle gera por predefinição Especifica código de estado HTTP legítimo de apenas uma, HTTP 200.

![](./media/app-service-api-dotnet-swashbuckle-customize/http-200-output-only.png)

Uma vez que o Visual Studio utiliza a definição de Swagger API para gerar o código do cliente, que cria código do cliente que eleva uma exceção para qualquer resposta que não seja uma 200 HTTP. O código abaixo é a partir de um cliente c# gerado para este método da Web API de exemplo.

    if (statusCode != HttpStatusCode.OK)
    {
        HttpOperationException<object> ex = new HttpOperationException<object>();
        ex.Request = httpRequest;
        ex.Response = httpResponse;
        ex.Body = null;
        if (shouldTrace)
        {
            ServiceClientTracing.Error(invocationId, ex);
        }
        throw ex;
    } 

Swashbuckle disponibiliza duas formas de personalizar a lista de códigos de resposta HTTP esperados gerado pelo, utilizando a opção comentários XML ou o `SwaggerResponse` atributo. O atributo é mais fácil, mas só está disponível no Swashbuckle 5.1.5 ou posterior. O modelo de novo projeto de pré-visualização de aplicações API no Visual Studio 2013 inclui Swashbuckle versão 5.0.0, pelo que se tenha utilizado o modelo e não pretende atualizar Swashbuckle, a única opção é utilizar XML comentários. 

### <a name="customize-expected-response-codes-using-xml-comments"></a>Personalizar os códigos de resposta esperado utilizando a opção comentários XML

Utilize este método para especificar códigos de resposta, se a sua versão de Swashbuckle for anterior ao 5.1.5.

1. Em primeiro lugar, adicione comentários de documentação XML sobre os métodos que pretende especificar HTTP códigos de resposta para. Tomar a amostra Web API ação apresentada acima e aplicar a documentação de XML ao mesmo resultaria código semelhante ao exemplo seguinte. 

        /// <summary>
        /// Returns the specified contact.
        /// </summary>
        /// <param name="id">The ID of the contact.</param>
        /// <returns>A contact record with an HTTP 200, or null with an HTTP 404.</returns>
        /// <response code="200">OK</response>
        /// <response code="404">Not Found</response>
        [ResponseType(typeof(Contact))]
        public HttpResponseMessage Get(int id)
        {
            var contacts = GetContacts();
        
            var requestedContact = contacts.FirstOrDefault(x => x.Id == id);
        
            if (requestedContact == null)
            {
                return Request.CreateResponse(HttpStatusCode.NotFound);
            }
            else
            {
                return Request.CreateResponse<Contact>(HttpStatusCode.OK, requestedContact);
            }
        }

1. Adicione as instruções no ficheiro *SwaggerConfig.cs* para direcionar Swashbuckle para tornar a utilização de XML ficheiro de documentação.

    * Abra *SwaggerConfig.cs* e crie um método na classe *SwaggerConfig* para especificar o caminho para o ficheiro XML de documentação. 

            private static string GetXmlCommentsPath()
            {
                return string.Format(@"{0}\XmlComments.xml", 
                    System.AppDomain.CurrentDomain.BaseDirectory);
            }

    * Desloque-se para baixo no ficheiro *SwaggerConfig.cs* até ver a linha de saída comentadas de código que se assemelhe a captura abaixo de ecrã. 

        ![](./media/app-service-api-dotnet-swashbuckle-customize/xml-comments-commented-out.png)
    
    * Remova os comentários na linha para ativar os comentários XML processamento durante a geração Swagger. 
    
        ![](./media/app-service-api-dotnet-swashbuckle-customize/xml-comments-uncommented.png)
    
1. Para gerar o ficheiro de documentação XML, vá para propriedades do projeto e ativar o ficheiro de documentação de XML, conforme apresentado na captura de ecrã abaixo. 

    ![](./media/app-service-api-dotnet-swashbuckle-customize/enable-xml-documentation-file.png) 

Depois de executar estes passos, o JSON Swagger gerado pelo Swashbuckle vão refletir as os códigos de resposta HTTP que especificou no comentários em XML. A captura de ecrã abaixo demonstra este carga de útil JSON nova. 

![](./media/app-service-api-dotnet-swashbuckle-customize/swagger-multiple-responses.png)

Quando utilizar o Visual Studio para gerar o código do cliente para o seu REST API, o código c# aceita HTTP OK tanto não encontrado códigos de estado sem aumentar uma exceção, permitindo que o seu código consome tomar decisões sobre como gerir a devolução de um registo de contacto nulo. 

        if (statusCode != HttpStatusCode.OK && statusCode != HttpStatusCode.NotFound)
        {
            HttpOperationException<object> ex = new HttpOperationException<object>();
            ex.Request = httpRequest;
            ex.Response = httpResponse;
            ex.Body = null;
            if (shouldTrace)
            {
                ServiceClientTracing.Error(invocationId, ex);
            }
                throw ex;
        }

O código para esta demonstração pode ser encontrado neste [repositório GitHub](https://github.com/Azure-Samples/app-service-api-dotnet-swashbuckle-swaggerresponse). Juntamente com a API Web projecto marcado com comentários de documentação XML é um projecto de aplicação de consola que contém um cliente gerado para esta API. 

### <a name="customize-expected-response-codes-using-the-swaggerresponse-attribute"></a>Personalizar os códigos de resposta esperado utilizando o atributo SwaggerResponse

O atributo [SwaggerResponse](https://github.com/domaindrivendev/Swashbuckle/blob/master/Swashbuckle.Core/Swagger/Annotations/SwaggerResponseAttribute.cs) está disponível no Swashbuckle 5.1.5 e versões posteriores. No caso de ter uma versão anterior no seu projeto, é iniciado esta secção explica como pode atualizar o pacote de Swashbuckle NuGet para que possa utilizar este atributo.

1. No **Explorador de soluções**, com o botão direito do projeto da Web API e clique em **Gerir pacotes de NuGet**. 

    ![](./media/app-service-api-dotnet-swashbuckle-customize/manage-nuget-packages.png)

1. Clique no botão de *atualização* junto ao pacote de NuGet *Swashbuckle* . 

    ![](./media/app-service-api-dotnet-swashbuckle-customize/update-nuget-dialog.png)

1. Adicione os atributos de *SwaggerResponse* para os métodos de ação da Web API para o qual pretende especificar códigos de resposta HTTP válidos. 

        [SwaggerResponse(HttpStatusCode.OK)]
        [SwaggerResponse(HttpStatusCode.NotFound)]
        [ResponseType(typeof(Contact))]
        public HttpResponseMessage Get(int id)
        {
            var contacts = GetContacts();

            var requestedContact = contacts.FirstOrDefault(x => x.Id == id);
            if (requestedContact == null)
            {
                return Request.CreateResponse(HttpStatusCode.NotFound);
            }
            else
            {
                return Request.CreateResponse<Contact>(HttpStatusCode.OK, requestedContact);
            }
        }

2. Adicionar um `using` declaração do espaço de nomes do atributo:

        using Swashbuckle.Swagger.Annotations;
        
1. Navegue para o URL de */swagger/docs/v1* do seu projeto e os códigos de resposta HTTP vários estará visíveis na JSON Swagger. 

    ![](./media/app-service-api-dotnet-swashbuckle-customize/multiple-responses-post-attributes.png)

O código para esta demonstração pode ser encontrado neste [repositório GitHub](https://github.com/Azure-Samples/API-Apps-DotNet-Swashbuckle-Customization-MultipleResponseCodes-With-Attributes). Juntamente com a API Web projecto decorado com o atributo *SwaggerResponse* é um projecto de aplicação de consola que contém um cliente gerado para esta API. 

## <a name="next-steps"></a>Próximos passos

Este artigo é apresentado como personalizar a forma como o Swashbuckle gera ids de operação e códigos de resposta válida. Para mais informações, consulte o artigo [Swashbuckle no GitHub](https://github.com/domaindrivendev/Swashbuckle).
 
