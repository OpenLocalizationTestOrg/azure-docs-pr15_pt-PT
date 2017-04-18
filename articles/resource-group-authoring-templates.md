<properties
   pageTitle="Criação de modelos de Gestor de recursos do Azure | Microsoft Azure"
   description="Crie modelos de Gestor de recursos do Azure utilizando declarativa sintaxe JSON para implementar aplicações para o Azure."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="tomfitz"/>

# <a name="authoring-azure-resource-manager-templates"></a>Criação de modelos de Gestor de recursos do Azure

Este tópico descreve a estrutura de um modelo de Gestor de recursos do Azure. Apresenta as secções diferentes de um modelo e as propriedades que estão disponíveis dessas secções. O modelo é composta por JSON e expressões que pode utilizar para construir valores para a sua implementação. 

Para visualizar o modelo para recursos que já tiver implementado, consulte [Exportar um modelo de Gestor de recursos do Azure a partir de recursos existentes](resource-manager-export-template.md). Para obter orientações sobre como criar um modelo, consulte o artigo [Tutorial do Gestor de recursos do modelo](resource-manager-template-walkthrough.md). Para obter recomendações sobre a criação de modelos, consulte o artigo [melhores práticas para criar modelos de Gestor de recursos do Azure](resource-manager-template-best-practices.md).

Um editor de JSON boa pode simplificar a tarefa da criação de modelos. Para obter informações sobre como utilizar o Visual Studio com os modelos, consulte [criar e implementar a grupos de recursos Azure através do Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md). Para obter informações sobre como utilizar o código de VS, consulte o artigo [trabalhar com modelos de Gestor de recursos do Azure no código do Visual Studio](resource-manager-vs-code.md).

Limite o tamanho do seu modelo para 1 MB e cada ficheiro parâmetro para 64 KB. O limite de 1 MB aplica-se para o estado final do modelo após foi expandido com definições de recurso iterativo e valores de variáveis e parâmetros. 

## <a name="template-format"></a>Formato de modelo

Na sua estrutura mais simples, um modelo contém os seguintes elementos:

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

| Nome do elemento   | Obrigatório | Descrição
| :------------: | :------: | :----------
| $schema        |   Sim    | Localização do ficheiro de esquema JSON que descreva a versão de idioma do modelo. Utilize o URL mostrado no exemplo anterior.
| contentVersion |   Sim    | Versão do modelo (como 1.0.0.0). Pode fornecer qualquer valor para este elemento. Quando implementar recursos utilizando o modelo, este valor pode ser utilizado para se certificar de que está a ser utilizado o modelo mais adequado.
| parâmetros     |   N     | Valores que são fornecidos quando é executada implementação para personalizar a implementação do recurso.
| variáveis      |   N     | Valores que são utilizados como JSON fragmentos no modelo para simplificar a expressões de idioma do modelo.
| recursos      |   Sim    | Tipos de recursos que são implementados ou atualizados num grupo de recursos.
| resultados de        |   N     | Valores que são devolvidos após a implementação.

Vamos examinar as secções do modelo em maior detalhe mais adiante neste tópico. Por agora, vamos vai revê algumas da sintaxe que constituem o modelo.

## <a name="expressions-and-functions"></a>Expressões e funções

A sintaxe básica do modelo é JSON. No entanto, expressões e funções alargar o JSON que está disponível no modelo. Com expressões, pode criar valores que não sejam valores literais estritas. Expressões estão entre com Parênteses Retos [e] e são avaliados quando o modelo é implementado. Expressões podem aparecer em qualquer parte de um valor de cadeia JSON e sempre outra JSON valor devolvido. Se tem de utilizar uma cadeia literal que começa com um parêntese Reto [, tem de utilizar dois parênteses [[.

Normalmente, pode utilizar expressões com funções para efetuar operações para configurar a implementação. Tal como faria em JavaScript, chamadas de função são formatadas como **functionName(arg1,arg2,arg3)**. Referenciar propriedades ao utilizar os operadores ponto e [Índice].

O exemplo seguinte mostra como utilizar várias funções quando construir valores:
 
    "variables": {
       "location": "[resourceGroup().location]",
       "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
       "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
    }

Para a lista completa das funções de modelo, consulte [funções de modelo de Gestor de recursos do Azure](resource-group-template-functions.md). 


## <a name="parameters"></a>Parâmetros

Na secção parâmetros do modelo, especificar quais os valores que pode introduzir quando implementar os recursos. Estes valores de parâmetros permitem-lhe personalizar a implementação, fornecendo valores que estão adaptadas para um ambiente específico (como Dev Center, teste e produção). Não possui que fornecer parâmetros no seu modelo, mas sem parâmetros, o modelo sempre seria implementar os mesmos recursos com o mesmo nomes, localizações e propriedades.

Pode utilizar estes valores de parâmetros em todo o modelo para definir valores para os recursos implementados. Apenas os parâmetros declarados na secção parâmetros podem ser utilizados nas outras secções do modelo.

Definir parâmetros com a seguinte estrutura:

    "parameters": {
       "<parameter-name>" : {
         "type" : "<type-of-parameter-value>",
         "defaultValue": "<default-value-of-parameter>",
         "allowedValues": [ "<array-of-allowed-values>" ],
         "minValue": <minimum-value-for-int>,
         "maxValue": <maximum-value-for-int>,
         "minLength": <minimum-length-for-string-or-array>,
         "maxLength": <maximum-length-for-string-or-array-parameters>,
         "metadata": {
             "description": "<description-of-the parameter>" 
         }
       }
    }

| Nome do elemento   | Obrigatório | Descrição
| :------------: | :------: | :----------
| parameterName  |   Sim    | Nome do parâmetro. Tem de ser um identificador de JavaScript válido.
| tipo           |   Sim    | Tipo do valor do parâmetro. Ver a lista por baixo de tipos de permitidos.
| ValorPredefinido   |   N     | Valor predefinido para o parâmetro, não se for fornecido nenhum valor para o parâmetro.
| allowedValues  |   N     | Matriz de valores permitidos para o parâmetro para se certificar de que o valor de à direita é fornecido.
| minValue       |   N     | O valor mínimo para os parâmetros de tipo de int, este valor é, inclusive.
| ao maxValue       |   N     | O valor máximo para os parâmetros de tipo de int, este valor é, inclusive.
| minLength      |   N     | O comprimento mínimo de cadeia, secureString e parâmetros de tipo de matriz, este valor é, inclusive.
| Compr. máximo      |   N     | O comprimento máximo para cadeia, secureString e parâmetros de tipo de matriz, este valor é, inclusive.
| Descrição    |   N     | Descrição o parâmetro, que é apresentada aos utilizadores do modelo através da interface do portal modelo personalizado.

Os tipos de permitidos e os valores são:

- **cadeia**
- **secureString**
- **Int**
- **Booleano**
- **objecto** 
- **secureObject**
- **matriz**

Para especificar um parâmetro opcional como, forneça um ValorPredefinido (pode ser uma cadeia vazia). 

Se especificar um nome de parâmetro que corresponde a um dos parâmetros no comando para implementar o modelo, lhe for pedido para fornecer um valor para um parâmetro com o postfix **FromTemplate**. Por exemplo, se incluir um parâmetro com o nome **ResourceGroupName** no seu modelo que é o mesmo que o parâmetro **ResourceGroupName** no [Novo AzureRmResourceGroupDeployment] [ deployment2cmdlet] cmdlet, lhe for pedido para fornecer um valor para **ResourceGroupNameFromTemplate**. Em geral, deverá evitar este confusões atribuindo não parâmetros com o mesmo nome como parâmetros utilizados para operações de implementação.

>[AZURE.NOTE] Todas as palavras-passe, chaves e outros segredos devem utilizar o tipo de **secureString** . Parâmetros de modelo com o tipo de secureString não podem ser lidos após a implementação do recurso. 

O exemplo seguinte mostra como definir parâmetros:

    "parameters": {
      "siteName": {
        "type": "string",
        "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]"
      },
      "hostingPlanName": {
        "type": "string",
        "defaultValue": "[concat(parameters('siteName'),'-plan')]"
      },
      "skuName": {
        "type": "string",
        "defaultValue": "F1",
        "allowedValues": [
          "F1",
          "D1",
          "B1",
          "B2",
          "B3",
          "S1",
          "S2",
          "S3",
          "P1",
          "P2",
          "P3",
          "P4"
        ]
      },
      "skuCapacity": {
        "type": "int",
        "defaultValue": 1,
        "minValue": 1
      }
    }

Para saber como os valores de parâmetro de entrada durante a implementação, consulte o artigo [Implementar uma aplicação com o modelo de Gestor de recursos do Azure](resource-group-template-deploy.md#parameter-file). 

## <a name="variables"></a>Variáveis

Na secção de variáveis, construir valores que podem ser utilizadas em todo o seu modelo. Normalmente, variáveis são baseadas nos valores fornecidos a partir de parâmetros. Não é necessário definir variáveis, mas eles frequentemente simplificam o modelo, reduzindo expressões complexas.

Definir variáveis com a seguinte estrutura:

    "variables": {
       "<variable-name>": "<variable-value>",
       "<variable-name>": { 
           <variable-complex-type-value> 
       }
    }

O exemplo seguinte mostra como definir uma variável de que a fórmula foi criada a partir de dois valores de parâmetro:

     "variables": {
       "connectionString": "[concat('Name=', parameters('username'), ';Password=', parameters('password'))]"
    }

O exemplo seguinte mostra uma variável que é um tipo JSON complexo e variáveis que são construídas a partir de outras variáveis:

    "parameters": {
       "environmentName": {
         "type": "string",
         "allowedValues": [
           "test",
           "prod"
         ]
       }
    },
    "variables": {
       "environmentSettings": {
         "test": {
           "instancesSize": "Small",
           "instancesCount": 1
         },
         "prod": {
           "instancesSize": "Large",
           "instancesCount": 4
         }
       },
       "currentEnvironmentSettings": "[variables('environmentSettings')[parameters('environmentName')]]",
       "instancesSize": "[variables('currentEnvironmentSettings').instancesSize]",
       "instancesCount": "[variables('currentEnvironmentSettings').instancesCount]"
    }

## <a name="resources"></a>Recursos

Na secção de recursos, pode definir os recursos que são implementados ou atualizados. Esta secção pode ser complicada, porque é necessário compreender os tipos de que está a implementar para fornecer os valores à direita. 

Definir recursos com a seguinte estrutura:

    "resources": [
       {
         "apiVersion": "<api-version-of-resource>",
         "type": "<resource-provider-namespace/resource-type-name>",
         "name": "<name-of-the-resource>",
         "location": "<location-of-resource>",
         "tags": "<name-value-pairs-for-resource-tagging>",
         "comments": "<your-reference-notes>",
         "dependsOn": [
           "<array-of-related-resource-names>"
         ],
         "properties": "<settings-for-the-resource>",
         "copy": {
           "name": "<name-of-copy-loop>",
           "count": "<number-of-iterations>"
         }
         "resources": [
           "<array-of-child-resources>"
         ]
       }
    ]

| Nome do elemento             | Obrigatório | Descrição
| :----------------------: | :------: | :----------
| apiVersion               |   Sim    | Versão da API REST a utilizar para criar o recurso.
| tipo                     |   Sim    | Tipo de recurso. Este valor é uma combinação de espaço de nomes do fornecedor de recursos e o tipo de recurso (como **Microsoft.Storage/storageAccounts**).
| nome                     |   Sim    | Nome do recurso. O nome deve seguir restrições de componente URI definidas no RFC3986. Além disso, serviços Azure que expõem o nome do recurso para fora da partes validar o nome para se certificar de que não é uma tentativa de falsificar outra identidade. Consulte o artigo [verificar o nome do recurso](https://msdn.microsoft.com/library/azure/mt219035.aspx).
| localização                 |   Varia  | Suportada geo-localizações do recurso fornecido. Pode selecionar qualquer uma das localizações disponíveis, mas normalmente faz sentido para selecionar uma que se aproxime os seus utilizadores. Normalmente, também faz sentido para colocar os recursos de interagem uns com os outros na mesma região. A maioria dos tipos de recursos requerem uma localização, mas alguns tipos (tal como uma atribuição de funções) não requer uma localização.
| etiquetas                     |   N     | Etiquetas de dados que estão associadas ao recurso.
| comentários                 |   N     | Suas notas para documentar os recursos no seu modelo
| dependsOn                |   N     | Recursos que depende do recurso a ser definido. As dependências entre recursos são avaliadas e recursos são implementados na respetiva ordem dependente. Quando não estão dependentes uns dos outros recursos, estes são implementados em paralelo. O valor pode ser uma lista separados por vírgulas de um recurso de nomes ou identificadores exclusivos do recurso.
| Propriedades               |   N     | Definições de configuração específicas do recurso. Os valores para as propriedades são os mesmos como os valores que fornece no corpo do pedido para a operação de REST API (método colocar) criar o recurso. Para estabelecer ligações a documentação do esquema de recursos ou REST API, consulte o artigo [fornecedores de Gestor de recursos, regiões, as versões API e esquemas](resource-manager-supported-services.md).
| copiar                     |   N     | Se for necessário mais do que uma instância, o número de recursos para criar. Para mais informações, consulte o artigo [criar várias instâncias de recursos no Gestor de recursos do Azure](resource-group-create-multiple.md). |
| recursos                |   N     | Recursos de subordinados que dependem do recurso a ser definido. Pode fornecer apenas os tipos de recursos que são permitidos pelo esquema do recurso principal. O nome do tipo de recurso subordinado totalmente qualificado inclui o tipo de recurso principal, tal como **Microsoft.Web/sites/extensions**. Dependência de recurso principal que não seja implícito; tem de definir explicitamente essa dependência. 

Ao saber quais os valores para especificar para **apiVersion**, **tipo**e **localização** não está imediatamente óbvios. Felizmente, pode determinar estes valores através do Azure PowerShell ou clip de Azure.

Para todos os fornecedores de recurso com o **PowerShell**, utilize:

    Get-AzureRmResourceProvider -ListAvailable

A partir da lista devolvida, localize os fornecedores de recursos que lhe interessam. Para obter os tipos de recurso para um fornecedor de recursos (como armazenamento), utilize:

    (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Storage).ResourceTypes

Para obter as versões de API para um tipo de recurso (como contas de armazenamento), utilize:

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Storage).ResourceTypes | Where-Object ResourceTypeName -eq storageAccounts).ApiVersions

Para obter localizações suportadas para um tipo de recurso, utilize:

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Storage).ResourceTypes | Where-Object ResourceTypeName -eq storageAccounts).Locations

Para todos os fornecedores de recurso com **Azure clip**, utilize:

    azure provider list

A partir da lista devolvida, localize os fornecedores de recursos que lhe interessam. Para obter os tipos de recurso para um fornecedor de recursos (como armazenamento), utilize:

    azure provider show Microsoft.Storage

Para obter localizações suportadas e as versões API, utilize:

    azure provider show Microsoft.Storage --details --json

Para saber mais acerca dos fornecedores de recursos, consulte o artigo [fornecedores de Gestor de recursos, regiões, as versões API e esquemas](resource-manager-supported-services.md).

A secção de recursos contém uma matriz de recursos para implementar. Dentro de cada recurso, também pode definir uma matriz de recursos de subordinados. Por conseguinte, a secção de recursos poderia ter uma estrutura como:

    "resources": [
       {
           "name": "resourceA",
       },
       {
           "name": "resourceB",
           "resources": [
               {
                   "name": "firstChildResourceB",
               },
               {   
                   "name": "secondChildResourceB",
               }
           ]
       },
       {
           "name": "resourceC",
       }
    ]


O exemplo seguinte mostra um recurso de **Microsoft.Web/serverfarms** e um recurso de **Microsoft.Web/sites** com subordinado **extensões de** recursos. Repare que o site está marcado como dependentes no farm de servidores de uma vez que o farm de servidores tem de existir antes do site pode ser implementado. Repare demasiado que o recurso de **extensões** é subordinado do site.

    "resources": [
      {
        "apiVersion": "2015-08-01",
        "name": "[parameters('hostingPlanName')]",
        "type": "Microsoft.Web/serverfarms",
        "location": "[resourceGroup().location]",
        "tags": {
          "displayName": "HostingPlan"
        },
        "sku": {
          "name": "[parameters('skuName')]",
          "capacity": "[parameters('skuCapacity')]"
        },
        "properties": {
          "name": "[parameters('hostingPlanName')]",
          "numberOfWorkers": 1
        }
      },
      {
        "apiVersion": "2015-08-01",
        "type": "Microsoft.Web/sites",
        "name": "[parameters('siteName')]",
        "location": "[resourceGroup().location]",
        "tags": {
          "environment": "test",
          "team": "Web"
        },
        "dependsOn": [
          "[concat('Microsoft.Web/serverFarms/', parameters('hostingPlanName'))]"
        ],
        "properties": {
          "name": "[parameters('siteName')]",
          "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
        },
        "resources": [
          {
            "apiVersion": "2015-08-01",
            "type": "extensions",
            "name": "MSDeploy",
            "dependsOn": [
              "[concat('Microsoft.Web/sites/', parameters('siteName'))]"
            ],
            "properties": {
              "packageUri": "https://auxmktplceprod.blob.core.windows.net/packages/StarterSite-modified.zip",
              "dbType": "None",
              "connectionString": "",
              "setParameters": {
                "Application Path": "[parameters('siteName')]"
              }
            }
          }
        ]
      }
    ]


## <a name="outputs"></a>Resultados de

Na secção de resultados, especifique valores que são devolvidos implementação. Por exemplo, poderá devolver o URI para aceder a um recurso implementado.

O exemplo seguinte mostra a estrutura de uma definição de saída:

    "outputs": {
       "<outputName>" : {
         "type" : "<type-of-output-value>",
         "value": "<output-value-expression>"
       }
    }

| Nome do elemento   | Obrigatório | Descrição
| :------------: | :------: | :----------
| outputName     |   Sim    | Nome do valor de saída. Tem de ser um identificador de JavaScript válido.
| tipo           |   Sim    | Tipo do valor de saída. Valores de saída suportam os mesmos tipos como parâmetros de entrada do modelo.
| valor          |   Sim    | Expressão de idioma do modelo que é avaliado e devolvido como valor de saída.


O exemplo seguinte mostra um valor que é devolvido na secção de resultados.

    "outputs": {
       "siteUri" : {
         "type" : "string",
         "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
       }
    }

Para mais informações sobre como trabalhar com a saída, consulte o artigo [partilhar Estado no Gestor de recursos do Azure modelos](best-practices-resource-manager-state.md).

## <a name="next-steps"></a>Próximos passos
- Para ver os modelos de concluída para muitos tipos diferentes de soluções, consulte o artigo os [Modelos de guia de introdução do Azure](https://azure.microsoft.com/documentation/templates/).
- Para obter detalhes sobre as funções que pode utilizar a partir de um modelo, consulte [Funções de modelo de Gestor de recursos do Azure](resource-group-template-functions.md).
- Para combinar múltiplos modelos durante a implementação, consulte o artigo [utilizar modelos ligados com o Gestor de recursos do Azure](resource-group-linked-templates.md).
- Poderá ter utilizar os recursos que existam dentro de um grupo de recursos diferentes. Este cenário é comum quando trabalhar com contas do armazenamento ou redes virtuais que são partilhadas por vários grupos de recursos. Para mais informações, consulte a [função resourceId](resource-group-template-functions.md#resourceid).


[deployment2cmdlet]: https://msdn.microsoft.com/library/mt740620(v=azure.200).aspx
