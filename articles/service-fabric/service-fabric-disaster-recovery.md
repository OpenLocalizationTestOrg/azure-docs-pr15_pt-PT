<properties
   pageTitle="Recuperação de falhas de serviço ferro Azure | Microsoft Azure"
   description="Azure Service ferro oferece as funcionalidades necessárias para lidar com todos os tipos de catástrofes. Este artigo descreve os tipos de catástrofes que podem ocorrer e como lidar com os mesmos."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/10/2016"
   ms.author="seanmck"/>

# <a name="disaster-recovery-in-azure-service-fabric"></a>Recuperação de falhas no ferro de serviço do Azure

Uma parte fundamental da faz uma aplicação de alta disponibilidade nuvem é garantir que-pode permanecem todos os tipos diferentes de falhas, incluindo os que estão fora da sua controlo. Este artigo descreve o esquema físico de um cluster de Azure Service ferro no contexto das catástrofes potenciais e fornece orientações sobre como lidar com essas catástrofes para limitar ou eliminar o risco de tempo de inatividade ou perda de dados.

## <a name="physical-layout-of-service-fabric-clusters-in-azure"></a>Esquema físico de serviço ferro clusters no Azure

Para compreender os riscos apresentados por diferentes tipos de falhas, é útil para saber como clusters são física apresentados no Azure.

Quando cria um cluster de serviço ferro no Azure, são necessários para escolher uma região onde irá alojado. Infraestrutura do Azure Aprovisiona, em seguida, os recursos para esse cluster dentro de uma região, nomeadamente mais o número de máquinas virtuais (VMs) solicitado. Vamos olhar mais atentamente como e onde configurações para esses VMs.

### <a name="fault-domains"></a>Domínios de falhas

Por predefinição, VMs no cluster uniformemente são propagados em grupos lógicos conhecidos como domínios de falhas (FDs), que segmentar máquinas com base em potenciais falhas no hardware de anfitrião. Especificamente, se dois VMs residem na duas FDs distintos, pode ter a certeza que não partilhar a mesma transição de origem ou de rede de power. Como resultado, uma falha de energia afetar uma VM ou de rede local será não vai afetar o outro, permitindo-ferro de serviço redistribuir a carga de trabalho do computador que não responde dentro do cluster.

Pode visualizar o esquema do seu cluster através de domínios de falhas utilizando o mapa de cluster fornecido no [Serviço ferro Explorer](service-fabric-visualizing-your-cluster.md):

![Distribuir a nós por domínios falhas no serviço ferro Explorer][sfx-cluster-map]

>[AZURE.NOTE] No eixo o mapa de cluster mostra domínios atualização, que logicamente grupo nós com base em atividades de manutenção planeada. Serviço ferro clusters no Azure sempre são apresentados em todos os cinco domínios de atualização.

### <a name="geographic-distribution"></a>Distribuição geográfica

Existem atualmente [26 regiões Azure em todo o mundo][azure-regions], com vários mais anunciada. Uma região individual pode conter um ou mais centros de dados físicos dependendo do pedido e a disponibilidade de localizações adequadas, entre outros fatores. Nota, no entanto, mesmo em regiões que contenham vários centros de dados físicos, não há garantias que seu cluster VMs irão sejam distribuídas uniformemente em várias localizações esses físicas. Na verdade, atualmente, todos os VMs para um determinado cluster configurações dentro de um site único físico.

## <a name="dealing-with-failures"></a>Trabalhar com falhas

Existem vários tipos de falhas podem afetar o seu cluster, cada uma com as suas próprias mitigação. Vamos-las por ordem de probabilidade para ocorrer.

### <a name="individual-machine-failures"></a>Falhas de máquina individuais

Tal como mencionado, falhas de máquina individuais, a VM ou na alojamento-la dentro de um domínio de falhas de software ou hardware constituem sem risco sozinho. Serviço ferro normalmente irá detetar a falha em segundos e responder em conformidade com base no estado do cluster. Por exemplo, se o nó foi alojar as réplicas principais para uma partição, é eleito um novo principal a partir do réplicas secundário a partição. Quando o Azure dá a máquina falha cópias de segurança, será automaticamente voltar a participar cluster e demorar mais uma vez na sua partilha da carga de trabalho.

### <a name="multiple-concurrent-machine-failures"></a>Várias falhas de máquina em simultâneo

Enquanto domínios falhas reduzam substancialmente o risco de falhas de máquina em simultâneo, há sempre a possibilidade de várias falhas aleatórias trazer para baixo várias máquinas num cluster em simultâneo.

Em geral, desde que a maioria de nós permanecem disponíveis, cluster irão continuar a funcionar, embora a capacidade inferior como réplicas com estado obtenham compactadas para um conjunto mais pequeno de máquinas e instâncias sem estado menos estão disponíveis para distribuir carga.

#### <a name="quorum-loss"></a>Perda de quórum

Se a maioria das réplicas para partição de um serviço com estado Ir para baixo, essa partição introduz um estado conhecido como "perda quórum." Neste momento, serviço ferro deixa de permitindo escritas para essa partição para assegurar que o seu estado permanece consistente e fiável. Na verdade, podemos estiver a escolher aceitar um período de indisponibilidade para se certificar de que os clientes não são informa que os respetivos dados foram guardados quando na verdade não foi. Tenha em atenção que se tiver optado por participar para permitir a lê provenientes réplicas secundárias desse serviço com estado, pode continuar a efetuar as operações enquanto neste estado de leitura. Uma partição permanece na perda de quórum até um número de réplicas suficiente voltar atrás ou até o administrador de cluster força o sistema para se deslocar utilizar a [API ServiceFabricPartition reparação][repair-partition-ps].

>[AZURE.WARNING] Efetuar uma ação de reparação enquanto está a réplica principal para baixo irá resultar na perda de dados.

Serviços de sistema também podem sofrem perda quórum, com o impacto serem específicas para o serviço em questão. Por exemplo, perda quórum no serviço de nomenclatura irá afetar a resolução de nomes, Considerando que perda quórum no serviço de Gestor de activação pós-falha bloqueará nova criação do serviço e activações pós-falha. Note que ao contrário para os seus serviços, tentar reparar os serviços do sistema é *não* recomendado. Em vez disso, é preferível simplesmente Aguarde até que as réplicas para baixo devolvem.

#### <a name="minimizing-the-risk-of-quorum-loss"></a>Minimizar o risco de perda de quórum

Pode minimizar o risco de perda de quórum aumentando o tamanho do conjunto de réplica destino do seu serviço. É útil a ter em conta o número de réplicas precisa em termos o número de nós indisponíveis que pode tolerar na assim enquanto restante disponíveis para operações de escrita, tendo em conta dessa aplicação ou actualizações de cluster fazer nós temporariamente indisponível, para além de falhas de hardware.

Considere os exemplos seguintes partindo do princípio de que configurou seus serviços de ter um MinReplicaSetSize de três, o menor número recomendado para os serviços de produção. Com um TargetReplicaSetSize de três (um primário e dois secundários), uma falha de hardware durante uma atualização (duas réplicas para baixo) irá resultar na perda de quórum e o seu serviço irão tornar-se só de leitura. Em alternativa, se tiver cinco réplicas, seria capaz de suportar duas falhas durante a atualização (três réplicas para baixo), tal como as duas réplicas restantes ainda podem formar um quórum dentro do conjunto de réplica mínimo.

### <a name="data-center-outages-or-destruction"></a>Falhas de centro de dados ou destruição

Em casos raros, centros de dados física podem estar temporariamente indisponíveis devido a perda de conectividade de rede ou power. Nestes casos, o serviço ferro clusters e as aplicações do mesmo modo não estará disponíveis mas os dados serão preservados. Para clusters em execução no Azure, pode ver as atualizações no falhas na [página Estado do Azure][azure-status-dashboard].

No evento altamente provável que um centro de dados física completa é destruído, qualquer clusters de serviço ferro alojados aí serão perdidos, juntamente com o seu estado.

Para proteger contra esta possibilidade, é muito importante periodicamente de [Estado da sua de cópia de segurança](service-fabric-reliable-services-backup-restore.md) para um arquivo geo redundantes e certifique-se de que tenham validados a capacidade de restaurá-la. Com que frequência efetuar uma cópia de segurança serão dependente no seu objectivo de ponto de recuperação (RPO). Mesmo não totalmente implementou cópia de segurança e restauro ainda, deve implementar um processador para o `OnDataLoss` evento para que pode iniciar sessão quando ocorre como se segue:

```c#
protected virtual Task<bool> OnDataLoss(CancellationToken cancellationToken)
{
  ServiceEventSource.Current.ServiceMessage(this, "OnDataLoss event received.");
  return Task.FromResult(true);
}
```


### <a name="software-failures-and-other-sources-of-data-loss"></a>Falhas de software e outras fontes de perda de dados

Como um problema de perda de dados, é mais comuns que falhas de centro de dados executarão defeito de código em serviços, erros operacionais humanos e violações de segurança. No entanto, todos os casos, a estratégia de recuperação é o mesmo: tirar cópias de segurança regulares de todos os serviços com estado e exercer a sua capacidade para restaurar desse Estado.

## <a name="next-steps"></a>Próximos passos

- Saiba como simular várias falhas utilizando o [quadro testability](service-fabric-testability-overview.md)
- Outros recursos de recuperação de falhas e elevada disponibilidade de leitura. A Microsoft publicou uma grande quantidade de orientações sobre estes tópicos. Enquanto algumas destes documentos consultar técnicas específicas para utilização em outros produtos, que contêm muitos procedimentos recomendados gerais que pode aplicar no contexto ferro de serviço:
 - [Lista de verificação de disponibilidade](../best-practices-availability-checklist.md)
 - [Efetuar uma pesquisa de recuperação de falhas](../sql-database/sql-database-disaster-recovery-drills.md)
 - [Recuperação de falhas e elevada disponibilidade para aplicações do Azure][dr-ha-guide]


<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
