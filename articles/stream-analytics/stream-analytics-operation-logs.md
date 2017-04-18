<properties 
    pageTitle="Depurar utilizando operação e registos de serviço na sequência Analytics | Microsoft Azure" 
    description="Instruções de utilizar a análise da cadeia operação registos" 
    keywords="registos de serviço"
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
    ms.workload="data-services" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"/>

# <a name="debug-stream-analytics-jobs-using-service-and-operation-logs"></a>Depurar trabalhos de análise da cadeia utilizando os registos do serviço e de operação

Todos os serviços do Azure fornecem registo operacionais mensagens aos utilizadores para os detalhes de registos relacionados com operações de gestão. No Azure da cadeia Analytics, estas informações podem ser utilizadas para fins como ver o estado da tarefa, o progresso da tarefa, de depuração e comece a mensagens de falha para controlar o progresso de uma tarefa ao longo do tempo, a partir de para processamento de saída.

## <a name="find-operation-logs-in-the-azure-management-portal"></a>Localizar registos de operação no portal de gestão do Azure

Registos de operação podem ser acedidos de duas maneiras:  

- Dashboard do projecto a análise da cadeia  
- Serviços de gestão de no portal do Azure clássico  

## <a name="dashboard-of-the-stream-analytics-job"></a>Dashboard do projecto a análise da cadeia

Uma ligação para os registos correspondentes de uma tarefa de análise da cadeia é apresentada no separador de Dashboard a tarefa. Se clicar dessa ligação, irá definir os filtros de forma a que mostra registos mais recentes para essa tarefa específica.

  ![Selecione os serviços de gestão de registos](./media/stream-analytics-operation-logs/01-stream-analytics-operation-logs.png)  

## <a name="management-services"></a>Serviços de gestão

Para navegar manualmente para os registos de operação para outros serviços no portal do Azure clássica e de análise de sequência:

1.  Clique em **Serviços de gestão de** no [portal do Azure clássica](https://manage.windowsazure.com).
2.  Selecione **A análise da cadeia** para o **tipo** e o nome da tarefa para **Nome do serviço**.  

  ![Selecione a análise da cadeia](./media/stream-analytics-operation-logs/02-stream-analytics-operation-logs.png)  

## <a name="find-audit-logs-in-the-azure-portal"></a>Localizar registos de auditoria no portal do Azure ##

Para localizar registos operacionais para a tarefa de análise da cadeia no portal do Azure, clique em **Procurar** e, em seguida, selecione **registos de auditoria**.

  ![Selecione a sequência de análise de portal Azure](./media/stream-analytics-operation-logs/06-stream-analytics-operation-logs.png)  

Isto vai abrir uma pá mostrar os eventos de nos últimos 7 dias para todos os recursos na sua subscrição.  Pode filtrar para ver os eventos de um tipo de especificar ou período de tempo ao clicar no comando de **filtro** .

  ![Selecione a sequência de análise de portal Azure](./media/stream-analytics-operation-logs/07-stream-analytics-operation-logs.png)  

## <a name="get-log-details"></a>Obter os detalhes de registo

Pode filtrar por intervalo de tempo e o estado para ver os registos para a tarefa.

No portal do Azure gestão, clique no botão **Detalhes** na parte inferior da janela para ver mais detalhes sobre um evento seleccionado. 

  ![Selecione detalhes](./media/stream-analytics-operation-logs/03-stream-analytics-operation-logs.png)  

No portal do Azure, clique numa entrada de registo para ver os eventos detalhados dentro da mesma.

  ![Portal Azure selecionar detalhes](./media/stream-analytics-operation-logs/08-stream-analytics-operation-logs.png)  

A partir daqui, pode abrir o pá **detalhe** clicando no evento.

  ![Portal Azure selecionar detalhes](./media/stream-analytics-operation-logs/09-stream-analytics-operation-logs.png)  

## <a name="debug-a-failed-job"></a>Depurar uma falha na tarefa

No portal do Azure gestão, clique no ícone de pesquisa e tipo 'falhadas'. Isto dá um resultado de todos os registos com falhas. 

  ![Depuração de uma falha na tarefa](./media/stream-analytics-operation-logs/04-stream-analytics-operation-logs.png)  

No portal do Azure, pode filtrar por nível de mensagem para visualizar os eventos **crítica** .

  ![Azure depuração de portal](./media/stream-analytics-operation-logs/10-stream-analytics-operation-logs.png)  

Pode selecionar qualquer uma das falhas e clique em **Detalhes** para obter mais informações sobre o erro.  Algumas mensagens de erro também fornecem informações sobre como mitigar o problema. 

  ![Detalhes da operação](./media/stream-analytics-operation-logs/05-stream-analytics-operation-logs.png)  

No caso de precisar de contactar o [suporte](https://azure.microsoft.com/support/options/) ou fornecer informações à equipa do através de da [Fórum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics), tenha em atenção os detalhes da operação, especificamente o **ID de correlação**. 

## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente nosso [Fórum Azure da cadeia Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximos passos

- [Introdução ao Azure sequência Analytics](stream-analytics-introduction.md)
- [Começar a utilizar o Azure da cadeia Analytics](stream-analytics-get-started.md)
- [Escala Azure da cadeia Analytics tarefas](stream-analytics-scale-jobs.md)
- [Azure sequência Analytics referência da linguagem de consulta](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da sequência Azure Analytics gestão REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
