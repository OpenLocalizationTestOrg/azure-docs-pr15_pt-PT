<properties
   pageTitle="Um relatório e verificar o estado de funcionamento com Azure Service ferro | Microsoft Azure"
   description="Saiba como enviar relatórios de estado de funcionamento a partir do código de serviço e como verificar o estado de funcionamento do serviço, utilizando as ferramentas de monitorização do Estado de funcionamento que fornece Azure Service ferro."
   services="service-fabric"
   documentationCenter=".net"
   authors="toddabel"
   manager="mfussell"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/06/2016"
   ms.author="toddabel"/>

# <a name="report-and-check-service-health"></a>Comunicar e verificar o estado de funcionamento do serviço
Quando os seus serviços de encontrar problemas, sua capacidade para responder a e corrigir incidentes e falhas depende da sua capacidade para detetar rapidamente os problemas. Se comunicar problemas e falhas ao Gestor de estado de funcionamento do Azure Service ferro a partir do código de serviço, pode utilizar o estado de funcionamento padrão ferramentas que fornece ferro de serviço para verificar o estado de funcionamento de monitorização.

Existem duas formas que pode comunicar o estado de funcionamento de serviço:

- Utilize os objetos [partição](https://msdn.microsoft.com/library/system.fabric.istatefulservicepartition.aspx) ou [CodePackageActivationContext](https://msdn.microsoft.com/library/system.fabric.codepackageactivationcontext.aspx) .  
Pode utilizar o `Partition` e `CodePackageActivationContext` objetos para comunicar o estado de funcionamento dos elementos que fazem parte de contexto atual. Por exemplo, código que é executada como parte de uma réplica pode comunicar o estado de funcionamento apenas em que a réplica, partição que pertence a e a aplicação que é uma parte do.

- Utilizar `FabricClient`.   
Pode utilizar `FabricClient` para o estado de funcionamento do relatório a partir do código do serviço se o cluster não for [segura](service-fabric-cluster-security.md) ou se o serviço está em execução com privilégios de administrador. Não será VERDADEIRO na maioria dos cenários reais. Com `FabricClient`, pode comunicar o estado de funcionamento em qualquer entidade que é uma parte do cluster. Idealmente, no entanto, código do serviço deve só enviar relatórios que estão relacionados com a sua própria saúde.

Este artigo explica um exemplo que relatórios de estado de funcionamento do código de serviço. O exemplo também mostra como as ferramentas que fornece serviço ferro podem ser utilizadas para verificar o estado de funcionamento. Este artigo destina-se para ser uma breve introdução ao estado de funcionamento monitorização capacidades do serviço ferro. Para informações mais detalhadas, pode consultar a série de artigos aprofundadas sobre o estado de funcionamento que começam com a ligação no final deste artigo.

## <a name="prerequisites"></a>Pré-requisitos
Tem de ter os seguintes componentes instalados:

   * Visual Studio 2015
   * Serviço ferro SDK

## <a name="to-create-a-local-secure-dev-cluster"></a>Para criar um cluster de Dev Center seguro local
- Abra o PowerShell com privilégios de administrador e, execute os seguintes comandos.

![Comandos que mostram como criar um cluster de seguro Dev Center](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-secure-dev-cluster.png)

## <a name="to-deploy-an-application-and-check-its-health"></a>Implementar uma aplicação e verificar o estado de funcionamento

1. Abrir o Visual Studio como administrador.

2. Crie um projeto utilizando o modelo **Com estado de serviço** .

    ![Criar uma aplicação de serviço ferro com estado de serviço](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)

3. Prima **F5** para executar a aplicação no modo de depuração. A aplicação será implementada no cluster local.

4. Após a aplicação estiver em execução, o botão direito do rato no ícone de Gestor de Cluster locais na área de notificação e selecione **Gerir Cluster Local** no menu de atalho para abrir o Explorador de ferro de serviço.

    ![Abra o Explorador de ferro do serviço a partir da área de notificação](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)

5. O estado de funcionamento da aplicação deverá ser apresentado como esta imagem. Neste momento, a aplicação deverá saudável com sem erros.

    ![Aplicação Saudável no serviço ferro Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)

6. Também pode verificar o estado de funcionamento ao utilizar o PowerShell. Pode utilizar ```Get-ServiceFabricApplicationHealth``` para verificar o estado de funcionamento de uma aplicação e, pode utilizar ```Get-ServiceFabricServiceHealth``` para verificar o estado de funcionamento de um serviço. O relatório de estado de funcionamento para a mesma aplicação no PowerShell é nesta imagem.

    ![Aplicação Saudável no PowerShell](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## <a name="to-add-custom-health-events-to-your-service-code"></a>Para adicionar eventos de estado de funcionamento personalizado ao seu código de serviço
Os modelos do project ferro de serviço no Visual Studio contenham código de exemplo. Os seguintes passos mostram como pode comunicar eventos de estado de funcionamento personalizada a partir do código de serviço. Esses relatórios irão aparecer automaticamente nas ferramentas de padrão para a monitorização de estado de funcionamento que fornece ferro de serviço, como o serviço ferro Explorer, ver estado de funcionamento portal Azure e PowerShell.

1. Voltar a abrir a aplicação que criou anteriormente no Visual Studio ou criar uma nova aplicação utilizando o modelo do Visual Studio **Com estado de serviço** .

2. Abra o ficheiro Stateful1.cs e localize o `myDictionary.TryGetValueAsync` ligar o `RunAsync` método. Pode ver que este método devolve um `result` que contém o valor atual do contador porque está a lógica chave nesta aplicação manter uma contagem em execução. Se esta fosse uma aplicação real e se a falta de resultado representada uma falha de, que seria que pretende sinalizar esse evento.

3. Para um evento de estado de funcionamento quando a falta de resultado representa uma falha de um relatório, adicione os passos seguintes.

    um. Adicionar o `System.Fabric.Health` espaço de nomes para o ficheiro Stateful1.cs.

    ```csharp
    using System.Fabric.Health;
    ```

    b. Adicione o código seguinte após a `myDictionary.TryGetValueAsync` de chamadas

    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
    Vamos um relatório do Estado de funcionamento de réplica porque está a ser comunicado de um serviço de estado. O `HealthInformation` parâmetro armazena informações sobre o problema de estado de funcionamento que está a ser comunicado.

    Se tiver criado um serviço sem estado, utilize o seguinte código

    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
    ```

4. Se o seu serviço está em execução com privilégios de administrador ou se o cluster não estiver [seguro](service-fabric-cluster-security.md), também pode utilizar `FabricClient` para o estado de funcionamento do relatório conforme mostrado nos passos seguintes.  

    um. Criar o `FabricClient` instância depois do `var myDictionary` declaração.

    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```

    b. Adicione o código seguinte após a `myDictionary.TryGetValueAsync` de chamadas.

    ```csharp
    if (!result.HasValue)
    {
       var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.ServiceInitializationParameters.PartitionId,
            this.ServiceInitializationParameters.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));
        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```

5. Vamos simular esta falha e vê-la aparecem nas ferramentas de monitorização do Estado de funcionamento. Para simular a falha, comenta a primeira linha no código de elaboração de relatórios de estado de funcionamento que adicionou anteriormente. Depois de comentar a primeira linha, o código de aspeto o exemplo seguinte.

    ```csharp
    //if(!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
 Este código agora irá fogo este relatório de estado de funcionamento de cada vez `RunAsync` executa. Depois de efetuar a alteração, prima **F5** para executar a aplicação.

6. Após a aplicação estiver em execução, abra o Explorador de ferro de serviço para verificar o estado de funcionamento da aplicação. Desta vez, serviço ferro Explorer irá mostrar que a aplicação está danificada. Este é devido o erro que foi comunicado do código que adicionámos anteriormente.

    ![Aplicações danificadas no serviço ferro Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)

7. Se selecionar a réplica principal na vista de árvore do serviço ferro Explorer, irá ver o **Estado de funcionamento** indica um erro, demasiado. Serviço ferro Explorer também apresenta os detalhes do relatório de estado de funcionamento que foram adicionados à `HealthInformation` parâmetro o código. Pode ver os mesmos relatórios de estado de funcionamento no portal do Azure e no PowerShell.

    ![Estado de funcionamento de réplica no serviço ferro Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

Este relatório permanecerá no Gestor de estado de funcionamento até é substituído por outro relatório ou até que esta réplica é eliminada. Porque não tiver a ser definido `TimeToLive` para este relatório de estado de funcionamento na `HealthInformation` objecto, o relatório nunca irá expirar.

Recomendamos que deve ser comunicado estado de funcionamento no nível mais granular, que é neste caso a réplica. Também pode comunicar o estado de funcionamento no `Partition`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
this.Partition.ReportPartitionHealth(healthInformation);
```

Para o estado de funcionamento do relatório no `Application`, `DeployedApplication`, e `DeployedServicePackage`, utilizar `CodePackageActivationContext`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
var activationContext = FabricRuntime.GetActivationContext();
activationContext.ReportApplicationHealth(healthInformation);
```

## <a name="next-steps"></a>Próximos passos
[Vôo picado abrangente sobre estado de funcionamento do serviço ferro](service-fabric-health-introduction.md)
