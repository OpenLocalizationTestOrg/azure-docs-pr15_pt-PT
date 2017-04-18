<properties
   pageTitle="Desfragmentação de métricas no serviço Azure ferro | Microsoft Azure"
   description="Uma descrição geral da utilização desfragmentação ou embalagem como uma estratégia de métricas no ferro de serviço"
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

# <a name="defragmentation-of-metrics-and-load-in-service-fabric"></a>Desfragmentação de métricas e carregar no ferro de serviço
O Gestor de recursos do serviço ferro Cluster principalmente diz respeito balanceamento em termos distribuir a carga – certificando-se de que todos os nós no cluster quatros são utilizados. Isto é, normalmente, o esquema mais seguro e smartest em termos de sobrevivência falhas de uma vez que o torna-se de que não demora qualquer falha determinada saída a algumas percentagem grande de uma determinado carga de trabalho. O Gestor de recursos do serviço ferro Cluster suporta uma estratégia de diferentes, assim, o que é desfragmentação. Desfragmentação normalmente significa que em vez de tentar distribuir a utilização de uma métrica ao longo do cluster, podemos deverá realmente tentar pretende consolidá-los. Este é um inversão fortunate da nossa estratégia normal – em vez de otimizar o cluster com base em minimizar o desvio-padrão média de carga métrica de uma determinada métrica, vamos começar a otimizar para aumentos desvio. Mas que motivos pode ter esta estratégia?

Bem, se tiver distribuir a carga de uniformemente entre os nós no cluster, em seguida, pode ter consumido o alguns dos recursos que os nós tem para oferecer. Normalmente isto não é um problema, mas, por vezes, algumas das cargas de trabalho criar serviços que sejam excepcionalmente grandes e consumam a maioria de um nó – apresentamos 75% e 95% de recursos de um nó seria conclui dedicada a uma instância do serviço única ou réplica. Esta não é um problema, irá detectar o Gestor de recursos Cluster na hora de criação de serviço que necessita para reorganizar cluster para arranjar espaço para este grande carga de trabalho e definir sobre facilitando a ocorrer, mas entretanto essa carga de trabalho tem de esperar até ser agendado no cluster.

Dado que o agendamento da novas das cargas de trabalho é normalmente, pelo menos, um pequeno latência sensíveis a maiúsculas e, se podemos não fazer nada forma diferente podemos, por vezes, para a direita por esses SLA bBaixa se existir um lote de serviços e estado para deslocar-se, especialmente se das cargas de trabalho no cluster são normalmente grandes (e, por conseguinte, a demorar mais tempo para deslocar-se no cluster). Na verdade, quando podemos medido horas de criação numa simulações com base em dados real cluster, podemos viu que se foram grandes o suficiente para serviços e cluster bastante foi utilizado que a criação desses serviços grandes seria ser mais lento para baixo e que recomendamos poderia melhorar isto ao introduzir a política de métricas de desfragmentação.

Tal como ficheiro criação ou do access poderá obter mais lento para baixo que se no disco rígido de um computador foi fragmentado e poderia acelerado através da desfragmentação a unidade de modo a que havia blocos contíguos maiores disponível, pode configurar métricas desfragmentação para que o Gestor de recursos de Cluster para tentar importante comprimir a carga dos serviços em nós menos para que seja (quase) sempre sala para serviços mesmo grandes , permitindo-lhes a ser criada rapidamente. A maioria das pessoas não é necessário esta ação, uma vez que normalmente devem ser pequenas serviços e, consequentemente, não é difícil localizar a sala das mesmas, mas se tiver serviços grandes e precisar deles criados rapidamente (e estiver disposto a aceitar as outras responsabilidades como impactfulness um aumento de falhas e alguns recursos que está a ser deixada superfície enquanto aguardam das cargas de trabalho ser agendado), em seguida, a estratégia desfragmentação é para si.

O diagrama abaixo dá uma representação visual de dois clusters diferentes, um que é necessária e outro que não está. No caso de desequilibradas, considere os movimentos do qual seriam necessários para colocar um dos objetos de serviço maior, se fosse uma nova para ser criado, comparado com cluster desfragmentado, onde, poderia ser imediatamente colocado em nós 4 ou 5.

![Comparar o equilíbrio e necessária Clusters][Image1]

## <a name="defragmentation-pros-and-cons"></a>Vantagens e desvantagens desfragmentação
Por isso, quais são as variações de conceptuais? Recomendamos que medidas aprofundada de seu das cargas de trabalho antes de ativar o registo métricas desfragmentação. Eis uma tabela rápida das coisas a ter em conta sobre:

| Desfragmentação profissionais  | Desvantagens desfragmentação |
|----------------------|----------------------|
|Permite a criação mais rápida de serviços de grandes dimensões | Concentrados carregar para menos nós, aumentando contenção
|Permite baixar movimento de dados durante a criação    | Falhas podem afetar mais serviços e causar vasilha mais
|Permite rich descrição requisitos e valorização de espaço | Configuração de gestão de recursos global mais complexa

Pode misturar normais e desfragmentadas métricas no mesmo cluster e o Gestor de recursos irá fazer do melhor para se certificar de que obter um esquema que consolida quanto métricas desfragmentação como pode enquanto a está a tentar expandir os restantes. Os resultados exatos que obterá irão variam consoante o número de balanceamento de métricas em comparação comparadas o número de métricas de desfragmentação e os respetivos espessuras, cargas atuais, etc.

## <a name="configuring-defragmentation-metrics"></a>Configurar métricas desfragmentação
Configurar métricas desfragmentação é uma decisão global no cluster e métricas individuais podem ser seleccionadas para desfragmentação:

ClusterManifest.xml:

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Disk" Value="true" />
    <Parameter Name="CPU" Value="false" />
</Section>
```

## <a name="next-steps"></a>Próximos passos
- O Gestor de recursos de Cluster tem muitas opções para descrever o cluster. Para saber mais sobre os mesmos dar saída neste artigo [que descreve um cluster de ferro de serviço](service-fabric-cluster-resource-manager-cluster-description.md)
- Métricas são a forma como o Gestor de recursos do serviço ferro Cluster gere consumo e a capacidade no cluster. Para saber mais sobre-los e como configurá-los consulte [Este artigo](service-fabric-cluster-resource-manager-metrics.md)

[Image1]:./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png
