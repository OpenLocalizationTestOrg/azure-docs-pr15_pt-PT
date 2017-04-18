<properties
   pageTitle="Sincronização do Azure AD Connect: Scheduler | Microsoft Azure"
   description="Este tópico descreve a funcionalidade de agenda incorporada no ligação do Azure AD sync."
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
   ms.date="08/04/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-sync-scheduler"></a>Sincronização do Azure AD Connect: Scheduler
Este tópico descreve o agendador incorporado na ligação do Azure AD sync (também conhecido como motor de sincronização).

Esta funcionalidade foi introduzida com compilação 1.1.105.0 (disponibilizada Fevereiro de 2016).

## <a name="overview"></a>Descrição geral
Sincronização do Azure AD Connect irá sincronizar alterações passa no seu diretório no local utilizando um programador. Existem dois processos de programador, uma para a sincronização de palavra-passe e outro para sincronizar objeto/atributo e tarefas de manutenção. Este tópico vai abordar o último.

Lançamentos anteriores scheduler para atributos e objetos era externo para o motor de sincronização e o Programador de tarefas do Windows ou um serviço em Windows separado foi utilizado para acionar o processo de sincronização. O scheduler é com a incorporada 1.1 lançamentos para a sincronização do motor e permitir que algumas personalização. A frequência de sincronização novo predefinido é 30 minutos.

O scheduler é responsável por duas tarefas:

- **Ciclo de sincronização**. O processo para importar, sincronize e exportar as alterações.
- **Tarefas de manutenção**. Renove chaves e certificados para a reposição de palavra-passe e o serviço de registo de dispositivo (DRS). Remover as entradas no registo de operações antigas.

O scheduler propriamente dito sempre está em execução, mas pode ser configurado para executar apenas um ou nenhuma destas tarefas. Por exemplo se necessita de ter o seu próprio processo de ciclo de sincronização, pode desativar esta tarefa no programador de mas continuar a executar a tarefa de manutenção.

## <a name="scheduler-configuration"></a>Configuração do Programador
Para ver as suas definições de configuração atuais, aceda ao PowerShell e executar `Get-ADSyncScheduler`. -Irão mostrar-lhe algo parecido com:

![GetSyncScheduler](./media/active-directory-aadconnectsync-feature-scheduler/getsynccyclesettings.png)

Se vir **o comando de sincronização ou cmdlet não está disponível** quando executa este cmdlet, o módulo PowerShell não é carregado. Isto pode acontecer se executar ligação do Azure AD num controlador de domínio ou num servidor com níveis de restrição do PowerShell mais elevados que as predefinições. Se vir este erro, em seguida, execute o `Import-Module ADSync` para disponibilizar o cmdlet.

- **AllowedSyncCycleInterval**. Com maior frequência Azure AD permitirá sincronizações a ocorrer. Não é possível sincronizar com uma frequência maior e continua a ser suportado.
- **CurrentlyEffectiveSyncCycleInterval**. A agenda atualmente em efeito. Este terá o mesmo valor que CustomizedSyncInterval (se definir) se não for mais frequente do que AllowedSyncInterval. Se alterar CustomizedSyncCycleInterval, este será aplicada depois próximo ciclo de sincronização.
- **CustomizedSyncCycleInterval**. Se pretender que o programador execute com qualquer outra frequência que o predefinido 30 minutos, irá configurar esta definição. Na imagem acima do scheduler foi definida para executar em vez disso, a cada hora. Se definir isto a um valor inferior a AllowedSyncInterval, o último será utilizado.
- **NextSyncCyclePolicyType**. Delta ou inicial. Define se executar seguinte deve apenas processo delta as alterações, ou se a executar seguinte deve fazer um inteiro para importar e sincronizar, que seria Reprocessar também todas as regras de novas ou alteradas.
- **NextSyncCycleStartTimeInUTC**. Próxima vez que o agendador iniciará próximo ciclo de sincronização.
- **PurgeRunHistoryInterval**. O tempo que devem ser mantidos em registos de operação. Estes podem ser revistos no Gestor de serviço de sincronização. A predefinição é para mantê-los durante 7 dias.
- **SyncCycleEnabled**. Indica se o programador está em execução a importar, sincronizar e processos de exportação como parte do seu funcionamento.
- **MaintenanceEnabled**. Mostra se o processo de manutenção está ativado. Será atualizar as teclas/certificados e remover o registo de operações.
- **IsStagingModeEnabled**. Mostra se o [modo de transição](active-directory-aadconnectsync-operations.md#staging-mode) está ativado.

Pode alterar algumas destas definições com `Set-ADSyncScheduler`. Podem ser modificados seguintes parâmetros:

- CustomizedSyncCycleInterval
- NextSyncCyclePolicyType
- PurgeRunHistoryInterval
- SyncCycleEnabled
- MaintenanceEnabled

A configuração do programador está armazenada no Azure AD. Se tiver um servidor de teste, qualquer alteração no servidor principal também terá efeito servidor de teste (com a exceção das IsStagingModeEnabled).

### <a name="customizedsynccycleinterval"></a>CustomizedSyncCycleInterval
Sintaxe:`Set-ADSyncScheduler -CustomizedSyncCycleInterval d.HH:mm:ss`  
d - dias, HH - horas, segundos mm - minutos, ss-

Exemplo:`Set-ADSyncScheduler -CustomizedSyncCycleInterval 03:00:00`  
Irá alterar o programador execute cada 3 horas.

Exemplo:`Set-ADSyncScheduler -CustomizedSyncCycleInterval 1.0:0:0`  
Irá alterar o Programador de tarefas para são executadas diariamente.

## <a name="start-the-scheduler"></a>Iniciar o programador
Por predefinição, o scheduler será executada a cada 30 minutos. Em alguns casos poderá pretender executar um ciclo de sincronização entre os ciclos agendados ou que precisa para executar um tipo diferente.

**Ciclo de sincronização delta**  
Um ciclo de sincronização delta inclui os seguintes passos:

- Importar delta em todas as conexões
- Sincronizar de delta em todas as conexões
- Exportar no todas as conexões

Dever-se que tem um alterar que têm de ser sincronizado imediatamente que é a razão pela qual o que precisa para executar manualmente um ciclo urgente. Se precisar de executar em manualmente um ciclo, em seguida, a partir do PowerShell executar `Start-ADSyncSyncCycle -PolicyType Delta`.

**Ciclo de sincronização completa**  
Se efetuar um das seguintes alterações à configuração, que precisa para executar um ciclo de sincronização completa (também conhecido como Inicial):

- Adicionado mais objetos ou atributos para serem importados a partir de um diretório de origem
- Efetuar as alterações para as regras de sincronização
- Alterados a [filtragem](active-directory-aadconnectsync-configure-filtering.md) por isso deve ser incluído num número diferente de objetos

Se efetuar um destas alterações, em seguida, que precisa para executar um ciclo de sincronização completa para que o motor de sincronização tenha a oportunidade de voltar a consolidar os espaços de conexão. Um ciclo de sincronização completa inclui os seguintes passos:

- Importação completa em todas as conexões
- Sincronizar completo em todas as conexões
- Exportar no todas as conexões

Para iniciar um ciclo de sincronização completa, execute `Start-ADSyncSyncCycle -PolicyType Initial` a partir de uma linha de comandos do PowerShell. Isto irá iniciar um ciclo de sincronização completa.

## <a name="stop-the-scheduler"></a>Parar o programador
Se o programador está a executar atualmente um ciclo de sincronização que poderá ter de pará-la. Por exemplo, se iniciar o Assistente de instalação e obtém este erro:

![SyncCycleRunningError](./media/active-directory-aadconnectsync-feature-scheduler/synccyclerunningerror.png)

Quando está a ser executado um ciclo de sincronização, não pode efetuar alterações na configuração. Foi Aguarde até que o agendador foi concluída o processo, mas também pode deixar de-lo para que possa efetuar as alterações imediatamente. Parar o ciclo de atual não é prejudicial e quaisquer alterações que ainda não foi processadas serão processadas com use seguinte.

1. Comece por informar do scheduler para parar de ciclo atual com o cmdlet do PowerShell `Stop-ADSyncSyncCycle`.
2. Parar o scheduler não irá parar a conexão atual a partir da sua tarefa atual. Para forçar a conexão para parar, efetuar as seguintes ações: ![StopAConnector](./media/active-directory-aadconnectsync-feature-scheduler/stopaconnector.png)
    - Inicie **Sincronização do serviço** a partir do menu Iniciar. Aceda aos **conectores**, realce o conector com o estado de **Executar** e selecione **Parar** as ações.

O programador ainda está activo e irá iniciar novamente na próxima oportunidade.

## <a name="custom-scheduler"></a>Scheduler personalizado
Os cmdlets documentados nesta secção só estão disponíveis na compilação [1.1.130.0](active-directory-aadconnect-version-history.md#111300) e versões posteriores.

Se o programador incorporado não corresponderem às seus requisitos, em seguida, pode agendar as conexões através do PowerShell.

### <a name="invoke-adsyncrunprofile"></a>ADSyncRunProfile invocar
Pode iniciar um perfil para uma conexão desta forma:

```
Invoke-ADSyncRunProfile -ConnectorName "name of connector" -RunProfileName "name of profile"
```

Os nomes a utilizar para [nomes de conexão](active-directory-aadconnectsync-service-manager-ui-connectors.md) e [executar o perfil](active-directory-aadconnectsync-service-manager-ui-connectors.md#configure-run-profiles) podem encontrar na [IU do Gestor de serviço de sincronização](active-directory-aadconnectsync-service-manager-ui.md).

![Invocar executar perfil](./media/active-directory-aadconnectsync-feature-scheduler/invokerunprofile.png)  

O `Invoke-ADSyncRunProfile` cmdlet é síncrono, ou seja, nem irá devolver controlo até o conector de concluída a operação, com êxito ou com um erro.

Agendar as conexões, a recomendação é agendá-los pela seguinte ordem:

1. (Inteiro/Delta) Importar a partir de diretórios no local, como o Active Directory
2. (Inteiro/Delta) Importar a partir do Azure AD
3. (Inteiro/Delta) Sincronização de diretórios no local, como o Active Directory
4. (Inteiro/Delta) Sincronização a partir do Azure AD
5. Exportar para o Azure AD
6. Exportar para directórios no local, como o Active Directory

Se observe a agenda incorporada, esta é a ordem que as conexões serão executado.

### <a name="get-adsyncconnectorrunstatus"></a>Get-ADSyncConnectorRunStatus
Também pode monitorizar o motor de sincronização para ver se for ocupado ou inactivo. Este cmdlet irá devolver um resultado vazio, se o motor de sincronização é idle e não está em execução de uma conexão. Se estiver a executar uma conexão, irá devolver o nome da conexão.

```
Get-ADSyncConnectorRunStatus
```

![Executar o conector de estado](./media/active-directory-aadconnectsync-feature-scheduler/getconnectorrunstatus.png)  
Na imagem acima, a primeira linha é de um Estado de onde está inactivo o motor de sincronização. A segunda linha de quando o conector do Azure AD está em execução.

## <a name="scheduler-and-installation-wizard"></a>Assistente de programador e a instalação
Se iniciar o Assistente de instalação, em seguida, o scheduler será temporariamente suspensa. Esta é uma vez que é considerada irá efetuar alterações à configuração e estes não podem ser aplicados se o motor de sincronização ativamente estiver em execução. Por este motivo, não deixe o Assistente de instalação aberta uma vez que irá deixar do motor de sincronização a partir de realizar quaisquer ações de sincronização.

## <a name="next-steps"></a>Próximos passos
Saiba mais sobre a configuração de [sincronizar a ligação do Azure AD](active-directory-aadconnectsync-whatis.md) .

Saiba mais sobre como [integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md).
