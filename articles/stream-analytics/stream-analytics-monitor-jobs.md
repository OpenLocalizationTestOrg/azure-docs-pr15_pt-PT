<properties
    pageTitle="Monitorizar através de programação tarefas em sequência de análise | Microsoft Azure"
    description="Saiba como monitorizar através de programação tarefas de análise da cadeia criadas através de REST APIs, Azure SDK ou Powershell."
    keywords="monitor de .net, monitor de tarefa, monitorização de aplicação"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>


# <a name="programmatically-create-a-stream-analytics-job-monitor"></a>Criar um monitor de tarefa de análise da cadeia através de programação
 Este artigo demonstra como ativar a monitorização de uma tarefa de análise da cadeia. Tarefas criadas através de REST APIs, Azure SDK ou Powershell fazer a análise da cadeia de monitorização ativada, não, por predefinição.  Pode manualmente ativar isto no Portal do Azure, navegue até à página de Monitor da tarefa e clicando no botão Ativar ou pode automatizar este processo ao seguir os passos neste artigo. Os dados de monitorização irão aparecer no separador "Monitor" no Portal do Azure para a tarefa de análise da cadeia.

![Separador tarefas do monitor de tarefa](./media/stream-analytics-monitor-jobs/stream-analytics-monitor-jobs-tab.png)

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, tem de ter o seguinte procedimento:

- Visual Studio 2012http ou 2013.
- Transfira e instale o [Azure.NET SDK](https://azure.microsoft.com/downloads/).
- Uma tarefa de análise da cadeia existente que necessita de monitorização ativado.

## <a name="setup-a-project"></a>Configurar um projecto

1.  Crie uma aplicação de consola do Visual Studio c# .net.
2.  Na consola do Gestor de pacotes, execute os seguintes comandos para instalar os pacotes de NuGet. A primeira parte é o Azure da cadeia Analytics gestão .NET SDK. O segundo é o SDK do Monitor Azure que irão ser utilizados para ativar a monitorização. No último registo é o cliente do Azure Active Directory que será utilizado para autenticação.

    ```
    Install-Package Microsoft.Azure.Management.StreamAnalytics
    Install-Package Microsoft.Azure.Insights -Pre
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

3.  Adicione a seguinte secção appSettings para o ficheiro App.

    ```
    <appSettings>
        <!--CSM Prod related values-->
        <add key="ResourceGroupName" value="RESOURCE GROUP NAME" />
        <add key="JobName" value="YOUR JOB NAME" />
        <add key="StorageAccountName" value="YOUR STORAGE ACCOUNT"/>
        <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
        <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
        <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
        <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
        <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
        <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION ID" />
        <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
    </appSettings>
    ```
Substituir valores para *SubscriptionId* e *ActiveDirectoryTenantId* com a sua subscrição Azure e IDs de inquilinos. Pode obter estes valores, executando o seguinte cmdlet do PowerShell:

    ```
    Get-AzureAccount
    ```
4.  Adicione o seguinte utilizando as instruções para o ficheiro de origem (Program.cs) no projeto.

    ```
        using System;
        using System.Configuration;
        using System.Threading;
        using Microsoft.Azure;
        using Microsoft.Azure.Management.Insights;
        using Microsoft.Azure.Management.Insights.Models;
        using Microsoft.Azure.Management.StreamAnalytics;
        using Microsoft.Azure.Management.StreamAnalytics.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```
5.  Adicione um método de autenticação de ajuda.

        public static string GetAuthorizationHeader()
            {
                AuthenticationResult result = null;
                var thread = new Thread(() =>
                {
                    try
                    {
                        var context = new AuthenticationContext(
                            ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
                            ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

                        result = context.AcquireToken(
                            resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                            clientId: ConfigurationManager.AppSettings["AsaClientId"],
                            redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
                            promptBehavior: PromptBehavior.Always);
                    }
                    catch (Exception threadEx)
                    {
                        Console.WriteLine(threadEx.Message);
                    }
                });

                thread.SetApartmentState(ApartmentState.STA);
                thread.Name = "AcquireTokenThread";
                thread.Start();
                thread.Join();

                if (result != null)
                {
                    return result.AccessToken;
                }

                throw new InvalidOperationException("Failed to acquire token");
        }

## <a name="create-management-clients"></a>Criar a gestão de clientes
O código seguinte irá configurar as variáveis necessárias e clientes de gestão.

    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials =
        new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader());

    Uri resourceManagerUri = new
    Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    // Create Stream Analytics and Insights management client
    StreamAnalyticsManagementClient streamAnalyticsClient = new
    StreamAnalyticsManagementClient(aadTokenCredentials, resourceManagerUri);
    InsightsManagementClient insightsClient = new
    InsightsManagementClient(aadTokenCredentials, resourceManagerUri);

## <a name="enable-monitoring-for-an-existing-stream-analytics-job"></a>Ativar a monitorização de uma tarefa de análise da cadeia existente

O código seguinte permitirá a monitorização de uma tarefa de análise da cadeia **existente** . A primeira parte do código executa num pedido GET contra o serviço de análise da cadeia para obter informações sobre a tarefa de análise da cadeia específica. Utiliza a propriedade de "Id" (obtida a partir do pedido GET) como um parâmetro para o método de colocar no segundo semestre do código que envia um local pedir para o serviço de informações para activar a monitorização para a tarefa de análise da cadeia.

> [AZURE.WARNING]
> Se anteriormente ativou a monitorização de uma tarefa de análise da cadeia diferente, através do Portal do Azure ou através de programação através de abaixo código, **é recomendado que fornecem o mesmo nome de conta de armazenamento que efetuou quando activou anteriormente monitorização.**
>
> A conta de armazenamento está associada à região que criou a tarefa de análise da cadeia em, não especificamente para a tarefa própria.
>
> Todas as análises de fluxo de trabalho (e todos os outros recursos Azure) nessa mesma região partilham esta conta de armazenamento para armazenar dados de monitorização. Se fornecer uma conta de armazenamento diferente, poderá fazer com que inesperados lado efeitos a monitorização dos seus trabalhos de análise da cadeia e/ou outros recursos Azure.
>
> O nome da conta de armazenamento utilizado para substituir ```“<YOUR STORAGE ACCOUNT NAME>”``` abaixo deve ser uma conta de armazenamento é na mesma subscrição, tal como as análises de fluxo de trabalho são activar a monitorização para.

    // Get an existing Stream Analytics job
    JobGetParameters jobGetParameters = new JobGetParameters()
    {
        PropertiesToExpand = "inputs,transformation,outputs"
    };
    JobGetResponse jobGetResponse = streamAnalyticsClient.StreamingJobs.Get(resourceGroupName, streamAnalyticsJobName, jobGetParameters);

    // Enable monitoring
    ServiceDiagnosticSettingsPutParameters insightPutParameters = new ServiceDiagnosticSettingsPutParameters()
    {
            Properties = new ServiceDiagnosticSettings()
            {
                StorageAccountName = "<YOUR STORAGE ACCOUNT NAME>"
            }
    };
    insightsClient.ServiceDiagnosticSettingsOperations.Put(jobGetResponse.Job.Id, insightPutParameters);



## <a name="get-support"></a>Obter suporte
Para obter mais assistência, experimente o nosso [Fórum Azure da cadeia Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Próximos passos

- [Introdução ao Azure sequência Analytics](stream-analytics-introduction.md)
- [Começar a utilizar o Azure da cadeia Analytics](stream-analytics-get-started.md)
- [Tarefas de análise do Azure da cadeia de escala](stream-analytics-scale-jobs.md)
- [Azure sequência Analytics referência da linguagem de consulta](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da sequência Azure Analytics gestão REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
