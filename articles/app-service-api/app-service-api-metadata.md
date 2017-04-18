<properties
    pageTitle="Metadados do serviço API aplicações de aplicação para geração de deteção e avaliação de código API | Microsoft Azure"
    description="Saiba como as aplicações de API na aplicação de serviço de Azure utilizar Swagger metadados para facilitar a geração de deteção e avaliação de código API."
    services="app-service\api"
    documentationCenter=".net"
    authors="tdykstra"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-api"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/30/2016"
    ms.author="rachelap"/>

# <a name="app-service-api-apps-metadata-for-api-discovery-and-code-generation"></a>Metadados do serviço API aplicações de aplicação para geração API de deteção e avaliação de código 

Suporte para [Swagger 2.0](http://swagger.io/) API metadados incorporado no iniciador de aplicações de API de serviço. Não tem de utilizar Swagger, mas se utilizá-la, pode tirar partido das funcionalidades de API aplicações que facilitam a deteção e avaliação de consumo.   

## <a name="swagger-endpoint"></a>Swagger ponto final

Pode especificar um ponto final que fornece Swagger 2.0 JSON metadados para uma aplicação de API de uma propriedade da aplicação API. O ponto final pode ser relativamente ao URL da aplicação API base ou um URL absoluto. URLs absolutas podem apontar fora a aplicação de API. 

Muitos clientes descendentes (por exemplo, geração de código do Visual Studio e PowerApps "Adicionar API" fluxo), o URL tem de ser acessível publicamente (não protegido pelo utilizador ou a autenticação do serviço). Isto significa que se estiver a utilizar a autenticação de aplicação de serviço e pretender expor a definição de API a partir da sua própria aplicação, terá de utilizar a opção de autenticação que permite que o tráfego anónimo atingir o seu API. Para mais informações, consulte o artigo [autenticação e autorização para as aplicações de API do serviço de aplicação](app-service-api-authentication.md).

### <a name="portal-blade"></a>Pá Portal

No [portal do Azure](https://portal.azure.com/) o URL de ponto final pode ser visto e alterado no pá a **Definição de API** .

![](./media/app-service-api-metadata/apidefblade.png)

### <a name="azure-resource-manager-property"></a>Propriedade Azure do Gestor de recursos

Também pode configurar o URL de definição de API para uma aplicação de API utilizando [Recurso Explorer](https://resources.azure.com/) ou [Gestor de recursos do Azure modelos](../resource-group-authoring-templates.md) nas ferramentas de linha de comandos como [PowerShell do Azure](../powershell-install-configure.md) e o [Clip do Azure](../xplat-cli-install.md). 

No **Explorador do recurso**, aceda a **subscrições > {a sua subscrição} > resourceGroups > {seu grupo de recursos} > fornecedores > Microsoft.Web > sites > {site} > config > web**, e verá a `apiDefinition` propriedade:

        "apiDefinition": {
          "url": "https://contactslistapi.azurewebsites.net/swagger/docs/v1"
        }

Para obter um exemplo de um modelo de Gestor de recursos do Azure que define o `apiDefinition` propriedade, abra o [ficheiro de azuredeploy.json na aplicação de exemplo de lista de itens pendentes](https://github.com/azure-samples/app-service-api-dotnet-todo-list/blob/master/azuredeploy.json). Localize a secção do modelo com um aspeto semelhante a amostra JSON mostrada acima.

### <a name="default-value"></a>Valor predefinido

Quando utilizar o Visual Studio para criar uma aplicação do API, o ponto final de definição de API automaticamente está definido para o URL da aplicação API sinal de adição de base `/swagger/docs/v1`. Este é o URL predefinido que o pacote de [Swashbuckle](https://www.nuget.org/packages/Swashbuckle) NuGet utiliza para servir gerados dinamicamente Swagger metadados de um projeto de ASP.NET Web API. 

## <a name="code-generation"></a>Geração de código

Uma das vantagens de integrar Swagger Azure API aplicações é geração de código automática. Cliente gerada classes tornam mais fácil de escrever código que liga uma aplicação de API.

Pode gerar código do cliente para uma aplicação de API utilizando o Visual Studio ou da linha de comandos. Para obter informações sobre como gerar classes de cliente no Visual Studio para um projeto de ASP.NET Web API, consulte o artigo [Introdução às aplicações API e ASP.NET](app-service-api-dotnet-get-started.md#codegen). Para obter informações sobre como fazê-lo na linha de comandos para todos os idiomas suportados, consulte o artigo o ficheiro Leia-me do repositório [Azure/autorest](https://github.com/azure/autorest) no GitHub.com.
 
## <a name="next-steps"></a>Próximos passos

Para obter um tutorial passo a passo que o orienta-a criar, implementar e consumir uma aplicação de API, consulte o artigo [começar a trabalhar com aplicações de API na aplicação de serviço de Azure](app-service-api-dotnet-get-started.md).

Se utiliza o Azure API gestão com aplicações API, pode utilizar metadados Swagger para importar o seu API para gestão de API. Para mais informações, consulte o artigo [como importar a definição de uma API com operações na gestão de API do Azure](../api-management/api-management-howto-import-api.md). 
