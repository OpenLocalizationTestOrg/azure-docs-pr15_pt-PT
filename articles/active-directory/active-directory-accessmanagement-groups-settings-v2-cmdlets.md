<properties
    pageTitle="Azure Active Directory pré-visualizar os cmdlets do PowerShell para a gestão de grupo no Azure AD | Microsoft Azure"
    description="Esta página fornece exemplos do PowerShell para ajudar a gerir os seus grupos no Azure Active Directory"
    keywords="Azure AD, Azure Active Directory, PowerShell, gestão de grupos, grupos"
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/29/2016"
    ms.author="curtand"/>

# <a name="azure-active-directory-preview-cmdlets-for-group-management"></a>Cmdlets de pré-visualização do Active Directory Azure para gestão de grupos

> [AZURE.SELECTOR]
- [Portal do Azure](active-directory-groups-create-azure-portal.md)
- [Azure portal clássico](active-directory-accessmanagement-manage-groups.md)
- [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)

O documento seguinte irá fornecer-lhe com exemplos de como utilizar o PowerShell para gerir os seus grupos no Azure Active Directory (Azure AD).  Também fornece informações sobre como configurar o módulo Azure AD PowerShell pré-visualização. Em primeiro lugar, tem de [Transferir o módulo Azure AD PowerShell](http://go.microsoft.com/fwlink/p/?LinkId=828627).

## <a name="installing-the-azure-ad-powershell-module"></a>Instalar o módulo Azure AD PowerShell

Para instalar o módulo de pré-visualização do AzureAD PowerShell, utilize os comandos seguintes:

    PS C:\Windows\system32> install-module azureadpreview

Para verificar que foi instalado o módulo de pré-visualização, utilize o seguinte comando:

    PS C:\Windows\system32> get-module azureadpreview

    ModuleType Version    Name                                ExportedCommands
    ---------- -------    ----                                ----------------
    Binary     1.1.146.0  azureadpreview                      {Add-AzureADAdministrati...}

Agora pode começar a utilizar os cmdlets no módulo. Para obter uma descrição completa dos cmdlets no módulo pré-visualização de AzureAD, consulte a [documentação de referência online](https://msdn.microsoft.com/library/azure/mt757216.aspx).

## <a name="connecting-to-the-directory"></a>Ligar ao diretório
Antes de poder iniciar Gerir grupos de utilizar os cmdlets do PowerShell do Azure AD pré-visualização, tem de ligar a sessão do PowerShell para o directório que pretende gerir. Para executar esta tarefa, utilize o seguinte comando:

    PS C:\Windows\system32> Connect-AzureAD -Force

O cmdlet irá pedir-lhe para as credenciais que pretende utilizar para aceder ao seu diretório. Neste exemplo, estamos a utilizar karen@drumkit.onmicrosoft.com para aceder ao directório de demonstração sobre. O cmdlet irá devolver uma confirmação para mostrar que a sessão foi com êxito ligada ao seu diretório:

    Account                       Environment Tenant
    -------                       ----------- ------
    Karen@drumkit.onmicrosoft.com AzureCloud  85b5ff1e-0402-400c-9e3c-0f…

Agora pode começar a utilizar os cmdlets da pré-visualização AzureAD para gerir grupos no seu diretório.

## <a name="retrieving-groups"></a>Obtenção de grupos
Para obter grupos existentes a partir do diretório da sua pode utilizar o cmdlet Get-AzureADGroups. Para obter todos os grupos no diretório, utilize o cmdlet sem parâmetros:

    PS C:\Windows\system32> get-azureadgroup

O cmdlet irá devolver todos os grupos no diretório ligado.

Pode utilizar o parâmetro - ID de objecto para obter um grupo específico para a qual que especificar o ID de objecto do grupo:

    PS C:\Windows\system32> get-azureadgroup -ObjectId e29bae11-4ac0-450c-bc37-6dae8f3da61b

O cmdlet agora irá devolver o grupo cujo ID de objecto corresponde ao valor do parâmetro que introduziu:

    DeletionTimeStamp            :
    ObjectId                     : e29bae11-4ac0-450c-bc37-6dae8f3da61b
    ObjectType                   : Group
    Description                  :
    DirSyncEnabled               :
    DisplayName                  : Pacific NW Support
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 9bb4139b-60a1-434a-8c0d-7c1f8eee2df9
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

Pode procurar um grupo específico a utilizar o filtro parâmetro-. Este parâmetro leva-o até uma cláusula de filtro ODATA e devolve todos os grupos que correspondam ao filtro, tal como no exemplo seguinte:

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

Tenha em atenção que os cmdlets do AzureAD PowerShell implementam o padrão de consulta de OData, mais informações podem ser encontradas [aqui](https://msdn.microsoft.com/library/gg309461.aspx#BKMK_filter).

## <a name="creating-groups"></a>A criação de grupos
Para criar um novo grupo no seu diretório, utilize o cmdlet AzureADGroup novo. Este cmdlet cria um novo grupo de segurança designado "Marketing":

    PS C:\Windows\system32> New-AzureADGroup -Description "Marketing" -DisplayName "Marketing" -MailEnabled $false -SecurityEnabled $true -MailNickName "Marketing"

## <a name="updating-groups"></a>Atualizar grupos
Para atualizar um grupo existente, utilize o cmdlet Set-AzureADGroup. Neste exemplo, vamos alterar a propriedade DisplayName do grupo "Administradores Intune." Primeiro, vamos encontrou grupo utilizando o cmdlet Get-AzureADGroup e filtrar utilizando o atributo DisplayName:

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

Em seguida, vamos alterar a propriedade descrição para o novo valor "Intune dispositivo administradores":

    PS C:\Windows\system32> Set-AzureADGroup -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -Description "Intune Device Administrators"

Agora já se estamos a encontrar o grupo, podemos ver a propriedade descrição é atualizada para refletir o novo valor:

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Device Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

## <a name="deleting-groups"></a>Eliminar grupos
Para eliminar grupos a partir do seu diretório, utilize o cmdlet AzureADGroup remover da seguinte forma:

    PS C:\Windows\system32> Remove-AzureADGroup -ObjectId b11ca53e-07cc-455d-9a89-1fe3ab24566b

## <a name="managing-members-of-groups"></a>Gerir membros de grupos
Se precisar de adicionar novos membros a um grupo, utilize o cmdlet AzureADGroupMember adicionar. Este comando adiciona um membro ao grupo de administradores de Intune que utilizámos no exemplo anterior:

    PS C:\Windows\system32> Add-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

O parâmetro - ID de objecto é o ID de objecto do grupo ao qual queremos adicionar um membro e - RefObjectId é o ID de objecto do utilizador que pretende adicionar como um membro ao grupo.

Para obter os membros de um grupo existentes, utilize o cmdlet Get-AzureADGroupMember, tal como no exemplo:

    PS C:\Windows\system32> Get-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                        72cd4bbd-2594-40a2-935c-016f3cfeeeea User
                        8120cc36-64b4-4080-a9e8-23aa98e8b34f User

Para remover o membro que adicionámos anteriormente ao grupo, utilize o cmdlet remover AzureADGroupMember, tal como é mostrado aqui:

    PS C:\Windows\system32> Remove-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -MemberId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

Para verificar o membership(s) de grupo de um utilizador, utilize o cmdlet selecione AzureADGroupIdsUserIsMemberOf. Este cmdlet leva como respetivos parâmetros, o ID de objecto do utilizador para o qual pretende verificar os membros do grupo e uma lista dos grupos para o qual pretende verificar os membros. A lista de grupos tem de ser fornecidos em forma de uma variável de complexo do tipo "Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck", por isso, podemos primeiro tem de criar uma variável com esse tipo de:

    PS C:\Windows\system32> $g = new-object Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck

Em seguida, fornecemos valores para groupIds dar entrada do atributo "GroupIds" desta variável complexo:

    PS C:\Windows\system32> $g.GroupIds = "b11ca53e-07cc-455d-9a89-1fe3ab24566b", "31f1ff6c-d48c-4f8a-b2e1-abca7fd399df"

Agora, se queremos verificar se os membros do grupo de um utilizador com o ID de objecto 72cd4bbd-2594-40a2-935c-016f3cfeeeea contra os grupos na $g, deverá utilizamos:

    PS C:\Windows\system32> Select-AzureADGroupIdsUserIsMemberOf -ObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea -GroupIdsForMembershipCheck $g

    OdataMetadata                                                                                               Value
    -------------                                                                                               -----
    https://graph.windows.net/85b5ff1e-0402-400c-9e3c-0f9e965325d1/$metadata#Collection(Edm.String)             {31f1ff6c-d48c-4f8a-b2e1-abca7fd399df}


Valor devolvido é uma lista dos grupos de que este utilizador é membro. Também pode aplicar este método para verificar a associação de contactos, grupos ou principais de serviço para obter uma lista dos grupos, com selecione AzureADGroupIdsContactIsMemberOf, selecione AzureADGroupIdsGroupIsMemberOf ou selecione AzureADGroupIdsServicePrincipalIsMemberOf determinada

## <a name="managing-owners-of-groups"></a>Gerir proprietários de grupos
Para adicionar os proprietários a um grupo, utilize o cmdlet AzureADGroupOwner adicionar:

    PS C:\Windows\system32> Add-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

O parâmetro - ID de objecto é o ID de objecto do grupo ao qual queremos adicionar um proprietário e - RefObjectId é o ID de objecto do utilizador que pretende adicionar como um proprietário do grupo.

Para obter os proprietários de um grupo, utilize o AzureADGroupOwner obter:

    PS C:\Windows\system32> Get-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

O cmdlet irá devolver a lista dos proprietários do grupo especificado:

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                        e831b3fd-77c9-49c7-9fca-de43e109ef67 User

Se pretender remover um proprietário de um grupo, utilize AzureADGroupOwner remover:

    PS C:\Windows\system32> remove-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -OwnerId e831b3fd-77c9-49c7-9fca-de43e109ef67

## <a name="next-steps"></a>Próximos passos

Pode encontrar mais documentação do Azure Active Directory PowerShell no [Azure Active Directory Cmdlets](http://go.microsoft.com/fwlink/p/?LinkId=808260).

* [Gerir o acesso aos recursos com os grupos do Azure Active Directory](active-directory-manage-groups.md)

* [Integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md)
