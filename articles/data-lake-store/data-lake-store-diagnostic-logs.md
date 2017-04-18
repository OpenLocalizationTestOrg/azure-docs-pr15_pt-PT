<properties 
   pageTitle="Ver registos de diagnóstico para Azure dados Lake arquivo | Microsoft Azure" 
   description="Compreender como configurar e aceder a registos de diagnóstico para Azure dados Lake arquivo " 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/05/2016"
   ms.author="nitinme"/>

# <a name="accessing-diagnostic-logs-for-azure-data-lake-store"></a>Aceder a registos de diagnóstico para arquivo de Lake de dados do Azure

Saiba mais sobre como ativar o registo de diagnóstico para a sua conta do arquivo de Lake de dados e sobre como ver os registos recolhidos para a sua conta.

As organizações podem permitir que os registos de diagnóstico para a respetiva conta Azure dados Lake arquivo recolher registos de auditoria de acesso de dados que fornece informações como lista de utilizadores aceder aos dados, frequência os dados são acedidos, quantidade de dados está armazenado conta, etc.

## <a name="prerequisites"></a>Pré-requisitos

- **Azure uma subscrição**. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

- **Conta azure dados Lake loja**. Siga as instruções no [artigo Introdução ao arquivo de Lake Azure dados utilizando o Portal do Azure](data-lake-store-get-started-portal.md).

## <a name="enable-diagnostic-logging-for-your-data-lake-store-account"></a>Ativar o registo de diagnóstico para a sua conta do arquivo de Lake de dados

1. Inicie sessão novo [Azure Portal](https://portal.azure.com).

2. Abre a sua conta do arquivo de Lake de dados e a partir do seu pá de conta do arquivo de Lake de dados, clique em **Definições**e, em seguida, clique em **Definições de diagnóstico**.

3. No pá de **diagnóstico** , efetue as seguintes alterações para configurar o registo de diagnóstico.

    ![Ativar o registo de diagnóstico] (./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "Ativar registos de diagnóstico")

    * Definir **Estado** **** para ativar o registo de diagnóstico.
    * Pode optar por/processo store os dados de duas maneiras diferentes.
        * Selecione a opção para **Exportar para o evento concentrador** de dados do registo da cadeia a um concentrador de evento Azure. Provavelmente irá utilizar esta opção se tiver um pipeline de processamento descendentes para analisar recebidos registos em tempo real. Se selecionar esta opção, tem de fornecer os detalhes do Centro de eventos do Azure que pretende utilizar.
        * Selecione a opção para **exportar a conta de armazenamento** para armazenar os registos a uma conta de armazenamento do Windows Azure. Utilize esta opção se pretender arquivar os dados que serão processadas lote numa data posterior. Se selecionar esta opção deve fornecer uma conta de armazenamento do Windows Azure para guardar os registos para.
    * Especifique se pretende obter registos de auditoria ou pedido de registos ou ambos.
    * Especifique o número de dias para a qual os dados devem ser guardados.
    * Clique em **Guardar**.

Assim que tiver activado as definições de diagnóstico, pode ver os registos no separador de **Registos de diagnóstico** .

## <a name="view-diagnostic-logs-for-your-data-lake-store-account"></a>Ver registos de diagnóstico para a sua conta do arquivo de Lake de dados

Existem duas formas de ver os dados do registo para a sua conta do arquivo de Lake de dados.

* A partir da vista de definições de conta do arquivo de Lake de dados
* Da conta de armazenamento do Windows Azure, onde os dados são armazenados

### <a name="using-the-data-lake-store-settings-view"></a>Utilizar a vista de definições de arquivo de Lake de dados

1. A partir do seu pá **Definições** de conta de arquivo de Lake de dados, clique em **Registos de diagnóstico**.

    ![Registo de diagnóstico de vista] (./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "Ver registos de diagnóstico") 

2. No pá **Registos de diagnóstico** , deverá ver os registos categorizados por **Pedir registos**e **Registos de auditoria** .
    * Registos de pedido capturam todos os pedidos de API efetuados na conta do arquivo de Lake de dados.
    * Registos de auditoria são semelhantes às pedir registos mas fornece uma desagregação muito mais detalhada das operações a ser executado na conta do arquivo de Lake de dados. Por exemplo, uma chamada de API carregamento única nos registos do pedido poderá resultar em várias operações "Acrescentar" nos registos de auditoria.

3. Clique na ligação **Transfira** contra cada entrada de registo para transferir os registos.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>A partir da conta de armazenamento do Windows Azure que contém os dados do registo

1. Abra a pá de conta de armazenamento do Windows Azure associado arquivo de dados de Lake para o registo e, em seguida, clique em Blobs. O **serviço de BLOBs** pá lista dois contentores.

    ![Registo de diagnóstico de vista] (./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "Ver registos de diagnóstico")

    * O contentor **informações-os registos de auditoria** contém os registos de auditoria.
    * O contentor **pedidos de informações de registos de início** contém os registos do pedido.

2. Dentro destes contentores, os registos são armazenados na seguinte estrutura.

    ![Registo de diagnóstico de vista] (./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "Ver registos de diagnóstico")

    Por exemplo, pode dever o caminho completo para um registo de auditoria`https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=04/m=00/PT1H.json`

    Similary, o caminho completo para um registo de requisição pode dever`https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="understand-the-structure-of-the-log-data"></a>Compreender a estrutura dos dados de registo

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
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
             "category": "Requests",
             "operationName": "GETCustomerIngressEgress",
             "resultType": "200",
             "callerIpAddress": "::ffff:1.1.1.1",
             "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
             "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
             "properties": {"HttpMethod":"GET","Path":"/webhdfs/v1/Samples/Outputs/Drivers.csv","RequestContentLength":0,"ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8","StartTime":"2016-07-07T21:02:52.472Z","EndTime":"2016-07-07T21:02:53.456Z"}
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
| operationName   | Cadeia | Nome da operação de que tem sessão iniciada. Por exemplo, getfilestatus.              |
| resultType      | Cadeia | O estado da operação de, por exemplo, 200.                                 |
| callerIpAddress | Cadeia | O endereço IP do cliente em fazer o pedido                                |
| correlationId   | Cadeia | O id do registo que pode utilizado para agrupar um conjunto de entradas de registo relacionados |
| identidade        | Objecto | A identidade que gerou o registo                                            |
| Propriedades      | JSON   | Consulte abaixo para obter mais detalhes                                                          |

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
             "time": "2016-07-08T19:08:59.359Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
             "category": "Audit",
             "operationName": "SeOpenStream",
             "resultType": "0",
             "correlationId": "381110fc03534e1cb99ec52376ceebdf;Append_BrEKAmg;25.66.9.145",
             "identity": "A9DAFFAF-FFEE-4BB5-A4A0-1B6CBBF24355",
             "properties": {"StreamName":"adl://<data_lake_store_account_name>.azuredatalakestore.net/logs.csv"}
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
| operationName   | Cadeia | Nome da operação de que tem sessão iniciada. Por exemplo, getfilestatus.              |
| resultType      | Cadeia | O estado da operação de, por exemplo, 200.                                 |
| correlationId   | Cadeia | O id do registo que pode utilizado para agrupar um conjunto de entradas de registo relacionados |
| identidade        | Objecto | A identidade que gerou o registo                                            |
| Propriedades      | JSON   | Consulte abaixo para obter mais detalhes                                                          |

#### <a name="audit-log-properties-schema"></a>Esquema de propriedades do registo de auditoria

| Nome       | Tipo   | Descrição                              |
|------------|--------|------------------------------------------|
| StreamName | Cadeia | O caminho a operação foi executado nas  |


## <a name="samples-to-process-the-log-data"></a>Exemplos para processar os dados do registo

Azure dados Lake arquivo fornece um exemplo sobre como processar e analisar os dados do registo. Pode encontrar a amostra na [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample). 


## <a name="see-also"></a>Consulte também

- [Descrição geral do Azure Lake de arquivo de dados](data-lake-store-overview.md)
- [Proteger os dados no arquivo de dados de Lake](data-lake-store-secure-data.md)

