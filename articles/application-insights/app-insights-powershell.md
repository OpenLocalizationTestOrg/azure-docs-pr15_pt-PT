<properties 
    pageTitle="Criar os recursos de aplicação informações através do PowerShell" 
    description="Crie através de programação recursos de aplicação informações como parte do seu compilação." 
    services="application-insights" 
    documentationCenter=""
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="03/02/2016" 
    ms.author="awills"/>
 
# <a name="create-application-insights-resources-using-powershell"></a>Criar os recursos de aplicação informações através do PowerShell

Este artigo mostra-lhe como criar automaticamente um recurso de [Informações de aplicação](app-insights-overview.md) no Azure. Por exemplo, poderá, faça-o como parte de um processo de compilação. Juntamente com o recurso de aplicação informações básico, pode criar [testes de web de disponibilidade](app-insights-monitor-web-app-availability.md), [Configure alertas](app-insights-alerts.md)e criar outros recursos Azure.

A chave para a criação destes recursos é modelos de JSON para o [Gestor de recursos do Azure](../powershell-azure-resource-manager.md). Em Eis um resumo dos, o procedimento é: transferir as definições de JSON dos recursos existentes; parametrizar determinados valores, como nomes; e, em seguida, execute o modelo sempre que pretender criar um novo recurso. Pode compactar vários recursos em conjunto, criá-los todos de uma aceda - por exemplo, um monitor de aplicação com testes de disponibilidade, alertas e armazenamento para exportar contínuo. Existem algumas subtilezas a algumas das parameterizations, que Explicaremos aqui.

## <a name="one-time-setup"></a>Configuração única

Se não utilizou o PowerShell com a sua subscrição Azure antes:

Instale o módulo Azure Powershell no computador em que pretende executar os scripts:

1. Instalar [Microsoft Web plataforma Installer (v5 ou superior)](http://www.microsoft.com/web/downloads/platform.aspx).
2. Utilizá-lo para instalar o Microsoft Azure Powershell.

## <a name="copy-the-json-for-existing-resources"></a>Copiar JSON para recursos existentes

1. Configure o [Informações de aplicação](app-insights-overview.md) para um projeto semelhante àqueles que pretende para gerar automaticamente. Adicione testes web e alertas, caso pretenda.
2. Criar um novo ficheiro .json - vamos chame- `template1.json` neste exemplo. Copie este conteúdo para o mesmo:


    ```JSON

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "appName": { "type": "string" },
            "webTestName": { "type": "string" },
            "url": { "type": "string" },
            "text": { "type" : "string" }
          },
          "variables": {
            "testName": "[concat(parameters('webTestName'), 
               '-', toLower(parameters('appName')))]"
            "alertRuleName": "[concat(parameters('webTestName'), 
               '-', toLower(parameters('appName')), 
               '-', subscription().subscriptionId)]"
          },
          "resources": [
            {
              // component JSON file contents
            },
            {
              //web test JSON file contents
            },
            {
              //alert rule JSON file contents
            }
 
            // Any other resources go here
          ]
        }
    
    ```

    Este modelo irá configurar a disponibilidade de um teste para além do recurso principal.


2. Abra o [Gestor de recursos do Azure](https://resources.azure.com/). Navegar para baixo pelos `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components`, para o seu recurso de aplicação. 

    ![Navegação no Explorador do recurso Azure](./media/app-insights-powershell/01.png)

    *Os componentes* são os recursos de aplicação informações básicos para apresentar aplicações. Existem recursos separados para as regras de alertas associadas e testes de web disponibilidade.

3. Copie JSON do componente para o local adequado no `template1.json`.
6. Elimine estas propriedades:
  * `id`
  * `InstrumentationKey`
  * `CreationDate`
4. Abra as secções webtests e alertrules e copie JSON para itens individuais para o modelo. (Não copiar a partir de nós webtests ou alertrules: aceda para os itens em-los.)

    Teste cada web tem uma regra de alerta associada, pelo que tem de copiar ambos-los.

    O teste de web deve ir antes da regra de alerta.

5. Para satisfazer o esquema, insira esta linha de cada recurso:

    `"apiVersion": "2014-04-01",`

    (O esquema também queixar sobre o uso de maiúsculas/minúsculas dos nomes de tipo de recursos `Microsoft.Insights/*` – mas *não* alterá-las.)


## <a name="parameterize-the-template"></a>Parametrizar o modelo

Agora, tem de substituir os nomes específicos com parâmetros. Para [parametrizar um modelo](../resource-group-authoring-templates.md), escreva utilizando um [conjunto de funções auxiliares](../resource-group-template-functions.md)de expressões. 

Não é possível parametrizar apenas uma parte de uma cadeia, por isso, utilize `concat()` para construir cadeias.

Eis alguns exemplos de substituição que pretende fazer. Existem várias ocorrências de cada substituição. Poderá ter outras pessoas no seu modelo. Estes exemplos utilizam os parâmetros e variáveis definido na parte superior do modelo.

localizar | Substitua
---|---
`"hidden-link:/subscriptions/.../components/MyAppName"`| `"[concat('hidden-link:',`<br/>` resourceId('microsoft.insights/components',` <br/> ` parameters('appName')))]"`
`"/subscriptions/.../alertrules/myAlertName-myAppName-subsId",` | `"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",`
`"/subscriptions/.../webtests/myTestName-myAppName",` | `"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",`
`"myWebTest-myAppName"` | `"[variables(testName)]"'`
`"myTestName-myAppName-subsId"` | `"[variables('alertRuleName')]"`
`"myAppName"` | `"[parameters('appName')]"`
`"myappname"`(minúsculas) | `"[toLower(parameters('appName'))]"`
`"<WebTest Name=\"myWebTest\" ...`<br/>` Url=\"http://fabrikam.com/home\" ...>"`|`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]" `



## <a name="set-dependencies-between-the-resources"></a>Definir dependências entre os recursos

Azure deve configurar os recursos por ordem estritamente. Para se certificar-se de que conclui pelo programa de configuração antes de começa a próxima, adicione linhas de dependência:

* No web teste recurso:

    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`

* No alerta recurso:

    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`

## <a name="create-application-insights-resources"></a>Criar recursos de informações de aplicação

1. PowerShell, inicie sessão Azure

    `Login-AzureRmAccount`

2. Execute um comando da seguinte forma:

    ```PS

        New-AzureRmResourceGroupDeployment -ResourceGroupName Fabrikam `
               -templateFile .\template1.json `
               -appName myNewApp `
               -webTestName aWebTest `
               -Url http://myapp.com `
               -text "Welcome!"
               -siteName "MyAzureSite"

    ``` 

    * -ResourceGroupName é o grupo onde pretende criar novos recursos.
    * -modeloEtiquetas deve ocorrer antes dos parâmetros personalizados.
    * -Nome o nome do recurso para criar.
    * -webTestName o nome do teste de web para criar.
    * -Url o url da sua aplicação web.
    * -texto uma cadeia que aparece na sua página web.
    * -nome do site - utilizado se for um Web site Azure


## <a name="define-metric-alerts"></a>Definir alertas métricas

Existe um [método de PowerShell de definir alertas](app-insights-alerts.md#set-alerts-by-using-powershell).


## <a name="an-example"></a>Um exemplo

Eis o concluída, teste web e o componente web testar alerta modelo que criei:

``` JSON

{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "webTestName": { "type": "string" },
    "appName": { "type": "string" },
    "URL": { "type": "string" },
    "text": { "type" : "string" }
  },
  "variables": {
    "alertRuleName": "[concat(parameters('webTestName'), '-', toLower(parameters('appName')), '-', subscription().subscriptionId)]",
    "testName": "[concat(parameters('webTestName'), '-', toLower(parameters('appName')))]"
  },
  "resources": [
    {
      //"id": "[resourceId('Microsoft.Insights/components', parameters('appName'))]",
      "apiVersion": "2014-04-01",
      "kind": "web",
      "location": "Central US",
      "name": "[parameters('appName')]",
      "properties": {
        "TenantId": "9122605a-471fc50f8438",
        "Application_Type": "web",
        "Flow_Type": "Brownfield",
        "Request_Source": "VSIX3.3.1.0",
        "Name": "[parameters('appName')]",
        //"CreationDate": "2015-10-14T15:55:10.0917441+00:00",
        "PackageId": null,
        "ApplicationId": "[parameters('appName')]"
      },
      "tags": { },
      "type": "microsoft.insights/components"
    },
    {
      //"id": "[resourceId('Microsoft.Insights/webtests', variables('testName'))]",
      "name": "[variables('testName')]",
      "apiVersion": "2014-04-01",
      "type": "microsoft.insights/webtests",
      "location": "Central US",
      "tags": {
        "[concat('hidden-link:', resourceId('microsoft.insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "provisioningState": "Succeeded",
        "Name": "[parameters('webTestName')]",
        "Description": "",
        "Enabled": true,
        "Frequency": 900,
        "Timeout": 120,
        "Kind": "ping",
        "RetryEnabled": true,
        "Locations": [
          {
            "Id": "us-va-ash-azr"
          },
          {
            "Id": "emea-nl-ams-azr"
          },
          {
            "Id": "emea-gb-db3-azr"
          }
        ],
        "Configuration": {
          "WebTest": "[concat(
             '<WebTest   Name=\"', 
                parameters('webTestName'), 
              '\"  Id=\"32cfc791-aaad-4b50-9c8d-993c21beb218\"   Enabled=\"True\"         CssProjectStructure=\"\"    CssIteration=\"\"  Timeout=\"120\"  WorkItemIds=\"\"         xmlns=\"http://microsoft.com/schemas/VisualStudio/TeamTest/2010\"         Description=\"\"  CredentialUserName=\"\"  CredentialPassword=\"\"         PreAuthenticate=\"True\"  Proxy=\"default\"  StopOnError=\"False\"         RecordedResultFile=\"\"  ResultsLocale=\"\">  <Items>  <Request Method=\"GET\"         Guid=\"a6f2c90b-61bf-b28hh06gg969\"  Version=\"1.1\"  Url=\"', 
              parameters('Url'), 
              '\" ThinkTime=\"0\"  Timeout=\"300\" ParseDependentRequests=\"True\"         FollowRedirects=\"True\" RecordResult=\"True\" Cache=\"False\"         ResponseTimeGoal=\"0\"  Encoding=\"utf-8\"  ExpectedHttpStatusCode=\"200\"         ExpectedResponseUrl=\"\" ReportingName=\"\" IgnoreHttpStatusCode=\"False\" />        </Items>  <ValidationRules> <ValidationRule  Classname=\"Microsoft.VisualStudio.TestTools.WebTesting.Rules.ValidationRuleFindText, Microsoft.VisualStudio.QualityTools.WebTestFramework, Version=10.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a\" DisplayName=\"Find Text\"         Description=\"Verifies the existence of the specified text in the response.\"         Level=\"High\"  ExectuionOrder=\"BeforeDependents\">  <RuleParameters>        <RuleParameter Name=\"FindText\" Value=\"', 
              parameters('text'), 
              '\" />  <RuleParameter Name=\"IgnoreCase\" Value=\"False\" />  <RuleParameter Name=\"UseRegularExpression\" Value=\"False\" />  <RuleParameter Name=\"PassIfTextFound\" Value=\"True\" />  </RuleParameters> </ValidationRule>  </ValidationRules>  </WebTest>')]"
        },
        "SyntheticMonitorId": "[variables('testName')]"
      }
    },
    {
      //"id": "[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",
      "name": "[variables('alertRuleName')]",
      "apiVersion": "2014-04-01",
      "type": "microsoft.insights/alertrules",
      "location": "East US",
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]",
        "[resourceId('Microsoft.Insights/webtests', variables('testName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource",
        "[concat('hidden-link:', resourceId('Microsoft.Insights/webtests', variables('testName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('alertRuleName')]",
        "description": "",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.LocationThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.LocationThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/webtests', variables('testName'))]",
            "metricName": "GSMT_AvRaW"
          },
          "windowSize": "PT15M",
          "failedLocationCount": 2
        },
        "action": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
          "sendToServiceOwners": true,
          "customEmails": [ ]
        },
        "provisioningState": "Succeeded",
        "actions": [ ]
      }

    }
  ]
}

```

## <a name="see-also"></a>Consulte também

Outros artigos de automatização:

* [Criar um recurso de informações da aplicação](app-insights-powershell-script-create-resource.md) - método rápido sem utilizar um modelo.
* [Configurar alertas](app-insights-powershell-alerts.md)
* [Criar testes web](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Enviar Azure diagnósticos para informações de aplicação](app-insights-powershell-azure-diagnostics.md)
* [Criar lançamento anotações](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)
