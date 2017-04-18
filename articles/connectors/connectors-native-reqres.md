<properties
    pageTitle="Utilizar as ações de pedidos e respostas | Microsoft Azure"
    description="Descrição geral da accionador pedidos e respostas e ação numa aplicação do Azure lógica"
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
   ms.date="07/18/2016"
   ms.author="jehollan"/>

# <a name="get-started-with-the-request-and-response-components"></a>Começar a trabalhar com os componentes de pedidos e respostas

Com os componentes de pedidos e respostas numa aplicação lógica, pode responder em tempo real a eventos.

Por exemplo, pode:

- Responda a um pedido de HTTP com dados de uma base de dados no local através de uma aplicação de lógica.
- Acionar uma aplicação de lógica a partir de um evento webhook externos.
- Ligar uma aplicação de lógica com uma ação de pedidos e respostas a partir de outra aplicação lógica.

Para começar a utilizar as ações de pedidos e respostas numa aplicação lógica, consulte o artigo [criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="use-the-http-request-trigger"></a>Utilizar o pedido de HTTP accionador

Um accionador é um evento que pode ser utilizado para iniciar o fluxo de trabalho que está definido numa aplicação de lógica. [Saiba mais sobre os accionadores](connectors-overview.md).

Eis uma sequência de exemplo de como configurar um pedido de HTTP no estruturador de aplicação de lógica.

1. Adicione o accionador **pedido - pedido de HTTP an quando for recebido** na sua aplicação de lógica. Opcionalmente, pode fornecer um esquema JSON (ao utilizar uma ferramenta como [JSONSchema.net](http://jsonschema.net)) para o corpo do pedido. Esta opção permite-designer gerar tokens para propriedades no pedido de HTTP.
2. Adicione outra ação para que possam guardar a aplicação de lógica.
3. Depois de guardar a aplicação de lógica, pode obter o URL do pedido HTTP do cartão pedido.
4. Uma mensagem de HTTP (que pode utilizar uma ferramenta como [Postman](https://www.getpostman.com/)) para o URL de accionadores a aplicação de lógica.

>[AZURE.NOTE] Se não definir uma ação de resposta, um `202 ACCEPTED` imediatamente é devolvida resposta o autor da chamada. Pode utilizar a ação de resposta para personalizar uma resposta.

![Acionador de resposta](./media/connectors-native-reqres/using-trigger.png)

## <a name="use-the-http-response-action"></a>Utilizar a ação de resposta de HTTP

A ação de resposta de HTTP apenas é válida quando utilizá-lo num fluxo de trabalho que é acionado por um pedido HTTP. Se não definir uma ação de resposta, um `202 ACCEPTED` imediatamente é devolvida resposta o autor da chamada.  Pode adicionar uma ação de resposta em qualquer um dos passos do fluxo de trabalho. A aplicação de lógica apenas mantém o pedido de entrada aberta para um minuto para obter uma resposta.  Depois de um minuto, se foi enviada sem resposta do fluxo de trabalho (e existe uma acção de resposta na definição do), um `504 GATEWAY TIMEOUT` são devolvidos ao autor da chamada.

Eis como adicionar uma ação de resposta de HTTP:

1. Selecione o botão de **Novo passo** .
2. Selecione **Adicionar uma ação**.
3. Na caixa de pesquisa de ação, escreva a **resposta** para a ação de resposta da lista.

    ![Selecione a ação de resposta](./media/connectors-native-reqres/using-action-1.png)

4. Adicione quaisquer parâmetros que são necessários para a mensagem de resposta HTTP.

    ![Concluir a ação de resposta](./media/connectors-native-reqres/using-action-2.png)

5. Clique no canto superior esquerdo da barra de ferramentas para guardar e, a aplicação de lógica irá guardar e publicar (ativar).

## <a name="request-trigger"></a>Pedido de accionador

Aqui estão os detalhes para o accionador compatível com esta conexão. Existe um accionador único pedido.

|Accionador|Descrição|
|---|---|
|Pedido|Ocorre quando um pedido de HTTP for recebido|

## <a name="response-action"></a>Ação de resposta

Aqui estão os detalhes para a ação que suporta esta conexão. Existe uma ação de única resposta apenas pode ser utilizada quando é fornecido com por um accionador pedido.

|Ação|Descrição|
|---|---|
|Resposta|Devolve uma resposta ao pedido de HTTP correlacionada|

### <a name="trigger-and-action-details"></a>Detalhes do acionador e acção

As tabelas seguintes descrevem os campos de entrada do acionador e ação e o correspondente de saída de detalhes.

#### <a name="request-trigger"></a>Pedido de accionador
Segue-se um campo de entrada para o accionador a partir de um pedido de HTTP recebido.

|Nome a apresentar|Nome da propriedade|Descrição|
|---|---|---|
|Esquema JSON|esquema|O esquema de JSON do corpo do pedido HTTP|
<br>

**Detalhes de saída**

Seguem-se detalhes de saída para o pedido.

|Nome da propriedade|Tipo de dados|Descrição|
|---|---|---|
|Cabeçalhos|objecto|Pedido de cabeçalhos|
|Corpo|objecto|Pedido de objeto|

#### <a name="response-action"></a>Ação de resposta

Seguem-se os campos de entrada para a ação de resposta de HTTP. A * significa que é um campo obrigatório.

|Nome a apresentar|Nome da propriedade|Descrição|
|---|---|---|
|Código de estado *|statusCode|O código de estado HTTP|
|Cabeçalhos|cabeçalhos|Um objeto JSON de quaisquer cabeçalhos de resposta para incluir|
|Corpo|corpo|O corpo da resposta|

## <a name="next-steps"></a>Próximos passos

Agora, experimente a plataforma e, em seguida, [criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md). Pode explorar as outras conexões disponíveis nas aplicações de lógica verificando nossa [lista APIs](apis-list.md).
