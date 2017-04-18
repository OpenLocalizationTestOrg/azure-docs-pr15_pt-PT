<properties
    pageTitle="Como pode proteger um back-end da Web API com a gestão de API e Azure Active Directory"
    description="Saiba como pode proteger o back-end Web API com a gestão de API e Azure Active Directory." 
    services="api-management"
    documentationCenter=""
    authors="steved0x"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="sdanie"/>

# <a name="how-to-protect-a-web-api-backend-with-azure-active-directory-and-api-management"></a>Como pode proteger um back-end da Web API com a gestão de API e Azure Active Directory

O vídeo seguinte mostra como construir um back-end da Web API e protegê-lo a utilizar o protocolo OAuth 2.0 com o Azure Active Directory e gestão de API.  Este artigo fornece uma descrição geral e informações adicionais para obter os passos no vídeo. Este vídeo de 24 minutos mostra-lhe como para:

-   Criar um back-end da Web API e seguro-lo com AAD - começando na 1:30
-   Importar a API para gestão de API - começando na 7:10
-   Configurar o portal do programador para chamar a API - começando na 9:09
-   Configurar uma aplicação de ambiente de trabalho para chamar a API - começando na 18:08
-   Configurar uma política de validação JWT para previamente autorizar pedidos - começando na 20:47

>[AZURE.VIDEO protecting-web-api-backend-with-azure-active-directory-and-api-management]

## <a name="create-an-azure-ad-directory"></a>Criar um directório Azure AD

Para proteger a API do seu Web cópias utilizando o Azure Active Directory primeiro tem de ter um um inquilino do AAD. Neste vídeo, é utilizado um inquilino denominado **APIMDemo** . Para criar um inquilino do AAD, início de sessão no [Portal clássica do Azure](https://manage.windowsazure.com) e clique em **Novo**->**Serviços de aplicação**->**Do Active Directory**->**diretório**->**Criar personalizado**. 

![Azure Active Directory][api-management-create-aad-menu]

Neste exemplo, num diretório chamado **APIMDemo** é criado com o domínio predefinido denominado **DemoAPIM.onmicrosoft.com**. Este diretório é utilizado em todo o vídeo.

![Azure Active Directory][api-management-create-aad]

## <a name="create-a-web-api-service-secured-by-azure-active-directory"></a>Criar um serviço Web API protegido por Azure Active Directory

Neste passo, back-end Web API é criada utilizando o Visual Studio 2013. Este passo do vídeo começa em 1:30. Para criar Web API back-end projeto no Visual Studio, clique em **ficheiro**->**Novo**->do**projeto**e selecione a **Aplicação Web do ASP.NET** a partir da lista de modelos **Web** . Neste vídeo, o projeto é denominado **APIMAADDemo**. Clique em **OK** para criar o projeto. 

![Visual Studio][api-management-new-web-app]

Clique em **Web API** da **Seleccionar uma lista de modelo** para criar um projeto de Web API. Para configurar a autenticação de diretório do Azure, clique em **Autenticação de alteração**.

![Novo projeto][api-management-new-project]

Clique em **Contas organizacionais**e especifique o **domínio** do seu inquilino AAD. Neste exemplo, o domínio é **DemoAPIM.onmicrosoft.com**. Pode ser obtido o domínio do seu diretório a partir do separador **domínios** do seu diretório.

![Domínios][api-management-aad-domains]

Configure as definições pretendidas na caixa de diálogo **Alterar autenticação** e clique em **OK**.

![Autenticação de alteração][api-management-change-authentication]

Quando clica em **OK** Visual Studio tentará para registar a aplicação com o seu diretório do Azure AD e poderá ser-lhe iniciar sessão pelo Visual Studio. Inicie sessão com uma conta administrativa para o seu diretório.

![Inicie sessão no Visual Studio][api-management-sign-in-vidual-studio]

Para configurar este projeto como uma a caixa de verificação Azure Web API do **anfitrião na nuvem** e, em seguida, clique em **OK**.

![Novo projeto][api-management-new-project-cloud]

Poderá ser-lhe pedido para iniciar sessão no Azure e, em seguida, pode configurar a aplicação Web.

![Configurar][api-management-configure-web-app]

Neste exemplo é especificado um novo **plano de serviço de aplicação** com o nome **APIMAADDemo** .

Clique em **OK** para configurar a aplicação Web e criar projeto.

## <a name="add-the-code-to-the-web-api-project"></a>Adicionar o código para o project Web API

O passo seguinte no vídeo adiciona o código para o project Web API. Este passo é iniciado no 4:35.

A API Web neste exemplo implementa um serviço de calculadora básica utilizando um modelo e um controlador. Para adicionar o modelo para o serviço, com o botão direito de **modelos** no **Explorador de soluções** e selecione **Adicionar**, **escolares**. Atribuir um nome a classe `CalcInput` e clique em **Adicionar**.

Adicione o seguinte `using` declaração na parte superior da `CalcInput.cs` ficheiro.

    using Newtonsoft.Json;

 Substitua a classe gerada com o código seguinte.

    public class CalcInput
    {
        [JsonProperty(PropertyName = "a")]
        public int a;

        [JsonProperty(PropertyName = "b")]
        public int b;
    }

**Controladores** no **Explorador de solução** com o botão direito e selecione **Adicionar**->**controlador**. Selecione o **Controlador de 2 de API Web - vazia** e clique em **Adicionar**. Escreva **CalcController** para o nome do controlador e clique em **Adicionar**.

![Adicionar controlador][api-management-add-controller]

Adicione o seguinte `using` declaração na parte superior da `CalcController.cs` ficheiro.

    using System.IO;
    using System.Web;
    using APIMAADDemo.Models;

Substitua a classe de controlador gerado com o código seguinte. Este código implementa o `Add`, `Subtract`, `Multiply`, e `Divide` operações da API do calculadora básica.

    [Authorize]
    public class CalcController : ApiController
    {
        [Route("api/add")]
        [HttpGet]
        public HttpResponseMessage GetSum([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a + b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }

        [Route("api/sub")]
        [HttpGet]
        public HttpResponseMessage GetDiff([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a - b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }

        [Route("api/mul")]
        [HttpGet]
        public HttpResponseMessage GetProduct([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a * b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }

        [Route("api/div")]
        [HttpGet]
        public HttpResponseMessage GetDiv([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a / b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }
    }

Prima **F6** para construir e verificar a solução.

## <a name="publish-the-project-to-azure"></a>Publicar o projeto no Azure

Neste passo, o Visual Studio projeto é publicado no Azure. Este passo do vídeo começa em 5:45.

Para publicar o projeto no Azure, com o botão direito do projecto **APIMAADDemo** no Visual Studio e escolha **Publicar**. Mantenha as predefinições na caixa de diálogo **Publicar Web** e clique em **Publicar**.

![Publicar Web][api-management-web-publish]

## <a name="grant-permissions-to-the-azure-ad-backend-service-application"></a>Conceder permissões para a aplicação de serviço do Azure AD back-end

No seu diretório do Azure AD como parte do processo de publicação e configuração do seu projeto Web API, é criada uma nova aplicação para o serviço de back-end. Neste passo do vídeo, começando na 6:13, são concedidas permissões para o Web API back-end.

![Aplicação][api-management-aad-backend-app]

Clique no nome da aplicação para configurar as permissões necessárias. Navegue para o separador de **Configurar** e desloque-se para a secção **permissões para outras aplicações** . Clique em **Permissões da aplicação** pendente ao lado do **Windows** **Azure Active Directory**, selecione a caixa do **diretório de ler dados**e clique em **Guardar**.

![Adicionar permissões][api-management-aad-add-permissions]

>[AZURE.NOTE] Se não estiver listado do **Windows** **Azure Active Directory** em permissões para outras aplicações, clique em **Adicionar aplicação** e adicioná-lo a partir da lista.

Tome nota do **URI de Id da aplicação** para utilização num passo subsequente quando uma aplicação do Azure AD está configurada para o portal de Programador de gestão de API.

![Id da aplicação URI][api-management-aad-sso-uri]

## <a name="import-the-web-api-into-api-management"></a>Importar da Web API para gestão de API

APIs estão configurados a partir do portal do publisher API, que pode é acedido através do Portal clássica do Azure. Para aceder ao portal do publisher, clique em **Gerir** no Portal clássica do seu serviço de gestão de API do Azure. Se ainda não criou uma instância do serviço de gestão de API, consulte o artigo [criar uma instância do serviço de gestão de API][] no tutorial [Gerir o seu primeiro API][] .

![Portal do Publisher][api-management-management-console]

Operações podem ser [adicionada manualmente a APIs](api-management-howto-add-operations.md)ou podem ser importados. Neste vídeo, operações são importadas no formato de Swagger começando na 6:40.

Criar um ficheiro com o nome `calcapi.json` com seguinte conteúdo e guardá-lo para o seu computador. Certifique-se de que o `host` atributo aponta para o seu back-end da Web API. Neste exemplo `"host": "apimaaddemo.azurewebsites.net"` é utilizado.

{"swagger": "2.0", "informações": {"título": "Calculadora", "Descrição": "Arithmetics através de HTTP!", "versão": "1.0"}, "anfitrião": "apimaaddemo.azurewebsites.net", "basePath": "/ api", "esquemas": ["http"], "caminhos": {"/ Adicionar? um = {a} & b = {b}": {"obter": {"Descrição": "Responde com uma soma de dois números.", "IDOperação": "Adicionar dois números inteiros", "parâmetros": [{"nome": "a", "em": "de consulta", "Descrição": "primeiro operando. Valor predefinido é <code>51</code>. ","obrigatório": VERDADEIRO,"predefinido":"51","enumeração": ["51"]}, {"nome":"b","em":"de consulta","Descrição":"segundo operando. Valor predefinido é <code>49</code>. ","obrigatório": VERDADEIRO,"predefinido":"49","enumeração": ["49"]}],"respostas": {}}}," / sub?a = {a} & b = {b} ": {"obter": {"Descrição":"Responde com uma diferença entre dois números.","IDOperação":"Subtrair dois números inteiros","parâmetros": [{"nome":"a","em":"de consulta","Descrição":"primeiro operando. Valor predefinido é <code>100</code>. ","exigidas": VERDADEIRO,"predefinido":"100","enumeração": ["100"]}, {"nome":"b","em":"de consulta","Descrição":"segundo operando. Valor predefinido é <code>50</code>. ","obrigatório": VERDADEIRO,"predefinido":"50","enumeração": ["50"]}],"respostas": {}}}," / div?a = {a} & b = {b} ": {"obter": {"Descrição":"Responde com um quociente de dois números.","IDOperação":"Dividir dois números inteiros","parâmetros": [{"nome":"a","em":"de consulta","Descrição":"primeiro operando. Valor predefinido é <code>100</code>. ","obrigatório": VERDADEIRO,"predefinido":"100","enumeração": ["100"]}, {"nome":"b","em":"de consulta","Descrição":"segundo operando. Valor predefinido é <code>20</code>. ","obrigatório": VERDADEIRO,"predefinido":"20","enumeração": ["20"]}],"respostas": {}}}," / mul?a = {a} & b = {b} ": {"obter": {"Descrição":"Responde com um produto de dois números.","IDOperação":"Multiplicar dois números inteiros","parâmetros": [{"nome":"a","em":"de consulta","Descrição":"primeiro operando. Valor predefinido é <code>20</code>. ","obrigatório": VERDADEIRO,"predefinido":"20","enumeração": ["20"]}, {"nome":"b","em":"de consulta","Descrição":"segundo operando. Valor predefinido é <code>5</code>. ","obrigatório": VERDADEIRO,"predefinido":"5","enumeração": ["5"]}],"respostas": {}}}}}

Para importar a Calculadora API, clique em **APIs** a partir do menu **API gestão** à esquerda e, em seguida, clique em **Importar API**.

![Botão Importar API][api-management-import-api]

Execute os seguintes passos para configurar a API Calculadora.

1. Clique na **partir de ficheiro**, procure o `calculator.json` de ficheiros que guardou e clique no botão de opção **Swagger** .
2. Escreva **calc** na caixa de texto a **URL da Web API sufixo** .
3. Clique na caixa **produtos (opcionais)** e selecione **Starter**.
4. Clique em **Guardar** para importar a API.

![Adicionar a nova API][api-management-import-new-api]

Assim que a API é importada, é apresentada a página de resumo para a API no portal do publisher.

## <a name="call-the-api-unsuccessfully-from-the-developer-portal"></a>Chamar a API sem êxito a partir do portal de programador

Neste momento, a API tiver sido importada API gestão, mas não pode ainda ser chamada com êxito a partir do portal de programador porque o serviço de back-end está protegido com a autenticação do Azure AD. Isso é demonstrado no vídeo começando na 7:40 através dos seguintes passos.

Clique em **Developer portal para** do lado superior direito do portal do publisher.

![Portal de programador][api-management-developer-portal-menu]

Clique em **APIs** e clique em **Calculadora** API.

![Portal de programador][api-management-dev-portal-apis]

Clique em **experimentar**.

![Experimente][api-management-dev-portal-try-it]

Clique em **Enviar** e anote o estado da resposta de **401 não autorizado**.

![Enviar][api-management-dev-portal-send-401]

O pedido é não autorizado porque o back-end API está protegido pelo Azure Active Directory. Antes de chamar com êxito API o Programador de portal tem de ser configurado para autorizar os programadores que utilizam OAuth 2.0. Este processo é descrito nas secções seguintes.

## <a name="register-the-developer-portal-as-an-aad-application"></a>Registe-se o portal do programador como uma aplicação de AAD

É o primeiro passo para configurar o portal de programador para autorizar os programadores que utilizam OAuth 2.0 registar o developer portal como uma aplicação de AAD. Isso é demonstrado começando na 8:27 no vídeo.

Navegue para o inquilino do Azure AD a partir do primeiro passo neste vídeo, neste exemplo **APIMDemo** e navegue para o separador de **aplicações** .

![Nova aplicação][api-management-aad-new-application-devportal]

Clique no botão **Adicionar** para criar uma nova aplicação do Azure Active Directory e selecione **Adicionar uma aplicação que está a desenvolver a minha organização**.

![Nova aplicação][api-management-new-aad-application-menu]

Selecione a **aplicação Web e/ou Web API**, introduza um nome e clique na seta seguinte. Neste exemplo é utilizada **APIMDeveloperPortal** .

![Nova aplicação][api-management-aad-new-application-devportal-1]

Para o **início de sessão URL** introduza o URL do seu serviço de gestão de API e acrescentar `/signin`. Neste exemplo é utilizada **https://contoso5.portal.azure-api.net/signin **.

Para o **URL de Id da aplicação** introduza o URL do seu serviço de gestão de API e acrescentar alguns carateres exclusivos. Estes podem ser quaisquer caracteres pretendidas e neste exemplo é utilizada **https://contoso5.portal.azure-api.net/dp** . Quando estiverem configuradas as **Propriedades da aplicação** de pretendidas, clique na marca de verificação para criar a aplicação.

![Nova aplicação][api-management-aad-new-application-devportal-2]

## <a name="configure-an-api-management-oauth-20-authorization-server"></a>Configurar um servidor de autorização API Management OAuth 2.0

O próximo passo é configurar um servidor de autorização de OAuth 2.0 na gestão de API. Este passo é demonstrado no vídeo começando na 9:43.

Clique em **segurança** no menu de gestão de API à esquerda, clique em **OAuth 2.0**e, em seguida, clique em **Adicionar autorização** server.

![Adicionar autorização servidor][api-management-add-authorization-server]

Introduza um nome e uma descrição opcional nos campos **nome** e **Descrição** . Estes campos são utilizados para identificar o servidor de autorização de OAuth 2.0 dentro a instância do serviço de gestão de API. Neste exemplo é utilizada **demonstração de servidor de autorização** . Mais tarde quando especificar um servidor de OAuth 2.0 para ser utilizado para autenticação para uma API, que irá selecionar este nome.

Para obter o **URL de página de registo do cliente** para introduzir um valor de marcador de posição, tal como `http://localhost`.  O **URL de página de registo do cliente** aponta para a página que os utilizadores podem utilizar para criar e configurar os seus próprios contas para OAuth 2.0 fornecedores que suportam a gestão de contas de utilizador. Neste exemplo os utilizadores não criar e configurar os seus próprios contas para que serve um marcador de posição.

![Adicionar autorização servidor][api-management-add-authorization-server-1]

Em seguida, especifique o **URL de ponto final de autorização** e **Token URL de ponto final**.

![Servidor de autorização][api-management-add-authorization-server-1a]

Podem ser obtidos estes valores a partir da página **Pontos finais de aplicação** da aplicação AAD que criou para o portal de programador. Para aceder aos pontos finais navegue para o separador **Configurar** para a aplicação de AAD e clique em **pontos finais de vista**.

![Aplicação][api-management-aad-devportal-application]

![Pontos finais de vista][api-management-aad-view-endpoints]

Copie o **ponto final de autorização de OAuth 2.0** e colá-la a caixa de texto do **URL de ponto final de autorização** .

![Adicionar autorização servidor][api-management-add-authorization-server-2]

Copie o **ponto final de token OAuth 2.0** e colá-la a caixa de texto do **URL do Token ponto final** .

![Adicionar autorização servidor][api-management-add-authorization-server-2a]

Além de colagem no ponto final do token, adicione um parâmetro de corpo adicionais com o nome **recurso** e para a utilização de valor o **URI de Id da aplicação** a partir da aplicação AAD para o serviço de back-end que foi criada quando o projeto do Visual Studio foi publicado.

![Id da aplicação URI][api-management-aad-sso-uri]

Em seguida, especifique as credenciais do cliente. Estas são as credenciais para o recurso que pretende aceder, neste caso o serviço de back-end.

![Credenciais do cliente][api-management-client-credentials]

Para obter o **Id de cliente**, navegue para o separador **Configurar** da aplicação AAD para o serviço de back-end e copie o **Código do cliente**.

Para obter o **Segredo cliente** clique **Selecione duração** seta de lista pendente na secção de **teclas** e especificar um intervalo. Neste exemplo é utilizado a 1 ano.

![ID de cliente][api-management-aad-client-id]

Clique em **Guardar** para guardar a configuração e apresentar a chave. 

>[AZURE.IMPORTANT] Anote esta chave. Depois de fechar a janela de configuração do Azure Active Directory, a tecla não pode ser apresentada novamente.

Copiar a chave para a área de transferência, mude novamente para o portal do publisher, cole a chave de caixa de texto a **Segredo cliente** e clique em **Guardar**.

![Adicionar autorização servidor][api-management-add-authorization-server-3]

Imediatamente a seguir as credenciais do cliente é uma conceder de código de autorização. Copiar este código autorização e mudar para a aplicação portal de programador do Azure AD Configurar página e cole a autorização conceder para o campo de **URL de resposta** e clique novamente em **Guardar** .

![URL de resposta][api-management-aad-reply-url]

O próximo passo é configurar as permissões para o portal de Programador de aplicação AAD. Clique em **Permissões da aplicação** e selecione a caixa do **diretório de ler dados**. Clique em **Guardar** para guardar esta alteração e, em seguida, clique em **Adicionar aplicação**.

![Adicionar permissões][api-management-add-devportal-permissions]

Clique no ícone de pesquisa, escreva **APIM** para a começar com caixa de, selecione **APIMAADDemo**e clique na marca de verificação para guardar.

![Adicionar permissões][api-management-aad-add-app-permissions]

Clique em **Permissões do delegado** para **APIMAADDemo** e selecione a caixa do **Access APIMAADDemo**e clique em **Guardar**. Esta opção permite-o Programador de aplicação portal para aceder ao serviço de back-end.

![Adicionar permissões][api-management-aad-add-delegated-permissions]

## <a name="enable-oauth-20-user-authorization-for-the-calculator-api"></a>Ativar OAuth 2.0 autorização de utilizador para a API Calculadora

Agora que o servidor OAuth 2.0 está configurado, pode especificá-la nas definições de segurança para sua API. Este passo é demonstrado no vídeo começando na 14:30.

Clique em **APIs** no menu à esquerda e clique em **Calculadora** para ver e configurar as suas definições.

![Calculadora de API][api-management-calc-api]

Navegue para o separador **segurança** , verifique a caixa de verificação **OAuth 2.0** , selecionar o servidor de autorização pretendido a partir do menu pendente de **servidor de autorização** e clique em **Guardar**.

![Calculadora de API][api-management-enable-aad-calculator]

## <a name="successfully-call-the-calculator-api-from-the-developer-portal"></a>Ligar com êxito API Calculadora a partir do portal de programador

Agora que a autorização de acesso OAuth 2.0 está configurada no API, as suas operações podem ser chamadas com êxito o Centro de programadores do. Este passo é demonstrado no vídeo começando na 15:00.

Navegar de volta para a operação de **Adicionar dois números inteiros** do serviço de calculadora no portal do programador e clique em **experimentar**. Tenha em atenção o novo item na secção **autorização** correspondente ao servidor de autorização que acabou de adicionar.

![Calculadora de API][api-management-calc-authorization-server]

Selecione o **código de autorização** a partir da lista pendente de autorização e introduza as credenciais da conta a utilizar. Se já tiver sessão iniciada com a conta que poderá não ser-lhe.

![Calculadora de API][api-management-devportal-authorization-code]

Clique em **Enviar** e tenha em atenção o **Estado da resposta** dos **200 OK** e os resultados da operação de no conteúdo de resposta.

![Calculadora de API][api-management-devportal-response]

## <a name="configure-a-desktop-application-to-call-the-api"></a>Configurar uma aplicação de ambiente de trabalho para chamar a API

O procedimento seguinte no vídeo começa em 16:30 e configura uma aplicação de ambiente de trabalho simple para chamar a API. O primeiro passo é registar a aplicação de ambiente de trabalho no Azure AD e conceder-lhe acesso ao diretório e para o serviço de back-end. Em 18:25 existe uma demonstração sobre da aplicação de ambiente de trabalho que entra em contacto uma operação a API Calculadora.

## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Configurar uma política de validação JWT para previamente autorizar pedidos

O procedimento final no vídeo começa em 20:48 e mostra-lhe como utilizar a política de [Validar JWT](https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT) para previamente autorizar pedidos por validar os tokens de acesso de cada pedido recebido. Se o pedido não for validado pela política de validar JWT, o pedido foi bloqueado pelo API gestão e não é transmitido ao longo para o back-end.

    <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
        <openid-config url="https://login.windows.net/DemoAPIM.onmicrosoft.com/.well-known/openid-configuration" />
        <required-claims>
            <claim name="aud">
                <value>https://DemoAPIM.NOTonmicrosoft.com/APIMAADDemo</value>
            </claim>
        </required-claims>
    </validate-jwt>

Para outra demonstração de sobre configurar e utilizar esta política, consulte o artigo [nuvem de folha de rosto episódio 177: mais funcionalidades de gestão de API](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e para a frente rápido para 13:50. Avançar para 15:00 para ver as políticas que foram configuradas no editor de política e, em seguida, para 18:50 para uma demonstração de chamar uma operação a partir do portal de programador com e sem o token de autorização necessária.

## <a name="next-steps"></a>Próximos passos
-   Consulte o artigo mais [vídeos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sobre a gestão de API.
-   Para outras formas de proteger o seu serviço de back-end, consulte [a autenticação de certificados comum](api-management-howto-mutual-certificates.md) e [Ligar via VPN ou ExpressRoute](api-management-howto-setup-vpn.md).

[api-management-management-console]: ./media/api-management-howto-protect-backend-with-aad/api-management-management-console.png

[api-management-import-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-new-api.png
[api-management-create-aad-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad-menu.png
[api-management-create-aad]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad.png
[api-management-new-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-web-app.png
[api-management-new-project]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project.png
[api-management-new-project-cloud]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project-cloud.png
[api-management-change-authentication]: ./media/api-management-howto-protect-backend-with-aad/api-management-change-authentication.png
[api-management-sign-in-vidual-studio]: ./media/api-management-howto-protect-backend-with-aad/api-management-sign-in-vidual-studio.png
[api-management-configure-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-configure-web-app.png
[api-management-aad-domains]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-domains.png
[api-management-add-controller]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-controller.png
[api-management-web-publish]: ./media/api-management-howto-protect-backend-with-aad/api-management-web-publish.png
[api-management-aad-backend-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-backend-app.png
[api-management-aad-add-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-permissions.png
[api-management-developer-portal-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-developer-portal-menu.png
[api-management-dev-portal-apis]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-apis.png
[api-management-dev-portal-try-it]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-try-it.png
[api-management-dev-portal-send-401]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-send-401.png
[api-management-aad-new-application-devportal]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal.png
[api-management-aad-new-application-devportal-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-1.png
[api-management-aad-new-application-devportal-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-2.png
[api-management-aad-devportal-application]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-devportal-application.png
[api-management-add-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server.png
[api-management-aad-sso-uri]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-sso-uri.png
[api-management-aad-view-endpoints]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-view-endpoints.png
[api-management-aad-client-id]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-client-id.png
[api-management-add-authorization-server-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1.png
[api-management-add-authorization-server-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2.png
[api-management-add-authorization-server-2a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2a.png
[api-management-add-authorization-server-3]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-3.png
[api-management-aad-reply-url]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-reply-url.png
[api-management-add-devportal-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-devportal-permissions.png
[api-management-aad-add-app-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-app-permissions.png
[api-management-aad-add-delegated-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-delegated-permissions.png
[api-management-calc-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-api.png
[api-management-enable-aad-calculator]: ./media/api-management-howto-protect-backend-with-aad/api-management-enable-aad-calculator.png
[api-management-devportal-authorization-code]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-authorization-code.png
[api-management-devportal-response]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-response.png
[api-management-calc-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-authorization-server.png
[api-management-add-authorization-server-1a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1a.png
[api-management-client-credentials]: ./media/api-management-howto-protect-backend-with-aad/api-management-client-credentials.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-aad-application-menu.png

[Criar uma instância do serviço de gestão de API]: api-management-get-started.md#create-service-instance
[Gerir a sua primeira API]: api-management-get-started.md
