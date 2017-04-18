<properties
    pageTitle="Azure Active Directory relatórios de auditoria amostras API | Microsoft Azure"
    description="Como começar com o Azure Active Directory relatórios API"
    services="active-directory"
    documentationCenter=""
    authors="dhanyahk"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/28/2016"
    ms.author="dhanyahk;markvi"/>

# <a name="azure-active-directory-reporting-audit-api-samples"></a>Azure Active Directory elaboração de relatórios de auditoria API amostras

Este tópico faz parte de uma coleção de tópicos sobre o Azure Active Directory API de elaboração de relatórios.  
Azure AD relatórios fornece uma API que lhe permita aceder a dados de registo de auditoria utilizando código ou ferramentas relacionadas.
O âmbito deste tópico é para lhe fornecer com código de exemplo para a **API de auditoria**.

Consulte o artigo:

- [Registos de auditoria](active-directory-reporting-azure-portal.md#audit-logs) para obter mais informações conceptuais

- [Introdução ao Azure Active Directory relatórios API](active-directory-reporting-api-getting-started.md) para obter mais informações sobre a API do relata.

Para as questões, problemas ou comentários, contacte o suportehttp [AAD relatórios ajudar](mailto:aadreportinghelp@microsoft.com).


## <a name="prerequisites"></a>Pré-requisitos
Antes de poder utilizar os exemplos neste tópico, tem de concluir os [Pré-requisitos para aceder ao Azure AD API de elaboração de relatórios](active-directory-reporting-api-prerequisites.md).  
  

## <a name="known-issue"></a>Problema conhecido

Aplicação Auth não irá funcionar se for do seu inquilino na região da UE. Utilize a autenticação de utilizador para aceder a API de auditoria como solução até podemos corrigir o problema. 


## <a name="powershell-script"></a>PowerShell script
    # This script will require registration of a Web Application in Azure Active Directory (see https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/)

    # Constants
    $ClientID       = "your-client-application-id-here"       # Insert your application's Client ID, a Globally Unique ID (registered by Global Admin)
    $ClientSecret   = "your-client-application-secret-here"   # Insert your application's Client Key/Secret string
    $loginURL       = "https://login.microsoftonline.com"     # AAD Instance; for example https://login.microsoftonline.com
    $tenantdomain   = "your-tenant-domain.onmicrosoft.com"    # AAD Tenant; for example, contoso.onmicrosoft.com
    $resource       = "https://graph.windows.net"             # Azure AD Graph API resource URI
    $7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
    Write-Output "Searching for events starting $7daysago"

    # Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    # Parse audit report items, save output to file(s): auditX.json, where X = 0 thru n for number of nextLink pages
    if ($oauth.access_token -ne $null) {   
        $i=0
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
        $url = 'https://graph.windows.net/' + $tenantdomain + 'activities/audit?api-version=beta&`$filter=eventTime gt ' + $7daysago

        # loop through each query page (1 through n)
        Do{
            # display each event on the console window
            Write-Output "Fetching data using Uri: $url"
            $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
            foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
                Write-Output ($event | ConvertTo-Json)
            }
        
            # save the query page to an output file
            Write-Output "Save the output to a file audit$i.json"
            $myReport.Content | Out-File -FilePath audit$i.json -Force
            $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
            $i = $i+1
        } while($url -ne $null)
    } else {
        Write-Host "ERROR: No Access Token"
        }

    Write-Host "Press any key to continue ..."
    $x = $host.UI.RawUI.ReadKey("NoEcho,IncludeKeyDown")


### <a name="executing-the-powershell-script"></a>Executar o script PowerShell
Quando terminar de editar o script, executá-la e certifique-se de que os dados a partir da auditoria esperados os registos de relatório é devolvido.

O script devolve o resultado do relatório de auditoria no formato JSON. Também cria uma `audit.json` ficheiro com o mesmo resultado. Pode experimentar modificando o script para devolver os dados de outros relatórios e comentário saída que não necessita de formatos de exportação.


## <a name="bash-script"></a>Script de festa

    #!/bin/bash

    # Author: Ken Hoff (kenhoff@microsoft.com)
    # Date: 2015.08.20
    # NOTE: This script requires jq (https://stedolan.github.io/jq/)

    CLIENT_ID="your-application-client-id-here"         # Should be a ~35 character string insert your info here
    CLIENT_SECRET="your-application-client-secret-here" # Should be a ~44 character string insert your info here
    LOGIN_URL="https://login.windows.net"
    TENANT_DOMAIN="your-directory-name-here.onmicrosoft.com"    # For example, contoso.onmicrosoft.com

    TOKEN_INFO=$(curl -s --data-urlencode "grant_type=client_credentials" --data-urlencode "client_id=$CLIENT_ID" --data-urlencode "client_secret=$CLIENT_SECRET" "$LOGIN_URL/$TENANT_DOMAIN/oauth2/token?api-version=1.0")

    TOKEN_TYPE=$(echo $TOKEN_INFO | ./jq-win64.exe -r '.token_type')
    ACCESS_TOKEN=$(echo $TOKEN_INFO | ./jq-win64.exe -r '.access_token')

    # get yesterday's date

    YESTERDAY=$(date --date='1 day ago' +'%Y-%m-%d')

    URL="https://graph.windows.net/$TENANT_DOMAIN/activities/audit?api-version=beta&$filter=eventTime%20gt%20$YESTERDAY"


    REPORT=$(curl -s --header "Authorization: $TOKEN_TYPE $ACCESS_TOKEN" $URL)

    echo $REPORT | ./jq-win64.exe -r '.value' | ./jq-win64.exe -r ".[]"

## <a name="python-script"></a>Script de Python

    # Author: Michael McLaughlin (michmcla@microsoft.com)
    # Date: January 20, 2016
    # This requires the Python Requests module: http://docs.python-requests.org

    import requests
    import datetime
    import sys

    client_id = 'your-application-client-id-here'
    client_secret = 'your-application-client-secret-here'
    login_url = 'https://login.windows.net/'
    tenant_domain = 'your-directory-name-here.onmicrosoft.com'

    # Get an OAuth access token
    bodyvals = {'client_id': client_id,
                'client_secret': client_secret,
                'grant_type': 'client_credentials'}

    request_url = login_url + tenant_domain + '/oauth2/token?api-version=1.0'
    token_response = requests.post(request_url, data=bodyvals)

    access_token = token_response.json().get('access_token')
    token_type = token_response.json().get('token_type')

    if access_token is None or token_type is None:
        print "ERROR: Couldn't get access token"
        sys.exit(1)

    # Use the access token to make the API request
    yesterday = datetime.date.strftime(datetime.date.today() - datetime.timedelta(days=1), '%Y-%m-%d')

    header_params = {'Authorization': token_type + ' ' + access_token}
    request_string = 'https://graph.windows.net/' + tenant_domain + 'activities/audit?api-version=beta&$filter=eventTime%20gt%20' + yesterday   
    response = requests.get(request_string, headers = header_params)

    if response.status_code is 200:
        print response.content
    else:
        print 'ERROR: API request failed'





## <a name="next-steps"></a>Próximos passos

- Deseja personalizar os exemplos neste tópico? Consulte o artigo [referência da API de auditoria do Azure Active Directory](active-directory-reporting-api-audit-reference.md). 

- Se pretender ver uma descrição geral completa da utilização do Azure Active Directory API de elaboração de relatórios, consulte o artigo [Introdução ao Azure Active Directory API de elaboração de relatórios](active-directory-reporting-api-getting-started.md).

- Se quiser saber mais sobre relatórios do Azure Active Directory, consulte o artigo o [Azure elaboração de relatórios Guia do Active Directory](active-directory-reporting-guide.md).  
