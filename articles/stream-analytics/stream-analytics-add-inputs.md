<properties
    pageTitle="Adicionar uma entrada de dados para as tarefas de análise da cadeia | Microsoft Azure"
    description="Saiba como ligar uma origem de dados para o seu trabalho de análise da cadeia como transmissão de introdução de dados a partir de dados de evento concentradores ou referência a partir do armazenamento no blogue."
    keywords="dados de entrada, transmissão de dados"
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"
/>


# <a name="add-a-streaming-data-input-or-reference-data-to-a-stream-analytics-job"></a>Adicionar um transmissão dados de entrada ou referência de dados a uma tarefa de análise da cadeia

Saiba como ligar uma origem de dados para o seu trabalho de análise da cadeia como transmissão de introdução de dados a partir de dados de evento concentradores ou referência a partir do armazenamento de Blobs.

Tarefas de análise da cadeia Azure podem ser ligadas a introdução de dados de uma ou mais, cada um dos quais definir uma ligação a uma origem de dados existente. Como dados são enviados para essa origem de dados, é consumida pela tarefa de análise da cadeia e transformado em tempo real como transmissão de dados. Análise de sequência tem primeira classe integração com o [Azure evento concentradores](https://azure.microsoft.com/services/event-hubs/) e [armazenamento de Blobs do Azure](../storage/storage-dotnet-how-to-use-blobs.md) dentro e fora da subscrição a tarefa.

Este artigo é um passo no [caminho de formação de análise da cadeia](/documentation/learning-paths/stream-analytics/).

## <a name="data-input-streaming-data-and-reference-data"></a>Introdução de dados: transmissão de dados e os dados de referência

Existem dois tipos distintos de entradas do tipo em sequência Analytics: sequências de dados e dados de referência.

- **Sequências de dados**: tarefas de análise da cadeia tem de incluir a introdução de dados pelo menos uma sequência ser consumidas e transformados através da tarefa. Armazenamento de Blobs do Azure e de Azure evento concentradores são suportados como origens de entrada de fluxo de dados. Azure evento concentradores são utilizados para recolher sequências de evento de dispositivos ligados, serviços e aplicações. Armazenamento de Blobs do Azure pode ser utilizado como uma origem de entrada para ingesting em volume de dados como uma sequência.  
- **Dados de referência**: análise da cadeia suporta um segundo tipo de dados de auxiliar referência denominados entrada.  Por oposição aos dados em movimento, estes dados são estático ou abrandar a alterar.  É normalmente utilizado para efetuar look-ups e correlação com sequências de dados para criar um conjunto de dados mais rico.  Armazenamento de Blobs do Azure está atualmente a origem de entrada suportada apenas para os dados de referência.  

Para adicionar uma entrada para a tarefa de análise de sequência:

1. No portal do Azure clique **introduções** e, em seguida, clique em **Adicionar uma entrada** na sua tarefa de análise da cadeia.

    ![Portal clássica Azure - adicionar uma entrada.](./media/stream-analytics-add-inputs/1-stream-analytics-add-inputs.png)  

    No portal do Azure clique no mosaico de **entradas** na sua tarefa de análise da cadeia.  

    ![Azure portal - adicionar a introdução de dados.](./media/stream-analytics-add-inputs/7-stream-analytics-add-inputs.png)  

2. Especificar o tipo de entrada: **fluxo de dados** ou **dados de referência**.

    ![Adicionar os dados corretos de entrada, transmitido em sequência ou de referência](./media/stream-analytics-add-inputs/2-stream-analytics-add-inputs.png)  

    ![Adicionar os dados corretos de entrada, transmitido em sequência ou de referência](./media/stream-analytics-add-inputs/8-stream-analytics-add-inputs.png)  

3. Se criar uma entrada de fluxo de dados, especifique o tipo de origem para o de entrada.  Este passo pode ser ignorado durante a criação de dados de referência como Blob apenas de armazenamento é suportado neste momento.

    ![Adicionar a introdução de dados de fluxo de dados](./media/stream-analytics-add-inputs/3-stream-analytics-add-inputs.png)  

    ![Adicionar a introdução de dados de fluxo de dados](./media/stream-analytics-add-inputs/9-stream-analytics-add-inputs.png)  

4. Fornece um nome amigável para esta entrada na caixa de entrada Alias.  Este nome será utilizada numa consulta do seu trabalho mais tarde para consultar a entrada de dados.

    Preencha o resto das propriedades da ligação necessários para ligar à sua origem de dados. Estes campos variam consoante o tipo do tipo de entrada e de origem e são definidos em detalhe [aqui](stream-analytics-create-a-job.md).  

    ![Adicionar a introdução de dados do concentrador de evento](./media/stream-analytics-add-inputs/4-stream-analytics-add-inputs.png)  

5. Especificar as definições de serialização para os dados de entrada:
    - Certifique-se as suas consultas trabalhar a forma como esperado, especifique o **Formato de serialização evento** de dados de entrada.  Formatos de serialização suportados são JSON, CSV e Avro.
    - Verifique se a **codificação** para os dados.  UTF-8 é o formato de codificação suportado apenas neste momento.

    ![Definições de serialização de dados para os dados de entrada](./media/stream-analytics-add-inputs/5-stream-analytics-add-inputs.png)  

    ![Definições de serialização de dados para os dados de entrada](./media/stream-analytics-add-inputs/10-stream-analytics-add-inputs.png)  

6. Depois de concluir a criação de entrada, análise da cadeia verificará que consegue ligar à origem de entrada.  Pode ver o estado da operação de ligação de teste no centro do notificação.

    ![Testar a ligação de dados em tempo real de entrada](./media/stream-analytics-add-inputs/6-stream-analytics-add-inputs.png)  

    ![Testar a ligação de dados em tempo real de entrada](./media/stream-analytics-add-inputs/11-stream-analytics-add-inputs.png)  

## <a name="get-help-with-streaming-data-inputs"></a>Obter ajuda com a transmissão entradas de dados
Para obter mais assistência, experimente nosso [Fórum Azure da cadeia Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximos passos

- [Introdução ao Azure sequência Analytics](stream-analytics-introduction.md)
- [Começar a utilizar o Azure da cadeia Analytics](stream-analytics-get-started.md)
- [Escala Azure da cadeia Analytics tarefas](stream-analytics-scale-jobs.md)
- [Azure sequência Analytics referência da linguagem de consulta](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da sequência Azure Analytics gestão REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
