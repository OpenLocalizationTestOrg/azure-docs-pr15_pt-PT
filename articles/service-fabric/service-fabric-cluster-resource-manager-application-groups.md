<properties
   pageTitle="Gestor de recursos do serviço ferro Cluster - os grupos de aplicações | Microsoft Azure"
   description="Descrição geral das funcionalidades do grupo de aplicações no Gestor de serviços ferro Cluster de recursos"
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

# <a name="introduction-to-application-groups"></a>Introdução aos grupos de aplicações
Gestor de recursos do serviço ferro Cluster normalmente faz a gestão dos recursos de cluster por propagação a carga (representada por métricas) uniformemente ao longo do cluster. Serviço ferro também gere a capacidade de nós cluster e o cluster como um todo através da noção de capacidade. Isto funciona Grã para um lote de diferentes tipos de cargas de trabalho, mas padrões que tornam utilização frequente de instâncias da aplicação do serviço ferro diferente, por vezes, cumprir requisitos adicionais. Alguns requisitos adicionais são normalmente:

- Capacidade de reservar capacidade para serviços de uma instância de aplicação no algumas número de nós
- Capacidade de limitar o número total de nós do que um determinado conjunto de serviços dentro de uma aplicação é permitido executar no
- Que define as capacidades na instância da aplicação para limitar o número ou o consumo de recursos total dos serviços dentro da mesma

Para poder cumprir estes requisitos, desenvolvemos o suporte para a que chamamos grupos de aplicações.

## <a name="managing-application-capacity"></a>Gerir a capacidade de aplicação
Capacidade de aplicação pode ser utilizada para limitar o número de nós expandidos por uma aplicação, bem como a carga métrica total do que instâncias dos aplicações em nós individuais. Podem também ser utilizado para reservar recursos no cluster para a aplicação.

Capacidade de aplicação pode ser definida para as novas aplicações quando são criadas; Também pode ser atualizado para aplicações existentes que foram criadas sem especificar a capacidade de aplicação.

### <a name="limiting-the-maximum-number-of-nodes"></a>Limitar o número máximo de nós
Caso de utilização mais simples para a capacidade de aplicação é quando uma instâncias da aplicação tem de estar limitado a um determinado número de nós máximo. Se não for especificada nenhum capacidade de aplicação, o Gestor de recursos do serviço ferro Cluster irá criar instâncias réplicas de acordo com as regras normais (balanceamento ou desfragmentação), que normalmente significa que os serviços serão distribuídos ao longo de todos os nós disponíveis no cluster, ou se desfragmentação estiver ativada algumas arbitrário mas mais pequeno, o número de nós.

A imagem seguinte mostra o posicionamento de uma instância de aplicação potencial sem o número máximo de nós definidos e, em seguida, mesma aplicação com um número máximo de nós definida. Tenha em atenção que não existe nenhuma garantias efectuadas sobre qual réplicas ou instâncias que serviços irão obter colocados em conjunto.

![Definir o número máximo de nós instância da aplicação][Image1]

No exemplo para a esquerda, a aplicação não tiver capacidade de aplicação definir e que tem três serviços. CRM fez uma decisão lógica para expandir todas as réplicas através de seis nós disponíveis para alcançar o melhor equilíbrio no cluster. No exemplo à direita, vemos a mesma aplicação que está restringido em três nós e onde o serviço ferro CRM tem atingir o melhor equilíbrio para réplicas dos serviços da aplicação.

O parâmetro que controla este comportamento é denominado MaximumNodes. Este parâmetro pode ser definido durante a criação da aplicação ou atualizado para uma instância da aplicação que já foi executado, na qual maiúsculas/minúsculas serviço ferro CRM irão restringir as réplicas de serviços de aplicação para o número máximo definido de nós.

PowerShell

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MaximumNodes 3
Update-ServiceFabricApplication –Name fabric:/AppName –MaximumNodes 5
```

C#

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MaximumNodes = 3;
fc.ApplicationManager.CreateApplicationAsync(ad);

ApplicationUpdateDescription adUpdate = new ApplicationUpdateDescription(new Uri("fabric:/AppName"));
adUpdate.MaximumNodes = 5;
fc.ApplicationManager.UpdateApplicationAsync(adUpdate);

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.TotalApplicationCapacity = 1000;

adUpdate.Metrics.Add(appMetric);
```

## <a name="application-metrics-load-and-capacity"></a>Métricas de aplicação, carregamento e capacidade
Grupos de aplicações também lhe permitem definir métricas associadas a uma instância da aplicação determinado, bem como a capacidade da aplicação exata relativamente a essas métricas. Por exemplo pode definir que como vários serviços de forma que pretende que podem ser criados na

Para cada métrica, existem 2 valores que podem ser definidas para descrever a capacidade para essa instância da aplicação:

-   Total de aplicação capacidade – representa a capacidade total da aplicação de uma determinada métrica. Serviço ferro CRM irá tentar limitar a soma dos carregamentos de métricas de serviços desta aplicação para o valor especificado; Além disso, se já estão a serviços a aplicação consumir carga até este limite, Gestor de recursos do serviço ferro Cluster desativará a criação de qualquer novos serviços ou a partições causar carga total ir ultrapassem este limite.
-   Capacidade de nó máxima – Especifica a carga total máxima para réplicas dos serviços dos aplicações num único nó. Se a carga total no nó abrange esta capacidade, serviço ferro CRM irá tentativa de mover réplicas para outros nós para que a restrição de capacidade é respeitada.

## <a name="reserving-capacity"></a>Capacidade de reserva
Outra utilização comum para grupos de aplicação é para se certificar de que os recursos dentro do cluster são reservados para uma instância da aplicação determinado, mesmo se a instância da aplicação ainda não contiver os serviços dentro da mesma, ou mesmo que não estão consumir os recursos ainda. Vamos olhar que iria funcionamento.  

### <a name="specifying-a-minimum-number-of-nodes-and-resource-reservation"></a>Especificar um número mínimo de nós e reserva de recursos
Reserva de recursos para uma instância da aplicação requer especificando algumas parâmetros adicionais: *MinimumNodes* e *NodeReservationCapacity*

- MinimumNodes - tal como especificando um número máximo de destino de nós do que os serviços dentro de uma aplicação podem ser executados em, pode também especificar o número mínimo de nós no deverá ser possível executar uma aplicação. Esta definição de forma eficaz define o número de nós os recursos devem estar reservados no mínimo, cubra capacidade dentro do cluster como parte da criação de instância da aplicação.
- NodeReservationCapacity - o NodeReservationCapacity pode ser definida para cada métrica dentro da aplicação. Isto define a quantidade de carga métrica reservada para a aplicação em qualquer nó onde são colocadas qualquer uma das réplicas ou instâncias dos serviços dentro da mesma.

Vamos ver um exemplo de reserva de capacidade:

![Instâncias da aplicação que define a capacidade reservada][Image2]

No exemplo para a esquerda, aplicações não tem qualquer capacidade de aplicação definidos. Gestor de recursos do serviço ferro Cluster estão equilibradas instâncias juntamente com os de outros serviços (fora da aplicação) e réplicas de serviços de subordinado a aplicação para garantir que o saldo do cluster.

No exemplo à direita, vamos supor que a aplicação foi criada com uma MinimumNodes definida como 2, MaximumNodes definido para 3 e uma aplicação métrica definida com uma reserva de 20, capacidade max num nó de 50 e uma capacidade de aplicação total de 100, serviço ferro irá reservar capacidade em dois nós para a aplicação de azul e não permitirá outras réplicas do cluster consumir dessa capacidade. Esta capacidade de aplicação reservadas será considerada consumidas e contadas contra a capacidade de cluster restante.

Quando uma aplicação é criada com reserva, o Gestor de recursos Cluster irá reservar capacidade igual a MinimumNodes * NodeReservationCapacity no cluster, mas não será reservar capacidade em nós específicos até as réplicas dos serviços a aplicação são criadas e colocadas. Esta opção permite-para flexibilidade, uma vez que nós são escolhidos para novas réplicas apenas quando são criadas. A capacidade é reservada num nó específico quando é efetuada pelo menos uma réplica no mesmo.

## <a name="obtaining-the-application-load-information"></a>Obter as informações de carga de aplicação
Para cada aplicação que tenha capacidade de aplicação definida pode obter as informações sobre a carga de agregação comunicada por réplicas dos seus serviços. Serviço ferro fornece consultas PowerShell e API gerida para o efeito.

Por exemplo, carga pode ser obtida utilizando o seguinte cmdlet do PowerShell:

``` posh
Get-ServiceFabricApplicationLoad –ApplicationName fabric:/MyApplication1

```

O resultado desta consulta irá conter as informações básicas sobre a capacidade de aplicação que foi especificada para a aplicação, tais como nós de mínimo e máximo nós. Também irá ser informações sobre o número de nós que está atualmente a utilizar a aplicação. Assim sendo, para cada métrica carga haverá informações sobre:
- : Métrica nome da métrica.
-   Capacidade de reserva: Capacidade de Cluster que está reservada no cluster para esta aplicação.
-   Carga da aplicação: Carga Total de réplicas de subordinados esta aplicação.
-   Capacidade de aplicação: Máximo permitido valor de carga de aplicação.

## <a name="removing-application-capacity"></a>Remover a capacidade de aplicação
Assim que os parâmetros de capacidade de aplicação estão configurados para uma aplicação, pode ser removidas utilizar cmdlets do APIs da aplicação de atualização ou PowerShell. Por exemplo:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

Este comando irá remover todos os parâmetros de capacidade de aplicação a partir da aplicação e irá iniciar o Gestor de recursos do serviço ferro Cluster treating esta aplicação como qualquer outra aplicação no cluster que não tem estes parâmetros definidos. O efeito do comando é imediato e Gestor de recursos do Cluster irá eliminar todos os parâmetros de capacidade de aplicação para esta aplicação; especificá-los novamente seria necessário atualizar aplicação APIs a chamada com os parâmetros adequados.

## <a name="restrictions-on-application-capacity"></a>Restrições sobre a capacidade de aplicação
Existem várias restrições parâmetros de capacidade de aplicação que devem ser respeitadas. Em caso de erros de validação, a criação ou atualização da aplicação será rejeitada com um erro.
Todos os parâmetros de número inteiro têm de ser números não negativo.
Além disso, para os parâmetros individuais restrições são os seguintes:

-   MinimumNodes nunca tem de ser maior do que MaximumNodes.
-   Se as capacidades de uma métrica de carregamento são definidas, em seguida, eles terá de seguir estas regras:
  - Capacidade de reserva nós não tem de ser maior capacidade de nó máxima. Por exemplo, não é possível limitar a capacidade de métrica "CPU" no nó para 2 unidades e tente reservar 3 unidades em cada nó.
  - Se não for especificado MaximumNodes, em seguida, o produto de MaximumNodes e a capacidade de nó máximo não devem maior capacidade Total de aplicação. Por exemplo, se definir a capacidade de nó máxima de carga métrica "CPU" para 8 e defina o número máximo de nós para 10, em seguida, capacidade Total de aplicação tem de ser superior a 80 para esta métrica de carregamento.

As restrições são impostas durante a criação de aplicação (no lado do cliente) e durante a atualização de aplicação (no lado do servidor). Durante a criação, este é um exemplo de uma violação de limpar dos requisitos desde MaximumNodes < MinimumNodes e o comando irão falhar no cliente do antes do pedido mesmo é enviado para cluster de hardware de serviço:

``` posh
New-ServiceFabricApplication –Name fabric:/MyApplication1 –MinimumNodes 6 –MaximumNodes 2
```

Um exemplo de actualização inválida é da seguinte forma. Se o podemos tirar uma aplicação existente e atualizar o número máximo de nós algum valor, passará a atualização:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 6 –MaximumNodes 2
```

Após esta ação, iremos pode tentar atualizar nós mínimos:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 6 –MinimumNodes 6
```

O cliente não tem o contexto suficiente sobre a aplicação desta-permitirá que a atualização passar para o cluster de serviço ferro. No entanto, no cluster, serviço ferro irá validar o novo parâmetro juntamente com os parâmetros existentes e irá falhar a operação de atualização porque os nós de mínimo foe valor é maior que o valor de número máximo de nós. Neste caso, parâmetros de capacidade de aplicação irão permanecer inalterados.

Estas restrições são colocadas num local por ordem para Cluster de Gestor de recursos possam fornecer o melhor local para réplicas do serviços dos aplicações.

## <a name="how-not-to-use-application-capacity"></a>Como não está a utilizar a capacidade de aplicação

-   Não utilize a capacidade de aplicação para restringir a aplicação a um subconjunto específico de nós: Embora ferro serviço irá garantir que o número máximo de nós é respeitado para cada aplicação que tem a capacidade de aplicação especificada, os utilizadores não podem decidir o que nós será possível criar instâncias no. Isto pode ser realizado com restrições posicionamento para serviços.
-   Não utilize a capacidade de aplicação para se certificar de que dois serviços a partir da aplicação a mesma serão sempre colocados juntamente com os outros. Isto pode ser realizado pelos utilizando afinidade relação entre os serviços, e afinidade pode estar limitada apenas para os serviços que realmente devem ser colocados em conjunto.

## <a name="next-steps"></a>Próximos passos
- Para obter mais informações sobre as outras opções disponíveis para a configuração dos serviços de dar saída o tópico sobre as outras configurações do Gestor de recursos de Cluster disponíveis, [Saiba mais sobre como configurar os serviços](service-fabric-cluster-resource-manager-configure-services.md)
- Para saber mais sobre como o Gestor de recursos Cluster gere e equilibra carga no cluster, consulte o artigo sobre [balanceamento de carga](service-fabric-cluster-resource-manager-balancing.md)
- Iniciar a partir do início e [obter uma introdução para o serviço ferro Cluster Gestor de recursos](service-fabric-cluster-resource-manager-introduction.md)
- Para mais informações sobre como métricas funcionam geralmente, continue a ler [Serviço ferro carga métricas](service-fabric-cluster-resource-manager-metrics.md)
- O Gestor de recursos de Cluster tem muitas opções para descrever o cluster. Para saber mais sobre os mesmos dar saída neste artigo [que descreve um cluster de ferro de serviço](service-fabric-cluster-resource-manager-cluster-description.md)


[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png
