<properties 
pageTitle="Comunicação para funções de serviços em nuvem | Microsoft Azure" 
description="Instâncias de função nos serviços em nuvem, podem ter os pontos finais (http, https, tcp, udp) para eles definidas que comunicam com o exterior ou entre outras instâncias de função." 
services="cloud-services" 
documentationCenter="" 
authors="Thraka" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="09/06/2016" 
ms.author="adegeo"/>

# <a name="enable-communication-for-role-instances-in-azure"></a>Permitir a comunicação para instâncias de funções no azure

Funções de serviço de nuvem comunicam através de ligações internas e externas. Ligações externas são denominadas **pontos finais de entrada** enquanto ligações internas chamam-se **os pontos finais internos**. Este tópico descreve como modificar a [definição de serviço](cloud-services-model-and-package.md#csdef) para criar os pontos finais.


## <a name="input-endpoint"></a>Ponto final de entrada
O ponto final de entrada é utilizado quando pretender expor uma porta para fora. Especifique o tipo de protocolo e a porta do ponto final, em seguida, aplica-se para portas internas e externas para o ponto final. Se pretender, pode especificar uma porta interna diferente para o ponto final com o atributo [PortaLocal](https://msdn.microsoft.com/library/azure/gg557552.aspx#InputEndpoint) .

O ponto final de entrada, pode utilizar os seguintes protocolos: **http, https, tcp e udp**.

Para criar um ponto final de entrada, adicione o elemento subordinado **InputEndpoint** para o elemento de **pontos finais** da função de um web ou trabalho.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
</Endpoints> 
```

## <a name="instance-input-endpoint"></a>Ponto final de entrada instância
Os pontos finais de entrada do instância são semelhantes a apresentar os pontos finais mas permite-lhe mapear portas direccionada específicas para cada instância de função individuais utilizando o reencaminhamento de portas do Balanceador de carga. Pode especificar uma única porta para o público ou um intervalo de portas.

O ponto de entrada final instância só pode utilizar **tcp** ou **udp** como o protocolo.

Para criar um ponto de entrada final instância, adicione o elemento subordinado **InstanceInputEndpoint** para o elemento de **pontos finais** da função de um web ou trabalho.

```xml
<Endpoints>
  <InstanceInputEndpoint name="Endpoint2" protocol="tcp" localPort="10100">
    <AllocatePublicPortFrom>
      <FixedPortRange max="10109" min="10105" />
    </AllocatePublicPortFrom>
  </InstanceInputEndpoint>
</Endpoints>
```

## <a name="internal-endpoint"></a>Ponto final interno
Os pontos finais internos estão disponíveis para a instância de instância comunicação. A porta é opcional e se for omitido, uma porta dinâmica é atribuída para o ponto final. Um intervalo de porta pode ser utilizado. Existe um limite de cinco os pontos finais internos por cada função.

O ponto final interno pode utilizar os seguintes protocolos: **http, tcp e udp, qualquer**.

Para criar um ponto de entrada interno final, adicione o elemento subordinado do **InternalEndpoint** para o elemento de **pontos finais** da função de um web ou trabalho.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" port="8999" />
</Endpoints> 
```

Também pode utilizar um intervalo de porta.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any">
    <FixedPortRange max="8995" min="8999" />
  </InternalEndpoint>
</Endpoints>
```


## <a name="worker-roles-vs-web-roles"></a>Funções de trabalho vs. funções da Web

Existe uma diferença mínima com os pontos finais ao trabalhar com trabalhador e funções da web. A função web tem de ter pelo menos um ponto final entrado única utilizando o protocolo **HTTP** .


```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
  <!-- more endpoints may be declared after the first InputEndPoint -->
</Endpoints>
```

## <a name="using-the-net-sdk-to-access-an-endpoint"></a>Utilizar o .NET SDK para aceder a um ponto final
A biblioteca do Azure gerido fornece métodos de instâncias de uma função comunicar o tempo de execução. A partir de código em execução no interior de uma instância de papel, pode obter informações sobre a existência de outras instâncias de função e os respetivos pontos finais e informações sobre a instância atual de função.

> [AZURE.NOTE] Apenas pode obter informações acerca de instâncias de funções que estiver a executar o no seu serviço de nuvem e que definem o ponto final interno, pelo menos. Não consegue obter dados sobre instâncias de funções em execução num serviço diferente.

Pode utilizar a propriedade de [instâncias](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.role.instances.aspx) para obter instâncias de uma função. Em primeiro lugar utilize o [CurrentRoleInstance](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.currentroleinstance.aspx) para devolver uma referência para a instância atual de função e, em seguida, utilizar a propriedade de [função](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstance.role.aspx) para devolver uma referência para a função própria.

Ao ligar para uma instância de função através de programação através do SDK do .NET, é fácil relativamente aceder às informações de ponto final. Por exemplo, após se já tiver ligado a um ambiente do função específica, pode obter a porta de um ponto final específico com este código:

```csharp
int port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["StandardWeb"].IPEndpoint.Port;
```

A propriedade de **instâncias** devolve um conjunto de objetos **RoleInstance** . Nesta coleção de sempre contém a instância atual. Se a função não definir um ponto final interno, a coleção de inclui a instância atual, mas não outros instâncias. O número de ocorrências de funções na coleção de será sempre 1 nas maiúsculas/minúsculas onde nenhum ponto final interno está definido para a função. Se a função define um ponto final interno, respectivas instâncias são podem ser encontradas na runtime e o número de instâncias na coleção de corresponderão para o número de instâncias especificada para a função no ficheiro de configuração de serviço.

> [AZURE.NOTE] A biblioteca do Azure gerido não fornecem um meio para determinar a saúde dos outros instâncias de função, mas pode implementar essas avaliações de estado de funcionamento si mesmo se o seu serviço tem esta funcionalidade. Pode utilizar o [Azure diagnóstico](cloud-services-dotnet-diagnostics.md) para obter informações sobre a função instâncias em execução.

Para determinar o número da porta para um ponto final interno numa instância de papel, pode utilizar a propriedade [InstanceEndpoints](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstance.instanceendpoints.aspx) para devolver um objeto de dicionário que contém os nomes de ponto final e as respetivas correspondentes endereços IP e portas. A propriedade [IPEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstanceendpoint.ipendpoint.aspx) devolve o endereço IP e porta para um ponto final de especificado. A propriedade **PublicIPEndpoint** devolve a porta para um ponto final de balanceamento de carga. Parte do endereço de IP da propriedade **PublicIPEndpoint** não é utilizada.

Eis um exemplo que se repete instâncias de função.

```csharp
foreach (RoleInstance roleInst in RoleEnvironment.CurrentRoleInstance.Role.Instances)
{
    Trace.WriteLine("Instance ID: " + roleInst.Id);
    foreach (RoleInstanceEndpoint roleInstEndpoint in roleInst.InstanceEndpoints.Values)
    {
        Trace.WriteLine("Instance endpoint IP address and port: " + roleInstEndpoint.IPEndpoint);
    }
}
```

Eis um exemplo de uma função de trabalho que obtém o ponto final exposto através da definição de serviço e começa a ouvir para ligações.

> [AZURE.WARNING] Este código só irá funcionar para um serviço implementado. Quando a ser executada em emulador calcular Azure, são ignorados elementos de configuração de serviço que criar pontos finais de porta direta (elementos de**InstanceInputEndpoint** ).

```csharp
using System;
using System.Diagnostics;
using System.Linq;
using System.Net;
using System.Net.Sockets;
using System.Threading;
using Microsoft.WindowsAzure;
using Microsoft.WindowsAzure.Diagnostics;
using Microsoft.WindowsAzure.ServiceRuntime;
using Microsoft.WindowsAzure.StorageClient;

namespace WorkerRole1
{
  public class WorkerRole : RoleEntryPoint
  {
    public override void Run()
    {
      try
      {
        // Initialize method-wide variables
        var epName = "Endpoint1";
        var roleInstance = RoleEnvironment.CurrentRoleInstance;
        
        // Identify direct communication port
        var myPublicEp = roleInstance.InstanceEndpoints[epName].PublicIPEndpoint;
        Trace.TraceInformation("IP:{0}, Port:{1}", myPublicEp.Address, myPublicEp.Port);

        // Identify public endpoint
        var myInternalEp = roleInstance.InstanceEndpoints[epName].IPEndpoint;
                
        // Create socket listener
        var listener = new Socket(
          myInternalEp.AddressFamily, SocketType.Stream, ProtocolType.Tcp);
                
        // Bind socket listener to internal endpoint and listen
        listener.Bind(myInternalEp);
        listener.Listen(10);
        Trace.TraceInformation("Listening on IP:{0},Port: {1}",
          myInternalEp.Address, myInternalEp.Port);

        while (true)
        {
          // Block the thread and wait for a client request
          Socket handler = listener.Accept();
          Trace.TraceInformation("Client request received.");

          // Define body of socket handler
          var handlerThread = new Thread(
            new ParameterizedThreadStart(h =>
            {
              var socket = h as Socket;
              Trace.TraceInformation("Local:{0} Remote{1}",
                socket.LocalEndPoint, socket.RemoteEndPoint);

              // Shut down and close socket
              socket.Shutdown(SocketShutdown.Both);
              socket.Close();
            }
          ));

          // Start socket handler on new thread
          handlerThread.Start(handler);
        }
      }
      catch (Exception e)
      {
        Trace.TraceError("Caught exception in run. Details: {0}", e);
      }
    }

    public override bool OnStart()
    {
      // Set the maximum number of concurrent connections 
      ServicePointManager.DefaultConnectionLimit = 12;

      // For information on handling configuration changes
      // see the MSDN topic at http://go.microsoft.com/fwlink/?LinkId=166357.
      return base.OnStart();
    }
  }
}
```

## <a name="network-traffic-rules-to-control-role-communication"></a>Regras de tráfego de rede para controlar as comunicações de função
Depois de definir os pontos finais internos, pode adicionar regras de tráfego de rede (com base nos pontos finais que criou) para o controlo como instâncias de função podem comunicar com os outros. O diagrama seguinte mostra alguns cenários comuns para controlar a comunicação de função:

![Cenários de regras de tráfego de rede] (./media/cloud-services-enable-communication-role-instances/scenarios.png "Cenários de regras de tráfego de rede")

Exemplo de código que se segue mostra as definições de funções para as funções mostradas no diagrama de anterior. Cada definição de função inclui pelo menos um ponto final interno definido:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalTCP1" protocol="tcp" />
    </Endpoints>
  </WebRole>
  <WorkerRole name="WorkerRole1">
    <Endpoints>
      <InternalEndpoint name="InternalTCP2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
  <WorkerRole name="WorkerRole2">
    <Endpoints>
      <InternalEndpoint name="InternalTCP3" protocol="tcp" />
      <InternalEndpoint name="InternalTCP4" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

> [AZURE.NOTE] Restrição de comunicação entre funções pode ocorrer com os pontos finais internos de ambos corrigido e portas automaticamente atribuídas.

Por predefinição, depois de um ponto final interno estiver definido, comunicação pode fluir a partir de qualquer função para o ponto final interno de uma função sem restrições. Para restringir a comunicação, tem de adicionar um elemento **NetworkTrafficRules** para o elemento **ServiceDefinition** no ficheiro de definição de serviço.

### <a name="scenario-1"></a>Cenário 1
Apenas para permitir o tráfego de rede de **WebRole1** para **WorkerRole1**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-2"></a>Cenário 2
Permite apenas o tráfego de rede **WebRole1** **WorkerRole1** e **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-3"></a>Cenário 3
Permite apenas o tráfego de rede **WebRole1** **WorkerRole1**e **WorkerRole1** para **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WorkerRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-4"></a>Cenário 4
Permite apenas o tráfego de rede **WebRole1** **WorkerRole1**, **WebRole1** para **WorkerRole2**e **WorkerRole1** para **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo >
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WorkerRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo >
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP4" roleName="WorkerRole2"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

Uma referência de esquema XML para os elementos utilizados acima pode ser encontrada [aqui](https://msdn.microsoft.com/library/azure/gg557551.aspx).

## <a name="next-steps"></a>Próximos passos
Leia mais sobre o [modelo](cloud-services-model-and-package.md)de serviço na nuvem.