<properties
   pageTitle="Ligação do Azure AD: Atualização automática | Microsoft Azure"
   description="Este tópico descreve a funcionalidade incorporada da atualização automática na ligação do Azure AD sync."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/24/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-automatic-upgrade"></a>Ligação do Azure AD: Atualização automática
Esta funcionalidade foi introduzida com compilação 1.1.105.0 (disponibilizada Fevereiro de 2016).

## <a name="overview"></a>Descrição geral
Certificar-se de que a instalação de ligação do Azure AD está sempre atualizada que nunca tenha sido mais fácil com a funcionalidade de **atualização automática** . Esta funcionalidade está ativada por predefinição para instalações express e actualizações do DirSync. Quando é disponibilizada uma nova versão, será automaticamente actualizada a instalação.

Atualização automática está ativada por predefinição para as seguintes opções:

- Express instalação de definições e DirSync atualiza-os.
- Utilizar o SQL Express LocalDB, que é que as definições de Express utilizam sempre. DirSync com SQL Express também utilizar LocalDB.
- A conta do AD é a conta MSOL_ predefinida criada pela Express definições e DirSync.
- Têm menos de 100.000 objetos na metaverse.

O estado atual da atualização automática pode ser visualizado com o cmdlet do PowerShell `Get-ADSyncAutoUpgrade`. Tem os seguintes Estados:

Estado | Comentário
---- | ----
Ativado | Atualização automática está ativada.
Suspensa | Defina pelo sistema apenas. O sistema já não é elegível para receber actualizações automáticas.
Desativado | Atualização automática é desativada.

Pode alterar entre **ativado** e **desativado** com `Set-ADSyncAutoUpgrade`. Apenas o sistema deve definir o estado **suspenso**.

Atualização automática está a utilizar Azure AD ligar-se do Estado de funcionamento para a infraestrutura de atualização. Para a atualização automática trabalhar, certifique-se de que abriu os URLs no servidor proxy para **Azure AD ligar-se do Estado de funcionamento** como documentado no [Office 365 URLs e intervalos de endereços IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

Se o **Gestor de serviço de sincronização** IU estiver em execução no servidor, em seguida, a atualização será suspensa até que a IU seja fechada.

## <a name="troubleshooting"></a>Resolução de problemas
Se a instalação do ligar não atualizar propriamente dito como esperado, em seguida, siga estes passos para saber o que poderá estar errado.

Em primeiro lugar, que deve esperar não a atualização automática para ser tentada o primeiro dia que é disponibilizada uma nova versão. Existe uma aleatoriedade intencional antes de uma atualização é tentada para que não se preocupe se a instalação não está atualizada imediatamente.

Se achar que algo não estiver à direita, em seguida, primeira execução `Get-ADSyncAutoUpgrade` para garantir a atualização automática está ativada.

Em seguida, certifique-se de que abriu os URLs necessários na sua firewall ou proxy. Atualização automática está a utilizar Azure AD ligar-se do Estado de funcionamento conforme descrito na [Descrição geral](#overview). Se utilizar um proxy, certifique-se de que estado de funcionamento tiver sido configurado para utilizar um [servidor proxy](active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). Também teste a [conectividade de estado de funcionamento](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) ao Azure AD.

Com a conectividade ao Azure AD verificado, é hora para analisar o eventlogs. Iniciar o Visualizador de eventos e verifique o registo de eventos de **aplicação** . Adicione um filtro de registo de eventos para a origem de **Azure AD ligar atualizar** e o evento id intervalo **300 399**.  
![Filtro de registo de eventos para a atualização automática](./media/active-directory-aadconnect-feature-automatic-upgrade/eventlogfilter.png)  

Agora pode ver eventlogs associados com o estado da atualização automática.  
![Filtro de registo de eventos para a atualização automática](./media/active-directory-aadconnect-feature-automatic-upgrade/eventlogresult.png)  

O código de resultado tem um prefixo com uma descrição geral do Estado.

Prefixo do código de resultado | Descrição
--- | ---
Sucesso | A instalação foi actualizada com êxito.
UpgradeAborted | Uma condição temporária parado a atualização. Vai ser repetido novamente e a expetativa é que sucede mais tarde.
UpgradeNotSupported | O sistema tem uma configuração que está a bloquear o sistema de automaticamente a ser atualizado. Vai ser tentada novamente para ver se o estado está a alterar, mas a expetativa é que o sistema deve ser atualizado manualmente.

Aqui está uma lista das mensagens mais comuns de que localizar. Não listar todos os, mas a mensagem de resultado deve ser limpar com que o problema é.

Mensagem de resultado | Descrição
--- | ---
**UpgradeAborted** |
UpgradeAbortedCouldNotSetUpgradeMarker | Não foi possível escrever para o registo.
UpgradeAbortedInsufficientDatabasePermissions | O grupo de administradores incorporados não tem permissões para a base de dados. Atualize manualmente para a versão mais recente da ligação do Azure AD para resolver este problema.
UpgradeAbortedInsufficientDiskSpace | Não existe espaço em disco suficiente para suportar uma atualização.
UpgradeAbortedSecurityGroupsNotPresent | Não foi possível encontrar e resolver todos os grupos de segurança utilizados pelo motor de sincronização.
UpgradeAbortedServiceCanNotBeStarted | O serviço NT **Microsoft Azure AD Sync** Falha ao iniciar.
UpgradeAbortedServiceCanNotBeStopped | Ocorreu uma falha parar o serviço NT **Microsoft Azure AD Sync** .
UpgradeAbortedServiceIsNotRunning | O serviço NT **Microsoft Azure AD Sync** não está em execução.
UpgradeAbortedSyncCycleDisabled | A opção SyncCycle do [scheduler](active-directory-aadconnectsync-feature-scheduler.md) tenha sido desativada.
UpgradeAbortedSyncExeInUse | O [Gestor de serviço de sincronização IU](active-directory-aadconnectsync-service-manager-ui.md) está aberto no servidor.
UpgradeAbortedSyncOrConfigurationInProgress | Está a ser executado o Assistente de instalação ou uma sincronização foi agendada fora do scheduler.
**UpgradeNotSupported** |
UpgradeNotSupportedCustomizedSyncRules | Ter adicionado o seus próprio regras personalizadas na configuração.
UpgradeNotSupportedDeviceWritebackEnabled | Ativou a funcionalidade de [dispositivo escrita não consolidada](active-directory-aadconnect-feature-device-writeback.md) .
UpgradeNotSupportedGroupWritebackEnabled | Ativou a funcionalidade de [grupo escrita não consolidada](active-directory-aadconnect-feature-preview.md#group-writeback) .
UpgradeNotSupportedInvalidPersistedState | A instalação não é uma definições Express ou uma atualização DirSync.
UpgradeNotSupportedMetaverseSizeExceeeded | Tem mais do que 100.000 objetos na metaverse.
UpgradeNotSupportedMultiForestSetup | Estiver a ligar a mais do que uma floresta. Configuração rápida liga-se para uma floresta.
UpgradeNotSupportedNonLocalDbInstall | Não está a utilizar uma base de dados do SQL Server Express LocalDB.
UpgradeNotSupportedNonMsolAccount | A [conta do AD Connector](active-directory-aadconnect-accounts-permissions.md#active-directory-account) já não está a conta MSOL_ predefinida.
UpgradeNotSupportedStagingModeEnabled | O servidor está definido para estar no [modo de transição](active-directory-aadconnectsync-operations.md#staging-mode).
UpgradeNotSupportedUserWritebackEnabled | Ativou a funcionalidade de [repetição de escrita de utilizador](active-directory-aadconnect-feature-preview.md#user-writeback) .

## <a name="next-steps"></a>Próximos passos
Saiba mais sobre como [integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md).
