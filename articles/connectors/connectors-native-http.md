<properties
    pageTitle="Adicionar a ação de HTTP nas aplicações de lógica | Microsoft Azure"
    description="Descrição geral da ação HTTP com propriedades"
    services=""
    documentationCenter=""
    authors="jeffhollan"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/15/2016"
   ms.author="jehollan"/>

# <a name="get-started-with-the-http-action"></a>Começar a trabalhar com a ação de HTTP

Com a ação de HTTP, pode expandir fluxos de trabalho para a sua organização e comunicar para qualquer ponto final através de HTTP.

Pode:

- Crie lógica de fluxos de trabalho da aplicação que ativar (accionador) quando um Web site que faz a gestão vai para baixo.
- Comunica para qualquer ponto final através de HTTP para expandir os seus fluxos de trabalho para outros serviços.

Para começar a utilizar a ação de HTTP numa aplicação lógica, consulte o artigo [criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="use-the-http-trigger"></a>Utilizar o accionador HTTP

Um accionador é um evento que pode ser utilizado para iniciar o fluxo de trabalho que está definido numa aplicação de lógica. [Saiba mais sobre os accionadores](connectors-overview.md).

Eis uma sequência de exemplo de como configurar o accionador HTTP no estruturador de aplicação de lógica.

1. Adicione o accionador HTTP na sua aplicação de lógica.
2. Preencha os parâmetros para o ponto final de HTTP que pretende consultar.
3. Modificar o intervalo de periodicidade no frequência deve as inquérito.
4. A aplicação de lógica é acionada com qualquer conteúdo que é devolvido durante a verificação de cada.

![Acionar HTTP](./media/connectors-native-http/using-trigger.png)

### <a name="how-the-http-trigger-works"></a>Como funciona o accionador HTTP

O accionador HTTP faz uma chamada para um ponto final HTTP num intervalo de periódico. Por predefinição, qualquer resposta HTTP código inferior a 300 resultados numa aplicação lógica executar. Pode adicionar uma condição na vista de código que vai ser avaliada depois da chamada HTTP para determinar se a aplicação de lógica deve fogo. Eis um exemplo de um acionador HTTP é acionada sempre que o código de estado devolvido é maior ou igual a `400`.

```javascript
"Http":
{
    "conditions": [
        {
            "expression": "@greaterOrEquals(triggerOutputs()['statusCode'], 400)"
        }
    ],
    "inputs": {
        "method": "GET",
        "uri": "https://blogs.msdn.microsoft.com/logicapps/",
        "headers": {
            "accept-language": "en"
        }
    },
    "recurrence": {
        "frequency": "Second",
        "interval": 15
    },
    "type": "Http"
}
```

Mais informações sobre os parâmetros de accionador HTTP estão disponíveis no [MSDN](https://msdn.microsoft.com/library/azure/mt643939.aspx#HTTP-trigger).

## <a name="use-the-http-action"></a>Utilizar a ação de HTTP

Uma ação é uma operação que é realizada pelo fluxo de trabalho que está definido numa aplicação de lógica. [Saber mais sobre ações](connectors-overview.md).

1. Selecione o botão de **Novo passo** .
2. Selecione **Adicionar uma ação**.
3. Na caixa de pesquisa de ação, escreva **http** para a ação de HTTP da lista.

    ![Selecione a ação de HTTP](./media/connectors-native-http/using-action-1.png)

4. Adicione quaisquer parâmetros que são necessários para a chamada HTTP.

    ![Concluir a ação de HTTP](./media/connectors-native-http/using-action-2.png)

5. Clique em canto superior esquerdo da barra de ferramentas para guardar. A aplicação de lógica irá guardar e publicar (ativar).

## <a name="http-trigger"></a>Acionar HTTP

Aqui estão os detalhes para o accionador compatível com esta conexão. O conector HTTP tem um accionador.

|Accionador|Descrição|
|---|---|
|HTTP|Faz uma chamada HTTP e devolve o conteúdo de resposta.|

## <a name="http-action"></a>Ação de HTTP

Aqui estão os detalhes para a ação que suporta esta conexão. O conector HTTP tem uma ação possível.

|Ação|Descrição|
|---|---|
|HTTP|Faz uma chamada HTTP e devolve o conteúdo de resposta.|

## <a name="http-details"></a>Detalhes HTTP

As tabelas seguintes descrevem os campos de entrada necessários e opcionais para a ação e os detalhes de saída correspondentes que estão associados a utilizar a ação.


#### <a name="http-request"></a>Pedido de HTTP
Seguem-se os campos de entrada para a ação, que torna um pedido de HTTP de saída.
A * significa que é um campo obrigatório.

|Nome a apresentar|Nome da propriedade|Descrição|
|---|---|---|
|Método de *|método|Para utilizar o verbo de HTTP|
|URI *|URI|O URI para o pedido de HTTP|
|Cabeçalhos|cabeçalhos|Um objeto JSON de cabeçalhos de HTTP para incluir|
|Corpo|corpo|Corpo do pedido HTTP|
|Autenticação|autenticação|Detalhes na secção [autenticação](#authentication)|
<br>

#### <a name="output-details"></a>Detalhes de saída

Seguem-se detalhes de saída para a resposta de HTTP.

|Nome da propriedade|Tipo de dados|Descrição|
|---|---|---|
|Cabeçalhos|objecto|Cabeçalhos de resposta|
|Corpo|objecto|Objeto de resposta|
|Código de estado|Int|Código de estado HTTP|

## <a name="authentication"></a>Autenticação

A funcionalidade de lógica aplicações de serviço de aplicação do Azure permite-lhe utilizar diferentes tipos de autenticação contra pontos finais de HTTP. Pode utilizar esta autenticação com os conectores **HTTP**, **[HTTP + Swagger](./connectors-native-http-swagger.md)**e **[HTTP Webhook](./connectors-native-webhook.md)** . Os seguintes tipos de autenticação são configuráveis:

* [Autenticação básica](#basic-authentication)
* [Autenticação de certificado de cliente](#client-certificate-authentication)
* [Autenticação do Azure Active Directory (Azure AD) OAuth](#azure-active-directory-oauth-authentication)

#### <a name="basic-authentication"></a>Autenticação básica

Objecto de autenticação que se segue é necessário para a autenticação básica.
A * significa que é um campo obrigatório.

|Nome da propriedade|Tipo de dados|Descrição|
|---|---|---|
|Tipo de *|tipo|Tipo de autenticação (tem de ser `Basic` para a autenticação básica)|
|Nome de utilizador *|nome de utilizador|Nome de utilizador para autenticar|
|Palavra-passe *|palavra-passe|Palavra-passe para autenticar|

>[AZURE.TIP] Se pretender utilizar uma palavra-passe não é possível obter da definição do, utilize uma `securestring` parâmetro e o `@parameters()` [função de definição de fluxo de trabalho](http://aka.ms/logicappdocs).

Por isso, pretender criar um objeto da seguinte forma no campo de autenticação:

```javascript
{
    "type": "Basic",
    "username": "user",
    "password": "test"
}
```

#### <a name="client-certificate-authentication"></a>Autenticação de certificado de cliente

Objecto de autenticação que se segue é necessário para autenticação de certificado de cliente. A * significa que é um campo obrigatório.

|Nome da propriedade|Tipo de dados|Descrição|
|---|---|---|
|Tipo de *|tipo|O tipo de autenticação (tem de ser `ClientCertificate` para certificados SSL de cliente)|
|PFX *|PFX|O conteúdo codificado em Base64 do ficheiro de intercâmbio de informações pessoais (PFX)|
|Palavra-passe *|palavra-passe|A palavra-passe para aceder ao ficheiro PFX|

>[AZURE.TIP] Pode utilizar uma `securestring` parâmetro e o `@parameters()` [função de definição de fluxo de trabalho](http://aka.ms/logicappdocs) para utilizar um parâmetro que não será legível na definição do depois de guardar a aplicação de lógica.

Por exemplo:

```javascript
{
    "type": "ClientCertificate",
    "pfx": "aGVsbG8g...d29ybGQ=",
    "password": "@parameters('myPassword')"
}
```

#### <a name="azure-ad-oauth-authentication"></a>Autenticação do Azure AD OAuth

Objecto de autenticação que se segue é necessário para a autenticação do Azure AD OAuth. A * significa que é um campo obrigatório.

|Nome da propriedade|Tipo de dados|Descrição|
|---|---|---|
|Tipo de *|tipo|O tipo de autenticação (tem de ser `ActiveDirectoryOAuth` para Azure AD OAuth)|
|Inquilino *|inquilino|O identificador de inquilino para o inquilino do Azure AD|
|Audiência *|audiência|Configurar para`https://management.core.windows.net/`|
|Cliente ID *|clientId|O identificador de cliente para a aplicação do Azure AD|
|Secreta *|secreta|O segredo do cliente que está a pedir o token|

>[AZURE.TIP] Pode utilizar uma `securestring` parâmetro e o `@parameters()` [função de definição de fluxo de trabalho](http://aka.ms/logicappdocs) para utilizar um parâmetro que não será legível na definição do depois de guardar.

Por exemplo:

```javascript
{
    "type": "ActiveDirectoryOAuth",
    "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "audience": "https://management.core.windows.net/",
    "clientId": "34750e0b-72d1-4e4f-bbbe-664f6d04d411",
    "secret": "hcqgkYc9ebgNLA5c+GDg7xl9ZJMD88TmTJiJBgZ8dFo="
}
```

## <a name="next-steps"></a>Próximos passos

Agora, experimente a plataforma e, em seguida, [criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md). Pode explore as outras conexões disponíveis nas aplicações de lógica verificando nossa [lista APIs](apis-list.md).
