<properties
    pageTitle="As aplicações que utilizam regras de acesso condicional do Azure Active Directory | Microsoft Azure"
    description="Com o controlo de acesso condicional, o Azure Active Directory verifica para condições específicas quando autentica o utilizador e para permitir o acesso de aplicação."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="10/26/2016"
    ms.author="markvi"/>

# <a name="applications-that-use-conditional-access-rules-in-azure-active-directory"></a>Aplicações que utilizam regras de acesso condicional do Azure Active Directory

Regras de acesso condicional são suportadas no Azure Active Directory (Azure AD)-ligados aplicações, previamente integrada software federado como um aplicações de serviço (SaaS), as aplicações que utilizam palavra-passe de sessão único (SSO), aplicações de linha de negócio e aplicações que utilizam o Proxy de aplicação do Azure AD. Para obter uma lista de aplicações para o qual pode utilizar o access condicional detalhada, consulte o artigo [Serviços activados com acesso condicional](active-directory-conditional-access-technical-reference.md#Services-enabled-with-conditional-access). Acesso condicional funciona tanto com aplicações de ambiente de trabalho e móveis que utilizam autenticação moderna. Neste artigo, vamos abordar condicional como o access trabalha nas aplicações de ambiente de trabalho e móveis.

Pode utilizar páginas de início de sessão no Azure AD aplicações que utilizam autenticação moderna. Com uma página de início de sessão no, um utilizador é-lhe pedido para autenticação multifator. Uma mensagem é apresentada se o acesso do utilizador é bloqueado. Autenticação moderna é necessária para o dispositivo autenticar com Azure AD, para que são avaliadas as políticas de acesso condicional com base no dispositivo.

É importante saber as aplicações que podem utilizar as regras de acesso condicional e os passos que poderá ter de tomar para proteger a outros pontos de entrada de aplicação.

## <a name="applications-that-use-modern-authentication"></a>Aplicações que utilizam autenticação moderna

> [AZURE.NOTE] Se tiver uma política de acesso condicional no Azure AD que tenha um equivalente no Office 365, configure ambas as políticas de acesso condicional. Isto teria se, por exemplo, para políticas de acesso condicional para o Exchange Online ou do SharePoint Online.

As seguintes aplicações suportam acesso condicional para Office 365 e outras aplicações de serviço Azure AD-ligado:

| Serviço de destino  | Plataforma  | Aplicação                                                  |
|--------------|-----------------|----------------------------------------------------------------|
|Office 365 Exchange Online | Windows 10|Aplicação calendário/correio/pessoas, no Outlook 2016, o Outlook 2013 (com autenticação moderna), o Skype para empresas (com autenticação moderna)|
|Office 365 Exchange Online| Windows 8.1, Windows 7 |Outlook 2016, o Outlook 2013 (com autenticação moderna), Skype para empresas (com autenticação moderna)|
|Office 365 Exchange Online|iOS, Android|  Aplicação móvel do Outlook|
|Office 365 Exchange Online|Mac OS X| Outlook 2016 para autenticação multifator e a localização apenas; suporte de política com base no dispositivo planeada para o futuro, Skype para empresas suporte planeado para o futuro|
|SharePoint Online do Office 365|Windows 10| Aplicações do Office 2016, as aplicações do Universal Office, Office 2013 (com a autenticação moderna), OneDrive para empresas aplicação (seguinte cliente de sincronização. ª geração ou NGSC) de suporte planeados para o futuro, o suporte de grupos do Office planeada para o futuro, o suporte de aplicação do SharePoint planeada para o futuro|
|SharePoint Online do Office 365|Windows 8.1, Windows 7|Aplicações do Office 2016, Office 2013 (com autenticação moderna), a aplicação OneDrive para empresas (cliente de sincronização do Groove)|
|SharePoint Online do Office 365|iOS, Android|  Aplicações do Office mobile |
|SharePoint Online do Office 365|Mac OS X| Aplicações do Office 2016 para autenticação multifator e a localização apenas; suporte de política com base no dispositivo planeada para o futuro|
|Yammer do Office 365|Windows 10, iOS e Android | Aplicação do Office do Yammer|
|Dynamics CRM|Windows 10, Windows 8.1, Windows 7, iOS e Android | Aplicação do Dynamics CRM|
|Serviço de obter|Windows 10, Windows 8.1, Windows 7, iOS e Android | Obter aplicação|
|Serviço de aplicação Remote Azure|Windows 10, Windows 8.1, Windows 7, iOS, Android e Mac OS X |Aplicação Remote Azure|
|Qualquer aplicação de serviço das minhas aplicações|Android e iOS|Qualquer aplicação de serviço das minhas aplicações |


## <a name="applications-that-do-not-use-modern-authentication"></a>Aplicações que não utilizam a autenticação moderna

Atualmente, tem de utilizar outros métodos para bloquear o acesso às aplicações que não utilize a autenticação moderna. Regras de acesso para as aplicações que não utilizam autenticação moderna não são impostas pelo access condicional. Este é principalmente uma consideração de acesso do Exchange e no SharePoint. A maioria das versões anteriores do aplicações utilizam mais antigos protocolos de controlo de acesso.

### <a name="control-access-in-office-365-sharepoint-online"></a>Controlar o acesso do Office 365 SharePoint Online
Pode desativar legados protocolos para o acesso ao SharePoint utilizando o cmdlet Set-SPOTenant. Utilize este cmdlet para impedir que os clientes do Office que utilizam protocolos que não sejam moderno autenticação de aceder a recursos do SharePoint Online.

**Comando de exemplo**:    `Set-SPOTenant -LegacyAuthProtocolsEnabled $false`

### <a name="control-access-in-office-365-exchange-online"></a>Controlar o acesso no Office 365 Exchange Online

Exchange oferece duas categorias principais de protocolos. Reveja as seguintes opções e, em seguida, selecione a política de que se adequa a sua organização.

-   **O Exchange ActiveSync**. Por predefinição, as políticas de acesso condicional para autenticação multifator e a localização não são impostas para o Exchange ActiveSync. Tem de proteger o acesso a estes serviços através da configuração do Exchange ActiveSync política diretamente ou bloqueando do Exchange ActiveSync utilizando as regras de serviços de Federação do Active Directory (AD FS).
-   **Protocolos legado**. Pode bloquear protocolos legados com o AD FS. Isto bloqueia acesso a clientes do Office mais antigos, como o Office 2013 sem autenticação moderna ativada e versões anteriores do Office.


### <a name="use-ad-fs-to-block-legacy-protocol"></a>Utilizar o AD FS para bloquear o protocolo legado

Pode utilizar as seguintes regras de exemplo para bloquear o acesso de protocolo legado ao nível do AD FS. Selecione duas configurações comuns.

#### <a name="option-1-allow-exchange-activesync-and-allow-legacy-apps-but-only-on-the-intranet"></a>Opção 1: Permitem do Exchange ActiveSync e permitir que as aplicações do legado, mas apenas na intranet

Ao aplicar as seguintes três regras para o AD FS confiar fidedignidade de terceiros para a plataforma de identidade do Microsoft Office 365, o tráfego do Exchange ActiveSync e o browser e o tráfego de autenticação moderna, têm acesso. Aplicações legadas estão bloqueadas relativamente à extranet.

##### <a name="rule-1"></a>Regra de 1

    @RuleName = “Allow all intranet traffic”
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

##### <a name="rule-2"></a>Regra de 2

    @RuleName = “Allow Exchange ActiveSync ”
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

##### <a name="rule-3"></a>Regra de 3

    @RuleName = “Allow extranet browser and browser dialog traffic”
    c1:[Type == " http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

#### <a name="option-2-allow-exchange-activesync-and-block-legacy-apps"></a>Opção 2: Permitem do Exchange ActiveSync e bloquear aplicações legadas

Ao aplicar as seguintes três regras para o AD FS confiar fidedignidade de terceiros para a plataforma de identidade do Microsoft Office 365, o tráfego do Exchange ActiveSync e o browser e o tráfego de autenticação moderna, têm acesso. Aplicações legadas estão bloqueadas relativamente a qualquer localização.

##### <a name="rule-1"></a>Regra de 1

    @RuleName = “Allow all intranet traffic only for browser and modern authentication clients”
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-2"></a>Regra de 2

    @RuleName = “Allow Exchange ActiveSync”
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-3"></a>Regra de 3

    @RuleName = “Allow extranet browser and browser dialog traffic”
    c1:[Type == " http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");
