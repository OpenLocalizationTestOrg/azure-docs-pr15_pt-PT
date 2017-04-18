<properties
    pageTitle="Subscrição do Microsoft Azure e limites de serviço, Quotas e restrições"
    description="Fornece uma lista de subscrição do Azure comuns e limites de serviço, quotas e restrições. Isto inclui informações sobre como aumentar limites juntamente com valores máximos."
    services=""
    documentationCenter=""
    authors="rothja"
    manager="jeffreyg"
    editor=""
    tags="billing"
    />

<tags
    ms.service="billing"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="btardif"/>

# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Subscrição do Azure e limites de serviço, quotas e restrições

## <a name="overview"></a>Descrição geral

Este documento especifica algumas dos limites do Microsoft Azure mais comuns. Isto não atualmente aborda a todos os serviços do Azure. Ao longo do tempo, estes limites serão expandidos e atualizados para cobrir de forma mais da plataforma.

Visite a [Descrição geral de preços do Azure](https://azure.microsoft.com/pricing/) para saber mais sobre preços Azure. Aqui pode calcular os custos do utilizando a [Preços Calculadora](https://azure.microsoft.com/pricing/calculator/) ou ao visitar a página de detalhes do preços para um serviço (por exemplo, [Windows VMs](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)).

> [AZURE.NOTE] Se pretender elevar o limite acima do **Limite predefinido**, pode [Abrir um pedido de suporte online do cliente sem encargos](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). Os limites não podem ser aumentados acima do **Limite máximo** de valor nas tabelas abaixo. Se não existirem nenhuma coluna **Limite máximo** , em seguida, o recurso especificado não têm limites ajustáveis.

## <a name="limits-and-the-azure-resource-manager"></a>Limites e o Azure Gestor de recursos

Agora é possível combinar múltiplos recursos no Azure para um único grupo de recursos do Azure. Quando utilizar grupos de recursos, limites de uma vez foram globais tornam-se geridos a um nível regional com o Gestor de recursos do Azure. Para mais informações sobre os grupos de recursos do Azure, consulte o artigo [Descrição geral do Gestor de recursos do Azure](azure-resource-manager/resource-group-overview.md).

Em limites abaixo, uma nova tabela foi adicionada para refletir as diferenças nos limites ao utilizar o Gestor de recursos do Azure. Por exemplo, existe uma tabela de **Limites de subscrição** e uma tabela de **Limites de subscrição - Gestor de recursos do Azure** . Quando aplica um limite para ambos os cenários, só é mostrada na primeira tabela. Salvo indicação em contrário, os limites são globais através de todas as regiões.

> [AZURE.NOTE] É importante realçar que as quotas de recursos em grupos de recursos do Azure são por região acessível pela sua subscrição e não por subscrição, tal como são as quotas de gestão de serviço. Vamos utilizar as quotas de core de exemplo. Se precisar de pedir um aumento de quota com o suporte para núcleos, tem de decidir quantos núcleos que pretende utilizar no quais regiões e, em seguida, faça um pedido de específico para as quotas do grupo de recursos do Azure principais para o quantidades e regiões que pretende. Por isso, se precisar de utilizar 30 núcleos na Europa Ocidental para executar a sua aplicação; especificamente deverá pedir 30 núcleos na Europa Ocidental. Mas não terá uma quota de core aumentar em qualquer outro região – apenas Ocidental Europe terão a quota de 30-core.
<!-- -->
Como resultado, poderá achar útil a ter em consideração decidir o que as quotas de grupo de recursos do Azure tem de ser para sua carga de trabalho em qualquer uma região e pedir esse valor em cada região em que está a considerar implementação. Consulte [resolução de problemas de implementação](./resource-manager-common-deployment-errors.md) para obter mais ajuda a descobrir as quotas atuais para regiões específicas.

## <a name="service-specific-limits"></a>Limites de específico do serviço

- [O Active Directory](#active-directory-limits)
- [Gestão de API](#api-management-limits)
- [Aplicação de serviço](#app-service-limits)
- [Gateway de aplicação](#application-gateway-limits)
- [Informações de aplicação](#application-insights-limits)
- [Automatização](#automation-limits)
- [Cache de Azure Redis](#azure-redis-cache-limits)
- [Azure RemoteApp](#azure-remoteapp-limits)
- [Cópia de segurança](#backup-limits)
- [Batch](#batch-limits)
- [Serviços de BizTalk](#biztalk-services-limits)
- [CDN](#cdn-limits)
- [Serviços em nuvem](#cloud-services-limits)
- [Fábrica de dados](#data-factory-limits)
- [Lake a análise de dados](#data-lake-analytics-limits)
- [DNS](#dns-limits)
- [DocumentDB](#documentdb-limits)
- [Concentradores de evento](#event-hubs-limits)
- [Concentrador IoT](#iot-hub-limits)
- [Chave Cofre](#key-vault-limits)
- [Serviços de multimédia](#media-services-limits)
- [Cativação móvel](#mobile-engagement-limits)
- [Serviços móveis](#mobile-services-limits)
- [Monitorização](#monitoring-limits)
- [Autenticação Multifator](#multi-factor-authentication)
- [Funcionamento em rede](#networking-limits)
- [Serviço de notificação do concentrador](#notification-hub-service-limits)
- [Informações operacionais avançadas](#operational-insights-limits)
- [Grupo de recursos](#resource-group-limits)
- [Programador](#scheduler-limits)
- [Pesquisa](#search-limits)
- [Serviço Bus](#service-bus-limits)
- [Recuperação de sites](#site-recovery-limits)
- [Base de dados SQL](#sql-database-limits)
- [Armazenamento](#storage-limits)
- [Sistema de StorSimple](#storsimple-system-limits)
- [Análise da cadeia](#stream-analytics-limits)
- [Subscrição](#subscription-limits)
- [Gestor de tráfego](#traffic-manager-limits)
- [Máquinas virtuais](#virtual-machines-limits)
- [Conjuntos de escala de máquina virtual](#virtual-machine-scale-sets-limits)


### <a name="subscription-limits"></a>Limites de subscrição
#### <a name="subscription-limits"></a>Limites de subscrição
[AZURE.INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>Limites de subscrição - Gestor de recursos do Azure

Os seguintes limites aplicam-se ao utilizar o Gestor de recursos do Azure e grupos de recursos do Azure. Limites de que não tenham sido alterados com o Gestor de recursos do Azure não estão indicados abaixo. Consulte a tabela anterior para esses limites.

Para obter informações sobre como lidar com limites de pedidos de Gestor de recursos, consulte o artigo [pedidos de limitação Gestor de recursos](resource-manager-request-limits.md).

[AZURE.INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]


### <a name="resource-group-limits"></a>Limites do grupo de recursos

[AZURE.INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Limites de máquinas virtuais
#### <a name="virtual-machine-limits"></a>Limites de máquina virtual
[AZURE.INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]


#### <a name="virtual-machines-limits---azure-resource-manager"></a>Limites de máquinas virtuais - Gestor de recursos do Azure

Os seguintes limites aplicam-se ao utilizar o Gestor de recursos do Azure e grupos de recursos do Azure. Limites de que não tenham sido alterados com o Gestor de recursos do Azure não estão indicados abaixo. Consulte a tabela anterior para esses limites.

[AZURE.INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="virtual-machine-scale-sets-limits"></a>Limites de conjuntos de escala de máquina virtual

[AZURE.INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="networking-limits"></a>Limites de funcionamento em rede

[AZURE.INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="networking-limits"></a>Limites de funcionamento em rede
[AZURE.INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="application-gateway-limits"></a>Limites de Gateway de aplicação

[AZURE.INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="traffic-manager-limits"></a>Limites do Gestor de tráfego

[AZURE.INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="dns-limits"></a>Limites DNS

[AZURE.INCLUDE [dns-limits](../includes/dns-limits.md)]

### <a name="storage-limits"></a>Limites de armazenamento

Para detalhes adicionais sobre os limites de conta de armazenamento, consulte o artigo [escalabilidade de armazenamento do Azure e metas de desempenho](../articles/storage/storage-scalability-targets.md).

#### <a name="storage-service-limits"></a>Limites do serviço de armazenamento

[AZURE.INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

#### <a name="virtual-machine-disk-limits"></a>Limites de disco máquina virtual

[AZURE.INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

Para detalhes adicionais, consulte [tamanhos de Máquina Virtual](../articles/virtual-machines/virtual-machines-linux-sizes.md) .

**Contas de armazenamento padrão**

[AZURE.INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

**Contas de armazenamento Premium**

[AZURE.INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

#### <a name="storage-resource-provider-limits"></a>Limites de armazenamento de fornecedor de recursos

[AZURE.INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]


### <a name="cloud-services-limits"></a>Limites de serviços em nuvem

[AZURE.INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]


### <a name="app-service-limits"></a>Limites do serviço de aplicação
Os seguintes limites de aplicação de serviço incluem limites para Web Apps, aplicações Mobile, API aplicações e Apps lógica.

[AZURE.INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="scheduler-limits"></a>Limites de programador

[AZURE.INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Limites do lote

[AZURE.INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

###<a name="biztalk-services-limits"></a>Serviços de BizTalk limites
A tabela seguinte mostra os limites do Azure Biztalk serviços.

[AZURE.INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]


### <a name="documentdb-limits"></a>Limites de DocumentDB

[AZURE.INCLUDE [azure-documentdb-limits](../includes/azure-documentdb-limits.md)]

Quotas listadas com um asterisco (*), [pode ser ajustados ao contactar o suporte Azure](./documentdb/documentdb-increase-limits.md).

### <a name="mobile-engagement-limits"></a>Limites de Cativação Mobile

[AZURE.INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]


### <a name="search-limits"></a>Limites de pesquisa

Comparar camadas determinam a capacidade e limites do seu serviço de pesquisa. Camadas incluem:

- Serviço de inquilino com várias *livre* , partilhado com outros subscritores Azure, destinados a avaliação e desenvolvimento pequenas projetos.
- *Básicas* fornece recursos informáticos dedicados para cargas de trabalho de produção uma escala mais pequena, com até três réplicas para cargas de trabalho de consulta altamente disponível.
- *Padrão (S1, S2, S3, S3 alta densidade)* é para maiores produção das cargas de trabalho. Vários níveis existem dentro da camada padrão, para que possa escolher uma configuração de recursos para cenários específicos.

**Limites por subscrição**

[AZURE.INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**Limites por serviço de pesquisa**

[AZURE.INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

Para mais granulares informações sobre os outros limites, incluindo o tamanho do documento, consultas por segundo, chaves, pedidos e respostas, consulte o artigo [limites de serviço do Azure pesquisa](search/search-limits-quotas-capacity.md).

### <a name="media-services-limits"></a>Limites dos serviços de multimédia

[AZURE.INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="cdn-limits"></a>Limites CDN

[AZURE.INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>Limites de serviços móveis

[AZURE.INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="monitoring-limits"></a>Limites de monitorização

[AZURE.INCLUDE [monitoring-limits](../includes/monitoring-limits.md)]

### <a name="notification-hub-service-limits"></a>Limites do serviço de concentrador de notificação

[AZURE.INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>Limites de concentradores de evento

[AZURE.INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>Limites de Bus de serviço

[AZURE.INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### <a name="iot-hub-limits"></a>Limites de IoT concentrador

[AZURE.INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### <a name="data-factory-limits"></a>Limites de fábrica do mesmo de dados

[AZURE.INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### <a name="data-lake-analytics-limits"></a>Limites de Lake análise de dados
[AZURE.INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### <a name="stream-analytics-limits"></a>Limites de análise da cadeia
[AZURE.INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### <a name="active-directory-limits"></a>Limites do Active Directory

[AZURE.INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]


### <a name="azure-remoteapp-limits"></a>Limites de RemoteApp Azure

[AZURE.INCLUDE [azure-remoteapp-limits](../includes/azure-remoteapp-limits.md)]

### <a name="storsimple-system-limits"></a>Limites do sistema StorSimple

[AZURE.INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]


### <a name="operational-insights-limits"></a>Limites de informações operacionais

[AZURE.INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

### <a name="backup-limits"></a>Limites de cópia de segurança

[AZURE.INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="site-recovery-limits"></a>Limites de recuperação de sites

[AZURE.INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="application-insights-limits"></a>Limites de informações de aplicações

[AZURE.INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

### <a name="api-management-limits"></a>Limites de gestão de API

[AZURE.INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-redis-cache-limits"></a>Limites de Redis Cache Azure

[AZURE.INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>Limites de chave Cofre

[AZURE.INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication"></a>Autenticação Multifator
[AZURE.INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Limites de automatização
[AZURE.INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="sql-database-limits"></a>Limites de base de dados SQL

Para limites de base de dados SQL, consulte o artigo [Limites de recursos de base de dados SQL](sql-database/sql-database-resource-limits.md).

## <a name="see-also"></a>Consulte também

[Noções sobre limites Azure e aumenta](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Máquina virtual e tamanhos de serviço de nuvem para Azure](virtual-machines/virtual-machines-linux-sizes.md)

[Tamanhos dos serviços em nuvem](cloud-services/cloud-services-sizes-specs.md)
