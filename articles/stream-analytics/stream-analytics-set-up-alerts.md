<properties
    pageTitle="Configurar alertas para consultas em sequência Analytics | Microsoft Azure"
    description="Noções sobre a análise da cadeia alertar"
    keywords="configurar alertas"
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


# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Configurar alertas para trabalhos de análise de sequência do Azure

## <a name="introduction-monitor-page"></a>Introdução: Página de Monitor

Pode definir alertas para acionar um alerta quando uma métrica atinge uma condição que especificar.

Por exemplo, "se for eventos de saída para últimos 15 minutos < 100, enviar notificação de e-mail para o id de correio electrónico: xyz@company.com”.

Regras podem ser configuradas no métricas através do portal do, ou podem ser configurado [através de programação](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a) sobre os dados de registos de operação.

## <a name="set-up-alerts-through-the-azure-classic-portal"></a>Configurar alertas através do Portal clássica do Azure

Existem duas formas de configurar alertas no portal do Azure clássico:  

1.  No separador **Monitor** da sua tarefa de análise da cadeia  
2.  O registo de operações os serviços de gestão  

## <a name="set-up-alert-through-the-monitor-tab-of-the-job-in-the-portal"></a>Configurar o alerta através do separador Monitor da tarefa no portal

1.  Selecione a métrica no separador monitor e clique no botão **Adicionar regra** na parte inferior do dashboard e as suas regras do programa de configuração.  

    ![Dashboard](./media/stream-analytics-set-up-alerts/01-stream-analytics-set-up-alerts.png)  

2.  Definir o nome e descrição do alerta  

    ![Criar regra](./media/stream-analytics-set-up-alerts/02-stream-analytics-set-up-alerts.png)  

3.  Introduza os limiares, janela alerta de avaliação e as ações para o alerta  

    ![Definir condições](./media/stream-analytics-set-up-alerts/03-stream-analytics-set-up-alerts.png)  

## <a name="set-up-alerts-through-the-operations-logs"></a>Configurar alertas através dos registos de operações

1.  Aceda ao separador **alertas** nos serviços de gestão no [Azure clássica Portal](https://manage.windowsazure.com).  
2.  Clique em **Adicionar regra**  

    ![Critérios](./media/stream-analytics-set-up-alerts/04-stream-analytics-set-up-alerts.png)  

3.  Defina o nome e descrição do alerta. Selecione 'Da cadeia Analytics' como tipo de serviço e o nome da tarefa como o nome do serviço.  

    ![Definir alerta](./media/stream-analytics-set-up-alerts/05-stream-analytics-set-up-alerts.png)  

## <a name="set-up-alerts-in-the-azure-portal"></a>Configurar alertas no portal do Azure ##

No portal do Azure, navegue para a tarefa de análise da cadeia que se estiver interessado em alertar para e clique na secção de **monitorização** .  Na pá de **métrica** que se abre, clique no comando de **alerta de adicionar** .

  ![Configuração do portal Azure](./media/stream-analytics-set-up-alerts/06-stream-analytics-set-up-alerts.png)  

Pode atribuir um nome a sua regra alerta e escolha uma descrição que será apresentado no e-mail notificação.

Quando seleciona métricas irá Selecione uma condição e o limiar de valor para a métrica do.

  ![Azure métrica selecione portal](./media/stream-analytics-set-up-alerts/07-stream-analytics-set-up-alerts.png)  

Para obter mais detalhes sobre como configurar alertas no portal do Azure, consulte o artigo [receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).  

## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente nosso [Fórum Azure da cadeia Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximos passos

- [Introdução ao Azure sequência Analytics](stream-analytics-introduction.md)
- [Começar a utilizar o Azure da cadeia Analytics](stream-analytics-get-started.md)
- [Escala Azure da cadeia Analytics tarefas](stream-analytics-scale-jobs.md)
- [Azure sequência Analytics referência da linguagem de consulta](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da sequência Azure Analytics gestão REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
