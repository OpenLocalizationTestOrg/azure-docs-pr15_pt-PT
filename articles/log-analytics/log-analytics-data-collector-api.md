<properties
    pageTitle="Inicie sessão Analytics HTTP recolha API | Microsoft Azure"
    description="Pode utilizar a API Recolectores do registo de análise HTTP dados para adicionar dados JSON de mensagem para o repositório de análise de registo a partir de qualquer cliente que pode ligar a API do resto. Este artigo descreve como utilizar a API e tem exemplos de como publicar dados ao utilizar linguagens de programação diferentes."
    services="log-analytics"
    documentationCenter=""
    authors="bwren"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/26/2016"
    ms.author="bwren"/>


# <a name="log-analytics-http-data-collector-api"></a>Inicie sessão Analytics HTTP recolha API

Ao utilizar a API do Azure registo Analytics HTTP dados Recolectores, pode adicionar dados de mensagem JavaScript objeto notação (JSON) para o repositório de análise de registo a partir de qualquer cliente que pode ligar a API REST. Ao utilizar este método, pode enviar dados a partir de aplicações de terceiros ou de scripts, como a partir de um livro de execuções no Azure automatização.  

## <a name="create-a-request"></a>Criar um pedido de

As duas tabelas apresentam os atributos que são necessários para cada pedido para o registo de análise HTTP dados Recolectores API. Descrevemos cada atributo mais detalhadamente posteriormente neste artigo.

### <a name="request-uri"></a>URI pedido

| Atributo | Propriedade |
|:--|:--|
| Método | MENSAGEM |
| URI | https://\<IDCliente\>.ods.opinsights.azure.com/api/logs?api-version=2016-04-01 |
| Tipo de conteúdo | aplicação/json |

### <a name="request-uri-parameters"></a>Parâmetros de URI pedido
| Parâmetro | Descrição |
|:--|:--|
| IDCliente  | O identificador exclusivo para a área de trabalho do conjunto de aplicações do Microsoft operações gestão. |
| Recurso    | O nome do recurso API: / api/registos. |
| Versão da API | A versão do API para utilizar com este pedido. Atualmente, é 2016-04-01. |

### <a name="request-headers"></a>Pedido de cabeçalhos
| Cabeçalho | Descrição |
|:--|:--|
| Autorização | A assinatura de autorização. Posteriormente neste artigo, pode obter informações sobre como criar um cabeçalho HMAC SHA256. |
| Tipo de registo | Especificar o tipo de registo dos dados que estão a ser submetidos. Atualmente, o tipo de registo suporta apenas alfa carateres. Não suporta valores numéricos ou carateres especiais. |
| x-ms-date | A data em que o pedido foi processado, no formato de RFC 1123. |
| campo Time gerado | O nome de um campo nos dados que contém o carimbo de data/hora do item de dados. Se especificar um campo, em seguida, os seus conteúdos são utilizados para **TimeGenerated**. Se este campo não é especificado, a predefinição para **TimeGenerated** é a hora em que a mensagem é penetração. O conteúdo do campo de mensagem deve seguir o formato de ISO 8601 aaaa-MM-DDThh:mm:ssZ. |


## <a name="authorization"></a>Autorização

Qualquer pedido à API do registo de análise HTTP dados Recolectores tem de incluir um cabeçalho de autorização. Para autenticar um pedido, tem de assinar o pedido com a página principal ou a tecla secundária para a área de trabalho que está a fazer o pedido. Passe, em seguida, essa assinatura como parte do pedido.   

Aqui é o formato para o cabeçalho de autorização:

```
Authorization: SharedKey <WorkspaceID>:<Signature>
```

*WorkspaceID* é o identificador exclusivo para a área de trabalho do conjunto de aplicações de gestão de operações. *A assinatura* é um [Código de autenticação de mensagem (HMAC) baseadas em Hash](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) que é construída do pedido e, em seguida, é calculado utilizando o [algoritmo de SHA256](https://msdn.microsoft.com/library/system.security.cryptography.sha256.aspx). Em seguida, codificá-lo ao utilizar a codificação Base64.

Utilize este formato para codificar a cadeia de assinatura **SharedKey** :

```
StringToSign = VERB + "\n" +
               Content-Length + "\n" +
               Content-Type + "\n" +
               x-ms-date + "\n" +
               "/api/logs";
```

Eis um exemplo de uma cadeia de assinatura:

```
POST\n1024\napplication/json\nx-ms-date:Mon, 04 Apr 2016 08:00:00 GMT\n/api/logs
```

Quando tiver a cadeia de assinatura, codificá-lo ao utilizar o algoritmo de HMAC SHA256 na cadeia codificado em UTF-8 e, em seguida, descodificar o resultado como Base64. Utilize este formato:

```
Signature=Base64(HMAC-SHA256(UTF8(StringToSign)))
```

Os exemplos nas secções seguintes tem o código de exemplo para o ajudar a criar um cabeçalho de autorização.

## <a name="request-body"></a>Corpo do pedido

Corpo da mensagem deve estar no JSON. Deve incluir um ou mais registos com os pares de nome e o valor de propriedade neste formato:

```
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
}
```

Pode batch vários registos em conjunto num único pedido utilizando o formato seguinte. Todos os registos tem de ser o mesmo tipo de registo.

```
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
},
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
}
```

## <a name="record-type-and-properties"></a>Tipo de registo e propriedades

Definir um tipo de registo personalizados quando submeter dados através da Recolectores API do registo de análise HTTP dados. Atualmente, não é possível escrever dados existentes tipos de registo que foram criados por outros tipos de dados e soluções. Lê os dados de entrada e, em seguida, cria propriedades que correspondem aos tipos de dados dos valores que introduzir de análise de registo.

Cada pedido à API Analytics registo tem de incluir um cabeçalho de **Tipo de registo** com o nome do tipo de registo. O sufixo **_CL** é acrescentada automaticamente para o nome que introduzir para distingui-lo a partir de outros tipos de registo como um registo personalizado. Por exemplo, se introduzir o nome **MyNewRecordType**, o registo de análise cria um registo com o tipo de **MyNewRecordType_CL**. Isto ajuda a garantir que não existirem conflitos entre os nomes dos tipos de criados pelo utilizador e os enviados no Microsoft solutions atuais ou futuras.

Para identificar o tipo de dados de uma propriedade, o registo de análise adiciona um sufixo para o nome da propriedade. Se uma propriedade contiver um valor nulo, a propriedade não está incluída nesse registo. Esta tabela lista o tipo de dados de propriedade e sufixo correspondente:

| Tipo de dados de propriedade | Sufixo |
|:--|:--|
| Cadeia    | _s |
| Booleano   | _b |
| Dupla    | _d |
| Data/hora | Fechar Hermet |
| GUID      | _g |


O tipo de dados que utiliza a análise de registo para cada propriedade depende do facto do tipo de registo do novo registo já existe.

- Se o tipo de registo não existir, registo Analytics cria um novo. Análise de registo utiliza a inferência de tipo JSON para determinar o tipo de dados para cada propriedade do novo registo.
- Se o tipo de registo existir, tentativas de análise de registo para criar um novo registo com base em propriedades existentes. Se o tipo de dados para uma propriedade no novo registo não correspondem ao e não pode ser convertida para o tipo de existente ou se o registo inclui uma propriedade que não existe, registo Analytics cria uma nova propriedade que tem o sufixo relevante.

Por exemplo, esta entrada de submissão pretende criar um registo com três propriedades, **number_d**, **boolean_b**e **string_s**:

![Registo de exemplo 1](media/log-analytics-data-collector-api/record-01.png)

Se, em seguida, submetido esta entrada seguinte, com todos os valores formatados como cadeias, as propriedades não pretende alterar. Tipos de dados existentes podem ser convertidas estes valores:

![Registo de exemplo 2](media/log-analytics-data-collector-api/record-02.png)

Mas, se, em seguida, a efetuar esta submissão seguinte, registo Analytics pretender criar a nova propriedades **boolean_d** e **string_d**. Não não possível converter estes valores:

![Registo de exemplo 3](media/log-analytics-data-collector-api/record-03.png)

Se submetido, em seguida, a seguinte entrada, antes do tipo de registo que foi criado, análises de registo de criar um registo com três propriedades, **núm_s**, **boolean_s**e **string_s**. Esta entrada, cada um dos valores iniciais é formatada como uma cadeia de:

![Registo de exemplo 4](media/log-analytics-data-collector-api/record-04.png)


## <a name="data-limits"></a>Limites de dados
Existem algumas restrições à volta dos dados publicados para a coleção de dados de análise do registo API.

- Máximo de 30 MB por mensagem à API dos Recolectores registo a análise de dados. Este é um limite de tamanho de uma única mensagem. Se os dados a partir de um único registo que excede 30 MB, deve dividir os dados por excesso para o menores médias blocos e enviá-las em simultâneo. 
- Máximo de limite de 32 KB para valores de campo. Se o valor do campo for maior que 32 KB, os dados serão truncados. 
- Recomendado número máximo de campos para um determinado tipo é 50. Este é um limite prático a partir da eficiência de utilização e pesquisa experiência perspetiva.  


## <a name="return-codes"></a>Códigos de retorno

O código de estado HTTP 202 significa que o pedido foi aceite para processamento, mas processamento ainda não foi concluída. Isto indica que a operação concluída com êxito.

Esta tabela lista o conjunto completo de códigos de estado que o serviço poderá devolver:

| Código | Estado | Código de erro | Descrição |
|:--|:--|:--|:--|
| 202 | Aceites |  | O pedido foi aceite com êxito. |
| 400 | Pedido inválido | InactiveCustomer | A área de trabalho foi fechada. |
| 400 | Pedido inválido | InvalidApiVersion | A versão da API que especificou não foi reconhecida pelo serviço. |
| 400 | Pedido inválido | InvalidCustomerId | O ID da área de trabalho especificado é inválido. |
| 400 | Pedido inválido | InvalidDataFormat | Foi submetido JSON inválido. O corpo da resposta pode conter mais informações sobre como resolver o erro. |
| 400 | Pedido inválido | InvalidLogType | O tipo de registo especificado contidas carateres especiais ou valores numéricos. |
| 400 | Pedido inválido | MissingApiVersion | A versão da API não foi especificada. |
| 400 | Pedido inválido | MissingContentType | O tipo de conteúdo não foi especificado. |
| 400 | Pedido inválido | MissingLogType | O tipo de registo de valor obrigatório não foi especificado. |
| 400 | Pedido inválido | UnsupportedContentType | O tipo de conteúdo não foi definido para **aplicação/json**. |
| 403 | Proibido | InvalidAuthorization | O serviço falhou autenticar o pedido. Certifique-se de que a chave de ID e ligação de área de trabalho são válidos. |
| 500 | Erro de servidor interno | UnspecifiedError | O serviço encontrou um erro interno. Volte a tentar o pedido. |
| 503 | Serviço indisponível | ServiceUnavailable | O serviço está disponível para receber pedidos. Volte a tentar seu pedido. |

## <a name="query-data"></a>Dados da consulta

Para consultar dados submetidos por dados Recolectores API do registo de análise HTTP, procurar registos com o **tipo** for igual ao valor **LogType** que o utilizador especificado, anexadas com **_CL**. Por exemplo, se tiver utilizado **MyCustomLog**, em seguida, deve devolver todos os registos com **tipo = MyCustomLog_CL**.


## <a name="sample-requests"></a>Pedidos de exemplo

As secções seguintes, encontrará exemplos de como submeter dados para o registo a análise de HTTP dados Recolectores API utilizando diferentes linguagens de programação.

Para cada amostra, siga estes passos para definir as variáveis para o cabeçalho de autorização:

1. No portal do conjunto de aplicações de gestão de operações, selecione o mosaico **Definições** e, em seguida, selecione o separador de **Origens ligadas** .
2. À direita do **ID de área de trabalho**, selecione o ícone de copiar e, em seguida, cole o ID como o valor da variável de **ID do cliente** .
3. À direita da **Chave primária**, selecione o ícone de copiar e, em seguida, cole o ID como o valor da variável **Chave partilhada** .

Em alternativa, pode alterar as variáveis para o tipo de registo e dados JSON.

### <a name="powershell-sample"></a>Exemplo de PowerShell

```
# Replace with your Workspace ID
$CustomerId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"  

# Replace with your Primary Key
$SharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Specify the name of the record type that you'll be creating
$LogType = "MyRecordType"

# Specify a field with the created time for the records
$TimeStampField = "DateValue"


# Create two records with the same set of properties to create
$json = @"
[{  "StringValue": "MyString1",
    "NumberValue": 42,
    "BooleanValue": true,
    "DateValue": "2016-05-12T20:00:00.625Z",
    "GUIDValue": "9909ED01-A74C-4874-8ABF-D2678E3AE23D"
},
{   "StringValue": "MyString2",
    "NumberValue": 43,
    "BooleanValue": false,
    "DateValue": "2016-05-12T20:00:00.625Z",
    "GUIDValue": "8809ED01-A74C-4874-8ABF-D2678E3AE23D"
}]
"@

# Create the function to create the authorization signature
Function Build-Signature ($customerId, $sharedKey, $date, $contentLength, $method, $contentType, $resource)
{
    $xHeaders = "x-ms-date:" + $date
    $stringToHash = $method + "`n" + $contentLength + "`n" + $contentType + "`n" + $xHeaders + "`n" + $resource

    $bytesToHash = [Text.Encoding]::UTF8.GetBytes($stringToHash)
    $keyBytes = [Convert]::FromBase64String($sharedKey)

    $sha256 = New-Object System.Security.Cryptography.HMACSHA256
    $sha256.Key = $keyBytes
    $calculatedHash = $sha256.ComputeHash($bytesToHash)
    $encodedHash = [Convert]::ToBase64String($calculatedHash)
    $authorization = 'SharedKey {0}:{1}' -f $customerId,$encodedHash
    return $authorization
}


# Create the function to create and post the request
Function Post-OMSData($customerId, $sharedKey, $body, $logType)
{
    $method = "POST"
    $contentType = "application/json"
    $resource = "/api/logs"
    $rfc1123date = [DateTime]::UtcNow.ToString("r")
    $contentLength = $body.Length
    $signature = Build-Signature `
        -customerId $customerId `
        -sharedKey $sharedKey `
        -date $rfc1123date `
        -contentLength $contentLength `
        -fileName $fileName `
        -method $method `
        -contentType $contentType `
        -resource $resource
    $uri = "https://" + $customerId + ".ods.opinsights.azure.com" + $resource + "?api-version=2016-04-01"

    $headers = @{
        "Authorization" = $signature;
        "Log-Type" = $logType;
        "x-ms-date" = $rfc1123date;
        "time-generated-field" = $TimeStampField;
    }

    $response = Invoke-WebRequest -Uri $uri -Method $method -ContentType $contentType -Headers $headers -Body $body -UseBasicParsing
    return $response.StatusCode

}

# Submit the data to the API endpoint
Post-OMSData -customerId $customerId -sharedKey $sharedKey -body ([System.Text.Encoding]::UTF8.GetBytes($json)) -logType $logType  
```

### <a name="c-sample"></a>Exemplo c#

```
using System;
using System.Net;
using System.Security.Cryptography;

namespace OIAPIExample
{
    class ApiExample
    {
// An example JSON object, with key/value pairs
        static string json = @"[{""DemoField1"":""DemoValue1"",""DemoField2"":""DemoValue2""},{""DemoField1"":""DemoValue3"",""DemoField2"":""DemoValue4""}]";

// Update customerId to your Operations Management Suite workspace ID
        static string customerId = "xxxxxxxx-xxx-xxx-xxx-xxxxxxxxxxxx";

// For sharedKey, use either the primary or the secondary Connected Sources client authentication key   
        static string sharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";

// LogName is name of the event type that is being submitted to Log Analytics
        static string LogName = "DemoExample";

// You can use an optional field to specify the timestamp from the data. If the time field is not specified, Log Analytics assumes the time is the message ingestion time
        static string TimeStampField = "";

        static void Main()
        {
// Create a hash for the API signature
            var datestring = DateTime.UtcNow.ToString("r");
            string stringToHash = "POST\n" + json.Length + "\napplication/json\n" + "x-ms-date:" + datestring + "\n/api/logs";
            string hashedString = BuildSignature(stringToHash, sharedKey);
            string signature = "SharedKey " + customerId + ":" + hashedString;

            PostData(signature, datestring, json);
        }

// Build the API signature
        public static string BuildSignature(string message, string secret)
        {
            var encoding = new System.Text.ASCIIEncoding();
            byte[] keyByte = Convert.FromBase64String(secret);
            byte[] messageBytes = encoding.GetBytes(message);
            using (var hmacsha256 = new HMACSHA256(keyByte))
            {
                byte[] hash = hmacsha256.ComputeHash(messageBytes);
                return Convert.ToBase64String(hash);
            }
        }

// Send a request to the POST API endpoint
        public static void PostData(string signature, string date, string json)
        {
            string url = "https://"+ customerId +".ods.opinsights.azure.com/api/logs?api-version=2016-04-01";
            using (var client = new WebClient())
            {
                client.Headers.Add(HttpRequestHeader.ContentType, "application/json");
                client.Headers.Add("Log-Type", LogName);
                client.Headers.Add("Authorization", signature);
                client.Headers.Add("x-ms-date", date);
                client.Headers.Add("time-generated-field", TimeStampField);
                client.UploadString(new Uri(url), "POST", json);
            }
        }
    }
}
```

### <a name="python-sample"></a>Exemplo de Python

```
import json
import requests
import datetime
import hashlib
import hmac
import base64

# Update the customer ID to your Operations Management Suite workspace ID
customer_id = 'xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

# For the shared key, use either the primary or the secondary Connected Sources client authentication key   
shared_key = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# The log type is the name of the event that is being submitted
log_type = 'WebMonitorTest'

# An example JSON web monitor object
json_data = [{
   "slot_ID": 12345,
    "ID": "5cdad72f-c848-4df0-8aaa-ffe033e75d57",
    "availability_Value": 100,
    "performance_Value": 6.954,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "true"
},
{   
    "slot_ID": 67890,
    "ID": "b6bee458-fb65-492e-996d-61c4d7fbb942",
    "availability_Value": 100,
    "performance_Value": 3.379,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "false"
}]
body = json.dumps(json_data)

#####################
######Functions######  
#####################

# Build the API signature
def build_signature(customer_id, shared_key, date, content_length, method, content_type, resource):
    x_headers = 'x-ms-date:' + date
    string_to_hash = method + "\n" + str(content_length) + "\n" + content_type + "\n" + x_headers + "\n" + resource
    bytes_to_hash = bytes(string_to_hash).encode('utf-8')  
    decoded_key = base64.b64decode(shared_key)
    encoded_hash = base64.b64encode(hmac.new(decoded_key, bytes_to_hash, digestmod=hashlib.sha256).digest())
    authorization = "SharedKey {}:{}".format(customer_id,encoded_hash)
    return authorization

# Build and send a request to the POST API
def post_data(customer_id, shared_key, body, log_type):
    method = 'POST'
    content_type = 'application/json'
    resource = '/api/logs'
    rfc1123date = datetime.datetime.utcnow().strftime('%a, %d %b %Y %H:%M:%S GMT')
    content_length = len(body)
    signature = build_signature(customer_id, shared_key, rfc1123date, content_length, method, content_type, resource)
    uri = 'https://' + customer_id + '.ods.opinsights.azure.com' + resource + '?api-version=2016-04-01'

    headers = {
        'content-type': content_type,
        'Authorization': signature,
        'Log-Type': log_type,
        'x-ms-date': rfc1123date
    }

    response = requests.post(uri,data=body, headers=headers)
    if (response.status_code == 202):
        print 'Accepted'
    else:
        print "Response code: {}".format(response.status_code)

post_data(customer_id, shared_key, body, log_type)
```

## <a name="next-steps"></a>Próximos passos

- Utilize o [Estruturador de vista](log-analytics-view-designer.md) para criar vistas personalizadas nos dados submetidos.
