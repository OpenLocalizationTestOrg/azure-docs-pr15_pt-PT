<properties
   pageTitle="Especificar pontos finais de serviço do serviço ferro | Microsoft Azure"
   description="Como descrever recursos de ponto final do manifesto serviço, incluindo como configurar os pontos finais HTTPS"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="subramar"/>

# <a name="specify-resources-in-a-service-manifest"></a>Especificar recursos do manifesto serviço

## <a name="overview"></a>Descrição geral

O manifesto serviço permite que os recursos utilizados pelo serviço para ser alterado/declarados sem alterar o código compilado. Azure Service ferro suporta configuração dos recursos de ponto final para o serviço. O acesso aos recursos que estão especificados no manifesto serviço pode ser controlado através do SecurityGroup no manifesto da aplicação. A declaração de recursos permite que estes recursos para ser alteradas no momento da implementação, que significa que o serviço não é necessário introduzir um novo mecanismo de configuração. A definição de esquema para o ficheiro ServiceManifest.xml é instalada com o serviço ferro SDK e ferramentas para *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

## <a name="endpoints"></a>Pontos finais

Quando um recurso de ponto final é definido no manifesto do serviço, o serviço ferro atribui portas o intervalo de porta aplicação reservadas quando não for explicitamente especificada uma porta. Por exemplo, observe o ponto final *ServiceEndpoint1* especificado no fragmento de manifesto fornecido após neste parágrafo. Para além disso, serviços também podem pedir uma porta específica de um recurso. Podem ser atribuídas réplicas do serviço em execução em nós de cluster diferentes números de porta diferente, enquanto réplicas de um serviço em execução no mesmo nó partilham a porta. As réplicas do serviço, em seguida, podem utilizar estas portas, conforme seja necessário para a replicação e ouvir para pedidos de cliente.

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

Consulte [configurar com estado serviços fiáveis](service-fabric-reliable-services-configuration.md) para ler mais sobre referenciar os pontos finais partir das definições do pacote de configuração de ficheiros (settings.xml).

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>Exemplo: especificar um ponto final HTTP do seu serviço

O seguinte manifesto de serviço define um recurso de ponto final TCP e recursos de ponto final HTTP duas na &lt;recursos&gt; elemento.

Os pontos finais HTTP são automaticamente que ACL faria ferro de serviço.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         This name must match the string used in the RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="Stateful1Type" HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>Stateful1.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an
       independently updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port number on which to
           listen. Note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
      <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
      <Endpoint Name="ServiceEndpoint3" Protocol="https"/>

      <!-- This endpoint is used by the replicator for replicating the state of your service.
           This endpoint is configured through the ReplicatorSettings config section in the Settings.xml
           file under the ConfigPackage. -->
      <Endpoint Name="ReplicatorEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>Exemplo: especificar um ponto final HTTPS do seu serviço

O protocolo HTTPS fornece autenticação de servidor e também é utilizado para encriptar comunicações de cliente-servidor. Para ativar o seu serviço de serviço ferro HTTPS, especifique o protocolo na *recursos -> pontos finais -> ponto final* secção do manifesto serviço, conforme mostrado anterior para o ponto final *ServiceEndpoint3*.

>[AZURE.NOTE] Protocolo de um serviço não pode ser alterado durante a atualização da aplicação sem-que constituem uma quebra de altera.


Eis um exemplo ApplicationManifest que precisa de configurar para HTTPS. A impressão digital para o certificado tem de ser fornecida. O EndpointRef é uma referência a EndpointResource no ServiceManifest, para o qual define o protocolo HTTPS. Pode adicionar mais do que um EndpointCertificate.  

```
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Application1Type"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="2" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <EndpointBindingPolicy CertificateRef="TestCert1" EndpointRef="ServiceEndpoint3"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types when an instance of this
         application type is created. You can also create one or more instances of service type by using the
         Service Fabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Stateful1">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
  <Certificates>
    <EndpointCertificate Name="TestCert1" X509FindValue="FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF F0" X509StoreName="MY" />  
  </Certificates>
</ApplicationManifest>
```
