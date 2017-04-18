<properties
    pageTitle="Funcionalidades de serviço de sincronização do Azure AD Connect e configuração | Microsoft Azure"
    description="Descreve as funcionalidades de lado do serviço do serviço de sincronização de ligação do Azure AD."
    services="active-directory"
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
    ms.date="08/22/2016"
    ms.author="andkjell;markvi"/>

# <a name="azure-ad-connect-sync-service-features"></a>Funcionalidades de serviço de sincronização do Azure AD Connect

A funcionalidade de sincronização de ligação do Azure AD têm dois componentes:

- O componente no local com o nome de **ligação do Azure AD sync**, também denominado **motor de sincronização**.
- O serviço que residem no Azure AD também conhecido como o **serviço de sincronização de ligação do Azure AD**

Este tópico explica como funcionam as seguintes funcionalidades do **serviço de sincronização de ligação do Azure AD** e como pode configurá-los através do Windows PowerShell.

Estas definições são configuradas pelo [Módulo Azure Active Directory para Windows PowerShell](http://aka.ms/aadposh). Transfira e instale-separadamente a partir de ligação do Azure AD. Os cmdlets documentados neste tópico foram introduzidos em [2016 Março solte (compilação 9031.1)](http://social.technet.microsoft.com/wiki/contents/articles/28552.microsoft-azure-active-directory-powershell-module-version-release-history.aspx#Version_9031_1). Se não tiver os cmdlets documentados neste tópico ou não produzem o mesmo resultado, em seguida, certifique-se de que executar a versão mais recente.

Para ver a configuração no seu diretório do Azure AD, executar `Get-MsolDirSyncFeatures`.  
![Get-MsolDirSyncFeatures resultado](./media/active-directory-aadconnectsyncservice-features/getmsoldirsyncfeatures.png)

Muitas destas definições só podem ser alteradas pelos ligação do Azure AD.

As seguintes definições podem ser configuradas pelo `Set-MsolDirSyncFeature`:

DirSyncFeature | Comentário
--- | ---
[DuplicateProxyAddressResiliency<br/>DuplicateUPNResiliency](#duplicate-attribute-resiliency) | Permite que um atributo ser colocada em quarentena quando é um duplicado de outro objeto em vez de a falhar o objecto inteiro durante a exportação.
[EnableSoftMatchOnUpn](#userprincipalname-soft-match) | Permite que os objectos na associação userPrincipalName para além de endereço SMTP principal.
[SynchronizeUpnForManagedUsers](#synchronize-userprincipalname-updates) | Permite ao motor de sincronização atualizar o atributo userPrincipalName para gerido/licenciado utilizadores (não Federado).

Depois de ter ativado uma funcionalidade, não pode ser desativada novamente.

>[AZURE.NOTE] Do 24 de Agosto de 2016 a funcionalidade *RDP de atributo duplicado* está ativada por predefinição para novo Azure directórios AD. Esta funcionalidade também será lançada e ativada no directórios criados antes dessa data. Receberá uma notificação de correio eletrónico quando o seu diretório está prestes a obter esta funcionalidade activada.

As seguintes definições são configuradas pelo ligação do Azure AD e não pode ser modificadas por `Set-MsolDirSyncFeature`:

DirSyncFeature | Comentário
--- | ---
DeviceWriteback | [Ligação do Azure AD: Ativar dispositivo escrita não consolidada](active-directory-aadconnect-feature-device-writeback.md)
DirectoryExtensions | [Sincronização do Azure AD Connect: extensões de diretório](active-directory-aadconnectsync-feature-directory-extensions.md)
PasswordSync | [Implementar a sincronização de palavras-passe com a sincronização de ligação do Azure AD](active-directory-aadconnectsync-implement-password-synchronization.md)
UnifiedGroupWriteback | [Pré-visualização: Repetição de escrita de grupo](active-directory-aadconnect-feature-preview.md#group-writeback)
UserWriteback | Actualmente não suportadas.

## <a name="duplicate-attribute-resiliency"></a>Duplicar RDP de atributo
Em vez de com falhas a aprovisionar objectos com os duplicados UPNs / proxyAddresses, o atributo duplicado é "colocada em quarentena" e um valor temporário é atribuído. Quando o conflito for resolvido, o UPN temporário é automaticamente alterado para o valor inicial maiúscula. Este comportamento pode ser ativado para UPN e proxyAddress separadamente. Para obter mais detalhes, consulte [a sincronização de identidade e RDP atributo duplicado](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md).

## <a name="userprincipalname-soft-match"></a>Correspondência de contornos UserPrincipalName
Quando esta funcionalidade está ativada, contornos correspondência está ativada para UPN para além do [endereço SMTP principal](https://support.microsoft.com/kb/2641663), que está sempre activada. Correspondência de contornos é utilizada para que correspondam aos utilizadores na nuvem existentes no Azure AD com utilizadores no local.

Se precisar de correspondência no local Contas de AD com contas existentes criadas na nuvem e não está a utilizar Exchange Online, em seguida, esta funcionalidade é útil. Neste cenário, geralmente não tem uma razão para definir o atributo SMTP na nuvem.

Esta funcionalidade é por predefinição para recentemente criada directórios Azure AD. Pode ver se esta funcionalidade está ativada para si através da execução:  
```
Get-MsolDirSyncFeatures -Feature EnableSoftMatchOnUpn
```

Se esta funcionalidade não estiver ativada para o seu diretório do Azure AD, pode ativá-lo ao executar:  
```
Set-MsolDirSyncFeature -Feature EnableSoftMatchOnUpn -Enable $true
```

## <a name="synchronize-userprincipalname-updates"></a>Sincronizar userPrincipalName actualizações
Historicamente, atualizações para o atributo UserPrincipalName utilizando o serviço de sincronização no local tiver sido bloqueado, a menos que ambas as condições são verdadeiras:

- O utilizador é gerido (não Federado).
- O utilizador não foi atribuído uma licença.

Para obter mais detalhes, consulte o artigo [nomes de utilizador no Office 365, Azure ou Intune não corresponderem o UPN no local ou o ID de início de sessão alternativo](https://support.microsoft.com/kb/2523192).

Ativar esta funcionalidade permite que o motor de sincronização atualizar o userPrincipalName quando é alterado no local e utilizar a sincronização de palavra-passe. Se utilizar Federação, esta funcionalidade não é suportada.

Esta funcionalidade é por predefinição para recentemente criada directórios Azure AD. Pode ver se esta funcionalidade está ativada para si através da execução:  
```
Get-MsolDirSyncFeatures -Feature SynchronizeUpnForManagedUsers
```

Se esta funcionalidade não estiver ativada para o seu diretório do Azure AD, pode ativá-lo ao executar:  
```
Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $true
```

Depois de activar esta funcionalidade, irão permanecer valores userPrincipalName existentes como-é. Na próxima alteração o userPrincipalName atributo no local, a sincronização de normal delta utilizadores irão atualizar o UPN.  

## <a name="see-also"></a>Consulte também

- [Sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md)
- [Integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md).
