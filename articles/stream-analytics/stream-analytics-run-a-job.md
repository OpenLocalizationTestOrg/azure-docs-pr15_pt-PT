<properties 
    pageTitle="Para iniciar a transmissão de tarefas em sequência Analytics | Microsoft Azure" 
    description="Como executar uma tarefa de transmissão no Azure da cadeia Analytics | segmento de caminho de formação."
    keywords="transmissão de tarefas"
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"/>

# <a name="how-to-run-a-streaming-job-in-azure-stream-analytics"></a>Como executar uma tarefa de transmissão no Azure da cadeia Analytics

Quando uma tarefa de entrada, a consulta e a saída todos foram especificadas que pode começar a tarefa de análise da cadeia.

Para iniciar o seu trabalho:

1.  No portal do Azure clássico, a partir do dashboard de tarefa, clique em **Iniciar** na parte inferior da página.

    ![Iniciar a tarefa de botão](./media/stream-analytics-run-a-job/1-stream-analytics-run-a-job.png)  

    No portal do Azure, clique em **Iniciar** na parte superior da sua página de tarefa.

    ![Azure portal início tarefa botão](./media/stream-analytics-run-a-job/4-stream-analytics-run-a-job.png)  

2.  Especificar um valor de **Saída iniciar** determinar quando esta tarefa irá produzir resultados. A predefinição para tarefas que anteriormente não iniciado é **Hora de início de tarefa**, o que significa que a tarefa imediatamente será iniciado dados de processamento. Também pode especificar uma hora **personalizada** no passado (para consumir dados histórico) ou o futuro (para atrasar processamento até uma hora futura). Para os casos quando uma tarefa foi anteriormente iniciada e parada, a opção **Última vez parado** está disponível para retomar a tarefa a partir da última vez de saída e evitar a perda de dados.  

    ![Iniciar a tarefa de tempo de transmissão](./media/stream-analytics-run-a-job/2-stream-analytics-run-a-job.png)  

    ![Azure tarefa de transmissão de início tempo de portal](./media/stream-analytics-run-a-job/5-stream-analytics-run-a-job.png)  

3.  Confirme a sua seleção. O estado da tarefa irá alterar para *inicial* e será brevemente deslocar-se para *a executar o* depois de ter começado a tarefa. Pode monitorizar o progresso da operação de **Iniciar** no **Concentrador de notificação**:

    ![transmissão o progresso da tarefa](./media/stream-analytics-run-a-job/3-stream-analytics-run-a-job.png)  

    ![Portal Azure transmissão o progresso da tarefa](./media/stream-analytics-run-a-job/6-stream-analytics-run-a-job.png)  

## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente nosso [Fórum Azure da cadeia Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximos passos

- [Introdução ao Azure sequência Analytics](stream-analytics-introduction.md)
- [Começar a utilizar o Azure da cadeia Analytics](stream-analytics-get-started.md)
- [Escala Azure da cadeia Analytics tarefas](stream-analytics-scale-jobs.md)
- [Azure sequência Analytics referência da linguagem de consulta](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da sequência Azure Analytics gestão REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
