<properties
    pageTitle="Sincronização do Azure AD Connect: como gerir a conta de serviço do Azure AD | Microsoft Azure"
    description="Este tópico documentos como restaurar a conta de serviço do Azure AD."
    services="active-directory"
    keywords="AADSTS70002, AADSTS50054, como repor a palavra-passe para a conta de serviço de conexão de sincronização de ligação do Azure AD"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/01/2016"
    ms.author="billmath"/>

# <a name="azure-ad-connect-sync-how-to-manage-the-azure-ad-service-account"></a>Sincronização do Azure AD Connect: como gerir a conta de serviço do Azure AD
A conta de serviço utilizada pelo conector do Azure AD é suposto para ser serviço gratuito. Se precisar da repor as suas credenciais, este tópico é para si. Por exemplo, se um Administrador Global tem por engano repor a palavra-passe da conta de serviço através do PowerShell.

## <a name="reset-the-credentials"></a>Repor as credenciais
Se a conta de serviço foi definida no conector do Azure AD não conseguir contactar Azure AD devido a problemas de autenticação, pode ser repor a palavra-passe.

1. Inicie sessão no servidor de sincronização de ligação do Azure AD e iniciar o PowerShell.
2. Executar `Add-ADSyncAADServiceAccount`.  
![Addadsyncaadserviceaccount de cmdlet do PowerShell](./media/active-directory-aadconnectsync-howto-azureadaccount/addadsyncaadserviceaccount.png)
3. Fornece as credenciais de Administrador Global do Azure AD.

Este cmdlet repõe a palavra-passe da conta de serviço e atualize-o no Azure AD e do motor de sincronização.

## <a name="known-issues-these-steps-can-solve"></a>Problemas conhecidos, que podem resolver estes passos
Esta secção é uma lista dos erros reportados por clientes que foram corrigidos por um repor na conta de serviço do Azure AD de credenciais.

-----------
Evento 6900  
O servidor encontrou um erro inesperado ao processamento uma notificação de alteração de palavra-passe:  
AADSTS70002: Erro ao validar as credenciais. AADSTS50054: Palavra-passe antiga é utilizado para autenticação.

----------
Evento 659  
Erro ao obter a configuração de sincronizar da política de palavra-passe. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException:  
AADSTS70002: Erro ao validar as credenciais. AADSTS50054: Palavra-passe antiga é utilizado para autenticação.

## <a name="next-steps"></a>Próximos passos

**Tópicos de descrição geral**

- [Sincronização do Azure AD Connect: compreender e personalizar a sincronização](active-directory-aadconnectsync-whatis.md)
- [Integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md)
