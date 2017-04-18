<properties 
    pageTitle="Como administrar Azure Redis Cache | Microsoft Azure"
    description="Saiba como efetuar tarefas de administração, como as atualizações de reiniciar o computador e agenda para Azure Redis Cache"
    services="redis-cache"
    documentationCenter="na"
    authors="steved0x"
    manager="douge"
    editor="tysonn" />
<tags 
    ms.service="cache"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="09/27/2016"
    ms.author="sdanie" />

# <a name="how-to-administer-azure-redis-cache"></a>Como administrar a Cache de Redis Azure

Este tópico descreve como efetuar tarefas de administração, como reiniciar o computador e agendar atualizações para o seu instâncias do Azure Redis Cache.

>[AZURE.IMPORTANT] As definições e as funcionalidades descritas neste artigo apenas estão disponíveis para caches de camada Premium.


## <a name="administration-settings"></a>Definições de administração

As definições de Cache de Redis Azure **Administração** permitem-lhe executar as seguintes tarefas administrativas para a cache de premium. Para aceder às definições de administração, clique em **Definições** ou **todas as definições** de pá Redis Cache e desloque-se à secção **Administração** pá as **Definições** .

![Administração](./media/cache-administration/redis-cache-administration.png)

-   [Reiniciar o computador](#reboot)
-   [Atualizações de agenda](#schedule-updates)

## <a name="reboot"></a>Reiniciar o computador

O pá **reinicie** permite-lhe um ou mais nós da cache de reiniciar. Permite-lhe testar a sua aplicação de RDP em caso de falha.

![Reiniciar o computador](./media/cache-administration/redis-cache-reboot.png)

Se tiver uma cache premium com clusters ativado, pode selecionar quais shards da cache para reiniciar o computador.

![Reiniciar o computador](./media/cache-administration/redis-cache-reboot-cluster.png)

Para reiniciar um ou mais nós da cache, selecione os nós pretendido e clique em **reiniciar**. Se tiver uma cache premium com clusters ativado, selecione shard(s) para reiniciar o computador e, em seguida, clique em **reiniciar**. Depois de alguns minutos, o reinício nós selecionado e estiver novamente online alguns minutos mais tarde.

O impacto na aplicações de cliente varia consoante o nós que reiniciar o computador.

-   **Modelo global de** -quando o nó mestra é reiniciado, Azure Redis Cache Falha ao longo para o nó réplica e promove ao modelo global. Durante este falha na ligação, poderão existir um breve intervalo na qual poderão falhar ligações para a cache.
-   **Secundária** - quando o nó subordinado é reiniciado, normalmente, não existe nenhuma impacto para os clientes de cache.
-   **Ambos mestre e secundária** - quando são reiniciados ambos os nós de cache, todos os dados perde-se na cache e ligações para as falhas de cache até o nó primário fica novamente online. Se tiver configurado [recorrência de dados](cache-how-to-premium-persistence.md), a cópia de segurança mais recente é restaurada quando a cache estiver novamente online. Tenha em atenção que as gravações de cache ocorreram após a cópia de segurança mais recente são perdidas.
-   O comportamento de **nós de uma cache premium com clusters ativado** - quando reiniciar o nós de uma cache premium com clusters ativado, é o mesmo que quando reiniciar o nós de uma cache que não sejam agrupadas.


>[AZURE.IMPORTANT] Reinicie só está disponível para caches de camada Premium.

## <a name="reboot-faq"></a>Reinicie o perguntas mais frequentes

-   [Qual o nó deverá reiniciar para testar a minha aplicação?](#which-node-should-i-reboot-to-test-my-application)
-   [Pode reiniciar a cache para limpar ligações de cliente?](#can-i-reboot-the-cache-to-clear-client-connections)
-   [Posso perderá dados a partir do meu cache se posso fazer um reinício?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
-   [Pode reiniciar meu cache utilizando o PowerShell, clip ou outras ferramentas de gestão?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)
-   [O que preços camadas, podem utilizar a funcionalidade de reiniciar o computador?](#what-pricing-tiers-can-use-the-reboot-functionality)


### <a name="which-node-should-i-reboot-to-test-my-application"></a>Qual o nó deverá reiniciar para testar a minha aplicação?

Para testar RDP da sua aplicação contra a falha do nó principal da sua cache, reinicie o nó do **modelo global** . Para testar RDP da sua aplicação contra falhas do nó secundário, reinicie o nó **secundária** . Para testar RDP da sua aplicação contra falhas do total da cache, reinicie **ambos os** nós.

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>Pode reiniciar a cache para limpar ligações de cliente?

Sim, se reiniciar a cache de todas as ligações de cliente estão desmarcadas. Isto pode útil nos casos onde todas as ligações de cliente são utilizadas para cima, por exemplo devido a um erro de lógica ou um erro na aplicação cliente. Cada camada comparar tem diferentes [limites de ligação do cliente](cache-configure.md#default-redis-server-configuration) para vários tamanhos e uma vez que estes limites são atingidos, sem mais ligações de cliente são aceites. Reiniciar a cache fornece uma forma para limpar todas as ligações de cliente.

>[AZURE.IMPORTANT] Se o seu cliente as ligações são utilizadas para cima devido a um erro de lógica ou erros no seu código do cliente, tenha em atenção que StackExchange.Redis irão automaticamente restabelecer a ligação assim que o nó Redis estiver novamente online. Se o problema subjacente não for resolvido, as ligações de cliente irão continuar a ser utilizado para cima.

### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>Posso perderá dados a partir do meu cache se posso fazer um reinício?

Se reiniciar o **modelo global** e de **secundária** nós todos os dados na cache (ou esse shard se estiver a utilizar uma cache premium com clusters ativado) será perdida. Se tiver configurado [recorrência de dados](cache-how-to-premium-persistence.md), a cópia de segurança mais recente será restaurada quando a cache estiver novamente online. Tenha em atenção que as gravações de cache que tenham ocorrido depois da cópia de segurança é efetuada são perdidas.

Se reiniciar apenas um de nós, dados não são normalmente perdidos, mas ainda pode ser. Por exemplo, se for reiniciado o nó principal e um processo de escrita cache está em curso, os dados a partir de escrita de cache não está perdido. Outro cenário perda de dados seria se reiniciar um nó e o outro nó acontece Ir para baixo devido a uma falha ao mesmo tempo. Para obter mais informações sobre as causas possíveis para perda de dados, consulte o artigo [o que aconteceu aos meus dados no Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md).

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>Pode reiniciar meu cache utilizando o PowerShell, clip ou outras ferramentas de gestão?

Sim, para PowerShell instruções ver [reiniciar uma cache Redis](cache-howto-manage-redis-cache-powershell.md#to-reboot-a-redis-cache).

### <a name="what-pricing-tiers-can-use-the-reboot-functionality"></a>O que preços camadas, podem utilizar a funcionalidade de reiniciar o computador?

Reiniciar o computador está disponível apenas no premium preços de camadas.

## <a name="schedule-updates"></a>Atualizações de agenda

A **atualizações de agenda** pá permite-lhe designar uma janela de manutenção para a cache. Quando a janela de manutenção for especificada, quaisquer actualizações de servidor Redis são feitas durante esta janela. Tenha em atenção que a janela de manutenção só se aplica a Redis atualizações do servidor e não para qualquer Azure actualizações ou atualizações para o sistema operativo das VMs que alojam a cache.

![Atualizações de agenda](./media/cache-administration/redis-schedule-updates.png)

Para especificar uma janela de manutenção, verifique os dias pretendidos e especificar a hora de início de janela de manutenção para cada dia e clique em **OK**. Tenha em atenção que a hora da janela de manutenção está no UTC. 

>[AZURE.NOTE] A janela de manutenção predefinido para atualizações é de 5 horas. Este valor não é configurável a partir do portal Azure, mas pode configurá-la ao utilizar o PowerShell a `MaintenanceWindow` parâmetro do cmdlet [AzureRmRedisCacheScheduleEntry novo](https://msdn.microsoft.com/library/azure/mt763833.aspx) . Para obter mais informações, consulte o artigo [pode posso gerido utilizando o PowerShell, clip ou outras ferramentas de gestão de actualizações agendadas?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)

## <a name="schedule-updates-faq"></a>Agendar atualizações perguntas mais frequentes

-   [Quando atualizações ocorrer se não a utilizar a funcionalidade de atualizações de agenda?](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
-   [Que tipo de atualizações forem efetuadas durante a janela de manutenção agendada?](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
-   [Posso actualizações agendadas geridas utilizando o PowerShell, clip ou outras ferramentas de gestão?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)
-   [O que preços camadas, podem utilizar a funcionalidade de atualizações de agenda?](#what-pricing-tiers-can-use-the-schedule-updates-functionality)

### <a name="when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature"></a>Quando as atualizações de ocorrem se não a utilizar a funcionalidade de atualizações de agenda?

Se não especificar uma janela de manutenção, podem ser efetuadas atualizações em qualquer altura.

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window"></a>Que tipo de atualizações forem efetuadas durante a janela de manutenção agendada?

Apenas Redis servidor atualizações forem efetuadas durante a janela de manutenção agendada. A janela de manutenção não se aplica ao Azure atualizações ou em atualizações para o sistema operativo VM.

### <a name="can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools"></a>Posso actualizações agendadas geridas utilizando o PowerShell, clip ou outras ferramentas de gestão?

Sim, pode gerir as suas atualizações agendadas utilizando os seguintes cmdlets do PowerShell.

-   [Get-AzureRmRedisCachePatchSchedule](https://msdn.microsoft.com/library/azure/mt763835.aspx)
-   [Novo AzureRmRedisCachePatchSchedule](https://msdn.microsoft.com/library/azure/mt763834.aspx)
-   [Novo AzureRmRedisCacheScheduleEntry](https://msdn.microsoft.com/library/azure/mt763833.aspx)
-   [Remover AzureRmRedisCachePatchSchedule](https://msdn.microsoft.com/library/azure/mt763837.aspx)

### <a name="what-pricing-tiers-can-use-the-schedule-updates-functionality"></a>O que preços camadas, podem utilizar a funcionalidade de atualizações de agenda?

Atualizações de agenda só está disponível no premium preços de camadas.

## <a name="next-steps"></a>Próximos passos

-   Explore mais funcionalidades de [camada de premium Azure Redis Cache](cache-premium-tier-intro.md) .





