<properties
    pageTitle="Criar um concentrador de IoT utilizar a API REST | Microsoft Azure"
    description="Siga este tutorial para começar a utilizar a API REST para criar um concentrador de IoT."
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

# <a name="tutorial-create-an-iot-hub-using-a-c-program-and-the-rest-api"></a>Tutorial: Criar um concentrador de IoT utilizando um programa c# e os REST API

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introdução

Pode utilizar o [fornecedor de recursos IoT concentrador REST API] [ lnk-rest-api] para criar e gerir Azure IoT concentradores através de programação. Este tutorial mostra-lhe como utilizar o fornecedor de recurso IoT concentrador REST API para criar um concentrador de IoT a partir de um programa c#.

> [AZURE.NOTE] Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Gestor de recursos do Azure e clássica](../resource-manager-deployment-model.md).  Este artigo aborda a utilizar o modelo de implementação do Azure o Gestor de recursos.

Para concluir este tutorial, precisa do seguinte:

- Microsoft Visual Studio 2015.
- Uma conta do Azure active. <br/>Se não tiver uma conta, pode criar uma [conta gratuita] [ lnk-free-trial] apenas de duas minutos.
- [Microsoft Azure PowerShell 1.0] [ lnk-powershell-install] ou posterior.

[AZURE.INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Preparar o seu projeto do Visual Studio

1. No Visual Studio, crie um projeto do Visual c# Windows utilizando o modelo de projeto de **Aplicação de consola** . Nome do projeto **CreateIoTHubREST**.

2. No Explorador de solução, com o botão direito no seu projeto e, em seguida, clique em **Gerir pacotes de NuGet**.

3. No Gestor de Nuget pacote, verifique a **incluir a versão de pré-lançamento**e pesquise por **Microsoft.Azure.Management.ResourceManager**. Clique em **instalar**, na **Rever alterações** clique em **OK**e, em seguida, clique em **posso aceitar** para aceitar as licenças.

4. No Gestor de Nuget pacote, procure **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Clique em **instalar**, na **Rever alterações** clique em **OK**e, em seguida, clique em **posso aceitar** para aceitar a licença.

6. Na Program.cs, substitua as declarações existente **utilizando** o seguinte procedimento:

    ```
    using System;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json;
    using Microsoft.Rest;
    using System.Linq;
    using System.Threading;
    ```
    
7. Na Program.cs, adicione as seguintes variáveis estáticas substituir os valores de marcador de posição. Uma nota de **ApplicationId**, **SubscriptionId**, **TenantId**e **palavra-passe** que efetuou anteriormente no tutorial. **Nome do grupo de recursos** é o nome do grupo de recursos que utilize quando criar o concentrador IoT, pode ser um grupo de recursos pré-existentes ou um novo. **Nome do concentrador de IoT** é o nome do concentrador IoT criar, tal como **MyIoTHub** (este nome tem de ser exclusivo global, para que deve incluir o nome ou as iniciais). **Nome de implementação** é um nome para a implementação, tal como **Deployment_01**.

    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    
    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name including your initials}";
    ```

[AZURE.INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="use-the-rest-api-to-create-an-iot-hub"></a>Utilizar a API REST para criar um concentrador de IoT

Utilizar a [IoT concentrador REST API] [ lnk-rest-api] para criar um concentrador de IoT no seu grupo de recursos. Também pode utilizar a API REST para efetuar alterações a um concentrador de IoT existente.

1. Adicione o seguinte método para Program.cs:
    
    ```
    static void CreateIoTHub(string token)
    {
        
    }
    ```

2. Adicione o seguinte código para o método de **CreateIoTHub** para criar um objeto de **HttpClient** com o token de autenticação nos cabeçalhos de:

    ```
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```

3. Adicione o seguinte código para o método de **CreateIoTHub** para descrever o concentrador IoT para criar e gerar uma representação JSON (para a lista atual de localizações que suportam IoT concentrador ver o [Estado de Azure][lnk-status]):

    ```
    var description = new
    {
      name = iotHubName,
      location = "East US",
      sku = new
      {
        name = "S1",
        tier = "Standard",
        capacity = 1
      }
    };
    
    var json = JsonConvert.SerializeObject(description, Formatting.Indented);
    ```

4. Adicione o seguinte código ao método **CreateIoTHub** para submeter o pedido de descanso à Azure, verifique a resposta e obter o URL que pode utilizar para monitorizar o estado da tarefa de implementação:

    ```
    var content = new StringContent(JsonConvert.SerializeObject(description), Encoding.UTF8, "application/json");
    var requestUri = string.Format("https://management.azure.com/subscriptions/{0}/resourcegroups/{1}/providers/Microsoft.devices/IotHubs/{2}?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var result = client.PutAsync(requestUri, content).Result;
      
    if (!result.IsSuccessStatusCode)
    {
      Console.WriteLine("Failed {0}", result.Content.ReadAsStringAsync().Result);
      return;
    }
    
    var asyncStatusUri = result.Headers.GetValues("Azure-AsyncOperation").First();
    ```

5. Adicione o seguinte código para o fim do método **CreateIoTHub** para utilizar o endereço de **asyncStatusUri** obtido no passo anterior para aguardar de implementação concluir:

    ```
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{\"status\":\"Running\"}");
    ```

6. Adicione o seguinte código para o fim do método **CreateIoTHub** para obter as chaves do concentrador IoT criado e imprimi-los na consola:

    ```
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2015-08-15-preview", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;
    
    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```
    
## <a name="complete-and-run-the-application"></a>Concluir e executar a aplicação

Pode agora concluir a aplicação ao contactar o método de **CreateIoTHub** antes de criar e executá-la.

1. Adicione o seguinte código para o fim do método **principais** :

    ```
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```
    
2. Clique em **Criar** e, em seguida, **crie a solução**. Corrija os erros.

3. Clique em **Depurar** e, em seguida, **Iniciar depuração** para executar a aplicação. Poderá demorar vários minutos até que a implementação para executar.

4. Pode verificar que a aplicação adicionado o concentrador IoT novo ao visitar o [Azure portal] [ lnk-azure-portal] e ver a lista de recursos, ou ao utilizar o cmdlet do PowerShell **Get-AzureRmResource** .

> [AZURE.NOTE] Esta aplicação exemplo adiciona um concentrador de IoT padrão S1 para a qual são faturada. Quando tiver terminado, pode eliminar o concentrador IoT através do [Azure portal] [ lnk-azure-portal] ou ao utilizar o cmdlet do PowerShell **Remover AzureRmResource** quando tiver terminado.

## <a name="next-steps"></a>Próximos passos

Agora ter implementado um concentrador de IoT utilizar a API REST, poderá querer explorar ainda mais:

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

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
