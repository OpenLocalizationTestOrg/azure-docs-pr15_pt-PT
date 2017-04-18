<properties
    pageTitle="Dimensionar verticalmente conjuntos de escala de máquina virtual Azure | Microsoft Azure"
    description="Como verticalmente dimensionar uma Máquina Virtual em resposta a monitorização de alertas de automatização do Azure"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gbowerman"
    manager="madhana"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="guybo"/>

# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>Autoscale vertical com conjuntos de escala de Máquina Virtual

Este artigo descreve como verticalmente Dimensionar Azure de [Conjuntos de escala de Máquina Virtual](https://azure.microsoft.com/services/virtual-machine-scale-sets/) com ou sem reprovisioning. Para vertical dimensionamento da VMs que não estão em conjuntos de escala, consulte [verticalmente Dimensionar Azure máquina virtual com a automatização Azure](../virtual-machines/virtual-machines-windows-vertical-scaling-automation.md).

Vertical dimensionamento, também conhecido como _Dimensionar para cima_ e _Dimensionar para baixo_, significa que crescentes ou decrescentes tamanhos de máquina virtual (VM) em resposta a uma carga de trabalho. Compare esta com [dimensionamento horizontal](./virtual-machine-scale-sets-autoscale-overview.md), também conhecida como _escala de saída_ e a _escala_, onde o número de VMs é alterado consoante a carga de trabalho.

Reprovisioning significa que remover um VM existente e ao substituí-la com um novo. Quando aumentar ou diminuir o tamanho da VMs num conjunto de escala VM, em alguns casos que pretende redimensionar VMs existentes e manter os seus dados, enquanto em outros casos, precisar implementar o novos VMs do novo tamanho. Este documento abrange ambos os casos.

Dimensionamento vertical pode ser útil quando:

- Um serviço criada com base em máquinas virtuais é utilizada em (por exemplo na fins de semana). Reduzir o tamanho da memória virtual pode reduzir custos mensais.
- Aumentar o tamanho da memória virtual para fazer face maior pedido sem criar VMs adicionais.

Pode configurar o dimensionamento vertical para ser acionadas com base no métricas baseada em alertas a partir do seu conjunto de escala de VM. Quando o alerta é activado-é acionada um webhook esse accionadores configurar um livro de execuções que pode dimensionar a escala para cima ou para baixo. Dimensionamento vertical pode ser configurado seguindo estes passos:

1. Crie uma conta Azure automatização com capacidade de executar como.
2. Importe escala Vertical do Azure automatização runbooks VM escala conjuntos de para a sua subscrição.
3. Adicione um webhook ao seu livro de execuções.
4. Adicione um alerta para o conjunto de escala de VM utilizando uma notificação de webhook.

> [AZURE.NOTE] Vertical autoscaling só pode ser realizadas dentro de determinados intervalos de tamanhos VM. Comparar as especificações de cada tamanho antes de decidir dimensionar a partir de uma para outra (número mais elevado não sempre indicar o tamanho da memória virtual maior). Pode escolher Dimensionar entre os pares de tamanhos seguintes:

>| VM tamanhos par de dimensionamento |   |
|---|---|
|  Standard_A0 | Standard_A11 |
|  Standard_D1 |  Standard_D14 |
|  Standard_DS1 |  Standard_DS14 |
|  Standard_D1v2 |  Standard_D15v2 |
|  Standard_G1 |  Standard_G5 |
|  Standard_GS1 |  Standard_GS5 |

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>Criar uma conta de automatização do Azure com capacidade de executar como

A primeira coisa que precisa de fazer é criar uma conta de automatização do Azure que aloja runbooks utilizado para dimensionar as instâncias VM escala definida. Recentemente [Azure automatização](https://azure.microsoft.com/services/automation/) introduzida a funcionalidade de "Executar como conta", que torna a definição para cima o capital de serviço para automaticamente a executar o runbooks em nome de um utilizador muito fácil. Pode ler mais sobre isto no artigo abaixo:

* [Autenticar Runbooks com a conta Azure executar como](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importar escala Vertical do Azure automatização runbooks para a sua subscrição

Runbooks necessários para verticalmente dimensionar os conjuntos de escala VM já estão publicados na Galeria de livro execuções de automatização do Azure. Para importá-los para a sua subscrição siga os passos neste artigo:

* [Livro execuções e módulo galerias de automatização do Azure](../automation/automation-runbook-gallery.md)

Escolha a opção de Galeria de procurar a partir do Runbooks menu:

![Runbooks para serem importados][runbooks]

Os que precisam de ser importado runbooks são apresentadas. Selecione o livro de execuções com base em se pretende vertical com ou sem reprovisioning de dimensionamento que:

![Galeria de Runbooks][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>Adicionar um webhook ao seu livro de execuções

Após importar runbooks que vai precisar de adicionar um webhook ao livro de execuções para que pode ser acionou por um alerta a partir de um conjunto de escala de VM. Os detalhes de criação de um webhook para o seu livro de execuções são descritos neste artigo:

* [Azure webhooks de automatização](../automation/automation-webhooks.md)

> [AZURE.NOTE] Certifique-se de que copiar o webhook URI antes de fechar a caixa de diálogo webhook que irá precisar isto na secção seguinte.

## <a name="add-an-alert-to-your-vm-scale-set"></a>Adicionar um alerta para o conjunto de escala de VM

Segue-se um script PowerShell, que mostra como adicionar um alerta para uma escala de VM definida. Consulte o artigo seguinte para obter o nome da métrica seja acionada o alerta na: [Azure Monitor autoscaling comuns métricas](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md).

```
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail user@contoso.com
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri <uri-of-the-webhook>
$threshold = <value-of-the-threshold>
$rg = <resource-group-name>
$id = <resource-id-to-add-the-alert-to>
$location = <location-of-the-resource>
$alertName = <name-of-the-resource>
$metricName = <metric-to-fire-the-alert-on>
$timeWindow = <time-window-in-hh:mm:ss-format>
$condition = <condition-for-the-threshold> # Other valid values are LessThanOrEqual, GreaterThan, GreaterThanOrEqual
$description = <description-for-the-alert>

Add-AzureRmMetricAlertRule  -Name  $alertName `
                            -Location  $location `
                            -ResourceGroup $rg `
                            -TargetResourceId $id `
                            -MetricName $metricName `
                            -Operator  $condition `
                            -Threshold $threshold `
                            -WindowSize  $timeWindow `
                            -TimeAggregationOperator Average `
                            -Actions $actionEmail, $actionWebhook `
                            -Description $description
```

> [AZURE.NOTE] Recomenda-se para configurar uma janela de tempo razoável para o alerta para evitar acionar dimensionamento vertical e qualquer interrupção de serviço associado, demasiado com frequência. Considere numa janela de pelo menos, 20-30 minutos ou mais. Considere se precisar de evitar qualquer interrupção de dimensionamento horizontal.

Para obter mais informações sobre como criar alertas referir-se para os seguintes artigos:

* [Exemplos de guia de introdução do Azure Monitor PowerShell](../monitoring-and-diagnostics/insights-powershell-samples.md)
* [Exemplos de guia de introdução do Azure Monitor em diferentes plataformas clip](../monitoring-and-diagnostics/insights-cli-samples.md)

## <a name="summary"></a>Resumo

Este artigo mostrava simples exemplos de dimensionamento verticais. Com estes blocos modulares - conta automatização, runbooks, webhooks, alertas - pode ligar a uma variedade de rich de eventos com um conjunto personalizado de ações.

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png
