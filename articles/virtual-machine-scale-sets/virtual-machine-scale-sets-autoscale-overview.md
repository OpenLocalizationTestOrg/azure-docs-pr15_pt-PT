<properties
    pageTitle="Dimensionamento e máquina virtual automática Dimensionar conjuntos | Microsoft Azure"
    description="Saiba como utilizar de diagnóstico e autoscale recursos para dimensionar automaticamente máquinas virtuais num conjunto de escala."
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="davidmu"/>

# <a name="automatic-scaling-and-virtual-machine-scale-sets"></a>Dimensionamento e máquina virtual automática Dimensionar conjuntos

Escala automática das máquinas virtuais num conjunto de escala é a criação ou eliminação de máquinas no conjunto, conforme necessário para que correspondam aos requisitos de desempenho. À medida que o volume de trabalho cresce, uma aplicação poderá necessitar de recursos adicionais para ativá-la de forma eficaz executar tarefas.

Escala automática é um processo automatizado que ajuda a simplificar gerais de gestão. Reduzir custos gerais, não precisa de continuamente monitorizar o desempenho do sistema ou decidir como gerir recursos. Dimensionamento é um processo flexível. Mais recursos podem ser adicionados como os aumentos de carga, mas como procura reduções de taxas de recursos podem ser removidos para minimizar os custos e manter os níveis de desempenho.

Configure o dimensionamento automática numa escala de definir utilizando um modelo de Gestor de recursos do Azure, Azure PowerShell, Azure clip ou o portal do Azure.

## <a name="set-up-scaling-by-using-resource-manager-templates"></a>Configurar a escala com os modelos de Gestor de recursos

Em vez de implementação e gestão de cada recurso da sua aplicação separadamente, utilizar um modelo que implementa todos os recursos numa única e coordenado operação. No modelo, são definidos recursos da aplicação e implementação parâmetros são especificados para diferentes ambientes. O modelo é composta por JSON e expressões que pode utilizar para construir valores para a sua implementação. Para saber mais, consulte [Gestor de recursos do Azure criação de modelos](../resource-group-authoring-templates.md).

No modelo, pode especificar o elemento de capacidade:

    "sku": {
      "name": "Standard_A0",
      "tier": "Standard",
      "capacity": 3
    },

Capacidade identifica o número de máquinas virtuais do conjunto. Pode alterar manualmente a capacidade de ao implementar um modelo com um valor diferente. Se estiver a implementar um modelo para alterar apenas a capacidade, pode incluir apenas o elemento SKU com a capacidade atualizado.

Altere automaticamente a capacidade da sua escala definir utilizando a combinação de recurso autoscaleSettings e a extensão de diagnóstico.

### <a name="configure-the-azure-diagnostics-extension"></a>Configurar a extensão de diagnósticos do Azure

Escala automática só pode ser feita se coleção de métricas for bem sucedida em cada máquina virtual no conjunto de escala. A extensão de diagnósticos do Azure fornece as capacidades de monitorização e diagnósticos que satisfaça as necessidades da coleção de métricas do recurso autoscale. Pode instalar a extensão como parte do modelo de Gestor de recursos.

Este exemplo mostra as variáveis que são utilizadas no modelo para configurar a extensão de diagnóstico:

    "diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'saa')]",
    "accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/', 'Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
    "wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
    "wadperfcounter": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Thread Count\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
    "wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId=\"')]",
    "wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
    "wadcfgxend": "[concat('\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"

Parâmetros são fornecidos quando o modelo é implementado. Neste exemplo, o nome da conta de armazenamento onde os dados são armazenados e o nome do conjunto de escala a partir do qual os dados são recolhidos são fornecidos. Também neste exemplo do Windows Server, apenas o contador de desempenho do número de threads é recolhido. Todos os contadores desempenho disponíveis no Windows ou Linux podem ser utilizados para recolher informações de diagnóstico e podem ser incluídos na configuração da extensão.

Este exemplo mostra a definição da extensão no modelo:

    "extensionProfile": {
      "extensions": [
        {
          "name": "Microsoft.Insights.VMDiagnosticsSettings",
          "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.5",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "xmlCfg": "[base64(concat(variables('wadcfgxstart'),variables('wadmetricsresourceid'),variables('wadcfgxend')))]",
              "storageAccount": "[variables('diagnosticsStorageAccountName')]"
            },
            "protectedSettings": {
              "storageAccountName": "[variables('diagnosticsStorageAccountName')]",
              "storageAccountKey": "[listkeys(variables('accountid'), variables('apiVersion')).key1]",
              "storageAccountEndPoint": "https://core.windows.net"
            }
          }
        }
      ]
    }

Quando executa a extensão de diagnóstico, os dados são recolhidos numa tabela que encontra-se na conta de armazenamento que especificar. Na tabela WADPerformanceCounters, pode encontrar os dados recolhidos:

![](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountBefore2.png)

### <a name="configure-the-autoscalesettings-resource"></a>Configurar o recurso autoScaleSettings

O recurso autoscaleSettings utiliza as informações da extensão de diagnóstico para decidir se pretende aumentar ou diminuir o número de máquinas virtuais do conjunto de escala.

Este exemplo mostra a configuração do recurso no modelo:

    {
      "type": "Microsoft.Insights/autoscaleSettings",
      "apiVersion": "2015-04-01",
      "name": "[concat(parameters('resourcePrefix'),'as1')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
      ],
      "properties": {
        "enabled": true,
        "name": "[concat(parameters('resourcePrefix'),'as1')]",
        "profiles": [
          {
            "name": "Profile1",
            "capacity": {
              "minimum": "1",
              "maximum": "10",
              "default": "1"
            },
            "rules": [
              {
                "metricTrigger": {
                  "metricName": "\\Process(_Total)\\Thread Count",
                  "metricNamespace": "",
                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
                  "timeGrain": "PT1M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 650
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "\\Process(_Total)\\Thread Count",
                  "metricNamespace": "",
                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
                  "timeGrain": "PT1M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 550
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              }
            ]
          }
        ],
        "targetResourceUri": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
      }
    }

No exemplo acima, duas regras são criadas para definir as ações de dimensionamento automáticas. A primeira regra define a ação de escala de saída e a segunda regra define a ação escala-in. Estes valores são fornecidos nas regras:

- **metricName** - este valor é o mesmo que o contador de desempenho que definiu na variável wadperfcounter para a extensão de diagnóstico. No exemplo acima, é utilizado o número de threads contador.  
- **metricResourceUri** - este valor é o identificador de recursos do conjunto de escala de máquina virtual. Este identificador contém o nome do grupo de recursos, o nome do fornecedor de recursos e o nome da escala definido com uma escala.
- **timeGrain** – este valor é a granularidade as métricas que são recolhidas. No exemplo anterior, os dados são recolhidos num intervalo de um minuto. Este valor é utilizada com timeWindow.
- **Estatística** – este valor determina a forma como as métricas são combinadas para acomodar a ação de dimensionamento automática. Os valores possíveis são: média, mínimo, máximo.
- **timeWindow** – este valor é o intervalo de tempo no qual os dados da instância são recolhidos. Tem de estar entre 5 minutos e 12 horas.
- **timeAggregation** – este valor determina como os dados que são recolhidos devem ser combinados ao longo do tempo. O valor predefinido é média. Os valores possíveis são: média, mínimo, máximo, última, Total, contagem.
- **operador** – este valor é o operador que é utilizado para comparar os dados métricos e o limiar de. Os valores possíveis são: for igual a, NotEquals, GreaterThan, GreaterThanOrEqual, LessThan, LessThanOrEqual.
- **limiar** – este valor é o valor que a ação de escala de accionadores. Certifique-se de que fornecem uma suficiente diferença entre o limiar de para a ação de escala de saída e o limiar de para a ação escala-in. Se definir os valores a ser a mesma, o sistema prevê alteração constante, que impede de implementação de uma acção dimensionamento. Por exemplo, não funciona tanto para 600 threads a definição no exemplo anterior.
- **direcção** – este valor determina a ação que é disponibilizada quando o valor limite é obtido. Os valores possíveis são aumentar ou diminuir.
- **tipo de** – este valor é o tipo de ação que deve ocorrer e tem de estar definida para ChangeCount.
- **valor** – este valor é o número de máquinas virtuais que são adicionados ou removidos do conjunto de escala. Este valor tem de ser 1 ou maior.
- **cooldown** – este valor é a quantidade de tempo a aguardar desde a última ação dimensionamento antes da ação seguinte ocorre. Este valor tem de estar entre um minuto e uma semana.

Consoante o contador de desempenho que está a utilizar, alguns dos elementos na configuração do modelo são utilizadas forma diferente. No exemplo anterior, o contador de desempenho é o número de threads, o valor do limiar for 650 para uma ação de escala de saída e o valor limite é 550 para a ação na escala. Se utilizar um contador como % processador de tempo, o valor limite está definido para a percentagem de utilização da CPU que determina uma ação de dimensionamento.

Quando um carregamento é criado nas máquinas virtuais que accionadores uma ação de dimensionamento, a capacidade do conjunto é aumentada com base no valor no modelo. Por exemplo, numa escala de conjunto de onde a capacidade de está definida para 3 e o valor de ação de escala está definido para 1:

![](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerBefore.png)

Quando o carregamento é criado que faz com que o número de threads média ir acima do limiar de 650:

![](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountAfter.png)

Uma ação de escala de saída é acionada que faz com que a capacidade do conjunto aumentado por uma:

    "sku": {
      "name": "Standard_A0",
      "tier": "Standard",
      "capacity": 4
    },

E uma máquina virtual é adicionada ao conjunto de escala:

![](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerAfter.png)

Após um período de cooldown de cinco minutos, se o número médio de threads nos computadores permanece superior 600, outro computador é adicionado ao conjunto. Se o número de threads média permanece abaixo 550, a capacidade do conjunto de escala é reduzida por uma e uma máquina é removida do conjunto.

## <a name="set-up-scaling-using-azure-powershell"></a>Configurar o dimensionamento através do PowerShell do Azure

Para ver exemplos de utilizar o PowerShell para configurar o autoscaling, veja o [Azure Monitor PowerShell rápida iniciar amostras](../monitoring-and-diagnostics/insights-powershell-samples.md).

## <a name="set-up-scaling-using-azure-cli"></a>Configurar o dimensionamento utilizando o clip do Azure

Para ver exemplos de utilização de Azure clip para configurar o autoscaling, veja o [Azure Monitor em diferentes plataformas clip rápida iniciar amostras](../monitoring-and-diagnostics/insights-cli-samples.md).

## <a name="set-up-scaling-using-the-azure-portal"></a>Configurar o dimensionamento através do portal Azure

Para ver um exemplo de através do portal Azure para configurar o autoscaling, consulte [criar um conjunto de escala de Máquina Virtual através do portal Azure](virtual-machine-scale-sets-portal-create.md).

## <a name="investigate-scaling-actions"></a>Investigar ações de dimensionamento

- [Azure portal]() - atualmente pode obter uma quantidade limitada de informações através do portal.
- [Azure recurso Explorer]() - esta ferramenta é adequado para a explorar o estado atual do seu conjunto de escala. Siga este caminho e deverá ver a vista de instância do conjunto de escala que criou: subscrições > {a sua subscrição} > resourceGroups > {seu grupo de recursos} > fornecedores > Microsoft.Compute > virtualMachineScaleSets > {seu conjunto de escala} > virtualMachines
- Azure PowerShell - Utilize este comando para obter algumas informações:

        Get-AzureRmResource -name vmsstest1 -ResourceGroupName vmsstestrg1 -ResourceType Microsoft.Compute/virtualMachineScaleSets -ApiVersion 2015-06-15
        Get-Autoscalesetting -ResourceGroup rainvmss -DetailedOutput

- Ligar a máquina virtual jumpbox tal como faria com qualquer outro computador e, em seguida, pode ser acedido remotamente as máquinas virtuais na escala do conjunto de para monitorizar processos individuais.

## <a name="next-steps"></a>Próximos passos

- Observe [Dimensionar automaticamente máquinas num conjunto de escala de Máquina Virtual](virtual-machine-scale-sets-windows-autoscale.md) para ver um exemplo de como criar uma escala de configurar com escala automática configurado.
- Localizar exemplos de Monitor de Azure funcionalidades no [Azure Monitor PowerShell rápida iniciar amostras](../monitoring-and-diagnostics/insights-powershell-samples.md) de monitorização
- Saiba mais sobre as funcionalidades de notificação no [utilizar autoscale ações para enviar correio eletrónico e webhook as notificações de alerta no Monitor do Azure](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md).
- Saiba mais sobre como [os registos de auditoria de utilização para enviar correio eletrónico e webhook as notificações de alerta no Monitor do Azure](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md)
- Saiba mais sobre [cenários autoscale avançadas](./virtual-machine-scale-sets-advanced-autoscale.md).
