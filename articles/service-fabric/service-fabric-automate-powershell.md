<properties
    pageTitle="Automatizar gestão de aplicações de serviço ferro utilizando o PowerShell | Microsoft Azure"
    description="Implementar, atualizar, testar e remover as aplicações do serviço ferro utilizando o PowerShell."
    services="service-fabric"
    documentationCenter=".net"
    authors="rwike77"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-fabric"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/25/2016"
    ms.author="ryanwi"/>

# <a name="automate-the-application-lifecycle-using-powershell"></a>Automatizar o ciclo de vida de aplicação através do PowerShell

Podem ser automatizados muitos aspetos do [ciclo de vida de aplicação de serviço ferro](service-fabric-application-lifecycle.md) .  Este artigo mostra como utilizar o PowerShell para automatizar tarefas comuns para implementar, atualizar, remover e testar Azure Service ferro aplicações.  Gerido e HTTP APIs para a gestão de aplicação também estão disponíveis. Consulte o artigo [aplicação ciclo de vida](service-fabric-application-lifecycle.md) para obter mais informações.  

## <a name="prerequisites"></a>Pré-requisitos
Antes de mover as tarefas no artigo, certifique-se de que a:

+ Familiarize-se com os conceitos de serviço ferro descritos na [Descrição geral técnica do serviço ferro](service-fabric-technical-overview.md).
+ [Instalar as ferramentas runtime, SDK e](service-fabric-get-started.md), que também instala o módulo do PowerShell **ServiceFabric** .
+ [Execução de script do PowerShell ativar](service-fabric-get-started.md#enable-powershell-script-execution).
+ Inicie um cluster local.  Inicie uma nova janela do PowerShell como administrador e, em seguida, execute o script de configuração de cluster a partir da pasta SDK:`& "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"`
+ Antes de executar qualquer comandos do PowerShell neste artigo, pela primeira vez ligar ao local serviço ferro cluster utilizando [**ServiceFabricCluster ligar**](https://msdn.microsoft.com/library/azure/mt125938.aspx):`Connect-ServiceFabricCluster localhost:19000`
+ As seguintes tarefas necessitam de um pacote de aplicação v1 para implementar e um pacote de aplicação v2 para a atualização. Transferir a [aplicação de exemplo **WordCount** ](http://aka.ms/servicefabricsamples) (localizada nas amostras Introdução). Criar e compactar a aplicação no Visual Studio (botão direito do rato no **WordCount** no Explorador de soluções e selecione **pacote**). Copiar o pacote de v1 no `C:\ServiceFabricSamples\Services\WordCount\WordCount\pkg\Debug` para `C:\Temp\WordCount`. Copiar `C:\Temp\WordCount` para `C:\Temp\WordCountV2`, a criar o pacote de aplicação v2 para a atualização. Abrir `C:\Temp\WordCountV2\ApplicationManifest.xml` num editor de texto. No elemento de **ApplicationManifest** , altere o atributo **ApplicationTypeVersion** a partir de "1.0.0" para "2.0.0" para atualizar o número da versão de aplicação. Guarde o ficheiro ApplicationManifest.xml alterado.

## <a name="task-deploy-a-service-fabric-application"></a>Tarefa: Implementar uma aplicação de serviço ferro

Depois de já criadas e empacotados a aplicação (ou transferido o pacote de aplicação), pode implementar a aplicação para um cluster de serviço ferro local. Implementação envolve carregar o pacote de aplicação, a registar o tipo de aplicação e criar a instância da aplicação. Utilize as instruções nesta secção para implementar uma nova aplicação a um cluster.

### <a name="step-1-upload-the-application-package"></a>Passo 1: Carregar o pacote de aplicação
Carregar o pacote de aplicação para o arquivo de imagem coloca-o numa localização acessível a componentes de serviço ferro internos.  O pacote de aplicação contém a configuração manifesto da aplicação, manifestos de serviço e código, necessárias e pacotes de dados para criar a aplicação e instâncias do serviço.  O comando [**Copiar ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt125905.aspx) carrega o pacote. Por exemplo:

```powershell
Copy-ServiceFabricApplicationPackage C:\Temp\WordCount\ -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

### <a name="step-2-register-the-application-type"></a>Passo 2: Registar o tipo de aplicação
Registar o pacote de aplicação faz com que o tipo de aplicação e versão declarados no manifesto da aplicação disponível para utilização. O sistema lê o pacote carregado no passo 1, verifique se o pacote (equivalente a ser executado [**Teste ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt125950.aspx) localmente), o conteúdo do pacote do processo e copiar o pacote transformado para uma localização de sistema internos.  Execute o cmdlet [**Register ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125958.aspx) :

```powershell
Register-ServiceFabricApplicationType WordCount
```
Para ver todos os tipos de aplicação registados no cluster, execute o cmdlet [Get-ServiceFabricApplicationType](https://msdn.microsoft.com/library/azure/mt125871.aspx) :

```powershell
Get-ServiceFabricApplicationType
```

### <a name="step-3-create-the-application-instance"></a>Passo 3: Criar a instância da aplicação
Uma aplicação é possível criar instâncias ao utilizar qualquer versão do tipo de aplicação que foi registado com êxito, utilizando o comando [**Novo ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt125913.aspx) . O nome de cada aplicação for declarado na implementar o tempo e tem de iniciar com o **ferro:** esquema e de ser exclusivo para cada instância da aplicação. O nome do tipo de aplicação e a versão de tipo de aplicação são declarados no ficheiro **ApplicationManifest.xml** do pacote de aplicação. Se quaisquer serviços predefinido foram definidos no manifesto da aplicação do tipo de aplicação de destino, em seguida, aqueles são criadas neste momento.

```powershell
New-ServiceFabricApplication fabric:/WordCount WordCount 1.0.0
```

O comando [**Get-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt163515.aspx) lista todas as instâncias da aplicação que foram criadas com êxito, juntamente com o respetivo estado geral. O comando [**Get-ServiceFabricService**](https://msdn.microsoft.com/library/azure/mt125889.aspx) lista todas as instâncias de serviço que foram criadas com êxito dentro de uma instância da aplicação determinado. Serviços de predefinidos (se existir) estão listados.

```powershell
Get-ServiceFabricApplication

Get-ServiceFabricApplication | Get-ServiceFabricService
```

## <a name="task-upgrade-a-service-fabric-application"></a>Tarefa: Atualizar uma aplicação de serviço ferro
Pode atualizar uma aplicação de serviço ferro anteriormente implementada com um pacote de aplicação atualizada. Esta tarefa actualizações a aplicação de WordCount que foi implementada "tarefa: implementar uma aplicação de serviço ferro." Leia através da [aplicação de serviço ferro atualizar](service-fabric-application-upgrade.md) para obter mais informações.

Para manter as coisas simples para este exemplo, apenas o número da versão de aplicação foi atualizado no pacote de aplicação WordCountV2 criado nos pré-requisitos. Um cenário mais real implicar atualizar os seus ficheiros de código, configuração ou os dados de serviço e, em seguida, a reformulação repetida e embalagem a aplicação com números de versão atualizada.  

### <a name="step-1-upload-the-updated-application-package"></a>Passo 1: Carregar o pacote de aplicação atualizada
A aplicação de v1 WordCount está pronta para ser atualizada. Se abrir uma janela do PowerShell como administrador e tipo [**Get-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt163515.aspx), consulte que versão 1.0.0 do tipo de aplicação WordCount é implementada.  

Agora, copie o pacote de aplicação atualizada ao arquivo de imagem do serviço ferro (onde estão armazenados os pacotes da aplicação ao serviço ferro). O parâmetro **ApplicationPackagePathInImageStore** informa ferro serviço onde foi possível encontrar o pacote de aplicação. O seguinte comando copia o pacote de aplicação para **WordCountV2** na loja imagem:  

```powershell
Copy-ServiceFabricApplicationPackage C:\Temp\WordCountV2\ -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCountV2

```
### <a name="step-2-register-the-updated-application-type"></a>Passo 2: Registar o tipo de aplicação atualizada
O passo seguinte é registar a nova versão da aplicação com ferro de serviço, que podem ser efetuadas por utilizando o cmdlet [**ServiceFabricApplicationType de registo**](https://msdn.microsoft.com/library/azure/mt125958.aspx) :

```powershell
Register-ServiceFabricApplicationType WordCountV2
```

### <a name="step-3-start-the-upgrade"></a>Passo 3: Iniciar a atualização
Vários parâmetros de atualização, tempos limite e critérios de estado de funcionamento podem ser aplicados a actualizações de aplicação. Leia os documentos [aplicação atualizar parâmetros](service-fabric-application-upgrade-parameters.md) e [do processo de atualização](service-fabric-application-upgrade.md) para obter mais informações. Todos os serviços e instâncias devem ser _Saudável_ após a atualização.  Configurar o **HealthCheckStableDuration** para 60 segundos (para que os serviços são saudáveis durante, pelo menos, 20 segundos antes da atualização avança para o domínio de atualização seguinte).  Também defina o **UpgradeDomainTimeout** 1200 segundos e o **UpgradeTimeout** para 3000 segundos. Por fim, defina o **UpgradeFailureAction** para **anulação**, que pede que serviço ferro reverte a aplicação para a versão anterior se falhas forem encontradas durante a atualização.

Agora pode começar a atualização da aplicação ao utilizar o cmdlet [**ServiceFabricApplicationUpgrade iniciar**](https://msdn.microsoft.com/library/azure/mt125975.aspx) :

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/WordCount -ApplicationTypeVersion 2.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000  -FailureAction Rollback -Monitored
```

Note que o nome da aplicação é o mesmo que o nome da aplicação v1.0.0 anteriormente implementado (ferro: / WordCount). Serviço ferro utiliza este nome para identificar qual é a aplicação é introdução atualizada. Se definir os limites de tempo para ser demasiado pequeno, poderá encontrar uma mensagem de erro de limite de tempo que diz o problema. Referir-se a [actualizações de aplicação de resolução de problemas](service-fabric-application-upgrade-troubleshooting.md)ou aumentar os limites de tempo.

### <a name="step-4-check-upgrade-progress"></a>Passo 4: Verificar o progresso atualização
Pode monitorizar o progresso de atualização de aplicação através do [Explorador de ferro de serviço](service-fabric-visualizing-your-cluster.md), ou ao utilizar o cmdlet [**Get-ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt125988.aspx) :

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/WordCount
```

Em alguns minutos, o cmdlet [Get-ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/azure/mt125988.aspx) mostra que tenham sido atualizados todos os domínios de atualização (concluído).

## <a name="task-test-a-service-fabric-application"></a>Tarefa: Testar uma aplicação de serviço ferro

Para escrever os serviços de alta qualidade, os programadores tem de ser conseguir induzir falhas de infraestrutura fiável para testar a estabilidade dos seus serviços. Serviço ferro dá os programadores a capacidade de induzir ações de falhas e testar serviços na presença de falhas utilizando os cenários de teste caos e activação pós-falha.  Leia através de [Descrição geral de Testability](service-fabric-testability-overview.md) para obter informações adicionais.

### <a name="step-1-run-the-chaos-test-scenario"></a>Passo 1: Executar o cenário de teste caos
O cenário de teste caos gera falhas ao longo de todo o cluster ferro de serviço. O cenário comprime falhas geralmente vistas sobre meses ou anos para algumas horas. A combinação de falhas intercaladas com uma taxa de alta falhas localiza casos canto que caso contrário, teriam perdidos. O exemplo seguinte é executado o cenário de teste caos para 60 minutos.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```

### <a name="step-2-run-the-failover-test-scenario"></a>Passo 2: Execute o cenário de teste activação pós-falha
A activação pós-falha teste destinos cenário uma partição de serviço específico em vez de todo o cluster e deixa outros serviços afetados. O cenário itera uma sequência de simulada falhas de validação de serviço enquanto executa a lógica empresarial. Uma falha na validação do serviço indica um problema de que necessita ainda mais o inquérito. O teste de activação pós-falha originar apenas uma falha de cada vez, por oposição a cenário de teste caos, que pode levar falhas múltiplos. O exemplo seguinte é executado o teste de activação pós-falha para 60 minutos contra o ferro: / WordCount/WordCountService serviço.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/WordCount/WordCountService"

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindUniformInt64 -PartitionKey 1
```

## <a name="task-remove-a-service-fabric-application"></a>Tarefa: Remover uma aplicação de serviço ferro
Pode eliminar uma instância de uma aplicação de implementado, remover o tipo de aplicação aprovisionada do cluster e remover o pacote de aplicação a partir do ImageStore.

### <a name="step-1-remove-an-application-instance"></a>Passo 1: Remover uma instância da aplicação
Quando já não é necessária uma instância de uma aplicação, pode removê-permanentemente utilizando o cmdlet [**ServiceFabricApplication remover**](https://msdn.microsoft.com/library/azure/mt125914.aspx) . Esta ação remove automaticamente todos os serviços que pertencem a aplicação, remover todos os Estado do serviço de forma permanente. Esta operação não pode ser revertida e estado da aplicação não pode ser recuperado.

```powershell
Remove-ServiceFabricApplication fabric:/WordCount
```

### <a name="step-2-unregister-the-application-type"></a>Passo 2: Anular o registo o tipo de aplicação
Quando já não precisar de uma versão específica de um tipo de aplicação, anular o registo-lo ao utilizar o cmdlet [**Unregister ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125885.aspx) . Anular o registo de tipos de não utilizados disponibilização de espaço de armazenamento utilizado pelo pacote de aplicações na loja imagem. Um tipo de aplicação possa ser anulado desde que não existem aplicações criadas instâncias contra-la ou pendente actualizações de aplicação referenciá-lo.

```powershell
Unregister-ServiceFabricApplicationType WordCount 1.0.0
```

### <a name="step-3-remove-the-application-package"></a>Passo 3: Remover o pacote de aplicação
Depois do tipo de aplicação não está registado, pode ser removido o pacote de aplicação a partir da loja de imagem, utilizando o cmdlet [**ServiceFabricApplicationPackage remover**](https://msdn.microsoft.com/library/azure/mt163532.aspx) .

```powershell
Remove-ServiceFabricApplicationPackage -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

## <a name="next-steps"></a>Próximos passos
[Ciclo de vida de aplicação de serviço ferro](service-fabric-application-lifecycle.md)

[Implementar uma aplicação](service-fabric-deploy-remove-applications.md)

[Atualização de aplicação](service-fabric-application-upgrade.md)

[Cmdlets de serviço ferro Azure](https://msdn.microsoft.com/library/azure/mt125965.aspx)

[Cmdlets de testability serviço ferro Azure](https://msdn.microsoft.com/library/azure/mt125844.aspx)
