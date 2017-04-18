<properties
    pageTitle="O registo de atividade Azure para evento concentradores transmitir em fluxo | Microsoft Azure"
    description="Saiba como transmitir em fluxo o registo de atividade Azure concentradores do evento."
    authors="johnkemnetz"
    manager="rboucher"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="johnkem"/>

# <a name="stream-the-azure-activity-log-to-event-hubs"></a>O registo de atividade Azure concentradores evento a sequência
O [**Registo de atividade Azure**](./monitoring-overview-activity-logs.md) pode ser transmitido em tempo real próximo para qualquer aplicação com a opção "Exportar" incorporada no portal do ou ao ativar o Id da regra Bus serviço de um perfil de registo através dos cmdlets do PowerShell do Azure ou clip de Azure.

## <a name="what-you-can-do-with-the-activity-log-and-event-hubs"></a>O que pode fazer com o registo de atividade e concentradores de evento
Seguem-se apenas algumas formas que poderá utilizar a funcionalidade de transmissão para o registo de atividade:

- **Sequência de sistemas de registo e de telemetria de terceiros** – ao longo do tempo, concentradores evento de transmissão irão tornar-se a mecanismos para encaminhar o registo de atividade para terceiros SIEMs e inicie sessão soluções de análise.
- **Criar um telemetria personalizada e a plataforma de registo** – se já tiver uma plataforma de telemetria personalizado ou são apenas a pensar sobre a criação de um, altamente dimensionáveis publiquem-subscrever natureza do evento concentradores permite-lhe flexível ingerir esta última o registo de atividade. [Consulte o artigo guia de Duarte Rosanova à utilização do evento concentradores numa plataforma de telemetria do escala global.](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)

## <a name="enable-streaming-of-the-activity-log"></a>Ativar a transmissão de registo de atividade
Pode ativar a transmissão do registo de atividade de através de programação ou através do portal. Qualquer forma, escolha um espaço de nomes do serviço Bus e uma política de acesso partilhado para esse espaço de nomes e um concentrador de evento é criado nesse espaço de nomes quando ocorre o primeiro novo evento de registo de atividade. Se não tiver um espaço de nomes do serviço Bus, primeiro terá de criar um. Se tiver anteriormente transmitido em sequência eventos de registo de atividade espaço de nomes de Bus este serviço, o concentrador de evento que foi criado anteriormente irá ser reutilizado. A política de acesso partilhado define as permissões que o dispositivo de transmissão. Hoje em dia, transmissão a um evento concentradores necessita de permissões **Gerir**, **Ler**e **Enviar** . Pode criar ou modificar as políticas de acesso do espaço de nomes do Bus serviço partilhadas no portal do clássico no separador "Configurar" para o seu espaço de nomes do serviço Bus. Para atualizar o perfil de registo de registo de atividade para incluir a transmissão, o utilizador a fazer a alteração tem de ter a permissão de ListKey no serviço Bus autorização regra.

### <a name="via-azure-portal"></a>Através do portal do Azure 
1. Navegue para a pá de **Registo de atividade** através do menu no lado esquerdo do portal.

    ![Navegue para o registo de atividade no portal](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. Clique no botão **Exportar** no topo da pá.

    ![Botão Exportar no portal](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. No pá que aparece, pode selecionar as regiões para o qual que gostaria de eventos da cadeia e o espaço de nomes de Bus do serviço no qual gostaria um concentrador de evento seja criado por transmissão estes eventos.

    ![Exportar pá de registo de atividade](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. Clique em **Guardar** para guardar estas definições. As definições estão aplicada imediatamente à sua subscrição.


### <a name="via-powershell-cmdlets"></a>Via os cmdlets do PowerShell
Se já existe um perfil de registo, primeiro tem de remover esse perfil.

1. Utilizar `Get-AzureRmLogProfile` identificar se existe um perfil de registo
2. Em caso afirmativo, utilize `Remove-AzureRmLogProfile` para removê-la.
3. Utilizar `Set-AzureRmLogProfile` para criar um perfil:

```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

O ID da regra Bus serviço é uma cadeia com neste formato: {serviço ID do recurso bus} /authorizationrules/ {chave name}, por exemplo 

### <a name="via-azure-cli"></a>Através do clip Azure
Se já existe um perfil de registo, primeiro tem de remover esse perfil.

1. Utilizar `azure insights logprofile list` identificar se existe um perfil de registo
2. Em caso afirmativo, utilize `azure insights logprofile delete` para removê-la.
3. Utilizar `azure insights logprofile add` para criar um perfil:

```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

O ID da regra Bus serviço é uma cadeia com neste formato: `{service bus resource ID}/authorizationrules/{key name}`.
 
## <a name="how-do-i-consume-the-log-data-from-event-hubs"></a>Como posso consumir os dados do registo de evento concentradores?
[O esquema para o registo de atividade está disponível aqui](./monitoring-overview-activity-logs.md). Cada evento está numa matriz de blobs JSON denominados "registos".

## <a name="next-steps"></a>Próximos passos
- [Arquivar o registo de atividade para uma conta de armazenamento](./monitoring-archive-activity-log.md)
- [Leia a descrição geral do registo de atividade Azure](./monitoring-overview-activity-logs.md)
- [Configurar um alerta com base num evento de registo de atividade](./insights-auditlog-to-webhook-email.md)
