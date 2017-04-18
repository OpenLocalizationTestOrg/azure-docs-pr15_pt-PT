<properties
   pageTitle="Eventos de relatório de auditoria do Azure Active Directory | Microsoft Azure"
   description="Auditados eventos que estão disponíveis para ver e transferir a partir do Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="dhanyahk"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/19/2016"
   ms.author="dhanyahk"/>

# <a name="azure-active-directory-audit-report-events"></a>Eventos de relatório de auditoria do Azure Active Directory

*Esta documentação faz parte do [Azure Active Directory relatórios Guide] (ativo-diretório de elaboração de relatórios-guide.md).*

O relatório de auditoria de diretório ativo do Azure ajuda os clientes identificar privilegiadas ações que ocorreram na sua Azure Active Directory. Acções privilegiadas incluem elevação alterações (por exemplo, a criação de função ou palavra-passe), a alteração configurações de política (por exemplo políticas de palavra-passe) ou as alterações à configuração de diretório (por exemplo, as alterações às definições de Federação de domínio). Os relatórios fornecem o registo de auditoria para o nome do evento, o ator que executou a acção, o recurso de destino afetada pela alteração e a data e hora (no UTC). Os clientes estão conseguir obter a lista de eventos de auditoria para os respetivos Azure Active Directory através de do [Portal do Azure](https://portal.azure.com/), conforme descrito na [vista os registos de auditoria](active-directory-reporting-azure-portal.md).


## <a name="list-of-audit-report-events"></a>Lista de eventos de relatório de auditoria
<!--- audit event descriptions should be in the past tense --->

Eventos                               | Descrição do evento
------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------
**Eventos de utilizador**                      |
Adicionar utilizador                             | Adicionar um utilizador ao diretório.
Eliminar utilizador                          | Eliminar um utilizador do diretório.
Definir propriedades de licença               | Defina as propriedades de licença para um utilizador no diretório.
Repor a palavra-passe do utilizador                  | Repor a palavra-passe para um utilizador no diretório.
Alterar a palavra-passe do utilizador                 | Alterar a palavra-passe para um utilizador no diretório.
Licença de utilizador de alteração                  | Alterar a licença atribuída a um utilizador no diretório. Para ver que licenças foram atualizadas, veja as propriedades do [utilizador de atualização](#update-user-attributes) abaixo
Atualizar o utilizador                          | Atualizado um utilizador no diretório. [Consulte abaixo](#update-user-attributes) para atributos que podem ser atualizados.
Palavra-passe de utilizador de alteração de forçar conjunto       | Defina a propriedade que cria um utilizador para alterar a respetiva palavra-passe no início de sessão.
Atualizar as credenciais de utilizador        | Utilizador alterado a palavra-passe  
**Eventos do grupo**                     |
Adicionar grupo                            | Criar um grupo no diretório.
Grupo de actualização                         | Atualizado um grupo no diretório. Para ver que propriedades do grupo foram atualizadas, referir-se para o [Grupo propriedades auditados](#update-group-attributes) na secção abaixo
Eliminar grupo                         | Eliminar um grupo do diretório.
Adicionar membro ao grupo            | Adicionado um membro a um grupo no diretório.
Remover membro do grupo         | Removido um membro de um grupo no diretório.
CreateGroupSettings              | Definições de grupo criado
UpdateGroupSettings          | Atualizadas as definições de grupo. Para ver quais as definições de grupo foram atualizadas, referir-se para o [Grupo propriedades auditados](#update-group-attributes) na secção abaixo
DeleteGroupSettings            | Definições de grupo eliminado
SetGroupLicense                  | Definir a licença de grupo.
SetGroupManagedBy              | Definir o grupo para ser gerido pelo utilizador
AddGroupMember               | Membro adicionado ao grupo
RemoveGroupMember            | Remover membro do grupo
AddGroupOwner                | Proprietário adicionado ao grupo
RemoveGroupOwner                 | Proprietário removido do grupo
**Eventos de aplicações**               |
Adicionar principal de serviço                | À principal de um serviço de diretório.
Remover principal de serviço             | Removido um principal de serviço do diretório.
Adicionar credenciais principal de serviço    | Adicionado credenciais para um principal de serviço.
Remover credenciais principal de serviço | Credenciais removidas de um serviço principal.
Adicionar a entrada de delegação                 | Criado um [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity) no diretório.
Entrada de conjunto de delegação                 | Actualizar uma [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity) no diretório.
Remover a entrada de delegação              | Eliminar um [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity) no diretório.
**Eventos de função**                      |
Adicionar membro de função a função              | Adicionado um utilizador a uma função de diretório.
Remover membro de função de função         | Removido um utilizador de uma função de diretório.
Definir as informações de contactos da empresa      | Definir as preferências de contacto de nível de empresa. Isto inclui endereços de e-mail para notificações de marketing, bem como técnicas sobre Microsoft Online Services.
Adicionar a entrada de delegação                 | Criado um [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity) no diretório.
Entrada de conjunto de delegação                 | Actualizar uma [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity) no diretório.
Remover a entrada de delegação              | Eliminar um [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity) no diretório.
AddSevicePrincipalOwner          | Adicionado proprietário para principal de serviço.
RemoveSevicePrincipalOwner   | Removido o proprietário do principal do serviço.
AddApplication  | Adicione aplicação.
UpdateApplication | Atualize a aplicação. Para ver as definições da aplicação que foram atualizadas, referir-se para a [Aplicação propriedades auditados](#update-application-attributes) na secção abaixo
DeleteApplication | Elimine aplicação.
RestoreApplication|Restaure a aplicação.
AddApplicationOwner   |     Adicione proprietário da aplicação.
RemoveApplicationOwner    |     Remova proprietário a partir da aplicação.
**Eventos de função**                         |
Adicionar membro de função a função                 | Adicionado um utilizador a uma função de diretório.
Remover membro de função de função            | Removido um utilizador de uma função de diretório.
AddRoleDefinition               | Definição de função adicionados.
UpdateRoleDefinition                | Definição de função atualizado. Para saber quais as definições da função foram atualizadas, consulte a [Função definição propriedades auditados](#update-role-definition-attributes) na secção abaixo
DeleteRoleDefinition                | Definição de função eliminada.
AddRoleAssignmentToRoleDefinition | Atribuição de funções adicionados a definição de função.
RemoveRoleAssignmentFromRoleDefinition | Atribuição de funções removidas de definição de função.
AddRoleFromTemplate     | Função adicionada a partir do modelo.
UpdateRole  | Função atualizada.
AddRoleScopeMemberToRole    | Membro âmbito adicionado à função.
RemoveRoleScopedMemberFromRole  | Removido âmbito membro da função.
**Eventos do dispositivo (todos os novos eventos)**                    |
AddDevice               | Dispositivo adicionado.
UpdateDevice            | Dispositivo actualizado. Para ver as propriedades do dispositivo que foram atualizadas, consulte [as propriedades do dispositivo Audited](#update-device-attributes) na secção abaixo
DeleteDevice            | Dispositivo eliminado.
AddDeviceConfiguration      | Configuração do dispositivo adicionado.
UpdateDeviceConfiguration   | Configuração do dispositivo atualizado. Para ver que propriedades de configuração do dispositivo foram atualizadas, consulte [Propriedades de configuração do dispositivo Audited](#update-device-configuration-attributes) na secção abaixo
DeleteDeviceConfiguration   | Configuração do dispositivo eliminadas.
AddRegisteredOwner     | Proprietário registado adicionado ao dispositivo.
AddRegisteredUsers     | Adicionado aos utilizadores registados para o dispositivo.
RemoveRegisteredOwner    | Remova proprietário registado a partir do dispositivo.
RemoveRegisteredUsers    | Remova utilizadores registados a partir do dispositivo.
RemoveDeviceCredentials  | Remova credenciais de dispositivo.
**B2B eventos**                       |
Convites lote carregados.              | Um administrador tem de carregar um ficheiro que contenha convites para ser enviados para utilizadores do parceiro.
Convites de lote processadas.             | Um ficheiro que contenha convites para utilizadores de parceiros foi processado.
Convide utilizadores externos.                | Um utilizador externo foi convidado ao diretório.
Resgate convidar utilizadores externos.         | Um utilizador externo tem resgatar o seu convite para o diretório.
Adicione utilizadores externos ao grupo.          | Um utilizador externo tem foi atribuído a associação a um grupo no diretório.
Atribua utilizadores externos a aplicação. | Foi atribuído um utilizador externo acesso direto para uma aplicação.
Criação do inquilino à.               | Um novo inquilino foi criado no Azure AD pelo reembolso de convite.
Criação de utilizador à.                 | Um utilizador for criado num inquilino existente no Azure AD pelo reembolso de convite.
**Unidades administrativas (todos os novos eventos)**             |
AddAdministrativeUnit   |   Adicione unidade administrativa.
UpdateAdministrativeUnit|   Actualize unidade administrativa. Para ver que propriedades de unidade administrativa foram atualizadas, consulte a [Propriedades de unidade administrativas auditados](#update-administrative-unit-attributes) na secção abaixo
DeleteAdministrativeUnit|   Elimine unidade administrativa.
AddMemberToAdministrativeUnit|  Adicione membro unidade administrativa.
RemoveMemberFromAdministrativeUnit|     Remova membro de unidade administrativa.
**Eventos de diretório**                 |
Adicionar parceiro a empresa               | Adicionar um parceiro ao diretório.
Remover o parceiro de empresa          | Removido um parceiro do diretório.
DemotePartner   |   Despromova parceiro.
Adicionar o domínio da empresa                | Adicionou um domínio ao diretório.
Remover o domínio de empresa           | Remover um domínio do diretório.
Atualizar o domínio                        | Atualizado um domínio no diretório. Para ver que propriedades do domínio que foram atualizadas, consulte a [Domain Properties auditados](#update-domain-attributes) na secção abaixo
Definir a autenticação de domínio            | Alterar a definição de domínio predefinido para a empresa.
Definir as informações de contactos da empresa      | Definir as preferências de contacto de nível de empresa. Isto inclui endereços de e-mail para notificações de marketing, bem como técnicas sobre Microsoft Online Services.
Configurar definições de Federação no domínio    | Atualizadas as definições de Federação para um domínio.
Verificar o domínio                        | Verificar um domínio no diretório.
Verificar o domínio verificado do e-mail         | Verificar um domínio no directório utilizando a verificação de correio eletrónico.
Definir o sinalizador de DirSyncEnabled na empresa   | Defina a propriedade que permite um diretório do Azure AD Sync.
Definir a política de palavra-passe                  | Definir restrições de comprimento e carateres para palavras-passe de utilizador.
Informações da empresa conjunto              | Atualizadas as informações de nível de empresa. Consulte o artigo o cmdlet do PowerShell [Get-MsolCompanyInformation](https://msdn.microsoft.com/library/azure/dn194126.aspx) para obter mais detalhes.
SetCompanyAllowedDataLocation  |    Conjunto de empresa permitida localização de dados.
SetCompanyDirSyncEnabled    |   Defina o sinalizador de DirSyncEnabled.
SetCompanyDirSyncFeature |  Defina a funcionalidade de DirSync.
SetCompanyInformation |   Informações de empresa do conjunto.
SetCompanyMultiNationalEnabled    |     Definir funcionalidade multinacionais empresa ativada.
SetDirectoryFeatureOnTenant   |     Configurar a funcionalidade de diretório no inquilino.
SetTenantLicenseProperties  |   Definir as propriedades de licença do inquilino.
CreateCompanySettings   |   Criar definições de empresa
UpdateCompanySettings   |   Atualize as definições da empresa. Para ver que propriedades de empresa foram atualizadas, consulte a [empresa propriedades auditados](#update-company-attributes) na secção abaixo
DeleteCompanySettings   |   Eliminar definições de empresa
SetAccidentalDeletionThreshold   |  Definir o limiar de acidentais.
SetRightsManagementProperties   |   Definir propriedades de gestão de direitos.
PurgeRightsManagementProperties     |   Limpar propriedades de gestão de direitos.
UpdateExternalSecrets   |   Atualizar segredos externos
**Eventos de política (todos os novos eventos)**           |
AddPolicy   |   Adicione política.
UpdatePolicy    |   Actualize política.
DeletePolicy    |   Elimine política.
AddDefaultPolicyApplication     |   Adicione política de aplicação.
AddDefaultPolicyServicePrincipal    |   Adicione política para principal de serviço.
RemoveDefaultPolicyApplication  |   Remova a política de aplicação.
RemoveDefaultPolicyServicePrincipal     |   Remova a política de capital de serviço.
RemovePolicyCredentials     |   Remova credenciais de política.

## <a name="audit-report-retention"></a>Retenção de relatório de auditoria
Eventos no relatório de auditoria do Azure AD são mantidos para 180 dias. Para mais informações sobre retenção relatórios, consulte o artigo [Políticas de retenção de relatório do Azure Active Directory](active-directory-reporting-retention.md).

Para clientes interessados em armazenar os seus eventos de auditoria para períodos de retenção mais longo, API elaboração de relatórios podem ser utilizado para regularmente importar eventos de auditoria para um arquivo de dados separado. Para obter mais detalhes, consulte a [Introdução com a API do relatório](active-directory-reporting-api-getting-started.md) .

## <a name="properties-included-with-each-audit-event"></a>Propriedades incluídas com cada evento de auditoria

Propriedade      | Descrição
------------- | --------------------------------------------------------------
Data e hora | A data e hora em que ocorreu o evento de auditoria
Ator         | O utilizador ou o capital de serviço que efetuada a ação
Ação        | A ação que foi efetuada
Destino        | O utilizador ou o capital de serviço que a ação foi efetuada no


## <a name="update-user-attributes"></a>Atributos "Atualizar utilizador"
O evento de auditoria "Utilizador de atualização" inclui informações adicionais sobre que atributos do utilizador foram atualizados. Para cada atributo, o valor anterior e o novo valor está incluído.

Atributo                           | Descrição
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AccountEnabled                      | O utilizador pode iniciar sessão.
AssignedLicense                     | Todas as licenças que foram atribuídas ao utilizador.
AssignedPlan                        | Planos do serviços resultantes as licenças atribuídas ao utilizador.
LicenseAssignmentDetail             | Obter detalhes sobre licenças atribuídas ao utilizador. Por exemplo, se com base no grupo de licenciamento, foi normalmente, isto incluir o grupo que lhe concedeu a licença.
Mobile                              | Número de telemóvel do utilizador.
OtherMail                           | Endereço de e-mail alternativo do utilizador.
OtherMobile                         | Alternativo número de telemóvel o utilizador.
StrongAuthenticationMethod          | Uma lista de métodos de verificação configurados pelo utilizador para a autenticação Multifator, tal como chamadas de voz, SMS ou verificação código a partir de uma aplicação móvel.
StrongAuthenticationRequirement     | Se a autenticação Multifator é aplicada, ativado ou desativado para este utilizador.
StrongAuthenticationUserDetails     | Número de telefone do utilizador, alternativo telefone número e endereço de e-mail utilizado para verificação de reposição de autenticação Multifator e palavra-passe.
StrongAuthenticationPhoneAppDetail  | Detalhe forof aplicações do telemóvel estão registadas para efetuar 2FA início de sessão
TelephoneNumber                     | Número de telefone do utilizador.
AlternativeSecurityId               | Um ID de segurança alternativo para o objeto.
CreationType                        |Método de criação do utilizador (seja por convite ou à).
InviteTicket                        |Lista de bilhetes de convite para o utilizador.
InviteReplyUrl                      |Lista de urls para responder após a aceitação do convite.
InviteResources                     |Lista de recursos para o qual o utilizador foi convidado.
LastDirSyncTime                     |Última vez que o objeto foi atualizado devido a sincronizar o autoritativas de diretório (cliente, no local).
MSExchRemoteRecipientType           |Mapas de tipos de destinatários TDIA. Referir-se a [tipos de destinatários TDIA] https://msdn.microsoft.com/library/microsoft.office.interop.outlook.recipient.type.aspx para tipos de destinatários
PreferredDataLocation               |A localização preferida para o utilizador, do grupo, contacto, pasta pública, ou de dados do dispositivo.
ProxyAddresses                      |O endereço pelo qual é reconhecido um objeto de destinatário do Exchange Server no sistema de correio externo.
StsRefreshTokensValidFrom           |Cumpram atualizar as informações de revogação token - qualquer tokens de atualização de STS emitidos antes deste período de tempo são consideradas expiraram.
UserPrincipalName                   |O UPN que é um nome de início de sessão do estilo de Internet para um utilizador.
UserState                           |Estado de utilizador PendingApproval/PendingAcceptance/aceites/PendingVerification.
UserStateChangedOn                  |O carimbo de data/hora da última alteração para UserState. Utilizado para desencadear fluxos de trabalho do ciclo de vida.
UserType                            |Tipo de utilizador. Membro de (0), convidado (1), Viral(2).

## <a name="update-group-attributes"></a>Atributos de "Atualizar grupo"
Atributo                           | Descrição
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
Classificação            | A classificação de um grupo de unificado (HBI, MBI, etc).
Descrição               | Frases descritivas legível sobre o objeto.  
DisplayName em grupos               |O nome a apresentar de um objeto
DirSyncEnabled            |Indica se a sincronização ocorre a partir de um autoritativas diretório (cliente, no local).
GroupLicenseAssignment    |Atribuição de licenças de um grupo
GroupType                 |Tipo de grupo, unificado (0)
IsMembershipRuleLocked    |Indica que a propriedade MembershipRule é definida pelo serviço de gestão de gestão personalizada grupo e não pode ser alterada pelos utilizadores. Aplicável apenas a grupos onde GroupType inclui GroupType.DynamicMembership
IsPublic                  |Sinalize para indicar se o grupo for público/privado.
LastDirSyncTime           |Última vez que o objeto foi atualizado estatístico como resultado da sincronização a partir do autoritativas diretório (cliente, local).
Correio                      |Endereço de correio principal
MailEnabled               |Indica se este grupo tem capacidade de correio electrónico.
MailNickname              |Moniker para um objecto do livro de endereços, normalmente, a parte do seu nome de correio eletrónico anterior a "@" símbolo.   
MembershipRule            |Uma cadeia expressar os critérios utilizados pelo serviço de gestão de grupo de gestão personalizada para determinar quais os membros devem pertencer a este grupo. Consulte também IsMembershipRuleLocked. Aplicável apenas a grupos onde GroupType inclui GroupType.DynamicMembership.
MembershipRuleProcessingState| Um valor de enumeração definido pelo serviço de gestão de grupo de gestão personalizada que define o estado de associação de processamento para este grupo. Aplicável apenas a grupos onde GroupType inclui GroupType.DynamicMembership.
ProxyAddresses            |O endereço pelo qual é reconhecido um objeto de destinatário do Exchange Server no sistema de correio externo.
RenewedDateTime           |Registo de data/hora de quando um grupo foi mais recentemente renovado.   
SecurityEnabled           |Indica se a associação ao grupo pode influenciar decisões de autorização.
WellKnownObject           |Etiquetas de um objeto de diretório, designá-lo como parte de um conjunto predefinido.

## <a name="update-device-attributes"></a>Atributos "Actualizar dispositivo"
Atributo                           | Descrição
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AccountEnabled | Indica se um principal de segurança pode autenticar.
CloudAccountEnabled | Indica se um principal de segurança pode autenticar. Escritos por InTune quando o dispositivo está conhece local.
CloudDeviceOSType | Tipo de dispositivo com base no SO, por exemplo, Windows RT, iOS. Quando é definida por um serviço na nuvem (como o Intune), este atributo torna-se autoritativas no diretório para DeviceOSType.
CloudDeviceOSVersion | Versão do SO. Quando é definida por um serviço na nuvem (como o Intune), este atributo torna-se autoritativas no diretório para DeviceOSVersion.
CloudDisplayName | Valor do atributo LDAP DisplayName em grupos. Quando é definida por um serviço na nuvem (como o Intune), este atributo torna-se autoritativas no diretório para DisplayName em grupos.
CloudCreated |Indica se o objeto foi criado por serviços em nuvem.
CompliantUntil | Até que altura dispositivo é considerado em conformidade.
DeviceMetadata |Metadados personalizados para o dispositivo
DeviceObjectVersion | Este atributo é utilizado para identificar a versão do esquema do dispositivo.
DeviceOSType |Tipo de dispositivo com base no SO, por exemplo, Windows RT, iOS. Escrito pelo serviço de registo e se destina a ser atualizados pela MDM serviço de gestão ou STS claro serviço de gestão.
DeviceOSVersion |Versão do SO. Escrito pelo serviço de registo e se destina a ser atualizados pela MDM serviço de gestão ou STS claro serviço de gestão.
DevicePhysicalIds |Atributo de valor múltiplo destinados armazenar os identificadores de dispositivo físico. Isto pode incluir BIOS IDs, miniaturas TPM, hardware IDs específicos, etc.
DirSyncEnabled | Indica se a sincronização ocorre a partir de um autoritativos (cliente, local) diretório.
DisplayName em grupos |O nome a apresentar de um objeto
IsCompliant | Este atributo é utilizado para gerir o estado de gestão de dispositivos móveis do dispositivo.
IsManaged |Este atributo é utilizado para indicar que o dispositivo é gerido por uma nuvem a MDM.
LastDirSyncTime |Última vez que o objeto foi atualizado devido a sincronizar o autoritativas de diretório (cliente, local).

## <a name="update-device-configuration-attributes"></a>Atributos "Atualizar a configuração do dispositivo"
Atributo                           | Descrição
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
MaximumRegistrationInactivityPeriod |Considera-se o número máximo de um dispositivo pode ser inativo antes de dias para a remoção.
RegistrationQuota   |Política utilizada para limitar o número de registos de dispositivo permitida para um único utilizador.

## <a name="update-service-principal-configuration-attributes"></a>Atributos "Atualizar configuração principal do serviço"
Atributo                           | Descrição
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AccountEnabled |Indica se um principal de segurança pode autenticar.
AppPrincipalId | Identidade externa, definidas pela aplicação para um principal de segurança.
DisplayName em grupos |O nome a apresentar de um objeto
ServicePrincipalName    | Um nome principal do serviço, que contém "nome/autoridade de" onde nome Especifica um valor de classe de aplicação e autoridade contém, pelo menos, hostname [: porta] ou "nome" que especifica um identificador para o capital de serviço.

## <a name="update-app-attributes"></a>Atributos "Atualizar a aplicação"
Atributo                           | Descrição
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AppAddress |O conjunto de endereços (redireccionar URLs) que são atribuídas a um principal de serviço.
AppId                               |ID da aplicação da aplicação   
AppIdentifierUri | URI aplicação, que identifica o pedido.  É normalmente o URL da aplicação access.
AppLogoUrl |O url para a imagem de logótipo de aplicação armazenada numa CDN.
AvailableToOtherTenants | VERDADEIRO a aplicação é inquilino com várias aplicação (ou seja, pode ser utilizado por outros inquilinos).
DisplayName em grupos | O nome a apresentar para nome de uma aplicação
Direito |Lista de direitos de aplicação.
ExternalUserAccountDelegationsAllowed |Sinalize que indica se a aplicação de recurso é um fidedigno e pode criar entradas de delegação para contas de utilizador externos.
GroupMembershipClaims |A política de violação de associação de grupo.
PublicClient | TRUE se o cliente não será possível manter secreto (ou seja, não confidenciais cliente OAuth2.0)
RecordConsentConditions | Tipos de consentimento condições, conforme definido pelos termos de contrato: nenhuma (0), SilentConsentForPartnerManagedApp(1). Este valor será apresentado quando se clica no esquema de gráfico API e só pode ser definidas/alteradas por administradores de inquilino.
RequiredResourceAccess |Conteúdos XML de um valor da propriedade RequiredResourceAccess.
Web App |Se for VERDADEIRO, indica que esta aplicação é uma aplicação web.
WwwHomepage |A página Web principal.

## <a name="update-role-attributes"></a>Atributos "Actualizar função"
Atributo                           | Descrição
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AppAddress |O conjunto de endereços (redireccionar URLs) que são atribuídas a um principal de serviço.
BelongsToFirstLoginObjectSet |Se for VERDADEIRO, indica que este objeto pertence ao conjunto de objetos necessárias para permitir o início de sessão da primeira admin de um novo inquilino.
Incorporados |Indica se o tempo de vida de um objeto é propriedade pelo sistema.
Descrição | Frases descritivas legível sobre o objeto.  
DisplayName em grupos |O nome a apresentar de um objeto
MailNickname | Moniker para um objecto do livro de endereços, normalmente, a parte do seu nome de correio eletrónico anterior a "@" símbolo.
RoleDisabled |Indica se a função deve ser ignorada para fins de controlo de acesso.
RoleTemplateId | Identidade do modelo de função.
ServiceInfo |Serviço de aprovisionamento informação específica que pode ser consumida por MOAC e/ou outras instâncias de serviço (dos tipos de serviço mesmo ou diferente).
TaskSetScopeReference |Identifica um TaskSet e um conjunto de âmbitos associados a uma função ou RoleTemplate.
ValidationError |Informações publicadas por um serviço federado que descreve um erro que não sejam transitória, específico do serviço relativamente ao propriedades ou ligação a partir de uma ação de administrador do objeto para resolver.
WellKnownObject |Etiquetas de um objeto de diretório, designá-lo como parte de um conjunto predefinido.

## <a name="update-role-definition-attributes"></a>Atributos "Atualizar a definição de função"
Atributo                           | Descrição
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AssignableScopes    |Colecção de âmbitos de autorização que podem ser referenciados ao atribuir esta RoleDefinition a um principal de segurança.
DisplayName em grupos     |O nome a apresentar de um objeto
GrantedPermissions  |Permissões concedidas por este RoleDefinition.


## <a name="update-administrative-unit-attributes"></a>Atributos "Atualizar unidade administrativa"
Atributo                           | Descrição
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
Descrição |   Esta propriedade é atualizada quando altera a descrição de uma unidade administrativa.
DisplayName em grupos |   Esta propriedade é atualizada quando altera o nome de uma unidade administrativo.

## <a name="update-company-attributes"></a>Atributos de "Atualização da empresa"
Atributo                           | Descrição
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AllowedDataLocation     | Uma localização na qual os utilizadores da empresa podem ser aprovisionado.
AuthorizedServiceInstance   | Nomes de instâncias do serviço para o qual um plano pode ser implementado.
DirSyncEnabled |Indica se a sincronização ocorre a partir de um autoritativos (cliente, local) diretório.
DirSyncStatus |Indica se a sincronização de objetos do livro de endereços neste contexto de inquilino ocorre a partir de um autoritativos (cliente, local) directório; uma expansão da propriedade DirSyncEnabled no objetos da empresa.
DirSyncFeatures |Sinalizador de bit para controlar o conjunto de funcionalidades do dirsync ativado e desativado para o inquilino.
DirectoryFeatures |Funcionalidades de activado/desactivado diretório.
DirSyncConfiguration |Contém toda DirSync a configuração específicas para o inquilino atual.
DisplayName em grupos |O nome a apresentar de um objeto
IsMnc |Definir um sinalizador booleano IFF "true" a empresa está ativada para a funcionalidade de multinacionais da empresa.
ObjectSettings | Uma coleção de definições aplicáveis para o âmbito do objeto.
PartnerCommerceUrl |URL do site de comércio o parceiro.
PartnerHelpUrl |URL do site de ajuda do parceiro.
PartnerSupportEmail | URL ao correio eletrónico de suporte do parceiro.
PartnerSupportTelephone |URL para o telefone de suporte do parceiro.
PartnerSupportUrl |URL do site de suporte do parceiro.
StrongAuthenticationDetails |Detalhes relacionados com StrongAuthentication.
StrongAuthenticationPolicy |Política de autenticação forte para a empresa.
TechnicalNotificationMail |Endereço de correio electrónico a notificar questões técnicas sobre a uma empresa.
TelephoneNumber |Números de telefone que está em conformidade com a E.123 recomendação ITU.
TenantType |O tipo de um inquilino. Se este valor não for especificado, o inquilino é uma empresa. Caso contrário, os valores possíveis são: MicrosoftSupport (0), SyndicatePartner (1), BreadthPartner (2) BreadthPartnerDelegatedAdmin (3) ResellerPartnerDelegatedAdmin (4) ValueAddedResellerPartnerDelegatedAdmin (5).
VerifiedDomain |Um conjunto de nomes de domínio DNS dependente de uma empresa.

## <a name="update-domain-attributes"></a>Atributos "Atualizar o domínio"
Atributo                           | Descrição
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
Capacidades de    |Bit sinalizadores que descrevem as funcionalidades do domínio, se existirem.
Predefinido |Indica se o domínio é o valor predefinido; Por exemplo, o UserPrincipalName sufixo predefinido quando um administrador cria um novo utilizador na MOAC.
Inicial |Indica se o domínio o domínio inicial para a empresa, tal como atribuído pelo OCP. O domínio inicial é um sub-domínio exclusivo de um domínio do Microsoft Online; e.g.contoso3.microsoftonline.com.
LiveType    |Tipo de correspondente do Windows Live espaço de nomes, se existirem.
Nome    | Identificador para o ponto final.
PasswordNotificationWindowDays  |O número de dias antes de uma palavra-passe expira o utilizador será notificado.
PasswordValidityPeriodDays  | O número de dias que uma palavra-passe é boa ideia para antes de têm de ser alterada.

Um controlo necessário para muitos regulamentos de conformidade são os registos de auditoria. Para clientes que utilizam o Azure Active Directory auditoria relatório para cumprir os seus regulamentos de conformidade, recomenda-se que o cliente submeter uma cópia deste tópico de ajuda com a cópia do relatório de auditoria exportado do cliente para o ajudar a explicar os detalhes do relatório. Se gostaria de revisor compreender os regulamentos de conformidade Azure atualmente cumpre, encaminhá revisor para a [página de conformidade](https://azure.microsoft.com/support/trust-center/compliance/) da Center do Microsoft Azure Trust.
