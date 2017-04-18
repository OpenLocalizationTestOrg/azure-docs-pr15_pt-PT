<properties
   pageTitle="Recursos de automatização no soluções OMS | Microsoft Azure"
   description="As soluções no OMS normalmente irão incluir runbooks no Azure automatização para automatizar processos de recolha e processamento de dados de monitorização.  Este artigo descreve como incluir runbooks e dos seus recursos relacionados uma solução."
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/19/2016"
   ms.author="bwren" />

# <a name="automation-resources-in-oms-solutions-preview"></a>Recursos de automatização no soluções OMS (pré-visualização)

>[AZURE.NOTE]Este é prévia documentação para criar soluções de gestão no OMS que estão atualmente em pré-visualização. Qualquer esquema descrita abaixo está sujeito a alterações.   

[Soluções de gestão de OMS](operations-management-suite-solutions.md) normalmente irá incluir runbooks no Azure automatização para automatizar processos de recolha e processamento de dados de monitorização.  Para além de runbooks, contas de automatização inclui elementos como variáveis e agendas que suportam runbooks utilizado na solução.  Este artigo descreve como incluir runbooks e dos seus recursos relacionados uma solução.

>[AZURE.NOTE]Os exemplos neste artigo utilizam parâmetros e variáveis que são necessários ou comuns para as soluções de gestão e descrito na [soluções de gestão de criar no conjunto de aplicações de gestão de operações (OMS)](operations-management-suite-solutions-creating.md) 


## <a name="prerequisites"></a>Pré-requisitos
Este artigo assume já está familiarizado a como para [criar uma solução de gestão](operations-management-suite-solutions-creating.md) e a estrutura de um ficheiro de solução.

## <a name="samples"></a>Amostras
Pode obter modelos de Gestor de recursos de exemplo para recursos de automatização dos [modelos de guia de introdução no GitHub](https://github.com/azureautomation/automation-packs/tree/master/101-sample-automation-resource-templates).

## <a name="automation-account"></a>Conta de automatização
Todos os recursos no Azure automatização estão contidos numa [conta de automatização](../automation/automation-security-overview.md#automation-account-overview).  Conforme descrito na [área de trabalho OMS e a conta de automatização](operations-management-suite-solutions-creating.md#oms-workspace-and-automation-account) a conta de automatização não está incluída no solução de gestão do, mas tem de existir antes da solução está instalada.  Se não estiver disponível, em seguida, a instalação de solução falhará.

É o nome da sua conta de automatização na nome a cada recurso de automatização.  Isto é feito da solução com o parâmetro **accountName** tal como no exemplo seguinte de um recurso de livro execuções.
    
    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Runbooks
Recursos do [livro de execuções do Azure automatização](../automation/automation-runbook-types.md) tenham um tipo de **Microsoft.Automation/automationAccounts/runbooks** e tem as propriedades da tabela seguinte.

| Propriedade | Descrição |
|:--|:--|
| runbookType | Especifica os tipos de livro de execuções. <br><br> Script - PowerShell script <br>PowerShell - PowerShell fluxo de trabalho <br> GraphPowerShell - livro de execuções do PowerShell gráficas script <br> GraphPowerShellWorkflow - livro de execuções do PowerShell gráficas fluxo de trabalho   |
| logProgress | Especifica se deve ser gerado [registos de progresso](../automation/automation-runbook-output-and-messages.md) para o livro de execuções. |
| logVerbose  | Especifica se deve ser gerado [verbosas registos](../automation/automation-runbook-output-and-messages.md) para o livro de execuções. |
| Descrição | Descrição opcional para o livro de execuções. |
| publishContentLink | Especifica o conteúdo do livro de execuções. <br><br>URI - Uri o conteúdo do livro de execuções.  Este será um ficheiro. ps1 para runbooks PowerShell e Script e um ficheiro de livro de execuções gráficas exportado para um livro de execuções do gráfico.  <br> versão - versão do livro de execuções para sua própria controlo. |

Um exemplo de um recurso de livro execuções está abaixo.  Neste caso, obtém o livro de execuções a partir da [Galeria do PowerShell](https://www.powershellgallery.com).

    "name": "[concat(parameters('accountName'), '/Start-AzureV2VMs'))]",
    "type": "Microsoft.Automation/automationAccounts/runbooks",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "location": "[parameters('regionId')]",
    "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('startVmsRunbookName'))]"
    ],
    "tags": { },
    "properties": {
        "runbookType": "PowerShell",
        "logProgress": "true",
        "logVerbose": "true",
        "description": "",
        "publishContentLink": {
            "uri": "https://www.powershellgallery.com/api/v2/items/psscript/package/Update-ModulesInAutomationToLatestVersion/1.0/Start-AzureV2VMs.ps1",
            "version": "1.0"
        }
    }

### <a name="starting-a-runbook"></a>Iniciar um livro de execuções
Existem dois métodos para iniciar um livro de execuções numa solução de gestão.

- Para iniciar o livro de execuções quando a solução é instalada, crie um [recurso de trabalho](#automation-jobs) , conforme descrito abaixo.
- Para iniciar o livro de execuções numa agenda, crie um [recurso de agenda](#schedules) conforme descrito abaixo. 


## <a name="automation-jobs"></a>Tarefas de automatização
Para iniciar um livro de execuções quando a solução de gestão está instalada, crie um recurso de **trabalho** .  Recursos de projecto tenham um tipo de **Microsoft.Automation/automationAccounts/jobs** e tem as propriedades da tabela seguinte.

| Propriedade | Descrição |
|:--|:--|
| livro execuções    | **Nome** entidade com o nome do livro de execuções.  |
| parâmetros | Entidade para cada parâmetro uma ferramenta necessária ao livro de execuções. |


A tarefa inclui o nome do livro execuções e os valores de parâmetros sejam enviadas para o livro de execuções.  A tarefa tem [dependem](operations-management-suite-solutions-creating.md#resources) do livro de execuções que está a ser iniciado desde o livro de execuções têm de ser criado antes da tarefa.  Pode também criar dependências outras tarefas de runbooks que devem ser concluídas antes da atual.

O nome de um recurso de trabalho tem de conter um GUID que é normalmente atribuído por um parâmetro.  Pode ler mais acerca dos parâmetros GUID em [criar soluções no conjunto de aplicações de gestão de operações (OMS)](operations-management-suite-solutions-creating.md#parameters).  

Segue-se um exemplo de um recurso de trabalho que inicia um livro de execuções quando a solução de gestão está instalada.  Um outras runbooks têm de ser concluídos antes de este um é iniciado, para que este tenha dependências relativamente às tarefas para esse livro de execuções.  Os detalhes para a tarefa são fornecidos através de parâmetros e variáveis em vez de a ser especificado diretamente.

    {
        "name": "[concat(parameters('accountName'), '/', parameters('startVmsRunbookGuid'))]",
        "type": "Microsoft.Automation/automationAccounts/jobs",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "location": "[parameters('regionId')]",
        "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('startVmsRunbookName'))]",
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/jobs/', parameters('initialPrepRunbookGuid'))]"
        ],
        "tags": { },
        "properties": {
            "runbook": {
                "name": "[variables('startVmsRunbookName')]"
            },
            "parameters": {
                "AzureConnectionAssetName": "[variables('AzureConnectionAssetName')]",
                "ResourceGroupName": "[variables('ResourceGroupName')]"
            }
        }
    }


## <a name="certificates"></a>Certificados
[Certificados de automatização do Azure](../automation/automation-certificates.md) tenham um tipo de **Microsoft.Automation/automationAccounts/certificates** e ter as propriedades da tabela seguinte.

| Propriedade | Descrição |
|:--|:--|
| base64Value   | Valor base 64 para o certificado. |
| impressão digital    | Impressão digital para o certificado. |

Um exemplo de um recurso de certificado está abaixo.

    "name": "[concat(parameters('accountName'), '/MyCertificate')]",
    "type": "certificates",
    "apiVersion": "2015-01-01-preview",
    "location": "[parameters('regionId')]",
    "tags": {},
    "dependsOn": [
    ],
    "properties": {
        "base64Value": "MIIC1jCCAA8gAwIAVgIYJY4wXCXH/YAHMtALh7qEFzANAgkqhkiG5w0AAQUFGDAUMRIwEBYDVQQDEwlsA3NhAGevc3QqHhcNMTZwNjI5MHQxMjAsWhcNOjEwNjI5NKWwMDAwWjAURIwEAYDVQQBEwlsA2NhAGhvc3QwggEiMA0GCSqGSIA3DQEAAQUAA4IADwAwggEKAoIAAQDIyzv2A0RUg1/AAryI9W1DGAHAqqGdlFfTkUSDfv+hEZTAwKv0p8daqY6GroT8Du7ctQmrxJsy8JxIpDWxUaWwXtvv1kR9eG9Vs5dw8gqhjtOwgXvkOcFdKdQwA82PkcXoHlo+NlAiiPPgmHSELGvcL1uOgl3v+UFiiD1ro4qYqR0ITNhSlq5v2QJIPnka8FshFyPHhVtjtKfQkc9G/xDePW8dHwAhfi8VYRmVMmJAEOLCAJzRjnsgAfznP8CZ/QUczPF8LuTZ/WA/RaK1/Arj6VAo1VwHFY4AZXAolz7xs2sTuHplVO7FL8X58UvF7nlxq48W1Vu0l8oDi2HjvAgMAAAGjJDAiMAsGA1UdDwREAwIEsDATAgNVHSUEDDAKAggrAgEFNQcDATANAgkqhkiG9w0AAQUFAAOCAQEAk8ak2A5Ug4Iay2v0uXAk95qdAthJQN5qIVA13Qay8p4MG/S5+aXOVz4uMXGt18QjGds1A7Q8KDV4Slnwn95sVgA5EP7akvoGXhgAp8Dm90sac3+aSG4fo1V7Y/FYgAgpEy4C/5mKFD1ATeyyhy3PmF0+ZQRJ7aLDPAXioh98LrzMZr1ijzlAAKfJxzwZhpJamAwjZCYqiNZ54r4C4wA4QgX9sVfQKd5e/gQnUM8gTQIjQ8G2973jqxaVNw9lZnVKW3C8/QyLit20pNoqX2qQedwsqg3WCUcPRUUqZ4NpQeHL/AvKIrt158zAfU903yElAEm2Zr3oOUR4WfYQ==",
        "thumbprint": "F485CBE5569F7A5019CB68D7G6D987AC85124B4C"
    }

## <a name="credentials"></a>Credenciais
[Credenciais de automatização do Azure](../automation/automation-credentials.md) tenham um tipo de **Microsoft.Automation/automationAccounts/credentials** e ter as propriedades da tabela seguinte.

| Propriedade | Descrição |
|:--|:--|
| nome de utilizador   | Nome de utilizador para a credencial. |
| palavra-passe   | Palavra-passe para a credencial. |

Um exemplo de um recurso de credenciais está abaixo.

    "name": "[concat(parameters('accountName'), '/', variables('credentialName'))]",
    "type": "Microsoft.Automation/automationAccounts/runbooks/credentials",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "location": "[parameters('regionId')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "userName": "User01",
        "password": "password"
    }


## <a name="schedules"></a>Agendas
[Agendas de automatização do Azure](../automation/automation-schedules.md) tenham um tipo de **Microsoft.Automation/automationAccounts/schedules** e ter as propriedades da tabela seguinte.

| Propriedade | Descrição |
|:--|:--|
| Descrição | Descrição opcional para a agenda. |
| hora de início   | Especifica a hora de início de uma agenda de como um objeto de data/hora. Uma cadeia pode ser fornecida se podem ser convertido para um valor DateTime válido. |
| isEnabled   | Especifica se a agenda está ativada. |
| intervalo    | O tipo de intervalo para a agenda.<br><br>dia<br>hora |
| frequência   | Frequência deve fire a agenda de número de dias ou horas. |

Um exemplo de um recurso de agenda está abaixo.

    "name": "[concat(parameters('accountName'), '/', variables('variableName'))]",
    "type": "microsoft.automation/automationAccounts/schedules",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "description": "Schedule for StartByResourceGroup runbook",
        "startTime": "10/30/2016 12:00:00",
        "isEnabled": "true",
        "interval": "day",
        "frequency": "1"
    }


## <a name="variables"></a>Variáveis
[Variáveis de automatização do Azure](../automation/automation-variables.md) tenham um tipo de **Microsoft.Automation/automationAccounts/variables** e ter as propriedades da tabela seguinte.

| Propriedade | Descrição |
|:--|:--|
| Descrição | Descrição opcional para a variável. |
| isEncrypted | Especifica se a variável de deve ser encriptada. |
| tipo        | Tipo de dados para a variável. |
| valor       | Valor para a variável. |

Um exemplo de um recurso variável está abaixo.

    "name": "[concat(parameters('accountName'), '/', variables('StartTargetResourceGroupsAssetName')) ]",
    "type": "microsoft.automation/automationAccounts/variables",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "description": "Description for the variable.",
        "isEncrypted": "true",
        "type": "string",
        "value": "'This is a string value.'"
    }


## <a name="modules"></a>Módulos
A solução de gestão não é necessário definir [módulos globais](../automation/automation-integration-modules.md) utilizada pelo seu runbooks porque sempre irá estar disponíveis.  Precisar de incluir um recurso para módulo de utilizado pelo seu runbooks e livro de execuções deve dependem do recurso de módulo para se certificar de que é criado antes de livro de execuções.

[Módulos de integração](../automation/automation-integration-modules.md) tenham um tipo de **Microsoft.Automation/automationAccounts/modules** e ter as propriedades da tabela seguinte.

| Propriedade | Descrição |
|:--|:--|
| contentLink | Especifica o conteúdo do módulo. <br><br>URI - Uri o conteúdo do livro de execuções.  Este será um ficheiro. ps1 para runbooks PowerShell e Script e um ficheiro de livro de execuções gráficas exportado para um livro de execuções do gráfico.  <br> versão - versão do livro de execuções para sua própria controlo. |

Um exemplo de um recurso de módulo está abaixo.

    {       
        "name": "[concat(parameters('accountName'), '/', variables('OMSIngestionModuleName'))]",
        "type": "Microsoft.Automation/automationAccounts/modules",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "dependsOn": [
        ],
        "properties": {
            "contentLink": {
                "uri": "https://devopsgallerystorage.blob.core.windows.net/packages/omsingestionapi.1.3.0.nupkg"
            }
        }
    }

### <a name="updating-modules"></a>Atualizar módulos
Se atualizar uma solução de gestão que inclua um livro de execuções que utiliza uma agenda e a nova versão da sua solução tem um novo módulo utilizado por esse livro de execuções, livro de execuções pode utilizar a versão antiga do módulo.  Deve incluir o seguinte runbooks na sua solução e criar uma tarefa para executá-las antes de qualquer outro runbooks.  Isto irá garantir que quaisquer módulos são actualizados como necessário antes das runbooks são carregadas.

- [Actualização ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/DisplayScript) irá garantir que todos os módulos utilizados pelo runbooks na sua solução estão a versão mais recente.  
- [MS de ReRegisterAutomationSchedule gestão](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/DisplayScript) irá registe novamente todos os recursos de agenda para se certificar de que o runbooks ligados às mesmas com, utilize os módulos mais recentes.


Segue-se uma amostra dos elementos necessários de uma solução para suportar a atualização do módulo.
    
    "parameters": {
        "ModuleImportGuid": {
            "type": "string",
            "metadata": {
                "control": "guid"
            }
        },
        "ReRegisterRunbookGuid": {
            "type": "string",
            "metadata": {
                "control": "guid"
            }
        }
    },

    "variables": {
        "ModuleImportRunbookName": "Update-ModulesInAutomationToLatestVersion",
        "ModuleImportRunbookUri": "https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/Content/Update-ModulesInAutomationToLatestVersion.ps1",
        "ModuleImportRunbookDescription": "Imports modules and also updates all Azure dependent modules that are in the same Automation Account.",
        "ReRegisterSchedulesRunbookName": "Update-ModulesInAutomationToLatestVersion",
        "ReRegisterSchedulesRunbookUri": "https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/Content/ReRegisterAutomationSchedule-MS-Mgmt.ps1",
        "ReRegisterSchedulesRunbookDescription": "Reregisters schedules to ensure that they use latest modules."
    },

    "resources": [
        {
            "name": "[concat(parameters('accountName'), '/', variables('ModuleImportRunbookName'))]",
            "type": "Microsoft.Automation/automationAccounts/runbooks",
            "apiVersion": "[variables('AutomationApiVersion')]",
            "dependsOn": [
            ],
            "location": "[parameters('regionId')]",
            "tags": { },
            "properties": {
                "runbookType": "PowerShell",
                "logProgress": "true",
                "logVerbose": "true",
                "description": "[variables('ModuleImportRunbookDescription')]",
                "publishContentLink": {
                    "uri": "[variables('ModuleImportRunbookUri')]",
                    "version": "1.0.0.0"
                }
            }
        },
        {
            "name": "[concat(parameters('accountName'), '/', parameters('ModuleImportGuid'))]",
            "type": "Microsoft.Automation/automationAccounts/jobs",
            "apiVersion": "[variables('AutomationApiVersion')]",
            "location": "[parameters('regionId')]",
            "dependsOn": [
                "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('ModuleImportRunbookName'))]"
            ],
            "tags": { },
            "properties": {
                "runbook": {
                    "name": "[variables('ModuleImportRunbookName')]"
                },
                "parameters": {
                    "ResourceGroupName": "[resourceGroup().name]",
                    "AutomationAccountName": "[parameters('accountName')]",
                    "NewModuleName": "AzureRM.Insights"
                }
            }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('ReRegisterSchedulesRunbookName'))]",
          "type": "Microsoft.Automation/automationAccounts/runbooks",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "location": "[parameters('regionId')]",
          "tags": { },
          "properties": {
            "runbookType": "PowerShell",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('ReRegisterSchedulesRunbookDescription')]",
            "publishContentLink": {
              "uri": "[variables('ReRegisterSchedulesRunbookUri')]",
              "version": "1.0.0.0"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', parameters('reRegisterSchedulesGuid'))]",
          "type": "Microsoft.Automation/automationAccounts/jobs",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('ReRegisterSchedulesRunbookName'))]"
          ],
          "tags": { },
          "properties": {
            "runbook": {
              "name": "[variables('ReRegisterSchedulesRunbookName')]"
            },
            "parameters": {
              "targetSubscriptionId": "[subscription().subscriptionId]",
              "resourcegroup": "[resourceGroup().name]",
              "automationaccount": "[parameters('accountName')]"
            }
        }
    ]


## <a name="next-steps"></a>Próximos passos

- [Adicionar uma vista a sua solução](operations-management-suite-solutions-resources-views.md) para visualizar os dados recolhidos.