<properties
    pageTitle="Descrição geral dos registos de diagnóstico Azure | Microsoft Azure"
    description="Saiba o que são registos de diagnóstico do Azure e como utilizá-los para compreender os eventos que ocorram dentro de um recurso Azure."
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
    ms.date="10/12/2016"
    ms.author="johnkem; magoedte"/>

# <a name="overview-of-azure-diagnostic-logs"></a>Descrição geral dos registos de diagnóstico Azure
**Registos de diagnóstico do Azure** são registos emitidos por um recurso que fornece dados avançados, frequentes acerca da operação desse recurso. O conteúdo destes registos varia consoante o tipo de recurso (por exemplo, registos de sistema de eventos do Windows são uma categoria de registo de diagnóstico para VMs e blob, tabela e os registos de fila são categorias de registos de diagnóstico para contas de armazenamento) e diferir o [registo de atividade (anteriormente conhecido como registo de auditoria ou registo operacional)](monitoring-overview-activity-logs.md), que fornece visão as operações que foram efetuadas no recursos na sua subscrição. Nem todos os recursos de suporte o novo tipo de registos de diagnóstico descritas aqui. A lista de serviços suportadas abaixo mostra quais os tipos de recursos de suporte a novos registos de diagnóstico.

![Posicionamento lógico de registos de diagnóstico](./media/monitoring-overview-of-diagnostic-logs/logical-placement-chart.png)

## <a name="what-you-can-do-with-diagnostic-logs"></a>O que pode fazer com registos de diagnóstico
Aqui estão algumas das coisas que pode fazer com registos de diagnóstico:

- Guardá-los a uma **Conta de armazenamento** para o controlo de auditoria ou manual. Pode especificar o tempo de retenção (em dias) utilizando as **Definições de diagnóstico**.
- A [sequência-los a **Concentradores de evento** ](monitoring-stream-diagnostic-logs-to-event-hubs.md) para ingestão por um serviço de terceiros ou solução de análise personalizada como obter.
- Analisá-los com [OMS registo Analytics](../log-analytics/log-analytics-azure-storage-json.md)

## <a name="diagnostic-settings"></a>Definições de diagnóstico
Registos de diagnóstico para recursos de que não sejam cluster são configurados utilizando definições de diagnóstico. **Definições de diagnóstico** para um controlo de recurso:

- Onde os registos de diagnóstico são enviados (conta de armazenamento, concentradores de evento, e/ou a análise de registo OMS).
- Quais as categorias registo são enviadas.
- Quanto tempo devo retida cada categoria de registo numa conta de armazenamento – uma retenção de zero dias significa que são mantidos uma eternidade registos. Caso contrário, este valor pode variar entre 1 e 2147483647. Se as políticas de retenção são definidas mas armazenar registos de início de uma conta de armazenamento é desativada (por exemplo se opções concentradores de evento ou OMS estiverem selecionadas apenas), as políticas de retenção não tem qualquer efeito.

Estas definições são configuradas facilmente através de pá de diagnóstico para um recurso no portal do Azure, através do PowerShell do Azure e clip comandos ou através do [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931943.aspx).

> [AZURE.WARNING] Registos de diagnóstico e métricas para recursos de cluster (por exemplo, VMs ou serviço ferro) utilizam [um espaço em separado configuração e seleção de resultados](../azure-diagnostics.md).

## <a name="how-to-enable-collection-of-diagnostic-logs"></a>Como ativar o conjunto de registos de diagnóstico
Colecção de registos de diagnóstico pode ser ativada como parte da criação de um recurso ou de um recurso é criado através da pá do recurso no Portal. Também pode ativar registos de diagnóstico em qualquer ponto utilizando os comandos do Azure PowerShell ou clip ou utilizar a API do Azure Monitor restantes.

> [AZURE.TIP] Estas instruções não poderão aplicar diretamente para cada recurso. Consulte as ligações de esquema na parte inferior desta página para compreender os passos especiais que podem aplicar a determinados tipos de recursos.

[Este artigo mostra como pode utilizar um modelo de recursos para ativar as definições de diagnóstico ao criar um recurso](./monitoring-enable-diagnostic-logs-using-template.md)

### <a name="enable-diagnostic-logs-in-the-portal"></a>Ativar registos de diagnóstico no portal
Pode ativar a registos de diagnóstico no portal do Azure quando criar tipos de cluster de recursos ao ativar a extensão do Windows ou Linux Azure diagnóstico:

1.  Ir para **Novo** e selecione o recurso que lhe interessam.
2.  Depois de configurar as definições básicas e selecionar um tamanho, no pá **Definições** , em **monitorização**, selecione **ativado** e selecione uma conta de armazenamento onde pretende armazenar os registos de diagnóstico. São cobradas taxas de dados normal para armazenamento e transações quando envia diagnósticos para uma conta de armazenamento.

    ![Ativar registos de diagnóstico durante a criação do recurso](./media/monitoring-overview-of-diagnostic-logs/enable-portal-new.png)
3.  Clique em **OK** e criar o recurso.

Para obter recursos de que não sejam cluster, pode ativar registos de diagnóstico no portal do Azure depois de um recurso foi criado ao efetuar o seguinte:

1.  Aceda ao pá para o recurso e abra o pá de **diagnóstico** .
2.  Clique **no** e selecione uma conta de armazenamento e/ou evento concentrador.

    ![Ativar registos de diagnóstico após a criação de recursos](./media/monitoring-overview-of-diagnostic-logs/enable-portal-existing.png)
3.  Em **registos**, selecione as **Categorias de registo** que pretende recolher ou transmitir em fluxo.
4.  Clique em **Guardar**.

### <a name="enable-diagnostic-logs-via-powershell"></a>Ativar registos de diagnóstico através do PowerShell
Para ativar registos de diagnóstico via os Cmdlets do PowerShell Azure, utilize os comandos seguintes.

Para permitir o armazenamento dos registos de diagnóstico numa conta de armazenamento, utilize este comando:

    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true

O ID de conta de armazenamento é o id do recurso para a conta de armazenamento à qual pretende enviar os registos.

Para ativar a transmissão de registos de diagnóstico para um concentrador de evento, utilize este comando:

    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true

O ID da regra Bus serviço é uma cadeia com neste formato: `{service bus resource ID}/authorizationrules/{key name}`.

Para permitir o envio de registos de diagnóstico para uma área de trabalho de análise de registo, utilize este comando:

    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [log analytics workspace id] -Enabled $true

> [AZURE.NOTE] O parâmetro WorkspaceId não está disponível na versão de Outubro. Ficará disponível na versão de Novembro.

Pode obter o seu ID de área de trabalho de análise de registo no portal do Azure.

Pode combinar estes parâmetros para ativar a várias opções de saída.

### <a name="enable-diagnostic-logs-via-cli"></a>Ativar registos de diagnóstico através do Clip
Para ativar registos de diagnóstico através do clip o Azure, utilize os comandos seguintes:

Para permitir o armazenamento dos registos de diagnóstico numa conta de armazenamento, utilize este comando:

    azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true

O ID de conta de armazenamento é o id do recurso para a conta de armazenamento à qual pretende enviar os registos.

Para ativar a transmissão de registos de diagnóstico para um concentrador de evento, utilize este comando:

    azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true

O ID da regra Bus serviço é uma cadeia com neste formato: `{service bus resource ID}/authorizationrules/{key name}`.

Para permitir o envio de registos de diagnóstico para uma área de trabalho de análise de registo, utilize este comando:

    azure insights diagnostic set --resourceId <resourceId> --workspaceId <workspaceId> --enabled true

> [AZURE.NOTE] O parâmetro workspaceId não está disponível na versão de Outubro. Ficará disponível na versão de Novembro.

Pode obter o seu ID de área de trabalho de análise de registo no portal do Azure.

Pode combinar estes parâmetros para ativar a várias opções de saída.

### <a name="enable-diagnostic-logs-via-rest-api"></a>Ativar registos de diagnóstico através do REST API
Para alterar as definições de diagnóstico utilizando o Azure Monitor REST API, consulte o artigo [neste documento](https://msdn.microsoft.com/library/azure/dn931931.aspx).

## <a name="manage-diagnostic-settings-in-the-portal"></a>Gerir definições de diagnóstico no portal

Para certificar-se de que todos os recursos estão corretamente configurados com definições de diagnóstico, pode navegar para o pá **monitorização** no portal do e abra o pá **Registos de diagnóstico** .

![Diagnóstico pá de registos no portal](./media/monitoring-overview-of-diagnostic-logs/manage-portal-nav.png)

Poderá ter de clique em "Mais serviços" para encontrar o pá de monitorização.

Neste pá, pode visualizar e filtrar todos os recursos que suportam registos de diagnóstico para ver se tiverem diagnósticos ativados e qual a conta de armazenamento, concentrador de evento, e/ou área de trabalho de análise de registo esses registos estão a fluir para.

![Resultados de pá de registos de diagnóstico no portal](./media/monitoring-overview-of-diagnostic-logs/manage-portal-blade.png)

Clicar num recurso irá mostrar todos os registos que tenham sido armazenados na conta de armazenamento e dar-lhe a opção para ativar ou modificar as definições de diagnóstico. Clique no ícone Transferir para transferir os registos para um determinado período de tempo.

![Diagnóstico registos pá um recurso](./media/monitoring-overview-of-diagnostic-logs/manage-portal-logs.png)

> [AZURE.NOTE] Registos de diagnóstico apenas serão apresentada esta vista e estar disponíveis para transferência se tiver configurado definições de diagnóstico para guardá-los a uma conta de armazenamento.

Clicar na ligação para **As definições de diagnóstico** moldados pá as definições de diagnóstico, onde pode ativar, desativar ou modificar as definições de diagnóstico para o recurso selecionado.

## <a name="supported-services-and-schema-for-diagnostic-logs"></a>Serviços suportados e o esquema para registos de diagnóstico
O esquema para registos de diagnóstico varia consoante a categoria de recursos e inicie sessão. Seguem-se a serviços suportados e os respetivos esquema.

| Serviço                       | Esquema e documentos                                                                                                   |
|-------------------------------|-----------------------------------------------------------------------------------------------------------------|
|    Balanceador de carga de software     |    [Análise de registo de Balanceador de carga Azure (pré-visualização)](../load-balancer/load-balancer-monitor-log.md)             |
|    Grupos de segurança de rede    |    [Registo a análise de grupos de segurança de rede (NSGs)](../virtual-network/virtual-network-nsg-manage-log.md)     |
|    Gateways de aplicação       |    [Registo de diagnóstico para o Gateway de aplicação](../application-gateway/application-gateway-diagnostics.md)     |
|    Cofre chave                  |    [Registo do cofre chave Azure](../key-vault/key-vault-logging.md)                                                 |
|    Pesquisa Azure               |    [Ativar e utilizar a análise de tráfego de pesquisa](../search/search-traffic-analytics.md)                         |
|    Arquivo de dados de Lake            |    [Aceder a registos de diagnóstico para arquivo de Lake de dados do Azure](../data-lake-store/data-lake-store-diagnostic-logs.md) |
|    Lake a análise de dados        |    [Aceder a registos de diagnóstico para análise do Azure dados Lake](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
|    Aplicações de lógica                 |    Nenhum esquema disponível.                                                                                         |
|    Azure Batch                |    [Registo de diagnóstico lote Azure](../batch/batch-diagnostics.md)                                              |
|    Automatização Azure           |    [Análise de registo de automatização do Azure](../automation/automation-manage-send-joblogs-log-analytics.md)          |
|    Concentrador de evento                  |    Nenhum esquema disponível.                                                                                         |
|    Serviço Bus                |    Nenhum esquema disponível.                                                                                         |
|    Análise da cadeia           |    Nenhum esquema disponível.                                                                                         |

## <a name="supported-log-categories-per-resource-type"></a>Suportado registo categorias por tipo de recurso

|Tipo de recurso|Categoria|Nome a apresentar categoria|
|---|---|---|
|Microsoft.Automation/automationAccounts|JobLogs|Registos de tarefa|
|Microsoft.Automation/automationAccounts|JobStreams|Fluxos de trabalho|
|Microsoft.Batch/batchAccounts|ServiceLog|Registos de serviço|
|Microsoft.DataLakeAnalytics/accounts|Auditoria|Registos de auditoria|
|Microsoft.DataLakeAnalytics/accounts|Pedidos de|Pedido de registos|
|Microsoft.DataLakeStore/accounts|Auditoria|Registos de auditoria|
|Microsoft.DataLakeStore/accounts|Pedidos de|Pedido de registos|
|Microsoft.EventHub/namespaces|ArchiveLogs|Arquivo de registos|
|Microsoft.EventHub/namespaces|OperationalLogs|Registos operacionais|
|Microsoft.KeyVault/vaults|AuditEvent|Registos de auditoria|
|Microsoft.Logic/workflows|WorkflowRuntime|Eventos de diagnóstico de tempo de execução do fluxo de trabalho|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Eventos de grupo de segurança de rede|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Contador de regra de grupo de segurança de rede|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupFlowEvent|Eventos de fluxo de regra de grupo de segurança de rede|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Eventos de alerta do Balanceador de carga|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Estado do Estado de funcionamento sonda Balanceador de carga|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Registo de acesso de Gateway de aplicação|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Registo de desempenho do Gateway de aplicação|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Registo da aplicação de Gateway Firewall|
|Microsoft.Search/searchServices|OperationLogs|Registos de operação|
|Microsoft.ServerManagement/nodes|RequestLogs|Pedido de registos|
|Microsoft.ServiceBus/namespaces|OperationalLogs|Registos operacionais|
|Microsoft.StreamAnalytics/streamingjobs|Execução|Execução|
|Microsoft.StreamAnalytics/streamingjobs|Criação de conteúdos|Criação de conteúdos|

## <a name="next-steps"></a>Próximos passos
- [Registos de diagnóstico da cadeia a **concentradores de evento**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
- [Alterar as definições de diagnóstico utilizando o Monitor REST API do Azure](https://msdn.microsoft.com/library/azure/dn931931.aspx)
- [Analisar os registos com a análise de registo OMS](../log-analytics/log-analytics-azure-storage-json.md)
