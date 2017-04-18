<properties
    pageTitle="Adicionar o accionador periodicidade nas aplicações de lógica | Microsoft Azure"
    description="Descrição geral de accionador periodicidade e como utilizá-la com uma aplicação do Azure lógica."
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

# <a name="get-started-with-the-recurrence-trigger"></a>Introdução ao accionador periodicidade

Ao utilizar o accionador de periodicidade, pode criar fluxos de trabalho poderosos na nuvem.

Por exemplo, pode:

- Agende um fluxo de trabalho para executar um procedimento armazenado SQL diariamente.
- E-mail um resumo de todos os tweets situadas na semana última sobre um determinado hashtag.

Para começar a utilizar o accionador periodicidade numa aplicação lógica, consulte o artigo [criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="use-a-recurrence-trigger"></a>Utilize um acionador de periodicidade

Um accionador é um evento que pode ser utilizado para iniciar o fluxo de trabalho que está definido numa aplicação de lógica. [Saiba mais sobre os accionadores](connectors-overview.md).

Eis uma sequência de exemplo de como configurar um acionador de periodicidade numa aplicação de lógica:

1. Adicione o accionador **Periodicidade** como o primeiro passo numa aplicação lógica.
2. Preencha os parâmetros para o intervalo de periodicidade.

A aplicação de lógica agora inicia uma executar após cada intervalo de tempo.

![Acionar HTTP](./media/connectors-native-recurrence/using-trigger.png)

## <a name="trigger-details"></a>Detalhes de accionador

O accionador periodicidade tem as seguintes propriedades que pode configurar.

Este é acionada uma aplicação de lógica após um intervalo de tempo especificado.
A * significa que é um campo obrigatório.

|Nome a apresentar|Nome da propriedade|Descrição|
|---|---|---|
|Frequência *|frequência|The unit of time: `Second`, `Minute`, `Hour`, `Day`, or `Year`.|
|Intervalo *|intervalo|Intervalo da determinado frequência de periodicidade.|
|Fuso horário|fuso horário|Se for fornecida uma hora de início sem uma em relação à UTC, será utilizado neste fuso horário.|
|Hora de início|hora de início|A hora de início no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations).|
<br>


## <a name="next-steps"></a>Próximos passos

Agora, experimente a plataforma e, em seguida, [criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md). Pode explorar as outras conexões disponíveis nas aplicações de lógica verificando nossa [lista APIs](apis-list.md).
