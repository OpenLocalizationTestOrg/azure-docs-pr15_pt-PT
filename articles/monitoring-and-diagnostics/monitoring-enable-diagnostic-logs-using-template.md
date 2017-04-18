<properties
    pageTitle="Activar automaticamente definições de diagnóstico através de um modelo de Gestor de recursos | Microsoft Azure"
    description="Saiba como utilizar um modelo de Gestor de recursos para criar as definições de diagnóstico permitem-lhe transmitir em fluxo os registos de diagnóstico para evento concentradores ou armazená-los numa conta de armazenamento."
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

# <a name="automatically-enable-diagnostic-settings-at-resource-creation-using-a-resource-manager-template"></a>Ativar automaticamente definições de diagnóstico na criação de recursos utilizando um modelo de Gestor de recursos
Este artigo mostramos como pode utilizar um [modelo de Gestor de recursos do Azure](../resource-group-authoring-templates.md) para configurar definições de diagnóstico sobre um recurso quando é criado. Permite-lhe iniciar automaticamente o transmissão os seus registos de diagnóstico e métricas para evento concentradores, arquivá-los numa conta armazenamento ou enviar-lhes para a análise de registo de quando é criado um recurso.

O método para ativar a registos de diagnóstico através de um modelo de Gestor de recursos depende do tipo de recurso.

- Recursos de **Cluster não** (por exemplo, grupos de segurança de rede, aplicações de lógica, automatização) utilizarem [definições de diagnóstico descritas neste artigo](./monitoring-overview-of-diagnostic-logs.md#diagnostic-settings).
- **Calcular** Recursos (WAD/espertalhão qualquer baseado) utilizam o [ficheiro de configuração de WAD/espertalhão qualquer descrito neste artigo](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md).

Neste artigo, iremos descrevem como configurar diagnósticos utilizando qualquer método indicado.

Os passos básicos são da seguinte forma:

1. Crie um modelo como um ficheiro JSON que descreve como criar o recurso e activar diagnósticos.
2. [Implementar o modelo utilizando qualquer método de implementação](../resource-group-template-deploy.md).

Abaixo estamos dar um exemplo de JSON do ficheiro de modelo que precisa para gerar para que não sejam cluster e recursos de cluster.

## <a name="non-compute-resource-template"></a>Modelo de cluster de não recurso
Para obter recursos de que não sejam cluster, terá de efetuar dois procedimentos:

1. Adicione parâmetros ao blob de parâmetros para o nome da conta de armazenamento, ID da regra bus serviço e/ou ID de área de trabalho OMS registo Analytics (activar arquivo de registos de diagnóstico numa conta de armazenamento, transmissão de registos para evento concentradores e/ou enviar registos de início para a análise de registo).

    ```json
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "serviceBusRuleId": {
      "type": "string",
      "metadata": {
        "description": "Service Bus Rule Id for the Service Bus Namespace in which the Event Hub should be created or streamed to."
      }
    },
    "workspaceId":{
      "type": "string",
      "metadata": {
        "description": "Log Analytics workspace ID for the Log Analytics workspace to which logs will be sent."
      }
    }
    ```
2. Na matriz recursos do recurso para o qual pretende ativar registos de diagnóstico, adicionar um recurso do tipo de `[resource namespace]/providers/diagnosticSettings`.

    ```json
    "resources": [
      {
        "type": "providers/diagnosticSettings",
        "name": "Microsoft.Insights/service",
        "dependsOn": [
          "[/*resource Id for which Diagnostic Logs will be enabled>*/]"
        ],
        "apiVersion": "2015-07-01",
        "properties": {
          "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
          "serviceBusRuleId": "[parameters('serviceBusRuleId')]",
          "workspaceId": "[parameters('workspaceId')]",
          "logs": [ 
            {
              "category": "/* log category name */",
              "enabled": true,
              "retentionPolicy": {
                "days": 0,
                "enabled": false
              }
            }
          ]
        }
      }
    ]
    ```

O blob propriedades para a definição de diagnóstico segue [o formato é descrito neste artigo](https://msdn.microsoft.com/library/azure/dn931931.aspx).

Eis um exemplo completo que cria um grupo de segurança de rede e ativa da transmissão concentradores de evento e armazenamento de uma conta de armazenamento.

```json

{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "nsgName": {
      "type": "string",
      "metadata": {
        "description": "Name of the NSG that will be created."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "serviceBusRuleId": {
      "type": "string",
      "metadata": {
        "description": "Service Bus Rule Id for the Service Bus Namespace in which the Event Hub should be created or streamed to."
      }
    },
    "workspaceId": {
      "type": "string",
      "metadata": {
        "description": "Log Analytics workspace ID for the Log Analytics workspace to which logs will be sent."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Network/networkSecurityGroups",
      "name": "[parameters('nsgName')]",
      "apiVersion": "2016-03-30",
      "location": "westus",
      "properties": {
        "securityRules": []
      },
      "resources": [
        {
          "type": "providers/diagnosticSettings",
          "name": "Microsoft.Insights/service",
          "dependsOn": [
            "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('nsgName'))]"
          ],
          "apiVersion": "2015-07-01",
          "properties": {
            "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
            "serviceBusRuleId": "[parameters('serviceBusRuleId')]",
            "workspaceId": "[parameters('workspaceId')]",
            "logs": [
              {
                "category": "NetworkSecurityGroupEvent",
                "enabled": true,
                "retentionPolicy": {
                  "days": 0,
                  "enabled": false
                }
              },
              {
                "category": "NetworkSecurityGroupRuleCounter",
                "enabled": true,
                "retentionPolicy": {
                  "days": 0,
                  "enabled": false
                }
              }
            ]
          }
        }
      ],
      "dependsOn": []
    }
  ]
}

```

## <a name="compute-resource-template"></a>Calcular o modelo de recursos
Para ativar diagnósticos num recurso cluster, por exemplo um cluster de Máquina Virtual ou serviço ferro, tem de:

1. Adicione a extensão do Azure diagnósticos para a definição do recurso VM.
2. Especifique um concentrador de conta e/ou evento de armazenamento como um parâmetro.
3. Adicione o conteúdo do seu ficheiro WADCfg XML na caixa da propriedade XMLCfg, perda todos os carateres XML corretamente.

> [AZURE.WARNING] Neste último passo pode ser difícil aceder à direita. [Consulte este artigo](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md#diagnostics-configuration-variables) para obter um exemplo que divide o esquema de configuração de diagnóstico em variáveis que são escape e formatadas corretamente.

Todo o processo, incluindo as amostras, é descrito [neste documento](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md).


## <a name="next-steps"></a>Próximos passos
- [Leia mais sobre registos de diagnóstico do Azure](./monitoring-overview-of-diagnostic-logs.md)
- [Transmitir em fluxo Azure registos de diagnóstico para concentradores de evento](./monitoring-stream-diagnostic-logs-to-event-hubs.md)
