
<properties
   pageTitle="Atualização da aplicação: atualizar parâmetros | Microsoft Azure"
   description="Descreve os parâmetros relacionados com a atualizar uma aplicação de serviço ferro, incluindo verificações de integridade para efetuar e políticas para anular automaticamente a atualização."
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



# <a name="application-upgrade-parameters"></a>Parâmetros de atualização da aplicação

Este artigo descreve os vários parâmetros que se aplicam durante a atualização de uma aplicação do Azure Service ferro. Os parâmetros incluem o nome e a versão da aplicação. Estes são os botões que controlam os tempos limite e os controlos de estado de funcionamento que são aplicados durante a atualização e especificam as políticas que devem ser aplicadas quando o falha de uma atualização.


<br>

| Parâmetro | Descrição |
| --- | --- |
| ApplicationName | Nome da aplicação que está a ser atualizada. Exemplos: ferro: / VisualObjects, ferro: / ClusterMonitor  |
| TargetApplicationTypeVersion | A versão da aplicação escreva que os destinos atualização. |
| FailureAction | A ação tomada pelo serviço ferro quando a atualização falha. A aplicação poderá ser revertida para a versão anterior à atualização (anular) ou a atualização pode ser terminada no domínio atual para a atualização. Neste caso, o modo de atualização também é alterado para Manual. Valores permitidos são anulação e Manual. |
| HealthCheckWaitDurationSec | O tempo a aguardar (em segundos) após a atualização já terminou no domínio atualização antes de serviço ferro avalia o estado de funcionamento da aplicação. Também pode ser considerada esta duração como hora de que uma aplicação deverá estar em execução antes de que pode ser considerado saudável. Se a verificação de integridade transmite, o processo de atualização avança para o domínio de atualização seguinte.  Se a verificação de integridade falhar, espera ferro de serviço para um intervalo de (o UpgradeHealthCheckInterval) antes de repetir a verificação de integridade novamente até que seja atingido a HealthCheckRetryTimeout. A predefinição e valor recomendado é 0 segundos. |
| HealthCheckRetryTimeoutSec | A duração (em segundos) ferro desse serviço continua a efectuar a avaliação do Estado de funcionamento antes de declarar a atualização, tal como falhou. A predefinição é 600 segundos. Esta duração é iniciado após HealthCheckWaitDuration é atingido. Dentro deste HealthCheckRetryTimeout, ferro serviço poderá executar várias verificações de integridade do Estado de funcionamento da aplicação. O valor predefinido é de 10 minutos e deve ser corretamente personalizado para a sua aplicação. |
| HealthCheckStableDurationSec | A duração (em segundos) para verificar que a aplicação é estável antes de passar para o domínio de atualização seguinte ou concluir a atualização. Esta duração de atividade aguardar é utilizada para impedir alterações não detectadas do Estado de funcionamento direita após ter sido efetuada a verificação de integridade. O valor predefinido é 120 segundos e deve ser corretamente personalizado para a sua aplicação. |
| UpgradeDomainTimeoutSec | Tempo máximo (em segundos) para atualizar um único domínio de atualização. Se for atingido este limite de tempo, a atualização deixa e avança com base na definição para UpgradeFailureAction. O valor predefinido é nunca (infinito) e deve ser corretamente personalizada para a sua aplicação. |
| UpgradeTimeout | Um limite de tempo (em segundos) que aplica-se para a atualização inteira. Se for atingido este limite de tempo, a atualização marcas e UpgradeFailureAction é acionada. O valor predefinido é nunca (infinito) e deve ser corretamente personalizada para a sua aplicação. |
| UpgradeHealthCheckInterval | A frequência que o estado de funcionamento está selecionado. Este parâmetro é especificado na secção de ClusterManager do _cluster_ _manifesto_e não for especificado como parte do cmdlet atualização. O valor predefinido é 60 segundos.  |






<br>
## <a name="service-health-evaluation-during-application-upgrade"></a>Avaliação de estado de funcionamento do serviço durante a atualização de aplicação

<br>
Os critérios de avaliação do Estado de funcionamento são opcionais. Se os critérios de avaliação do Estado de funcionamento não estão especificados quando uma atualização é iniciado, o serviço ferro utiliza as políticas de estado de funcionamento da aplicação especificadas na ApplicationManifest.xml da instância da aplicação.


<br>

| Parâmetro | Descrição |
| --- | --- |
| ConsiderWarningAsError | Valor predefinido é falso. Trata os eventos de estado de funcionamento de aviso para a aplicação de como erros ao avaliar o estado de funcionamento da aplicação durante a atualização. Por predefinição, o serviço ferro avaliar não eventos de estado de funcionamento de aviso para ser falhas (erros), para a atualização possa continuar mesmo se existem eventos de aviso.   |
| MaxPercentUnhealthyDeployedApplications | Predefinição e valor recomendado é 0. Especifique o número máximo de aplicações implementados (consulte a [secção funcionamento](service-fabric-health-introduction.md)) que pode ser danificado antes da aplicação é considerada danificada e falha a atualização. Este parâmetro define o estado de funcionamento de aplicação no nó e ajuda a Deteta problemas durante a atualização. Normalmente, as réplicas da aplicação obtém balanceamento de carga para o outro nó, que permite que a aplicação seja apresentado saudável, permitindo assim que a atualização continuar. Ao especificar um Estado de funcionamento do MaxPercentUnhealthyDeployedApplications estritamente, serviço ferro pode detetar um problema com o pacote de aplicação rapidamente e ajudar a produzir uma falha a mesma rapidez com a atualização. |
| MaxPercentUnhealthyServices | Predefinição e valor recomendado é 0. Especifique o número máximo de serviços na instância da aplicação que pode ser danificada antes da aplicação é considerada danificada e falha a atualização. |
| MaxPercentUnhealthyPartitionsPerService | Predefinição e valor recomendado é 0. Especifique o número máximo de partições num serviço que pode ser danificado antes do serviço é considerado danificado. |
| MaxPercentUnhealthyReplicasPerPartition | Predefinição e valor recomendado é 0. Especifique o número máximo de réplicas na partição que pode ser danificada antes da partições considera danificada. |
| UpgradeReplicaSetCheckTimeout | **Serviço sem estado**– dentro de um único domínio atualização, serviço ferro tentará para se certificar de que estão disponíveis adicionais instâncias do serviço. Se a contagem de instância de destino for mais do que um, espera ferro de serviço para mais do que uma instância esteja disponível, por excesso para um valor de tempo limite máximo. Este limite de tempo especificado, utilizando a propriedade UpgradeReplicaSetCheckTimeout. Se o tempo limite expirar, serviço ferro avança com a atualização, independentemente do número de instâncias do serviço. Se a contagem de instância de destino é uma, ferro de serviço não esperar e imediatamente avança com a atualização. **Serviço com estado**– dentro de um único domínio atualização, serviço ferro tentará para se certificar de que o conjunto de réplicas tem um quórum. Serviço ferro aguarda um quórum esteja disponível, por excesso para um valor de tempo limite máximo (especificado pela propriedade UpgradeReplicaSetCheckTimeout). Se o tempo limite expirar, serviço ferro avança com a atualização, independentemente de quórum. Esta definição está conjunto como nunca (infinito) quando gradual reencaminhar e 900 segundos quando reverter. |
| /Forcerestart | Se atualizar uma configuração ou o pacote de dados sem atualizar o código de serviço, o serviço for reiniciado apenas se a propriedade /forcerestart está definida como verdadeiro. Quando a atualização for concluída, o serviço ferro notifica o serviço que está disponível um pacote de dados ou de novo pacote de configuração. O serviço é responsável por aplicar as alterações. Se for necessário, pode reiniciar o serviço própria. |



<br>
<br>
Os critérios MaxPercentUnhealthyServices, MaxPercentUnhealthyPartitionsPerService e MaxPercentUnhealthyReplicasPerPartition podem ser especificados por tipo de serviço para uma instância da aplicação. Estes parâmetros por serviço a definição permite uma aplicação para conter tipos de serviços diferente com as políticas de avaliação diferente. Por exemplo, um tipo de serviço de gateway sem estado pode ter uma MaxPercentUnhealthyPartitionsPerService que é a diferença entre um tipo de serviço do motor de estado para uma determinada instância da aplicação.

## <a name="next-steps"></a>Próximos passos

[Atualizar a sua aplicação de utilizar o Visual Studio](service-fabric-application-upgrade-tutorial.md) orienta uma atualização de aplicação utilizando o Visual Studio.

[Atualizar a sua aplicação de utilizar o Powershell](service-fabric-application-upgrade-tutorial-powershell.md) orienta uma atualização de aplicação através do PowerShell.

Torne o seu actualizações de aplicação compatíveis ao aprender a utilizar [Dados serialização](service-fabric-application-upgrade-data-serialization.md).

Saiba como utilizar funcionalidades avançadas ao atualizar a aplicação ao referir [Tópicos avançados](service-fabric-application-upgrade-advanced.md).

Corrigir problemas comuns em actualizações de aplicação ao referir os passos em [Actualizações de resolução de problemas de aplicação](service-fabric-application-upgrade-troubleshooting.md).
 
