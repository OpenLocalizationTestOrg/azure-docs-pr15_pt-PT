<properties
   pageTitle="Resolução de problemas de actualizações de aplicação | Microsoft Azure"
   description="Este artigo abrange alguns problemas comuns em torno atualizar uma aplicação de serviço ferro e como para os resolver."
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

# <a name="troubleshoot-application-upgrades"></a>Resolver problemas de actualizações de aplicação

Este artigo aborda alguns dos problemas comuns em torno atualizar uma aplicação do Azure Service ferro e como para os resolver.

## <a name="troubleshoot-a-failed-application-upgrade"></a>Resolver problemas de uma atualização de falha na aplicação

Quando uma atualização falha, o resultado do comando **Get-ServiceFabricApplicationUpgrade** contém informações adicionais para depuração a falha.  A lista seguinte especifica como as informações adicionais podem ser utilizadas:

1. Identifique o tipo de falha.
2. Identifique o motivo da falha.
3. Isole componentes de falha de uma ou mais para o inquérito ainda mais.

Estas informações ficam disponíveis quando serviço ferro Deteta a falha, independentemente de se está a **FailureAction** recuperar ou suspender a atualização.

### <a name="identify-the-failure-type"></a>Identificar o tipo de falha

Em saída de **Obter ServiceFabricApplicationUpgrade**, **FailureTimestampUtc** identifica o carimbo de hora (no UTC) no qual uma falha de atualização foi detectada pelo serviço ferro e **FailureAction** foi acionou. **FailureReason** identifica um dos três causas de alto nível potenciais da falha:

1. UpgradeDomainTimeout - indica que um determinado domínio atualização demorou demasiado tempo a concluir e **UpgradeDomainTimeout** expirou.
2. OverallUpgradeTimeout - indica que a atualização geral demorou demasiado tempo a concluir e **UpgradeTimeout** expirou.
3. HealthCheck - indica que depois de atualizar um domínio de atualização, a aplicação permaneceu danificada, de acordo com as políticas de estado de funcionamento especificado e **HealthCheckRetryTimeout** expirou.

Estas entradas apenas aparecem na saída quando a atualização falha e começa a reverter. Mais informações são apresentadas dependendo do tipo de falha.

### <a name="investigate-upgrade-timeouts"></a>Investigar tempos limite de atualização

Atualize o tempo limite falhas mais frequentemente problemas são causadas pela problemas de disponibilidade de serviço. O resultado seguinte neste parágrafo é típico de actualizações de onde réplicas do serviço ou instâncias falharem iniciar a nova versão do código. O campo **UpgradeDomainProgressAtFailure** para capturar um instantâneo de qualquer trabalho atualização pendente no momento da falha.

~~~
PS D:\temp> Get-ServiceFabricApplicationUpgrade fabric:/DemoApp

ApplicationName                : fabric:/DemoApp
ApplicationTypeName            : DemoAppType
TargetApplicationTypeVersion   : v2
ApplicationParameters          : {}
StartTimestampUtc              : 4/14/2015 9:26:38 PM
FailureTimestampUtc            : 4/14/2015 9:27:05 PM
FailureReason                  : UpgradeDomainTimeout
UpgradeDomainProgressAtFailure : MYUD1

                                 NodeName            : Node4
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 744c8d9f-1d26-417e-a60e-cd48f5c098f0

                                 NodeName            : Node1
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 4b43f4d8-b26b-424e-9307-7a7a62e79750
UpgradeState                   : RollingBackCompleted
UpgradeDuration                : 00:00:46
CurrentUpgradeDomainDuration   : 00:00:00
NextUpgradeDomain              :
UpgradeDomainsStatus           : { "MYUD1" = "Completed";
                                 "MYUD2" = "Completed";
                                 "MYUD3" = "Completed" }
UpgradeKind                    : Rolling
RollingUpgradeMode             : UnmonitoredAuto
ForceRestart                   : False
UpgradeReplicaSetCheckTimeout  : 00:00:00
~~~

Neste exemplo, a atualização falhou no domínio atualização *MYUD1* e duas partições (*744c8d9f-1d26-417e-a60e-cd48f5c098f0* e *4b43f4d8-b26b-424e-9307-7a7a62e79750*) foram bloqueadas. As partições foram bloqueadas porque o tempo de execução não foi possível colocar réplicas principais (*WaitForPrimaryPlacement*) em nós de destino *Nó1* e *Nó4*.

O comando **Get-ServiceFabricNode** pode ser utilizado para confirmar que estes dois nós estejam no domínio atualização *MYUD1*. O *UpgradePhase* diz *PostUpgradeSafetyCheck*, que significa que ocorrem depois de tem terminado a todos os nós de no domínio de atualização de actualização estas verificações de segurança. Apontam todas estas informações para um potencial problema com a nova versão do código da aplicação. Os problemas mais comuns são serviço erros aberto ou promoção para caminhos de código principal.

Um *UpgradePhase* de *PreUpgradeSafetyCheck* significa que ocorreram problemas preparar o domínio atualização antes de foi efetuada. Neste caso, os problemas mais comuns são erros do serviço na fechar ou despromoção a partir de caminhos de código principal.

O atual **UpgradeState** é *RollingBackCompleted*, por isso a atualização original têm de ter sido efectuada com uma reversão **FailureAction**, que automaticamente revertida a atualização em caso de falha. Se a atualização original foi efetuada com um manual **FailureAction**, em seguida, a atualização seria em vez disso ser num estado suspenso para permitir que direto depuração da aplicação.

### <a name="investigate-health-check-failures"></a>Investigar falhas de verificação do Estado de funcionamento

Falhas de verificação do Estado de funcionamento podem ser acionou por vários problemas que poderão ocorrer após a conclusão de todos os nós de um domínio atualização atualizar e prisma todas as verificações de segurança. O resultado seguinte neste parágrafo é típico de uma falha de atualização devido verificações de integridade de falha. O campo **UnhealthyEvaluations** para capturar um instantâneo dos controlos de estado de funcionamento que falhou no momento da atualização de acordo com a [política de estado de funcionamento](service-fabric-health-introduction.md)de especificada.

~~~
PS D:\temp> Get-ServiceFabricApplicationUpgrade fabric:/DemoApp

ApplicationName                         : fabric:/DemoApp
ApplicationTypeName                     : DemoAppType
TargetApplicationTypeVersion            : v4
ApplicationParameters                   : {}
StartTimestampUtc                       : 4/24/2015 2:42:31 AM
UpgradeState                            : RollingForwardPending
UpgradeDuration                         : 00:00:27
CurrentUpgradeDomainDuration            : 00:00:27
NextUpgradeDomain                       : MYUD2
UpgradeDomainsStatus                    : { "MYUD1" = "Completed";
                                          "MYUD2" = "Pending";
                                          "MYUD3" = "Pending" }
UnhealthyEvaluations                    :
                                          Unhealthy services: 50% (2/4), ServiceType='PersistedServiceType', MaxPercentUnhealthyServices=0%.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc3', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='3a9911f6-a2e5-452d-89a8-09271e7e49a8', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc2', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='744c8d9f-1d26-417e-a60e-cd48f5c098f0', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

UpgradeKind                             : Rolling
RollingUpgradeMode                      : Monitored
FailureAction                           : Manual
ForceRestart                            : False
UpgradeReplicaSetCheckTimeout           : 49710.06:28:15
HealthCheckWaitDuration                 : 00:00:00
HealthCheckStableDuration               : 00:00:10
HealthCheckRetryTimeout                 : 00:00:10
UpgradeDomainTimeout                    : 10675199.02:48:05.4775807
UpgradeTimeout                          : 10675199.02:48:05.4775807
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
~~~

Em primeiro lugar investigar falhas de verificação do Estado de funcionamento requer a compreender o modelo de estado de funcionamento do serviço ferro. Mas, mesmo sem essa informações aprofundadas sobre, podemos ver que dois serviços estão danificados: *ferro: / DemoApp/Svc3* e *ferro: / DemoApp/Svc2*, juntamente com os relatórios de estado de funcionamento de erro ("InjectedFault" neste caso). Neste exemplo, os serviços de dois a quatro fora do são-danificados, ou seja, abaixo de destino predefinido de 0% danificado (*MaxPercentUnhealthyServices*).

A atualização foi suspensa relativamente a uma falha ao especificar um **FailureAction** do manual ao iniciar a atualização. Este modo permite-nos investigar o sistema direto no estado de falhado antes de efetuar quaisquer outras ação.

### <a name="recover-from-a-suspended-upgrade"></a>Recuperar a partir de uma atualização suspensa

Com uma reversão **FailureAction**, não existe nenhuma recuperação necessária desde a actualização automaticamente reverte relativamente a uma falha. Com um manual **FailureAction**, existem várias opções de recuperação:

1. Acionar manualmente uma reversão
2. Avance manualmente o resto da atualização
3. Retomar a atualização monitorizada

O comando de **Início ServiceFabricApplicationRollback** pode ser utilizado em qualquer altura para iniciar a reverter a aplicação. Assim que o comando devolver com êxito, o pedido de reposição foi registado no sistema e inicia brevemente posteriormente.

O comando de **Currículo ServiceFabricApplicationUpgrade** pode ser utilizado para avançar manualmente, através do resto da atualização de um domínio para a atualização ao mesmo tempo. Neste modo, controlos de segurança só são executados pelo sistema. Verificações de integridade mais não são executadas. Este comando só pode ser utilizado quando o *UpgradeState* mostra *RollingForwardPending*, que significa que o domínio atual para a atualização já terminou atualizar mas aquele seguinte não foi iniciado (pendente).

O comando de **Atualização ServiceFabricApplicationUpgrade** pode ser utilizado para retomar a atualização monitorizada com ambos os segurança e estado de funcionamento do verifica a ser executado.

~~~
PS D:\temp> Update-ServiceFabricApplicationUpgrade fabric:/DemoApp -UpgradeMode Monitored

UpgradeMode                             : Monitored
ForceRestart                            :
UpgradeReplicaSetCheckTimeout           :
FailureAction                           :
HealthCheckWaitDuration                 :
HealthCheckStableDuration               :
HealthCheckRetryTimeout                 :
UpgradeTimeout                          :
UpgradeDomainTimeout                    :
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :

PS D:\temp>
~~~

Continua a atualização a partir do domínio atualização onde foi suspensa última e a utilização da mesma atualizar parâmetros e estado de funcionamento políticas, conforme antes. Se for necessário, qualquer um dos parâmetros de atualização e políticas de estado de funcionamento apresentadas no resultado anterior podem ser alteradas no mesmo comando quando a atualização currículos. Neste exemplo, a atualização foi retomada no modo de monitorizadas, com os parâmetros e as políticas de estado de funcionamento inalteradas.

## <a name="further-troubleshooting"></a>Ainda mais a resolução de problemas

### <a name="service-fabric-is-not-following-the-specified-health-policies"></a>Serviço ferro não está a seguir as políticas de estado de funcionamento especificado

Causa possível 1:

Serviço ferro converte todas as percentagens em números reais de entidades de (por exemplo, réplicas, partições e serviços) de avaliação do Estado de funcionamento e arredonda sempre por excesso para entidades todos. Por exemplo, se o máximo _MaxPercentUnhealthyReplicasPerPartition_ é 21% e existem cinco réplicas, em seguida, serviço ferro permite até duas réplicas danificadas (esse is,'Math.Ceiling (5\*0.21)). Assim sendo, políticas de estado de funcionamento devem ser definidas em conformidade.

Causa possível 2:

Políticas de estado de funcionamento estão especificadas em termos de percentagens dos serviços de total e instâncias do serviço específicos. Por exemplo, antes de uma atualização, se tiver uma aplicação quatro instâncias A, B, C e D, do serviço onde o serviço D está danificado, mas com pouco impacto da aplicação. Pretendemos ignorar o serviço danificado conhecido D durante a atualização e configurar o parâmetro *MaxPercentUnhealthyServices* para ser 25%, partindo do princípio que apenas A, B e C tem de ser saudável.

No entanto, durante a atualização, D poderá ficar Saudável enquanto C fica danificado. A atualização seria ainda teve êxito porque apenas 25% dos serviços estão danificado. No entanto, poderá resultar em erros imprevistos devido a C a ser inesperadamente danificado em vez de D. Esta situação, D considerado um tipo de serviços diferente a partir de A, B e C. Uma vez que as políticas de estado de funcionamento forem especificadas por tipo de serviço, limiares de percentagem danificado diferente podem ser aplicados a diferentes serviços. 

### <a name="i-did-not-specify-a-health-policy-for-application-upgrade-but-the-upgrade-still-fails-for-some-time-outs-that-i-never-specified"></a>Posso não especificado uma política de estado de funcionamento para a atualização da aplicação, mas ainda falha a atualização para alguns limites de tempo que nunca especifiquei

Quando não são fornecidas políticas de estado de funcionamento ao pedido de atualização, estes são tiradas do *ApplicationManifest.xml* da versão de aplicação atual. Por exemplo, se estiver a atualizar aplicação X da versão 1.0 para a versão 2.0, políticas de estado de funcionamento da aplicação especificada para na versão 1.0 são utilizadas. Se uma política de estado de funcionamento diferentes deve ser utilizada para a atualização, em seguida, a política de tem de ser especificado como parte da chamada API a atualização da aplicação. As políticas que foram especificadas como parte da chamada API apenas serem aplicadas durante a atualização. Assim que a atualização estiver concluída, são utilizadas as políticas que foram especificadas no *ApplicationManifest.xml* .

### <a name="incorrect-time-outs-are-specified"></a>Tempos limite incorreto está especificado

Pode pode ter questionado sobre o que acontece quando tempos limite está definida inconsistente. Por exemplo, poderá ter um *UpgradeTimeout* for menor que o *UpgradeDomainTimeout*. A resposta é que é devolvido um erro. Se o *UpgradeDomainTimeout* for menor que a soma de *HealthCheckWaitDuration* e *HealthCheckRetryTimeout*ou se *UpgradeDomainTimeout* for menor que a soma de *HealthCheckWaitDuration* e *HealthCheckStableDuration*, são devolvidos erros.

### <a name="my-upgrades-are-taking-too-long"></a>Meu atualiza os estão a demorar demasiado tempo

A hora de uma atualização concluir depende dos controlos de estado de funcionamento e limites de tempo especificados. Verificações de integridade e tempos limite depende quanto tempo demora para copiar, implementar e regularização a aplicação. A ser demasiado agressivas com tempos limite poderá significam que falha actualizações de mais, para que recomendamos a começar em espera tempos limite mais longa.

Eis um rápida atualizador no modo como os limites de tempo interagem com os tempos de atualização:

Actualizações para um domínio para a atualização não é possível concluir mais depressa que *HealthCheckWaitDuration* + *HealthCheckStableDuration*.

Falha na atualização não pode ocorrer mais depressa que *HealthCheckWaitDuration* + *HealthCheckRetryTimeout*.

A hora de atualização de um domínio para a atualização é limitada pelo *UpgradeDomainTimeout*.  Se *HealthCheckRetryTimeout* e *HealthCheckStableDuration* ficam diferente de zero e o estado de funcionamento da aplicação mantém a mudança de e para trás, em seguida, a atualização eventualmente exceda o tempo limite *UpgradeDomainTimeout*. *UpgradeDomainTimeout* inicia contando para baixo, assim que começa a atualização para o domínio de atualização atual.

## <a name="next-steps"></a>Próximos passos

[Atualizar a sua aplicação de utilizar o Visual Studio](service-fabric-application-upgrade-tutorial.md) orienta uma atualização de aplicação utilizando o Visual Studio.

[Atualizar a sua aplicação de utilizar o Powershell](service-fabric-application-upgrade-tutorial-powershell.md) orienta uma atualização de aplicação através do PowerShell.

Controlar como a aplicação actualiza utilizando [Parâmetros de atualização](service-fabric-application-upgrade-parameters.md).

Torne o seu actualizações de aplicação compatíveis ao aprender a utilizar [Dados serialização](service-fabric-application-upgrade-data-serialization.md).

Saiba como utilizar funcionalidades avançadas ao atualizar a aplicação ao referir [Tópicos avançados](service-fabric-application-upgrade-advanced.md).

Corrigir problemas comuns em actualizações de aplicação ao referir os passos em [Actualizações de resolução de problemas de aplicação](service-fabric-application-upgrade-troubleshooting.md).
 
