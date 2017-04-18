<properties
    pageTitle="Azure Active Directory Domain Services: Guia de resolução de problemas | Microsoft Azure"
    description="Guia de resolução de problemas para serviços de domínio do Azure AD"
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
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="maheshu"/>

# <a name="azure-ad-domain-services---troubleshooting-guide"></a>Serviços de domínio do Azure AD - guia de resolução de problemas
Este artigo fornece sugestões de resolução de problemas para problemas que poderá encontrar quando configura ou administração dos serviços de domínio do Azure Active Directory (AD).


## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Não é possível ativar serviços de domínio do Azure AD para o seu diretório do Azure AD
Esta secção ajuda-o a resolver problemas de erros quando tenta ativar serviços de domínio do Azure AD no seu diretório e falha ou obtém alternada novamente para 'Desactivado'.

Selecione os passos de resolução de problemas que correspondem à mensagem de erro se deparar com.

|**Mensagem de erro**|**Resolução**|
|---|:---|
|*O nome contoso100.com já está a ser utilizado desta rede. Especifique um nome que não está em utilização.*|[Conflito de nomes de domínio na rede virtual](active-directory-ds-troubleshooting.md#domain-name-conflict)
|*Serviços de domínio não podem ser ativados no inquilino Azure AD. O serviço não tem as permissões adequadas para a aplicação denominado 'Sincronização de serviços de domínio do Azure AD'. Eliminar a aplicação denominada 'Sincronização de serviços de domínio do Azure AD' e, em seguida, tente ativar serviços de domínio do seu inquilino do Azure AD.*|[Serviços de domínio não tem as permissões adequadas para a aplicação de sincronização de serviços de domínio do Azure AD](active-directory-ds-troubleshooting.md#inadequate-permissions)|
|*Serviços de domínio não podem ser ativados no inquilino Azure AD. A aplicação de serviços de domínio no inquilino do Azure AD não tem as permissões necessárias para activar os serviços de domínio. Eliminar a aplicação com o d87dcbc6-a371-462e-88e3-28ad15ec4e64 identificador de aplicação e, em seguida, tente ativar serviços de domínio do seu inquilino do Azure AD.*|[A aplicação de serviços de domínio não está configurada corretamente no seu inquilino](active-directory-ds-troubleshooting.md#invalid-configuration)|
|*Serviços de domínio não podem ser ativados no inquilino Azure AD. A aplicação do Microsoft Azure AD é desativada no inquilino do Azure AD. Ativar a aplicação com o 00000002-0000-0000-c000-000000000000 identificador de aplicação e, em seguida, tente ativar serviços de domínio do seu inquilino do Azure AD.*|[A aplicação Microsoft Graph está desativada no seu inquilino do Azure AD](active-directory-ds-troubleshooting.md#microsoft-graph-disabled)|


### <a name="domain-name-conflict"></a>Conflito de nomes de domínio
**Mensagem de erro:**

*O nome contoso100.com já está a ser utilizado desta rede. Especifique um nome que não está em utilização.*

**Remediação:**

Certifique-se de que não possui um domínio existente com o mesmo nome de domínio disponível nessa rede virtual. Por exemplo, partem do princípio que tenha um domínio denominado 'contoso.com' já disponíveis na rede virtual selecionada. Mais tarde, experimente ativar um domínio gerido de serviços de domínio do Azure AD com o mesmo nome de domínio (isto é, ' contoso.com') nessa rede virtual. Encontrar uma falha ao tentar ativar serviços de domínio do Azure AD.

Esta falha é devido a nome está em conflito do nome de domínio nessa rede virtual. Esta situação, tem de utilizar um nome diferente para configurar o seu domínio gerido de serviços de domínio do Azure AD. Em alternativa, pode anular aprovisionar o domínio existente e, em seguida, avance para o ativar serviços de domínio do Azure AD.


### <a name="inadequate-permissions"></a>Permissões inadequadas
**Mensagem de erro:**

*Serviços de domínio não podem ser ativados no inquilino Azure AD. O serviço não tem as permissões adequadas para a aplicação denominado 'Sincronização de serviços de domínio do Azure AD'. Eliminar a aplicação denominada 'Sincronização de serviços de domínio do Azure AD' e, em seguida, tente ativar serviços de domínio do seu inquilino do Azure AD.*

**Remediação:**

Verifique se existe uma aplicação com o nome 'Sincronização de serviços de domínio do Azure AD' no seu diretório do Azure AD. Se esta aplicação não existir, eliminá-la e, em seguida, voltar a ativar serviços de domínio do Azure AD.

Execute os seguintes passos para verificar o estado de presença da aplicação e eliminá-lo, se existir a aplicação:

  1. Navegue para o **portal clássica Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
  2. Selecione o nó **Do Active Directory** no painel da esquerda.
  3. Selecione o inquilino do Azure AD (directório) para o qual pretende ativar serviços de domínio do Azure AD.
  4. Navegue para o separador de **aplicações** .
  5. Selecione a opção de **aplicações do proprietário da minha empresa** na lista pendente.
  6. Procurar uma aplicação designado por **sincronização de serviços de domínio do Azure AD**. Se a aplicação não existir, avance para eliminá-la.
  7. Uma vez que tenha eliminado da aplicação, tente ativar serviços de domínio do Azure AD mais uma vez.


### <a name="invalid-configuration"></a>Configuração inválida
**Mensagem de erro:**

*Serviços de domínio não podem ser ativados no inquilino Azure AD. A aplicação de serviços de domínio no inquilino do Azure AD não tem as permissões necessárias para activar os serviços de domínio. Eliminar a aplicação com o d87dcbc6-a371-462e-88e3-28ad15ec4e64 identificador de aplicação e, em seguida, tente ativar serviços de domínio do seu inquilino do Azure AD.*

**Remediação:**

Verifique se tem uma aplicação com o nome AzureActiveDirectoryDomainControllerServices (com um identificador da aplicação do d87dcbc6-a371-462e-88e3-28ad15ec4e64) no seu diretório do Azure AD. Se esta aplicação não existir, tem de eliminá-lo e, em seguida, voltar a ativar serviços de domínio do Azure AD.

Utilize o seguinte script do PowerShell para localizar a aplicação e eliminá-la.

> [AZURE.NOTE] Este script utiliza os cmdlets do **Azure AD PowerShell versão 2** . Para obter uma lista completa de todos os cmdlets disponíveis e para transferir o módulo, leia a [documentação de referência AzureAD PowerShell](https://msdn.microsoft.com/library/azure/mt757189.aspx).

```
$InformationPreference = "Continue"
$WarningPreference = "Continue"

$aadDsSp = Get-AzureADServicePrincipal -Filter "AppId eq 'd87dcbc6-a371-462e-88e3-28ad15ec4e64'" -ErrorAction Ignore
if ($aadDsSp -ne $null)
{
    Write-Information "Found Azure AD Domain Services application. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $aadDsSp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services application."
}

$identifierUri = "https://sync.aaddc.activedirectory.windowsazure.com"
$appFilter = "IdentifierUris eq '" + $identifierUri + "'"
$app = Get-AzureADApplication -Filter $appFilter
if ($app -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync application. Deleting it ..."
    Remove-AzureADApplication -ObjectId $app.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync application."
}

$spFilter = "ServicePrincipalNames eq '" + $identifierUri + "'"
$sp = Get-AzureADServicePrincipal -Filter $spFilter
if ($sp -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync service principal. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $sp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync service principal."
}
```
<br>

### <a name="microsoft-graph-disabled"></a>Microsoft Graph desativado
**Mensagem de erro:**

Serviços de domínio não podem ser ativados no inquilino Azure AD. A aplicação do Microsoft Azure AD é desativada no inquilino do Azure AD. Ativar a aplicação com o 00000002-0000-0000-c000-000000000000 identificador de aplicação e, em seguida, tente ativar serviços de domínio do seu inquilino do Azure AD.

**Remediação:**

Verifique se desativou uma aplicação com o identificador 00000002-0000-0000-c000-000000000000. Esta aplicação é a aplicação do Microsoft Azure AD e fornece acesso à API do gráfico ao seu inquilino do Azure AD. Serviços de domínio do AD do Azure tem de estar ativada para sincronizar seu inquilino do Azure AD para o seu domínio gerido esta aplicação.

Para resolver este erro, ativar esta aplicação e, em seguida, tente ativar serviços de domínio do seu inquilino do Azure AD.


## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>Os utilizadores não conseguem iniciar sessão para o domínio gerido de serviços de domínio do Azure AD
Se não conseguir iniciar sessão para o domínio gerido recentemente criado um ou mais utilizadores no seu inquilino do Azure AD, execute os seguintes passos de resolução de problemas:

- **Iniciar sessão utilizando o formato UPN:** Tente iniciar sessão utilizando o formato UPN (por exemplo, 'joeuser@contoso.com') em vez do formato de SAMAccountName ('CONTOSO\joeuser'). O SAMAccountName pode ser gerado automaticamente para utilizadores cujo prefixo UPN é demasiado longo ou é igual a outro utilizador no domínio gerido. O formato UPN se garante ser exclusivos dentro de um inquilino do Azure AD.

> [AZURE.NOTE] Recomendamos que utilize o formato UPN para iniciar sessão no domínio gerido dos serviços de domínio do Azure AD.

- Certifique-se de que tem [com capacidade de sincronização de palavra-passe](active-directory-ds-getting-started-password-sync.md) em conformidade com os passos descritos no guia de introdução.

- **Contas externas:** Certifique-se de que a conta de utilizador afetado não é uma conta no inquilino Azure AD externa. Exemplos de contas externas incluem contas Microsoft (por exemplo, 'joe@live.com') ou contas de utilizador a partir de um externo diretório do Azure AD. Uma vez que os serviços de domínio do Azure AD não tem as credenciais para essas contas de utilizador, estes utilizadores não iniciar sessão para o domínio gerido.

- **Sincronizado contas:** Se as contas de utilizador afetado são sincronizadas a partir de um diretório no local, verifique se:
    - Ter implementado ou atualizadas para a [versão mais recente recomendada de ligação do Azure AD](active-directory-ds-getting-started-password-sync.md#install-or-update-azure-ad-connect).

    - Ter configurado a ligação do Azure AD para [efetuar uma sincronização completa](active-directory-ds-getting-started-password-sync.md).

    - Dependendo do tamanho do seu diretório, poderá demorar algum tempo para contas de utilizador e hashes esteja disponível nos serviços de domínio do Azure AD de credenciais. Certifique-se de que esperar durante o tempo suficiente antes de repetir autenticação (consoante o tamanho do diretório da sua - algumas horas num dia ou dois para directórios de grandes dimensões).

    - Se o problema persistir depois de verificar os passos anteriores, experimente reiniciar o serviço do Microsoft Azure AD Sync. A partir do seu computador de sincronização, iniciar uma linha de comandos e execute os seguintes comandos:
      1. parar líquido 'Microsoft Azure AD Sync'
      2. Iniciar líquido 'Microsoft Azure AD Sync'

- **Contas apenas na nuvem**: se a conta de utilizador afectado é uma conta de utilizador apenas na nuvem, certifique-se de que o utilizador tiver alterado a respetiva palavra-passe depois de ter ativado dos serviços de domínio do Azure AD. Este passo faz com que a credencial hashes necessários para serviços de domínio do Azure AD ser gerado.


## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Os utilizadores removidos do seu inquilino do Azure AD não são removidos a partir do seu domínio gerido
Azure AD protege-o partir do acidentais de objetos de utilizador. Quando elimina uma conta de utilizador a partir do seu inquilino do Azure AD, objeto do utilizador correspondente é movido para a Reciclagem. Quando esta operação de eliminação é sincronizada para o seu domínio gerido, faz com que a conta de utilizador correspondente ser marcada desativado. Esta funcionalidade ajuda-o a recuperar ou anular a conta de utilizador mais tarde.

Para remover completamente a conta de utilizador a partir do seu domínio gerido, elimine permanentemente o utilizador a partir do seu inquilino do Azure AD. Utilize o cmdlet do PowerShell remover-MsolUser com o '-RemoveFromRecycleBin' opção, conforme descrito neste [artigo do MSDN](https://msdn.microsoft.com/library/azure/dn194132.aspx).


## <a name="contact-us"></a>Contacte-nos
Contacte a equipa de produto do Azure Active Directory Domain Services para [partilhar comentários ou para obter suporte] (ativo-diretório-ds-contacto-us.md).
