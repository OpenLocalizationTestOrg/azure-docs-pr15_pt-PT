<properties
    pageTitle="RBAC: Funções incorporadas | Microsoft Azure"
    description="Este tópico descreve incorporado em funções para controlo de acesso baseado em funções (RBCA)."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/25/2016"
    ms.author="kgremban"/>

#<a name="rbac-built-in-roles"></a>RBAC: Funções incorporadas

Azure baseado em funções acesso controlo RBCA () é fornecido com as seguintes funções incorporadas que podem ser atribuídas a utilizadores, grupos e serviços. Não é possível modificar as definições das funções incorporadas. No entanto, pode criar [funções de personalizada no Azure RBAC](role-based-access-control-custom-roles.md) para se ajustar as necessidades específicas da sua organização.

## <a name="roles-in-azure"></a>Funções no Azure

A tabela seguinte fornece breves descrições das funções incorporadas. Clique no nome da função para ver a lista detalhada das **ações** e **notactions** para a função. A propriedade de **ações** Especifica as ações permitidas no Azure recursos. Cadeias de ação, podem utilizar carateres universais. A propriedade **notactions** Especifica as ações que são excluídas das ações permitidas.

>[AZURE.NOTE] As definições de funções Azure constantemente estão a evoluir. Este artigo é mantido como atualizado possível, mas pode sempre localizá as definições de funções mais recentes no Azure PowerShell. Utilizar os cmdlets `(get-azurermroledefinition "<role name>").actions` ou `(get-azurermroledefinition "<role name>").notactions` , conforme aplicável.

| Nome de função | Descrição |
| --------- | ----------- |
| [Contribuinte de serviço de gestão de API](#api-management-service-contributor) | Pode gerir os serviços de gestão de API |
| [Aplicação informações componente contribuinte](#application-insights-component-contributor) | Pode gerir componentes de informações de aplicação |
| [Operador de automatização](#automation-operator) | Conseguir iniciar, parar, suspender e retomar tarefas |
| [BizTalk contribuinte](#biztalk-contributor) | Pode gerir os serviços de BizTalk |
| [ClearDB MySQL DB contribuinte](#cleardb-mysql-db-contributor) | Pode gerir bases de dados do ClearDB MySQL |
| [Contribuinte](#contributor) | Pode gerir tudo exceto acesso. |
| [Dados fábrica contribuinte](#data-factory-contributor) | Pode criar e gerir fábricas de dados e recursos de subordinados nelas. |
| [DevTest Labs utilizador](#devtest-labs-user) | Pode ver tudo e ligar, início, reinicie e encerramento máquinas virtuais |
| [Zona de DNS de contribuinte](#dns-zone-contributor) | Pode gerir zonas de DNS e registos |
| [Conta DocumentDB contribuinte](#documentdb-account-contributor) | Pode gerir as contas de DocumentDB |
| [Sistemas inteligente conta contribuinte](#intelligent-systems-account-contributor) | Pode gerir as contas de sistemas inteligente |
| [Rede de contribuinte](#network-contributor) | Pode gerir todos os recursos de rede |
| [Novo contribuinte de conta APM Relic](#new-relic-apm-account-contributor) | Pode gerir contas de gestão de desempenho de aplicações do novo Relic e de aplicações |
| [Proprietário](#owner) | Pode gerir tudo, incluindo o access |
| [Leitor](#reader) | Pode ver tudo, mas não pode efetuar alterações |
| [Redis contribuinte Cache](#redis-cache-contributor) | Pode gerir Redis caches |
| [Contribuinte de coleções de tarefa de programador](#scheduler-job-collections-contributor) | Pode gerir coleções de tarefa de programador |
| [Contribuinte de serviço de pesquisa](#search-service-contributor) | Pode gerir os serviços de pesquisa |
| [Gestor de segurança](#security-manager) | Pode gerir componentes de segurança, políticas de segurança e máquinas virtuais |
| [SQL DB contribuinte](#sql-db-contributor) | Pode gerir bases de dados SQL, mas não os respetivos políticas relacionadas com segurança |
| [Gestor de segurança do SQL](#sql-security-manager) | Pode gerir as políticas que foram relacionado com a segurança dos servidores SQL e bases de dados |
| [SQL Server contribuinte](#sql-server-contributor) | Pode gerir os servidores de SQL e bases de dados, mas não os respetivos políticas relacionadas com segurança |
| [Contribuinte de conta de armazenamento clássico](#classic-storage-account-contributor) | Pode gerir as contas de armazenamento clássico |
| [Contribuinte de conta de armazenamento](#storage-account-contributor) | Pode gerir as contas de armazenamento |
| [Administrador de acesso de utilizadores](#user-access-administrator) | Pode gerir o acesso do utilizador para recursos Azure |
| [Máquina Virtual clássico de contribuinte](#classic-virtual-machine-contributor) | Pode gerir máquinas virtuais clássicas, mas não a virtual armazenamento ou rede conta à qual estão ligados |
| [Contribuinte máquina virtual](#virtual-machine-contributor) | Pode gerir máquinas virtuais, mas não a virtual armazenamento ou rede conta à qual estão ligados |
| [Rede clássico de contribuinte](#classic-network-contributor) | Pode gerir IPs reservadas e redes virtuais clássicos |
| [Web plano de contribuinte](#web-plan-contributor) | Pode gerir planos web |
| [Web site contribuinte](#website-contributor) | Pode gerir Web sites, mas não os planos de web à qual estão ligados |

## <a name="role-permissions"></a>Permissões de função
As tabelas seguintes descrevem as permissões específicas atribuídas a cada função. Isto pode incluir **ações**, que atribuir permissões e **NotActions**, restringi-los.

### <a name="api-management-service-contributor"></a>Contribuinte de serviço de gestão de API
Pode gerir os serviços de gestão de API

| **Ações** | |
| ------- | ------ |
| Microsoft.ApiManagement/Service/* | Criar e gerir serviços de gestão de API |
| Microsoft.Authorization/*/read | Autorização de leitura |
| Microsoft.Insights/alertRules/* | Criar e gerir regras de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Funções de leitura e atribuições de funções |
| Microsoft.Support/* | Criar e gerir pedidos de suporte |

### <a name="application-insights-component-contributor"></a>Aplicação informações componente contribuinte
Pode gerir componentes de informações de aplicação

| **Ações** | |
| ------- | ------ |
| Microsoft.Authorization/*/read | Funções de leitura e atribuições de funções |
| Microsoft.Insights/alertRules/* | Criar e gerir regras de alertas |
| Microsoft.Insights/components/* | Criar e gerir componentes de informações |
| Microsoft.Insights/webtests/* | Criar e gerir testes web |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |
| Microsoft.Support/* | Criar e gerir pedidos de suporte |

### <a name="automation-operator"></a>Operador de automatização
Conseguir iniciar, parar, suspender e retomar tarefas

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Funções de leitura e atribuições de funções |
| Microsoft.Automation/automationAccounts/jobs/read | Ler automatização trabalhos de conta |
| Microsoft.Automation/automationAccounts/jobs/resume/action | Retomar uma tarefa de conta de automatização |
| Microsoft.Automation/automationAccounts/jobs/stop/action | Parar uma tarefa de conta de automatização |
| Microsoft.Automation/automationAccounts/jobs/streams/read | Ler automatização fluxos de trabalho de conta |
| Microsoft.Automation/automationAccounts/jobs/suspend/action | Suspender uma tarefa de conta de automatização |
| Microsoft.Automation/automationAccounts/jobs/write | Escrever automatização trabalhos de conta |
| Microsoft.Automation/automationAccounts/jobSchedules/read | Ler uma agenda de trabalho de conta de automatização |
| Microsoft.Automation/automationAccounts/jobSchedules/write | Ler uma agenda de trabalho de conta de automatização |
| Microsoft.Automation/automationAccounts/read | Ler contas de automatização |
| Microsoft.Automation/automationAccounts/runbooks/read | Ler runbooks de automatização |
| Microsoft.Automation/automationAccounts/schedules/read | Ler automatização agendas de conta |
| Microsoft.Automation/automationAccounts/schedules/write | Escrever automatização agendas de conta |
| Microsoft.Insights/components/* | Criar e gerir componentes de informações |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |
| Microsoft.Support/* | Criar e gerir pedidos de suporte |

### <a name="biztalk-contributor"></a>BizTalk contribuinte
Pode gerir os serviços de BizTalk

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Funções de leitura e atribuições de funções |
| Microsoft.BizTalkServices/BizTalk/* | Criar e gerir BizTalk serviços |
| Microsoft.Insights/alertRules/* | Criar e gerir regras de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |
| Microsoft.Support/* | Criar e gerir pedidos de suporte |

### <a name="cleardb-mysql-db-contributor"></a>ClearDB MySQL DB contribuinte
Pode gerir bases de dados do ClearDB MySQL

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Funções de leitura e atribuições de funções |
| Microsoft.Insights/alertRules/* | Criar e gerir regras de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |
| Microsoft.Support/* | Criar e gerir pedidos de suporte |
| successbricks.cleardb/Databases/* | Criar e gerir bases de dados do ClearDB MySQL |

### <a name="contributor"></a>Contribuinte
Pode gerir tudo exceto access

| **Ações** ||
| ------- | ------ |
| * | Criar e gerir os recursos de todos os tipos |

| **NotActions** ||
| ------- | ------ |
| Microsoft.Authorization/*/Delete | Não é possível eliminar funções e atribuições de funções |  
| Microsoft.Authorization/*/Write | Não é possível criar funções e atribuições de funções |

### <a name="data-factory-contributor"></a>Dados fábrica contribuinte
Criar e gerir fábricas de dados e recursos de subordinados nelas.

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
| Microsoft.DataFactory/dataFactories/* | Criar e gerir fábricas de dados e recursos de subordinados nelas. |
| Microsoft.Insights/alertRules/* | Criar e gerir regras de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |
| Microsoft.Support/* | Criar e gerir pedidos de suporte |

### <a name="devtest-labs-user"></a>DevTest Labs utilizador
Pode ver tudo e ligar, início, reinicie e encerramento máquinas virtuais

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
| Microsoft.Compute/availabilitySets/read | Ler as propriedades dos conjuntos de disponibilidade |
| Microsoft.Compute/virtualMachines/*/read | Ler as propriedades de uma máquina de virtual (tamanhos VM, estado de runtime, extensões VM, etc.) |
| Microsoft.Compute/virtualMachines/deallocate/action | Retirar máquinas virtuais |
| Microsoft.Compute/virtualMachines/read | Ler as propriedades de uma máquina virtual |
| Microsoft.Compute/virtualMachines/restart/action | Reinicie o máquinas virtuais |
| Microsoft.Compute/virtualMachines/start/action | Iniciar máquinas virtuais |
| Microsoft.DevTestLab/*/read | Leia as propriedades de um laboratório de |
| Microsoft.DevTestLab/labs/createEnvironment/action | Criar um ambiente de teste |
| Microsoft.DevTestLab/labs/formulas/delete | Eliminar fórmulas |
| Microsoft.DevTestLab/labs/formulas/read | Fórmulas de leitura |
| Microsoft.DevTestLab/labs/formulas/write | Adicionar ou modificar as fórmulas |
| Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Avaliar as políticas de laboratório |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Aderir a um conjunto de endereços de back-end de Balanceador de carga |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Aderir a um balanceador de carga regra de NAT entrada |
| Microsoft.Network/networkInterfaces/*/read | Leia as propriedades de uma interface de rede (por exemplo, todos os as carga balanceadores de que a interface de rede é uma parte do) |
| Microsoft.Network/networkInterfaces/join/action | Aderir a uma Máquina Virtual para uma interface de rede |
| Microsoft.Network/networkInterfaces/read | Ler interfaces de rede |
| Microsoft.Network/networkInterfaces/write | Escrever interfaces de rede |
| Microsoft.Network/publicIPAddresses/*/read | Leia as propriedades de um endereço IP público |
| Microsoft.Network/publicIPAddresses/join/action | Aderir a um endereço IP público |
| Microsoft.Network/publicIPAddresses/read | Leia os endereços IP públicos de rede |
| Microsoft.Network/virtualNetworks/subnets/join/action | Aderir a uma rede virtual |
| Microsoft.Resources/deployments/operations/read | Operações de implementação de leitura |
| Microsoft.Resources/deployments/read | Ler híbridas |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |
| Microsoft.Storage/storageAccounts/listKeys/action | Lista de teclas de conta de armazenamento |

### <a name="dns-zone-contributor"></a>Zona de DNS de contribuinte
Pode gerir registos e zonas de DNS.

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/\*/ler | Funções de leitura e atribuições de funções |
| Microsoft.Insights/alertRules/\* | Criar e gerir regras de alertas |
| Microsoft.Network/dnsZones/\* | Criar e gerir zonas de DNS e registos |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/\* | Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |
| Microsoft.Support/\* | Criar e gerir pedidos de suporte |


### <a name="documentdb-account-contributor"></a>Conta DocumentDB contribuinte
Pode gerir as contas de DocumentDB

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
| Microsoft.DocumentDb/databaseAccounts/* | Criar e gerir contas de DocumentDB |
| Microsoft.Insights/alertRules/* | Criar e gerir regras de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |
| Microsoft.Support/* | Criar e gerir pedidos de suporte |

### <a name="intelligent-systems-account-contributor"></a>Sistemas inteligente conta contribuinte
Pode gerir as contas de sistemas inteligente

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
| Microsoft.Insights/alertRules/* | Criar e gerir regras de alertas |
| Microsoft.IntelligentSystems/accounts/* | Criar e gerir contas de sistemas inteligente |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |
| Microsoft.Support/* | Criar e gerir pedidos de suporte |

### <a name="network-contributor"></a>Rede de contribuinte
Pode gerir todos os recursos de rede

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
| Microsoft.Insights/alertRules/* | Criar e gerir regras de alertas |
| Microsoft.Network/* | Criar e gerir redes |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |
| Microsoft.Support/* | Criar e gerir pedidos de suporte |

### <a name="new-relic-apm-account-contributor"></a>Novo contribuinte de conta APM Relic
Pode gerir contas de gestão de desempenho de aplicações do novo Relic e de aplicações

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
| Microsoft.Insights/alertRules/* | Criar e gerir regras de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |
| Microsoft.Support/* | Criar e gerir pedidos de suporte |
| NewRelic.APM/accounts/* | Criar e gerir contas de gestão de desempenho do Relic nova aplicação |

### <a name="owner"></a>Proprietário
Pode gerir tudo, incluindo o access

| **Ações** ||
| ------- | ------ |
| * | Criar e gerir os recursos de todos os tipos |

### <a name="reader"></a>Leitor
Pode ver tudo, mas não pode efetuar alterações

| **Ações** ||
| ------- | ------ |
| * / ler | Leia os recursos de todos os tipos, exceto segredos. |

### <a name="redis-cache-contributor"></a>Redis contribuinte Cache
Pode gerir Redis caches

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
| Microsoft.Cache/redis/* | Criar e gerir Redis caches |
| Microsoft.Insights/alertRules/* | Criar e gerir regras de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |
| Microsoft.Support/* | Criar e gerir pedidos de suporte |

### <a name="scheduler-job-collections-contributor"></a>Contribuinte de coleções de tarefa de programador
Pode gerir coleções de tarefa de programador

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
| Microsoft.Insights/alertRules/* | Criar e gerir regras de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |  
| Microsoft.Scheduler/jobcollections/* | Criar e gerir coleções de tarefa |
| Microsoft.Support/* | Criar e gerir pedidos de suporte  |

### <a name="search-service-contributor"></a>Contribuinte de serviço de pesquisa
Pode gerir os serviços de pesquisa

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
| Microsoft.Insights/alertRules/* | Criar e gerir regras de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |  
| Microsoft.Search/searchServices/* | Criar e gerir os serviços de pesquisa |
| Microsoft.Support/* | Criar e gerir pedidos de suporte  |

### <a name="security-manager"></a>Gestor de segurança
Pode gerir componentes de segurança, políticas de segurança e máquinas virtuais

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
| Microsoft.ClassicCompute/*/read | Informações de configuração de ler clássica calcular máquinas virtuais |
| Microsoft.ClassicCompute/virtualMachines/*/write | Escrever a configuração do máquinas virtuais |
| Microsoft.ClassicNetwork/*/read | Informações de configuração de ler sobre rede clássico  |
| Microsoft.Insights/alertRules/* | Criar e gerir regras de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |  
| Microsoft.Security/* | Criar e gerir componentes de segurança e políticas |
| Microsoft.Support/* | Criar e gerir pedidos de suporte  |

### <a name="sql-db-contributor"></a>SQL DB contribuinte
Pode gerir bases de dados SQL, mas não os respetivos políticas relacionadas com segurança

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Funções de leitura e as atribuições de funções |
| Microsoft.Insights/alertRules/* | Criar e gerir regras de alertas |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |
| Microsoft.Sql/servers/databases/* | Criar e gerir bases de dados SQL |
| Microsoft.Sql/servers/read | Leia os servidores de SQL |
| Microsoft.Support/* | Criar e gerir pedidos de suporte |

| **NotActions** ||
| ------- | ------ |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Não é possível editar políticas de auditoria |
| Microsoft.Sql/servers/databases/auditingSettings/* | Não é possível editar definições de auditoria |
| Microsoft.Sql/servers/databases/auditRecords/read  | Não consegue ler registos de auditoria |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Não pode editar as políticas de ligação |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Não é possível editar dados masking políticas |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Não é possível editar políticas de alerta de segurança |
| Microsoft.Sql/servers/databases/securityMetrics/* | Não é possível editar métricas de segurança |

### <a name="sql-security-manager"></a>Gestor de segurança do SQL
Pode gerir as políticas que foram relacionado com a segurança dos servidores SQL e bases de dados

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorização Microsoft de leitura |
| Microsoft.Insights/alertRules/* | Criar e gerir regras de alertas de informações |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |
| Microsoft.Sql/servers/auditingPolicies/* | Criar e gerir políticas de auditoria do SQL server |
| Microsoft.Sql/servers/auditingSettings/* | Criar e gerir as definições de auditoria do SQL server |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Criar e gerir políticas de auditoria de base de dados do SQL server |
| Microsoft.Sql/servers/databases/auditingSettings/* | Criar e gerir definições de auditoria de base de dados do SQL server |
| Microsoft.Sql/servers/databases/auditRecords/read | Registos de auditoria de leitura |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Criar e gerir políticas de ligação de base de dados do SQL server |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Criar e gerir dados de base de dados do SQL server masking políticas |
| Microsoft.Sql/servers/databases/read | Bases de dados SQL de leitura |
| Microsoft.Sql/servers/databases/schemas/read | Esquemas de base de dados do leitura SQL server |
| Microsoft.Sql/servers/databases/schemas/tables/columns/read | Colunas de tabela do SQL de leitura servidor da base de dados |
| Microsoft.Sql/servers/databases/schemas/tables/read | Tabelas de base de dados do leitura SQL server |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Criar e gerir políticas alertas de segurança da base de dados de servidor SQL |
| Microsoft.Sql/servers/databases/securityMetrics/* | Criar e gerir métricas de segurança da base de dados do SQL server |
| Microsoft.Sql/servers/read | Leia os servidores de SQL |
| Microsoft.Sql/servers/securityAlertPolicies/* | Criar e gerir políticas de alertas de segurança do SQL server |
| Microsoft.Support/* | Criar e gerir pedidos de suporte |

### <a name="sql-server-contributor"></a>SQL Server contribuinte
Pode gerir os servidores SQL e bases de dados, mas não os respetivos políticas relacionadas com segurança

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorização de leitura|
| Microsoft.Insights/alertRules/* | Criar e gerir regras de alertas de informações |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |
| Microsoft.Sql/servers/* | Criar e gerir os servidores de SQL |
| Microsoft.Support/* | Criar e gerir pedidos de suporte |

| **NotActions** ||
| ------- | ------ |
| Microsoft.Sql/servers/auditingPolicies/* | Não pode editar as políticas de auditoria do SQL server |
| Microsoft.Sql/servers/auditingSettings/* | Não pode editar as definições de auditoria do SQL server |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Não é possível editar políticas de auditoria de base de dados do SQL server |
| Microsoft.Sql/servers/databases/auditingSettings/* | Não é possível editar definições de auditoria de base de dados do SQL server |
| Microsoft.Sql/servers/databases/auditRecords/read  | Não consegue ler registos de auditoria |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Não é possível editar políticas de ligação de base de dados do SQL server |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Não é possível editar dados de base de dados do SQL server masking políticas |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Não é possível editar políticas alertas de segurança da base de dados de servidor SQL |
| Microsoft.Sql/servers/databases/securityMetrics/* | Não é possível editar métricas de segurança da base de dados do SQL server |
| Microsoft.Sql/servers/securityAlertPolicies/* | Não é possível editar políticas de alertas de segurança do SQL server |

### <a name="classic-storage-account-contributor"></a>Contribuinte de conta de armazenamento clássico
Pode gerir as contas de armazenamento clássico

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorização de leitura |
| Microsoft.ClassicStorage/storageAccounts/* | Criar e gerir contas de armazenamento |
| Microsoft.Insights/alertRules/* | Criar e gerir regras de alertas de informações |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |  
| Microsoft.Support/* | Criar e gerir pedidos de suporte |

### <a name="storage-account-contributor"></a>Contribuinte de conta de armazenamento
Pode gerir as contas de armazenamento, mas não aceder aos mesmos.

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Ler todas as autorização |
| Microsoft.Insights/alertRules/* | Criar e gerir regras de alertas de informações |
| Microsoft.Insights/diagnosticSettings/* | Gerir definições de diagnóstico |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |  
| Microsoft.Storage/storageAccounts/* | Criar e gerir contas de armazenamento |
| Microsoft.Support/* | Criar e gerir pedidos de suporte |

### <a name="user-access-administrator"></a>Administrador de acesso de utilizadores
Pode gerir o acesso do utilizador para recursos Azure

| **Ações** ||
| ------- | ------ |
| * / ler | Leia os recursos de todos os tipos, exceto segredos. |
| Microsoft.Authorization/* | Gerir a autorização |
| Microsoft.Support/* | Criar e gerir pedidos de suporte |

### <a name="classic-virtual-machine-contributor"></a>Máquina Virtual clássico de contribuinte
Pode gerir clássicas máquinas virtuais mas não a virtual armazenamento ou rede conta à qual estão ligados

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorização de leitura |
| Microsoft.ClassicCompute/domainNames/* | Criar e gerir os nomes de domínio cluster clássico |
| Microsoft.ClassicCompute/virtualMachines/* | Criar e gerir máquinas virtuais |
| Microsoft.ClassicNetwork/networkSecurityGroups/join/action | Aderir a grupos de segurança de rede |
| Microsoft.ClassicNetwork/reservedIps/link/action | Ligar IPs reservadas |
| Microsoft.ClassicNetwork/reservedIps/read | Leitura reservada endereços IP |
| Microsoft.ClassicNetwork/virtualNetworks/join/action | Aderir a redes virtuais |
| Microsoft.ClassicNetwork/virtualNetworks/read | Ler redes virtuais |
| Microsoft.ClassicStorage/storageAccounts/disks/read | Ler discos de armazenamento de conta |
| Microsoft.ClassicStorage/storageAccounts/images/read | Imagens de conta de armazenamento de leitura |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | Lista de teclas de conta de armazenamento |
| Microsoft.ClassicStorage/storageAccounts/read | Contas de armazenamento clássico de leitura |
| Microsoft.Insights/alertRules/* | Criar e gerir regras de alertas de informações |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |
| Microsoft.Support/* | Criar e gerir pedidos de suporte |

### <a name="virtual-machine-contributor"></a>Contribuinte máquina virtual
Pode gerir máquinas virtuais mas não a virtual armazenamento ou rede conta à qual estão ligados

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorização de leitura |
| Microsoft.Compute/availabilitySets/* | Criar e gerir conjuntos de disponibilidade de cluster |
| Microsoft.Compute/locations/* | Criar e gerir localizações de cluster |
| Microsoft.Compute/virtualMachines/* | Criar e gerir máquinas virtuais |
| Microsoft.Compute/virtualMachineScaleSets/* | Criar e gerir conjuntos de escala de máquina virtual |
| Microsoft.Insights/alertRules/* | Criar e gerir regras de alertas de informações |
| Microsoft.Network/applicationGateways/backendAddressPools/join/action | Aderir a rede gateway back-end endereço aplicacionais |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Participar Balanceador de carga back-end conjuntos de endereços |
| Microsoft.Network/loadBalancers/inboundNatPools/join/action | Participar Balanceador de carga de agrupamentos de NAT de entrada |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Participar Balanceador de carga NAT regras de entrada |
| Microsoft.Network/loadBalancers/read | Balanceadores de carga de leitura |
| Microsoft.Network/locations/* | Criar e gerir localizações de rede |
| Microsoft.Network/networkInterfaces/* | Criar e gerir interfaces de rede |
| Microsoft.Network/networkSecurityGroups/join/action | Aderir a grupos de segurança de rede |
| Microsoft.Network/networkSecurityGroups/read | Leia os grupos de segurança de rede |
| Microsoft.Network/publicIPAddresses/join/action | Aderir a endereços IP públicos de rede |
| Microsoft.Network/publicIPAddresses/read | Leia os endereços IP públicos de rede |
| Microsoft.Network/virtualNetworks/read | Ler redes virtuais |
| Microsoft.Network/virtualNetworks/subnets/join/action | Participar sub-redes de rede virtual |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |  
| Microsoft.Storage/storageAccounts/listKeys/action | Lista de teclas de conta de armazenamento |
| Microsoft.Storage/storageAccounts/read | Contas de armazenamento de leitura |
| Microsoft.Support/* | Criar e gerir pedidos de suporte |

### <a name="classic-network-contributor"></a>Rede clássico de contribuinte
Pode gerir IPs reservadas e redes virtuais clássicos

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorização de leitura |
| Microsoft.ClassicNetwork/* | Criar e gerir redes clássicos |
| Microsoft.Insights/alertRules/* | Criar e gerir regras de alertas de informações |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |  
| Microsoft.Support/* | Criar e gerir pedidos de suporte |

### <a name="web-plan-contributor"></a>Web plano de contribuinte
Pode gerir planos web

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorização de leitura |
| Microsoft.Insights/alertRules/* | Criar e gerir regras de alertas de informações |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |  
| Microsoft.Support/* | Criar e gerir pedidos de suporte |
| Microsoft.Web/serverFarms/* | Criar e gerir os farms |

### <a name="website-contributor"></a>Web site contribuinte
Pode gerir Web sites, mas não os planos de web à qual estão ligados

| **Ações** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorização de leitura |
| Microsoft.Insights/alertRules/* | Criar e gerir regras de alertas de informações |
| Microsoft.Insights/components/* | Criar e gerir componentes de informações |
| Microsoft.ResourceHealth/availabilityStatuses/read | Ler o estado de funcionamento dos recursos |
| Microsoft.Resources/deployments/* | Criar e gerir implementações do grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupos de recursos de leitura |  
| Microsoft.Support/* | Criar e gerir pedidos de suporte |
| Microsoft.Web/certificates/* | Criar e gerir certificados de Web site |
| Microsoft.Web/listSitesAssignedToHostName/read | Ler sites atribuídas a um nome de anfitrião |
| Microsoft.Web/serverFarms/join/action | Participar em farms de servidores |
| Microsoft.Web/serverFarms/read | Leia os farms |
| Microsoft.Web/sites/* | Criar e gerir Web sites |

## <a name="see-also"></a>Consulte também
- [Controlo de acesso baseado em funções](role-based-access-control-configure.md): introdução ao RBAC no portal do Azure.
- [Funções de personalizada no Azure RBAC](role-based-access-control-custom-roles.md): Saiba como criar funções personalizadas para corresponder às suas necessidades de acesso.
- [Criar um acesso alterar relatório de histórico](role-based-access-control-access-change-history-report.md): manter a par das alterar atribuições de funções no RBAC.
- [Resolução de problemas do controlo de acesso baseado em funções](role-based-access-control-troubleshooting.md): obter sugestões para corrigir problemas comuns.
