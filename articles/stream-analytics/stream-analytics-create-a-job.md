<properties 
    pageTitle="Como criar uma tarefa de processamento de análise de dados para a análise da cadeia | Microsoft Azure" 
    description="Criar uma tarefa de processamento de análise de dados para a análise da cadeia | segmento de caminho de formação."
    keywords="processamento de análises de dados"
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

# <a name="how-to-create-a-data-analytics-processing-job-for-stream-analytics"></a>Como criar uma tarefa de processamento de análise de dados para a análise da cadeia

O recurso de nível superior no Azure da cadeia Analytics é uma tarefa de análise da cadeia.  É constituído por uma ou mais origens de dados de entrada, uma consulta expressar a transformação de dados e um ou mais destinos de saída que sejam escritos nos resultados. Em conjunto estas permitem ao utilizador executar análises de dados de processamento de transmissão de cenários de dados.

Para começar a utilizar a análise da cadeia, comece por criar uma nova tarefa de análise da cadeia.  Note que esta ação tem sem implicações faturaçãohttps até a tarefa é iniciada.

1.  Inicie sessão no online [Azure portal clássico](http://manage.windowsazure.com) ou o [Azure portal](https://portal.azure.com/).
2.  No portal: **Clique em novo**, em seguida, clique em **Serviços de dados** ou **A análise de dados** , dependendo do seu portal do e, em seguida, clique em **Azure da cadeia Analytics** ou **Da cadeia Analytics** e, em seguida, **Criar rápida**.

    ![Assistente de tarefa de processamento de análises de dados](./media/stream-analytics-create-a-job/1-stream-analytics-create-a-job.png)  

    ![Criar a tarefa de processamento de análise de dados](./media/stream-analytics-create-a-job/4-stream-analytics-create-a-job.png)  

3.  Especifique a configuração pretendida para a tarefa de análise da cadeia.
    - Na caixa **Nome da tarefa** , introduza um nome para identificar a tarefa de análise da cadeia. Quando o **Nome da tarefa** for validado, aparece uma marca de verificação verde na caixa Nome da tarefa. O **Nome da tarefa** pode conter apenas caracteres alfanuméricos e o '-' caráter e tem de estar entre 3 e 63 caracteres.
    - Utilize **região** no portal do Azure ou **localização** no portal do Azure para especificar a localização geográfica em que pretende executar a tarefa.
    - Se utilizar o portal do Azure, selecione ou crie uma conta de armazenamento para utilizar como a **Conta de armazenamento de monitorização regionais**. Esta conta de armazenamento é utilizada para armazenar dados de monitorização para todas as tarefas de análise da cadeia a ser executada nesta região.
    - Se utilizar o portal do Azure, especificar um novo ou existente **Grupo de recursos** para colocar em espera recursos relacionados para a sua aplicação.

4.  Assim que as novas opções de tarefa de análise da cadeia estão configuradas, clique em **Criar tarefa do fluxo de Analytics**. Pode demorar alguns minutos para a tarefa de análise da cadeia a ser criada. Para verificar o estado, pode monitorizar o progresso no centro do notificações.

    ![Concentrador de notfications dos dados analytics processamento de tarefa](./media/stream-analytics-create-a-job/2-stream-analytics-create-a-job.png)  

    ![Análise de dados de portal Azure processamento trabalho criar tarefa](./media/stream-analytics-create-a-job/5-stream-analytics-create-a-job.png)  

5.  A nova tarefa será apresentada com o estado **criado**. Repare que o botão **Iniciar do** está desativado. Tem de configurar a entrada de tarefa, a consulta e a saída antes de poder iniciar a tarefa.

    ![Tarefa de processamento de análises de dados tarefa Estado](./media/stream-analytics-create-a-job/3-stream-analytics-create-a-job.png)  

    ![Análise de dados de portal Azure processamento estado da tarefa de trabalho](./media/stream-analytics-create-a-job/6-stream-analytics-create-a-job.png)  

## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente nosso [Fórum Azure da cadeia Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximos passos

- [Introdução ao Azure sequência Analytics](stream-analytics-introduction.md)
- [Começar a utilizar o Azure da cadeia Analytics](stream-analytics-get-started.md)
- [Tarefas de análise do Azure da cadeia de escala](stream-analytics-scale-jobs.md)
- [Azure sequência Analytics referência da linguagem de consulta](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da sequência Azure Analytics gestão REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
