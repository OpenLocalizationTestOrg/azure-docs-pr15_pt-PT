<properties
   pageTitle="Induzir caos em clusters de serviço ferro | Microsoft Azure"
   description="Utilizar APIs de serviço de análise de Cluster e de inserção para gerir caos no cluster."
   services="service-fabric"
   documentationCenter=".net"
   authors="motanv"
   manager="rsinha"
   editor="toddabel"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/19/2016"
   ms.author="motanv"/>

# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Induzir caos controlado em clusters ferro de serviço
Sistemas como nuvem infra-estruturas são implicitamente fiáveis distribuídos em grande escala. Azure Service ferro permite que os programadores escrever serviços fiáveis na parte superior de uma infraestrutura não fiável. Para escrever serviços robustos, os programadores tem de ser conseguir induzir falhas contra essa infraestrutura fiável para testar a estabilidade dos seus serviços.

A inserção e o serviço de análise de Cluster (também conhecido como o serviço de análise de falhas) permite que os programadores para induzir ações de falhas para testar serviços. No entanto, falhas simuladas alvo ajudá-lo apenas até ao momento. Para manter os testes adicional, pode utilizar caos.

Caos simula contínuas, intercaladas falhas (sem problemas e inesperadas) ao longo do cluster adicional períodos de tempo. Depois de configurar caos com a taxa e o tipo de falhas, pode iniciar ou pará-la através de c# APIs ou PowerShell para gerar falhas no cluster e no seu serviço.

Enquanto caos estiver em execução, gera diferentes eventos capturar o estado da executar no momento. Por exemplo, uma ExecutingFaultsEvent contém todas as falhas que estão a ser executadas nesse iteração. Um ValidationFailedEvent contém os detalhes de uma falha de que foi encontrado durante a validação de cluster. Pode chamar a API GetChaosReportAsync para obter o relatório de execuções caos.

## <a name="faults-induced-in-chaos"></a>Falhas induzidas em caos
Caos gera falhas ao longo de todo o cluster ferro de serviço e comprime falhas que visualizadas em meses ou anos para algumas horas. A combinação de falhas intercaladas com a taxa de alta falhas localiza casos canto que caso contrário, são perdidos. Este exercício caos orienta para uma melhoria significativa da qualidade de código do serviço.

Caos originar falhas das seguintes categorias:

 - Reiniciar um nó
 - Reiniciar um pacote de código implementado
 - Remover uma réplica
 - Reiniciar uma réplica
 - Mover uma réplica principal (configurável)
 - Mover uma réplica secundária (configurável)

Caos é executado nas várias iterações. Cada iteração consiste em falhas e validação cluster durante o período especificado. Pode configurar o tempo gasto para a cluster de regularização e para a validação teve êxito. Se for encontrada uma falha na validação cluster, caos gera e persistir um ValidationFailedEvent com o carimbo de hora UTC e os detalhes de falha.

Por exemplo, considere uma instância de caos está definido para executar o para uma hora com um máximo de três falhas em simultâneo. Caos originar três falhas e, em seguida, valida o estado de funcionamento do cluster. -Itera anterior transmite passo até que seja parado através da API StopChaosAsync ou explicitamente uma hora. Se o cluster ficar danificado no qualquer iteração (ou seja,-não regularização dentro de um período de tempo configurado), caos gera um ValidationFailedEvent. Este evento indica que algo funcionado mal e poderá ter aprofundar.

Na sua forma atual, caos originar apenas falhas de seguras. Isto significa que, na ausência de falhas externas, uma perda de quórum ou a perda de dados nunca ocorra.

## <a name="important-configuration-options"></a>Opções de configuração importantes
 - **TimeToRun**: Total de tempo que é executada caos antes de terminar com êxito. Pode deixar de caos antes de que tem a ser executado durante o período de TimeToRun através da API StopChaos.
 - **MaxClusterStabilizationTimeout**: A quantidade máxima de tempo a aguardar para cluster para se tornar Saudável antes de verificar novamente no mesmo. Aguardar é reduzir a carregar no cluster enquanto está a recuperar. As verificações executadas são:
    - Se o estado de funcionamento do cluster for OK
    - Se o estado de funcionamento do serviço está OK
    - Se o tamanho do conjunto de réplicas de destino é obtida para a partição de serviço
    - Que existem de sem réplicas InBuild
 - **MaxConcurrentFaults**: O número máximo de falhas em simultâneo, que são induzidos em cada iteração. São o caos o número, mais agressivos superior. Isto resulta em activações pós-falha mais complexas e as combinações de transição. Caos garante que, na ausência de falhas externas, não há perda quórum ou perda de dados, independentemente de um valor como elevado tem esta configuração.
 - **EnableMoveReplicaFaults**: ativa ou desativa as falhas que provocam réplicas principais ou secundárias a mover. Estas falhas estão desativadas por predefinição.
 - **WaitTimeBetweenIterations**: A quantidade de tempo a aguardar entre iterações, ou seja, após uma round de falhas e validação correspondente.
 - **WaitTimeBetweenFaults**: A quantidade de tempo a aguardar entre duas falhas consecutivas numa iteração.

## <a name="how-to-run-chaos"></a>Como executar caos
**C#:**

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Fabric;

using System.Diagnostics;
using System.Fabric.Chaos.DataStructures;

class Program
{
    private class ChaosEventComparer : IEqualityComparer<ChaosEvent>
    {
        public bool Equals(ChaosEvent x, ChaosEvent y)
        {
            return x.TimeStampUtc.Equals(y.TimeStampUtc);
        }

        public int GetHashCode(ChaosEvent obj)
        {
            return obj.TimeStampUtc.GetHashCode();
        }
    }

    static void Main(string[] args)
    {
        var clusterConnectionString = "localhost:19000";
        using (var client = new FabricClient(clusterConnectionString))
        {
            var startTimeUtc = DateTime.UtcNow;
            var stabilizationTimeout = TimeSpan.FromSeconds(30.0);
            var timeToRun = TimeSpan.FromMinutes(60.0);
            var maxConcurrentFaults = 3;

            var parameters = new ChaosParameters(
                stabilizationTimeout,
                maxConcurrentFaults,
                true, /* EnableMoveReplicaFault */
                timeToRun);

            try
            {
                client.TestManager.StartChaosAsync(parameters).GetAwaiter().GetResult();
            }
            catch (FabricChaosAlreadyRunningException)
            {
                Console.WriteLine("An instance of Chaos is already running in the cluster.");
            }

            var filter = new ChaosReportFilter(startTimeUtc, DateTime.MaxValue);

            var eventSet = new HashSet<ChaosEvent>(new ChaosEventComparer());

            while (true)
            {
                var report = client.TestManager.GetChaosReportAsync(filter).GetAwaiter().GetResult();

                foreach (var chaosEvent in report.History)
                {
                    if (eventSet.add(chaosEvent))
                    {
                        Console.WriteLine(chaosEvent);
                    }
                }

                // When Chaos stops, a StoppedEvent is created.
                // If a StoppedEvent is found, exit the loop.
                var lastEvent = report.History.LastOrDefault();

                if (lastEvent is StoppedEvent)
                {
                    break;
                }

                Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
            }
        }
    }
}
```
**PowerShell:**

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

$events = @{}
$now = [System.DateTime]::UtcNow

Start-ServiceFabricChaos -TimeToRunMinute $timeToRun -MaxConcurrentFaults $concurrentFaults -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec

while($true)
{
    $stopped = $false
    $report = Get-ServiceFabricChaosReport -StartTimeUtc $now -EndTimeUtc ([System.DateTime]::MaxValue)

    foreach ($e in $report.History) {

        if(-Not ($events.Contains($e.TimeStampUtc.Ticks)))
        {
            $events.Add($e.TimeStampUtc.Ticks, $e)
            if($e -is [System.Fabric.Chaos.DataStructures.ValidationFailedEvent])
            {
                Write-Host -BackgroundColor White -ForegroundColor Red $e
            }
            else
            {
                if($e -is [System.Fabric.Chaos.DataStructures.StoppedEvent])
                {
                    $stopped = $true
                }

                Write-Host $e
            }
        }
    }

    if($stopped -eq $true)
    {
        break
    }

    Start-Sleep -Seconds 1
}

Stop-ServiceFabricChaos
```
