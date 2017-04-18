<properties
    pageTitle="Exemplos de relatório API de atividade de início de sessão do Azure Active Directory | Microsoft Azure"
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
    ms.date="09/25/2016"
    ms.author="dhanyahk;markvi"/>

# <a name="azure-active-directory-sign-in-activity-report-api-samples"></a>Exemplos de relatório API de atividade de início de sessão do Azure Active Directory

Este tópico faz parte de uma coleção de tópicos sobre o Azure Active Directory API de elaboração de relatórios.  
Azure AD relatórios fornece uma API que permite-lhe aceder a dados de início de sessão no atividade utilizando código ou ferramentas relacionadas.  
O âmbito deste tópico é para lhe fornecer com código de exemplo para o **início de sessão no atividade API**.

Consulte o artigo:

- [Registos de auditoria](active-directory-reporting-azure-portal.md#audit-logs) para obter mais informações conceptuais
- [Introdução ao Azure Active Directory relatórios API](active-directory-reporting-api-getting-started.md) para obter mais informações sobre a API do relata.

Para as questões, problemas ou comentários, contacte o suportehttp [AAD relatórios ajudar](mailto:aadreportinghelp@microsoft.com).


## <a name="prerequisites"></a>Pré-requisitos
Antes de poder utilizar os exemplos neste tópico, tem de concluir os [Pré-requisitos para aceder ao Azure AD API de elaboração de relatórios](active-directory-reporting-api-prerequisites.md).  


##<a name="powershell-script"></a>PowerShell script

    # This script will require the Web Application and permissions setup in Azure Active Directory
    $ClientID       = "<clientId>"             # Should be a ~35 character string insert your info here
    $ClientSecret   = "<clientSecret>"         # Should be a ~44 character string insert your info here
    $loginURL       = "https://login.windows.net/"
    $tenantdomain   = "<tenantDomain>"
    $ daterange            # For example, contoso.onmicrosoft.com

    $7daysago = "{0:s}" -f (get-date).AddDays(-7) + "Z"
    # or, AddMinutes(-5)

    Write-Output $7daysago

    # Get an Oauth 2 access token based on client id, secret and tenant domain
    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}

    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    if ($oauth.access_token -ne $null) {
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

    $url = "https://graph.windows.net/$tenantdomain/activities/signinEvents?api-version=beta&`$filter=signinDateTime ge $7daysago"
    
    $i=0
    
    Do{
        Write-Output "Fetching data using Uri: $url"
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        Write-Output "Save the output to a file SigninActivities$i.json"
        Write-Output "---------------------------------------------"
        $myReport.Content | Out-File -FilePath SigninActivities$i.json -Force
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
        $i = $i+1
    } while($url -ne $null)

    } else {
    
        Write-Host "ERROR: No Access Token"
    }




## <a name="executing-the-script"></a>Executar o script
Quando terminar de editar o script, executá-la e certifique-se de que os dados a partir da auditoria esperados os registos de relatório é devolvido.

O script devolve o resultado do início de sessão no relatório no formato JSON. Também cria uma `SigninActivities.json` ficheiro com o mesmo resultado. Pode experimentar modificando o script para devolver os dados de outros relatórios e comentário saída que não necessita de formatos de exportação.



## <a name="next-steps"></a>Próximos passos

- Deseja personalizar os exemplos neste tópico? Dar saída a [referência da API Azure Active Directory início de sessão no atividade](active-directory-reporting-api-sign-in-activity-reference.md). 

- Se pretender ver uma descrição geral completa da utilização do Azure Active Directory API de elaboração de relatórios, consulte o artigo [Introdução ao Azure Active Directory API de elaboração de relatórios](active-directory-reporting-api-getting-started.md).

- Se quiser saber mais sobre relatórios do Azure Active Directory, consulte o artigo o [Azure elaboração de relatórios Guia do Active Directory](active-directory-reporting-guide.md).  