<properties
   pageTitle="Gestor de recursos do serviço ferro Cluster - posicionamento políticas | Microsoft Azure"
   description="Descrição geral das políticas de posicionamento adicionais e regras para serviços de ferro de serviço"
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/19/2016"
   ms.author="masnider"/>

# <a name="placement-policies-for-service-fabric-services"></a>Políticas de posicionamento para serviços de ferro de serviço
Existem muitas regras adicionais diferentes que pode terminar o cuidar sobre se o seu cluster de serviço ferro é expandido através de uma distância geográficos, diga vários centros de dados ou regiões Azure, ou se o seu ambiente abrange várias áreas do controlo geopolítica (ou alguns casos onde tem limites legais ou política que lhe interessam ou a distância entre envolvidos ter impacto de desempenho real/latência). A maior parte destas pôde ser configurada através de propriedades de nó e restrições de colocação, mas algumas são mais complicadas. Para fazer coisas mais simples fornecemos estes commmands adicionais. Tal como faria com outros constrangimentos posicionamento, posicionamento políticas podem ser configuradas numa base instância por denominada serviço.

## <a name="specifying-invalid-domains"></a>Especificar domínios inválidos
A política de posicionamento InvalidDomain permite-lhe especificar que um determinado domínio falhas é inválido para este carga de trabalho. Esta política assegura que um determinado serviço nunca é executada numa área específica, por exemplo por motivos de política de geopolítica ou empresarial. Vários domínios inválidos podem ser especificados através das políticas de separada.

![Exemplo de domínio inválido][Image1]

Código:

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
invalidDomain.DomainName = "fd:/DCEast"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/DCEast”)
```
## <a name="specifying-required-domains"></a>Especificar domínios necessários
A política de colocação de domínio necessários requer que todas as réplicas com estado ou instâncias do Estado de serviço para o serviço de estar presentes no domínio especificado. Vários domínios necessários podem ser especificados através das políticas de separada.

![Exemplo de domínio necessários][Image2]

Código:

```csharp
ServicePlacementRequiredDomainPolicyDescription requiredDomain = new ServicePlacementRequiredDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DC01/RK03/BL2";
serviceDescription.PlacementPolicies.Add(requiredDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomain,fd:/DC01/RK03/BL2")
```

## <a name="specifying-a-preferred-domain-for-the-primary-replicas"></a>Especificar um domínio preferido para as réplicas principais
O domínio principal preferido é um controlo interessante, uma vez que permite a seleção do domínio falhas no qual a página principal deve ser colocada se é possível fazê-lo. Se tudo o que está em bom estado a página principal irá conclui neste domínio. Deve o domínio ou falhas de réplica principal ou encerrar por algum motivo que será migrada a página principal para outra localização. Se esta localização não o domínio preferido, em seguida, sempre que possível o Gestor de recursos Cluster irá separá-lo para o domínio preferido. Naturalmente esta definição apenas sentido para os serviços com estado. Esta política é muito útil em clusters que são expandidos entre regiões Azure ou os centros de dados de várias. Nas seguintes situações que está a utilizar todas as localizações para redundância, mas se preferir que as réplicas primárias ser colocadas numa determinada localização para fornecer latência inferior para operações de ir para a página principal (escreve e também por predefinição lê todos os é fornecido pela página principal).

![Domínios principais e activação pós-falha preferidos][Image3]

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUS/";
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUS")
```

## <a name="requiring-replicas-to-be-distributed-among-all-domains-and-disallowing-packing"></a>Exigir réplicas para ser distribuído entre todos os domínios e que não permite embalagem
Outra política que pode especificar é exigir réplicas sempre sejam distribuídas entre os domínios de falhas disponíveis. Isto irá acontecer por predefinição na maioria dos casos onde o cluster está em bom estado, no entanto existem degenerate casos onde réplicas para uma determinada partição poderá temporariamente terminar compactadas num única falhas ou domínio de atualização. Por exemplo, vamos supor que apesar cluster tem 9 nós na 3 domínios de falhas (0, 1 e 2) e o seu serviço tem 3 réplicas, correu os nós que foram a ser utilizados para essas réplicas no falhas domínios 1 e 2 para baixo e devido a problemas de capacidade nenhum dos outros nós desses domínios foram válida. Se fosse ferro de serviço construir substituições para essas réplicas, o Gestor de recursos Cluster teria colocá-los no domínio de falhas 0, mas que cria uma situação onde está a ser suspenso a restrição de falhas domínio. Também aumenta a hipótese de pode perder o conjunto de réplicas inteira (se foram DF 0 ser permananently perdido). (Para mais informações sobre restrições e restrição prioridades em geral, consulte o artigo [neste tópico](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities) )

Se nunca visualizou um aviso de estado de funcionamento como "o Balanceador de carga detetou uma violação de restrição para este réplica: ferro: /<some service name> partição secundária <some partition ID> está a violar a restrição: FaultDomain" tenha premir esta condição ou algo parecido com-lo. Normalmente, estas situações são breves (os nós não manter-se para baixo longa, ou se comparativamente e precisamos de construir substituições aí são outros nós nos domínios falhas correto que são válidos), mas existem algumas das cargas de trabalho que preferir seriam comércio disponibilidade para o risco de perder todas as suas réplicas. Vamos pode fazê-lo ao especificar a política de "RequireDomainDistribution", que irá garantir que não existem duas réplicas da mesma partição nunca são permitidas no mesmo domínio falhas ou atualizar.

Algumas das cargas de trabalho preferem o número de destino de réplicas (cópias do Estado de) em todas as horas (apostas contra falhas de domínio total e saber que normalmente pode recuperar estado local), Considerando que outras pessoas seriam tomar preferir o tempo de inatividade anteriores às preocupações regularidade e dataloss o risco. Uma vez que executar a maioria dos produção das cargas de trabalho com mais de 3 réplicas, a predefinição é para não necessitam de distribuição de domínio e deixar equilíbrio e activação pós-falha processar normalmente casos, mesmo se o que significa que temporariamente um domínio tem várias réplicas embaladas na mesma.

Código:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Agora, seria possível utilizar estas configurações para serviços num cluster de que não foi expandido geograficamente? Tem a certeza poderia! Mas não existem não é uma excelente razão demasiado – devem ser evitadas especialmente as configurações de domínio necessários, inválidos e preferido, a menos que realmente estiver a executar um cluster geograficamente expandido - -não fazer qualquer sentido para tentar forçar uma determinada carga de trabalho para executar um bastidor única ou para preferir algumas segmento seu cluster local ao longo do outro, a menos que existam diferentes tipos de hardware ou carga de trabalho segmentação o problema? , e essas casos, poderá ser resolvidos através de restrições posicionamento normal.

## <a name="next-steps"></a>Próximos passos
- Para obter mais informações sobre as outras opções disponíveis para a configuração dos serviços de dar saída o tópico sobre as outras configurações do Gestor de recursos de Cluster disponíveis, [Saiba mais sobre como configurar os serviços](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png
