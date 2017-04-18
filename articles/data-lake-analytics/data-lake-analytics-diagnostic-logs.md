<properties 
   pageTitle="Ver registos de diagnóstico para análise do Azure dados Lake | Microsoft Azure" 
   description="Compreender como configurar e aceder a registos de diagnóstico para análise Azure dados Lake " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="Blackmist" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="08/11/2016"
   ms.author="larryfr"/>

# <a name="accessing-diagnostic-logs-for-azure-data-lake-analytics"></a>Aceder a registos de diagnóstico para análise do Azure dados Lake

Saiba mais sobre como ativar o registo de diagnóstico para a sua conta de dados Lake Analytics e sobre como ver os registos recolhidos para a sua conta.

As organizações podem ativar o registo de diagnóstico para a respetiva conta Azure dados Lake Analytics recolher registos de auditoria de acesso de dados. Estes registos fornecem informações tais como:

* Uma lista de utilizadores que pode ser consultada os dados.
* Com que frequência os dados são acedidos.
* Quantidade de dados é armazenado na conta.

## <a name="prerequisites"></a>Pré-requisitos

- **Azure uma subscrição**. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
- **Ativar a sua subscrição do Azure** para dados Lake Analytics público pré-visualização. Consulte as [instruções](data-lake-analytics-get-started-portal.md#signup).
- **Conta azure dados Lake Analytics**. Siga as instruções no [artigo Introdução ao Azure dados Lake Analytics através do portal Azure](data-lake-analytics-get-started-portal.md).

## <a name="enable-logging"></a>Ativar o registo

1. Inicie sessão novo [Azure portal](https://portal.azure.com).

2. Abre a sua conta de análise de Lake de dados e a partir do seu pá conta dados Lake análise, clique em **Definições**e, em seguida, clique em **Definições de diagnóstico**.

3. No pá de **diagnóstico** , efetue as seguintes alterações para configurar o registo de diagnóstico.

    ![Ativar o registo de diagnóstico] (./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "Ativar registos de diagnóstico")

    * Definir **Estado** **** para ativar o registo de diagnóstico.
    * Pode optar por/processo store os dados de duas maneiras diferentes.
        * Selecione **Exportar para o Centro de evento** para os dados do registo da cadeia a um concentrador de evento Azure. Utilize esta opção se tiver um pipeline de processamento descendentes para analisar recebidos registos em tempo real. Se selecionar esta opção, tem de fornecer os detalhes do Centro de eventos do Azure que pretende utilizar.
        * Selecione **Exportar para a conta de armazenamento** para armazenar os registos a uma conta de armazenamento do Windows Azure. Utilize esta opção se pretender arquivar os dados. Se selecionar esta opção, tem de fornecer uma conta de armazenamento do Windows Azure para guardar os registos para.
    * Especifique se pretende obter registos de auditoria ou pedido de registos ou ambos.
    * Especifique o número de dias para a qual os dados devem ser guardados.
    * Clique em **Guardar**.

Assim que tiver activado as definições de diagnóstico, pode ver os registos no separador de **Registos de diagnóstico** .

## <a name="view-logs"></a>Ver registos

Existem duas formas de ver os dados do registo para a sua conta de análise de Lake de dados.

* A partir das definições da conta dados Lake Analytics
* Da conta de armazenamento do Windows Azure, onde os dados são armazenados

### <a name="using-the-data-lake-analytics-settings-view"></a>Utilizar a vista de dados Lake Analytics definições

1. A partir do seu pá de **Definições** de conta dados Lake análise, clique em **Registos de diagnóstico**.

    ![Registo de diagnóstico de vista] (./media/data-lake-analytics-diagnostic-logs/view-diagnostic-logs.png "Ver registos de diagnóstico") 

2. No pá **Registos de diagnóstico** , deverá ver os registos categorizados por **Pedir registos**e **Registos de auditoria** .
    * Registos de pedido capturam todos os pedidos de API efetuados na conta do dados Lake Analytics.
    * Registos de auditoria são semelhantes às pedir registos mas fornece uma desagregação muito mais detalhada das operações a ser executado na conta do dados Lake Analytics. Por exemplo, uma chamada de API carregamento única nos registos do pedido poderá resultar em várias operações "Acrescentar" nos registos de auditoria.

3. Clique na ligação **Transferir** para uma entrada de registo transferir os registos.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>A partir da conta de armazenamento do Windows Azure que contém os dados do registo

1. Abra o pá de conta de armazenamento do Windows Azure associado Lake a análise de dados para o registo e, em seguida, clique em Blobs. O **serviço de BLOBs** pá lista dois contentores.

    ![Registo de diagnóstico de vista] (./media/data-lake-analytics-diagnostic-logs/view-diagnostic-logs-storage-account.png "Ver registos de diagnóstico")

    * O contentor **informações-os registos de auditoria** contém os registos de auditoria.
    * O contentor **pedidos de informações de registos de início** contém os registos do pedido.

2. Dentro destes contentores, os registos são armazenados na seguinte estrutura.

        resourceId=/
          SUBSCRIPTIONS/
            <<SUBSCRIPTION_ID>>/
              RESOURCEGROUPS/
                <<RESOURCE_GRP_NAME>>/
                  PROVIDERS/
                    MICROSOFT.DATALAKEANALYTICS/
                      ACCOUNTS/
                        <DATA_LAKE_ANALYTICS_NAME>>/
                          y=####/
                            m=##/
                              d=##/
                                h=##/
                                  m=00/
                                    PT1H.json
    
    > [AZURE.NOTE] O `##` as entradas no caminho de conter o ano, mês, dia e hora em que foi criado o registo. A análise de dados Lake cria por isso, cada hora, de um ficheiro `m=` contém sempre um valor de `00`.

    Por exemplo, pode ser o caminho completo para um registo de auditoria:
    
        https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=04/m=00/PT1H.json

    Da mesma forma, pode ser o caminho completo para um registo do pedido:
    
        https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=14/m=00/PT1H.json

## <a name="log-structure"></a>Estrutura de registo

Os registos de auditoria e pedido são num formato JSON. Nesta secção, observe a estrutura de JSON pedido e registos de auditoria.

### <a name="request-logs"></a>Pedido de registos

Eis um exemplo de entrada no registo de formatados como JSON pedido. Cada blob tem um objeto de raiz denominado **registos** que contém uma matriz de objetos de registo.

    {
    "records": 
      [     
        . . . .
        ,
        {
             "time": "2016-07-07T21:02:53.456Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_analytics_account_name>",
             "category": "Requests",
             "operationName": "GetAggregatedJobHistory",
             "resultType": "200",
             "callerIpAddress": "::ffff:1.1.1.1",
             "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
             "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
             "properties": {
                 "HttpMethod":"POST",
                 "Path":"/JobAggregatedHistory",
                 "RequestContentLength":122,
                 "ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8",
                 "StartTime":"2016-07-07T21:02:52.472Z",
                 "EndTime":"2016-07-07T21:02:53.456Z"
                 }
        }
        ,
        . . . .
      ]
    }

#### <a name="request-log-schema"></a>Esquema do pedido de registo

| Nome            | Tipo   | Descrição                                                                    |
|-----------------|--------|--------------------------------------------------------------------------------|
| tempo            | Cadeia | O carimbo de hora (no UTC) do registo                                              |
| resourceId      | Cadeia | Coloque o ID do recurso que tenha apontada operação na                            |
| categoria        | Cadeia | A categoria de registo. Por exemplo, **pedidos**.                                   |
| operationName   | Cadeia | Nome da operação de que tem sessão iniciada. Por exemplo, GetAggregatedJobHistory.              |
| resultType      | Cadeia | O estado da operação de, por exemplo, 200.                                 |
| callerIpAddress | Cadeia | O endereço IP do cliente em fazer o pedido                                |
| correlationId   | Cadeia | O id do registo. Este valor pode ser utilizada para agrupar um conjunto de entradas de registo relacionados |
| identidade        | Objecto | A identidade que gerou o registo                                            |
| Propriedades      | JSON   | Consulte a secção seguinte (esquema de propriedades do pedido de registo) para obter mais detalhes |

#### <a name="request-log-properties-schema"></a>Esquema de propriedades do pedido de registo

| Nome                 | Tipo   | Descrição                                               |
|----------------------|--------|-----------------------------------------------------------|
| HttpMethod           | Cadeia | O método de HTTP utilizado para a operação. Por exemplo, obtenha. |
| Caminho                 | Cadeia | O caminho a operação foi executado nas                   |
| RequestContentLength | Int    | O comprimento do conteúdo do pedido HTTP                    |
| ClientRequestId      | Cadeia | O Id que identifica exclusivamente este pedido              |
| Hora de início            | Cadeia | Data e hora em que o servidor recebeu o pedido         |
| Hora de fim              | Cadeia | A hora em que o servidor enviada uma resposta              |

### <a name="audit-logs"></a>Registos de auditoria

Eis um exemplo de entrada no registo de auditoria formatados como JSON. Cada blob tem um objeto de raiz denominado **registos** que contém uma matriz de objetos de registo

    {
    "records": 
      [     
        . . . .
        ,
        {
             "time": "2016-07-28T19:15:16.245Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_ANALYTICS_account_name>",
             "category": "Audit",
             "operationName": "JobSubmitted",
             "identity": "user@somewhere.com",
             "properties": {
                 "JobId":"D74B928F-5194-4E6C-971F-C27026C290E6",
                 "JobName": "New Job", 
                 "JobRuntimeName": "default",
                 "SubmitTime": "7/28/2016 7:14:57 PM"
                 }
        }
        ,
        . . . .
      ]
    }

#### <a name="audit-log-schema"></a>Esquema do registo de auditoria

| Nome            | Tipo   | Descrição                                                                    |
|-----------------|--------|--------------------------------------------------------------------------------|
| tempo            | Cadeia | O carimbo de hora (no UTC) do registo                                              |
| resourceId      | Cadeia | Coloque o ID do recurso que tenha apontada operação na                            |
| categoria        | Cadeia | A categoria de registo. Por exemplo, **auditoria**.                                      |
| operationName   | Cadeia | Nome da operação de que tem sessão iniciada. Por exemplo, JobSubmitted.              |
| resultType | Cadeia | Um subestado para o estado da tarefa (operationName). |
| resultSignature | Cadeia | Detalhes adicionais sobre o estado da tarefa (operationName). |
| identidade      | Cadeia | O utilizador que pediram a operação. Por exemplo, susan@contoso.com.                                 |
| Propriedades      | JSON   | Consulte a secção seguinte (esquema de propriedades de registo de auditoria) para obter mais detalhes |

> [AZURE.NOTE] __resultType__ __resultSignature__ fornecem informações sobre o resultado de uma operação e conter apenas um valor se tiver concluído uma operação. Por exemplo, contêm um valor quando __operationName__ contém um valor de __JobStarted__ ou __JobEnded__.

#### <a name="audit-log-properties-schema"></a>Esquema de propriedades do registo de auditoria

| Nome       | Tipo   | Descrição                              |
|------------|--------|------------------------------------------|
| JobId | Cadeia | O ID atribuído à tarefa  |
| Nometarefa | Cadeia | O nome que foi fornecido para a tarefa |
| JobRunTime | Cadeia | O tempo de execução utilizado para processar a tarefa |
| SubmitTime | Cadeia | O tempo (UTC) que a tarefa foi submetida |
| Hora de início | Cadeia | A hora em que a tarefa de iniciada a ser executado depois de submissão (no UTC). |
| Hora de fim | Cadeia | A hora em que a tarefa foi terminada. |
| Paralelismo | Cadeia | O número de unidades dados Lake Analytics pedidas para esta tarefa durante a apresentação. |

> [AZURE.NOTE] __SubmitTime__, __hora de início__, __hora de fim__ e __paralelismo__ fornecem informações sobre uma operação e contenham apenas um valor se uma operação tem iniciadas ou concluídas. Por exemplo, __SubmitTime__ contém um valor após __operationName__ indica __JobSubmitted__.

## <a name="process-the-log-data"></a>Processo dos dados do registo

Azure dados Lake Analytics fornece um exemplo sobre como processar e analisar os dados do registo. Pode encontrar a amostra na [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample). 


## <a name="next-steps"></a>Próximos passos

- [Descrição geral dos dados Azure Lake Analytics](data-lake-analytics-overview.md)


