<properties
   pageTitle="Implementar um executável existente para Azure Service ferro | Microsoft Azure"
   description="Instruções passo a passo sobre como compactar uma aplicação existente como convidado executável, para que possa ser implementado a um cluster de ferro de serviço"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="10/22/2016"
   ms.author="msfussell;mikhegn"/>

# <a name="deploy-a-guest-executable-to-service-fabric"></a>Implementar o convidado executável em ferro de serviço

Pode executar qualquer tipo de aplicação, como node.js, Java ou aplicações nativas no Azure Service ferro. Serviço ferro refere-se a estes tipos de aplicações como convidado executáveis.
Convidado executáveis são tratadas pelo serviço ferro como serviços sem estado. Como resultado, são colocados em nós num cluster, com base em disponibilidade e outras métricas. Este artigo descreve como compactar e implementar o convidado executável para um cluster de ferro de serviço, utilizando o Visual Studio ou um utilitário de linha de comandos.

Neste artigo, vamos abordar os passos para compactar convidado executável e implemente-o serviço ferro.  

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>Vantagens de executar o convidado executável no ferro de serviço

Existem várias vantagens que estão incluídos a executar o convidado executável num cluster ferro de serviço:

- Disponibilidade de alta. Aplicações que são executadas no serviço ferro são altamente disponibilizadas. Serviço ferro assegura que instâncias de uma aplicação estão a funcionar.
- Monitorizar o estado de funcionamento. Monitorização de estado de funcionamento do serviço ferro Deteta se uma aplicação está a ser executado e fornece informações de diagnóstico, se existir uma falha.   
- Gestão de ciclo de vida de aplicações. Para além de fornecer atualiza os com sem indisponibilidade, o serviço ferro fornece Reversão automática para a versão anterior se existir um evento de estado de funcionamento incorretas comunicado durante uma atualização.    
- Densidade. Pode executar várias aplicações num cluster, que elimina a necessidade de cada aplicação para ser executada no seu próprio hardware.


## <a name="overview-of-application-and-service-manifest-files"></a>Descrição geral da aplicação e ficheiros de manifesto de serviço

Como parte de implementação do convidado executável, é útil para compreender embalagem do serviço ferro e modelo de implementação como descrito [modelo de aplicação](service-fabric-application-model.md). O modelo da embalagem de serviço ferro depende de dois ficheiros XML: manifestos aplicações e serviços. A definição de esquema para os ficheiros ApplicationManifest.xml e ServiceManifest.xml é instalada com o SDK do serviço ferro em *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

* **Manifesto da aplicação** Manifesto da aplicação é utilizado para descrever o pedido. Lista os serviços que compõem-lo e outros parâmetros que são utilizados para definir como um ou mais serviços devem ser implementados, tal como o número de instâncias.

  No ferro serviço de uma aplicação é uma unidade de implementação e atualização. Pode ser actualizada uma aplicação como uma única unidade onde as potenciais falhas e reversões potenciais são geridas. Serviço ferro garante que o processo de atualização é efetuada com êxito, ou, se a atualização falhar, não deixe a aplicação num Estado desconhecido instável.

* **Serviço de manifesto** O manifesto serviço descreve os componentes de um serviço. Inclui dados, como o nome e tipo de serviço e respectivo código, configuração e dados. O manifesto serviço também inclui alguns parâmetros adicionais que podem ser utilizados para configurar o serviço assim que é implementada.


## <a name="application-package-file-structure"></a>Estrutura de ficheiro do pacote de aplicação
Para implementar uma aplicação do serviço ferro, a aplicação tem de seguir uma estrutura de directórios predefinido. O exemplo seguinte dessa estrutura.

```
|-- ApplicationPackageRoot
  	|-- GuestService1Pkg
        |-- Code
            |-- existingapp.exe
        |-- Config
            |-- Settings.xml
        |-- Data
        |-- ServiceManifest.xml
  	|-- ApplicationManifest.xml
```

O ApplicationPackageRoot contém o ficheiro de ApplicationManifest.xml que define a aplicação. Um subdirectório para cada serviço incluído na aplicação, é utilizado para conter todos os erros que o serviço requer – a ServiceManifest.xml e normalmente, os seguintes três directórios:

- *Código*. Este directório contém o código de serviço.
- *Config*. Este directório contém um ficheiro de Settings.xml (e outros ficheiros, se necessário) que o serviço pode aceder ao executar tempo para obter definições de configuração específicas.
- *Dados*. Este é um directório adicional para armazenar dados locais adicionais que poderá ter do serviço. Nota: Os dados devem ser utilizados para armazenar apenas efémeras dados. Serviço ferro não copiar/replicação alterações ao diretório dados se o serviço tem de ser reatribuída – por exemplo, durante activação pós-falha.

Nota: Não tem de criar o `config` e `data` directórios caso não precise-los.

## <a name="packaging-an-existing-executable"></a>Embalagem de um ficheiro executável existente

Quando embalagem um executável de convidado, pode escolher quer ao utilizar um modelo de projeto do Visual Studio ou [criar manualmente o pacote de aplicação](#manually). Utilizar o Visual Studio, a estrutura de pacote de aplicações e ficheiros manifest são criados pelo Assistente do project novo para si.

>[AZURE.NOTE] A forma mais fácil para compactar um Windows existente executável para um serviço é utilizar o Visual Studio.

## <a name="using-visual-studio-to-package-an-existing-executable"></a>Utilizando o Visual Studio para compactar um executável existente

O Visual Studio fornece um modelo de serviço do serviço ferro para o ajudar a implementar o convidado executável a um cluster de serviço ferro.

Aceda através dos seguintes passos para concluir a publicação:

1. Selecione ficheiro -> novo projeto e criar uma aplicação de serviço de ferro.
2. Selecione convidado executável como o modelo de serviço.
3. Clique em Procurar para selecionar a pasta com o seu ficheiro executável e preencha o resto dos parâmetros para criar o serviço.
    - *Comportamento do pacote de código* pode ser definido para copiar todo o conteúdo da pasta para o projecto do Visual Studio, que é útil quando o ficheiro executável não forem alterados. Se pretender executável a alterar e pretender a capacidade de Pegue compilações novas dinamicamente, pode optar por ligar para a pasta em vez disso. Tenha em atenção que pode utilizar pastas ligadas ao criar o projeto de aplicação no Visual Studio. Este contém ligações para a localização de origem a partir do projeto, tornando possíveis para actualizar o convidado executável no seu destino de origem, tendo essas actualizações tornam-se a parte do pacote de aplicação na compilação.
    - *Programa* – selecione o ficheiro executável que deve ser executado para iniciar o serviço.
    - *Argumentos* - especificar os argumentos que devem ser passados para o ficheiro executável. Pode ser uma lista dos parâmetros com argumentos.
    - *WorkingFolder* - Especifica o directório de trabalho para o processo de que vai ser iniciado. Pode especificar três valores:
        - `CodeBase`Especifica que o directório de trabalho vai ser definidas para o directório de código no pacote de aplicação (`Code` directório na estrutura de ficheiro apresentada anterior).
        - `CodePackage`Especifica que o directório de trabalho vai ser definidas na raiz do pacote de aplicação (`GuestService1Pkg` na estrutura de ficheiro apresentada anterior).
        - `Work`Especifica que os ficheiros são colocados num subdirectório denominado trabalho
4. Dê um nome ao seu serviço e clique em OK.
5. Se o seu serviço necessita de um ponto final para a comunicação, agora pode adicionar o protocolo, porta e tipo para o ficheiro ServiceManifest.xml. e.g. `<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" UriScheme="http" PathSuffix="myapp/" Type="Input" />`.
6. Agora pode utilizar o pacote e publicar ação contra seu cluster local através da depuração a solução no Visual Studio. Quando estiver pronto pode publicar a aplicação a um cluster remoto ou dar entrada a solução de controlo da origem.
7. Ir para o fim deste artigo para saber como pode ver o serviço de executável convidado em execução no serviço ferro Explorer.

<a id="manually"></a>
## <a name="manually-packaging-and-deploying-an-existing-executable"></a>Embalagem manualmente e implementar um executável existente
O processo de compactação manualmente um executável convidado baseia-se os seguintes passos:

1. Crie a estrutura de diretório do pacote.
2. Adicione ficheiros de código e de configuração da aplicação.
3. Edite o ficheiro de manifesto de serviço.
4. Edite o ficheiro manifesto da aplicação.

<!--
>[AZURE.NOTE] We do provide a packaging tool that allows you to create the ApplicationPackage automatically. The tool is currently in preview. You can download it from [here](http://aka.ms/servicefabricpacktool).
-->

### <a name="create-the-package-directory-structure"></a>Criar a estrutura de diretório do pacote
Pode começar por criar a estrutura do directório, conforme descrito anteriormente.

### <a name="add-the-applications-code-and-configuration-files"></a>Adicionar ficheiros de código e de configuração da aplicação
Depois de ter criado a estrutura do directório, pode adicionar o código da aplicação e ficheiros de configuração em directórios de código e config. Também pode criar directórios adicionais ou os seus subdiretórios em directórios do código ou config.

Serviço ferro faz uma xcopy do conteúdo do directório de raiz da aplicação, pelo que não existe nenhuma estrutura predefinida para utilizar além de criar dois directórios superiores, código e definições. (Pode escolher nomes diferentes se pretender. Obter mais detalhes são na secção seguinte.)

>[AZURE.NOTE] Certifique-se de que inclua todos os ficheiros/dependências que a aplicação necessita. Serviço ferro copia o conteúdo do pacote de aplicação em todos os nós no cluster onde serviços a aplicação irão ser implementada. O pacote de deve conter o código que precisa de executar a aplicação. Não recomendamos que partindo do princípio de que as dependências já estão instaladas.

### <a name="edit-the-service-manifest-file"></a>Editar o ficheiro de manifesto de serviço
O passo seguinte é editar o ficheiro de serviço manifesto para incluir as seguintes informações:

- O nome do tipo de serviço. Este é um ID que serviço ferro utiliza para identificar um serviço.
- O comando para utilizar para iniciar a aplicação (ExeHost).
- Qualquer script que precisa para executar para definir cima/configurar a aplicação (SetupEntrypoint).

Segue-se um exemplo de um `ServiceManifest.xml` ficheiro:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="NodeApp" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true"/>
   </ServiceTypes>
   <CodePackage Name="code" Version="1.0.0.0">
      <SetupEntryPoint>
         <ExeHost>
             <Program>scripts\launchConfig.cmd</Program>
         </ExeHost>
      </SetupEntryPoint>
      <EntryPoint>
         <ExeHost>
            <Program>node.exe</Program>
            <Arguments>bin/www</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
         </ExeHost>
      </EntryPoint>
   </CodePackage>
   <Resources>
      <Endpoints>
         <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
   </Resources>
</ServiceManifest>
```

Vamos abordar as diferentes partes do ficheiro que necessita de atualizar:

#### <a name="updating-the-servicetypes"></a>Atualizar a ServiceTypes

```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

- Pode escolher qualquer nome que pretende para `ServiceTypeName`. É utilizado o valor na `ApplicationManifest.xml` ficheiro para identificar o serviço.
- Precisa de especificar `UseImplicitHost="true"`. Este atributo indica ao serviço ferro que o serviço é baseado numa aplicação autónoma, para que todas as necessidades de serviço ferro fazer consiste em iniciá-la como um processo e monitorizar o seu estado de funcionamento.

#### <a name="updating-the-codepackage"></a>Atualizar a CodePackage
O elemento CodePackage Especifica a localização (e versão) de código do serviço.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

O `Name` elemento é utilizado para especificar o nome do diretório no pacote de aplicação que contém de código o serviço. `CodePackage`Também tem o `version` atributo. Isto pode ser utilizado para especificar a versão do código – e potencialmente também pode ser utilizado para atualizar o código do serviço utilizando infraestrutura de gestão de ciclo de vida de aplicação do serviço ferro.
#### <a name="optional-updating-the-setupentrypoint"></a>Opcional: Atualizar o SetupEntrypoint

```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
O elemento SetupEntrypoint é utilizado para especificar qualquer ficheiro executável ou batch que deve ser executado antes de código do serviço é iniciado. É um passo opcional, pelo que não necessita de ser incluídos não se existir inicialização/é necessária nenhuma configuração. O SetupEntryPoint é executada sempre que o serviço for reiniciado.

Existe apenas uma SetupEntrypoint, por isso scripts de configuração/config tem de ser agrupadas num ficheiro batch única se configuração/config a aplicação requer vários scripts. O SetupEntrypoint pode executar qualquer tipo de ficheiro – executável lote ficheiros e os cmdlets do PowerShell. Para obter mais detalhes, consulte o artigo [Configurar SetupEntryPoint](service-fabric-application-runas-security.md).

O exemplo anterior, a SetupEntrypoint é executado num ficheiro batch chamado `LaunchConfig.cmd` que está localizado o `scripts` subdirectório do directório de código (partindo do princípio que o elemento WorkingFolder está definido para código base).

#### <a name="updating-the-entrypoint"></a>Atualizar o ponto de entrada

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```

O `Entrypoint` elemento no ficheiro manifesto do serviço é utilizado para especificar como iniciar o serviço. O `ExeHost` elemento Especifica executável (e argumentos) que deverá ser utilizado para iniciar o serviço.

- `Program`Especifica o nome do executável que deve ser executado para iniciar o serviço.
- `Arguments`Especifica os argumentos que devem ser passados para o ficheiro executável. Pode ser uma lista dos parâmetros com argumentos.
- `WorkingFolder`Especifica o directório de trabalho para o processo de que vai ser iniciado. Pode especificar três valores:
    - `CodeBase`Especifica que o directório de trabalho vai ser definidas para o directório de código no pacote de aplicação (`Code` directório na estrutura de ficheiro anterior).
    - `CodePackage`Especifica que o directório de trabalho vai ser definidas na raiz do pacote de aplicação (`GuestService1Pkg` na estrutura de ficheiro anterior).
  - `Work`Especifica que os ficheiros são colocados num subdirectório denominado trabalho

O WorkingFolder é útil para configurar o directório de trabalho correto para que os caminhos relativos podem ser utilizados pelo scripts a aplicação ou inicialização.

#### <a name="updating-the-endpoints-and-registering-with-naming-service-for-communication"></a>Atualizar os pontos finais e registar com o serviço de nomenclatura para a comunicação

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
No exemplo anterior a `Endpoint` elemento Especifica os pontos finais que pode escutar a aplicação. Neste exemplo, a aplicação Node.js recebe http na porta 3000.

Além disso pode pedir ferro de serviço para publicar este ponto final para o serviço de nomenclatura para que outros serviços podem descobrir o endereço de ponto final para este serviço. Isto permite-lhe possam comunicar entre os serviços que estão executáveis convidado.
O endereço de ponto final publicados está do formulário `UriScheme://IPAddressOrFQDN:Port/PathSuffix`. `UriScheme`e `PathSuffix` são atributos opcionais. `IPAddressOrFQDN`é o endereço IP ou completamente qualificado nome de domínio do nó este executável é colocado na e é calculado por si.

No exemplo seguinte depois do serviço é implementado, no Explorador de ferro serviço vir um ponto final semelhante ao `http://10.1.4.92:3000/myapp/` publicadas para a instância do serviço ou se se tratar de um computador local que vê `http://localhost:3000/myapp/`. 

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000"  UriScheme="http" PathSuffix="myapp/" Type="Input" />
</Endpoints>
```
Pode utilizar estes endereços com o [proxy inverso](service-fabric-reverseproxy.md) para comunicar entre os serviços.

### <a name="edit-the-application-manifest-file"></a>Editar o ficheiro manifesto da aplicação

Depois de ter configurado o `Servicemanifest.xml` ficheiro, precisa de efetuar algumas alterações para o `ApplicationManifest.xml` ficheiro para se certificar de que o tipo de serviço correto e o nome são utilizadas.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

#### <a name="servicemanifestimport"></a>ServiceManifestImport

No `ServiceManifestImport` elemento, pode especificar um ou mais serviços que pretende incluir na aplicação. Serviços estão referenciados com `ServiceManifestName`, que especifica o nome do diretório onde o `ServiceManifest.xml` ficheiro está localizado.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

## <a name="set-up-logging"></a>Configurar o registo
Para executáveis de convidado, é útil para conseguir ver registos de consola para saber se os scripts de aplicação e configuração mostram todos os erros.
Pode ser configurado redireccionamento da consola do `ServiceManifest.xml` ficheiro utilizando a `ConsoleRedirection` elemento.

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="5" FileMaxSizeInKb="2048"/>
  </ExeHost>
</EntryPoint>
```

* `ConsoleRedirection`pode ser utilizado para redirecionar saída da consola (stdout e stderr) para um diretório de trabalho para que podem ser utilizadas para verificar que não existem erros durante a configuração ou a execução da aplicação no cluster ferro de serviço.

    * `FileRetentionCount`determina como muitos ficheiros são guardados no directório de trabalho. Um valor de 5, por exemplo, significa que estão armazenados os ficheiros de registo para os cinco execuções anterior no directório de trabalho.
    * `FileMaxSizeInKb`Especifica o tamanho máximo de ficheiros de registo.

Ficheiros de registo são guardados dos directórios de trabalho do serviço. Para determinar onde estão localizados os ficheiros, é necessário utilizar o serviço ferro Explorer para determinar qual o nó que o serviço está em execução e qual directório de trabalho está a ser utilizado. Este processo é abrangido neste artigo.

## <a name="deployment"></a>Implementação
O último passo é implementar a aplicação. O seguinte script do PowerShell mostra como implementar a aplicação para o cluster de desenvolvimento local e iniciar um novo serviço do serviço ferro.

```PowerShell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MultipleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```
Um serviço de serviço ferro pode ser implementado em várias "configurações." Por exemplo, pode ser implementada como uma ou várias instâncias ou pode ser implementada de forma a que uma instância do serviço em cada nó do cluster ferro de serviço.

O `InstanceCount` parâmetro da `New-ServiceFabricService` cmdlet é utilizada para especificar quantas instâncias do serviço, deverão ser iniciadas no cluster ferro de serviço. Pode definir a `InstanceCount` valor, dependendo do tipo de aplicação que está a implementar. Os dois cenários mais comuns são:

* `InstanceCount = "1"`. Neste caso, apenas uma instância do serviço é implementada no cluster. Programador do serviço ferro determina qual o serviço vai ser implementada no nó.

* `InstanceCount ="-1"`. Neste caso, uma instância do serviço é implementada em todos os nós cluster ferro de serviço. O resultado está a ter uma (e apenas uma) instância do serviço para cada nó no cluster.

Esta é uma configuração útil para aplicações front-end (por exemplo, um resto ponto final de) uma vez que necessitam de aplicações de cliente para "ligar" a qualquer de nós do cluster para utilizar o ponto final. Também pode ser utilizada esta configuração quando, por exemplo, todos os nós do cluster serviço ferro estão ligados a um balanceador de carga para que o tráfego de cliente pode ser distribuído ao longo o serviço que está a ser executado em todos os nós no cluster.

## <a name="check-your-running-application"></a>Verificar a sua aplicação em execução

No Explorador de ferro do serviço, identificar o nó onde o serviço está em execução. Neste exemplo,-é executado em Nó1:

![Nó onde o serviço está a ser executado](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

Se navegar para o nó e navegue para a aplicação, verá as informações essenciais nó, incluindo a sua localização no disco.

![Localização no disco](./media/service-fabric-deploy-existing-app/locationondisk2.png)

Se procurar ao diretório através do Explorador de servidor, pode encontrar o directório de trabalho e uma pasta de registo do serviço como apresentado na seguinte imagem.

![Localização do registo de](./media/service-fabric-deploy-existing-app/loglocation.png)


## <a name="next-steps"></a>Próximos passos
Neste artigo, aprendeu como compactar um executável convidado e implemente-o serviço ferro. Como um passo seguinte, pode consultar adicional conteúdo deste tópico.

- [Exemplo de embalagem e implementar o convidado executável GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/GuestExe/SimpleApplication), incluindo uma ligação para a versão de pré-lançamento da ferramenta de embalagem
- [Implementar várias convidado executáveis](service-fabric-deploy-multiple-apps.md)
- [Criar uma aplicação de serviço ferro primeira utilizando o Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
