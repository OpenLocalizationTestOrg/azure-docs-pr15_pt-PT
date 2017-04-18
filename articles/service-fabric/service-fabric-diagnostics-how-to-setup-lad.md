<properties
   pageTitle="Recolher registos utilizando os diagnósticos do Linux Azure | Microsoft Azure"
   description="Este artigo descreve como configurar o Azure diagnósticos para recolher registos a partir de um cluster de serviço ferro Linux em execução no Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/28/2016"
   ms.author="subramar"/>


# <a name="collect-logs-by-using-azure-diagnostics"></a>Recolher registos utilizando os diagnósticos do Azure

> [AZURE.SELECTOR]
- [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
- [Linux](service-fabric-diagnostics-how-to-setup-lad.md)

Quando estiver a executar um cluster de Azure Service ferro, é aconselhável recolher os registos de todos os nós numa localização central. Está a ter os registos numa localização central torna mais fácil analisar e resolução de problemas, quer estejam no seus serviços, a aplicação ou o próprio cluster. Uma forma de carregar e recolher registos é utilizar a extensão de diagnósticos do Azure, carrega registos de armazenamento do Windows Azure. Pode ler os eventos a partir do armazenamento e colocá-los num produto como [Pesquisa flexível](service-fabric-diagnostic-how-to-use-elasticsearch.md) ou outra solução de registo de análise.

## <a name="log-sources-that-you-might-want-to-collect"></a>Origens de registo que poderá pretender recolher
- **Registos de serviço ferro**: emitida a partir da plataforma através do [LTTng](http://lttng.org) e carregado para a sua conta de armazenamento. Registos podem ser eventos operacionais ou eventos de runtime que emite a plataforma. Estes registos são armazenados na localização que especifica o manifesto cluster. (Para obter os detalhes de conta de armazenamento, procure a etiqueta **AzureTableWinFabETWQueryable** e procure **StoreConnectionString**.)
- **Eventos de aplicações**: emitida de código do seu serviço. Pode utilizar qualquer solução de registo que escreve ficheiros de registo baseado em texto – por exemplo, LTTng. Para mais informações, consulte a documentação de LTTng no rastrear a aplicação.  


## <a name="deploy-the-diagnostics-extension"></a>Implementar a extensão de diagnóstico
É o primeiro passo no recolher registos implementar a extensão de diagnóstico em cada uma das VMs no cluster ferro de serviço. A extensão de diagnóstico recolhe registos em cada VM e os carregamentos pendentes para a conta de armazenamento que especificar. Os passos variam consoante se utiliza o Azure portal ou Gestor de recursos do Azure.

Para implementar a extensão de diagnóstico VMs no cluster como parte da criação do cluster, defina **Diagnósticos** para **no**. Depois de criar o cluster, não pode alterar esta definição utilizando o portal.

Em seguida, configure Linux Azure diagnósticos (espertalhão qualquer) para recolher os ficheiros e colocá-los para a sua conta de armazenamento. Este processo é explicado como cenário 3 ("aos seus ficheiros de registo carregar") no artigo [Espertalhão qualquer utilizando a monitorização e diagnosticar Linux VMs](../virtual-machines/virtual-machines-linux-classic-diagnostic-extension.md). Seguir este processo obtém acesso para os seus rastreios. Pode carregar os traços para um visualizer da sua escolha.

Também pode implementar a extensão de diagnóstico ao utilizar o Gestor de recursos do Azure. O processo é semelhante para Windows e Linux e é documentado para clusters do Windows no [artigo como recolher registos com diagnósticos do Azure](service-fabric-diagnostics-how-to-setup-wad.md).

Também pode utilizar operações de gestão de conjunto de aplicações, tal como descrito em [Operações de gestão de conjunto de aplicações do registo Analytics com Linux](https://blogs.technet.microsoft.com/hybridcloud/2016/01/28/operations-management-suite-log-analytics-with-linux/).

Depois de concluir esta configuração, o agente espertalhão qualquer monitoriza os ficheiros de registo especificado. Sempre que uma nova linha é anexada ao ficheiro, que cria uma entrada do sistema que é enviada para o armazenamento que especificou.


## <a name="next-steps"></a>Próximos passos
Para compreender mais detalhadamente que eventos deverá examinar durante a resolução de problemas, consulte o artigo [LTTng documentação](http://lttng.org/docs) e [Espertalhão utilizando qualquer](../virtual-machines/virtual-machines-linux-classic-diagnostic-extension.md).
