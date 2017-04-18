<properties
    pageTitle="Aplicação de serviço de API Apps - o que é alterado | Microsoft Azure"
    description="Saiba quais são as novidades para as aplicações de API na aplicação de serviço de Azure."
    services="app-service\api"
    documentationCenter=".net"
    authors="mohitsriv"
    manager="wpickett"
    editor="tdykstra"/>

<tags
    ms.service="app-service-api"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="rachelap"/>

# <a name="app-service-api-apps---whats-changed"></a>Aplicação de serviço de API Apps - o que é alterado

No evento Connect () de Novembro de 2015, um número dos melhoramentos no serviço de aplicação do Azure foram [anunciada](https://azure.microsoft.com/blog/azure-app-service-updates-november-2015/). Estes melhoramentos incluem subjacentes alterações às aplicações de API para melhor alinhar com Mobile e Web Apps, reduzir a contagem de conceito e melhorar implementação e desempenho de runtime. Iniciar 30 de Novembro de 2015, novas API aplicações que cria utilizando o portal de gestão Azure ou a mais recente ferramentas vão refletir as estas alterações. Este artigo descreve estas alterações, bem como a implementar aplicações existentes para tirar partido das capacidades de.

## <a name="feature-changes"></a>Alterações de funcionalidade
Principais funcionalidades do API aplicações – autenticação, metadados CORS e API – tenham movido diretamente na aplicação de serviço. Com esta alteração, as funcionalidades estão disponíveis nos Web, Mobile e API Apps. Na verdade, todos os três partilham o mesmo tipo de recurso **Microsoft.Web/sites** no Gestor de recursos. O gateway API aplicações já não é necessária ou oferecido com API aplicações. Isto também torna mais fácil de utilizar a gestão de API do Azure desde serão apenas o única API data Management gateway.

![Descrição geral de aplicações API](./media/app-service-api-whats-changed/api-apps-overview.png)

Um princípio estrutura chave com a atualização de aplicações API é permitem-lhe trazer o seu API tal como está, no idioma escolhido.  Se o seu API já for implementado como uma aplicação Web ou a aplicação Mobile, não possui a implementar a sua aplicação para tirar partido das novas funcionalidades. Se estiver atualmente a pré-visualização de aplicações API, orientação de migração é detalhada abaixo.

### <a name="authentication"></a>Autenticação
As chave na mão API aplicações, serviços/aplicações Mobile e Web Apps autenticação funcionalidades existentes tem sido unified e estão disponíveis numa única pá de autenticação do Azure aplicação de serviço no portal de gestão. Para obter uma introdução para serviços de autenticação na aplicação de serviço, consulte o artigo [autenticação do serviço de aplicação expandir / autorização](https://azure.microsoft.com/blog/announcing-app-service-authentication-authorization/).

Cenários de API, existem várias novas funcionalidades relevantes:

- **Suporte para utilizar o Azure do Active Directory diretamente**, sem ter do token AAD para obter um token de sessão do exchange de código de cliente: O cliente apenas pode incluir os tokens AAD no cabeçalho da autorização, de acordo com a especificação de token de ligação. Isto também significa que não é necessária nenhuma SDK específicas de serviço de aplicação do lado do cliente ou servidor. 
- **Serviço de serviço ou access "Interno"**: Se tiver um processo daemon ou de outro cliente necessário especificá-las acesso a APIs sem uma interface, pode pedir um token utilizando um capital de serviço AAD e transmitem a aplicação de serviço para autenticação com a aplicação.
- **Autorização ' diferida '**: muitas aplicações têm variados restrições de acesso para diferentes partes da aplicação. Talvez pretenda que algumas APIs acessível publicamente, enquanto outras necessitam de iniciar sessão. A funcionalidade de autenticação/autorização original foi tipo tudo ou nada, com a todo o site que exige o início de sessão. Esta opção ainda existe, mas em alternativa pode permitir que o código da aplicação para compor as decisões de acesso após a aplicação de serviço tem o utilizador autenticado.
 
Para mais informações sobre as novas funcionalidades de autenticação, consulte o artigo [autenticação e autorização para aplicações de API na aplicação de serviço de Azure](app-service-api-authentication.md). Para obter informações sobre como migrar existentes API aplicações a partir do modelo de aplicações API anterior para a nova, consulte o artigo [Migrar existentes API aplicações](#migrating-existing-api-apps) neste artigo.
 
### <a name="cors"></a>CORS
Em vez de definição de aplicação um **MS_CrossDomainOrigins** delimitado por vírgulas, não existe agora um pá no portal de gestão Azure para configurar CORS. Em alternativa, pode ser configurado utilizando o Gestor de recursos ferramenta como Azure PowerShell, clip ou [Recurso Explorer](https://resources.azure.com/). Defina a propriedade **cors** no tipo de recurso **Microsoft.Web/sites/config** para sua ** &lt;nome do site&gt;/web** recurso. Por exemplo:

    {
        "cors": {
            "allowedOrigins": [
                "https://localhost:44300"
            ]
        }
    } 

### <a name="api-metadata"></a>API metadados
O pá de definição de API está agora disponível nos Web, Mobile e API Apps. No portal de gestão, pode especificar um url relativo ou um url absoluto apontando para um ponto final que representação de anfitriões um 2.0 Swagger da sua API. Em alternativa, pode ser configurado utilizando ferramentas de Gestor de recursos. Defina a propriedade **apiDefinition** no tipo de recurso **Microsoft.Web/sites/config** para sua ** &lt;nome do site&gt;/web** recurso. Por exemplo:

    {
        "apiDefinition":
        {
            "url": "https://myStorageAccount.blob.core.windows.net/swagger/apiDefinition.json"
        }
    }

Neste momento, o ponto final de metadados tem de estar acessível publicamente sem autenticação para muitos clientes descendentes (por exemplo, Visual Studio REST API cliente ª geração ou PowerApps "Adicionar API" fluxo) consumi-lo. Isto significa que se estiver a utilizar autenticação de aplicação de serviço e pretender expor a definição de API a partir da sua própria aplicação, terá de utilizar a opção de autenticação adiada descrita anteriormente para que a rota para os seus metadados de Swagger seja pública.

## <a name="management-portal"></a>Portal de gestão
Selecionar **Novo > Web + Mobile > API aplicação** no portal do irá criar aplicações de API que refletem as novas capacidades descritas no artigo. **Procurar > aplicações de API** só irá mostrar estas aplicações de API novas. Depois de navegar para uma aplicação de API, o pá partilha o mesmo esquema e capacidades idênticos de Web e Mobile Apps. São as diferenças apenas conteúdo de guia de introdução e ordenação das definições.

Aplicações de API existentes (ou aplicações de Marketplace API criadas a partir das aplicações de lógica) com a pré-visualização anterior capacidades continuarão visíveis no estruturador de lógica aplicações e ao procurar todos os recursos num grupo de recursos.

## <a name="visual-studio"></a>Visual Studio

A maior parte dos Web Apps ferramenta funcionam com novas aplicações API uma vez que partilham o mesmo tipo de recurso **Microsoft.Web/sites** subjacente. O Azure Visual Studio ferramenta, no entanto, deve ser atualizado para a versão 2.8.1 ou posterior desde que expõe um número de funcionalidades específicas para APIs. Transferir o SDK a partir da [página de transferências Azure](https://azure.microsoft.com/downloads/).

Com o racionalização dos tipos de serviço de aplicação, publicar também é unified em **Publicar > serviço de aplicação do Microsoft Azure**:

![Publicar aplicações API](./media/app-service-api-whats-changed/api-apps-publish.png)

Para saber mais sobre SDK 2.8.1, leia o anúncio [mensagem no blogue](https://azure.microsoft.com/blog/announcing-azure-sdk-2-8-1-for-net/).

Em alternativa, pode importar manualmente o perfil de publicar a partir do portal de gestão para ativar a publicar. No entanto, Explorer na nuvem, geração de código e API aplicação seleção/criação irão necessitar SDK 2.8.1 ou superior.

## <a name="migrating-existing-api-apps"></a>Migrar aplicações API existentes
Se o seu API personalizado for implementado a versão anterior da pré-visualização de aplicações API, solicitamos que migrar para o novo modelo para as aplicações de API 31 de Dezembro de 2015. Uma vez que tanto o modelo de novo e antigo é baseado na Web APIs alojado na aplicação de serviço, pode ser reutilizada a maioria dos código existente.

### <a name="hosting-and-redeployment"></a>O alojamento e a nova implementação
Os passos para Reimplementar são a mesma do implementar qualquer API Web existente para a aplicação de serviço. Passos:

1. Crie uma aplicação de API vazia. Isto pode ser feito no portal com o novo > aplicação API, no Visual Studio a partir de publicar, ou de ferramentas do Gestor de recursos. Se utilizar ferramentas de Gestor de recursos ou modelos, defina o valor de **tipo** à **api** no tipo de recurso **Microsoft.Web/sites** para ter a tutoriais e definições no portal de gestão orientado para cenários API.
2. Ligar e implementar o projeto para a aplicação de API vazia utilizar qualquer um dos mecanismos de implementação suportados pelo serviço de aplicação. Leia a [documentação de implementação do serviço de aplicação do Azure](../app-service-web/web-sites-deploy.md) para obter mais informações. 
  
### <a name="authentication"></a>Autenticação
Os serviços de autenticação de aplicação de serviço suportam as mesmas capacidades que estavam disponíveis com o modelo de aplicações API anterior. Se estiver a utilizar o tokens de sessão e exigir SDK, utilize os SDK do cliente e servidor seguintes:

- No [cliente móvel Azure SDK](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) do cliente:
- Servidor: [Microsoft Azure aplicação móvel .NET autenticação extensão](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/) 

Se alfa a aplicação de serviço SDK estivesse a utilizar em vez disso, estes são agora preteridas:

- Cliente: [Microsoft Azure AppService SDK](http://www.nuget.org/packages/Microsoft.Azure.AppService)
- Servidor: [Microsoft.Azure.AppService.ApiApps.Service](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service)

Em particular com o Azure Active Directory, no entanto, sem aplicação específicas de um serviço são necessária se estiver a utilizar o token AAD diretamente.

### <a name="internal-access"></a>Acesso interno
Modelo de anterior API aplicações incluídas um nível de acesso interno incorporadas. Utilização do SDK isto necessário para assinar pedidos. Conforme descrito acima, com o novo modelo de API aplicações, principais de serviço AAD podem ser utilizados como uma alternativa para a autenticação do serviço a serviço sem necessidade de uma aplicação específicas do serviço SDK. Saiba mais do [serviço de autenticação principal para aplicações de API na aplicação de serviço de Azure](app-service-api-dotnet-service-principal-auth.md).

### <a name="discovery"></a>Deteção
O modelo de aplicações API anterior teve APIs para descobrir outras aplicações de API o tempo de execução no mesmo grupo de recursos atrás do mesmo gateway. Este é particularmente útil arquitecturas implementam microservice padrões. Enquanto isto não é suportado diretamente, um número de opções está disponível:

1. Utilize a API Gestor de recursos do Azure para deteção.
2. Coloca o Azure API gestão à frente do seu APIs à alojado de aplicação de serviço. Gestão de API Azure serve como uma fachada e pode disponibilizar um url de opostas externos estável mesmo se topologia interno é alterada.
3. Construir a sua própria aplicação API de deteção e ter outras aplicações de API registe-se com a aplicação de deteção no arranque.
4. No momento da implementação, preencha as definições de aplicação de todas as aplicações de API (e clientes) com os pontos finais de outras aplicações do API. Este é viável nas implementações do modelo e uma vez que as aplicações de API dar-lhe agora controlo do url.

## <a name="using-api-apps-with-logic-apps"></a>Utilizar aplicações do API com aplicações de lógica

O novo modelo de aplicações API funciona bem com [Aplicações de lógica de versão do esquema de 2015-08-01](../app-service-logic/app-service-logic-schema-2015-08-01.md).

## <a name="next-steps"></a>Próximos passos

Para saber mais, consulte os artigos na [secção API aplicações documentação](https://azure.microsoft.com/documentation/services/app-service/api/). Estes tem sido atualizados para refletir o novo modelo para as aplicações de API. Além disso, comunique nos fóruns para detalhes adicionais ou informações sobre a migração:

- [Fórum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureAPIApps)
- [Texto em excesso de pilha](http://stackoverflow.com/questions/tagged/azure-api-apps)
