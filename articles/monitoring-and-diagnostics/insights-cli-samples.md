<properties
    pageTitle="Exemplos de guia de introdução do Monitor Clip Azure. | Microsoft Azure"
    description="Comandos de clip de exemplo para funcionalidades do Azure Monitor. Azure Monitor é um serviço do Microsoft Azure que permite-lhe enviar notificações de alerta, contacte o URL de web com base em valores de dados de telemetria configurado e serviços em nuvem autoScale, máquinas virtuais e Web Apps."
    authors="kamathashwin"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="ashwink"/>

# <a name="azure-monitor--cross-platform-cli-quick-start-samples"></a>Exemplos de guia de introdução do Azure Monitor em diferentes plataformas clip

Este artigo mostra-lhe amostra interface de comandos (CLI) comandos para o ajudar a aceder às funcionalidades Azure Monitor. Azure Monitor permite-lhe a serviços em nuvem AutoScale, máquinas virtuais e Web Apps e enviar as notificações de alerta ou uma chamada URLs web com base em valores de dados de telemetria configurado.

>[AZURE.NOTE] Azure Monitor é o novo nome para o que era denominado "Azure informações" até 25 de Setembro de 2016. No entanto, os espaços de nomes e, consequentemente, os comandos abaixo ainda contenham "informações".


## <a name="prerequisites"></a>Pré-requisitos

Se já não tiver instalado o clip do Azure, consulte o artigo [instalar o clip do Azure](../xplat-cli-install.md). Se não estiver familiarizado com Azure clip, pode ler mais sobre o mesmo a utilizar [O clip do Azure para Mac, Linux e o Windows com o Gestor de recursos do Azure](../xplat-cli-azure-resource-manager.md).


No Windows, instale npm a partir do [Web site de Node.js](https://nodejs.org/). Depois de concluir a instalação, utilizar CMD.exe com privilégios de executar como administrador, execute o seguinte procedimento a partir da pasta onde está instalado o npm:

```console
npm install azure-cli --global
```

Em seguida, navegue para qualquer/localização da pasta que pretende e escreva na linha de comandos:

```console
azure help
```

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

O primeiro passo consiste em iniciar sessão na sua conta Azure.

```console
azure login
```

Depois de executar este comando, tem de iniciar sessão através das instruções no ecrã. Mais tarde, consulte da sua conta, TenantId e predefinido ID da subscrição. Todos os comandos funcionam no contexto da sua subscrição predefinido.

Para listar os detalhes da sua subscrição atual, utilize o seguinte comando.

```console
azure account show
```

Para alterar o contexto de trabalho para uma subscrição diferente, utilize o seguinte comando.

```console
azure account set "subscription ID or subscription name"
```

Para utilizar os comandos Gestor de recursos do Azure e Azure Monitor, tem de estar no modo de Gestor de recursos do Azure.

```console
azure config mode arm
```

Para ver uma lista de todos os comandos do Azure Monitor suportados, faça o seguinte.

```console
azure insights
```

## <a name="view-audit-logs-for-a-subscription"></a>Ver registos de auditoria para uma subscrição

Para ver uma lista de registos de auditoria, faça o seguinte.

```console
azure insights logs list [options]
```

Experimente o seguinte procedimento para visualizar todas as opções disponíveis.

```console
azure insights logs list -help
```

Eis um exemplo aos registos de lista por um resourceGroup

```console
azure insights logs list --resourceGroup "myrg1"
```

Exemplo para registos de lista por autor da chamada

```console
azure insights logs list --caller "myname@company.com"
```

Exemplo à lista de registos por autor num tipo de recurso, dentro de uma data de início e de fim

```console
azure insights logs list --resourceProvider "Microsoft.Web" --caller "myname@company.com" --startTime 2016-03-08T00:00:00Z --endTime 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>Trabalhar com os alertas
Pode utilizar as informações na secção para trabalhar com os alertas.

### <a name="get-alert-rules-in-a-resource-group"></a>Obter o alertas de regras num grupo de recursos

```console
azure insights alerts rule list abhingrgtest123
azure insights alerts rule list abhingrgtest123 --ruleName andy0323
```

### <a name="create-a-metric-alert-rule"></a>Criar uma regra de alerta métrica

```console
azure insights alerts actions email create --customEmails foo@microsoft.com
azure insights alerts actions webhook create https://someuri.com
azure insights alerts rule metric set andy0323 eastus abhingrgtest123 PT5M GreaterThan 2 /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Web-EastUS/providers/Microsoft.Web/serverfarms/Default1 BytesReceived Total
```

### <a name="create-a-log-alert-rule"></a>Criar uma regra de alerta de registo

```console
azure insights alerts rule log set ruleName eastus resourceGroupName someOperationName
```

### <a name="create-webtest-alert-rule"></a>Criar regra alerta webtest

```console
azure insights alerts rule webtest set leowebtestr1-webtestr1 eastus Default-Web-WestUS PT5M 1 GSMT_AvRaw /subscriptions/b67f7fec-69fc-4974-9099-a26bd6ffeda3/resourcegroups/Default-Web-WestUS/providers/microsoft.insights/webtests/leowebtestr1-webtestr1
```

### <a name="delete-an-alert-rule"></a>Eliminar uma regra de alerta

```console
azure insights alerts rule delete abhingrgtest123 andy0323
```

## <a name="log-profiles"></a>Perfis de registo
Utilize as informações nesta secção para trabalhar com os perfis de registo.

### <a name="get-a-log-profile"></a>Obter um perfil de registo

```console
azure insights logprofile list
azure insights logprofile get -n default
```


### <a name="add-a-log-profile-without-retention"></a>Adicionar um perfil de registo sem retenção

```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Remover um perfil de registo

```console
azure insights logprofile delete --name default
```

### <a name="add-a-log-profile-with-retention"></a>Adicionar um perfil de registo com retenção

```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>Adicionar um perfil de registo com retenção e EventHub

```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --serviceBusRuleId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/testshoeboxeastus/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```


## <a name="diagnostics"></a>Diagnósticos
Utilize as informações nesta secção para trabalhar com definições de diagnóstico.

### <a name="get-a-diagnostic-setting"></a>Obter uma definição de diagnóstico

```console
azure insights diagnostic get --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp
```

### <a name="disable-a-diagnostic-setting"></a>Desativar uma definição de diagnóstico

```console
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled false
```

### <a name="enable-a-diagnostic-setting-without-retention"></a>Ativar uma definição de diagnóstico sem retenção

```console
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled true
```


## <a name="autoscale"></a>Autoscale
Utilize as informações nesta secção para trabalhar com definições de autoscale. Tem de modificar estes exemplos.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Obter autoscale definições para um grupo de recursos

```console
azure insights autoscale setting list montest2
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>Obter definições de autoscale pelo nome num grupo de recursos

```console
azure insights autoscale setting list montest2 -n setting2
```


### <a name="set-auotoscale-settings"></a>Configurar definições de auotoscale

```console
azure insights autoscale setting set montest2 -n setting2 --settingSpec
```
