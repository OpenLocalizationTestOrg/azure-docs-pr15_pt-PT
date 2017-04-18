<properties
 pageTitle="Importar a exportação de identidades do dispositivo IoT concentrador | Microsoft Azure"
 description="Conceitos e .NET fragmentos para a gestão de em volume de identidades do dispositivo IoT concentrador de código"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/05/2016"
 ms.author="dobett"/>

# <a name="bulk-management-of-iot-hub-device-identities"></a>Em volume de gestão de identidades do dispositivo de IoT concentrador

Cada concentrador IoT tem um registo de identidade do dispositivo que pode utilizar para criar recursos por dispositivo no serviço, tal como uma fila que contém as mensagens de dispositivo de nuvem em voo. Também o registo de identidade do dispositivo permite-lhe controlar o acesso aos pontos finais destinado do dispositivo. Este artigo descreve como importar e Exportar identidades do dispositivo em massa de e para a um registo de identidade do dispositivo.

Importar e exportar operações retirar colocar no contexto das *tarefas* que permitem-lhe executar operações de serviço em volume relativamente a um concentrador de IoT.

A classe de **RegistryManager** inclui os **ExportDevicesAsync** **ImportDevicesAsync** métodos e que utilizam o framework de **tarefa** . Estes métodos permitem-lhe exportar, importar e sincronizar a totalidade do registo de dispositivo IoT concentrador.

## <a name="what-are-jobs"></a>O que são tarefas?

Operações de registo do dispositivo identidade utilizam o sistema de **tarefa** quando a operação:

*  Tem um tempo de execução longo potencialmente em comparação com operações de runtime padrão, ou
*  Devolve uma grande quantidade de dados ao utilizador.

Nestes casos, em vez de uma chamada de API única à espera ou a bloquear o resultado da operação, a operação modo assíncrono cria uma **tarefa** para desse concentrador IoT. A operação, em seguida, devolve imediatamente um objeto de **JobProperties** .

Fragmento de código c# seguinte mostra como criar uma tarefa de exportação:

```
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);
```

Em seguida, pode utilizar a classe de **RegistryManager** para consultar o estado da **tarefa** utilizando os metadados **JobProperties** devolvidos.

Fragmento de código c# seguinte mostra como as inquérito em cinco segundos para ver se a tarefa já terminou em execução:

```
// Wait until job is finished
while(true)
{
  exportJob = await registryManager.GetJobAsync(exportJob.JobId);
  if (exportJob.Status == JobStatus.Completed || 
      exportJob.Status == JobStatus.Failed ||
      exportJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="export-devices"></a>Exportar dispositivos

Utilize o método de **ExportDevicesAsync** para exportar a totalidade do registo de dispositivo de concentrador IoT a um contentor de Blobs do [Armazenamento do Windows Azure](https://azure.microsoft.com/documentation/services/storage/) utilizando uma [Assinatura de acesso partilhado](https://msdn.microsoft.com/library/ee395415.aspx).

Este método permite-lhe criar cópias de segurança das suas informações de dispositivo fiáveis num contentor de BLOBs que pode controlar o.

O método de **ExportDevicesAsync** necessita de dois parâmetros:

*  Uma *cadeia* que contém um URI de um contentor de Blobs. Este URI tem de conter um token de SA concede acesso de escrita para o contentor. A tarefa cria um blob bloco neste contentor para armazenar os dados de dispositivo de exportação série. O token de SA tem de incluir estas permissões:
    
    ```
    SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
    ```

*  *Booleano* que indica se pretende excluir chaves de autenticação a partir do seu exportar dados. Se o resultado **Falso**, autenticação teclas estão incluídas em exportar; caso contrário, as teclas são exportadas como **Nulo**.

Fragmento de código c# seguinte mostra como iniciar uma tarefa de exportação que inclui as teclas de autenticação de dispositivo na exportação de dados e, em seguida, as inquérito para a conclusão:

```
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);

// Wait until job is finished
while(true)
{
    exportJob = await registryManager.GetJobAsync(exportJob.JobId);
    if (exportJob.Status == JobStatus.Completed || 
        exportJob.Status == JobStatus.Failed ||
        exportJob.Status == JobStatus.Cancelled)
    {
    // Job has finished executing
    break;
    }

    await Task.Delay(TimeSpan.FromSeconds(5));
}
```

A tarefa armazena respectiva saída no contentor de BLOBs fornecida como um blob bloco com o nome **devices.txt**. Os dados de saída consistem JSON serializado dados do dispositivo, com um dispositivo por linha.

O exemplo seguinte mostra os dados de saída:

```
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

Se precisar de acesso a estes dados no código, pode facilmente de serialização estes dados utilizando a classe de **ExportImportDevice** . Fragmento de código c# seguinte mostra como ler informações sobre o dispositivo que foi exportado anteriormente para um blob bloco:

```
var exportedDevices = new List<ExportImportDevice>();

using (var streamReader = new StreamReader(await blob.OpenReadAsync(AccessCondition.GenerateIfExistsCondition(), RequestOptions, null), Encoding.UTF8))
{
  while (streamReader.Peek() != -1)
  {
    string line = await streamReader.ReadLineAsync();
    var device = JsonConvert.DeserializeObject<ExportImportDevice>(line);
    exportedDevices.Add(device);
  }
}
```

> [AZURE.NOTE]  Também pode utilizar o método de **GetDevicesAsync** a classe de **RegistryManager** de para obter uma lista dos seus dispositivos. No entanto, esta abordagem tem um disco rígido remate de 1000 no número de objetos do dispositivo que são devolvidos. No caso de uso esperado para o método **GetDevicesAsync** é para cenários de desenvolvimento à ajuda depuração e não é recomendado para cargas de trabalho de produção.

## <a name="import-devices"></a>Importar dispositivos

O método de **ImportDevicesAsync** na classe **RegistryManager** permite-lhe executar operações de importação e sincronização em massa num registo de dispositivo de concentrador IoT. Como o método de **ExportDevicesAsync** , o método de **ImportDevicesAsync** utiliza a arquitetura de **tarefa** .

Encarregam-se utilizando o método de **ImportDevicesAsync** porque para além de aprovisionamento novos dispositivos no registo de identidade do dispositivo, pode atualizar e eliminar dispositivos existentes também.

> [AZURE.WARNING]  Uma operação de importação não pode ser anulada. Sempre uma cópia de segurança os seus dados existentes utilizando o método de **ExportDevicesAsync** para outro contentor blob antes de efetuar alterações em massa para o registo de identidade do dispositivo.

O método de **ImportDevicesAsync** assume dois parâmetros:

*  Uma *cadeia* que contém um URI de um contentor de Blobs do [Azure armazenamento](https://azure.microsoft.com/documentation/services/storage/) para utilizar como *entrada* para a tarefa. Este URI tem de conter um token de SA concede acesso de leitura para o contentor. Neste contentor tem de conter um blob com o nome **devices.txt** que contém os dados da série de dispositivo para importar para o registo de identidade do dispositivo. Importar dados tem de conter o mesmo formato JSON que a tarefa de **ExportImportDevice** utiliza quando cria um blob **devices.txt** informações sobre o dispositivo. O token de SA tem de incluir estas permissões:

    ```
    SharedAccessBlobPermissions.Read
    ```

*  Uma *cadeia* que contém um URI de um contentor de Blobs do [Azure armazenamento](https://azure.microsoft.com/documentation/services/storage/) para utilizar como *resultado* da tarefa. A tarefa cria um blob bloco neste contentor para armazenar quaisquer informações de erro da importação **tarefa**concluída. O token de SA tem de incluir estas permissões:
    
    ```
    SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
    ```

> [AZURE.NOTE]  Os dois parâmetros podem apontar para o contentor blob mesmo. Os parâmetros separados ativar simplesmente mais controlo sobre os seus dados, tal como o contentor de saída requer permissões adicionais.

Fragmento de código c# seguinte mostra como iniciar uma tarefa de importação:

```
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

## <a name="import-behavior"></a>Comportamento da importação

Pode utilizar o método de **ImportDevicesAsync** para efetuar as seguintes operações em massa no registo de identidade do dispositivo:

-   Registo de em volume dos novos dispositivos
-   Eliminações em volume dos dispositivos existentes
-   Em volume as alterações de estado (activar ou desactivar dispositivos)
-   Atribuição de em volume de teclas de autenticação do novo dispositivo
-   Em volume automática nova geração de teclas de autenticação de dispositivo

Pode executar qualquer combinação das operações precedentes dentro de uma chamada de **ImportDevicesAsync** única. Por exemplo, pode registar novos dispositivos e eliminar ou atualizar dispositivos existentes ao mesmo tempo. Quando utilizado juntamente com o método de **ExportDevicesAsync** , completamente pode migrar todos os seus dispositivos a partir de um concentrador de IoT para outra.

Utilize a propriedade opcional **importMode** na importação de dados serialização para cada dispositivo para controlar o processo por-dispositivo de importação. A propriedade **importMode** tem as seguintes opções:

| importMode |  Descrição |
| -------- | ----------- |
| **createOrUpdate** | Se não existir um dispositivo com o **id**de especificado, recentemente está registado. <br/>Se já existir no dispositivo, informações existentes são substituídas pelos dados de entrada fornecidos consideração o valor **ETag** . |
| **criar** | Se não existir um dispositivo com o **id**de especificado, recentemente está registado. <br/>Se já existir no dispositivo, é escrito um erro ao ficheiro de registo. |
| **Atualizar** | Se já existe um dispositivo com o **id**de especificado, são substituídas informações existentes com os dados de entrada fornecidos consideração o valor **ETag** . <br/>Se o dispositivo não existir, é escrito um erro ao ficheiro de registo. |
| **updateIfMatchETag** | Se já existe um dispositivo com o **id**de especificado, informações existentes são substituídas pelos dados de entrada fornecidos apenas se existir uma correspondência **ETag** . <br/>Se o dispositivo não existir, é escrito um erro ao ficheiro de registo. <br/>Se existir um erro de correspondência **ETag** , é escrito um erro ao ficheiro de registo. |
| **createOrUpdateIfMatchETag** | Se não existir um dispositivo com o **id**de especificado, recentemente está registado. <br/>Se já existir no dispositivo, informações existentes são substituídas pelos dados de entrada fornecidos apenas se existir uma correspondência **ETag** . <br/>Se existir um erro de correspondência **ETag** , é escrito um erro ao ficheiro de registo. |
| **eliminar** | Se já existe um dispositivo com o **id**de especificado, é eliminado consideração o valor **ETag** . <br/>Se o dispositivo não existir, é escrito um erro ao ficheiro de registo. |
| **deleteIfMatchETag** | Se já existe um dispositivo com o **id**de especificado, é eliminado apenas se existir uma correspondência **ETag** . Se o dispositivo não existir, é escrito um erro ao ficheiro de registo. <br/>Se existir um erro de correspondência ETag, é escrito um erro ao ficheiro de registo. |

> [AZURE.NOTE] Se os dados de serialização não definir um sinalizador de **importMode** para um dispositivo explicitamente, esta assume a predefinição **createOrUpdate** durante a operação de importação.

## <a name="import-devices-example--bulk-device-provisioning"></a>Exemplo de dispositivos de importar – em volume de aprovisionamento do dispositivo 

Exemplo de código c# seguinte ilustra como gerar várias identidades do dispositivo que:

- Inclua chaves de autenticação.
- Escreva essas informações de dispositivo para um blob bloco.
- Importe os dispositivos para o registo de identidade do dispositivo.

```
// Provision 1,000 more devices
var serializedDevices = new List<string>();

for (var i = 0; i < 1000; i++)
{
// Create a new ExportImportDevice
  var deviceToAdd = new ExportImportDevice()
  {
    Id = Guid.NewGuid().ToString(),
    Status = DeviceStatus.Enabled,
    Authentication = new AuthenticationMechanism()
    {
      SymmetricKey = new SymmetricKey()
      {
        PrimaryKey = CryptoKeyGenerator.GenerateKey(32),
        SecondaryKey = CryptoKeyGenerator.GenerateKey(32)
      }
    },
    ImportMode = ImportMode.Create
  };

  // Add device to existing list
  serializedDevices.Add(JsonConvert.SerializeObject(deviceToAdd));
}

// Write this list to the blob
var sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice => sb.AppendLine(serializedDevice));
await blob.DeleteIfExistsAsync();

using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Call import using the same blob to add new devices!
// This normally takes 1 minute per 100 devices the normal way
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="import-devices-example--bulk-deletion"></a>Exemplo de dispositivos de importar – eliminação em massa

O código de exemplo seguinte mostra-lhe como eliminar os dispositivos que adicionou utilizando o código de exemplo anterior:

```
// Step 1: Update each device's ImportMode to be Delete
sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice =>
{
  // Deserialize back to an ExportImportDevice
  var device = JsonConvert.DeserializeObject<ExportImportDevice>(serializedDevice);

  // Update property
  device.ImportMode = ImportMode.Delete;

  // Re-serialize
  sb.AppendLine(JsonConvert.SerializeObject(device));
});

// Step 2: Write the new import data back to the block blob
await blob.DeleteIfExistsAsync();
using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Step 3: Call import using the same blob to delete all devices!
importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}

```

## <a name="getting-the-container-sas-uri"></a>Obter o contentor URI SA


O código de exemplo seguinte mostra-lhe como gerar um [SA URI](../storage/storage-dotnet-shared-access-signature-part-2.md) com ler, escrever e eliminar permissões para um contentor blob:

```
static string GetContainerSasUri(CloudBlobContainer container)
{
  // Set the expiry time and permissions for the container.
  // In this case no start time is specified, so the
  // shared access signature becomes valid immediately.
  var sasConstraints = new SharedAccessBlobPolicy();
  sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
  sasConstraints.Permissions = 
    SharedAccessBlobPermissions.Write | 
    SharedAccessBlobPermissions.Read | 
    SharedAccessBlobPermissions.Delete;

  // Generate the shared access signature on the container,
  // setting the constraints directly on the signature.
  string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

  // Return the URI string for the container,
  // including the SAS token.
  return container.Uri + sasContainerToken;
}

```

## <a name="next-steps"></a>Próximos passos

Neste artigo, o que aprendeu como efetuar operações em massa contra o registo de identidade do dispositivo num concentrador de IoT. Siga estas ligações para saber mais sobre a gestão de Azure IoT concentrador:

- [Métricas de utilização][lnk-metrics]
- [Operações de monitorização][lnk-monitor]

Para explorar ainda mais as funcionalidades do concentrador de IoT, consulte:

- [Guia de programador][lnk-devguide]
- [Simulação de um dispositivo com o SDK do Gateway IoT][lnk-gateway]

[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md