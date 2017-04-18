<properties
   pageTitle="Atualização de serviço ferro aplicação através do PowerShell | Microsoft Azure"
   description="Este artigo explica a experiência de implementar uma aplicação de serviço ferro, alterar o código e gradual saída de uma atualização através do PowerShell."
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


# <a name="service-fabric-application-upgrade-using-powershell"></a>Atualização da aplicação de serviço ferro através do PowerShell

> [AZURE.SELECTOR]
- [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
- [Visual Studio](service-fabric-application-upgrade-tutorial.md)

<br/>

O mais frequentemente utilizado e abordagem atualização recomendada é a atualização de ficha técnica em monitorizada.  Azure Service ferro monitoriza o estado de funcionamento da aplicação que está a ser atualizada com base num conjunto de políticas de estado de funcionamento de. Assim que um domínio de atualização (UD) é atualizado, o serviço ferro avalia o estado de funcionamento da aplicação e avança para o domínio de atualização seguinte ou falha a atualização, dependendo das políticas de estado de funcionamento.

Pode ser executada uma atualização de aplicações monitorizadas utilizando o geridos ou nativo APIs, PowerShell ou restantes. Para obter instruções sobre como efectuar uma atualização utilizando o Visual Studio, consulte o artigo [atualizar a sua aplicação utilizando o Visual Studio](service-fabric-application-upgrade-tutorial.md).

Com actualizações de ficha técnica em ferro serviço monitorizadas, o administrador da aplicação pode configurar a política de avaliação do Estado de funcionamento que utiliza o serviço ferro para determinar se a aplicação está em bom estada. Além disso, o administrador pode configurar a ação a tomar quando a avaliação de estado de funcionamento falha (por exemplo, efetuar uma reposição automática.) Esta secção explica uma atualização monitorizada para um das amostras SDK, que utiliza o PowerShell.

## <a name="step-1-build-and-deploy-the-visual-objects-sample"></a>Passo 1: Criar e implementar a amostra de objectos visuais


Criar e publicar a aplicação ao clicar no projeto de aplicação, **VisualObjectsApplication** e selecionar o comando **Publicar** .  Para mais informações, consulte o artigo [Atualizar tutorial de aplicação de serviço ferro](service-fabric-application-upgrade-tutorial.md).  Em alternativa, pode utilizar o PowerShell para implementar a aplicação.

> [AZURE.NOTE] Antes de qualquer um dos comandos serviço ferro podem ser utilizados no PowerShell, tem primeiro de se ligar à cluster utilizando o `Connect-ServiceFabricCluster` cmdlet. Da mesma forma, é assumido que o Cluster já foi definido no seu computador local para cima. Consulte o artigo sobre [como configurar o seu ambiente de desenvolvimento ferro de serviço](service-fabric-get-started.md).

Depois de criar o projeto no Visual Studio, pode utilizar o comando do PowerShell **ServiceFabricApplicationPackage copiar** para copiar o pacote de aplicação para o ImageStore. O passo seguinte é registar a aplicação para o tempo de execução do serviço ferro utilizando o cmdlet **Register ServiceFabricApplicationPackage** . O passo final é iniciar uma instância da aplicação ao utilizar o cmdlet **ServiceFabricApplication novo** .  Estes três passos são semelhantes a utilizar o item de menu **Implementar** no Visual Studio.

Agora, pode utilizar o [Serviço ferro Explorador para ver o cluster e a aplicação](service-fabric-visualizing-your-cluster.md). A aplicação tem um serviço web que pode ser compreendê-las no Internet Explorer, escrevendo [http://localhost:8081/visualobjects](http://localhost:8081/visualobjects) na barra de endereço.  Deverá ver algumas objetos flutuantes visuais mover-se no ecrã.  Para além disso, pode utilizar **Get-ServiceFabricApplication** para verificar o estado de aplicação.

## <a name="step-2-update-the-visual-objects-sample"></a>Passo 2: Atualizar a amostra de objectos visuais

Poderá reparar que com a versão que foi implementada no passo 1, os objetos visuais não rodar. Vamos atualizar esta aplicação para um local onde os objectos visuais também rodar.

Selecione projeto VisualObjects.ActorService da solução VisualObjects e abra o ficheiro StatefulVisualObjectActor.cs. Dentro do ficheiro, navegue para o método `MoveObject`, comentar `this.State.Move()`e remova os comentários `this.State.Move(true)`. Esta alteração roda os objectos depois do serviço é atualizado.

Também é necessário atualizar o ficheiro de *ServiceManifest.xml* (em PackageRoot) do projeto **VisualObjects.ActorService**. Atualize o *CodePackage* e a versão do serviço para 2.0 e as linhas correspondentes no ficheiro *ServiceManifest.xml* .
Pode utilizar a opção Visual Studio *Editar ficheiros manifesto* depois do botão direito do rato sobre a solução para fazer as alterações de ficheiro de manifesto.


Depois das alterações são feitas, o manifesto deverá ter o aspeto semelhante ao seguinte (partes realçados mostram as alterações):

```xml
<ServiceManifestName="VisualObjects.ActorService" Version="2.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code" Version="2.0">
```

Agora o ficheiro de *ApplicationManifest.xml* (encontrado no projeto **VisualObjects** a solução **VisualObjects** ) é atualizado para a versão 2.0 do projeto **VisualObjects.ActorService** . Além disso, a versão da aplicação é atualizada para 2.0.0.0 de 1.0.0.0. O *ApplicationManifest.xml* deve aspeto fragmento que se segue:

```xml
<ApplicationManifestxmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VisualObjects" ApplicationTypeVersion="2.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.ActorService" ServiceManifestVersion="2.0" />
```


Agora, crie o projeto ao selecionar apenas o projecto **ActorService** e, em seguida, clicar e selecionar a opção **Criar** no Visual Studio. Se selecionar **reconstruir todas**, devem actualizar as versões para todos os projetos, uma vez que o código seria foram alteradas. Seguinte, vamos compactar a aplicação actualizada ao clicar no ***VisualObjectsApplication***, selecionar o Menu de ferro de serviço e escolher **pacote**. Esta ação cria um pacote de aplicações que pode ser implementado.  A aplicação actualizada está pronta para ser implementada.


## <a name="step-3--decide-on-health-policies-and-upgrade-parameters"></a>Passo 3: Decidir sobre as políticas de estado de funcionamento e atualizar parâmetros

Familiarizar-se com a [aplicação atualizar parâmetros](service-fabric-application-upgrade-parameters.md) e o [processo de atualização](service-fabric-application-upgrade.md) para obter uma boa compreensão dos vários parâmetros atualização, tempos limite e critério de estado de funcionamento aplicado. Para este tutorial, o critério de avaliação de estado de funcionamento do serviço está definido como a predefinição (e recomendado) valores, o que significa que todos os serviços e instâncias devem ser _Saudável_ após a atualização.  

No entanto, vamos aumentar a *HealthCheckStableDuration* para 60 segundos (para que os serviços são saudáveis durante, pelo menos, 20 segundos antes da atualização avança para o domínio de atualização seguinte).  Vamos também definir o *UpgradeDomainTimeout* para ser 1200 segundos e o *UpgradeTimeout* para ser 3000 segundos.

Por fim, vamos também definir o *UpgradeFailureAction* anular. Esta opção requer ferro de serviço recuperar a aplicação para a versão anterior se encontrar problemas durante a atualização. Assim sendo, ao iniciar a atualização (no passo 4), os seguintes parâmetros são especificados:

FailureAction = anulação

HealthCheckStableDurationSec = 60

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000


## <a name="step-4-prepare-application-for-upgrade"></a>Passo 4: Preparar a aplicação para a atualização

Agora a aplicação está incorporado e pronta para ser atualizada. Se abrir uma janela do PowerShell como administrador e tipo **Get-ServiceFabricApplication**, deve informar que o informa de que é o tipo de aplicação 1.0.0.0 de **VisualObjects** é foram implementados.  

O pacote de aplicação está armazenado no caminho relativo seguinte onde que não comprimido SDK de ferro serviço - *Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug*. Deverá localizar uma pasta de "Pacote" nesse directório, a localização onde armazenou o pacote de aplicação. Verifique se as marcas de hora para se certificar de que é a compilação mais recente (poderá ter de modificar os caminhos corretamente, assim).

Agora vamos copiar o pacote de aplicação atualizada para o ImageStore ferro de serviço (onde estão armazenados os pacotes da aplicação ao serviço ferro). O parâmetro *ApplicationPackagePathInImageStore* informa ferro serviço onde foi possível encontrar o pacote de aplicação. Vamos tiver sido adicionado a aplicação actualizada "VisualObjects\_V2" com o seguinte comando (poderá ter de modificar caminhos novamente corretamente).

```powershell
Copy-ServiceFabricApplicationPackage  -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package
-ImageStoreConnectionString fabric:ImageStore   -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

O passo seguinte é registar esta aplicação com ferro de serviço, que pode ser executada utilizando o seguinte comando:

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

Se o comando anterior não tiver êxito, é provável que precisa de um reconstruir de todos os serviços. Tal como mencionado no passo 2, poderá ter de atualizar a sua versão de Serviçoweb também.

## <a name="step-5-start-the-application-upgrade"></a>Passo 5: Iniciar a atualização de aplicação

Agora, vamos está pronto para iniciar a atualização da aplicação utilizando o seguinte comando:

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


O nome da aplicação é o mesmo, tal como foi descrito no ficheiro *ApplicationManifest.xml* . Serviço ferro utiliza este nome para identificar qual é a aplicação é introdução atualizada. Se definir os limites de tempo para ser demasiado pequeno, que poderá encontrar uma mensagem de erro que diz o problema. Consulte a secção Resolução de problemas ou aumentar os limites de tempo.

Agora, como a receita atualização da aplicação, pode monitorizá-lo através do Explorador de ferro do serviço, ou utilizando o PowerShell seguinte comando: **Get-ServiceFabricApplicationUpgrade ferro: / VisualObjects**.

Em alguns minutos, o estado que recebeu utilizando o comando PowerShell anterior, deverá indicar que foram atualizados todos os domínios de atualização (concluído). E deverá localizar os objetos visuais na janela do browser iniciaram rodar!

Pode tentar atualizar da versão 2 para a versão 3 ou da versão 2 para a versão 1 como um exercício. Mover da versão 2 para a versão 1 também é considerada uma atualização. Reproduzir com limites de tempo e políticas de estado de funcionamento para tornar si próprio familiarizado com os mesmos. Quando estiver a implementar a um cluster Azure, os parâmetros tem de ser definida corretamente. É boa ideia definir os limites de tempo de espera.


## <a name="next-steps"></a>Próximos passos

[Atualizar a sua aplicação utilizando o Visual Studio](service-fabric-application-upgrade-tutorial.md) orienta uma atualização de aplicação utilizando o Visual Studio.

Controlar como a aplicação actualiza utilizando [parâmetros de atualização](service-fabric-application-upgrade-parameters.md).

Torne o seu actualizações de aplicação compatíveis ao aprender a utilizar [serialização de dados](service-fabric-application-upgrade-data-serialization.md).

Saiba como utilizar funcionalidades avançadas ao atualizar a aplicação ao referir [Tópicos avançados](service-fabric-application-upgrade-advanced.md).

Corrigir problemas comuns em actualizações de aplicação ao referir os passos em [actualizações de aplicação de resolução de problemas](service-fabric-application-upgrade-troubleshooting.md).
