<properties
   pageTitle="Recolher registos utilizando os diagnósticos do Azure | Microsoft Azure"
   description="Este artigo descreve como configurar o Azure diagnósticos para recolher registos a partir de um cluster de serviço ferro em execução no Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="ms-toddabel"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/28/2016"
   ms.author="toddabel"/>


# <a name="collect-logs-by-using-azure-diagnostics"></a>Recolher registos utilizando os diagnósticos do Azure

> [AZURE.SELECTOR]
- [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
- [Linux](service-fabric-diagnostics-how-to-setup-lad.md)

Quando estiver a executar um cluster de Azure Service ferro, é aconselhável recolher os registos de todos os nós numa localização central. Está a ter os registos numa localização central ajuda-o a analisar e resolver problemas no seu cluster ou problemas nas aplicações e serviços a ser executada em que o cluster.

Uma forma de carregar e recolher registos é utilizar a extensão de diagnósticos do Azure, carrega registos de armazenamento do Windows Azure. Os registos não estão nesse útil diretamente no armazenamento. Mas pode utilizar um processo externo para ler os eventos a partir do armazenamento e colocá-los num produto como [A análise de registo](../log-analytics/log-analytics-service-fabric.md), [Pesquisa flexível](service-fabric-diagnostic-how-to-use-elasticsearch.md)ou outra solução de registo de análise.

## <a name="prerequisites"></a>Pré-requisitos
Irá utilizar estas ferramentas para efetuar algumas das operações neste documento:

* [Diagnósticos do Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) (relacionado com aos serviços em nuvem Azure, mas tem boas informações e exemplos)
* [Gestor de recursos do Azure](../azure-resource-manager/resource-group-overview.md)
* [Azure PowerShell](../powershell-install-configure.md)
* [Azure cliente do Gestor de recursos](https://github.com/projectkudu/ARMClient)
* [Modelo do Gestor de recursos Azure](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)


## <a name="log-sources-that-you-might-want-to-collect"></a>Origens de registo que poderá pretender recolher
- **Registos de serviço ferro**: emitida a partir da plataforma de canais de evento rastreio para Windows (ETW) e Origemdoevento padrão. Registos podem ser um de vários tipos de:
  - Os eventos operacionais: os registos para operações de que efetua a plataforma de serviço ferro. Alguns exemplos incluem a criação de aplicações e serviços, alterações de estado de nó e informações de atualização.
  - [Intervenientes fiáveis eventos do modelo de programação](service-fabric-reliable-actors-diagnostics.md)
  - [Serviços fiáveis eventos do modelo de programação](service-fabric-reliable-services-diagnostics.md)
- **Eventos de aplicações**: eventos emitida de código do seu serviço e escritas utilizando a classe de helper Origemdoevento fornecida nos modelos Visual Studio. Para mais informações sobre como escrever registos a partir da sua aplicação, consulte o artigo [Monitor e diagnosticar serviços numa configuração de desenvolvimento do computador local](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).


## <a name="deploy-the-diagnostics-extension"></a>Implementar a extensão de diagnóstico
É o primeiro passo no recolher registos implementar a extensão de diagnóstico em cada uma das VMs no cluster ferro de serviço. A extensão de diagnóstico recolhe registos em cada VM e os carregamentos pendentes para a conta de armazenamento que especificar. Os passos variam ligeiramente consoante se utiliza o Azure portal ou Gestor de recursos do Azure. Os passos também podem variar com base no se a implementação faz parte de criação do cluster ou é para um cluster de que já existe. Observemos os passos para cada cenário.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-the-portal"></a>Implementar a extensão de diagnóstico como parte da criação do cluster através do portal
Para implementar a extensão de diagnóstico VMs no cluster como parte da criação do cluster, utilize o painel de definições de diagnósticos apresentado na seguinte imagem. Para ativar intervenientes fiáveis ou serviços fiável coleção de evento, certifique-se de que diagnósticos está definido para **no** (predefinição). Depois de criar o cluster, não é possível alterar estas definições utilizando o portal.

![Definições dos diagnósticos do Azure no portal para a criação de cluster](./media/service-fabric-diagnostics-how-to-setup-wad/portal-cluster-creation-diagnostics-setting.png)

Inicia o Azure suporte equipa *requer o* suporte para ajudar a resolver quaisquer pedidos de suporte que criar. Estes registos são armazenados das contas de armazenamento criadas no grupo de recursos e são recolhidos em tempo real. As definições de diagnósticos configurar eventos de nível de aplicação. Estes eventos incluem [Intervenientes fiável](service-fabric-reliable-actors-diagnostics.md) eventos, eventos de [Serviços fiáveis](service-fabric-reliable-services-diagnostics.md) e algumas eventos de ferro de serviço de nível do sistema para serem armazenados em armazenamento do Windows Azure.

Produtos, como [Pesquisa flexível](service-fabric-diagnostic-how-to-use-elasticsearch.md) ou o seu próprio processo podem obter os eventos da conta de armazenamento. Atualmente, não existe nenhuma forma para filtrar ou forma os eventos que são enviados para a tabela. Se não implementar um processo para remover eventos a partir da tabela, a tabela irão continuar a aumentar.

Quando estiver a criar um cluster utilizando o portal, recomendamos vivamente que transferir o modelo *antes de clicar em * *OK** * para criar o cluster. Para obter mais detalhes, consulte [configurar um cluster de serviço ferro utilizando um modelo de Gestor de recursos do Azure](service-fabric-cluster-creation-via-arm.md). Terá o modelo para fazer alterações mais tarde, pois não é possível fazer algumas alterações utilizando o portal.

Pode exportar modelos a partir do portal através dos seguintes passos. No entanto, estes modelos podem ser mais difícil utilizar uma vez que estes podem ter valores nulos aos quais faltam informações necessárias.

1. Abra o seu grupo de recursos.
2. Selecione **as definições** para apresentar o painel de definições.
3. Selecione **híbridas** , para apresentar o painel de histórico de implementação.
4. Selecione uma implementação para apresentar os detalhes da implementação.
5. Selecione **Exportar modelo** para apresentar o painel de modelo.
6. Selecione **Guardar ficheiro** para exportar um ficheiro. zip que contém o modelo, parâmetro e ficheiros de PowerShell.

Depois de exportar os ficheiros, tem de fazer uma modificação. Editar o ficheiro parameters.json e remova o elemento **adminPassword** . Isto vai originar um pedido para a palavra-passe ao script de implementação é executado. Quando estiver a executar o script de implementação, poderá ter que corrigir valores de parâmetros nulo.

Para utilizar o modelo transferido para atualizar uma configuração:

1. Extrai o conteúdo para uma pasta no seu computador local.
2. Modifica o conteúdo para refletir a configuração da nova.
3. Inicie o PowerShell e mude para a pasta onde o conteúdo que extraída.
4. Executar **deploy.ps1** e preencha o ID da subscrição, o nome do grupo de recursos (utilize o mesmo nome para a configuração de atualizar) e um nome exclusivo implementação.


### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-by-using-azure-resource-manager"></a>Implementar a extensão de diagnóstico como parte da criação do cluster utilizando o Gestor de recursos do Azure
Para criar um cluster utilizando o Gestor de recursos, é necessário adicionar a configuração de diagnósticos JSON ao modelo de Gestor de recursos de cluster completa antes de criar o cluster. Fornecemos um modelo de Gestor de recursos do exemplo cinco VM cluster configuração de diagnósticos adicionada como parte do nossas amostras de modelo de Gestor de recursos. Pode vê-la neste localização na Galeria de amostras Azure: [cluster de cinco nó com o Gestor de recursos dos diagnósticos do exemplo de modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad).

Para ver a definição de diagnóstico no modelo de Gestor de recursos, abra o ficheiro de azuredeploy.json e procurar **IaaSDiagnostics**. Para criar um cluster utilizando este modelo, selecione o botão de **implementar para Azure** disponível a ligação anterior.

Em alternativa, pode transferir a amostra de Gestor de recursos, efetuar alterações ao mesmo e criar um cluster com o modelo modificado utilizando o `New-AzureRmResourceGroupDeployment` comando numa janela do Azure PowerShell. Ver o código seguinte para os parâmetros que decorrer ao comando. Para obter informações detalhadas sobre como implementar um grupo de recursos utilizando o PowerShell, consulte o artigo [Implementar um grupo de recursos com o modelo de Gestor de recursos do Azure](../resource-group-template-deploy.md).

```powershell

New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToARMConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

### <a name="deploy-the-diagnostics-extension-to-an-existing-cluster"></a>Implementar a extensão de diagnóstico para um cluster existente
Se tiver um cluster existente que não tenha diagnósticos implementados ou se pretende modificar uma configuração existente, pode adicionar ou atualizar. Modifica o modelo de Gestor de recursos utilizados para criar o cluster existente ou transferir o modelo a partir do portal conforme descrito anteriormente. Modificar o ficheiro template.json ao efetuar as seguintes tarefas.

Adicione um novo recurso de armazenamento para o modelo ao adicionar a secção de recursos.

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "properties": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 Em seguida, adicionar a secção de parâmetros apenas depois das definições de conta de armazenamento, entre `supportLogStorageAccountName` e `vmNodeType0Name`. Substitua o texto de marcador de posição, *nome de conta de armazenamento aqui* o nome da conta de armazenamento.

```json
    "applicationDiagnosticsStorageAccountType": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "Replication option for the application diagnostics storage account"
      }
    },
    "applicationDiagnosticsStorageAccountName": {
      "type": "string",
      "defaultValue": "storage account name goes here",
      "metadata": {
        "description": "Name for the storage account that contains application diagnostics data from the cluster"
      }
    },
```
Em seguida, atualize o `VirtualMachineProfile` secção do ficheiro template.json ao adicionar o código seguinte na matriz extensões. Certifique-se de que adicionar um ponto e vírgula no início ou fim, dependendo de onde é inserido.

```json
{
    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
    "properties": {
        "type": "IaaSDiagnostics",
        "autoUpgradeMinorVersion": true,
        "protectedSettings": {
        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
        "storageAccountEndPoint": "https://core.windows.net/"
        },
        "publisher": "Microsoft.Azure.Diagnostics",
        "settings": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": "50000",
            "EtwProviders": {
                "EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
                {
                    "provider": "Microsoft-ServiceFabric-Services",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableServiceEventTable"
                    }
                }
                ],
                "EtwManifestProviderConfiguration": [
                {
                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                    "scheduledTransferLogLevelFilter": "Information",
                    "scheduledTransferKeywordFilter": "4611686018427387904",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricSystemEventTable"
                    }
                }
                ]
            }
            }
        },
        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
        },
        "typeHandlerVersion": "1.5"
    }
}
```

Depois de modificar o ficheiro template.json, tal como descrito, volte a publicar o modelo de Gestor de recursos. Se o modelo foi exportado, executar o ficheiro deploy.ps1 bloqueou voltar a publicar o modelo. Depois de implementar, certifique-se de que **ProvisioningState** é **foi concluída com êxito**.


## <a name="update-diagnostics-to-collect-and-upload-logs-from-new-eventsource-channels"></a>Atualizar diagnósticos para recolher e carregar registos de novos Origemdoevento canais
Para atualizar diagnósticos para recolher registos de canais Origemdoevento novos que representam uma nova aplicação que está prestes a implementar, execute os mesmos passos tal como a [secção anterior](#deploywadarm) para o programa de configuração de diagnóstico para um cluster existente.

Atualizar a `EtwEventSourceProviderConfiguration` secção no ficheiro template.json para adicionar entradas para os canais Origemdoevento novos antes de aplicar a configuração de atualizar utilizando o `New-AzureRmResourceGroupDeployment` comando PowerShell. O nome da origem de evento está definido como parte do seu código no ficheiro ServiceEventSource.cs gerados pelo Visual Studio.

Por exemplo, se a origem do evento se chamar meus Origemdoevento, adicione o seguinte código para colocar os eventos a partir do meu Origemdoevento numa tabela denominada MyDestinationTableName.

```json
        {
            "provider": "My-Eventsource",
            "scheduledTransferPeriod": "PT5M",
            "DefaultEvents": {
            "eventDestination": "MyDestinationTableName"
            }
        }
```

Para recolher contadores de desempenho ou registos de eventos, modifique o modelo de Gestor de recursos utilizando os exemplos fornecidos no [artigo criar uma máquina de virtual do Windows com a monitorização e diagnósticos de utilizando um modelo de Gestor de recursos do Azure](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md). Em seguida, volte a publicar o modelo de Gestor de recursos.

## <a name="next-steps"></a>Próximos passos
Para compreender mais detalhadamente que eventos que deverá ter o aspeto durante a resolução de problemas, consulte os eventos de diagnóstico emitidos para [Serviços fiável](service-fabric-reliable-services-diagnostics.md)e [Intervenientes fiável](service-fabric-reliable-actors-diagnostics.md) .


## <a name="related-articles"></a>Artigos relacionados
* [Saiba como recolher contadores de desempenho ou registos de utilizando a extensão de diagnóstico](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)
* [Solução de serviço ferro no registo de análise](../log-analytics/log-analytics-service-fabric.md)
