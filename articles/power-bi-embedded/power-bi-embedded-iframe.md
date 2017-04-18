<properties
   pageTitle="Como utilizar o Power BI incorporado com resto | Microsoft Azure"
   description="Saiba como utilizar o Power BI incorporado com restantes "
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="how-to-use-power-bi-embedded-with-rest"></a>Como utilizar o Power BI incorporado com restantes


## <a name="power-bi-embedded-what-it-is-and-what-its-for"></a>Power BI incorporados: O que é e o que é para
Uma descrição geral do Power BI incorporado é descrita no [site do Power BI incorporado](https://azure.microsoft.com/services/power-bi-embedded/)oficial, mas vamos dar uma rápida antes de recebemos para os detalhes sobre a utilizá-lo com restantes.

É muito simple, na verdade. Um ISV muitas vezes quer utilizar as visualizações de dados dinâmicos do [Power BI](https://powerbi.microsoft.com) na sua própria aplicação como IU blocos modulares.

No entanto, sabe, a incorporação de relatórios do Power BI ou mosaicos na sua página web já se possível, sem o serviço do Power BI incorporado Azure, utilizando a **API do Power BI**. Quando quiser partilhar os relatórios na sua organização do mesmo, pode incorporar os relatórios com a autenticação do Azure AD. O utilizador quem vê os relatórios deve utilizar os seus próprios conta Azure AD de início de sessão. Quando quiser partilhar os relatórios para todos os utilizadores (incluindo os utilizadores externos), pode simplesmente incorporar com acesso anónimo.

Mas vir, este simples incorporar solução bastante não cumprir as necessidades de uma aplicação do ISV.
A maioria das aplicações ISV necessárias para a prestação os dados para os seus próprios clientes, não necessariamente os utilizadores na sua própria organização. Por exemplo, se estiver a realizar a alguns serviços para a empresa A e B de empresa, os utilizadores na empresa A só deverão ver dados para os seus próprios empresa respostas. Isto é, o multitenancy é necessário para a entrega.

A aplicação ISV pode também ser perguntar se as suas próprias métodos de autenticação como formulários auth, auth básica, etc... Em seguida, a solução incorporação tem colaborar com este existente métodos de autenticação em segurança. Também é necessário para que os utilizadores poderá utilizar essas aplicações ISV sem a compra extra ou licenciamento de uma subscrição do Power BI.

 **Power BI incorporado** foi concebido para com precisão estes tipos de cenários ISV. Por isso, agora que temos esse breve introdução fora da área de trabalho, vamos alguns detalhes

Pode utilizar .NET \(c#) ou SDK Node.js, para construir facilmente a sua aplicação com o Power BI incorporado. Mas, neste artigo, explicaremos sobre fluxo HTTP \(incluindo AuthN) do Power BI sem SDK. Noções sobre este fluxo, pode criar a sua aplicação **com qualquer linguagem de programação**e fortemente conseguem compreender a essência da incorporado do Power BI.

## <a name="create-power-bi-workspace-collection-and-get-access-key-provisioning"></a>Colecção de área de trabalho de criar o Power BI e obter acesso chave \(aprovisionar)
Power BI incorporado, é um dos serviços do Azure. Apenas o ISV que utilizam o Portal do Azure é cobrado para taxas de utilização \(por hora a hora sessão de utilizador), e o utilizador quem vê o relatório não é cobrada ou mesmo exigir uma subscrição do Azure.
Antes de iniciar o nosso desenvolvimento de aplicações, podemos tem de criar a **coleções de sites do Power BI da área de trabalho** utilizando o Portal do Azure.

Cada área de trabalho do Power BI incorporado é a área de trabalho para cada cliente (inquilino) e, podemos acrescentar muitas áreas de trabalho em cada colecção de área de trabalho. A tecla de acesso mesmo é utilizada em cada colecção de área de trabalho. Em efeito, área de trabalho é o limite de segurança para o Power BI incorporado.

![](media\power-bi-embedded-iframe\create-workspace.png)

Quando o podemos concluir a criação da coleção de área de trabalho, copie a tecla de acesso a partir do Azure Portal.

![](media\power-bi-embedded-iframe\copy-access-key.png)

> [AZURE.NOTE] Recomendamos também pode aprovisionar a coleção de área de trabalho e obter a chave de acesso através da REST API. Para saber mais, consulte o artigo [APIs de fornecedor de recursos do Power BI](https://msdn.microsoft.com/library/azure/mt712306.aspx).

## <a name="create-pbix-file-with-power-bi-desktop"></a>Criar .pbix ficheiro com o Power BI Desktop
Em seguida, podemos tem de criar a ligação de dados e relatórios para ser incorporado.
Para esta tarefa, não existe nenhuma programação ou o código. Utilizamos apenas o ambiente de trabalho do Power BI.
Neste artigo, iremos não percorrer os detalhes sobre como utilizar o Power BI Desktop. Se precisar de ajuda aqui, consulte o artigo [Introdução ao Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/). Para o nosso exemplo, iremos apenas utilizar a [Amostra de análise de venda a retalho](https://powerbi.microsoft.com/documentation/powerbi-sample-datasets/).

![](media\power-bi-embedded-iframe\power-bi-desktop-1.png)

## <a name="create-a-power-bi-workspace"></a>Criar uma área de trabalho do Power BI

Agora que o aprovisionamento todos concluir o processo, vamos começar a criar a área de trabalho de um cliente na coleção de área de trabalho através do REST APIs. A seguinte HTTP mensagem pedir (REST) está a criar nova área de trabalho no nossa coleção de área de trabalho existente. No nosso exemplo, o nome de coleções de sites de área de trabalho é **mypbiapp**.
Vamos basta definir a chave de acesso, podemos copiou anteriormente, como **AppKey**. É muito simple autenticação!

**Pedido de HTTP**

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces
Authorization: AppKey MpaUgrTv5e...
```

**Resposta de HTTP**

```
HTTP/1.1 201 Created
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
Location: https://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces
RequestId: 4220d385-2fb3-406b-8901-4ebe11a5f6da

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/$metadata#workspaces/$entity",
  "workspaceId": "32960a09-6366-4208-a8bb-9e0678cdbb9d",
  "workspaceCollectionName": "mypbiapp"
}
```

O devolvida **workspaceId** é utilizada para as seguintes chamadas da API subsequentes. Este valor tem de reter nossa aplicação.

## <a name="import-pbix-file-into-the-workspace"></a>Importar ficheiro de .pbix para a área de trabalho
Cada área de trabalho pode alojar num único ficheiro com um conjunto de dados de ambiente de trabalho do Power BI \(incluindo definições de origem de dados) e relatórios. Vamos pode importar ficheiro nosso .pbix para a área de trabalho, conforme mostrado no código abaixo. Como pode ver, podemos pode carregar o ficheiro binário .pbix do ficheiro do utilizando MIME multipart no http.

O fragmento de uri **32960a09-6366-4208-a8bb-9e0678cdbb9d** é o workspaceId e de parâmetro de consulta **datasetDisplayName** é o nome do conjunto de dados para criar. O conjunto de dados criado mantém todos os dados relacionados com artefactos no .pbix de ficheiros tal como os dados importados, o ponteiro à origem de dados, etc...

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports?datasetDisplayName=mydataset01
Authorization: AppKey MpaUgrTv5e...
Content-Type: multipart/form-data; boundary="A300testx"

--A300testx
Content-Disposition: form-data

{the content (binary) of .pbix file}
--A300testx--
```

Poderá executar esta tarefa de importar para o tempo. Quando estiver concluído, a nossa aplicação pode pedir o estado da tarefa com o id de importação. No nosso exemplo, o id de importar é **4eec64dd-533b-47c3-a72c-6508ad854659**.

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports/4eec64dd-533b-47c3-a72c-6508ad854659?tenantId=myorg
RequestId: 658bd6b4-b68d-4ec3-8818-2a94266dc220

{"id":"4eec64dd-533b-47c3-a72c-6508ad854659"}
```

A seguinte está a pedir Estado utilizando este id importar:

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports/4eec64dd-533b-47c3-a72c-6508ad854659
Authorization: AppKey MpaUgrTv5e...
```

Se não a tarefa estiver concluída, a resposta de HTTP dever-se da seguinte forma:

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
RequestId: 614a13a5-4de7-43e8-83c9-9cd225535136

{
  "id": "4eec64dd-533b-47c3-a72c-6508ad854659",
  "importState": "Publishing",
  "createdDateTime": "2016-07-19T07:36:06.227",
  "updatedDateTime": "2016-07-19T07:36:06.227",
  "name": "mydataset01"
}
```

Se a tarefa estiver concluída, a resposta de HTTP pode ser mais da seguinte forma:

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
RequestId: eb2c5a85-4d7d-4cc2-b0aa-0bafee4b1606

{
  "id": "4eec64dd-533b-47c3-a72c-6508ad854659",
  "importState": "Succeeded",
  "createdDateTime": "2016-07-19T07:36:06.227",
  "updatedDateTime": "2016-07-19T07:36:06.227",
  "reports": [
    {
      "id": "2027efc6-a308-4632-a775-b9a9186f087c",
      "name": "mydataset01",
      "webUrl": "https://app.powerbi.com/reports/2027efc6-a308-4632-a775-b9a9186f087c",
      "embedUrl": "https://app.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c"
    }
  ],
  "datasets": [
    {
      "id": "458e0451-7215-4029-80b3-9627bf3417b0",
      "name": "mydataset01",
      "tables": [
      ],
      "webUrl": "https://app.powerbi.com/datasets/458e0451-7215-4029-80b3-9627bf3417b0"
    }
  ],
  "name": "mydataset01"
}
```

## <a name="data-source-connectivity-and-multi-tenancy-of-data"></a>Conectividade de origem de dados \(e multitenancy dos dados)
Enquanto quase todas as dos erros no ficheiro .pbix são importadas para os nossos área de trabalho, as credenciais de origens de dados não estão. Como resultado, ao utilizar o **modo DirectQuery**, o relatório incorporado não pode ser apresentado corretamente. No entanto, quando utilizar o **modo de importação**, podemos pode ver o relatório utilizando os dados importados existentes. Neste caso, podemos tem de definir credenciais através dos seguintes passos através do resto chamadas.

Primeiro, vamos terá de obter a origem de dados do gateway. Saiba quais que o **id** do conjunto de dados é o id devolvido anteriormente.

**Pedido de HTTP**

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/datasets/458e0451-7215-4029-80b3-9627bf3417b0/Default.GetBoundGatewayDatasources
Authorization: AppKey MpaUgrTv5e...
```

**Resposta de HTTP**

```
GET HTTP/1.1 200 OK
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
RequestId: 574b0b18-a6fa-46a6-826c-e65840cf6e15

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/$metadata#gatewayDatasources",
  "value": [
    {
      "id": "5f7ee2e7-4851-44a1-8b75-3eb01309d0ea",
      "gatewayId": "ca17e77f-1b51-429b-b059-6b3e3e9685d1",
      "datasourceType": "Sql",
      "connectionDetails": "{\"server\":\"testserver.database.windows.net\",\"database\":\"testdb01\"}"
    }
  ]
}
```

Com o id da origem de dados e o id de gateway devolvida \(consulte o artigo o anterior **gatewayId** e o **id** no resultado devolvido), podemos pode alterar a credencial desta origem de dados da seguinte forma:

**Pedido de HTTP**

```
PATCH https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/gateways/ca17e77f-1b51-429b-b059-6b3e3e9685d1/datasources/5f7ee2e7-4851-44a1-8b75-3eb01309d0ea
Authorization: AppKey MpaUgrTv5e...
Content-Type: application/json; charset=utf-8

{
  "credentialType": "Basic",
  "basicCredentials": {
    "username": "demouser",
    "password": "P@ssw0rd"
  }
}
```

**Resposta de HTTP**

```
HTTP/1.1 200 OK
Content-Type: application/octet-stream
RequestId: 0e533c13-266a-4a9d-8718-fdad90391099
```

Produção, recomendamos também pode definir a cadeia de ligação diferente para cada área de trabalho utilizando REST API. \(seja, podemos pode separar a base de dados para cada clientes.)

A seguinte consiste em alterar a cadeia de ligação de origem de dados através do resto.

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/datasets/458e0451-7215-4029-80b3-9627bf3417b0/Default.SetAllConnections
Authorization: AppKey MpaUgrTv5e...
Content-Type: application/json; charset=utf-8

{
  "connectionString": "data source=testserver02.database.windows.net;initial catalog=testdb02;persist security info=True;encrypt=True;trustservercertificate=False"
}
```

Ou, podemos utilizar segurança de nível de linha no Power BI incorporado e podemos pode separar os dados de cada que os utilizadores num relatório. As a Result, podemos pode aprovisionar o relatório cada cliente com o mesmo .pbix \(IU, etc.) origens de dados diferentes.

> [AZURE.NOTE] Se estiver a utilizar o **modo de importação** em vez de **modo DirectQuery**, não existe nenhuma forma de atualizar os modelos de através da API. E, no local origens de dados através do gateway do Power BI ainda não não suportada no Power BI incorporado. No entanto, irá realmente quer controlar o [blogue do Power BI](https://powerbi.microsoft.com/blog/) para o que há de novo e o que está a chegar no futuro liberta.

## <a name="authentication-and-hosting-embedding-reports-in-our-web-page"></a>Autenticação e aloja relatórios (incorporação) na nossa página web

No REST API anterior, podemos pode utilizar a tecla de acesso **AppKey** propriamente dito como cabeçalho da autorização. Uma vez que estas chamadas poderá ser resolvidas do lado do servidor de back-end, é seguro.

Mas, quando o relatório podemos incorporar na nossa página web, este tipo de informações de segurança seria processado utilizando JavaScript \(frontend). Em seguida, tem de estar protegido o valor de autorização de cabeçalho. Se os nossos tecla de acesso é identificada por um utilizador malicioso ou código malicioso, eles podem ligar qualquer operações utilizando esta chave.

Quando o relatório podemos incorporar na nossa página web, podemos tem de utilizar o token calculado em vez de tecla de acesso **AppKey**. Nossa aplicação tem de criar o Token OAuth Json Web \(JWT) que consiste os créditos e a assinatura digital calculada. Tal como ilustrado abaixo, este JWT OAuth é tokens de cadeia codificada delimitado por ponto.

![](media\power-bi-embedded-iframe\oauth-jwt.png)

Em primeiro lugar, podemos tem de preparar o valor de entrada, está assinado mais tarde. Este valor é a cadeia de codificação de url (rfc4648) base64 da seguinte json e estes são delimitados por ponto da \(.) caráter. Mais tarde, explicaremos como obter o id do relatório.

> [AZURE.NOTE] Se queremos utilizar segurança de nível de linha (RLS) com o Power BI incorporado, podemos tem também de especificar **nome de utilizador** e **funções** nas afirmações.

```
{
  "typ":"JWT",
  "alg":"HS256"
}
```

```
{
  "wid":"{workspace id}",
  "rid":"{report id}",
  "wcn":"{workspace collection name}",
  "iss":"PowerBISDK",
  "ver":"0.2.0",
  "aud":"https://analysis.windows.net/powerbi/api",
  "nbf":{start time of token expiration},
  "exp":{end time of token expiration}
}
```

Em seguida, podemos tem de criar a cadeia de codificação base64 de HMAC \(a assinatura) com SHA256 algoritmo. Este valor de teclado com a sessão iniciada é a cadeia anterior.

Por último, podemos tem de combinar a cadeia de valor e assinatura entrada utilizando período \(.) caráter. A cadeia concluída é o token de aplicação para a incorporação de relatório. Mesmo se o token de aplicação é identificado por um utilizador malicioso, estes não conseguem aceder a tecla de acesso original. Este token de aplicação irá expirar rapidamente.

Eis um exemplo PHP para que estes passos:

```
<?php
// 1. power bi access key
$accesskey = "MpaUgrTv5e...";

// 2. construct input value
$token1 = "{" .
  "\"typ\":\"JWT\"," .
  "\"alg\":\"HS256\"" .
  "}";
$token2 = "{" .
  "\"wid\":\"32960a09-6366-4208-a8bb-9e0678cdbb9d\"," . // workspace id
  "\"rid\":\"2027efc6-a308-4632-a775-b9a9186f087c\"," . // report id
  "\"wcn\":\"mypbiapp\"," . // workspace collection name
  "\"iss\":\"PowerBISDK\"," .
  "\"ver\":\"0.2.0\"," .
  "\"aud\":\"https://analysis.windows.net/powerbi/api\"," .
  "\"nbf\":" . date("U") . "," .
  "\"exp\":" . date("U" , strtotime("+1 hour")) .
  "}";
$inputval = rfc4648_base64_encode($token1) .
  "." .
  rfc4648_base64_encode($token2);

// 3. get encoded signature
$hash = hash_hmac("sha256",
    $inputval,
    $accesskey,
    true);
$sig = rfc4648_base64_encode($hash);

// 4. show result (which is the apptoken)
$apptoken = $inputval . "." . $sig;
echo($apptoken);

// helper functions
function rfc4648_base64_encode($arg) {
  $res = $arg;
  $res = base64_encode($res);
  $res = str_replace("/", "_", $res);
  $res = str_replace("+", "-", $res);
  $res = rtrim($res, "=");
  return $res;
}
?>
```

## <a name="finally-embed-the-report-into-the-web-page"></a>Por fim, incorporar o relatório na página web

Para incorporar o nosso relatório, podemos tem de obter o url de incorporação e **id de** utilizar a API REST seguintes do mapa.

**Pedido de HTTP**

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/reports
Authorization: AppKey MpaUgrTv5e...
```

**Resposta de HTTP**

```
HTTP/1.1 200 OK
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
RequestId: d4099022-405b-49d3-b3b7-3c60cf675958

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/$metadata#reports",
  "value": [
    {
      "id": "2027efc6-a308-4632-a775-b9a9186f087c",
      "name": "mydataset01",
      "webUrl": "https://app.powerbi.com/reports/2027efc6-a308-4632-a775-b9a9186f087c",
      "embedUrl": "https://embedded.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c",
      "isFromPbix": false
    }
  ]
}
```

Vamos pode incorporar o relatório no nosso web app utilizando o token de aplicação anterior.
Se olharmos para o seguinte código de exemplo, a parte antiga é o mesmo como no exemplo anterior. Na parte último, este exemplo mostra o **embedUrl** \(ver o resultado anterior) na iframe e está a registar o token de aplicação para a iframe.

> [AZURE.NOTE] Terá de alterar o valor de id do relatório para um dos seus próprios. Além disso, devido a um erro no nosso sistema de gestão de conteúdo, a tag iframe no código de exemplo é lido literalmente. Remova o texto capped da etiqueta se copiar e colar este código de exemplo.

```
    <?php
    // 1. power bi access key
    $accesskey = "MpaUgrTv5e...";

    // 2. construct input value
    $token1 = "{" .
      "\"typ\":\"JWT\"," .
      "\"alg\":\"HS256\"" .
      "}";
    $token2 = "{" .
      "\"wid\":\"32960a09-6366-4208-a8bb-9e0678cdbb9d\"," . // workspace id
      "\"rid\":\"2027efc6-a308-4632-a775-b9a9186f087c\"," . // report id
      "\"wcn\":\"mypbiapp\"," . // workspace collection name
      "\"iss\":\"PowerBISDK\"," .
      "\"ver\":\"0.2.0\"," .
      "\"aud\":\"https://analysis.windows.net/powerbi/api\"," .
      "\"nbf\":" . date("U") . "," .
      "\"exp\":" . date("U" , strtotime("+1 hour")) .
      "}";
    $inputval = rfc4648_base64_encode($token1) .
      "." .
      rfc4648_base64_encode($token2);

    // 3. get encoded signature value
    $hash = hash_hmac("sha256",
        $inputval,
        $accesskey,
        true);
    $sig = rfc4648_base64_encode($hash);

    // 4. get apptoken
    $apptoken = $inputval . "." . $sig;

    // helper functions
    function rfc4648_base64_encode($arg) {
      $res = $arg;
      $res = base64_encode($res);
      $res = str_replace("/", "_", $res);
      $res = str_replace("+", "-", $res);
      $res = rtrim($res, "=");
      return $res;
    }
    ?>
    <!DOCTYPE html>
    <html>
    <head>
      <meta charset="utf-8" />
      <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <title>Test page</title>
      <meta name="viewport" content="width=device-width, initial-scale=1">
    </head>
    <body>
      <button id="btnView">View Report !</button>
      <div id="divView">
        <**REMOVE THIS CAPPED TEXT IF COPIED** iframe id="ifrTile" width="100%" height="400"></iframe>
      </div>
      <script>
        (function () {
          document.getElementById('btnView').onclick = function() {
            var iframe = document.getElementById('ifrTile');
            iframe.src = 'https://embedded.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c';
            iframe.onload = function() {
              var msgJson = {
                action: "loadReport",
                accessToken: "<?=$apptoken?>",
                height: 500,
                width: 722
              };
              var msgTxt = JSON.stringify(msgJson);
              iframe.contentWindow.postMessage(msgTxt, "*");
            };
          };
        }());
      </script>
    </body>
```

E Eis os nossos resultado:

![](media\power-bi-embedded-iframe\view-report.png)

Neste momento, Power BI incorporado apenas mostra o relatório na iframe. No entanto, atento o [Blogue do Power BI](). Melhorias futuras podem utilizar novo do lado do cliente APIs que irá diga-nos enviar informações para a iframe, bem como obter informações de. Conteúdos apelativo!


## <a name="see-also"></a>Consulte também
- [Autenticação e autorização no Power BI incorporado](power-bi-embedded-app-token-flow.md)
