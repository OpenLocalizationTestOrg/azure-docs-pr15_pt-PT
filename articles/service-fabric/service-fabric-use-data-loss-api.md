<properties
   pageTitle="Como invocar perda de dados no serviço ferro serviços | Microsoft Azure"
   description="Descreve como utilizar a perda de dados api"
   services="service-fabric"
   documentationCenter=".net"
   authors="LMWF"
   manager="rsinha"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/19/2016"
   ms.author="lemai"/>
   
# <a name="how-to-invoke-data-loss-on-services"></a>Como invocar perda de dados serviços

>[AZURE.WARNING] Este documento descrevem como causar perda de dados nos seus serviços e deverá ser utilizado com cuidado.

## <a name="introduction"></a>Introdução
Pode invocar perda de dados numa partição do seu serviço de ferro por StartPartitionDataLossAsync() chamada.  Esta api utiliza a inserção e o serviço de análise para trabalhar para fazer com que as condições de perda de dados.

## <a name="using-the-fault-injection-and-analysis-service"></a>Utilizar a inserção e o serviço de análise

A inserção e o serviço de análise atualmente suporta as seguintes APIs no gráfico abaixo.  No lado direito do gráfico mostra o cmdlet do PowerShell correspondente.  Consulte a documentação do msdn sobre cada API para mais informações sobre cada um deles.

|           API C#                    |         Cmdlet do PowerShell                      |
|-------------------------------------|-----------------------------------------------:|
|[StartPartitionDataLossAsync] [dl]   |[Iniciar ServiceFabricPartitionDataLoss] [psdl]   |
|[StartPartitionQuorumLossAsync] [ql] |[Iniciar ServiceFabricPartitionQuorumLoss] [psql] |
|[StartPartitionRestartAsync] [rp]    |[Iniciar ServiceFabricPartitionRestart] [psrp]    |

## <a name="conceptual-overview-of-running-a-command"></a>Descrição geral conceptual do executar um comando

O serviço de análise de inserção e utiliza um modelo assíncrono onde que iniciar o comando com uma API, designados de API "Iniciar" neste documento, em seguida, verifica o progresso deste comando utilizando um "GetProgress" API até que atingiu um Estado terminal ou cancelá-lo.
Para iniciar um comando, ligue a API "Iniciar" para a API correspondente.  Esta API devolve quando a inserção e o serviço de análise aceitou o pedido.  No entanto, tal não indicar como extremidade tem executar um comando, ou mesmo se começou ainda.  Para poder verificar progresso de um comando, ligue para "GetProgress" API que corresponde à API anteriormente denominado "Iniciar".  "GetProgress" API irá devolver um objeto que indica o estado atual do comando dentro da respetiva propriedade de estado.  É executado um comando indefinidamente até:

1.  Que é concluído com êxito.  Se chamar "GetProgress" no mesmo neste caso, estado o objeto de progresso vai ser concluído.
2.  Encontrar um erro fatal.  Se chamar "GetProgress" no mesmo neste caso, estado o objeto de progresso irá falhar
3.  Cancelar a através de [CancelTestCommandAsync]  [ cancel] API ou [Parar ServiceFabricTestCommand]  [ cancelps] cmdlet do PowerShell.  Se chamar "GetProgress" no mesmo neste caso, estado o objeto de progresso será cancelada ou ForceCancelled, dependendo do que API um argumento.  Consulte a documentação para [CancelTestCommandAsync]  [ cancel] para obter mais detalhes.


## <a name="details-of-running-a-command"></a>Detalhes de executar um comando

Para poder começar um comando, contacte a API iniciar com os argumentos esperados.  Todos os APIs iniciar tem um argumento de Guid denominado IDOperação.  Qual deve controle do argumento IDOperação, uma vez que é utilizado para controlar o progresso deste comando.  Tem passou para "GetProgress" API para poder controlar o progresso do comando.  A IDOperação tem de ser exclusiva.

Depois de com êxito chamar a API iniciar, GetProgress API deve ser chamado reproduzida continuamente até que seja concluída a propriedade de State o objeto de progresso devolvidos.  Todos os [do FabricTransientException]  [ fte] e OperationCanceledException deve ser tentada novamente.
Quando o comando atingiu um Estado terminal (concluído, falhado ou cancelada), a propriedade de resultado de um objeto de progresso devolvida terá informações adicionais.  Se o estado estiver concluído, Result.SelectedPartition.PartitionId irá conter o id da partição que foi selecionado.  Result.Exception será nulo.  Se o estado pode falhar, Result.Exception terão o motivo a inserção e serviço de análise falhar o comando.  Result.SelectedPartition.PartitionId terão o id da partição que foi selecionado.  Em algumas situações, o comando não pode ter até ao momento suficiente precedidos para escolher uma partição.  Nesse caso, a PartitionId será 0.  Se o estado é cancelado, Result.Exception será nulo.  Como as maiúsculas/minúsculas falhado, Result.SelectedPartition.PartitionId terão o id da partição que foi escolhido mas, se o comando não houve extremidade suficiente para fazê-lo, será 0.  Consulte também referir-se para o exemplo abaixo.

O código de exemplo abaixo mostra como iniciar, em seguida, verifique o progresso de um comando para causar perda de dados numa partição específica.

```csharp
    static async Task PerformDataLossSample()
    {
        // Create a unique operation id for the command below
        Guid operationId = Guid.NewGuid();

        // Note: Use the appropriate overload for your configuration
        FabricClient fabricClient = new FabricClient();

        // The name of the target service
        Uri targetServiceName = new Uri("fabric:/MyService");

        // The id of the target partition inside the target service
        Guid targetPartitionId = new Guid("00000000-0000-0000-0000-000002233445");

        PartitionSelector partitionSelector = PartitionSelector.PartitionIdOf(targetServiceName, targetPartitionId);

        // Start the command.  Retry OperationCanceledException and all FabricTransientException's.  Note when StartPartitionDataLossAsync completes
        // successfully it only means the Fault Injection and Analysis Service has saved the intent to perform this work.  It does not say anything about the progress
        // of the command.
        while (true)
        {
            try
            {
                await fabricClient.TestManager.StartPartitionDataLossAsync(operationId, partitionSelector, DataLossMode.FullDataLoss).ConfigureAwait(false);
                break;
            }
            catch (OperationCanceledException)
            {
            }
            catch (FabricTransientException)
            {
            }

            await Task.Delay(TimeSpan.FromSeconds(1.0d)).ConfigureAwait(false);
        }

        PartitionDataLossProgress progress = null;

        // Poll the progress using GetPartitionDataLossProgressAsync until it is either Completed or Faulted.  In this example, we're assuming
        // the command won't be cancelled.        

        while (true)
        {
            try
            {
                progress = await fabricClient.TestManager.GetPartitionDataLossProgressAsync(operationId).ConfigureAwait(false);
            }
            catch (OperationCanceledException)
            {
                continue;
            }
            catch (FabricTransientException)
            {
                continue;
            }

            if (progress.State == TestCommandProgressState.Completed)
            {
                Console.WriteLine("Command '{0}' completed successfully", operationId);

                // In a terminal state .Result.SelectedPartition.PartitionId will have the chosen partition
                Console.WriteLine("  Printing selected partition='{0}'", progress.Result.SelectedPartition.PartitionId);
                break;
            }
            else if (progress.State == TestCommandProgressState.Faulted)
            {
                // If State is Faulted, the progress object's Result property's Exception property will have the reason why.
                Console.WriteLine("Command '{0}' failed with '{1}'", operationId, progress.Result.Exception);
                break;
            }
            else
            {
                Console.WriteLine("Command '{0}' is currently Running", operationId);
            }

            await Task.Delay(TimeSpan.FromSeconds(5.0d)).ConfigureAwait(false);
        }
    }
```

O exemplo abaixo mostra como utilizar o PartitionSelector para escolher uma partição aleatória de um serviço especificado:

```csharp
    static async Task PerformDataLossUseSelectorSample()
    {
        // Create a unique operation id for the command below
        Guid operationId = Guid.NewGuid();

        // Note: Use the appropriate overload for your configuration
        FabricClient fabricClient = new FabricClient();

        // The name of the target service
        Uri targetServiceName = new Uri("fabric:/SampleService ");

        // Use a PartitionSelector that will have the Fault Injection and Analysis Service choose a random partition of “targetServiceName”
        PartitionSelector partitionSelector = PartitionSelector.RandomOf(targetServiceName);

        // Start the command.  Retry OperationCanceledException and all FabricTransientException's.  Note when StartPartitionDataLossAsync completes
        // successfully it only means the Fault Injection and Analysis Service has saved the intent to perform this work.  It does not say anything about the progress
        // of the command.
        while (true)
        {
            try
            {
                await fabricClient.TestManager.StartPartitionDataLossAsync(operationId, partitionSelector, DataLossMode.FullDataLoss).ConfigureAwait(false);
                break;
            }
            catch (OperationCanceledException)
            {
            }
            catch (FabricTransientException)
            {
            }
            catch (Exception e)
            {
                Console.WriteLine("Unexpected exception '{0}'", e);
                throw;
            }

            await Task.Delay(TimeSpan.FromSeconds(1.0d)).ConfigureAwait(false);
        }

        PartitionDataLossProgress progress = null;

        // Poll the progress using GetPartitionDataLossProgressAsync until it is either Completed or Faulted.  In this example, we're assuming
        // the command won't be cancelled.

        while (true)
        {
            try
            {
                progress = await fabricClient.TestManager.GetPartitionDataLossProgressAsync(operationId).ConfigureAwait(false);
            }
            catch (OperationCanceledException)
            {
                continue;
            }
            catch (FabricTransientException)
            {
                continue;
            }

            if (progress.State == TestCommandProgressState.Completed)
            {
                Console.WriteLine("Command '{0}' completed successfully", operationId);

                Console.WriteLine("Printing progress.Result:");
                Console.WriteLine("  Printing selected partition='{0}'", progress.Result.SelectedPartition.PartitionId);

                break;
            }
            else if (progress.State == TestCommandProgressState.Faulted)
            {
                // If State is Faulted, the progress object's Result property's Exception property will have the reason why.
                Console.WriteLine("Command '{0}' failed with '{1}', SelectedPartition {2}", operationId, progress.Result.Exception, progress.Result.SelectedPartition);
                break;
            }
            else
            {
                Console.WriteLine("Command '{0}' is currently Running", operationId);
            }

            await Task.Delay(TimeSpan.FromSeconds(1.0d)).ConfigureAwait(false);
        }
    }
```

## <a name="history-and-truncation"></a>Histórico e truncamento

Depois de um comando um Estado terminal, respectivos metadados permanecerá na inserção e o serviço de análise para uma certa altura, antes de serão removido para poupar espaço.  Se "GetProgress" chama-se utilizando IDOperação de um comando após foi removida, irá devolver uma FabricException com um código de erro KeyNotFound.

[dl]: https://msdn.microsoft.com/library/azure/mt693569.aspx
[ql]: https://msdn.microsoft.com/library/azure/mt693558.aspx
[rp]: https://msdn.microsoft.com/library/azure/mt645056.aspx
[psdl]: https://msdn.microsoft.com/library/mt697573.aspx
[psql]: https://msdn.microsoft.com/library/mt697557.aspx
[psrp]: https://msdn.microsoft.com/library/mt697560.aspx
[cancel]: https://msdn.microsoft.com/library/azure/mt668910.aspx
[cancelps]: https://msdn.microsoft.com/library/mt697566.aspx
[fte]: https://msdn.microsoft.com/library/azure/system.fabric.fabrictransientexception.aspx
