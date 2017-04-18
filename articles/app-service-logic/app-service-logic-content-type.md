<properties
   pageTitle="Aplicações de lógica conteúdas escreva processamento | Microsoft Azure"
   description="Compreender como lógica aplicações lida com tipos de conteúdo na estrutura e o tempo de execução"
   services="logic-apps"
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

# <a name="logic-apps-content-type-handling"></a>Processamento do tipo de conteúdo de aplicações de lógica

Existem vários tipos de conteúdo que pode um fluxo de através de uma aplicação de lógica - incluindo JSON, XML, ficheiros simples e dados binários diferentes.  Enquanto todos os tipos de conteúdo são suportados, algumas vierem geralmente utilizadas pelo motor de aplicações de lógica e outras pessoas podem exigir moldagem ou conversões conforme necessário.  O seguinte artigo irá descrevem como o motor trata os diferentes tipos de conteúdo e podem ser corretamente processados conforme necessário.

## <a name="content-type-header"></a>Cabeçalho de tipo de conteúdo

Para começar a simples, vamos ver as duas `Content-Types` que não exigir início de qualquer conversão ou converter para utilizar o dentro de uma aplicação de lógica - `application/json` e `text/plain`.

### <a name="applicationjson"></a>Aplicação/json

Depende do motor de fluxo de trabalho a `Content-Type` cabeçalho HTTP chamadas para determinar o processamento adequado.  Qualquer pedido com o tipo de conteúdo `application/json` serão armazenados e processado como um objeto de JSON.  Além disso, conteúdo JSON pode possam ser analisado por predefinição sem necessitar de qualquer moldagem.  Por isso, um pedido de que tem o cabeçalho do tipo de conteúdo `application/json ` da seguinte forma:

```
{
    "data": "a",
    "foo": [
        "bar"
    ]
}
```

foi analisar num fluxo de trabalho com uma expressão como `@body('myAction')['foo'][0]` para obter um valor (neste caso, `bar`).  É necessário sem moldagem adicional.  Se estiver a trabalhar com dados de que está JSON mas não tem um cabeçalho especificado, pode manualmente convertê-la para JSON utilizando o `@json()` função (por exemplo: `@json(triggerBody())['foo']`).

### <a name="textplain"></a>Texto/simples

Semelhante ao `application/json`, as mensagens HTTP recebidas com as `Content-Type` cabeçalho da `text/plain` serão armazenados no formulário observou.  Além disso, se incluído num acções subsequentes sem moldagem em qualquer pedido de subscreve com um `Content-Type`: `text/plain` cabeçalho.  Por exemplo, se a trabalhar com um ficheiro simples poderá receber o conteúdo HTTP seguinte:

```
Date,Name,Address
Oct-1,Frank,123 Ave.
```

as `text/plain`.  Se na seguinte ação enviou-lo como o corpo do pedido de outro (`@body('flatfile')`), teria de pedido de um `text/plain` cabeçalho de tipo de conteúdo.  Se estiver a trabalhar com os dados que é o texto simples mas não tiverem um cabeçalho especificado, pode manualmente convertê-la para texto utilizando o `@string()` função (por exemplo: `@string(triggerBody())`)

### <a name="applicationxml-and-applicationoctet-stream-and-converter-functions"></a>Aplicação/xml e octeto/aplicação-sequência e conversor de funções

O motor de aplicação lógica sempre irá preservar a `Content-Type` que foi recebida no pedido de HTTP ou resposta.  O que significa que está se uma conteúdo é recebida com `Content-Type` de `application/octet-stream`, incluindo que numa ação subsequente com sem moldagem irá resultar num pedido de envio com `Content-Type`: `application/octet-stream`.  Desta forma o motor podem guaruntee dados não serão perdidos como move o cursor ao longo do fluxo de trabalho.  No entanto, o estado de ação (entradas e saídas) são armazenadas num objeto JSON como flua em todo o fluxo de trabalho.  Isto significa para poder preservar alguns tipos de dados, o motor será converta o conteúdo para uma cadeia de codificação base64 binário com metadados adequados que preserva o ambos `$content` e `$content-type` -qual será convertido automaticamente.  Pode também manualmente converter entre tipos de conteúdo utilizando compilado nas funções conversor:

* `@json()`-Converte dados para`application/json`
* `@xml()`-Converte dados para`application/xml`
* `@binary()`-Converte dados para`application/octet-stream`
* `@string()`-Converte dados para`text/plain`
* `@base64()`-Converte uma cadeia base64 conteúdo
* `@base64toString()`-Converte uma cadeia de codificação base64 para`text/plain`
* `@base64toBinary()`-Converte uma cadeia de codificação base64 para`application/octet-stream`
* `@encodeDataUri()`-codifica numa cadeia como uma matriz de bytes dataUri
* `@decodeDataUri()`-Descodifica um dataUri para uma matriz de bytes

Por exemplo, se tiver recebido um pedido de HTTP com `Content-Type`: `application/xml` de:

```
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

Posso poderá haver e utilizar mais tarde com algo parecido com `@xml(triggerBody())`, ou dentro de uma função como `@xpath(xml(triggerBody()), '/CustomerName')`.

### <a name="other-content-types"></a>Tipos de outro conteúdo

Outros tipos de conteúdo são suportados e funcionam com uma aplicação de lógica, mas pode exigir manualmente a obter o corpo da mensagem por descodificar a `$content`.  Por exemplo, se posso foram acionar fora de um `application/x-www-url-formencoded` pedido que velhos o seguinte:

```
CustomerName=Frank&Address=123+Avenue
```

uma vez que não texto simples ou JSON serão armazenado na ação como:

```
...
"body": {
    "$content-type": "application/x-www-url-formencoded",
    "$content": "AAB1241BACDFA=="
}
```

Onde `$content` é a carga útil codificado como uma cadeia de base64 preservar todos os dados.  Uma vez que neste momento não existe uma função nativa para os dados de formulário, poderia ainda utilizo estes dados dentro de um fluxo de trabalho acedendo manualmente os dados com uma função como `@string(body('formdataAction'))`.  Se pretendia o meu pedido de envio também tenham o `application/x-www-url-formencoded` cabeçalho de tipo de conteúdo, poderia basta adicioná-la ao corpo de acção sem qualquer moldagem como `@body('formdataAction')`.  No entanto, isto irá apenas funciona se corpo é o único parâmetro na `body` entrada.  Se tentar fazer `@body('formdataAction')` dentro de uma `application/json` pedido irá obter um erro de execução como enviará codificado corpo.
