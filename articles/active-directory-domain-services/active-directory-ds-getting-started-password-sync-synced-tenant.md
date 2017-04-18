<properties
    pageTitle="Serviços de domínio do Azure AD: Ativar a sincronização de palavra-passe | Microsoft Azure"
    description="Introdução ao Azure Active Directory Domain Services"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/20/2016"
    ms.author="maheshu"/>

# <a name="enable-password-synchronization-to-azure-ad-domain-services"></a>Ativar a sincronização de palavra-passe para serviços de domínio do Azure AD
Em tarefas anteriores, ativada serviços de domínio do Azure AD do seu inquilino do Azure AD. A tarefa seguinte é activar a sincronização de palavras-passe para serviços de domínio do Azure AD. Assim que a sincronização de credenciais está configurada, os utilizadores podem iniciar sessão domínio gerido utilizando as respetivas credenciais da empresa.

Os passos que envolvem são diferentes consoante se a sua organização tem um apenas na nuvem Azure AD de inquilinos ou estiver definido para sincronizar com o seu diretório no local com a ligação do Azure AD.

<br>

> [AZURE.SELECTOR]
- [Apenas na nuvem Azure AD de inquilinos](active-directory-ds-getting-started-password-sync.md)
- [Sincronizou o inquilino do Azure AD](active-directory-ds-getting-started-password-sync-synced-tenant.md)

<br>


## <a name="task-5-enable-password-synchronization-to-aad-domain-services-for-a-synced-azure-ad-tenant"></a>Tarefa 5: Ativar a sincronização de palavra-passe para serviços de domínio AAD para um Azure sincronizado inquilino de AD
A sincronizado Azure inquilino AD está definido para sincronizar com o diretório de no local da sua organização utilizar a ligação do Azure AD. Ligação do Azure AD não sincroniza NTLM e Kerberos de credenciais hashes para Azure AD por predefinição. Para utilizar os serviços de domínio do Azure AD, é necessário configurar a ligação do Azure AD para sincronizar hashes credenciais necessárias para autenticação NTLM e Kerberos. Os passos seguintes ativar a sincronização dos hashes credenciais necessárias ao seu inquilino do Azure AD.


### <a name="install-or-update-azure-ad-connect"></a>Instalar ou atualizar ligação do Azure AD
Instalar a versão mais recente recomendada de ligação do Azure AD num domínio associadas computador. Se tiver uma instância da configuração de ligação do Azure AD existente, terá de atualizar para utilizar a versão mais recente da ligação do Azure AD. Para evitar problemas/erros conhecidos que poderá já foram corrigidos, certifique-se de que utiliza sempre a versão mais recente da ligação do Azure AD.

**[Ligar a transferência do Azure AD](http://www.microsoft.com/download/details.aspx?id=47594)**

Recomendado versão: **1.1.281.0** - publicado no 7 de Setembro de 2016.

  > [AZURE.WARNING] TEM de instalar a versão mais recente recomendada de ligação do Azure AD para ativar as credenciais de palavra-passe legado (necessárias para autenticação NTLM e Kerberos) para sincronizar ao seu inquilino do Azure AD. Esta funcionalidade não está disponível em versões anteriores de ligação do Azure AD ou com a ferramenta de DirSync legada.

Instruções de instalação para ligação do Azure AD estão disponíveis no seguinte artigo - [Introdução ao ligação do Azure AD](../active-directory/active-directory-aadconnect.md)


### <a name="enable-synchronization-of-ntlm-and-kerberos-credential-hashes-to-azure-ad"></a>Ativar a sincronização de NTLM e Kerberos hashes credenciais para Azure AD
Execute o seguinte script do PowerShell no cada floresta AD, para forçar a sincronização completa da palavra-passe e ativar hashes de credenciais dos todos os utilizadores no local a sincronizar com o seu inquilino do Azure AD. Este script permite que os hashes credenciais necessários para a autenticação NTLM/Kerberos a ser sincronizada ao seu inquilino do Azure AD.

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"  
$azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"  
Import-Module adsync  
$c = Get-ADSyncConnector -Name $adConnector  
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1  
$c.GlobalParameters.Remove($p.Name)  
$c.GlobalParameters.Add($p)  
$c = Add-ADSyncConnector -Connector $c  
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false   
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true  
```

Dependendo do tamanho do seu diretório (número de utilizadores, grupos, etc.), a sincronização de hashes credenciais para Azure AD demora tempo. As palavras-passe serão utilizáveis no domínio gerido dos serviços de domínio do Azure AD pouco depois dos hashes credencial foram sincronizadas para Azure AD.


<br>

## <a name="related-content"></a>Conteúdo relacionado

- [Ativar a sincronização de palavra-passe para serviços de domínio AAD para um Azure apenas na nuvem diretório de AD](active-directory-ds-getting-started-password-sync.md)

- [Administrar um domínio gerido de serviços de domínio do Azure AD](active-directory-ds-admin-guide-administer-domain.md)

- [Aderir a uma máquina de virtual do Windows para um domínio gerido de serviços de domínio do Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)

- [Aderir a uma máquina de virtual vermelho chapéu Enterprise Linux para um domínio gerido de serviços de domínio do Azure AD](active-directory-ds-admin-guide-join-rhel-linux-vm.md)
