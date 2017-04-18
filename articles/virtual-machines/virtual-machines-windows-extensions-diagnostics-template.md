<properties
    pageTitle="Criar uma Máquina Virtual do Windows com a monitorização e diagnósticos de utilizar o modelo de Gestor de recursos do Azure | Microsoft Azure"
    description="Utilize um modelo de Gestor de recursos Azure para criar uma nova máquina de virtual do Windows com a extensão de diagnóstico Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="sbtron"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/15/2015"
    ms.author="saurabh"/>

# <a name="create-a-windows-virtual-machine-with-monitoring-and-diagnostics-using-azure-resource-manager-template"></a>Criar uma Máquina Virtual do Windows com a monitorização e diagnósticos de utilizar o modelo de Gestor de recursos do Azure

A extensão de diagnósticos do Azure fornece a monitorização e capacidades de diagnóstico num Windows com base no Azure máquina virtual. Pode ativar a estas capacidades na máquina virtual incluindo a extensão como parte do modelo de Gestor de recursos azure. Consulte o artigo [Criação de modelos de Gestor de recursos do Azure com as extensões de VM](virtual-machines-windows-extensions-authoring-templates.md) para mais informações sobre incluindo qualquer extensão como parte de um modelo de máquina virtual. Este artigo descreve como pode adicionar a extensão do Azure diagnósticos a um modelo de máquina virtual do windows.  
  

## <a name="add-the-azure-diagnostics-extension-to-the-vm-resource-definition"></a>Adicionar a extensão do Azure diagnósticos para a definição do recurso VM 

Para ativar a extensão de diagnóstico sobre um Windows Máquina Virtual tem de adicionar a extensão como um recurso VM no modelo de Gestor de recursos.

Para um Gestor de recursos simples Máquina Virtual baseada em Adicionar a configuração de extensão à matriz de *recursos* para a Máquina Virtual: 

    "resources": [
                {
                    "name": "Microsoft.Insights.VMDiagnosticsSettings",
                    "type": "extensions",
                    "location": "[resourceGroup().location]",
                    "apiVersion": "2015-06-15",
                    "dependsOn": [
                        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
                    ],
                    "tags": {
                        "displayName": "AzureDiagnostics"
                    },
                    "properties": {
                        "publisher": "Microsoft.Azure.Diagnostics",
                        "type": "IaaSDiagnostics",
                        "typeHandlerVersion": "1.5",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
                            "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
                        },
                        "protectedSettings": {
                            "storageAccountName": "[parameters('existingdiagnosticsStorageAccountName')]",
                            "storageAccountKey": "[listkeys(variables('accountid'), '2015-05-01-preview').key1]",
                            "storageAccountEndPoint": "https://core.windows.net"
                        }
                    }
                }
            ]


Outra convenção comum é pode adicionar a configuração de extensão no nó de recursos de raiz do modelo em vez de defini-lo em nó de recursos a máquina virtual. Com esta abordagem tem de especificar uma relação entre a extensão e a máquina virtual hierárquica explicitamente com os valores de *nome* e *tipo* . Por exemplo: 
  
    "name": "[concat(variables('vmName'),'Microsoft.Insights.VMDiagnosticsSettings')]",
    "type": "Microsoft.Compute/virtualMachines/extensions",

A extensão sempre está associada a máquina virtual, pode quer diretamente defini-la diretamente em nó do recurso a máquina virtual ou defini-la ao nível da base e utilize a Convenção de nomenclatura hierárquica para associá-lo a máquina virtual.

Para conjuntos de escala de Máquina Virtual a configuração de extensões é especificada na propriedade *extensionProfile* da *VirtualMachineProfile*.
   
A propriedade do *publisher* com o valor de **Microsoft.Azure.Diagnostics** e a propriedade *tipo* com o valor de **IaaSDiagnostics** identificar exclusivamente a extensão de diagnósticos do Azure.

O valor da propriedade *nome* pode ser utilizado para consultar a extensão no grupo de recursos. Defini-la especificamente para **Microsoft.Insights.VMDiagnosticsSettings** irá ativá-la facilmente ser verificada assegurando o Azure clássica portal portal que os gráficos de monitorização aparecem corretamente no portal do Azure clássico.

O *typeHandlerVersion* Especifica a versão da extensão que pretende utilizar. Definição *autoUpgradeMinorVersion* versão secundária **Verdadeiro** garante que irá obter a versão mais recente secundária da extensão que está disponível. Recomenda-se vivamente que o utilizador sempre defina *autoUpgradeMinorVersion* para estar sempre **true** para que sempre chegar ao utilizar a extensão de diagnóstico disponíveis mais recente com todas as novas funcionalidades e correções de erros. 

O elemento de *Definições* contém propriedades de configurações para a extensão que pode definir e lerem voltar a extensão (por vezes designado por configuração pública). A propriedade *xmlcfg* contém a configuração de xml com base nos registos de diagnóstico, contadores de desempenho etc serão recolhidas pelo agente de diagnóstico. Consulte o artigo [Esquema de configuração de diagnóstico](https://msdn.microsoft.com/library/azure/dn782207.aspx) para mais informações sobre o esquema xml própria. É prática comum armazenar a configuração de xml real como uma variável no modelo de Gestor de recursos do Azure e, em seguida, concatenar e base64 descodificá-los para definir o valor para *xmlcfg*. Consulte a secção sobre [variáveis de configuração de diagnóstico](#diagnostics-configuration-variables) para compreender melhor como armazenar xml em variáveis. A propriedade *storageAccount* Especifica o nome da conta de armazenamento à qual os dados de diagnóstico serão transferidos. 
 
As propriedades *protectedSettings* (por vezes designado por configuração privada) podem ser definidas mas não podem ser lidos novamente após a ser definido. Da natureza só de leitura do *protectedSettings* torna útil para armazenar segredos como a chave de conta de armazenamento onde serão escritos os dados de diagnóstico.    

## <a name="specifying-diagnostics-storage-account-as-parameters"></a>Especificar a conta de armazenamento de diagnóstico como parâmetros 

O fragmento de json diagnósticos extensão acima assume dois parâmetros *existingdiagnosticsStorageAccountName* e *existingdiagnosticsStorageResourceGroup* para especificar a conta de armazenamento de diagnóstico onde os dados de diagnóstico serão armazenados. Especificar a conta de armazenamento de diagnóstico, como um parâmetro torna mais fácil alterar a conta de armazenamento de diagnóstico em diferentes ambientes, por exemplo, poderá querer utilizar uma conta de armazenamento de diagnóstico diferentes para testar e um diferente para a sua implementação de produção.  

        "existingdiagnosticsStorageAccountName": {
            "type": "string",
            "metadata": {
        "description": "The name of an existing storage account to which diagnostics data will be transfered."
            }        
        },
        "existingdiagnosticsStorageResourceGroup": {
            "type": "string",
            "metadata": {
        "description": "The resource group for the storage account specified in existingdiagnosticsStorageAccountName"
            }
        }

É aconselhável para especificar uma conta de armazenamento de diagnóstico num grupo de recursos diferentes que o grupo de recursos para a máquina virtual. Um grupo de recursos pode ser considerado uma unidade de implementação com as suas próprias duração, uma máquina virtual podem ser implementada e novamente implementada como novas atualizações de configurações são efetuadas-lo ao mesmo, mas pretende continuar a armazenar os dados de diagnóstico na mesma conta de armazenamento nesses implementações máquina virtual. Está a ter a conta de armazenamento de um recurso de diferentes permite a conta de armazenamento aceitar dados a partir de vários máquina virtual híbridas, tornando mais fácil a resolução de problemas de várias versões.

>[AZURE.NOTE] Se criar um modelo de máquina virtual do windows a partir do Visual Studio a conta de armazenamento de predefinida pode estar definida para utilizar a mesma conta de armazenamento onde a máquina de virtual VHD é carregada. Este é para simplificar a configuração inicial da VM. Voltar deve factor o modelo para utilizar uma conta de armazenamento diferentes que pode ser passada no como um parâmetro. 

## <a name="diagnostics-configuration-variables"></a>Variáveis de configuração de diagnóstico
 
O fragmento de json diagnósticos extensão acima define uma variável de *accountid* para simplificar a introdução a chave de conta de armazenamento para o armazenamento de diagnóstico:   
    
    "accountid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',parameters('existingdiagnosticsStorageResourceGroup'), '/providers/','Microsoft.Storage/storageAccounts/', parameters('existingdiagnosticsStorageAccountName'))]"


A propriedade *xmlcfg* para a extensão de diagnóstico é definida utilizando várias variáveis encontram-se concatenados em conjunto. Os valores destas variáveis são em xml para que precisam de ser escape corretamente quando definir as variáveis json.

A seguinte tabela descreve o xml de configuração de diagnósticos recolhe contadores de desempenho ao nível do sistema padrão juntamente com alguns registos dos eventos do windows e registos de infraestrutura de diagnóstico. Foi escape e formatado corretamente para que a configuração diretamente possa ser colada na secção de variáveis do seu modelo. Consulte o artigo o [Esquema de configuração de diagnóstico](https://msdn.microsoft.com/library/azure/dn782207.aspx) para um exemplo legível mais humano de xml configuração.
    
        "wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
        "wadperfcounters1": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Privileged Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU privileged time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% User Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU user time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor Information(_Total)\\Processor Frequency\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"CPU frequency\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\System\\Processes\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Processes\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Threads\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Handle Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Handles\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\% Committed Bytes In Use\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Memory usage\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Available Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory available\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Committed Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory committed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Commit Limit\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory commit limit\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active time\" locale=\"en-us\"/></PerformanceCounterConfiguration>",
        "wadperfcounters2": "<PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Read Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active read time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Write Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active write time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Transfers/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Reads/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk read operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Writes/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk write operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Read Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk read speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Write Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk write speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\LogicalDisk(_Total)\\% Free Space\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk free space (percentage)\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
        "wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters1'), variables('wadperfcounters2'), '<Metrics resourceId=\"')]",
        "wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name , '/providers/', 'Microsoft.Compute/virtualMachines/')]",
        "wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>"

O nó de xml de definição de métricas na configuração acima é um elemento de configuração importantes à medida que define como os contadores de desempenho definidos anteriormente no xml no *PerformanceCounter* nó serão agregados e armazenados. 

> [AZURE.IMPORTANT] Estes métricas os gráficos de monitorização e alertas de unidade no portal do Azure.  O nó de **métricas** com a *resourceID* e **MetricAggregation** deve ser incluído na configuração de diagnóstico para sua VM se pretender ver os dados de monitorização VM no portal do Azure. 

Segue-se um exemplo de xml para definições de métricas: 

        <Metrics resourceId="/subscriptions/subscription().subscriptionId/resourceGroups/resourceGroup().name/providers/Microsoft.Compute/virtualMachines/vmName">
            <MetricAggregation scheduledTransferPeriod="PT1H"/>
            <MetricAggregation scheduledTransferPeriod="PT1M"/>
        </Metrics>

O atributo *resourceID* identifica máquina virtual na sua subscrição. Certifique-se utilizar as funções subscription() e resourceGroup() para que o modelo atualiza automaticamente esses valores com base no grupo de recursos que está a implementar e subscrição.

Se estiver a criar várias máquinas virtuais reproduzida continuamente terá de povoar o valor de *resourceID* com uma função copyIndex() para corretamente diferenciar cada VM individual. O valor de *xmlCfg* pode ser atualizado para suportar esta funcionalidade da seguinte forma:  

    "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), concat(parameters('vmNamePrefix'), copyindex()), variables('wadcfgxend')))]", 

O valor de MetricAggregation de *PT1H* e *PT1M* significam uma agregação através de um minuto e uma agregação através de uma hora.

## <a name="wadmetrics-tables-in-storage"></a>Tabelas de WADMetrics no armazenamento

A configuração de métricas acima irá gerar tabelas na sua conta de armazenamento de diagnóstico com as seguintes convenções de nomenclatura:

- **WADMetrics** : prefixo padrão para todas as tabelas de WADMetrics
- **PT1H** ou **PT1M** : se trata de que a tabela que contém agregar dados mais de 1 hora ou 1 minuto
- **P10D** : se trata de tabela irá conter dados para 10 dias a contar quando a tabela iniciado recolha de dados
- **V2S** : constante de cadeia
- **aaaammdd** : A data em que a tabela que começou a recolha de dados

Exemplo: *WADMetricsPT1HP10DV2S20151108* irá conter dados métricas agregados através de uma hora para 10 dias podendo 11 de Novembro de 2015    

Cada tabela WADMetrics irá conter as seguintes colunas:

- **PartitionKey**: O partitionkey é construída baseado no valor *resourceID* para identificar exclusivamente o recurso VM. para, por exemplo,: 002Fsubscriptions:<subscriptionID>: 002FresourceGroups:002F<ResourceGroupName>: 002Fproviders:002FMicrosoft:002ECompute:002FvirtualMachines:002F<vmName>  
- **RowKey** : segue o formato <Descending time tick>:<Performance Counter Name>. O cálculo de escala de tempo descendente é marcas de escala de tempo máximo menos a hora de início do período de agregação. Por exemplo Se o período de amostra iniciada 10-Novembro de 2015 e 00:00Hrs UTC, em seguida, o cálculo seria: DateTime.MaxValue.Ticks - (DateTime(2015,11,10,0,0,0,DateTimeKind.Utc) novo. Marcas de escala). Para será o aspeto da memória a chave de linha de contador de desempenho de bytes disponíveis como: 2519551871999999999__:005CMemory:005CAvailable:0020 Bytes
- **CounterName** : É o nome do contador de desempenho. O que corresponde a *counterSpecifier* definidas na tabela xml config.
- **Máximo** : O valor máximo de contador de desempenho durante o período de agregação.
- **Mínimo** : O valor mínimo de contador de desempenho durante o período de agregação.
- **Total** : comunicado a soma de todos os valores de contador de desempenho durante o período de agregação.
- **Contar** : O número total de valores comunicados para o contador de desempenho.
- **Média** : O valor médio (total/contagem) de contador de desempenho durante o período de agregação.


## <a name="next-steps"></a>Próximos passos

- Para um modelo de exemplo completos de uma máquina de virtual do Windows com a extensão de diagnóstico consulte [201-vm-monitorização-diagnósticos-extensão](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-monitoring-diagnostics-extension)   
- Implementar o modelo de Gestor de recursos utilizando o [PowerShell do Azure](virtual-machines-windows-ps-manage.md) ou [linha de comandos do Azure](virtual-machines-linux-cli-deploy-templates.md)
- Saiba mais sobre a [criação de modelos de Gestor de recursos do Azure](../resource-group-authoring-templates.md)







