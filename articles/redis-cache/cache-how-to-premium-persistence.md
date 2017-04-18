<properties 
    pageTitle="Como configurar a recorrência de dados para uma Cache de Redis do Azure Premium" 
    description="Saiba como configurar e gerir recorrência de dados do seu instâncias de Azure Redis Cache de camadas Premium" 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/30/2016" 
    ms.author="sdanie"/>

# <a name="how-to-configure-data-persistence-for-a-premium-azure-redis-cache"></a>Como configurar a recorrência de dados para uma Cache de Redis do Azure Premium

Azure Redis Cache tem ofertas de cache diferentes que disponibiliza a flexibilidade na escolha do tamanho da cache e funcionalidades, incluindo a nova camada de Premium.

A camada do Azure Redis Cache premium inclui funcionalidades como clusters, persistente e suporte de rede virtual. Este artigo descreve como configurar persistente numa instância Azure Redis Cache premium.

Para obter informações sobre outras funcionalidades de cache premium, consulte [Introdução à camada Azure Redis Cache Premium](cache-premium-tier-intro.md).

## <a name="what-is-data-persistence"></a>O que é a recorrência de dados?
Redis persistente permite-lhe persistirem dados armazenados num Redis. Também pode tirar instantâneos e agregar os dados, que pode carregar em caso de uma falha de hardware. Esta é uma enorme vantagem sobre camada Basic ou padrão onde todos os dados são armazenadas na memória e é possível que possível perda de dados em caso de uma falha de onde nós de Cache estão para baixo. 

Azure Redis Cache oferece persistente Redis utilizando o [modelo de RDB](http://redis.io/topics/persistence), onde os dados são armazenados numa conta de armazenamento Azure. Quando persistente está configurado, Azure Redis Cache persiste um instantâneo da cache Redis num formato binário Redis no disco com base numa configurável frequência cópia de segurança. Caso ocorra um evento grave que desativa a principal e uma réplica cache, a cache é recriada utilizando o instantâneo mais recente.

Pode ser configurada persistente a partir do pá **Nova Cache Redis** durante a criação de cache e, no pá **Definições** para existente premium caches.

## <a name="create-a-premium-cache"></a>Criar uma cache premium

Para criar uma cache e configurar persistente, início de sessão no [portal do Azure](https://portal.azure.com) e clique em **Novo**->**dados + armazenamento**>**Redis Cache**.

![Criar uma Cache Redis][redis-cache-new-cache-menu]

Para configurar persistente, primeiro selecione uma das caches **Premium** na pá **Selecione a camada comparar** .

![Selecione a camada comparar][redis-cache-premium-pricing-tier]

Assim que um prémio preços camada estiver selecionado, clique em **Redis persistente**.

![Redis persistente][redis-cache-persistence]

Os passos na secção seguinte descrevem como configurar Redis persistente na sua nova cache premium. Depois de Redis persistente está configurado, clique em **Criar** para criar a sua nova cache premium com Redis persistente.

## <a name="configure-redis-persistence"></a>Configurar Redis persistente

Redis persistente está configurada na pá **Redis recorrência de dados** . Para o novos caches, este pá é acedida durante o processo de criação de cache, conforme descrito na secção anterior. Para caches existentes, o pá **Redis recorrência de dados** é acedida a partir pá as **Definições** para a sua cache.

![Redis definições][redis-cache-settings]

Para ativar Redis persistente, clique em **ativado** para ativar a cópia de segurança do RDB (Redis base de dados). Para desativar Redis persistente numa cache premium activados anteriormente, clique em **desativado**.

Para configurar o intervalo de cópia de segurança, selecione uma **Cópia de segurança frequência** a partir da lista pendente. Opções incluem **15 minutos**, **30 minutos**, **60 minutos**, **6 horas**, **12 horas**e **24 horas**. Este intervalo inicia contando para baixo, depois da operação de cópia de segurança anterior é concluída com êxito e quando-decorrida é iniciada uma nova cópia de segurança.

Clique em **Conta de armazenamento** para selecionar a conta de armazenamento para utilizar e selecione a **chave primária** ou **chave secundária** para utilizar a partir do menu pendente **Chave de armazenamento** . Tem de escolher uma conta de armazenamento na mesma região como a cache e uma conta de **Armazenamento de Premium** é recomendada porque armazenamento premium tem débito superior. 

>[AZURE.IMPORTANT] Se a chave de armazenamento da sua conta persistente é geradas novamente, tem de rechoose a chave pretendida a partir do menu pendente **Chave de armazenamento** .

![Redis persistente][redis-cache-persistence-selected]

Clique em **OK** para guardar a configuração de persistente.

Da próxima cópia de segurança (ou primeira cópia de segurança para o novos caches) é iniciada assim que o intervalo de cópia de segurança frequência decorrer.



## <a name="persistence-faq"></a>Persistente perguntas mais frequentes

A lista seguinte contém respostas às perguntas mais frequentes sobre persistente Azure Redis Cache.

-   [Pode ativar persistente numa cache previamente criada?](#can-i-enable-persistence-on-a-previously-created-cache)
-   [Posso alterar a frequência de cópia de segurança depois do que devo criar a cache?](#can-i-change-the-backup-frequency-after-i-create-the-cache)
-   [Por que motivo se tiver uma cópia de segurança frequência de 60 minutos existe mais de 60 minutos entre cópias de segurança?](#why-if-i-have-a-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
-   [O que acontece às cópias de segurança antigas quando é feita uma nova cópia de segurança?](#what-happens-to-the-old-backups-when-a-new-backup-is-made)
-   [O que acontece se posso ter dimensionados para um tamanho diferente e uma cópia de segurança é restaurada efetuada antes da operação de dimensionamento?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>Pode ativar persistente numa cache previamente criada?

Sim, Redis persistente pode ser configurado na criação de cache e nas caches do premium existente.

### <a name="can-i-change-the-backup-frequency-after-i-create-the-cache"></a>Posso alterar a frequência de cópia de segurança depois do que devo criar a cache?

Sim, pode alterar a frequência de cópia de segurança na pá **Redis recorrência de dados** . Para obter instruções, consulte o artigo [Configurar Redis persistente](#configure-redis-persistence).

### <a name="why-if-i-have-a-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>Por que motivo se tiver uma cópia de segurança frequência de 60 minutos existe mais de 60 minutos entre cópias de segurança?

Não é iniciado o intervalo de cópia de segurança frequência até que o processo de cópia de segurança anterior foi concluída com êxito. Se a frequência de cópia de segurança é 60 minutos e demora um processo de cópia de segurança 15 minutos para concluir com êxito, a cópia de segurança seguinte não é iniciado até 75 minutos após a hora de início da cópia de segurança anterior.

### <a name="what-happens-to-the-old-backups-when-a-new-backup-is-made"></a>O que acontece às cópias de segurança antigas quando é feita uma nova cópia de segurança?

Todas as cópias de segurança, à exceção mais recente são eliminadas automaticamente. Esta eliminação poderá não ocorrer imediatamente mas cópias de segurança antigas não são permanentes indefinidamente.

### <a name="what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>O que acontece se posso ter dimensionados para um tamanho diferente e uma cópia de segurança é restaurada efetuada antes da operação de dimensionamento?

-   Se tiver dimensionado para um tamanho maior, não existe nenhuma impacto.
-   Se tiver dimensionada para um tamanho mais pequeno e tiver personalizado [bases de dados](cache-configure.md#databases) que a definição é maior do que o [limite de bases de dados](cache-configure.md#databases) para o novo tamanho, dados esses bases de dados não está ser restaurada. Para obter mais informações, consulte o artigo [é minhas bases de dados personalizados definir afetado durante o dimensionamento?](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)
-   Se tiver dimensionada para um tamanho mais pequeno e não existe espaço suficiente no tamanho mais pequeno para colocar em espera todos os dados a partir da última cópia de segurança, vai ser retiradas teclas durante o processo de restaurar, normalmente utilizando a política de eviction [allkeys lru](http://redis.io/topics/lru-cache) .

## <a name="next-steps"></a>Próximos passos
Saiba como utilizar mais funcionalidades de cache premium.

-   [Introdução à camada Azure Redis Cache Premium](cache-premium-tier-intro.md)
  
<!-- IMAGES -->

[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-persistence/redis-cache-new-cache-menu.png

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-persistence-selected]: ./media/cache-how-to-premium-persistence/redis-cache-persistence-selected.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
