<properties
   pageTitle="Aplicações de lógica como passível pontos finais"
   description="Como criar e configurar acionar os pontos finais e utilizá-los numa aplicação lógica na aplicação de serviço do Azure"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="10/18/2016"
   ms.author="jehollan"/>


# <a name="logic-apps-as-callable-endpoints"></a>Aplicações de lógica como passível pontos finais

Lógica aplicações vierem pode expor a um ponto final de HTTP síncrono como um accionador.  Também pode utilizar o padrão de passível pontos finais para invocar lógica aplicações como um fluxo de trabalho aninhado através da acção "fluxo de trabalho" numa aplicação lógica.

Existem 3 tipos de accionadores que podem receber pedidos de:

* Pedido
* ApiConnectionWebhook
* HttpWebhook

Para o resto do artigo, vamos utilizar o **pedido** como no exemplo, mas todos os princípios aplicam idêntico para os outros tipos de accionadores 2.

## <a name="adding-a-trigger-to-your-definition"></a>Adicionar um accionador a sua definição de
É o primeiro passo adicionar um accionador a sua definição de aplicação de lógica que possa receber pedidos recebidos.  Pode procurar no estruturador de para "Pedido de HTTP" para adicionar o cartão de accionador. Pode definir um corpo do pedido JSON esquema e o estruturador irá gerar tokens para o ajudar a analisar e transmitir dados a partir do manual accionador através do fluxo de trabalho.  Posso que utilize uma ferramenta como [jsonschema.net](http://jsonschema.net) gerar um esquema JSON a partir de uma carga útil corpo de exemplo.

![Pedido de accionador cartão][2]

Após guardar a sua definição de aplicação de lógica, um URL de chamada de retorno será gerado semelhante a este:
 
``` text
https://prod-03.eastus.logic.azure.com:443/workflows/080cb66c52ea4e9cabe0abf4e197deff/triggers/myendpointtrigger?*signature*...
```

Este URL contém uma chave de SA os parâmetros de consulta utilizados para autenticação.

Também pode obter este ponto final no portal do Azure:

![][1]

Ou, ao contactar o suporte:

``` text
POST https://management.azure.com/{resourceID of your logic app}/triggers/myendpointtrigger/listCallbackURL?api-version=2015-08-01-preview
```

### <a name="security-for-the-trigger-url"></a>Segurança para o URL de accionador

URLs de chamada de retorno lógica aplicação são gerados segura utilizando uma assinatura de acesso partilhado.  A assinatura é transmitida através de como um parâmetro de consulta e tem de ser validada antes da aplicação de lógica irá fogo.  Este é gerado através de uma combinação de uma chave secreta por aplicação lógica, o nome do acionador e a operação a ser executada exclusiva.  A menos que alguém tenha acesso à chave de aplicação lógica secreta, teria não poderão gerar uma assinatura válida.

## <a name="calling-the-logic-app-triggers-endpoint"></a>Contactar o ponto final do accionador de aplicação de lógica

Assim que tiver criado o ponto final para o accionador, pode acioná-lo através de um `POST` para o URL completo. Pode incluir cabeçalhos adicionais e qualquer tipo de conteúdo no corpo.

Se o tipo de conteúdo é `application/json` , em seguida, que poderão propriedades de referência de dentro o pedido. Caso contrário, será tratado como uma única unidade binária que possam ser transmitida para outras APIs mas não pode ser referenciada dentro do fluxo de trabalho sem a converter o conteúdo.  Por exemplo, se passar `application/xml` conteúdo pode utilizar `@xpath()` fazer extração de xpath, ou `@json()` para converter de XML JSON.  Obter mais informações sobre como trabalhar com conteúdo tipos [podem ser encontradas aqui](app-service-logic-content-type.md)

Além disso, pode especificar um esquema JSON na definição do. Isto faz com que o designer para gerar tokens que, em seguida, pode passar para o passos.  Por exemplo o seguinte procedimento irá tornar uma `title` e `name` token disponível no estruturador de:

```
{
    "properties":{
        "title": {
            "type": "string"
        },
        "name": {
            "type": "string"
        }
    },
    "required": [
        "title",
        "name"
    ],
    "type": "object"
}
```

## <a name="referencing-the-content-of-the-incoming-request"></a>Referenciar o conteúdo do pedido a receber

O `@triggerOutputs()` função vai exportar o conteúdo do pedido a receber. Por exemplo,-teria o seguinte aspeto:

```
{
    "headers" : {
        "content-type" : "application/json"
    },
    "body" : {
        "myprop" : "a value"
    }
}
```

Pode utilizar o `@triggerBody()` atalho para aceder a `body` propriedade especificamente. 

## <a name="responding-to-the-request"></a>Responder ao pedido

Para alguns pedidos de iniciar uma aplicação de lógica, poderá pretende responder com algum conteúdo para o autor da chamada. Existe um novo tipo de ação denominado **resposta** que podem ser utilizados para construir o código de estado, o corpo e cabeçalhos para a sua resposta. Nota que se existir nenhuma forma de **resposta** , o ponto final de aplicação de lógica irá *imediatamente* responder com **202 aceite**.

![Ação de resposta de HTTP][3]

``` json
"Response": {
            "conditions": [],
            "inputs": {
                "body": {
                    "name": "@{triggerBody()['name']}",
                    "title": "@{triggerBody()['title']}"
                },
                "headers": {
                    "content-type": "application/json"
                },
                "statusCode": 200
            },
            "type": "Response"
        }
```

As respostas de tem o seguinte:

| Propriedade | Descrição |
| -------- | ----------- |
| statusCode | O código de estado HTTP para responder ao pedido de entrada. Pode ser qualquer código de estado válida que começa com 2xx, 4xx ou 5xx. códigos de estado 3xx não são permitidos. | 
| corpo | Um objeto de corpo que pode ser uma cadeia, um objeto JSON ou até mesmo binário conteúdo referenciados a partir de um passo anterior. | 
| cabeçalhos | Pode definir qualquer número de cabeçalhos para ser incluído na resposta | 

Todos os passos na aplicação lógica que são necessários para a resposta tem de concluir dentro de *60 segundos* para que o pedido original para receber a resposta **a menos que o fluxo de trabalho a ser chama-se como uma aplicação de lógica aninhadas**. Se nenhuma ação de resposta for atingida dentro de 60 segundos, em seguida, o pedido de entrada irá expirar e recebe uma resposta de **tempo limite do cliente 408** HTTP.  Aplicações de lógica aninhadas, principal lógica aplicação irão continuar a aguardar uma resposta até concluído, independentemente da quantidade de tempo demora.

## <a name="advanced-endpoint-configuration"></a>Configuração de ponto final avançadas

Aplicações de lógica ter criado no suporte para o ponto final de acesso direto e utilizar sempre o `POST` método para iniciar uma execução da aplicação lógica. A aplicação de **Escuta HTTP** API anteriormente também suportada alterar os segmentos de URL e o método de HTTP. Poderia configurar par a segurança adicional ou um domínio personalizado, adicionando-o para o anfitrião de aplicação API (a aplicação Web que alojado a aplicação de API). 

Esta funcionalidade está disponível através da **Gestão de API**:
* [Alterar o método do pedido de](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod)
* [Alterar os segmentos de URL do pedido de](https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL)
* Configurar o seus domínios de gestão de API no separador **Configurar** no portal do Azure clássico
* Configurar a política para verificar a existência de autenticação básica (**ligação seja necessário**)

## <a name="summary-of-migration-from-2014-12-01-preview"></a>Resumo da migração de 2014-12-01-pré-visualização

|  2014-12-01-pré-visualização | 2016-06-01 |
|---------------------|--------------------|
| Clique em aplicação **Escuta HTTP** API | Clique em **acionador Manual** (sem aplicação API necessária) |
| Escuta HTTP a definição "*envia automaticamente resposta*" | Quer incluir uma ação de **resposta** ou não na definição do fluxo de trabalho |
| Configurar basic ou a autenticação do token | através de API management |
| Configurar o método de HTTP | através de gestão de API |
| Configurar caminho relativo | através de gestão de API |
| Corpo recebido através de referência`@triggerOutputs().body.Content` | Referência através do`@triggerOutputs().body` |
| Ação **Enviar HTTP resposta** a escuta HTTP | Clique em **responder a pedidos de HTTP** (sem aplicação API necessária)


[1]: ./media/app-service-logic-http-endpoint/manualtriggerurl.png
[2]: ./media/app-service-logic-http-endpoint/manualtrigger.png
[3]: ./media/app-service-logic-http-endpoint/response.png
