<properties
 pageTitle="Guia do programador - linguagem de consulta | Microsoft Azure"
 description="Guia do programador IoT concentrador Azure - descrição da linguagem de consulta utilizada para obter informações sobre gémeos, métodos e tarefas a partir do seu centro IoT"
 services="iot-hub"
 documentationCenter=".net"
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="elioda"/>

# <a name="reference---query-language-for-twins-and-jobs"></a>Referência - linguagem de consulta para gémeos e tarefas de implementação

## <a name="overview"></a>Descrição geral

Concentrador IoT fornece um idioma de SQL gosto poderoso para obter informações relativas ao [gémeos dispositivo] [ lnk-twins] e [tarefas de implementação][lnk-jobs]. Este artigo apresenta:

* Introdução às funcionalidades principais da linguagem de consulta do concentrador de IoT, e
* Descrição detalhada do idioma.

## <a name="getting-started-with-twin-queries"></a>Introdução ao consultas duplos

[Gémeos dispositivo] [ lnk-twins] pode conter objetos JSON arbitrários como etiquetas e propriedades. Concentrador IoT permite que gémeos de dispositivo de consulta como um único documento JSON que contém todas as informações de duplos.
Por exemplo, suponha que seu gémeos de concentrador IoT tem a seguinte estrutura:

        {                                                                      
            "deviceId": "myDeviceId",                                            
            "etag": "AAAAAAAAAAc=",                                              
            "tags": {                                                            
                "location": {                                                      
                    "region": "US",                                                  
                    "plant": "Redmond43"                                             
                }                                                                  
            },                                                                   
            "properties": {                                                      
                "desired": {                                                       
                    "telemetryConfig": {                                             
                        "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",            
                        "sendFrequencyInSecs": 300                                          
                    },                                                               
                    "$metadata": {                                                   
                    ...                                                     
                    },                                                               
                    "$version": 4                                                    
                },                                                                 
                "reported": {                                                      
                    "connectivity": {                                                
                        "type": "cellular"                            
                    },                                                               
                    "telemetryConfig": {                                             
                        "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",            
                        "sendFrequencyInSecs": 300,                                         
                        "status": "Success"                                            
                    },                                                               
                    "$metadata": {                                                   
                    ...                                                
                    },                                                               
                    "$version": 7                                                    
                }                                                                  
            }                                                                    
        }

Concentrador IoT expõe gémeos o dispositivo, como uma colecção de documento designado por **dispositivos**.
Por isso, a seguinte consulta obtém todo o conjunto de gémeos dispositivo:

        SELECT * FROM devices

> [AZURE.NOTE] [IoT concentrador SDK] [ lnk-hub-sdks] suporta paginação de resultados de grandes dimensões.

Concentrador IoT permite para obter gémeos com arbitrários condições de filtragem. Por exemplo,

        SELECT * FROM devices
        WHERE tags.location.region = 'US'

obtém os gémeos com a etiqueta **location.region** definir **-nos**.
Operadores booleanos e comparações aritméticas são suportadas, assim, por exemplo

        SELECT * FROM devices
        WHERE tags.location.region = 'US'
            AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60

obtém todos os gémeos localizados nos EUA configurado para enviar telemetria menor frequência minuto. Como comodidade, também é possível utilizar constantes de matriz em conjunto com o **IN** e operadores **NIN** (não em). Por exemplo,

        SELECT * FROM devices
        WHERE property.reported.connectivity IN ['wired', 'wifi']

obtém todos os gémeos que comunicado conta ou wired conectividade. Referir a [cláusula WHERE] [ lnk-query-where] secção para a referência completa das capacidades de filtragem.

Agrupamento e agregações também são suportadas. Por exemplo,

        SELECT properties.reported.telemetryConfig.status AS status,
            COUNT() AS numberOfDevices
        FROM devices
        GROUP BY properties.reported.telemetryConfig.status

Devolve a contagem dos dispositivos de cada Estado de configuração de telemetria.

        [
            {
                "numberOfDevices": 3,
                "status": "Success"
            },
            {
                "numberOfDevices": 2,
                "status": "Pending"
            },
            {
                "numberOfDevices": 1,
                "status": "Error"
            }
        ]

O exemplo acima ilustra uma situação onde três dispositivos comunicado configuração bem sucedida, dois ainda estão a aplicar a configuração e um comunicou um erro.

### <a name="c-example"></a>Exemplo c#

A funcionalidade de consulta é exposta pelo [serviço c# SDK] [ lnk-hub-sdks] no a classe de **RegistryManager** .
Eis um exemplo de uma consulta simples:

        var query = registryManager.CreateQuery("SELECT * FROM devices", 100);
        while (query.HasMoreResults)
        {
            var page = await query.GetNextAsTwinAsync();
            foreach (var twin in page) 
            {
                // do work on twin object
            }
        }

Tenha em atenção como o objeto de **consulta** é criada uma instância com um tamanho de página (até 1000) e, em seguida, podem ser obtidas várias páginas ao contactar o suporte os métodos de **GetNextAsTwinAsync** várias vezes.
É importante ter em atenção que o objeto de consulta expõe múltiplo **seguinte\***, consoante a opção de desserialização obrigatório pela consulta, ou seja, duplos ou objetos ou Json simples para ser utilizado quando utilizar projecções de cargo.

### <a name="node-example"></a>Exemplo de nó

A funcionalidade de consulta é exposta pelo [serviço nó SDK] [ lnk-hub-sdks] no o objeto de **registo** .
Eis um exemplo de uma consulta simples:

        var query = registry.createQuery('SELECT * FROM devices', 100);
        var onResults = function(err, results) {
            if (err) {
                console.error('Failed to fetch the results: ' + err.message);
            } else {
                // Do something with the results
                results.forEach(function(twin) {
                    console.log(twin.deviceId);
                });

                if (query.hasMoreResults) {
                    query.nextAsTwin(onResults);
                }
            }
        };
        query.nextAsTwin(onResults);

Tenha em atenção como o objeto de **consulta** é criada uma instância com um tamanho de página (até 1000) e, em seguida, podem ser obtidas várias páginas ao contactar o suporte os métodos de **nextAsTwin** várias vezes.
É importante ter em atenção que o objeto de consulta expõe múltiplo **seguinte\***, consoante a opção de desserialização obrigatório pela consulta, ou seja, duplos ou objetos ou Json simples para ser utilizado quando utilizar projecções de cargo.

### <a name="limitations"></a>Limitações

Atualmente, projecções só são suportadas quando utilizar agregações, ou seja, só podem utilizar consultas não agregado `SELECT *`. Além disso, agregação só são suportadas em conjunto com o agrupamento.

## <a name="getting-started-with-jobs-queries"></a>Começar a trabalhar com consultas de tarefas

[Tarefas de] [ lnk-jobs] fornece uma maneira de executar operações em conjuntos de dispositivos. Cada duplos dispositivo contém as informações das tarefas que faz parte numa coleção de denominado **tarefas**.
De forma lógica

        {                                                                      
            "deviceId": "myDeviceId",                                            
            "etag": "AAAAAAAAAAc=",                                              
            "tags": {                                                            
                ...                                                              
            },                                                                   
            "properties": {                                                      
                ...                                                                 
            },
            "jobs": [
                { 
                    "deviceId": "myDeviceId",
                    "jobId": "myJobId",    
                    "jobType": "scheduleTwinUpdate",            
                    "status": "completed",                    
                    "startTimeUtc": "2016-09-29T18:18:52.7418462",
                    "endTimeUtc": "2016-09-29T18:20:52.7418462",
                    "createdDateTimeUtc": "2016-09-29T18:18:56.7787107Z",
                    "lastUpdatedDateTimeUtc": "2016-09-29T18:18:56.8894408Z",
                    "outcome": {
                        "deviceMethodResponse": null   
                    }                                         
                },
                ...
            ]                                                             
        }

Atualmente, este é queriable como **devices.jobs** o idioma de consulta IoT concentrador.

Por exemplo, para obter todas as tarefas (agendadas e anteriores) que afetam a um único dispositivo, pode utilizar a seguinte consulta:

        SELECT * FROM devices.jobs
        WHERE devices.jobs.deviceId = 'myDeviceId'

Tenha em atenção como esta consulta fornece o estado de específicas do dispositivo (e possivelmente a resposta método directo) de cada tarefa devolvida.
Também é possível filtrar com arbitrários condições booleanas em todas as propriedades dos objetos na coleção de **devices.jobs** .
Por exemplo, a seguinte consulta:

        SELECT * FROM devices.jobs
        WHERE devices.jobs.deviceId = 'myDeviceId'
            AND devices.jobs.jobType = 'scheduleTwinUpdate'
            AND devices.jobs.status = 'completed'
            AND devices.jobs.createdTimeUtc > '2016-09-01'

obtém todos os duplos concluídas trabalhos de actualização para dispositivo **myDeviceId** que foram criados após Setembro de 2016.

Também é possível obter os resultados por dispositivo de uma única tarefa.

        SELECT * FROM devices.jobs
        WHERE devices.jobs.jobId = 'myJobId'

### <a name="limitations"></a>Limitações
Atualmente, consultas no **devices.jobs** não suportam:

* Projecções, ou seja, apenas `SELECT *` é possível;
* Condições que se referem à duplos dispositivo, para além de propriedades da tarefa conforme mostrado acima;
* Efectuar agregações, por exemplo, contagem, média, agrupar por.

## <a name="basics-of-an-iot-hub-query"></a>Noções básicas de uma consulta de IoT concentrador

Todas as consultas IoT concentrador consiste em of um SELECT e de cláusulas e ao WHERE opcional e GROUP BY cláusulas. Cada consulta é executada numa coleção de documentos JSON, por exemplo, gémeos de dispositivo. A cláusula FROM indica a coleção de documento para ser sujeita a iteração no (**dispositivos** ou **devices.jobs**). Em seguida, o filtro na cláusula WHERE é aplicado. No caso de agregações, os resultados deste passo são agrupados, conforme especificado na cláusula GROUP BY e, para cada grupo, uma linha é gerada conforme especificado na cláusula SELECT.

        SELECT <select_list>
        FROM <from_specification>
        [WHERE <filter_condition>]
        [GROUP BY <group_specification>]

## <a name="from-clause"></a>Cláusula FROM

**A < from_specification >** cláusula FROM pode partem do pressuposto de apenas dois valores: **de dispositivos**, para gémeos de dispositivo de consulta ou **devices.jobs de**, para detalhes da consulta tarefa por dispositivo.

## <a name="where-clause"></a>Cláusula WHERE

O **onde < filter_condition >** cláusula é opcional. Especifica as condições que os documentos JSON na coleção de FROM satisfazer para poder ser incluídos como parte do resultado. Qualquer documento JSON têm de ser avaliados as condições especificadas como "true" para ser incluídas no resultado.

As condições permitidas são descritas na secção [expressões e condições][lnk-query-expressions].

## <a name="select-clause"></a>Cláusula SELECT

A cláusula SELECT (**SELECIONAR < select_list >**) é obrigatória e especifica o que vai ser valores obtidos a partir da consulta. Especifica os valores JSON para ser utilizada para gerar novos objetos de JSON para cada elemento do subconjunto filtrado (e, opcionalmente, agrupado) da coleção de, a fase projecções gera um novo objeto JSON, construídos com os valores especificados na cláusula SELECT.

Esta é a gramática da cláusula SELECT:

        SELECT [TOP <max number>] <projection list>

        <projection_list> ::=
            '*'
            | <projection_element> AS alias [, <projection_element> AS alias]+

        <projection_element> :==
            attribute_name
            | <projection_element> '.' attribute_name
            | <aggregate>

        <aggregate> :==
            count(<projection_element>) | count()
            | avg(<projection_element>) | avg()
            | sum(<projection_element>) | sum()
            | min(<projection_element>) | min()
            | max(<projection_element>) | max()

onde **attribute_name** refere-se a propriedade do documento JSON na coleção de FROM. Alguns exemplos de cláusulas seleccionar podem ser encontrados na [introdução com consultas duplos] [ lnk-query-getstarted] secção.

Atualmente, seleção cláusulas diferentes **SELECIONE \* ** só são suportadas em consultas de agregação gémeos.

## <a name="group-by-clause"></a>Cláusula GROUP BY

Cláusula **GROUP BY < group_specification >** é um passo opcional que pode ser executado após o filtro especificado na cláusula WHERE e antes de projecções especificado na SELECIONAR. Grupos de documentos baseados no valor de um atributo. Estes grupos são utilizados para gerar valores agregados conforme especificado na cláusula SELECT.

Um exemplo de uma consulta utilizando GROUP BY é:

        SELECT properties.reported.telemetryConfig.status AS status,
            COUNT() AS numberOfDevices
        FROM devices
        GROUP BY properties.reported.telemetryConfig.status

A sintaxe formal para GROUP BY é:

        GROUP BY <group_by_element>
        <group_by_element> :==
            attribute_name
            | < group_by_element > '.' attribute_name

onde **attribute_name** refere-se a propriedade do documento JSON na coleção de FROM. 

Atualmente, a cláusula GROUP BY só é suportada quando consultar gémeos.

## <a name="expressions-and-conditions"></a>Expressões e condições

De alto nível, uma *expressão*:

* Avalia a uma instância de um tipo JSON (ou seja, booleano, número, cadeia, matriz ou objeto), e
* É definido pelo manipular os dados provenientes do dispositivo JSON documento e constantes através de operadores incorporados e funções.

*Condições* são expressões avaliada como um valor booleano, ou seja, qualquer constante diferente booleana **Verdadeiro** é considerado como **Falso** (incluindo **Nulo**, **indefinido**, qualquer instância do objeto ou de uma matriz, qualquer cadeia e claramente o booleana **Falso**).

A sintaxe para expressões é:

        <expression> ::=
            <constant> |
            attribute_name |
            unary_operator <expression> |
            <expression> binary_operator <expression> |
            <create_array_expression> |
            '(' <expression> ')'

        <constant> ::=
            <undefined_constant>
            | <null_constant> 
            | <number_constant> 
            | <string_constant> 
            | <array_constant> 

        <undefined_constant> ::= undefined
        <null_constant> ::= null
        <number_constant> ::= decimal_literal | hexadecimal_literal
        <string_constant> ::= string_literal
        <array_constant> ::= '[' <constant> [, <constant>]+ ']'

em que:

| Símbolo | Definição |
| -------------- | -----------------|
| attribute_name | Qualquer a propriedade do documento JSON na coleção de FROM. |
| unary_operator | Qualquer operador unário de acordo com o ponto de operadores. |
| binary_operator | Qualquer operador binário de acordo com o ponto de operadores. |
| decimal_literal | Um flutuar expresso em números decimais. |
| hexadecimal_literal | Um número expresso pela cadeia 'x 0', seguido de uma cadeia de dígitos hexadecimais. |
| string_literal | Cadeias literais são cadeias de Unicode representadas por uma sequência de zero ou mais carateres Unicode ou sequências de escape. Cadeias literais estão entre aspas simples (apóstrofe: ') ou faça duplo propostas (ponto de interrogação: "). Permitido sai: `\'`, `\"`, `\\`, `\uXXXX` para carateres Unicode definidas pela 4 dígitos hexadecimais. |

### <a name="operators"></a>Operadores

São suportados os seguintes operadores:

| Família | Operadores |
| -------------- | -----------------|
| Média aritmética | +,-,*,/,% |
| Lógico | E, OU, NÃO |
| Comparação |  =,! =, <>,, < =, > =, <> |

## <a name="next-steps"></a>Próximos passos

Saiba como executar consultas no seu aplicações utilizar [IoT concentrador SDK][lnk-hub-sdks].

[lnk-query-where]: iot-hub-devguide-query-language.md#where-clause
[lnk-query-expressions]: iot-hub-devguide-query-language.md#expressions-and-conditions
[lnk-query-getstarted]: iot-hub-devguide-query-language.md#getting-started-with-twin-queries

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md