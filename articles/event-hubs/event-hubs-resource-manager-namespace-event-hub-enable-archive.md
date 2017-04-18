<properties
    pageTitle="Criar um espaço de nomes do evento concentradores com concentrador de evento e ativar o arquivo utilizando um modelo de Gestor de recursos do Azure | Microsoft Azure"
    description="Criar um espaço de nomes do evento concentradores com concentrador de evento e ativar o arquivo utilizando o modelo de Gestor de recursos do Azure"
    services="event-hubs"
    documentationCenter=".net"
    authors="ShubhaVijayasarathy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="09/14/2016"
    ms.author="ShubhaVijayasarathy"/>

# <a name="create-an-event-hubs-namespace-with-event-hub-and-enable-archive-using-an-azure-resource-manager-template"></a>Criar um espaço de nomes do evento concentradores com concentrador de evento e ativar o arquivo utilizando um modelo de Gestor de recursos do Azure

Este artigo mostra como utilizar um modelo de Gestor de recursos do Azure que cria um espaço de nomes do evento concentradores com um concentrador de evento e permite o arquivo no seu centro de evento. Saiba como definir quais os recursos são implementados e como definir parâmetros que estão especificados quando a implementação é executada. Pode utilizar este modelo para a suas própria implementações ou personalizá-lo aos seus requisitos

Para obter mais informações sobre a criação de modelos, consulte [Gestor de recursos do Azure criação de modelos][].

Para mais informações sobre as práticas e os padrões de recursos do Azure convenções de nomenclatura, consulte o artigo [Azure convenções de nomenclatura de recursos][].

Para o modelo concluído, consulte o artigo o [concentrador de evento e ativar o modelo de arquivo][] GitHub.

>[AZURE.NOTE]
>Para verificar os modelos mais recentes, visite a Galeria de [Modelos de guia de introdução do Azure][] e procurar concentradores do evento.

## <a name="what-you-deploy"></a>O que implementar?

Com este modelo, implementar um espaço de nomes do evento concentradores com um concentrador de evento e ativar o arquivo.

[Evento concentradores](../event-hubs/event-hubs-what-is-event-hubs.md) é um evento serviço utilizado para fornecer penetração evento e telemetria Azure na escala grandes, com baixa latência e fiabilidade alta de processamento. Evento concentradores arquivo permite-lhe entregar automaticamente os dados de transmissão no seu concentradores evento ao armazenamento de Blobs do Azure da sua preferência dentro de um período de tempo especificado ou o intervalo de tamanho da sua escolha.

Para executar a implementação automaticamente, clique no botão seguinte:

[![Implementar Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-archive%2Fazuredeploy.json)

## <a name="parameters"></a>Parâmetros

Com o Gestor de recursos do Azure, pode definir parâmetros para valores que pretende especificar quando o modelo é implementado. O modelo inclui uma secção denominada `Parameters` que contém todos os valores de parâmetro. Deve definir um parâmetro para esses valores variar com base no projeto que está a implementar ou com base no ambiente de que está a implementar. Não defina parâmetros para valores que permaneçam sempre o mesmo. Para definir os recursos que são implementados, cada valor do parâmetro é utilizado no modelo.

O modelo define seguintes parâmetros.

### <a name="eventhubnamespacename"></a>eventHubNamespaceName

O nome do evento concentradores espaço de nomes para criar.

```
"eventHubNamespaceName":{  
     "type":"string",
     "metadata":{  
         "description":"Name of the EventHub namespace"
      }
}
```

### <a name="eventhubname"></a>eventHubName

O nome do centro do evento de evento criado concentradores espaço de nomes.

```
"eventHubName":{  
    "type":"string",
    "metadata":{  
        "description":"Name of the Event Hub"
    }
}
```

### <a name="messageretentionindays"></a>messageRetentionInDays

O número de dias que pretende que as mensagens retidas no seu centro de evento. 

```
"messageRetentionInDays":{
    "type":"int",
    "defaultValue": 1,
    "minValue":"1",
    "maxValue":"7",
    "metadata":{
       "description":"How long to retain the data in Event Hub"
     }
 }
```

### <a name="partitioncount"></a>partitionCount

O número de partições que pretende no seu centro de evento.

```
"partitionCount":{
    "type":"int",
    "defaultValue":2,
    "minValue":2,
    "maxValue":32,
    "metadata":{
        "description":"Number of partitions chosen"
    }
 }
```

### <a name="archiveenabled"></a>archiveEnabled

Ative o arquivo no seu centro de evento.

```
"archiveEnabled":{
    "type":"string",
    "defaultValue":"true",
    "allowedValues": [
    "false",
    "true"],
    "metadata":{
        "description":"Enable or disable the Archive for your Event Hub"
    }
 }
```
### <a name="archiveencodingformat"></a>archiveEncodingFormat

O formato de codificação que especificar serializar os dados do evento.

```
"archiveEncodingFormat":{
    "type":"string",
    "defaultValue":"Avro",
    "allowedValues":[
    "Avro"],
    "metadata":{
        "description":"The encoding format Archive serializes the EventData"
    }
}
```

### <a name="archivetime"></a>archiveTime

O intervalo de tempo no qual o arquivo é iniciado arquivar os dados no armazenamento de Blobs do Azure.

```
"archiveTime":{
    "type":"int",
    "defaultValue":300,
    "minValue":60,
    "maxValue":900,
    "metadata":{
         "description":"the time window in seconds for the archival"
    }
}
```

### <a name="archivesize"></a>archiveSize

O intervalo de tamanho no qual o arquivo é iniciado arquivar os dados no armazenamento de Blobs do Azure.

```
"archiveSize":{
    "type":"int",
    "defaultValue":314572800,
    "minValue":10485760,
    "maxValue":524288000,
    "metadata":{
        "description":"the size window in bytes for archival"
    }
}
```

### <a name="destinationstorageaccountresourceid"></a>destinationStorageAccountResourceId

O arquivo requer um id do recurso conta de armazenamento, para ativar o arquivo ao seu armazenamento do Azure pretendida.

```
 "destinationStorageAccountResourceId":{
    "type":"string",
    "metadata":{
        "description":"Your existing storage Account resource id where you want the blobs be archived"
    }
 }
```

### <a name="blobcontainername"></a>blobContainerName

O contentor de BLOBs onde pretende que os dados de evento ser arquivados.

```
 "blobContainerName":{
    "type":"string",
    "metadata":{
        "description":"Your existing storage Container that you want the blobs archived in"
    }
}
```


### <a name="apiversion"></a>apiVersion

A versão da API do modelo.

```
 "apiVersion":{  
    "type":"string",
    "defaultValue":"2015-08-01",
    "metadata":{  
        "description":"ApiVersion used by the template"
    }
 }
```

## <a name="resources-to-deploy"></a>Recursos para implementar

Cria um espaço de nomes do tipo **EventHubs**, com um concentrador de evento e permite-arquivo.

```
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('eventHubNamespaceName')]",
         "type":"Microsoft.EventHub/Namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources":[  
            {  
               "apiVersion":"[variables('ehVersion')]",
               "name":"[parameters('eventHubName')]",
               "type":"EventHubs",
               "dependsOn":[  
                  "[concat('Microsoft.EventHub/namespaces/', parameters('eventHubNamespaceName'))]"
               ],
               "properties":{  
                  "path":"[parameters('eventHubName')]",
                  "MessageRetentionInDays":"[parameters('messageRetentionInDays')]",
                  "PartitionCount":"[parameters('partitionCount')]",
                  "ArchiveDescription":{
                        "enabled":"[parameters('archiveEnabled')]",
                        "encoding":"[parameters('archiveEncodingFormat')]",
                        "intervalInSeconds":"[parameters('archiveTime')]",
                        "sizeLimitInBytes":"[parameters('archiveSize')]",
                        "destination":{
                            "name":"EventHubArchive.AzureBlockBlob",
                            "properties":{
                                "StorageAccountResourceId":"[parameters('destinationStorageAccountResourceId')]",
                                "BlobContainer":"[parameters('blobContainerName')]"
                            }
                        } 
                  }
                  
               }
               
            }
         ]
      }
   ]
```

## <a name="commands-to-run-deployment"></a>Comandos para executar a implementação

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-archive/azuredeploy.json
```

## <a name="azure-cli"></a>Clip Azure

```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-archive/azuredeploy.json][]
```

[Criação de modelos de Gestor de recursos do Azure]: ../resource-group-authoring-templates.md
[Guia de introdução Azure modelos]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event Hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-eventhubs-create-namespace-and-enable-archive/
[Recursos Azure convenções de nomenclatura]: https://azure.microsoft.com/en-us/documentation/articles/guidance-naming-conventions/
[Concentrador de evento e ativar o modelo de arquivo]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-archive
