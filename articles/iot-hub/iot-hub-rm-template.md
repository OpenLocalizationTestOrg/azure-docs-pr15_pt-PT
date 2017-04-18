<properties
    pageTitle="Criar um concentrador de IoT utilizando um modelo de processador e c# | Microsoft Azure"
    description="Siga este tutorial para começar a utilizar o Gestor de recursos do Azure modelos para criar um concentrador de IoT com um programa c#."
    services="iot-hub"
    documentationCenter=".net"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/16/2016"
     ms.author="dobett"/>

# <a name="create-an-iot-hub-using-a-c-program-with-an-azure-resource-manager-template"></a>Criar um concentrador de IoT utilizando o programa c# com um modelo de Gestor de recursos do Azure

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introdução

Pode utilizar o Gestor de recursos do Azure para criar e gerir Azure IoT concentradores através de programação. Este tutorial mostra-lhe como utilizar um modelo de Gestor de recursos do Azure para criar um concentrador de IoT a partir de um programa c#.

> [AZURE.NOTE] Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Gestor de recursos do Azure e clássica](../resource-manager-deployment-model.md).  Este artigo aborda a utilizar o modelo de implementação do Azure o Gestor de recursos.

Para concluir este tutorial, precisa do seguinte:

- Microsoft Visual Studio 2015.
- Uma conta do Azure active. <br/>Se não tiver uma conta, pode criar uma [conta gratuita] [ lnk-free-trial] apenas de duas minutos.
- Uma [conta de armazenamento do Windows Azure] [ lnk-storage-account] onde pode armazenar os seus ficheiros de modelo de Gestor de recursos do Azure.
- [Microsoft Azure PowerShell 1.0] [ lnk-powershell-install] ou posterior.

[AZURE.INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Preparar o seu projeto do Visual Studio

1. No Visual Studio, crie um projeto do Visual c# Windows utilizando o modelo de projeto de **Aplicação de consola** . Nome do projeto **CreateIoTHub**.

2. No Explorador de solução, com o botão direito no seu projeto e, em seguida, clique em **Gerir pacotes de NuGet**.

3. No Gestor de Nuget pacote, verifique a **incluir a versão de pré-lançamento**e pesquise por **Microsoft.Azure.Management.ResourceManager**. Clique em **instalar**, na **Rever alterações** clique em **OK**e, em seguida, clique em **posso aceitar** para aceitar as licenças.

4. No Gestor de Nuget pacote, procure **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Clique em **instalar**, na **Rever alterações** clique em **OK**e, em seguida, clique em **posso aceitar** para aceitar a licença.

5. Na Program.cs, substitua as declarações existente **utilizando** o seguinte procedimento:

    ```
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```
    
6. Na Program.cs, adicione as seguintes variáveis estáticas substituir os valores de marcador de posição. Uma nota de **ApplicationId**, **SubscriptionId**, **TenantId**e **palavra-passe** que efetuou anteriormente no tutorial. **O armazenamento do Windows Azure nome da conta** é o nome da conta de armazenamento do Windows Azure onde armazenar os ficheiros de modelo de Gestor de recursos do Azure. **Nome do grupo de recursos** é o nome do grupo de recursos que utilize quando criar o concentrador IoT, pode ser um grupo de recursos pré-existentes ou um novo. **Nome de implementação** é um nome para a implementação, tal como **Deployment_01**.

    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    static string storageAddress = "https://{Your storage account name}.blob.core.windows.net";
    static string rgName = "{Resource group name}";
    static string deploymentName = "{Deployment name}";
    ```

[AZURE.INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="submit-an-azure-resource-manager-template-to-create-an-iot-hub"></a>Submeter um modelo de Gestor de recursos do Azure para criar um concentrador de IoT

Utilize um ficheiro de modelo e um parâmetro JSON para criar um concentrador de IoT no seu grupo de recursos. Também pode utilizar um modelo de Gestor de recursos do Azure para efetuar alterações a um concentrador de IoT existente.

1. No Explorador de solução, com o botão direito no seu projeto, clique em **Adicionar**e, em seguida, clique em **Novo Item**. Adicione um ficheiro JSON denominado **template.json** ao seu projeto.

2. Substitua o conteúdo da **template.json** com a definição de recursos seguintes para adicionar um concentrador de IoT padrão à região de **Leste dos EUA** (para a lista atual de regiões que suportam IoT concentrador ver o [Estado de Azure][lnk-status]):

    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

3. No Explorador de solução, com o botão direito no seu projeto, clique em **Adicionar**e, em seguida, clique em **Novo Item**. Adicione um ficheiro JSON denominado **parameters.json** ao seu projeto.

4. Substitua o conteúdo da **parameters.json** com as seguintes informações de parâmetro que define como um nome para o novo concentrador de IoT **{as suas iniciais} mynewiothub**. Tenha em atenção que o nome do concentrador IoT deve ser exclusivo globalmente para que deve incluir o nome ou as iniciais):

    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": { "value": "mynewiothub" }
      }
    }
    ```

5. No **Explorador do servidor**, ligar à sua subscrição do Azure e, na sua conta de armazenamento do Windows Azure criar um contentor denominado **modelos**. No painel de **Propriedades** , defina as permissões de **acesso de leitura público** para o contentor de **modelos** para **Blob**.

6. No **Explorador do servidor**, com o botão direito no contentor de **modelos** e, em seguida, clique em **Vista Blob contentor**. Clique no botão **Carregar Blob** , selecione os dois ficheiros, **parameters.json** e **templates.json**e, em seguida, clique em **Abrir** para carregar os ficheiros JSON para o contentor de **modelos** . Os URLs das blobs que contêm os dados JSON são:

    ```
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.blob.core.windows.net/templates/template.json
    ```

7. Adicione o seguinte método para Program.cs:
    
    ```
    static void CreateIoTHub(ResourceManagementClient client)
    {
        
    }
    ```

5. Adicione o seguinte código para o método de **CreateIoTHub** para submeter os ficheiros de modelo e um parâmetro para o Gestor de recursos do Azure:

    ```
    var createResponse = client.Deployments.CreateOrUpdate(
        rgName,
        deploymentName,
        new Deployment()
        {
          Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink
            {
              Uri = storageAddress + "/templates/template.json"
            },
            ParametersLink = new ParametersLink
            {
              Uri = storageAddress + "/templates/parameters.json"
            }
          }
        });
    ```

6. Adicione o seguinte código para o método de **CreateIoTHub** que apresenta o estado e as teclas para o concentrador IoT novo:

    ```
    string state = createResponse.Properties.ProvisioningState;
    Console.WriteLine("Deployment state: {0}", state);

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
    }
    Console.WriteLine(createResponse.Properties.Outputs);
    ```

## <a name="complete-and-run-the-application"></a>Concluir e executar a aplicação

Pode agora concluir a aplicação ao contactar o método de **CreateIoTHub** antes de criar e executá-la.

1. Adicione o seguinte código para o fim do método **principais** :

    ```
    CreateIoTHub(client);
    Console.ReadLine();
    ```
    
2. Clique em **Criar** e, em seguida, **crie a solução**. Corrija os erros.

3. Clique em **Depurar** e, em seguida, **Iniciar depuração** para executar a aplicação. Poderá demorar vários minutos até que a implementação para executar.

4. Pode verificar que a aplicação adicionado o concentrador IoT novo ao visitar o [Azure portal] [ lnk-azure-portal] e ver a lista de recursos, ou ao utilizar o cmdlet do PowerShell **Get-AzureRmResource** .

> [AZURE.NOTE] Esta aplicação exemplo adiciona um concentrador de IoT padrão S1 para a qual são faturada. Pode eliminar o concentrador IoT através do [Azure portal] [ lnk-azure-portal] ou ao utilizar o cmdlet do PowerShell **Remover AzureRmResource** quando tiver terminado.

## <a name="next-steps"></a>Próximos passos

Agora ter implementado um concentrador de IoT utilizando um modelo de Gestor de recursos do Azure com um programa de c#, poderá querer explorar ainda mais:

- Leia sobre as funcionalidades do [fornecedor de recursos IoT concentrador REST API][lnk-rest-api].
- Leia a [Descrição geral do Gestor de recursos do Azure] [ lnk-azure-rm-overview] para saber mais sobre as funcionalidades do Gestor de recursos do Azure.

Para saber mais sobre como desenvolver IoT concentrador, consulte o seguinte:

- [Introdução ao C SDK][lnk-c-sdk]
- [Concentrador IoT SDK][lnk-sdks]

Para explorar ainda mais as funcionalidades do concentrador de IoT, consulte:

- [Simulação de um dispositivo com o SDK do Gateway IoT][lnk-gateway]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: ../powershell-install-configure.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-storage-account]: ../storage/storage-create-storage-account.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
