<properties
   pageTitle="Gestor de recursos do serviço ferro Cluster - afinidade | Microsoft Azure"
   description="Descrição geral de configurar afinidade para serviços de ferro de serviço"
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

# <a name="configuring-and-using-service-affinity-in-service-fabric"></a>Configurar e utilizar afinidade de serviço no ferro de serviço

Afinidade é um controlo que é fornecido principalmente para o ajudar a facilitar a transição de aplicações monolíticos maiores para o mundo na nuvem e microservices. Desta forma que sugerem-pode também ser utilizado em certos casos como uma optimização legítima para melhorar o desempenho dos serviços, apesar de esta pode ter efeitos de lado.

Digamos que estiver a colocar uma aplicação maior, ou uma que não foi concebido apenas com microservices Lembre-se ao serviço ferro. Esta transição é realmente comuns e podemos teve vários clientes (internos e externos) esta situação. Comece pela aplicação completa para o ambiente, introdução-empacotados e a execução de elevação. Em seguida, inicie separá-la para baixo para diferentes serviços mais pequenos do que todas as falar entre si.

Em seguida, é um "Oops...". "Oops" normalmente enquadra uma destas categorias:

1. Algumas componente X na aplicação monolítico tido uma dependência não documentada no componente Y e podemos ativou agora aqueles para serviços separados. Uma vez que estes estão agora em execução em nós diferentes no cluster, as mesmas são quebradas.
2.  Estes pontos comunicam através de (local pipes nomeado | memória partilhada | ficheiros no disco), mas é realmente necessário conseguir atualizar-verificação independente para acelerar um pouco. Posso irá remover a dependência de disco rígida mais tarde.
3.  Tudo o que está bem formatado, mas se revele que estes dois componentes são realmente muito chatty/desempenho confidencial. Depois de terem os movidos para serviços separados global desempenho da aplicação tanked ou latência aumentada. Como resultado, a aplicação geral é não expectativas.

Nestes casos utilizamos não quiser perder o nosso trabalho refactorização e não quiser voltar para o monolito, mas precisamos algumas sensação de localidade. Isto vai permanecer até podemos pode reestruturar as os componentes para trabalhar naturalmente como serviços ou até o podemos resolver as expectativas do desempenho outra forma, se possível.

O que fazer? Bem, tentar ativar o registo afinidade.

## <a name="how-to-configure-affinity"></a>Como configurar afinidade
Para configurar o afinidade, pode definir uma relação afinidade entre dois de serviços diferentes. Poderá pensar afinidade como "apontando" um serviço na outra e dizer "este serviço só pode executar onde serviço está em execução." Por vezes, podemos referem-se para afinidade como uma relação principal/subordinado (onde que aponte para a criança principal). Afinidade assegura que são colocadas réplicas ou ocorrências de um serviço em nós do mesmo como réplicas ou instâncias do outro.

``` csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

## <a name="different-affinity-options"></a>Opções de afinidade diferente
Afinidade for representada por um dos vários esquemas de correlação e possui dois modos diferentes. O modo mais comuns de afinidade é a que chamamos NonAlignedAffinity. NonAlignedAffinity réplicas ou instâncias de diferentes serviços são colocadas em nós do mesmo. O modo de outros é AlignedAffinity. Afinidade alinhada é útil apenas com os serviços com estado. Configurar os serviços com estado duas para ter alinhados afinidade assegura que os primários desses serviços são colocados em nós do mesmo como entre si. Também faz com que cada par de secundários desses serviços seja colocado em nós do mesmo. Também é possível (apesar de menos frequente) configurar NonAlignedAffinity para serviços com estado. Para NonAlignedAffinity as réplicas diferentes dos serviços com estado duas seriam ser co-instalados em nós do mesmo, mas não tentativa seria para alinhar o respetivo primários da ou secundários.

![Modos de afinidade e os seus efeitos][Image1]

### <a name="best-effort-desired-state"></a>Estado de esforço pretendido melhor
Existem algumas diferenças entre afinidade e arquitecturas monolíticos. Muitos dos mesmos são porque uma relação afinidade é melhor esforço. Os serviços numa relação afinidade são bastante diferentes entidades que podem falhar e ser movidas de forma independente. Também existem causas para por que motivo quebra de uma relação afinidade. Por exemplo, podem ajustar o limitações de capacidade onde apenas algumas do serviço de objectos na relação afinidade num dado nó. Nestes casos apesar de não existe uma relação afinidade num local,-não pode ser aplicada devido as noutras restrições. Se é possível impor todas as outras restrições e afinidade tarde violação da restrição afinidade será corrigida automaticamente.  

### <a name="chains-vs-stars"></a>Cadeias vs. estrelas
Hoje em dia não podermos cadeias de modelo de relações de afinidade. O que significa que é um serviço que é um elemento subordinado uma relação de afinidade não pode ser um elemento principal nas noutra relação afinidade. Se pretender modelar deste tipo de relação, terá eficazmente modelá-la como uma estrela, em vez de uma cadeia. Para poder fazer isto, a criança inferior seria pertencer a principal a criança "meio" em vez disso. Dependendo da disposição dos seus serviços, poderá ser necessário criação de um serviço de "marcador de posição" para servir como principal para crianças múltiplos.

![Cadeias vs. estrelas no contexto de afinidade relações][Image2]

Outra ação atenção sobre relações de afinidade hoje é que estes provêm direccionais. Isto significa que a regra "afinidade" impõe apenas que a criança está onde principal. Se por exemplo principal inesperadamente falhar sobre para outro nó, em seguida, o Gestor de recursos de Cluster não realmente pense que não há nada mal até dos anúncios que a criança não está localizada com um elemento principal; a relação não é aplicada imediatamente.

### <a name="partitioning-support"></a>Criar a partições suporte
A coisa final para aviso sobre afinidade é essa afinidade relações não são suportadas onde estiver dividido em partições principal. Este é algo que poderá suportamos desejar, mas hoje não é permitida.

## <a name="next-steps"></a>Próximos passos
- Para obter mais informações sobre as outras opções disponíveis para a configuração dos serviços de dar saída o tópico sobre as outras configurações do Gestor de recursos de Cluster disponíveis, [Saiba mais sobre como configurar os serviços](service-fabric-cluster-resource-manager-configure-services.md)
- Muitas razões onde as pessoas utilizam afinidade, tal como limitar serviços para um pequeno conjunto de máquinas e tentar agregar a carga de uma coleção de serviços, são suportadas melhor através dos grupos de aplicação. Dar saída de [Grupos de aplicações](service-fabric-cluster-resource-manager-application-groups.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png
