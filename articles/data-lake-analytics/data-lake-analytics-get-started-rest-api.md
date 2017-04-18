<properties 
   pageTitle="Introdução ao utilizar a REST API a análise de dados Lake | Microsoft Azure" 
   description="Utilizar WebHDFS REST APIs para efetuar operações nos dados Lake Analytics" 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/19/2016"
   ms.author="jgao"/>

# <a name="get-started-with-azure-data-lake-analytics-using-rest-apis"></a>Introdução ao Azure dados Lake Analytics utilizando REST APIs

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Saiba como utilizar WebHDFS REST APIs e dados Lake Analytics REST APIs para gerir contas, tarefas e catálogo de dados Lake Analytics. 

## <a name="prerequisites"></a>Pré-requisitos

- **Azure uma subscrição**. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

- **Criar uma aplicação do Azure Active Directory**. Utilize a aplicação do Azure AD para autenticar a aplicação de análise de Lake de dados com o Azure AD. Existem abordagens diferentes para autenticar com Azure AD, que são **autenticação de utilizador final** ou do **serviço de serviço**. Para obter instruções e mais informações sobre como autenticar, consulte o artigo [autenticar com a análise de Lake dados utilizando o Azure Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md).

- [cURL](http://curl.haxx.se/). Este artigo utiliza Laço para demonstrar como efetuar chamadas de REST API contra uma conta de análise de Lake de dados.

## <a name="authenticate-with-azure-active-directory"></a>Autenticar com o Azure Active Directory

Existem dois métodos para autenticar com o Azure Active Directory.

### <a name="end-user-authentication-interactive"></a>Autenticação de utilizador final (interativa)

Com este método, aplicação pede ao utilizador para iniciar sessão e todas as operações são executadas no contexto do utilizador. 

Siga estes passos para autenticação interativo:

1. Através da aplicação, redirecione o utilizador para o seguinte URL:

        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<CLIENT-ID>&response_type=code&redirect_uri=<REDIRECT-URI>

    >[AZURE.NOTE] \<REDIRECIONAR URI > tem de ser codificado para utilização num URL. Sim, para https://localhost, utilize `https%3A%2F%2Flocalhost`)

    Para este tutorial, pode substituir os valores de marcador de posição no URL acima e colá-lo na barra de endereço do browser. Serão redirecionados para autenticar utilizando o início de sessão Azure. Assim que iniciar sessão com êxito no, é apresentada a resposta na barra de endereço do browser. A resposta será no seguinte formato:
        
        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>

2. Capture o código de autorização da resposta. Para este tutorial, pode copiar o código de autorização a partir da barra de endereço do browser e transmitir-a no pedido de mensagem para o ponto final do token, conforme apresentado abaixo:

        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
        -F redirect_uri=<REDIRECT-URI> \
        -F grant_type=authorization_code \
        -F resource=https://management.core.windows.net/ \
        -F client_id=<CLIENT-ID> \
        -F code=<AUTHORIZATION-CODE>

    >[AZURE.NOTE] Neste caso, a \<REDIRECIONAR URI > não precisa de ser codificado.

3. A resposta é um objeto de JSON que contém um token de acesso (por exemplo, `"access_token": "<ACCESS_TOKEN>"`) e um token de atualização (por exemplo, `"refresh_token": "<REFRESH_TOKEN>"`). A aplicação utiliza o token de acesso ao aceder ao arquivo de Lake de dados do Azure e o token de atualização para obter outro token de acesso, quando um token de acesso expira.

        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before": "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}

4.  Quando o token de acesso expira, pode pedir um novo token de acesso utilizando o token de atualização, conforme apresentado abaixo:

         curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
            -F grant_type=refresh_token \
            -F resource=https://management.core.windows.net/ \
            -F client_id=<CLIENT-ID> \
            -F refresh_token=<REFRESH-TOKEN>
 
Para mais informações sobre a autenticação de utilizador interactivo, ver o [código de autorização conceder fluxo](https://msdn.microsoft.com/library/azure/dn645542.aspx).

### <a name="service-to-service-authentication-non-interactive"></a>Serviço de serviço de autenticação (não interativa)

Com este método, aplicação fornece as suas próprias credenciais para realizar as operações. Para que isto, tem de emitir um pedido de mensagem como aquele apresentado abaixo: 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

O resultado deste pedido irá incluir um token de autorização (representada por `access-token` na saída abaixo) que serão posteriormente ser efetuada com as suas chamadas REST API. Guardar este token de autenticação num ficheiro de texto; terá este neste artigo.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

Este artigo utiliza o **não interativa** abordagem. Para mais informações sobre não interativa (chamadas de serviço-para-service), consulte [serviço para chamadas de serviço utilizando credenciais](https://msdn.microsoft.com/library/azure/dn645543.aspx).
## <a name="create-a-data-lake-analytics-account"></a>Criar uma conta de dados Lake Analytics

Tem de criar um grupo de recursos do Azure e uma conta do arquivo de Lake dados antes de poder criar uma conta de análise de Lake de dados.  Consulte o artigo [criar uma conta do arquivo de dados de Lake](../data-lake-store/data-lake-store-get-started-rest-api.md#create-a-data-lake-store-account).

O comando Laço seguinte mostra como criar uma conta:

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<NewAzureDataLakeAnalyticsAccountName>?api-version=2016-11-01 -d@"C:\tutorials\adla\CreateDataLakeAnalyticsAccountRequest.json"

Substituir \< `REDACTED` \> com o token de autorização \< `AzureSubscriptionID` \> com o seu ID da subscrição, \< `AzureResourceGroupName` \> com um nome de grupo de recursos do Azure existente, e \< `NewAzureDataLakeAnalyticsAccountName` \> com um novo nome de conta de análise de Lake de dados. A carga útil pedido para este comando encontra-se no ficheiro **CreateDatalakeAnalyticsAccountRequest.json** que é fornecido para a `-d` parâmetro acima. O conteúdo do ficheiro input.json semelhante ao seguinte:

    {  
        "location": "East US 2",  
        "name": "myadla1004",  
        "tags": {},  
        "properties": {  
            "defaultDataLakeStoreAccount": "my1004store",  
            "dataLakeStoreAccounts": [  
                {  
                    "name": "my1004store"  
                }     
            ]
        }  
    }  


## <a name="list-data-lake-analytics-accounts-in-a-subscription"></a>Contas de análise de Lake de dados de lista numa subscrição

O comando Laço seguinte mostra como a lista de contas numa subscrição:

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/providers/Microsoft.DataLakeAnalytics/Accounts?api-version=2016-11-01

Substituir \< `REDACTED` \> com o token de autorização \< `AzureSubscriptionID` \> com o seu ID de subscrição. O resultado é semelhante a:

    {
        "value": [
            {
            "properties": {
                "provisioningState": "Succeeded",
                "state": "Active",
                "endpoint": "myadla0831.azuredatalakeanalytics.net",
                "accountId": "21e74660-0941-4880-ae72-b143c2615ea9",
                "creationTime": "2016-09-01T12:49:12.7451428Z",
                "lastModifiedTime": "2016-09-01T12:49:12.7451428Z"
            },
            "location": "East US 2",
            "tags": {},
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla0831rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla0831",
            "name": "myadla0831",
            "type": "Microsoft.DataLakeAnalytics/accounts"
            },
            {
            "properties": {
                "provisioningState": "Succeeded",
                "state": "Active",
                "endpoint": "myadla1004.azuredatalakeanalytics.net",
                "accountId": "3ff9b93b-11c4-43c6-83cc-276292eeb350",
                "creationTime": "2016-10-04T20:46:42.287147Z",
                "lastModifiedTime": "2016-10-04T20:46:42.287147Z"
            },
            "location": "East US 2",
            "tags": {},
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004",
            "name": "myadla1004",
            "type": "Microsoft.DataLakeAnalytics/accounts"
            }
        ]
    }

## <a name="get-information-about-a-data-lake-analytics-account"></a>Obter informações acerca de uma conta de dados Lake Analytics

O comando Laço seguinte mostra como obter informações de conta:

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<DataLakeAnalyticsAccountName>?api-version=2015-11-01

Substituir \< `REDACTED` \> com o token de autorização \< `AzureSubscriptionID` \> com o seu ID da subscrição, \< `AzureResourceGroupName` \> com um nome de grupo de recursos do Azure existente, e \< `DataLakeAnalyticsAccountName` \> com o nome de uma conta de análise de Lake de dados existente. O resultado é semelhante a:

    {
        "properties": {
            "defaultDataLakeStoreAccount": "my1004store",
            "dataLakeStoreAccounts": [
            {
                "properties": {
                "suffix": "azuredatalakestore.net"
                },
                "name": "my1004store"
            }
            ],
            "provisioningState": "Creating",
            "state": null,
            "endpoint": null,
            "accountId": "3ff9b93b-11c4-43c6-83cc-276292eeb350",
            "creationTime": null,
            "lastModifiedTime": null
        },
        "location": "East US 2",
        "tags": {},
        "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004",
        "name": "myadla1004",
        "type": "Microsoft.DataLakeAnalytics/accounts"
    }

## <a name="list-data-lake-stores-of-a-data-lake-analytics-account"></a>Lista de arquivos de Lake de dados de uma conta de dados Lake Analytics

O seguinte comando Laço mostra como listar arquivos de Lake de dados de uma conta:

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<DataLakeAnalyticsAccountName>/DataLakeStoreAccounts/?api-version=2016-11-01

Substituir \< `REDACTED` \> com o token de autorização \< `AzureSubscriptionID` \> com o seu ID da subscrição, \< `AzureResourceGroupName` \> com um nome de grupo de recursos do Azure existente, e \< `DataLakeAnalyticsAccountName` \> com o nome de uma conta de análise de Lake de dados existente. O resultado é semelhante a:

    {
        "value": [
            {
            "properties": {
                "suffix": "azuredatalakestore.net"
            },
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004/dataLakeStoreAccounts/my1004store",
            "name": "my1004store",
            "type": "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts"
            }
        ]
    }

## <a name="submit-u-sql-jobs"></a>Submeter tarefas U SQL

O comando Laço seguinte mostra como submeter uma tarefa U SQL:

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/Jobs/<NewGUID>?api-version=2016-03-20-preview -d@"C:\tutorials\adla\SubmitADLAJob.json"

Substituir \< `REDACTED` \> com o token de autorização \< `DataLakeAnalyticsAccountName` \> com o nome de uma conta de análise de Lake de dados existente. A carga útil pedido para este comando encontra-se no ficheiro **SubmitADLAJob.json** que é fornecido para a `-d` parâmetro acima. O conteúdo do ficheiro input.json semelhante ao seguinte:

    {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "degreeOfParallelism": 1,
        "priority": 1000,
        "properties": {
            "type": "USql",
            "script": "@searchlog =\n    EXTRACT UserId          int,\n            Start           DateTime,\n            Region          string,\n            Query          
        string,\n            Duration        int?,\n            Urls            string,\n            ClickedUrls     string\n    FROM \"/Samples/Data/SearchLog.tsv\"\n    US
        ING Extractors.Tsv();\n\nOUTPUT @searchlog   \n    TO \"/Output/SearchLog-from-Data-Lake.csv\"\nUSING Outputters.Csv();"
        }
    }

O resultado é semelhante a:

    {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "myadl@SPI",
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "2016-10-05T13:54:59.9871859+00:00",
        "state": "Compiling",
        "result": "Succeeded",
        "stateAuditRecords": [
            {
            "newState": "New",
            "timeStamp": "2016-10-05T13:54:59.9871859+00:00",
            "details": "userName:myadl@SPI;submitMachine:N/A"
            }
        ],
        "properties": {
            "owner": "myadl@SPI",
            "resources": [],
            "runtimeVersion": "default",
            "rootProcessNodeId": "00000000-0000-0000-0000-000000000000",
            "algebraFilePath": "adl://myadls0831.azuredatalakestore.net/system/jobservice/jobs/Usql/2016/10/05/13/54/8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a/algebra.xml",
            "compileMode": "Semantic",
            "errorSource": "Unknown",
            "totalCompilationTime": "PT0S",
            "totalPausedTime": "PT0S",
            "totalQueuedTime": "PT0S",
            "totalRunningTime": "PT0S",
            "type": "USql"
        }
    }


## <a name="list-u-sql-jobs"></a>Lista tarefas de U SQL

O comando Laço seguinte mostra como listar U SQL tarefas:

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/Jobs?api-version=2016-11-01 

Substituir \< `REDACTED` \> com o token de autorização e \< `DataLakeAnalyticsAccountName` \> com o nome de uma conta de análise de Lake de dados existente. 


O resultado é semelhante a:

    {
    "value": [
        {
        "jobId": "65cf1691-9dbe-43cd-90ed-1cafbfb406fb",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "someone@microsoft.com",
        "account": null,
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "Wed, 05 Oct 2016 13:46:53 GMT",
        "startTime": "Wed, 05 Oct 2016 13:47:33 GMT",
        "endTime": "Wed, 05 Oct 2016 13:48:07 GMT",
        "state": "Ended",
        "result": "Succeeded",
        "errorMessage": null,
        "storageAccounts": null,
        "stateAuditRecords": null,
        "logFilePatterns": null,
        "properties": null
        },
        {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "someoneadl@SPI",
        "account": null,
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "Wed, 05 Oct 2016 13:54:59 GMT",
        "startTime": "Wed, 05 Oct 2016 13:55:43 GMT",
        "endTime": "Wed, 05 Oct 2016 13:56:11 GMT",
        "state": "Ended",
        "result": "Succeeded",
        "errorMessage": null,
        "storageAccounts": null,
        "stateAuditRecords": null,
        "logFilePatterns": null,
        "properties": null
        }
    ],
    "nextLink": null,
    "count": null
    }


## <a name="get-catalog-items"></a>Obter itens de catálogo.

O comando Laço seguinte mostra como obter as bases de dados do catálogo:

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/catalog/usql/databases?api-version=2016-11-01

O resultado é semelhante a:

    {
    "@odata.context":"https://myadla0831.azuredatalakeanalytics.net/sqlip/$metadata#databases","value":[
        {
        "computeAccountName":"myadla0831","databaseName":"mytest","version":"f6956327-90b8-4648-ad8b-de3ff09274ea"
        },{
        "computeAccountName":"myadla0831","databaseName":"master","version":"e8bca908-cc73-41a3-9564-e9bcfaa21f4e"
        }
    ]
    }

## <a name="see-also"></a>Consulte também

- Para ver uma consulta mais complexa, consulte o artigo [registos de Web site de analisar utilizando a análise do Azure dados Lake](data-lake-analytics-analyze-weblogs.md).
- Para começar a desenvolver aplicações U SQL, consulte o artigo [scripts de desenvolver U-SQL utilizando ferramentas de Lake de dados para o Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Para saber U SQL, consulte [Introdução ao idioma do Azure dados Lake Analytics U-SQL](data-lake-analytics-u-sql-get-started.md).
- Para tarefas de gestão, consulte o artigo [Gerir Azure dados Lake a análise de utilização Azure portal](data-lake-analytics-manage-use-portal.md).
- Para obter uma descrição geral do Lake a análise de dados, consulte o artigo [Descrição geral do Azure dados Lake Analytics](data-lake-analytics-overview.md).
- Para ver o mesmo tutorial utilizar outras ferramentas, clique em seletores o separador na parte superior da página.
- Para registar informações de diagnóstico, consulte o artigo [aceder a registos de diagnóstico para análise do Azure dados Lake](data-lake-analytics-diagnostic-logs.md)
