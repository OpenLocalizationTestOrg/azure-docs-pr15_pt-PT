<properties
    pageTitle="Exemplos de padrões de utilização comum em Analytics da cadeia de consulta | Microsoft Azure"
    description="Padrões de consulta de análise da cadeia Azure comuns "
    keywords="Exemplos de consulta"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>


# <a name="query-examples-for-common-stream-analytics-usage-patterns"></a>Exemplos de padrões de utilização de análise da cadeia comuns de consulta

## <a name="introduction"></a>Introdução

Consultas no Azure a análise da cadeia estão expresso num idioma de consulta SQL semelhantes, que é documentado no guia de [Referência da linguagem da cadeia a análise de consulta](https://msdn.microsoft.com/library/azure/dn834998.aspx) .  Este artigo descreve soluções para várias padrões comuns de consulta com base em cenários do mundo real.  É um trabalho em curso e continua a ser atualizado com os padrões de novos de uma forma contínua.

## <a name="query-example-data-type-conversions"></a>Exemplo de consulta: conversões de tipos de dados
**Descrição**: definir os tipos de propriedades a sequência de entrada.
Por exemplo, a espessura de carro está a chegar na sequência de entrada como cadeias e precisa de ser convertidas em INT para efetuar soma-lo.

**Introdução**:

| Fazer | Tempo | Peso |
| --- | --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z | "1000" |
| Honda | 2015-01-01T00:00:02.0000000Z | "2000" |

**Resultado**:

| Fazer | Peso |
| --- | --- |
| Honda | 3000 |

**Solução**:

    SELECT
        Make,
        SUM(CAST(Weight AS BIGINT)) AS Weight
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)

**Explicação**: utilizar uma instrução CAST no campo peso para especificar o tipo (consulte a lista de tipos de dados suportados [aqui](https://msdn.microsoft.com/library/azure/dn835065.aspx)).


## <a name="query-example-using-likenot-like-to-do-pattern-matching"></a>Exemplo de consulta: utilizar gosto/não como para padrões de correspondência
**Descrição**: Verifique que um valor de campo no evento corresponde a um determinado padrão, por exemplo, o remetente chapas de licenças que começam e terminam com 9

**Introdução**:

| Fazer | LicensePlate | Tempo |
| --- | --- | --- |
| Honda | ABC-123 | 2015-01-01T00:00:01.0000000Z |
| Assistência da Toyota | AAA 999 | 2015-01-01T00:00:02.0000000Z |
| Nissan | ABC 369 | 2015-01-01T00:00:03.0000000Z |

**Resultado**:

| Fazer | LicensePlate | Tempo |
| --- | --- | --- |
| Assistência da Toyota | AAA 999 | 2015-01-01T00:00:02.0000000Z |
| Nissan | ABC 369 | 2015-01-01T00:00:03.0000000Z |

**Solução**:

    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LicensePlate LIKE 'A%9'

**Explicação**: utilizar a declaração como para verificar que o valor do campo LicensePlate começa com um, em seguida, tem uma cadeia de carateres de zero ou mais e termina com 9. 

## <a name="query-example-specify-logic-for-different-casesvalues-case-statements"></a>Exemplo de consulta: Especifique lógica para diferentes casos/valores (declarações de maiúsculas e minúsculas)
**Descrição**: forneça cálculo diferente para um campo com base em alguns critérios.
Por exemplo, forneça uma descrição de cadeia para carros quantos passou da mesma efetuar com um caso especial para 1.

**Introdução**:

| Fazer | Tempo |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Assistência da Toyota | 2015-01-01T00:00:02.0000000Z |
| Assistência da Toyota | 2015-01-01T00:00:03.0000000Z |

**Resultado**:

| CarsPassed | Tempo |
| --- | --- | --- |
| 1 Honda | 2015-01-01T00:00:10.0000000Z |
| 2 Toyotas | 2015-01-01T00:00:10.0000000Z |

**Solução**:

    SELECT
        CASE
            WHEN COUNT(*) = 1 THEN CONCAT('1 ', Make)
            ELSE CONCAT(CAST(COUNT(*) AS NVARCHAR(MAX)), ' ', Make, 's')
        END AS CarsPassed,
        System.TimeStamp AS Time
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)

**Explicação**: cláusula de maiúsculas/minúsculas permite-nos fornecer um cálculo diferentes, com base em alguns critérios (no nosso caso a contagem de carros na janela de agregação).

## <a name="query-example-send-data-to-multiple-outputs"></a>Exemplo de consulta: enviar dados para várias saídas
**Descrição**: enviar dados para vários destinos de saída de uma única tarefa.
Por exemplo, analisar os dados para um alerta de limiar e arquivar todos os eventos ao armazenamento blob

**Introdução**:

| Fazer | Tempo |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Assistência da Toyota | 2015-01-01T00:00:01.0000000Z |
| Assistência da Toyota | 2015-01-01T00:00:02.0000000Z |
| Assistência da Toyota | 2015-01-01T00:00:03.0000000Z |

**Output1**:

| Fazer | Tempo |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Assistência da Toyota | 2015-01-01T00:00:01.0000000Z |
| Assistência da Toyota | 2015-01-01T00:00:02.0000000Z |
| Assistência da Toyota | 2015-01-01T00:00:03.0000000Z |

**Output2**:

| Fazer | Tempo | Contar |
| --- | --- | --- |
| Assistência da Toyota | 2015-01-01T00:00:10.0000000Z | 3 |

**Solução**:

    SELECT
        *
    INTO
        ArchiveOutput
    FROM
        Input TIMESTAMP BY Time

    SELECT
        Make,
        System.TimeStamp AS Time,
        COUNT(*) AS [Count]
    INTO
        AlertOutput
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
    HAVING
        [Count] >= 3

**Explicação**: cláusula de INTO indica a análise da cadeia qual das saídas de escrever os dados a partir desta declaração de.
A primeira consulta é uma pass-through dos dados que recebemos para um resultado que a que chamamos ArchiveOutput.
A segunda consulta é que algumas agregação simple e filtragem e envia os resultados a um sistema de alerta descendentes.
*Nota*: também pode reutilizar resultados de CTEs (ou seja, com declarações) na várias declarações de saída – este tem a vantagem de abertura de leitores menos para a origem de entrada.
Por exemplo, 

    WITH AllRedCars AS (
        SELECT
            *
        FROM
            Input TIMESTAMP BY Time
        WHERE
            Color = 'red'
    )
    SELECT * INTO HondaOutput FROM AllRedCars WHERE Make = 'Honda'
    SELECT * INTO ToyotaOutput FROM AllRedCars WHERE Make = 'Toyota'

## <a name="query-example-counting-unique-values"></a>Exemplo de consulta: contar valores exclusivos
**Descrição**: contar o número de valores de campo exclusivo que aparecer na sequência dentro de uma janela de tempo.
Por exemplo, quantos tornar exclusivo de carros passou o stand de serviço pago em janelas do segundo 2?

**Introdução**:

| Fazer | Tempo |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Assistência da Toyota | 2015-01-01T00:00:01.0000000Z |
| Assistência da Toyota | 2015-01-01T00:00:02.0000000Z |
| Assistência da Toyota | 2015-01-01T00:00:03.0000000Z |

**Resultado:**

| Contar | Tempo |
| --- | --- |
| 2 | 2015-01-01T00:00:02.000Z |
| 1 | 2015-01-01T00:00:04.000Z |

**Solução:**

    WITH Makes AS (
        SELECT
            Make,
            COUNT(*) AS CountMake
        FROM
            Input TIMESTAMP BY Time
        GROUP BY
              Make,
              TumblingWindow(second, 2)
    )
    SELECT
        COUNT(*) AS Count,
        System.TimeStamp AS Time
    FROM
        Makes
    GROUP BY
        TumblingWindow(second, 1)


**Explicação:** Vamos fazer uma agregação inicial para obter exclusivo faz com os respetivos contagem através da janela.
Vamos, em seguida, efetue uma agregação de quantas torna podemos tem – tendo em conta todos os exclusivo valores numa janela obter o mesmo carimbo de hora, em seguida, a segunda janela de agregação tem de estar mínimas para agregar não 2 windows a partir do primeiro passo.

## <a name="query-example-determine-if-a-value-has-changed"></a>Exemplo de consulta: determinar se um valor foi alterada#
**Descrição**: Veja um valor para determinar se é diferente do valor atual por exemplo, é o carro anterior em viagem de serviço pago o mesmo fazer como o carro atual anterior?

**Introdução**:

| Fazer | Tempo |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Assistência da Toyota | 2015-01-01T00:00:02.0000000Z |

**Resultado**:

| Fazer | Tempo |
| --- | --- |
| Assistência da Toyota | 2015-01-01T00:00:02.0000000Z |

**Solução**:

    SELECT
        Make,
        Time
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make

**Explicação**: utilizar tempo de desfasamento a observar para a entrada transmitir em fluxo um evento novamente e obter o valor de fazer. Em seguida, compará-la com a efetuar no evento atual e o evento de saída se forem diferentes.

## <a name="query-example-find-first-event-in-a-window"></a>Exemplo de consulta: localizar primeiro evento numa janela
**Descrição**: localizar primeiro carro em cada intervalo de 10 minutos?

**Introdução**:

| LicensePlate | Fazer | Tempo |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | Ford | 2015-07-27T00:02:17.0000000Z |
| RMV 8282 | Honda | 2015-07-27T00:05:01.0000000Z |
| YHN 6970 | Assistência da Toyota | 2015-07-27T00:06:00.0000000Z |
| VFE 1616 | Assistência da Toyota | 2015-07-27T00:09:31.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**Resultado**:

| LicensePlate | Fazer | Tempo |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |

**Solução**:

    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) = 1

Agora, vamos tornar a alterar o problema e localizar primeiro carro de determinado em cada intervalo de 10 minutos.

| LicensePlate | Fazer | Tempo |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | Ford | 2015-07-27T00:02:17.0000000Z |
| YHN 6970 | Assistência da Toyota | 2015-07-27T00:06:00.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**Solução**:

    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) OVER (PARTITION BY Make) = 1

## <a name="query-example-find-last-event-in-a-window"></a>Exemplo de consulta: localizar último evento numa janela
**Descrição**: localizar carro último em cada intervalo de 10 minutos.

**Introdução**:

| LicensePlate | Fazer | Tempo |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | Ford | 2015-07-27T00:02:17.0000000Z |
| RMV 8282 | Honda | 2015-07-27T00:05:01.0000000Z |
| YHN 6970 | Assistência da Toyota | 2015-07-27T00:06:00.0000000Z |
| VFE 1616 | Assistência da Toyota | 2015-07-27T00:09:31.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**Resultado**:

| LicensePlate | Fazer | Tempo |
| --- | --- | --- |
| VFE 1616 | Assistência da Toyota | 2015-07-27T00:09:31.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**Solução**:

    WITH LastInWindow AS
    (
        SELECT 
            MAX(Time) AS LastEventTime
        FROM 
            Input TIMESTAMP BY Time
        GROUP BY 
            TumblingWindow(minute, 10)
    )
    SELECT 
        Input.LicensePlate,
        Input.Make,
        Input.Time
    FROM
        Input TIMESTAMP BY Time 
        INNER JOIN LastInWindow
        ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
        AND Input.Time = LastInWindow.LastEventTime

**Explicação**: Existem dois passos na consulta – a primeira parte localiza carimbo de data/hora mais recente no windows 10 minutos. O segundo passo associa os resultados da consulta primeiro com original da cadeia para encontrar os eventos com correspondência últimos selos de tempo em cada janela. 

## <a name="query-example-detect-the-absence-of-events"></a>Exemplo de consulta: detetar ausência de eventos
**Descrição**: Verifique que uma sequência não tem um valor que corresponde a um determinado critério.
Por exemplo, tem 2 carros consecutivos a partir do mesmo para disponibilizar introduzido viagem de serviço pago no prazo de 90 segundos?

**Introdução**:

| Fazer | LicensePlate | Tempo |
| --- | --- | --- |
| Honda | ABC-123 | 2015-01-01T00:00:01.0000000Z |
| Honda | AAA 999 | 2015-01-01T00:00:02.0000000Z |
| Assistência da Toyota | DEFINIÇÃO DE 987 | 2015-01-01T00:00:03.0000000Z |
| Honda | GHI 345 | 2015-01-01T00:00:04.0000000Z |

**Resultado**:

| Fazer | Tempo | CurrentCarLicensePlate | FirstCarLicensePlate | FirstCarTime |
| --- | --- | --- | --- | --- |
| Honda | 2015-01-01T00:00:02.0000000Z | AAA 999 | ABC-123 | 2015-01-01T00:00:01.0000000Z |

**Solução**:

    SELECT
        Make,
        Time,
        LicensePlate AS CurrentCarLicensePlate,
        LAG(LicensePlate, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarLicensePlate,
        LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarTime
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make

**Explicação**: utilizar tempo de desfasamento a observar para a entrada transmitir em fluxo um evento novamente e obter o valor de fazer. Em seguida, compará-la com a efetuar no evento atual e o evento de saída, se estes são os mesmos e utilizam o tempo de desfasamento para obter dados sobre o carro anterior.

## <a name="query-example-detect-duration-between-events"></a>Exemplo de consulta: detetar duração entre eventos
**Descrição**: encontrar a duração de um determinado evento. Por exemplo, tendo em conta um clickstream web determine tempo gasto uma funcionalidade.

**Introdução**:  
  
| Utilizador | Funcionalidade | Evento | Tempo |
| --- | --- | --- | --- |
| user@location.com | RightMenu | Iniciar | 2015-01-01T00:00:01.0000000Z |
| user@location.com | RightMenu | Fim | 2015-01-01T00:00:08.0000000Z |
  
**Resultado**:  
  
| Utilizador | Funcionalidade | Duração |
| --- | --- | --- |
| user@location.com | RightMenu | 7 |
  

**Solução**

````
    SELECT
        [user], feature, DATEDIFF(second, LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'), Time) as duration
    FROM input TIMESTAMP BY Time
    WHERE
        Event = 'end'
````

**Explicação**: utilizar a função última para obter o último valor de hora quando o tipo de evento foi 'Iniciar'. Repare que a função última utiliza PARTIÇÃO por [utilizador] para indicar que o resultado será calculado por utilizador exclusivo.  A consulta tem um limite máximo de 1 hora para diferença de tempo entre eventos 'Start' e 'Parar' mas é configurável como necessária (limite DURATION(hour, 1).

## <a name="query-example-detect-duration-of-a-condition"></a>Exemplo de consulta: detetar duração de uma condição
**Descrição**: localizar fora quanto tempo Ocorreu uma condição para.
Por exemplo, imaginemos que um erro que resultou num carros todos os ter uma espessura incorreta (acima libras 20.000) – queremos calcular a duração do erro.

**Introdução**:

| Fazer | Tempo | Peso |
| --- | --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z | 2000 |
| Assistência da Toyota | 2015-01-01T00:00:02.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:03.0000000Z | 26000 |
| Assistência da Toyota | 2015-01-01T00:00:04.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:05.0000000Z | 26000 |
| Assistência da Toyota | 2015-01-01T00:00:06.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:07.0000000Z | 26000 |
| Assistência da Toyota | 2015-01-01T00:00:08.0000000Z | 2000 |

**Resultado**:

| StartFault | EndFault |
| --- | --- |
| 2015-01-01T00:00:02.000Z | 2015-01-01T00:00:07.000Z |

**Solução**:

````
    WITH SelectPreviousEvent AS
    (
    SELECT
    *,
        LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previousTime,
        LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previousWeight
    FROM input TIMESTAMP BY [time]
    )

    SELECT 
        LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previousWeight < 20000 ) [StartFault],
        previousTime [EndFault]
    FROM SelectPreviousEvent
    WHERE
        [weight] < 20000
        AND previousWeight > 20000
````

**Explicação**: Utilize o tempo de desfasamento para ver a sequência de entrada 24 horas e procure instâncias onde StartFault e StopFault são expandidos por weight < 20000.

## <a name="query-example-fill-missing-values"></a>Exemplo de consulta: preencher os valores em falta
**Descrição**: para a sequência de eventos que têm valores em falta, produzir uma sequência de eventos com intervalos regulares.
Por exemplo, gera evento 5 segundos que irá reportar o ponto de dados viu mais recentemente.

**Introdução**:

| t | valor |
|--------------------------|-------|
| "2014-01-01T06:01:00" | 1 |
| "2014-01-01T06:01:05" | 2 |
| "2014-01-01T06:01:10" | 3 |
| "2014-01-01T06:01:15" | 4 |
| "2014-01-01T06:01:30" | 5 |
| "2014-01-01T06:01:35" | 6 |

**Resultado (primeiros 10 linhas)**:

| windowend | lastevent.t | lastevent.Value |
|--------------------------|--------------------------|--------|
| 2014-01-01T14:01:00.000Z | 2014-01-01T14:01:00.000Z | 1 |
| 2014-01-01T14:01:05.000Z | 2014-01-01T14:01:05.000Z | 2 |
| 2014-01-01T14:01:10.000Z | 2014-01-01T14:01:10.000Z | 3 |
| 2014-01-01T14:01:15.000Z | 2014-01-01T14:01:15.000Z | 4 |
| 2014-01-01T14:01:20.000Z | 2014-01-01T14:01:15.000Z | 4 |
| 2014-01-01T14:01:25.000Z | 2014-01-01T14:01:15.000Z | 4 |
| 2014-01-01T14:01:30.000Z | 2014-01-01T14:01:30.000Z | 5 |
| 2014-01-01T14:01:35.000Z | 2014-01-01T14:01:35.000Z | 6 |
| 2014-01-01T14:01:40.000Z | 2014-01-01T14:01:35.000Z | 6 |
| 2014-01-01T14:01:45.000Z | 2014-01-01T14:01:35.000Z | 6 |

    
**Solução**:

    SELECT
        System.Timestamp AS windowEnd,
        TopOne() OVER (ORDER BY t DESC) AS lastEvent
    FROM
        input TIMESTAMP BY t
    GROUP BY HOPPINGWINDOW(second, 300, 5)


**Explicação**: esta consulta irá gerar eventos cada segundo 5 e será o último evento foi recebido antes de saída. [Salto de janela] Duração de (https://msdn.microsoft.com/library/dn835041.aspx "Salto de janela - Azure da cadeia Analytics") determina como extremo anterior, a consulta será o aspeto para localizar o evento mais recente (300 segundos neste exemplo).


## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente nosso [Fórum Azure da cadeia Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximos passos

- [Introdução ao Azure sequência Analytics](stream-analytics-introduction.md)
- [Começar a utilizar o Azure da cadeia Analytics](stream-analytics-get-started.md)
- [Escala Azure da cadeia Analytics tarefas](stream-analytics-scale-jobs.md)
- [Azure sequência Analytics referência da linguagem de consulta](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da sequência Azure Analytics gestão REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 
