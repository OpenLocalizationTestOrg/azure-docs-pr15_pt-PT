<properties
   pageTitle="Implementação da aplicação de serviço ferro | Microsoft Azure"
   description="Como implementar e remover aplicações no ferro de serviço"
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/25/2016"
   ms.author="ryanwi"/>

# <a name="deploy-and-remove-applications-using-powershell"></a>Implementar e remover aplicações através do PowerShell

> [AZURE.SELECTOR]
- [PowerShell](service-fabric-deploy-remove-applications.md)
- [Visual Studio](service-fabric-publish-app-remote-cluster.md)

<br/>

Uma vez por um [tipo de aplicação foi empacotado][10], está pronto para implementação para um cluster de Azure Service ferro. Implementação envolve os três passos seguintes:

1. Carregar o pacote de aplicação
2. Registe-se o tipo de aplicação
3. Criar a instância da aplicação

>[AZURE.NOTE] Se utilizar o Visual Studio para implementar e depuração de aplicações no seu cluster de desenvolvimento local, todos os passos seguintes são processados automaticamente através de um script PowerShell que se encontram na pasta Scripts do projeto aplicação. Este artigo fornece informações gerais sobre o que esses scripts estão a fazer, de modo a que pode efetuar as mesmas operações fora do Visual Studio.

## <a name="upload-the-application-package"></a>Carregar o pacote de aplicação

Carregar o pacote de aplicação coloca-o numa localização que esteja acessível por componentes de serviço ferro internos. Pode utilizar o PowerShell para executar o carregamento. Antes de executar qualquer comandos do PowerShell neste artigo, sempre inicie utilizando [ServiceFabricCluster ligar](https://msdn.microsoft.com/library/mt125938.aspx) para ligar ao serviço ferro cluster.

Suponha que tem uma pasta denominada *MyApplicationType* que contém a manifesto aplicação for necessário, manifestos de serviço e pacotes config/código/dados. O comando [Copiar ServiceFabricApplicationPackage](https://msdn.microsoft.com/library/mt125905.aspx) carrega o pacote de cluster arquivo da imagem. O cmdlet **Get-ImageStoreConnectionStringFromClusterManifest** , o que faz parte do módulo serviço ferro SDK PowerShell, é utilizado para obter a cadeia de ligação de arquivo de imagem.  Para importar o módulo SDK, execute:

```
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```

Pode copiar um pacote de aplicações a partir do *C:\users\ryanwi\Documents\Visual Studio 2015\Projects\MyApplication\myapplication\pkg\debug* para *c:\temp\MyApplicationType* (mudar o nome no directório de "depuração" para "MyApplicationType"). O exemplo seguinte carrega o pacote de:

~~~
PS C:\temp> dir

    Directory: c:\temp


Mode                LastWriteTime         Length Name                                                                                   
----                -------------         ------ ----                                                                                   
d-----        8/12/2016  10:23 AM                MyApplicationType                                                                          

PS C:\temp> tree /f .\Stateless1Pkg

C:\TEMP\MyApplicationType
│   ApplicationManifest.xml
|
└───Stateless1Pkg
  	|   ServiceManifest.xml
    │
    └───Code
    │   │  Microsoft.ServiceFabric.Data.dll
    │   │  Microsoft.ServiceFabric.Data.Interfaces.dll
    │   │  Microsoft.ServiceFabric.Internal.dll
    │   │  Microsoft.ServiceFabric.Internal.Strings.dll
    │   │  Microsoft.ServiceFabric.Services.dll
    │   │  ServiceFabricServiceModel.dll
    │   │  MyService.exe
    │   │  MyService.exe.config
    │   │  MyService.pdb
    │   │  System.Fabric.dll
    │   │  System.Fabric.Strings.dll
    │   │
    │   └───en-us
  	|         Microsoft.ServiceFabric.Internal.Strings.resources.dll
  	|         System.Fabric.Strings.resources.dll
  	|
    ├───Config
    │     Settings.xml
    │
    └───Data
          init.dat

PS C:\temp> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath MyApplicationType -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest))
Copy application package succeeded

PS D:\temp>
~~~

## <a name="register-the-application-package"></a>Registe-se o pacote de aplicação

Registar o pacote de aplicação faz com que o tipo de aplicação e versão declarados no manifesto da aplicação disponível para utilização. O sistema lê o pacote carregado no passo anterior, verifique se o pacote (equivalente a ser executado [Teste ServiceFabricApplicationPackage](https://msdn.microsoft.com/library/mt125950.aspx) localmente), o conteúdo do pacote do processo e copiar o pacote transformado para uma localização de sistema internos.

~~~
PS D:\temp> Register-ServiceFabricApplicationType MyApplicationType
Register application type succeeded

PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
DefaultParameters      : {}

PS D:\temp>
~~~

O comando [Register ServiceFabricApplicationType](https://msdn.microsoft.com/library/mt125958.aspx) devolve apenas depois do sistema tem copiados com êxito o pacote de aplicação. Quanto tempo este botão leva depende os conteúdos do pacote de aplicação. Se for necessário, o parâmetro **- TimeoutSec** possa ser utilizado para fornecer um tempo limite maior. (O tempo limite predefinido é 60 segundos.)

O comando [Get-ServiceFabricApplicationType](https://msdn.microsoft.com/library/mt125871.aspx) lista todas as versões de tipo de aplicação foi registado com êxito.

## <a name="create-the-application"></a>Criar a aplicação

Pode criar a instância de uma aplicação ao utilizar qualquer versão do tipo de aplicação que foi registado com êxito através do comando [Novo ServiceFabricApplication](https://msdn.microsoft.com/library/mt125913.aspx) . O nome de cada aplicação tem de iniciar com o *ferro:* esquema e de ser exclusivo para cada instância da aplicação. Quaisquer serviços predefinido definidos no manifesto da aplicação do tipo de aplicação de destino são criados neste momento.

~~~
PS D:\temp> New-ServiceFabricApplication fabric:/MyApp MyApplicationType AppManifestVersion1

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
ApplicationParameters  : {}

PS D:\temp> Get-ServiceFabricApplication  

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
ApplicationStatus      : Ready
HealthState            : OK
ApplicationParameters  : {}

PS D:\temp> Get-ServiceFabricApplication | Get-ServiceFabricService

ServiceName            : fabric:/MyApp/MyService
ServiceKind            : Stateless
ServiceTypeName        : MyServiceType
IsServiceGroup         : False
ServiceManifestVersion : SvcManifestVersion1
ServiceStatus          : Active
HealthState            : Ok

PS D:\temp>
~~~

O comando [Get-ServiceFabricApplication](https://msdn.microsoft.com/library/mt163515.aspx) lista todas as instâncias da aplicação que foram criadas com êxito, juntamente com o respetivo estado geral.

O comando [Get-ServiceFabricService](https://msdn.microsoft.com/library/mt125889.aspx) lista todas as instâncias de serviço que foram criadas com êxito dentro de uma instância da aplicação determinado. Serviços de predefinidos (se existirem) são listados aqui.

Várias instâncias da aplicação podem ser criadas para qualquer versão determinado de um tipo de aplicação registada. Cada instância da aplicação é executado no isolamento, com as suas próprias diretório de trabalho e o processo.

## <a name="remove-an-application"></a>Remover uma aplicação

Quando já não é necessária uma instância de uma aplicação, pode removê-permanentemente utilizando o comando [Remover ServiceFabricApplication](https://msdn.microsoft.com/library/mt125914.aspx) . Este comando remove automaticamente todos os serviços que pertencem à aplicação, assim, remover todos os Estado do serviço de forma permanente. Esta operação não pode ser revertida e estado da aplicação não pode ser recuperado.

~~~
PS D:\temp> Remove-ServiceFabricApplication fabric:/MyApp

Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded

PS D:\temp> Get-ServiceFabricApplication
PS D:\temp>
~~~

Quando já não é necessária uma versão específica de um tipo de aplicação, deverá /unregistê-lo utilizando o comando [Unregister ServiceFabricApplicationType](https://msdn.microsoft.com/library/mt125885.aspx) . Anular o registo de tipos de não utilizados disponibilização de espaço de armazenamento utilizado pelos conteúdos do pacote de aplicação desse tipo no arquivo de imagem. Um tipo de aplicação possa ser anulado desde que não existem aplicações são criadas instâncias contra ele e não pendentes aplicação atualiza os são referenciá-lo.

~~~
PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v1
DefaultParameters      : {}

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v2
DefaultParameters      : {}

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
DefaultParameters      : {}

PS D:\temp> Unregister-ServiceFabricApplicationType MyApplicationType AppManifestVersion1
Unregister application type succeeded

PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v1
DefaultParameters      : {}

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v2
DefaultParameters      : {}

PS D:\temp>
~~~

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copiar ServiceFabricApplicationPackage pede um ImageStoreConnectionString

O ambiente do serviço ferro SDK já deve ter as predefinições corretas configurar. Mas, se for necessário, ImageStoreConnectionString para todos os comandos deverá corresponder o valor que está a utilizar o cluster de serviço ferro. Pode localizar isto no manifesto cluster obtido através do comando [Get-ServiceFabricClusterManifest](https://msdn.microsoft.com/library/mt126024.aspx) :

~~~
PS D:\temp> Copy-ServiceFabricApplicationPackage .\MyApplicationType

cmdlet Copy-ServiceFabricApplicationPackage at command pipeline position 1
Supply values for the following parameters:
ImageStoreConnectionString:

PS D:\temp> Get-ServiceFabricClusterManifest
<ClusterManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]

PS D:\temp> Copy-ServiceFabricApplicationPackage .\MyApplicationType -ImageStoreConnectionString file:D:\ServiceFabric\Data\ImageStore
Copy application package succeeded

PS D:\temp>
~~~

## <a name="next-steps"></a>Próximos passos

[Atualização da aplicação de serviço ferro](service-fabric-application-upgrade.md)

[Introdução de estado de funcionamento do serviço ferro](service-fabric-health-introduction.md)

[Diagnosticar e resolver problemas de um serviço de ferro de serviço](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modelar uma aplicação no ferro de serviço](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-application-model.md
[11]: service-fabric-application-upgrade.md
