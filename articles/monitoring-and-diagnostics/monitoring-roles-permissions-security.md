<properties
    pageTitle="Introdução ao funções, permissões e segurança com o Azure Monitor | Microsoft Azure"
    description="Saiba como utilizar funções incorporadas e as permissões do Azure Monitor para restringir o acesso a monitorização de recursos."
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
    ms.date="09/26/2016"
    ms.author="johnkem"/>

# <a name="get-started-with-roles-permissions-and-security-with-azure-monitor"></a>Introdução ao funções, permissões e segurança com o Monitor do Azure

Tem muitas equipas de regular estritamente o acesso ao monitorização de dados e definições. Por exemplo, se tiver os membros de equipa que trabalham exclusivamente sobre como monitorizar (engenheiros de suporte, devops engineers) ou se utilizar um fornecedor de serviços geridos, poderá querer conceder-lhes acesso a dados de monitorização apenas enquanto restringir a sua capacidade de criar, modificar ou eliminar recursos. Este artigo mostra como aplicar uma função de monitorização RBAC incorporada a um utilizador no Azure ou construir a sua própria função personalizada para um utilizador que precise de permissões de monitorização limitadas rapidamente. Em seguida, fala sobre considerações de segurança para os recursos relacionados com o Monitor do Azure e como pode limitar o acesso aos dados que neles contidas.

## <a name="built-in-monitoring-roles"></a>Funções incorporadas de monitorização

As funções incorporadas do Azure Monitor são concebidas para o ajudar a limitar o acesso aos recursos numa subscrição permitindo ainda responsáveis infraestrutura para obter e configurar os dados que necessitam de monitorização. O Monitor Azure fornece duas funções out of box: A monitorização de leitor e um contribuinte monitorização.

### <a name="monitoring-reader"></a>Leitor de monitorização

Pessoas que foi atribuídas a função de monitorização leitor podem ver todos os dados de monitorização numa subscrição mas não é possível modificar qualquer recurso ou editar qualquer definições relacionadas com a monitorização de recursos. Esta função é adequada para utilizadores numa organização, tais como suporte ou operações engenheiros, que precisam de ser possível:

- Ver monitorização dashboards no portal e criar os seus próprios dashboards monitorização privados.
- Consulta para utilizar o [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931930.aspx), [os cmdlets do PowerShell](insights-powershell-samples.md)ou [em diferentes plataformas clip](insights-cli-samples.md)de métricas.
- Consulta o registo de atividade utilizando o portal, Azure Monitor REST API, os cmdlets do PowerShell ou clip em diferentes plataformas.
- Ver as [definições de diagnóstico](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings) para um recurso.
- Ver o [perfil de registo](monitoring-overview-activity-logs.md#export-the-activity-log-with-log-profiles) para uma subscrição.
- Ver definições de autoscale.
- Atividade de alerta de vista e definições.
- Aceder a dados de informações da aplicação e visualizar dados em AI Analytics.
- Procurar dados de área de trabalho de análise de registo (OMS) incluindo a utilização dados para a área de trabalho.
- Veja os grupos de gestão de análise de registo (OMS).
- Obter o esquema de pesquisa de análise de registo (OMS).
- Listar os pacotes de informações da empresa de análise de registo (OMS).
- Obter e executar a análise de registo (OMS) procuras guardadas.
- Obter a configuração de armazenamento de análise de registo (OMS).

> [AZURE.NOTE] Esta função não dá acesso de leitura para os dados do registo que foi transmitido em sequência a um concentrador de evento ou armazenados numa conta de armazenamento. [Consulte abaixo](#security-considerations-for-monitoring-data) para obter informações sobre como configurar o acesso a estes recursos.

### <a name="monitoring-contributor"></a>Monitorização de contribuinte

As pessoas foi atribuídas a função Contribuinte monitorização podem ver todos os dados de monitorização numa subscrição e criar ou modificar as definições de monitorização, mas não é possível modificar quaisquer outros recursos. Esta função é um vasto conjunto de função de leitor de monitorização e é adequada para os membros de equipa monitorização ou fornecedores de serviço gerido que, para além das permissões acima, também tem de ser capaz de uma organização:

- Publica dashboards monitorização como um dashboard partilhado.
- Configurar [definições de diagnóstico](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings) para um resource.*
- Definir o [perfil de registo](monitoring-overview-activity-logs.md#export-the-activity-log-with-log-profiles) para um subscription.*
- Definir alerta atividade e definições.
- Crie informações de aplicação web testes e componentes.
- Teclas de área de trabalho partilhada de análise de registo (OMS) da lista.
- Ativar ou desativar o registo Analytics (OMS) pacotes de informações da empresa.
- Criar e eliminar e executar a análise de registo (OMS) procuras guardadas.
- Criar e eliminar a configuração de armazenamento de análise de registo (OMS).

* utilizador separadamente também tem de conceder permissão ListKeys no recurso de destino (armazenamento conta ou evento concentrador espaço de nomes) para definir uma definição de diagnóstico ou perfil de registo.

> [AZURE.NOTE] Esta função não dá acesso de leitura para os dados do registo que foi transmitido em sequência a um concentrador de evento ou armazenados numa conta de armazenamento. [Consulte abaixo](#security-considerations-for-monitoring-data) para obter informações sobre como configurar o acesso a estes recursos.

## <a name="monitoring-permissions-and-custom-rbac-roles"></a>Monitorização de permissões e funções RBAC personalizadas
Se as funções incorporadas acima não cumprem as necessidades da sua equipa exatas, pode [criar uma função RBAC personalizada](../active-directory/role-based-access-control-custom-roles.md) com permissões mais granulares. Seguem-se as operações de Azure Monitor RBAC comuns com as respectivas descrições.

| Operação                                                   | Descrição                                                                                                                                               |
|-------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| Eliminar Microsoft.Insights/AlertRules/[Read, escrita]         | Regras de alertas de leitura/escrita/eliminar.                                                                                                                            |
| Microsoft.Insights/AlertRules/Incidents/Read                | Lista incidentes (histórico da regra alerta a ser acionou) para regras de alertas. Só se aplica ao portal.                                              |
| Eliminar Microsoft.Insights/AutoscaleSettings/[Read, escrita]  | Definições de leitura/escrita/eliminar autoscale.                                                                                                                     |
| Eliminar Microsoft.Insights/DiagnosticSettings/[Read, escrita] | Definições de diagnóstico de leitura/escrita/eliminar.                                                                                                                    |
| Microsoft.Insights/eventtypes/digestevents/Read             | Esta permissão é necessário para utilizadores que precisam de aceder aos registos de atividade através do portal.                                                                   |
| Microsoft.Insights/eventtypes/values/Read                   | Lista eventos de registo de atividade (eventos de gestão) numa subscrição. Esta permissão é aplicável a programação e portal acesso ao registo de atividade. |
| Microsoft.Insights/LogDefinitions/Read                      | Esta permissão é necessário para utilizadores que precisam de aceder aos registos de atividade através do portal.                                                                   |
| Microsoft.Insights/MetricDefinitions/Read                   | Leia métricas definições (lista de tipos de métricas disponíveis para um recurso).                                                                                  |
| Microsoft.Insights/Metrics/Read                             | Leia métricas de um recurso.                                                                                                                              |

> [AZURE.NOTE] Aceder às alertas, definições de diagnóstico e métricas para um recurso requer que o utilizador tem acesso de leitura para o tipo de recurso e o âmbito do recurso. Criar ("escrever") um perfil de definição ou registo de diagnóstico que arquiva para uma conta de armazenamento ou sequências a concentradores evento requer o utilizador também ter a permissão de ListKeys o recurso de destino.

Por exemplo, utilizando a tabela acima, pode criar uma função RBAC personalizada para um "leitor do registo de atividade" da seguinte forma:

```powershell
$role = Get-AzureRmRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Activity Log Reader"
$role.Description = "Can view activity logs."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Insights/eventtypes/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription")
New-AzureRmRoleDefinition -Role $role 
```

## <a name="security-considerations-for-monitoring-data"></a>Considerações de segurança para monitorizar os dados
Monitorizar os dados — especialmente os ficheiros de registo — pode conter informações confidenciais, tais como nomes de utilizador ou endereços IP. Monitorizar os dados a partir do Azure é recebida três formas básicas:
1. O registo de atividade que descreve todas as ações de plano de controlo na sua subscrição do Azure.
2. Registos de diagnóstico, que são registos emitidos por um recurso.
3. Métricas, que são emitidas por recursos.

Três estes tipos de dados podem ser armazenados numa conta armazenamento ou transmitidos para um concentrador de evento, sendo que ambos estão recursos Azure uso geral. Uma vez que estes são recursos uso geral, criar, eliminar e acedê-los são uma operação privilegiada normalmente reservada para um administrador. Sugerimos que utilize as seguintes práticas para relacionadas com a monitorização de recursos para evitar utilização indevida:

- Utilize uma conta de armazenamento único e dedicado para monitorizar os dados. Se precisar de separar os dados de monitorização para várias contas de armazenamento, nunca partilhar a utilização de uma conta de armazenamento entre monitorização e dados que não sejam monitorização, como este podem dar inadvertidamente aqueles que só precisam de aceder para monitorizar os dados (por ex. um SIEM de terceiros) acesso a que não sejam monitorizar os dados.
- Utilize um único e dedicado Bus de serviço ou evento concentrador espaço de nomes em todas as definições de diagnóstico para pela mesma razão que acima.
- Limitar o acesso a contas de armazenamento relacionadas com a monitorização ou concentradores evento mantendo-los um grupo de recursos separadas e, [Utilize o âmbito](../active-directory/role-based-access-control-what-is.md#basics-of-access-management-in-azure) no seu funções de monitorização para limitar o acesso a esse grupo de recursos.
- Nunca conceda a permissão de ListKeys para contas do armazenamento ou concentradores do evento no âmbito de subscrição quando um utilizador precisa apenas de acesso a dados de monitorização. Em vez disso, dê estas permissões ao utilizador um recurso ou grupo de recursos (se tiver um grupo de recursos de monitorização dedicado) âmbito.

### <a name="limiting-access-to-monitoring-related-storage-accounts"></a>Limitação de acesso às contas de relacionadas com a monitorização de armazenamento
Quando um utilizador ou aplicação necessita de acesso a dados numa conta de armazenamento de monitorização, deverá [Gerar SA uma conta](https://msdn.microsoft.com/library/azure/mt584140.aspx) na conta de armazenamento que contém os dados de monitorização com acesso só de leitura do nível de serviço ao armazenamento blob. No PowerShell, isto poderá ser semelhante:

```powershell
$context = New-AzureStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzureStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

Em seguida, pode dar o token à entidade que necessita de ler a partir do armazenamento conta e pode da lista e ler a partir de todos os blobs nessa conta de armazenamento.

Em alternativa, se precisar de controlar esta permissão com RBAC, pode conceder essa entidade a permissão de Microsoft.Storage/storageAccounts/listkeys/action nessa conta de armazenamento específico. Isto é necessário para utilizadores que precisam de ser possível definir uma definição de diagnóstico ou iniciar sessão para arquivar o perfil a uma conta de armazenamento. Por exemplo, poderia criar a seguinte função RBAC personalizada para um utilizador ou a aplicação que só necessita de ler a partir de uma conta de armazenamento:

```powershell
$role = Get-AzureRmRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Monitoring Storage Account Reader"
$role.Description = "Can get the storage account keys for a monitoring storage account."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/storageAccounts/listkeys/action")
$role.Actions.Add("Microsoft.Storage/storageAccounts/Read")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/myMonitoringStorageAccount")
New-AzureRmRoleDefinition -Role $role 
```

> [AZURE.WARNING] A permissão de ListKeys permite ao utilizador para listam as teclas de conta de armazenamento principais e secundários. Estas teclas conceder ao utilizador assinado todas as permissões (ler, escrever, criar blobs, eliminar blobs, etc.) em todos os assinado serviços (blob, fila de espera, tabela, ficheiro) nessa conta de armazenamento. Recomendamos que utilize um SA conta descrito acima, sempre que possível.

### <a name="limiting-access-to-monitoring-related-event-hubs"></a>Limitação de acesso a concentradores relacionadas com a monitorização de evento
Pode ser seguido um padrão semelhante com concentradores de evento, mas primeiro tem de criar uma regra de autorização de ouvir dedicada. Se pretende conceder acesso a uma aplicação que necessita de apenas para ouvir concentradores relacionadas com a monitorização de evento, efetue o seguinte procedimento:

1. Crie uma política de acesso partilhado no hub(s) evento que foram criadas para transmissão de dados de monitorização com apenas ouvir afirmações. Isto pode ser feito no portal. Por exemplo, poderá chamar-"monitoringReadOnly." Se possível, irá querer atribuir essa tecla diretamente para o consumidor e ignorar o próximo passo.
2. Se necessitar do consumidor consigam obter chave ad-hoc, conceda a ação ListKeys concentrador evento ao utilizador. Isto também é necessário para utilizadores que precisam de poderá definir uma definição de diagnóstico ou inicie sessão perfil da cadeia a concentradores do evento. Por exemplo, poderá criar uma regra RBAC:

   ```powershell
   $role = Get-AzureRmRoleDefinition "Reader"
   $role.Id = $null
   $role.Name = "Monitoring Event Hub Listener"
   $role.Description = "Can get the key to listen to an event hub streaming monitoring data."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/authorizationrules/listkeys/action")
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/Read")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.ServiceBus/namespaces/mySBNameSpace")
   New-AzureRmRoleDefinition -Role $role 
   ```


## <a name="next-steps"></a>Próximos passos
- [Leia sobre RBAC e permissões no Gestor de recursos](../active-directory/role-based-access-control-what-is.md)
- [Leia a descrição geral da monitorização no Azure](monitoring-overview.md)
