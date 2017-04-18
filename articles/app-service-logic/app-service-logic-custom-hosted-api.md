<properties
    pageTitle="Ligar uma API personalizada nas aplicações de lógica"
    description="Utilizar a API personalizado alojado no serviço de aplicação com aplicações de lógica"
    authors="stepsic-microsoft-com"
    manager="dwrede"
    editor=""
    services="logic-apps"
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/31/2016"
    ms.author="stepsic"/>

# <a name="using-your-custom-api-hosted-on-app-service-with-logic-apps"></a>Utilizar a API personalizado alojado no serviço de aplicação com aplicações de lógica

Apesar de lógica aplicações tem um conjunto avançado de 40 + conectores para uma variedade de serviços, poderá querer telefonar para o seu próprio API personalizado que pode executar o seu próprio código. Uma das formas mais fácil e mais dimensionáveis para alojar o seu próprio web personalizada do API é utilizar a aplicação de serviço. Este artigo aborda como ligar para qualquer web API alojado numa aplicação API do serviço de aplicação, no browser ou aplicação móvel.

Para obter informações sobre a criação de APIs como um accionador ou ação lógica aplicações, consulte o artigo [neste artigo](app-service-logic-create-api-app.md).

## <a name="deploy-your-web-app"></a>Implementar a aplicação Web

Em primeiro lugar, terá de implementar o seu API como uma aplicação Web na aplicação de serviço. Implementação básica de folha de rosto as instruções aqui: [criar uma aplicação web do ASP.NET](../app-service-web/web-sites-dotnet-get-started.md).  Enquanto pode ligar para qualquer API através de uma aplicação de lógica, para a melhor experiência, que recomendamos adicione metadados Swagger para integrar facilmente com ações de aplicações lógica.  Pode localizar detalhes sobre como [Adicionar swagger](../app-service-api/app-service-api-dotnet-get-started.md#use-swagger-api-metadata-and-ui).

### <a name="api-settings"></a>Definições de API

Ordem para o estruturador de aplicações lógica para analisar o seu Swagger, é importante que ativar CORS e definir as propriedades de APIDefinition da sua aplicação web.  Este é muito fácil de definir dentro do Portal do Azure.  Basta abrir pá as definições da sua aplicação Web, na secção API configurar a 'API definição' para o URL do seu ficheiro de swagger.json (este nome costuma https://{name}.azurewebsites.net/swagger/docs/v1) e adicionar uma política CORS para ' *' para permitir os pedidos da aplicações lógica Designer.

## <a name="calling-into-the-api"></a>Que entra em contacto para a API

Quando se encontra dentro do portal de aplicações do lógica, se tiver de definir CORS e as propriedades de definição de API deverá conseguir facilmente Adicione ações personalizadas API do seu fluxo.  No estruturador de pode selecionar para procurar os Web sites públicos subscrição lista de Web sites com um URL de swagger definido.  Também pode utilizar o protocolo HTTP + Swagger ação apontar para uma swagger e ações disponíveis e entradas do tipo de lista.  Por fim, pode sempre criar um pedido de utilizar a ação de HTTP para ligar a qualquer API, mesmo para aqueles que não tem ou não expõem num documento swagger.

Se quiser proteger o seu API, existem algumas maneiras diferentes de fazê-lo:

1. Não altera o código necessária - Azure Active Directory pode ser utilizada para proteger a sua API sem necessidade de quaisquer alterações de código ou nova implementação.
1. Impor Auth básicas, AAD Auth ou certificado Auth no código do seu API.

## <a name="securing-calls-to-your-api-without-a-code-change"></a>Proteger as chamadas para o seu API sem uma alteração de código

Nesta secção, irá criar duas aplicações do Azure Active Directory – uma para a sua aplicação de lógica e outra para a sua aplicação Web.  Irá autenticar chamadas para a sua aplicação Web utilizando o capital de serviço (id de cliente e secreta) associado à aplicação AAD para a sua aplicação de lógica. Por fim, irá incluir a aplicação ID na sua definição de aplicação lógica.

### <a name="part-1-setting-up-an-application-identity-for-your-logic-app"></a>Parte 1: Configurar uma identidade de aplicação para a sua aplicação de lógica

Este é o que utiliza a aplicação de lógica para autenticar do active directory. Só *precisa de* efetuar este procedimento uma vez para o seu diretório. Por exemplo, pode optar por utilizar a mesma identidade para todas as aplicações de lógica, apesar de também poderá criar identidades exclusivas por aplicação lógica se assim o pretender. Pode fazê-lo na IU ou utilizar o PowerShell.

#### <a name="create-the-application-identity-using-the-azure-classic-portal"></a>Criar a identidade da aplicação através do portal clássico Azure

1. Navegue até [do Active directory no portal clássico do Azure](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory) e selecione o diretório que utilizar para a sua aplicação Web
2. Clique no separador **aplicações**
3. Clique em **Adicionar** na barra de comandos na parte inferior da página
4. Dê um nome para o utilizar, clique na seta seguinte ao sua identidade
5. Colocar numa cadeia exclusiva formatada como um domínio nas duas caixas de texto e clique na marca de verificação
6. Clique no separador **Configurar** para esta aplicação
7. Copiar o **Código do cliente**, este será utilizada na sua aplicação de lógica
8. Na secção **teclas** clique em **Seleccionar duração** e selecione 1 ano ou anos a 2
9. Clique no botão **Guardar** na parte inferior do ecrã (poderá ter de aguardar alguns segundos)
10. Certifique-se agora copiar a chave na caixa. Isto também ser utilizado na sua aplicação de lógica

#### <a name="create-the-application-identity-using-powershell"></a>Criar a identidade da aplicação através do PowerShell
1. `Switch-AzureMode AzureResourceManager`
2. `Add-AzureAccount`
3. `New-AzureADApplication -DisplayName "MyLogicAppID" -HomePage "http://someranddomain.tld" -IdentifierUris "http://someranddomain.tld" -Password "Pass@word1!"`
4. Certifique-se copiar o **ID de inquilino**, o **ID da aplicação** e a palavra-passe que utilizou

### <a name="part-2-protect-your-web-app-with-an-aad-app-identity"></a>Parte 2: Proteger a sua aplicação Web com uma identidade de aplicação AAD

Se já for implementada a sua aplicação Web apenas pode ativá-la no portal. Caso contrário, pode efetuar ativar autorização parte da sua implementação do Gestor de recursos do Azure.

#### <a name="enable-authorization-in-the-azure-portal"></a>Ativar a autorização no Portal do Azure

1. Navegue para a aplicação Web e clique nas **Definições** , na barra de comandos.
2. Clique em **Autorização/autenticação**.
3. **Ativá-la.**

Neste momento, é automaticamente criada uma aplicação para si. Precisa ID de cliente esta aplicação para a parte 3, pelo que terá para:

1. Aceda ao [Active directory no portal do Azure clássico](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory) e selecione o seu diretório.
2. Pesquisa para a aplicação na caixa de pesquisa
3. Clique na mesma na lista
4. Clique no separador **Configurar**
5. Deverá visualizar o **Código do cliente**

#### <a name="deploying-your-web-app-using-an-arm-template"></a>Implementar a aplicação Web do utilizando um modelo de processador

Em primeiro lugar, tem de criar uma aplicação para a sua aplicação Web. Deve ser diferente a partir da aplicação que é utilizada para a sua aplicação de lógica. Comece por seguir os passos acima na parte 1, mas agora para a utilização da **Home page** e **IdentifierUris** o https:// real**URL** da sua aplicação Web.

>[AZURE.NOTE]Quando cria a aplicação para a sua aplicação Web, tem de utilizar a [abordagem de portal clássica Azure](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory), tal como o commandlet PowerShell não configurar as permissões necessárias para iniciar sessão utilizadores para um Web site.

Assim que tiver o cliente de ID e o ID de inquilino, incluir o seguinte como um recurso de sub da aplicação Web no seu modelo de implementação:

```
"resources" : [
    {
        "apiVersion" : "2015-08-01",
        "name" : "web",
        "type" : "config",
        "dependsOn" : [
            "[concat('Microsoft.Web/sites/','parameters('webAppName'))]"
        ],
        "properties" : {
            "siteAuthEnabled": true,
            "siteAuthSettings": {
              "clientId": "<<clientID>>",
              "issuer": "https://sts.windows.net/<<tenantID>>/",
            }
        }
    }
]
```

Para executar uma implementação automaticamente que implementa uma aplicação Web vazia e lógica app em conjunto que utilizam AAD, clique no botão seguinte:

[![Implementar Azure](./media/app-service-logic-custom-hosted-api/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

Para o modelo concluído, consulte o artigo [chamadas de aplicação de lógica numa API personalizado alojado no serviço de aplicação e protegidos por AAD](https://github.com/Azure/azure-quickstart-templates/blob/master/201-logic-app-custom-api/azuredeploy.json).


### <a name="part-3-populate-the-authorization-section-in-the-logic-app"></a>Parte 3: Preencher a secção de autorização na aplicação lógica

Na secção **autorização** da ação **HTTP** :`{"tenant":"<<tenantId>>", "audience":"<<clientID from Part 2>>", "clientId":"<<clientID from Part 1>>","secret": "<<Password or Key from Part 1>>","type":"ActiveDirectoryOAuth" }`

| Elemento | Descrição |
|---------|-------------|
| tipo | Tipo de autenticação. Para a autenticação de ActiveDirectoryOAuth, o valor é ActiveDirectoryOAuth. |
| inquilino | O identificador de inquilino utilizado para identificar o inquilino do AD. |
| audiência | Obrigatório. O recurso que está a ligar. |
| clientID | O identificador de cliente para a aplicação do Azure AD. |
| secreta | Obrigatório. Secreta do cliente que está a pedir o token. |

O modelo acima já tiver isto configurar, mas se estiver a criar a aplicação de lógica diretamente, terá de incluir a secção de autorização completo.

## <a name="securing-your-api-in-code"></a>Proteger o seu API no código

### <a name="certificate-auth"></a>Autenticação de certificado

Pode utilizar os certificados de cliente para validar os pedidos recebidos para a sua aplicação Web. Consulte o artigo [Como para configurar TLS comum autenticação para o Web App](../app-service-web/app-service-web-configure-tls-mutual-auth.md) para saber como configurar o seu código.

Na secção *autorização* deve fornecer: `{"type": "clientcertificate","password": "test","pfx": "long-pfx-key"}`.

| Elemento | Descrição |
|---------|-------------|
| tipo | Obrigatório. Tipo de autenticação. Para certificados SSL de cliente, o valor tem de ser ClientCertificate. |
| PFX | Obrigatório. Conteúdo do ficheiro PFX codificado em Base64. |
| palavra-passe | Obrigatório. Palavra-passe para aceder ao ficheiro PFX. |

### <a name="basic-auth"></a>Autenticação básica

Pode utilizar a autenticação básica (por exemplo, nome de utilizador e palavra-passe) para validar os pedidos recebidos. Auth básica é um padrão comuns e pode fazê-lo em qualquer idioma que construir a sua aplicação no.

Na secção *autorização* , deve fornecer: `{"type": "basic","username": "test","password": "test"}`.

| Elemento | Descrição |
|---------|-------------|
| tipo | Obrigatório. Tipo de autenticação. Para a autenticação básica, o valor tem de ser Basic. |
| nome de utilizador | Obrigatório. Nome de utilizador para autenticar. |
| palavra-passe | Obrigatório. Palavra-passe para autenticar. |

### <a name="handle-aad-auth-in-code"></a>Alça de AAD auth no código

Por predefinição, a autenticação do Azure Active Directory que permitem no Portal do não autorização extensivamente. Por exemplo, não bloquear o API para uma aplicação ou utilizador específico, mas apenas para um inquilino específico.

Se pretende restringir a API apenas lógica aplicação, por exemplo, no código, pode extrair o cabeçalho que contém a JWT e verifique quem o autor da chamada é, rejeitar quaisquer pedidos de que não correspondem.

Ir para além disso, se pretender implementá-lo completamente no seu próprio código e não tirar partido a funcionalidade de Portal, pode ler este artigo: [Utilizar do Active Directory para autenticação na aplicação de serviço de Azure](../app-service-web/web-sites-authentication-authorization.md).

Ainda precisa de seguir os passos acima para criar uma identidade de aplicação para a sua aplicação de lógica e utilizá-la para chamar a API.
