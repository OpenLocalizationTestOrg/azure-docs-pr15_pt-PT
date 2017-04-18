<properties
   pageTitle="Criar soluções de gestão no conjunto de aplicações de gestão de operações (OMS) | Microsoft Azure"
   description="Soluções de gestão de aumentam a funcionalidade de conjunto de aplicações de gestão de operações (OMS), fornecendo cenários de gestão embalado que os clientes podem adicionar a sua área de trabalho OMS.  Este artigo fornece detalhes sobre como pode criar soluções de gestão para ser utilizado no seu ambiente ou disponibilizados aos seus clientes."
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
   ms.date="10/27/2016"
   ms.author="bwren" />

# <a name="creating-management-solutions-in-operations-management-suite-oms-preview"></a>Criar soluções de gestão no conjunto de aplicações de gestão de operações (OMS) (pré-visualização)

>[AZURE.NOTE]Este é prévia documentação para criar soluções de gestão no OMS que estão atualmente em pré-visualização. Qualquer esquema descrita abaixo está sujeito a alterações.  

Soluções de gestão de aumentam a funcionalidade de conjunto de aplicações de gestão de operações (OMS), fornecendo cenários de gestão embalado que os clientes podem adicionar a sua área de trabalho OMS.  Este artigo fornece detalhes sobre como criar o seus próprio soluções de gestão de que pode utilizar no seu próprio ambiente ou tornar disponível aos clientes através da Comunidade.

## <a name="planning-your-management-solution"></a>A solução de gestão de planeamento
Soluções de gestão de OMS incluem vários recursos de suporte num cenário de gestão específica.  Quando planear a sua solução, deverá concentrar-no cenário que está a tentar para alcançar e todos os recursos necessários para suportá-lo.  Todas as soluções devem ser self contidas e definir cada recurso que requer, mesmo se uma ou mais recursos também são definidos pelas outras soluções.  Quando uma solução de gestão é instalada, cada recurso é criado, a menos que já existe e pode definir o que acontece aos recursos quando uma solução é removida.  

Por exemplo, uma solução de gestão pode incluir um [livro de execuções do Azure automatização](../automation/automation-intro.md) que recolhe dados para o repositório de análise de registo utilizando uma [agenda](../automation/automation-schedules.md) e uma [vista](../log-analytics/log-analytics-view-designer.md) que fornece várias visualizações dos dados recolhidos.  A mesma agenda pode ser utilizada por outra solução.  Como o autor de solução de gestão, que definir todos os três recursos mas especificar que o livro execuções e ver devem ser automaticamente removidas quando a solução é removida.    Também deve definir a agenda, mas especificar que-deve permanecem no local se tiverem sido removida a solução no caso de estava continuar a ser utilizado pela outra solução.

## <a name="management-solution-files"></a>Ficheiros de solução de gestão
Soluções de gestão de são implementadas como [modelos de gestão de recursos](../resource-manager-template-walkthrough.md).  A tarefa principal no aprender a criar soluções de gestão é aprender como [autor de um modelo](../resource-group-authoring-templates.md).  Este artigo fornece exclusivos detalhes de modelos utilizados para soluções e sobre como definir solução típica recursos.

A estrutura básica de um ficheiro de solução de gestão é o mesmo como um [Modelo de Gestor de recursos](resource-group-authoring-templates.md#template-format) que é da seguinte forma.  Cada uma das seguintes secções descreve os elementos de nível superior e e os seus conteúdos numa solução.  

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>Parâmetros

[Parâmetros](../resource-group-authoring-templates.md#parameters) são valores que necessitam do utilizador quando instalam solução de gestão do.  Existem parâmetros padrão com todas as soluções e pode adicionar parâmetros adicionais conforme necessário para a sua solução específica.  Como os utilizadores irão fornecer valores de parâmetros quando instalam a solução irá dependem o parâmetro específico e como a solução está a ser instalada.


Quando um utilizador instala a solução de gestão através do [Azure Marketplace](operations-management-suite-solutions.md#finding-and-installing-solutions) ou o [Guia de introdução do Azure modelos](operations-management-suite-solutions.md#finding-and-installing-solutions) são-lhe pedidos para selecionar uma [área de trabalho OMS e a conta de automatização](operations-management-suite-solutions-creating.md#oms-workspace-and-automation-account).  Estas são utilizadas para preencher os valores de cada um dos parâmetros padrão.  Não é pedido ao utilizador para fornecer diretamente os valores para os parâmetros padrão, mas são-lhe pedidas para fornecer valores para quaisquer parâmetros adicionais.

Quando o utilizador instala a sua solução de [outro método](operations-management-suite-solutions.md#finding-and-installing-solutions), tem de fornecer um valor para todos os parâmetros padrão e todos os parâmetros adicionais.

Um parâmetro de exemplo é apresentado abaixo.

    "Daily Start Time": {
        "type": "string",
        "metadata": {
            "description": "Enter time for starting VMs by resource group.",
            "control": "datetime",
            "category": "Schedule"
        }

A tabela seguinte descreve os atributos de um parâmetro.

| Atributo | Descrição |
|:--|:--|
| tipo        | Tipo de dados para o parâmetro. O controlo de entrada apresentado para o utilizador depende do tipo de dados.<br><br>Booleano - caixa pendente<br>cadeia - caixa de texto<br>Int - caixa de texto<br>SecureString - campo palavra-passe<br> |
| categoria    | Categoria opcional para o parâmetro.  Parâmetros na mesma categoria são agrupados. |
| controlo     | Funcionalidades adicionais para os parâmetros de cadeia.<br><br>data/hora - controlo de data/hora é apresentada.<br>GUID - valor Guid é gerado automaticamente e o parâmetro não é apresentado. |
| Descrição | Descrição opcional para o parâmetro.  Mostradas num balão de informações junto ao parâmetro. |


### <a name="standard-parameters"></a>Parâmetros padrão
A tabela seguinte lista os parâmetros de padrão para todas as soluções de gestão.  Estes valores são preenchidas para o utilizador em vez de solicitá-los quando a solução é instalada a partir da Azure Marketplace ou modelos de guia de introdução.  O utilizador terá de fornecer valores para os mesmos se a solução é instalada com o outro método.

>[AZURE.NOTE]A interface de utilizador no Azure Marketplace e modelos de guia de introdução está à espera os nomes dos parâmetros na tabela.  Se utilizar os nomes dos parâmetros diferentes, em seguida, será pedido ao utilizador para as mesmas e serão não povoadas automaticamente.


| Parâmetro | Tipo | Descrição |
|:--|:--|:--|
| accountName       | cadeia |  Nome da conta Azure automatização. |
| pricingTier       | cadeia | Preços camada de área de trabalho de análise de registo e conta Azure automatização. |
| regionId          | cadeia | Região da conta Azure automatização. |
| solutionName      | cadeia | Nome da solução. |
| NomeDaÁreaDeTrabalho     | cadeia | Nome de área de trabalho de análise de registo. |
| workspaceRegionId | cadeia | Região da área de trabalho a análise de registo. |





### <a name="sample"></a>Exemplo
Segue-se uma entidade de parâmetro de exemplo para uma solução.  Isto inclui todos os parâmetros padrão e dois parâmetros adicionais na mesma categoria.

    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "A valid Log Analytics workspace name"
            }
        },
        "accountName": {
            "type": "string",
            "metadata": {
                "description": "A valid Azure Automation account name"
            }
        },
        "workspaceRegionId": {
            "type": "string",
            "metadata": {
                "description": "Region of the Log Analytics workspace"
            }
        },
        "regionId": {
            "type": "string",
            "metadata": {
                "description": "Region of the Azure Automation account"
            }
        },
        "pricingTier": {
            "type": "string",
            "metadata": {
                "description": "Pricing tier of both Log Analytics workspace and Azure Automation account"
            }
        },
        "jobIdGuid": {
        "type": "string",
            "metadata": {
                "description": "GUID for a runbook job",
                "control": "guid",
                "category": "Schedule"
            }
        },
        "startTime": {
            "type": "string",
            "metadata": {
                "description": "Time for starting the runbook.",
                "control": "datetime",
                "category": "Schedule"
            }
        }


Referir a valores de parâmetro na outros elementos da solução com a sintaxe de **parâmetros ('nome do parâmetro')**.  Por exemplo, para aceder ao nome da área de trabalho, que utilizaria **parameters('workspaceName')** 

## <a name="variables"></a>Variáveis

O elemento de **variáveis** inclui valores que irá utilizar os restantes solução de gestão do.  Estes valores não são expostos ao utilizador que instala a solução.  Se destinam para fornecer o autor com uma única localização onde que possam gerir valores que podem ser utilizados várias vezes em toda a solução. Deve colocar quaisquer valores específicos a sua solução em variáveis por oposição a hardcoding-los no elemento de **recursos** .  Isto torna o código mais legível e permite-lhe alterar facilmente estes valores em versões posteriores.

Segue-se um exemplo de um elemento de **variáveis** com parâmetros típicos utilizados em soluções.

    "variables": { 
        "SolutionVersion": "1.1", 
        "SolutionPublisher": "Contoso", 
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Fizer referência ao valores de variáveis através da solução com sintaxe **variáveis ('nome da variável')**.  Por exemplo, para aceder a variável de SolutionName, que utilizaria **variables('solutionName')** 


## <a name="resources"></a>Recursos

O elemento de **recursos** define os diferentes incluídos na sua solução de gestão de recursos.  Esta será a parte maior e mais complexa do modelo.  Recursos são definidos com a seguinte estrutura.  

    "resources": [
        {
            "name": "<name-of-the-resource>",           
            "apiVersion": "<api-version-of-resource>",
            "type": "<resource-provider-namespace/resource-type-name>",     
            "location": "<location-of-resource>",
            "tags": "<name-value-pairs-for-resource-tagging>",
            "comments": "<your-reference-notes>",
            "dependsOn": [
                "<array-of-related-resource-names>"
            ],
            "properties": "<unique-settings-for-the-resource>",
            "resources": [
                "<array-of-child-resources>"
            ]
        }
    ]

### <a name="dependencies"></a>Dependências
Os elementos de **dependsOn** Especifica uma [dependência](../resource-group-define-dependencies.md) no outro recurso.  Quando a solução é instalada, não é criado um recurso até todas as suas dependências tem sido criadas.  Por exemplo, a solução poderá [Iniciar um livro de execuções](operations-management-suite-solutions-resources-automation.md#runbooks) quando é instalado utilizando um [recurso de trabalho](operations-management-suite-solutions-resources-automation.md#automation-jobs).  O recurso de trabalho seria dependente do recurso livro execuções para se certificar de que o livro de execuções é criado antes da tarefa é criada.

### <a name="oms-workspace-and-automation-account"></a>Área de trabalho OMS e a conta de automatização
Soluções de gestão de requerem uma [área de trabalho OMS](../log-analytics/log-analytics-manage-access.md) para contêm vistas e uma [conta de automatização](../automation/automation-security-overview.md#automation-account-overview) para conter runbooks e recursos relacionados.  Estes devem estar disponíveis antes dos recursos da solução são criados e não devem ser definidos na própria solução.  O utilizador irá, [Especifique uma área de trabalho e conta](operations-management-suite-solutions.md#oms-workspace-and-automation-account) quando implementar a sua solução, mas como o autor deverá tomar em consideração os seguintes pontos.


## <a name="solution-resource"></a>Recurso de solução
Cada solução requer uma entrada de recursos no elemento de **recursos** que define a solução própria.  Isto vai ter um tipo de **Microsoft.OperationsManagement/solutions**.  Segue-se um exemplo de um recurso de solução.  Os respetivos elementos diferentes são descritos nas secções abaixo.

    "name": "[concat(variables('SolutionName'), '[ ' ,parameters('workspacename'), ' ]')]",
    "location": "[parameters('workspaceRegionId')]",
    "tags": { },
    "type": "Microsoft.OperationsManagement/solutions",
    "apiVersion": "[variables('LogAnalyticsApiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('RunbookName'))]",
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/schedules/', variables('ScheduleName'))]",
        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
    ]
    "properties": {
        "workspaceResourceId": "[concat(resourceGroup().id, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
        "referencedResources": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/schedules/', variables('ScheduleName'))]"
        ],
        "containedResources": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('RunbookName'))]",
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
        ]
    },
    "plan": {
        "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
        "Version": "[variables('SolutionVersion')]",
        "product": "AzureSQLAnalyticSolution",
        "publisher": "[variables('SolutionPublisher')]",
        "promotionCode": ""
    }

### <a name="solution-name"></a>Nome da solução
O nome da solução tem de ser exclusivo na sua subscrição do Azure. O valor recomendado utilizar é o seguinte.  Esta opção utiliza uma variável denominada **SolutionName** para o nome de base e o parâmetro de **NomeDaÁreaDeTrabalho** para se certificar de que o nome é exclusivo.

    [concat(variables('SolutionName'), ' [' ,parameters('workspaceName'), ']')]

Isto seria resolver a um nome semelhante ao seguinte.

    My Solution Name [MyWorkspace]
 

### <a name="dependencies"></a>Dependências
O recurso de solução tem de ter uma [dependência](../resource-group-define-dependencies.md) em todos os outros recursos da solução uma vez que precisam de existir antes da solução pode ser criada.  Pode fazê-lo adicionando uma entrada para cada recurso no elemento de **dependsOn** .


### <a name="properties"></a>Propriedades
O recurso de solução tem as propriedades da tabela seguinte.  Isto inclui os recursos referenciado e contido pela solução que define a forma como o recurso é gerido depois da solução está instalada.  Cada recurso da solução deve ser indicado na **referencedResources** ou a propriedade **containedResources** .

| Propriedade | Descrição |
|:--|:--|
| workspaceResourceId | ID da área de trabalho OMS no formulário de * <Resource Group ID>/providers/Microsoft.OperationalInsights/workspaces/\<nome da área de trabalho\>*. |
| referencedResources   | Lista de recursos da solução que não devem ser removidos quando a solução é removida. |
| containedResources   | Lista de recursos da solução que devem ser removidos quando a solução é removida. |

O exemplo acima é para obter uma solução com um livro de execuções, uma agenda e vista.  A agenda e o livro execuções estão *referenciados* no elemento de **Propriedades** por isso não são removidos quando a solução é removida.  A vista é *contidas* para que este é removido quando a solução é removida.


### <a name="plan"></a>Plano
A entidade de **plano** do recurso solução tem as propriedades da tabela seguinte. 

| Propriedade | Descrição |
|:--|:--|
| nome          | Nome da solução. |
| versão       | Versão da solução conforme determinado pelo autor. |
| produto       | Cadeia exclusiva para identificar a solução. |
| Publisher     | Publicador da solução. |


## <a name="other-resources"></a>Outros recursos
Pode obter os detalhes e amostras de recursos que são comuns a soluções de gestão nos seguintes artigos.

- [Vistas e dashboards](operations-management-suite-solutions-resources-views.md)
- [Recursos de automatização](operations-management-suite-solutions-resources-automation.md)

## <a name="testing-a-management-solution"></a>Testar uma solução de gestão
Antes de implementar a solução de gestão, recomenda-se que a teste utilizando [AzureRmResourceGroupDeployment de teste](../resource-group-template-deploy.md#deploy-with-powershell).  Isto irá validar o ficheiro de solução e a ajuda que identificar quaisquer problemas antes de tentar implementá-lo.



## <a name="next-steps"></a>Próximos passos

- Obter informações sobre os detalhes dos [modelos de criação de Gestor de recursos do Azure](../resource-group-authoring-templates.md).
- Pesquisar [Modelos do Azure guia de introdução](https://azure.microsoft.com/documentation/templates) para obter exemplos das diferentes modelos de Gestor de recursos.
- Ver detalhes de [vistas para uma solução de gestão de adicionar](operations-management-suite-solutions-resources-views.md).
- Ver os detalhes para [Adicionar recursos de automatização para uma solução de gestão](operations-management-suite-solutions-resources-automation.md).
 