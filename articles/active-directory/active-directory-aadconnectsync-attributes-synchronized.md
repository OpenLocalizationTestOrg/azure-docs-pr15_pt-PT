<properties
    pageTitle="Sincronização do Azure AD Connect: atributos sincronizados com o Azure Active Directory | Microsoft Azure"
    description="Lista os atributos que são sincronizados para o Azure Active Directory."
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
    ms.date="09/13/2016"
    ms.author="markvi;andkjell"/>


# <a name="azure-ad-connect-sync-attributes-synchronized-to-azure-active-directory"></a>Sincronização do Azure AD Connect: atributos sincronizados com o Azure Active Directory
Este tópico indica os atributos que são sincronizados através da ligação do Azure AD sync.  
Os atributos são agrupados pelo relacionados Azure AD aplicação.

## <a name="attributes-to-synchronize"></a>Atributos para sincronizar
Uma pergunta comuns é *o que é a lista de atributos mínimos para sincronizar*. A predefinição e abordagem recomendada é para manter os atributos padrão para que uma inteira GAL (lista de endereços Global) pode ser criada na nuvem e obter todas as funcionalidades no Office 365 cargas de trabalho. Em alguns casos, existem algumas atributos que a sua organização não pretende sincronizada para a nuvem uma vez que estes atributos contêm confidencial ou dados de (informação identificativa) PII, como neste exemplo:  
![atributos incorretas](./media/active-directory-aadconnectsync-attributes-synchronized/badextensionattribute.png)

Neste caso, comece com a lista de atributos neste tópico e identificar os atributos que iria conter sensível ou dados PII e não podem ser sincronizados. Em seguida, desmarque a opção esses atributos durante a instalação utilizando a [aplicação do Azure AD e filtragem de atributo](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering).

>[AZURE.WARNING] Quando desseleccionar atributos, deve ter cuidado e apenas desmarcar esses atributos absoluta não é possíveis sincronizar. Desmarcar outros atributos, poderá ter um impacto negativo sobre as funcionalidades.

## <a name="office-365-proplus"></a>Office 365 ProPlus

| Nome do atributo| Utilizador| Comentário |
| --- | :-: | --- |
| accountEnabled| X| Define se uma conta está ativada.|
| NC| X|  |
| DisplayName em grupos| X|  |
| objectSID| X| propriedade mecânica. Identificador de utilizador do AD utilizado para manter a sincronização entre o Azure AD e AD.|
| pwdLastSet| X| propriedade mecânica. Utilizado para saber quando invalidar tokens já emitidos. Utilizado pelo sincronização de palavra-passe e Federação.|
| sourceAnchor| X| propriedade mecânica. Identificador imutáveis para manter a relação entre adiciona e Azure AD.|
| usageLocation| X| propriedade mecânica. O país do utilizador. Utilizado para a atribuição de licenças.|
| userPrincipalName| X| UPN é o ID de início de sessão do utilizador. Com mais frequência é o valor igual ao [correio].|

## <a name="exchange-online"></a>Exchange Online

| Nome do atributo| Utilizador| Contacto| Grupo| Comentário |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Define se uma conta está ativada.|
| Assistente de início| X| X|  |  |
| authOrig| X| X| X|  |
| c| X| X|  |  |
| NC| X|  | X|  |
| Co| X| X|  |  |
| empresa| X| X|  |  |
| Indicativo| X| X|  |  |
| departamento| X| X|  |  |
| Descrição| X| X| X|  |
| DisplayName em grupos| X| X| X|  |
| dLMemRejectPerms| X| X| X|  |
| dLMemSubmitPerms| X| X| X|  |
| extensionAttribute1| X| X| X|  |
| extensionAttribute10| X| X| X|  |
| extensionAttribute11| X| X| X|  |
| extensionAttribute12| X| X| X|  |
| extensionAttribute13| X| X| X|  |
| extensionAttribute14| X| X| X|  |
| extensionAttribute15| X| X| X|  |
| extensionAttribute2| X| X| X|  |
| extensionAttribute3| X| X| X|  |
| extensionAttribute4| X| X| X|  |
| extensionAttribute5| X| X| X|  |
| extensionAttribute6| X| X| X|  |
| extensionAttribute7| X| X| X|  |
| extensionAttribute8| X| X| X|  |
| extensionAttribute9| X| X| X|  |
| facsimiletelephonenumber| X| X|  |  |
| givenName| X| X|  |  |
| Telefone| X| X|  |  |
| informações| X| X| X| Este atributo está atualmente não consumido para grupos.|
| Iniciais| X| X|  |  |
| l| X| X|  |  |
| legacyExchangeDN| X| X| X|  |
| mailNickname| X| X| X|  |
| mangedBy|  |  | X|  |
| Gestor de| X| X|  |  |
| Membro|  |  | X|  |
| dispositivos móveis| X| X|  |  |
| msDS HABSeniorityIndex| X| X| X|  |
| msDS PhoneticDisplayName| X| X| X|  |
| msExchArchiveGUID| X|  |  |  |
| msExchArchiveName| X|  |  |  |
| msExchAssistantName| X| X|  |  |
| msExchAuditAdmin| X|  |  |  |
| msExchAuditDelegate| X|  |  |  |
| msExchAuditDelegateAdmin| X|  |  |  |
| msExchAuditOwner| X|  |  |  |
| msExchBlockedSendersHash| X| X|  |  |
| msExchBypassAudit| X|  |  |  |
| msExchCoManagedByLink|  |  | X|  |
| msExchDelegateListLink| X|  |  |  |
| msExchELCExpirySuspensionEnd| X|  |  |  |
| msExchELCExpirySuspensionStart| X|  |  |  |
| msExchELCMailboxFlags| X|  |  |  |
| msExchEnableModeration| X|  | X|  |
| msExchExtensionCustomAttribute1| X| X| X| Este atributo está atualmente não consumido pelo Exchange Online. |
| msExchExtensionCustomAttribute2| X| X| X| Este atributo está atualmente não consumido pelo Exchange Online. |
| msExchExtensionCustomAttribute3| X| X| X| Este atributo está atualmente não consumido pelo Exchange Online. |
| msExchExtensionCustomAttribute4| X| X| X| Este atributo está atualmente não consumido pelo Exchange Online. |
| msExchExtensionCustomAttribute5| X| X| X| Este atributo está atualmente não consumido pelo Exchange Online. |
| msExchHideFromAddressLists| X| X| X|  |
| msExchImmutableID| X|  |  |  |
| msExchLitigationHoldDate| X| X| X|  |
| msExchLitigationHoldOwner| X| X| X|  |
| msExchMailboxAuditEnable| X|  |  |  |
| msExchMailboxAuditLogAgeLimit| X|  |  |  |
| msExchMailboxGuid| X|  |  |  |
| msExchModeratedByLink| X| X| X|  |
| msExchModerationFlags| X| X| X|  |
| msExchRecipientDisplayType| X| X| X|  |
| msExchRecipientTypeDetails| X| X| X|  |
| msExchRemoteRecipientType| X|  |  |  |
| msExchRequireAuthToSendTo| X| X| X|  |
| msExchResourceCapacity| X|  |  |  |
| msExchResourceDisplay| X|  |  |  |
| msExchResourceMetaData| X|  |  |  |
| msExchResourceSearchProperties| X|  |  |  |
| msExchRetentionComment| X| X| X|  |
| msExchRetentionURL| X| X| X|  |
| msExchSafeRecipientsHash| X| X|  |  |
| msExchSafeSendersHash| X| X|  |  |
| msExchSenderHintTranslations| X| X| X|  |
| msExchTeamMailboxExpiration| X|  |  |  |
| msExchTeamMailboxOwners| X|  |  |  |
| msExchTeamMailboxSharePointUrl| X|  |  |  |
| msExchUserHoldPolicies| X|  |  |  |
| msOrg IsOrganizational|  |  | X|  |
| objectSID| X|  | X| propriedade mecânica. Identificador de utilizador do AD utilizado para manter a sincronização entre o Azure AD e AD.|
| oOFReplyToOriginator|  |  | X|  |
| otherFacsimileTelephone| X| X|  |  |
| otherHomePhone| X| X|  |  |
| otherTelephone| X| X|  |  |
| pager| X| X|  |  |
| physicalDeliveryOfficeName| X| X|  |  |
| código postal| X| X|  |  |
| proxyAddresses| X| X| X|  |
| publicDelegates| X| X| X|  |
| pwdLastSet| X|  |  | propriedade mecânica. Utilizado para saber quando invalidar tokens já emitidos. Utilizado pelo sincronização de palavra-passe e Federação.|
| reportToOriginator|  |  | X|  |
| reportToOwner|  |  | X|  |
| securityEnabled|  |  | X| Derivado de groupType|
| sn| X| X|  |  |
| sourceAnchor| X| X| X| propriedade mecânica. Identificador imutáveis para manter a relação entre adiciona e Azure AD.|
| St| X| X|  |  |
| streetAddress| X| X|  |  |
| targetAddress| X| X|  |  |
| telephoneAssistant| X| X|  |  |
| telephoneNumber| X| X|  |  |
| thumbnailphoto| X| X|  |  |
| título| X| X|  |  |
| unauthOrig| X| X| X|  |
| usageLocation| X|  |  | propriedade mecânica. O país do utilizador. Utilizado para a atribuição de licenças.|
| userCertificate| X| X|  |  |
| userPrincipalName| X|  |  | UPN é o ID de início de sessão do utilizador. Com mais frequência é o valor igual ao [correio].|
| userSMIMECertificates| X| X|  |  |
| wWWHomePage| X| X|  |  |

## <a name="sharepoint-online"></a>SharePoint Online

| Nome do atributo| Utilizador| Contacto| Grupo| Comentário |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Define se uma conta está ativada.|
| authOrig| X| X| X|  |
| c| X| X|  |  |
| NC| X|  | X|  |
| Co| X| X|  |  |
| empresa| X| X|  |  |
| Indicativo| X| X|  |  |
| departamento| X| X|  |  |
| Descrição| X| X| X|  |
| DisplayName em grupos| X| X| X|  |
| dLMemRejectPerms| X| X| X|  |
| dLMemSubmitPerms| X| X| X|  |
| extensionAttribute1| X| X| X|  |
| extensionAttribute10| X| X| X|  |
| extensionAttribute11| X| X| X|  |
| extensionAttribute12| X| X| X|  |
| extensionAttribute13| X| X| X|  |
| extensionAttribute14| X| X| X|  |
| extensionAttribute15| X| X| X|  |
| extensionAttribute2| X| X| X|  |
| extensionAttribute3| X| X| X|  |
| extensionAttribute4| X| X| X|  |
| extensionAttribute5| X| X| X|  |
| extensionAttribute6| X| X| X|  |
| extensionAttribute7| X| X| X|  |
| extensionAttribute8| X| X| X|  |
| extensionAttribute9| X| X| X|  |
| facsimiletelephonenumber| X| X|  |  |
| givenName| X| X|  |  |
| hideDLMembership|  |  | X|  |
| telefone| X| X|  |  |
| informações| X| X| X|  |
| iniciais| X| X|  |  |
| ipPhone| X| X|  |  |
| l| X| X|  |  |
| correio| X| X| X|  |
| mailNickname| X| X| X|  |
| managedBy|  |  | X|  |
| Gestor de| X| X|  |  |
| Membro|  |  | X|  |
| middleName| X| X|  |  |
| dispositivos móveis| X| X|  |  |
| msExchTeamMailboxExpiration| X|  |  |  |
| msExchTeamMailboxOwners| X|  |  |  |
| msExchTeamMailboxSharePointLinkedBy| X|  |  |  |
| msExchTeamMailboxSharePointUrl| X|  |  |  |
| objectSID| X|  | X| propriedade mecânica. Identificador de utilizador do AD utilizado para manter a sincronização entre o Azure AD e AD.|
| oOFReplyToOriginator|  |  | X|  |
| otherFacsimileTelephone| X| X|  |  |
| otherHomePhone| X| X|  |  |
| otherIpPhone| X| X|  |  |
| otherMobile| X| X|  |  |
| otherPager| X| X|  |  |
| otherTelephone| X| X|  |  |
| pager| X| X|  |  |
| physicalDeliveryOfficeName| X| X|  |  |
| código postal| X| X|  |  |
| postOfficeBox| X| X|  |  |
| preferredLanguage| X|  |  |  |
| proxyAddresses| X| X| X|  |
| pwdLastSet| X|  |  | propriedade mecânica. Utilizado para saber quando invalidar tokens já emitidos. Utilizado pelo sincronização de palavra-passe e Federação.|
| reportToOriginator|  |  | X|  |
| reportToOwner|  |  | X|  |
| securityEnabled|  |  | X| Derivado de groupType|
| sn| X| X|  |  |
| sourceAnchor| X| X| X| propriedade mecânica. Identificador imutáveis para manter a relação entre adiciona e Azure AD.|
| St| X| X|  |  |
| streetAddress| X| X|  |  |
| targetAddress| X| X|  |  |
| telephoneAssistant| X| X|  |  |
| telephoneNumber| X| X|  |  |
| thumbnailphoto| X| X|  |  |
| título| X| X|  |  |
| unauthOrig| X| X| X|  |
| URL| X| X|  |  |
| usageLocation| X|  |  | propriedade mecânica. O país do utilizador. Utilizado para a atribuição de licenças.|
| userPrincipalName| X|  |  | UPN é o ID de início de sessão do utilizador. Com mais frequência é o valor igual ao [correio].|
| wWWHomePage| X| X|  |  |

## <a name="lync-online"></a>Lync Online

| Nome do atributo| Utilizador| Contacto| Grupo| Comentário |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Define se uma conta está ativada.|
| c| X| X|  |  |
| NC| X|  | X|  |
| Co| X| X|  |  |
| empresa| X| X|  |  |
| departamento| X| X|  |  |
| Descrição| X| X| X|  |
| DisplayName em grupos| X| X| X|  |
| facsimiletelephonenumber| X| X| X|  |
| givenName| X| X|  |  |
| telefone| X| X|  |  |
| ipPhone| X| X|  |  |
| l| X| X|  |  |
| correio| X| X| X|  |
| mailNickname| X| X| X|  |
| managedBy|  |  | X|  |
| Gestor de| X| X|  |  |
| Membro|  |  | X|  |
| dispositivos móveis| X| X|  |  |
| msExchHideFromAddressLists| X| X| X|  |
| atributo msRTCSIP-ApplicationOptions| X|  |  |  |
| atributo msRTCSIP-DeploymentLocator| X| X|  |  |
| atributo msRTCSIP-linha| X| X|  |  |
| atributo msRTCSIP-OptionFlags| X| X|  |  |
| atributo msRTCSIP-OwnerUrn| X|  |  |  |
| atributo msRTCSIP-PrimaryUserAddress| X| X|  |  |
| atributo msRTCSIP-UserEnabled| X| X|  |  |
| objectSID| X|  | X| propriedade mecânica. Identificador de utilizador do AD utilizado para manter a sincronização entre o Azure AD e AD.|
| otherTelephone| X| X|  |  |
| physicalDeliveryOfficeName| X| X|  |  |
| código postal| X| X|  |  |
| preferredLanguage| X|  |  |  |
| proxyAddresses| X| X| X|  |
| pwdLastSet| X|  |  | propriedade mecânica. Utilizado para saber quando invalidar tokens já emitidos. Utilizado pelo sincronização de palavra-passe e Federação.|
| securityEnabled|  |  | X| Derivado de groupType|
| sn| X| X|  |  |
| sourceAnchor| X| X| X| propriedade mecânica. Identificador imutáveis para manter a relação entre adiciona e Azure AD.|
| St| X| X|  |  |
| streetAddress| X| X|  |  |
| telephoneNumber| X| X|  |  |
| thumbnailphoto| X| X|  |  |
| título| X| X|  |  |
| usageLocation| X|  |  | propriedade mecânica. O país do utilizador. Utilizado para a atribuição de licenças.|
| userPrincipalName| X|  |  | UPN é o ID de início de sessão do utilizador. Com mais frequência é o valor igual ao [correio].|
| wWWHomePage| X| X|  |  |

## <a name="azure-rms"></a>Azure RMS

| Nome do atributo| Utilizador| Contacto| Grupo| Comentário |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Define se uma conta está ativada.|
| NC| X|  | X| Nome ou alias comuns. Com mais frequência o prefixo do valor [correio].|
| DisplayName em grupos| X| X| X| Uma cadeia que representa o nome frequentemente apresentado como o nome amigável (nome apelido).|
| correio| X| X| X| endereço de e-mail completo.|
| Membro|  |  | X|  |
| objectSID| X|  | X| propriedade mecânica. Identificador de utilizador do AD utilizado para manter a sincronização entre o Azure AD e AD.|
| proxyAddresses| X| X| X| propriedade mecânica. Utilizado pelo Azure AD. Contém todos os endereços de e-mail secundários para o utilizador.|
| pwdLastSet| X|  |  | propriedade mecânica. Utilizado para saber quando invalidar tokens já emitidos.|
| securityEnabled|  |  | X| Derivado de groupType.|
| sourceAnchor| X| X| X| propriedade mecânica. Identificador imutáveis para manter a relação entre adiciona e Azure AD.|
| usageLocation| X|  |  | propriedade mecânica. O país do utilizador. Utilizado para a atribuição de licenças.|
| userPrincipalName| X|  |  | Este UPN é o ID de início de sessão do utilizador. Com mais frequência é o valor igual ao [correio].|

## <a name="intune"></a>Intune

| Nome do atributo| Utilizador| Contacto| Grupo| Comentário |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Define se uma conta está ativada.|
| c| X| X|  |  |
| NC| X|  | X|  |
| Descrição| X| X| X|  |
| DisplayName em grupos| X| X| X|  |
| correio| X| X| X|  |
| mailNickname| X| X| X|  |
| Membro|  |  | X|  |
| objectSID| X|  | X| propriedade mecânica. Identificador de utilizador do AD utilizado para manter a sincronização entre o Azure AD e AD.|
| proxyAddresses| X| X| X|  |
| pwdLastSet| X|  |  | propriedade mecânica. Utilizado para saber quando invalidar tokens já emitidos. Utilizado pelo sincronização de palavra-passe e Federação.|
| securityEnabled|  |  | X| Derivado de groupType|
| sourceAnchor| X| X| X| propriedade mecânica. Identificador imutáveis para manter a relação entre adiciona e Azure AD.|
| usageLocation| X|  |  | propriedade mecânica. O país do utilizador. Utilizado para a atribuição de licenças.|
| userPrincipalName| X|  |  | UPN é o ID de início de sessão do utilizador. Com mais frequência é o valor igual ao [correio].|

## <a name="dynamics-crm"></a>Dynamics CRM

| Nome do atributo| Utilizador| Contacto| Grupo| Comentário |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Define se uma conta está ativada.|
| c| X| X|  |  |
| NC| X|  | X|  |
| Co| X| X|  |  |
| empresa| X| X|  |  |
| Indicativo| X| X|  |  |
| Descrição| X| X| X|  |
| DisplayName em grupos| X| X| X|  |
| facsimiletelephonenumber| X| X|  |  |
| givenName| X| X|  |  |
| l| X| X|  |  |
| managedBy|  |  | X|  |
| Gestor de| X| X|  |  |
| Membro|  |  | X|  |
| dispositivos móveis| X| X|  |  |
| objectSID| X|  | X| propriedade mecânica. Identificador de utilizador do AD utilizado para manter a sincronização entre o Azure AD e AD.|
| physicalDeliveryOfficeName| X| X|  |  |
| código postal| X| X|  |  |
| preferredLanguage| X|  |  |  |
| pwdLastSet| X|  |  | propriedade mecânica. Utilizado para saber quando invalidar tokens já emitidos. Utilizado pelo sincronização de palavra-passe e Federação.|
| securityEnabled|  |  | X| Derivado de groupType|
| sn| X| X|  |  |
| sourceAnchor| X| X| X| propriedade mecânica. Identificador imutáveis para manter a relação entre adiciona e Azure AD.|
| St| X| X|  |  |
| streetAddress| X| X|  |  |
| telephoneNumber| X| X|  |  |
| título| X| X|  |  |
| usageLocation| X|  |  | propriedade mecânica. O país do utilizador. Utilizado para a atribuição de licenças.|
| userPrincipalName| X|  |  | UPN é o ID de início de sessão do utilizador. Com mais frequência é o valor igual ao [correio].|

## <a name="3rd-party-applications"></a>3º aplicações de terceiros
Este grupo é um conjunto de atributos utilizados como os atributos mínimos necessários para uma aplicação ou genérica carga de trabalho. Pode ser utilizado para uma carga de trabalho não listados na secção outra ou para uma aplicação que não sejam da Microsoft. Explicitamente é utilizado para o seguinte:

- Yammer (apenas o utilizador é consumido)
- [Híbrido (B2B) Business-para-Business organograma publicação em cenários de colaboração em disponibilizadas pelos recursos como o SharePoint](http://go.microsoft.com/fwlink/?LinkId=747036)

Este grupo é um conjunto de atributos que pode ser utilizado se diretório do Azure AD não é utilizado para suportar o Office 365, Dynamics ou Intune. Tem um pequeno conjunto de atributos core.

| Nome do atributo| Utilizador| Contacto| Grupo| Comentário |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | Define se uma conta está ativada.|
| NC| X|  | X|  |
| DisplayName em grupos| X| X| X|  |
| givenName| X| X|  |  |
| correio| X|  | X|  |
| managedBy|  |  | X|  |
| mailNickName| X| X| X|  |
| Membro|  |  | X|  |
| objectSID| X|  |  | propriedade mecânica. Identificador de utilizador do AD utilizado para manter a sincronização entre o Azure AD e AD.|
| proxyAddresses| X| X| X|  |
| pwdLastSet| X|  |  | propriedade mecânica. Utilizado para saber quando invalidar tokens já emitidos. Utilizado pelo sincronização de palavra-passe e Federação.|
| sn| X| X|  |  |
| sourceAnchor| X| X| X| propriedade mecânica. Identificador imutáveis para manter a relação entre adiciona e Azure AD.|
| usageLocation| X|  |  | propriedade mecânica. O país do utilizador. Utilizado para a atribuição de licenças.|
| userPrincipalName| X|  |  | UPN é o ID de início de sessão do utilizador. Com mais frequência é o valor igual ao [correio].|

## <a name="windows-10"></a>Windows 10
Um computer(device) façam parte de um domínio do Windows 10 sincroniza alguns atributos para Azure AD. Para mais informações sobre os cenários, consulte o artigo [ligar dispositivos de domínio para Azure AD para Windows 10 experiências](active-directory-azureadjoin-devices-group-policy.md). Estes atributos sempre sincronizar e Windows 10 não é apresentado como uma aplicação que pode anular a seleção. Um computador de domínio do Windows 10 é identificado por ter o userCertificate atributo povoada.

| Nome do atributo| Dispositivo| Comentário |
| --- | :-: | --- |
| accountEnabled| X| |
| deviceTrustType| X| Valor de codificado para computadores associados ao domínio. |
| DisplayName em grupos | X| |
| MS-DS-CreatorSID | X| Também denominado registeredOwnerReference.|
| GUID de objecto | X| Também denominado deviceID.|
| objectSID | X| Também denominado onPremisesSecurityIdentifier.|
| sistema operativo | X| Também denominado deviceOSType.|
| operatingSystemVersion | X| Também denominado deviceOSVersion.|
| userCertificate | X| |

Estes atributos de **utilizador** são para além de outras aplicações que selecionou.  

| Nome do atributo| Utilizador| Comentário |
| --- | :-: | --- |
| domainFQDN| X| Também denominado NomeDomínioDNS. Por exemplo, contoso.com.|
| domainNetBios| X| Também denominado NomeNetBIOS. Por exemplo, CONTOSO.|

## <a name="exchange-hybrid-writeback"></a>Repetição de escrita do Exchange híbrido
Estes atributos são escritos novamente a partir do Azure AD do Active Directory no local ao selecionar para ativar a **implementação híbrida do Exchange**. Consoante a sua versão do Exchange, poderão ser sincronizados atributos menos.

| Nome do atributo| Utilizador| Contacto| Grupo| Comentário |
| --- | :-: | :-: | :-: | --- |
| msDS ExternalDirectoryObjectID| X|  |  | Derivado de cloudAnchor no Azure AD. Este atributo é novo no Exchange 2016.|
| msExchArchiveStatus| X|  |  | Arquivo online: Permite que os clientes Arquivar correio.|
| msExchBlockedSendersHash| X|  |  | Filtragem: Responde no local a filtragem e dados de remetente seguros e bloqueados online a partir dos clientes.|
| msExchSafeRecipientsHash| X|  |  | Filtragem: Responde no local a filtragem e dados de remetente seguros e bloqueados online a partir dos clientes.|
| msExchSafeSendersHash| X|  |  | Filtragem: Responde no local a filtragem e dados de remetente seguros e bloqueados online a partir dos clientes.|
| msExchUCVoiceMailSettings| X|  |  | Ativar Unified Messaging (UM) - voicemail Online: utilizado pelo Microsoft Lync Server integration para indicar ao Lync Server no local que o utilizador tem do voice mail no serviços online.|
| msExchUserHoldPolicies| X|  |  | Suspensão de litígio: Permite aos serviços em nuvem para determinar quais os utilizadores que estão em mantenha a tecla de litígios.|
| proxyAddresses| X| X| X| Apenas o x500 endereço a partir do Exchange Online é inserido.|

## <a name="device-writeback"></a>Dispositivo repetição de escrita
Objectos de dispositivo são criados no Active Directory. Estes objetos podem ser dispositivos aderidos ao Azure AD ou domínio computadores do Windows 10.

| Nome do atributo| Dispositivo| Comentário |
| --- | :-: | --- |
| altSecurityIdentities | X| |
| DisplayName em grupos | X| |
| DN | X| |
| msDS CloudAnchor | X| |
| msDS DeviceID | X| |
| msDS DeviceObjectVersion | X| |
| msDS DeviceOSType | X| |
| msDS DeviceOSVersion | X| |
| msDS DevicePhysicalIDs | X| |
| msDS KeyCredentialLink | X| Apenas com o esquema do AD do Windows Server 2016 |
| msDS IsCompliant | X| |
| msDS IsEnabled | X| |
| msDS IsManaged | X| |
| msDS RegisteredOwner | X| |


## <a name="notes"></a>Notas

- Ao utilizar um ID alternativo, o userPrincipalName atributo no local é sincronizado com o onPremisesUserPrincipalName de atributo do Azure AD. O atributo ID alternativo, para correio eletrónico de exemplo, é sincronizado com o userPrincipalName atributo Azure AD.
- Nas listas acima, o tipo de objecto **utilizador** também se aplica ao de tipo de objeto **iNetOrgPerson**.

## <a name="next-steps"></a>Próximos passos
Saiba mais sobre a configuração de [sincronizar a ligação do Azure AD](active-directory-aadconnectsync-whatis.md) .

Saiba mais sobre como [integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md).
