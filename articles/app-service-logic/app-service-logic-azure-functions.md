<properties
   pageTitle="Utilizar funções Azure com aplicações de lógica | Microsoft Azure"
   description="Veja como utilizar funções Azure com aplicações de lógica"
   services="logic-apps,functions"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="10/18/2016"
   ms.author="jehollan"/>

# <a name="using-azure-functions-with-logic-apps"></a>Utilizar funções Azure com aplicações de lógica

Pode executar fragmentos personalizados do c# ou node.js utilizando funções de Azure a partir de uma aplicação de lógica.  [Funções de Azure](../azure-functions/functions-overview.md) oferece servidor de royalties computação no Microsoft Azure. Isto é útil para efetuar as seguintes tarefas:

* Formatação avançada ou cluster de campos dentro de uma aplicação de lógica
* Efetuar cálculos dentro de um fluxo de trabalho
* Expandir as funcionalidades de lógica de aplicações com funções são suportadas no c# ou node.js

## <a name="create-a-function-for-logic-apps"></a>Criar uma função para as aplicações de lógica

Recomendamos que cria uma nova função no portal do Azure funções utilizando os modelos de **Webhook genérico - nó** ou **Webhook genérico - c#** . Isto automática-preenche um modelo que aceita `application/json` através de uma aplicação de lógica.  Se selecionar o separador de **integrar** nas funções Azure deve ter **modo** definido para **Webhook** e **Webhook tipo** de **JSON genérico**.  Funções que utilizam estes modelos aparecem automaticamente descobertas e os listados no estruturador de lógica aplicações em **funções Azure na minha região.**

Funções de Webhook aceitar um pedido e passá-lo para o método através de um `data` variável. Pode aceder às propriedades da sua carga útil utilizando a notação ponto como `data.foo`.  Por exemplo, uma função de JavaScript simple que converte um valor DateTime numa cadeia de data aspeto o exemplo seguinte:

```
function start(req, res){
    var data = req.body;
    res = {
        body: data.date.ToDateString();
    }
}
```

## <a name="call-azure-functions-from-a-logic-app"></a>Chamar funções do Azure através de uma aplicação de lógica

No estruturador de, se clicar em menu **ações** , pode selecionar **Funções Azure na minha região**.  Esta lista os contentores na sua subscrição e permite-lhe escolher a função que pretende ligar.  

Depois de selecionar a função, lhe for pedido para especificar um objeto de carga útil entrada. Esta é a mensagem que envia a aplicação de lógica para a função e tem de ser um objeto JSON. Por exemplo, se pretender transmitir a data da **Última modificação** de um accionador Salesforce, a carga útil função poderá este aspeto:

![Data da última modfied][1]

## <a name="trigger-logic-apps-from-a-function"></a>Acionar lógica aplicações a partir de uma função

Também é possível acionar uma aplicação a partir de lógica dentro de uma função.  Para fazer isto, basta crie uma aplicação de lógica com um accionador manual. Para mais informações, consulte o artigo [lógica aplicações como passível pontos finais](app-service-logic-http-endpoint.md).  Em seguida, dentro de uma função, gere uma mensagem de HTTP para o URL de accionador manual com a carga de útil que pretende enviar para a aplicação de lógica.

### <a name="create-a-function-from-the-designer"></a>Criar uma função a partir do designer

Também pode criar node.js webhook função a partir do estruturador de. Em primeiro lugar, selecione **Funções Azure na minha região** e, em seguida, selecione um contentor para a sua função.  Se ainda não tenha um contentor, terá de criar um partir do [portal do Azure funções](https://functions.azure.com/signin). Em seguida, selecione **Criar novo**.  

Para gerar um modelo com base nos dados que pretende calcular, especifique o objeto de contexto que planeia para passar para uma função. Tem de ser um objeto JSON. Por exemplo, se passar no conteúdo do ficheiro a partir de uma ação de FTP, a carga útil contexto terá o seguinte aspeto:

![Carga útil de contexto][2]

>[AZURE.NOTE] Uma vez que este objeto não foi haver como uma cadeia, o conteúdo será adicionado diretamente para a carga útil JSON. No entanto, irá apresentar um erro se não for um token JSON (ou seja, uma cadeia ou um objeto/matriz JSON). Convertê-la como uma cadeia, basta adicione propostas conforme mostrado na ilustração primeira neste artigo.

O estruturador, em seguida, gera um modelo de função que pode criar inline. Variáveis previamente são criadas com baseadas no contexto que planeia para passar para a função.




<!--Image references-->
[1]: ./media/app-service-logic-azure-functions/callFunction.png
[2]: ./media/app-service-logic-azure-functions/createFunction.png
