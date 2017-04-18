<properties
    pageTitle="Limites de aplicação de lógica e configuração | Microsoft Azure"
    description="Descrição geral dos limites de serviço e os valores de configuração disponíveis para as aplicações de lógica."
    services="logic-apps"
    documentationCenter=".net,nodejs,java"
    authors="jeffhollan"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="jehollan"/>

# <a name="logic-app-limits-and-configuration"></a>Limites de aplicação de lógica e de configuração

Abaixo, encontrará informações sobre os limites de atuais e detalhes de configuração para aplicações do Azure lógica.

## <a name="limits"></a>Limites

### <a name="http-request-limits"></a>Limites de pedido HTTP

Estes são os limites para uma chamada do pedido e/ou conexão HTTP único

#### <a name="timeout"></a>Tempo limite

|Nome|Limite|Notas|
|----|----|----|
|Tempo limite do pedido|1 minuto|Um [padrão de assíncrono](app-service-logic-create-api-app.md) ou [até o ciclo](app-service-logic-loops-and-scopes.md) pode o Adquirente na íntegra conforme necessário|

#### <a name="message-size"></a>Tamanho da mensagem

|Nome|Limite|Notas|
|----|----|----|
|Tamanho da mensagem|50 MB|Algumas APIs e conectores poderá não suportar 50MB.  Pedido de accionador suporta até 25MB|
|Limite de avaliação de expressão|131.072 carateres|`@concat()`, `@base64()`, `string` não pode ser superior a esta|

#### <a name="retry-policy"></a>Volte a tentar política

|Nome|Limite|Notas|
|----|----|----|
|Tentativas de repetição|4|Pode configurar com o [parâmetro de política de repetir](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)|
|Volte a tentar atraso máximo|1 hora|Pode configurar com o [parâmetro de política de repetir](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)|
|Volte a tentar atraso mínimo|mínimo 20|Pode configurar com o [parâmetro de política de repetir](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)|

### <a name="run-duration-and-retention"></a>Executar duração e retenção

Estes são os limites para uma aplicação de lógica única executar.

|Nome|Limite|Notas|
|----|----|----|
|Duração da execução|cerca de 90 dias||
|Retenção de armazenamento|cerca de 90 dias|Esta é a partir da hora de início executar|
|Intervalo de periodicidade mínimo|15 sec||
|Intervalo de periodicidade máximo|500 dias||


### <a name="looping-and-debatching-limits"></a>Ciclo e debatching limites

Estes são os limites para uma aplicação de lógica única executar.

|Nome|Limite|Notas|
|----|----|----|
|ForEach itens|5.000|Pode utilizar a [ação de consulta](../connectors/connectors-native-query.md) para filtrar matrizes maiores conforme necessário|
|Até iterações|10.000||
|SplitOn itens|5.000||
|ForEach paralelismo|20|Pode configurar para um foreach sequencial adicionando `"operationOptions": "Sequential"` para o `foreach` ação|


### <a name="throughput-limits"></a>Limites de débito

Estes são os limites de uma instância da aplicação de lógica único. 

|Nome|Limite|Notas|
|----|----|----|
|Accionadores por segundo|100|Pode distribuir fluxos de trabalho entre várias aplicações, conforme necessário|

### <a name="definition-limits"></a>Definição de limites

Estes são os limites para obter uma definição de aplicação única lógica.

|Nome|Limite|Notas|
|----|----|----|
|Ações no ForEach|1|Pode adicionar aninhados fluxos de trabalho para alargar isto conforme necessário|
|Ações por fluxo de trabalho|60|Pode adicionar aninhados fluxos de trabalho para alargar isto conforme necessário|
|Permitido profundidade aninhamento de ação|5|Pode adicionar aninhados fluxos de trabalho para alargar isto conforme necessário|
|Fluxos de por região por subscrição|1000||
|Accionadores por fluxo de trabalho|10||
|Carateres de máximo por expressão|8,192||
|Máximo `trackedProperties` tamanho em carateres|16.000|
|`action`/`trigger`limite de nomes|80||
|`description`limite de comprimento|256||
|`parameters`limite|50||
|`outputs`limite|10||

## <a name="configuration"></a>Configuração

### <a name="ip-address"></a>Endereço IP

Chamadas criadas a partir de uma [conexão](../connectors/apis-list.md) serão proveniente do endereço IP especificado abaixo.

Chamadas efetuadas através de uma aplicação de lógica diretamente (ou seja, através de [HTTP](../connectors/connectors-native-http.md) ou [HTTP + Swagger](../connectors/connectors-native-http-swagger.md)) poderão vir a partir de qualquer um dos [Intervalos de IP do Centro de dados de Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653).

|Região de aplicação de lógica|IP de saída|
|-----|----|
|Leste Austrália|40.126.251.213|
|Sudeste Austrália|40.127.80.34|
|Sul do Brasil|191.232.38.129|
|Índia central|104.211.98.164|
|Central (EUA)|40.122.49.51|
|Este asiático|23.99.116.181|
|Leste dos EUA|191.237.41.52|
|Leste dos EUA 2|104.208.233.100|
|Japão leste|40.115.186.96|
|Japão oeste|40.74.130.77|
|América do Norte Central (EUA)|65.52.218.230|
|Europa Norte|104.45.93.9|
|Sul Central (EUA)|104.214.70.191|
|Sudeste asiático|13.76.231.68|
|Sul Índia|104.211.227.225|
|Europa Ocidental|40.115.50.13|
|Índia Ocidental|104.211.161.203|
|Ocidental dos e.u.a.|104.40.51.248|


## <a name="next-steps"></a>Próximos passos  

- Para começar a lógica de aplicações, siga o tutorial de [criar uma aplicação de lógica](app-service-logic-create-a-logic-app.md) .  
- [Ver exemplos e cenários comuns](app-service-logic-examples-and-scenarios.md)
- [Pode automatizar processos de negócio com aplicações de lógica](http://channel9.msdn.com/Events/Build/2016/T694) 
- [Saiba como integrar o seu sistemas com aplicações de lógica](http://channel9.msdn.com/Events/Build/2016/P462)