<properties
    pageTitle="Ligar dispositivos façam parte de um domínio ao Azure AD para Windows 10 experiências | Microsoft Azure"
    description="Explica como os administradores podem configurar a política de grupo para ativar a dispositivos para ser associados ao domínio para a rede da empresa."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""
    tags="azure-classic-portal"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="connect-domain-joined-devices-to-azure-ad-for-windows-10-experiences"></a>Ligar dispositivos façam parte de um domínio ao Azure AD para Windows 10 experiências

Associação do domínio é que as organizações de forma tradicional ligou dispositivos para o trabalho para o último 15 anos e muito mais. Se tem activada utilizadores iniciar sessão para os seus dispositivos utilizando o trabalho do Windows Server Active Directory (Active Directory) ou escola contas e permitido IT para gerir totalmente nestes dispositivos. Organizações normalmente dependem imagens métodos para dispositivos aprovisionar aos utilizadores e utilizam geralmente System Centro de configuração do Gestor (SCCM) ou política de grupo para geri-los.

Associação de domínio no Windows 10 irá fornecer as seguintes vantagens depois de se ligar dispositivos para o Azure Active Directory (Azure AD):

- Serviço Single sign-on (SSO) para recursos do Azure AD a partir de qualquer lugar
- Acesso à empresa da loja Windows utilizando contas escolar ou profissional (sem conta Microsoft necessária)
- Compatível com Enterprise roaming das definições do utilizador em todos os dispositivos utilizando as contas escolar ou profissional (sem conta Microsoft necessária)
- Autenticação forte e conveniente sessão para contas escolar ou profissional com o Microsoft Passport e Olá do Windows
- Capacidade de restringir o acesso apenas a dispositivos que está em conformidade com as definições de política de grupo do dispositivo organizacional

## <a name="prerequisites"></a>Pré-requisitos

Associação de domínio continua a ser úteis. No entanto, para obter os benefícios do Azure AD de SSO, roaming das definições de trabalho ou escola contas e acesso da loja Windows com contas escolar ou profissional, terá o seguinte:

- Subscrição do Azure AD
- Ligação do Azure AD para expandir o diretório no local ao Azure AD
- Política que tenha definido para ligar dispositivos façam parte de um domínio a Azure AD
- Windows 10 compilação (compilação 10551 ou mais recente) para dispositivos

Para permitir que o Microsoft Passport de trabalho e Olá do Windows, também terá o seguinte procedimento:

- Pública chave infraestrutura (PKI) para negadas de certificados de utilizador.
- Gestor de configuração do Centro de sistema versão 1509 para pré-visualização técnica. Para mais informações, consulte [Microsoft sistema Centro de configuração do Gestor de pré-visualização técnica](https://technet.microsoft.com/library/dn965439.aspx#BKMK_TP3Update) e [Sistema Centro de Gestor de configuração do blogue de equipa](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx). Isto é necessário para implementar os certificados de utilizador baseados em chaves Microsoft Passport.

Como alternativa ao requisito de implementação de PKI, pode fazer o seguinte:

- Ter alguns controladores de domínio com o Windows Server 2016 Active Directory Domain Services.

Para ativar o acesso condicional, pode criar definições de política de grupo permitem acesso ao domínio dispositivos com sem implementações adicionais. Para gerir o controlo de acesso baseado em conformidade do dispositivo, terá o seguinte procedimento:

- Gestor de configuração do sistema Centro versão 1509 para pré-visualização técnica para cenários Passport

## <a name="deployment-instructions"></a>Instruções de implementação



### <a name="step-1-deploy-azure-active-directory-connect"></a>Passo 1: Implementar o Azure Active Directory ligar

Ligação do Azure AD permite-lhe aprovisionar computadores no local, como objectos de dispositivo na nuvem. Para implementar a ligação do Azure AD, consulte "Instalar Azure AD Connect" no artigo [integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md#install-azure-ad-connect).

 - Se seguiu uma [instalação personalizada para a ligação do Azure AD](./connect/active-directory-aadconnect-get-started-custom.md) (e não a instalação do Express), em seguida, siga o procedimento **criar uma ligação de serviço ponto no Active Directory no local**, posteriormente neste passo.
 - Se tiver uma configuração federada com Azure AD antes de instalar a ligação do Azure AD (por exemplo, se tiver implementado serviços de Federação do Active Directory (AD FS) antes de), em seguida, siga o procedimento de **regras de reclamação de configurar o AD FS** , mais tarde este passo.

#### <a name="create-a-service-connection-point-in-on-premises-active-directory"></a>Criar um ponto de ligação de serviço no Active Directory no local

Dispositivos façam parte de um domínio irão utilizar o ponto de ligação de serviço para descobrir informação de inquilino do Azure AD no momento da registo automático com o serviço de registo de dispositivo Azure.

No servidor de ligação do Azure AD, execute os seguintes comandos do PowerShell:

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;


Ao executar o cmdlet $aadAdminCred = Get-credencial, utilize o formato *user@example.com* para o nome de utilizador da credencial que for introduzido quando aparece de pop-up Get-credenciais.

Quando executar o cmdlet inicialização ADSyncDomainJoinedComputerSync..., substitua [*nome da conta conexão*] a conta do domínio que é utilizada como a conta de conector do Active Directory.

#### <a name="configure-ad-fs-claim-rules"></a>Configurar regras de afirmação AD FS
Configurar as regras do AD FS afirmação permite instantâneo registo de um computador com o serviço de registo de dispositivo Azure, permitindo aos computadores autenticar utilizando Kerberos/NTLM através do AD FS. Neste passo, computadores receberão para Azure AD de uma forma adiada (sujeito a ligação do Azure AD vezes de sincronização).

>[AZURE.NOTE]
Se não tiver o AD FS como a Federação servidor no local, siga as instruções do seu fornecedor para criar as regras de afirmação.

O servidor do AD FS (ou numa sessão ligado ao servidor do AD FS), execute os seguintes comandos do PowerShell:

      <#----------------------------------------------------------------------
     |   Modify the Azure AD Relying Party to include the claims needed
     |   for DomainJoin++. The rules include:
     |   -ObjectGuid
     |   -AccountType
     |   -ObjectSid
     +---------------------------------------------------------------------#>

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules

    $rule1 = '@RuleName = "Issue object GUID"
          c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&
          c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
          => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), query = ";objectguid;{0}", param = c2.Value);'

    $rule2 = '@RuleName = "Issue account type for domain joined computers"
          c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
          => issue(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "DJ");'

    $rule3 = '@RuleName = "Pass through primary SID"
          c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&
          c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
          => issue(claim = c2);'

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString

>[AZURE.NOTE]
Windows 10 computadores irão autenticar utilizando a autenticação integrada do Windows para um ponto final WS fidedignidade ativo alojado pelo AD FS. Certifique-se de que este ponto final está ativado. Se estiver a utilizar o Proxy de autenticação da Web, também Certifique-se de que este ponto final é publicado através de um proxy. Pode fazê-lo ao selecionar o adfs/serviços/fidedignidade/13/windowstransport. Deverá ser apresentado como estando ativados na consola de gestão do AD FS sob o **serviço de** > **pontos finais**.


### <a name="step-2-configure-automatic-device-registration-via-group-policy-in-active-directory"></a>Passo 2: Configurar o registo de dispositivo automático através da política de grupo no Active Directory

Pode utilizar política de grupo no Active Directory para configurar os dispositivos de domínio para o Windows 10 para registar automaticamente com Azure AD.

> [AZURE.NOTE]
> Para obter mais recentes instruções sobre como configurar o registo de dispositivo automático consulte o artigo, [como configurar o registo automático de domínio do Windows associadas dispositivos com o Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).
>
> Este modelo de política de grupo nome foi mudado no Windows 10. Se estiver a executar a ferramenta de política de grupo a partir de um computador Windows 10, a política aparecerá como: <br>
> **Registar computadores de domínio associado como dispositivos**<br>
> A política é das seguintes localizações:<br>
> ***Registo de componentes/dispositivo do computador configuração/políticas/administrativo modelos/Windows***


## <a name="additional-information"></a>Informações adicionais
* [Windows 10 para a empresa: formas de utilizar dispositivos para o trabalho](active-directory-azureadjoin-windows10-devices-overview.md)
* [Expandir capacidades de nuvem para dispositivos Windows 10 através do Azure Active Directory participar](active-directory-azureadjoin-user-upgrade.md)
* [Saiba mais sobre cenários de utilização para participar do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Ligar dispositivos façam parte de um domínio ao Azure AD para Windows 10 experiências](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar o participar do Azure AD](active-directory-azureadjoin-setup.md)
