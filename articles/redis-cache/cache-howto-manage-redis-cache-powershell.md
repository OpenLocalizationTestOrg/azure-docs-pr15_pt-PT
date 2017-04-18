<properties
    pageTitle="Gerir a Cache do Azure Redis com Azure PowerShell | Microsoft Azure"
    description="Saiba como desempenhar tarefas administrativas para Azure Redis Cache através do Azure PowerShell."
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
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="manage-azure-redis-cache-with-azure-powershell"></a>Gerir a Cache do Azure Redis com Azure PowerShell

> [AZURE.SELECTOR]
- [PowerShell](cache-howto-manage-redis-cache-powershell.md)
- [Clip Azure](cache-manage-cli.md)

Este tópico mostra-lhe como para executar tarefas comuns tais como de criar, atualiza e dimensionar seu instâncias do Azure Redis Cache, como a gerar teclas de acesso e como ver informações sobre os caches. Para uma lista completa dos cmdlets do Azure Redis Cache PowerShell, consulte [cmdlets de Azure Redis Cache](https://msdn.microsoft.com/library/azure/mt634513.aspx).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)][modelo clássico](../resource-manager-deployment-model.md#classic-deployment-characteristics).

## <a name="prerequisites"></a>Pré-requisitos

Se já tiver instalado o Azure PowerShell, tem de ter Azure PowerShell versão 1.0.0 ou posterior. Pode verificar a versão do Azure PowerShell, que tem instalada com este comando na linha de comandos do Azure PowerShell.

    Get-Module azure | format-table version

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

Em primeiro lugar, tem de iniciar sessão Azure com este comando.

    Login-AzureRmAccount

Especifique o endereço de e-mail da sua conta Azure e a palavra-passe na Microsoft Azure início de sessão na caixa de diálogo.

Em seguida, se tiver múltiplas subscrições Azure, terá de configurar a sua subscrição do Azure. Para ver uma lista das suas subscrições atuais, execute este comando.

    Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

Para especificar a subscrição, execute o seguinte comando. No exemplo seguinte, o nome da subscrição é `ContosoSubscription`.

    Select-AzureRmSubscription -SubscriptionName ContosoSubscription

Antes de poder utilizar o Windows PowerShell com o Gestor de recursos do Azure, precisa do seguinte:

- Windows PowerShell, versão 3.0 ou 4.0. Para localizar a versão do Windows PowerShell, escreva:`$PSVersionTable` e verifique se o valor de `PSVersion` é 3.0 ou 4.0. Para instalar uma versão compatível, consulte o artigo [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

Para obter ajuda detalhada sobre qualquer cmdlet, que consulte o artigo neste tutorial, utilize o cmdlet Get-Help.

    Get-Help <cmdlet-name> -Detailed

Por exemplo, para obter ajuda para o `New-AzureRmRedisCache` cmdlet, tipo:

    Get-Help New-AzureRmRedisCache -Detailed

### <a name="how-to-connect-to-azure-government-cloud-or-azure-china-cloud"></a>Como se ligar à Azure Government Cloud ou Azure China em nuvem

Por predefinição, o Azure ambiente é `AzureCloud`, que representa a instância global Azure cloud. Para ligar a uma instância diferente, utilize o `Add-AzureRmAccount` comando com o `-Environment` ou -`EnvironmentName` parâmetro de linha de comandos com o ambiente pretendida ou o nome do ambiente.

Para ver a lista de ambientes disponíveis, execute o `Get-AzureRmEnvironment` cmdlet.

### <a name="to-connect-to-the-azure-government-cloud"></a>Para ligar para a nuvem do Azure administração pública

Para ligar para a nuvem do Azure administração pública, utilize um dos seguintes comandos.

    Add-AzureRMAccount -EnvironmentName AzureUSGovernment

ou

    Add-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureUSGovernment)

Para criar uma cache na nuvem Azure administração pública, utilize uma das seguintes localizações.

-   USGov Virginia
-   USGov Iowa

Para mais informações sobre a nuvem do Azure administração pública, consulte [Microsoft Azure administração pública](https://azure.microsoft.com/features/gov/) e [Guia de programador do Microsoft Azure administração pública](../azure-government-developer-guide.md).

### <a name="to-connect-to-the-azure-china-cloud"></a>Para ligar para a nuvem do Azure China

Para ligar para a nuvem do Azure China, utilize um dos seguintes comandos.

    Add-AzureRMAccount -EnvironmentName AzureChinaCloud

ou

    Add-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureChinaCloud)

Para criar uma cache na nuvem China Azure, utilize uma das seguintes localizações.

-   Leste China
-   América do Norte China

Para mais informações sobre a nuvem do Azure China, consulte o artigo [AzureChinaCloud para Azure operado pela 21Vianet na China](http://www.windowsazure.cn/).

### <a name="properties-used-for-azure-redis-cache-powershell"></a>Propriedades utilizadas para Azure Redis Cache PowerShell

A tabela seguinte contém propriedades e as descrições para parâmetros utilizadas mais frequentemente, quando criar e gerir o seu instâncias de Cache Redis do Azure através do Azure PowerShell.

| Parâmetro          | Descrição                                                                                                                                                                                                        | Predefinido  |
|--------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|
| Nome               | Nome da cache                                                                                                                                                                                                  |          |
| Localização           | Localização da cache                                                                                                                                                                                              |          |
| ResourceGroupName  | Nome do grupo de recursos na qual pretende criar a cache                                                                                                                                                                   |          |
| Tamanho               | O tamanho da cache. Os valores válidos são: P1, P2, P3, E4, C0, C1, C2, C3, C4, C5, C6, 250MB, 1GB, GB de 2,5, 6 GB, 13 GB, 26 GB, 53 GB                                                                     | 1GB      |
| ShardCount         | O número de shards para criar quando criar uma cache premium com clusters ativado. Os valores válidos são: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10                                                                                                      |          |
| SKU                | Especifica o SKU da cache. Os valores válidos são: básicas, padrão, Premium                                                                                                                                         | Padrão |
| RedisConfiguration | Especifica as definições de configuração de Redis. Para obter detalhes sobre cada definição, consulte a tabela de [Propriedades RedisConfiguration](#redisconfiguration-properties) seguinte. |          |
| EnableNonSslPort   | Indica se a porta de que não sejam SSL está ativada.                                                                                                                                                                     | FALSO    |
| MaxMemoryPolicy    | Este parâmetro foi preterido - como alternativa, utilize RedisConfiguration.                                                                                                                                              |          |
| StaticIP           | Quando a alojar o seu cache num VNET, especifica um endereço IP exclusivo na sub-rede para a cache. Se não for fornecido, um é escolhido por si da sub-rede.                                                                                                                     |          |
| Sub-rede             | Quando a alojar o seu cache num VNET, especifica o nome da sub-rede na qual pretende implementar a cache.                                                                                                                  |          |
| VirtualNetwork     | Quando a alojar o seu cache num VNET, especifica o ID do recurso de VNET na qual pretende implementar a cache.                                                                                                             |          |
| KeyType            | Especifica que tecla de acesso a gerar quando renovar as teclas de acesso. Os valores válidos são: primário, secundário |  |                                                                                                                                                                                                              |          |


### <a name="redisconfiguration-properties"></a>Propriedades de RedisConfiguration

| Propriedade                      | Descrição                                                                                                          | Preços camadas       |
|-------------------------------|----------------------------------------------------------------------------------------------------------------------|---------------------|
| RDB cópia de segurança activada            | Se o [Redis recorrência de dados](cache-how-to-premium-persistence.md) está ativado                                     | Apenas Premium        |
| RDB-armazenamento-ligação de cadeia | A cadeia de ligação para a conta de armazenamento para [Redis recorrência de dados](cache-how-to-premium-persistence.md)       | Apenas Premium        |
| frequência de cópia de segurança RDB          | A frequência de cópia de segurança para [Redis recorrência de dados](cache-how-to-premium-persistence.md)                               | Apenas Premium        |
| reservada maxmemory            | Configura a [memória reservados](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) para processos de que não sejam cache | Padrão e Premium |
| política de maxmemory              | Configura a [política de eviction](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) para a cache           | Todas as camadas comparar   |
| notificar-keyspace-eventos        | Configura [keyspace notificações](cache-configure.md#keyspace-notifications-advanced-settings)                     | Padrão e Premium |
| hash-máximo-ziplist-entradas      | Configura [Otimização da memória](http://redis.io/topics/memory-optimization) para tipos de dados de agregação pequenas          | Padrão e Premium |
| hash-máximo-ziplist-value        | Configura [Otimização da memória](http://redis.io/topics/memory-optimization) para tipos de dados de agregação pequenas          | Padrão e Premium |
| conjunto de máximo-intset entradas        | Configura [optimização da memória](http://redis.io/topics/memory-optimization) para tipos de dados de agregação pequenas          | Padrão e Premium |
| zset-máximo-ziplist-entradas      | Configura [Otimização da memória](http://redis.io/topics/memory-optimization) para tipos de dados de agregação pequenas          | Padrão e Premium |
| zset-máximo-ziplist-value        | Configura [Otimização da memória](http://redis.io/topics/memory-optimization) para tipos de dados de agregação pequenas          | Padrão e Premium |
| bases de dados                     | Configura o número de bases de dados. Esta propriedade pode ser configurada apenas na criação da cache.                          | Padrão e Premium |

## <a name="to-create-a-redis-cache"></a>Para criar uma Cache Redis

Novas instâncias do Azure Redis Cache são criadas utilizando o cmdlet [AzureRmRedisCache novo](https://msdn.microsoft.com/library/azure/mt634517.aspx) .

>[AZURE.IMPORTANT] A primeira vez que cria uma cache Redis numa subscrição através do portal Azure, o portal regista o `Microsoft.Cache` espaço de nomes para esse subscrição. Se tentar criar a cache de Redis primeira numa subscrição através do PowerShell, primeiro tem de registar esse espaço de nomes utilizando o seguinte comando; cmdlets caso contrário, como `New-AzureRmRedisCache` e `Get-AzureRmRedisCache` falhar.
>
>`Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Cache"`

Para ver uma lista dos parâmetros disponíveis e as respectivas descrições para `New-AzureRmRedisCache`, execute o seguinte comando.

    PS C:\> Get-Help New-AzureRmRedisCache -detailed
    
    NAME
        New-AzureRmRedisCache
    
    SYNOPSIS
        Creates a new redis cache.
    
    
    SYNTAX
        New-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Location <String> [-RedisVersion <String>]
        [-Size <String>] [-Sku <String>] [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort
        <Boolean>] [-ShardCount <Integer>] [-VirtualNetwork <String>] [-Subnet <String>] [-StaticIP <String>]
        [<CommonParameters>]
    
    
    DESCRIPTION
        The New-AzureRmRedisCache cmdlet creates a new redis cache.
    
    
    PARAMETERS
        -Name <String>
            Name of the redis cache to create.
    
        -ResourceGroupName <String>
            Name of resource group in which to create the redis cache.
    
        -Location <String>
            Location in which to create the redis cache.
    
        -RedisVersion <String>
            RedisVersion is deprecated and will be removed in future release.
    
        -Size <String>
            Size of the redis cache. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.
    
        -Sku <String>
            Sku of redis cache. The default value is Standard. Possible values are Basic, Standard and Premium.
    
        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}
    
        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value, databases.
    
        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Redis Cache. If no value is provided, the default value is false and the
            non-SSL port will be disabled. Possible values are true and false.
    
        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.
    
        -VirtualNetwork <String>
            The exact ARM resource ID of the virtual network to deploy the redis cache in. Example format: /subscriptions/{
            subid}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicNetwork/VirtualNetworks/{vnetName}
    
        -Subnet <String>
            Required when deploying a redis cache inside an existing Azure Virtual Network.
    
        -StaticIP <String>
            Required when deploying a redis cache inside an existing Azure Virtual Network.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Para criar uma cache com parâmetros predefinidos, execute o seguinte comando.

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US"

`ResourceGroupName`, `Name`, e `Location` são necessários parâmetros, mas os restantes são opcionais e têm valores predefinidos. Executar o comando anterior cria uma instância de padrão SKU Azure Redis Cache com o nome especificado, a localização e o grupo de recursos, que é 1 GB de tamanho com a porta não SSL desativado.

Para criar uma cache premium, especifique um tamanho de P1 (6 GB - 60 GB), P2 (13 GB - 130 GB), P3 (26 GB - 260 GB), ou E4 (53 GB - 530 GB). Para ativar clusters, especifique uma contagem de shard utilizando o `ShardCount` parâmetro. O exemplo seguinte cria uma cache de premium P1 com 3 shards. Uma cache de premium P1 é 6 GB de tamanho e uma vez que recomendamos especificada três shards o tamanho total é 18 GB (3 x 6 GB).

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P1 -ShardCount 3

Para especificar valores para o `RedisConfiguration` parâmetro, incluir os valores no interior `{}` como valor de chave/pares como `@{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}`. O exemplo seguinte cria uma cache de 1 GB padrão com `allkeys-random` maxmemory política keyspace as notificações e configuradas com `KEA`. Para obter mais informações, consulte o artigo [Keyspace notificações (definições avançadas)](cache-configure.md#keyspace-notifications-advanced-settings) e [Maxmemory política e reservados maxmemory](cache-configure.md#maxmemory-policy-and-maxmemory-reserved).

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}

<a name="databases"></a>
## <a name="to-configure-the-databases-setting-during-cache-creation"></a>Para configurar as bases de dados a definição durante a criação de cache

O `databases` definição pode ser configurada apenas durante a criação de cache. O exemplo seguinte cria uma premium P3 (26 GB) de cache com 48 bases de dados utilizando o cmdlet [AzureRmRedisCache novo](https://msdn.microsoft.com/library/azure/mt634517.aspx) .

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P3 -RedisConfiguration @{"databases" = "48"}

Para mais informações sobre o `databases` propriedade, consulte o artigo [predefinido Azure Redis Cache configuração do servidor](cache-configure.md#default-redis-server-configuration). Para obter mais informações sobre a criação de uma cache utilizando o cmdlet [AzureRmRedisCache novo](https://msdn.microsoft.com/library/azure/mt634517.aspx) , consulte a secção anterior [para criar uma Cache Redis](#to-create-a-redis-cache) .

## <a name="to-update-a-redis-cache"></a>Para atualizar uma cache Redis

Azure instâncias de Redis Cache são atualizadas utilizando o cmdlet [Set-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634518.aspx) .

Para ver uma lista dos parâmetros disponíveis e as respectivas descrições para `Set-AzureRmRedisCache`, execute o seguinte comando.

    PS C:\> Get-Help Set-AzureRmRedisCache -detailed
    
    NAME
        Set-AzureRmRedisCache
    
    SYNOPSIS
        Set redis cache updatable parameters.
    
    SYNTAX
        Set-AzureRmRedisCache -Name <String> -ResourceGroupName <String> [-Size <String>] [-Sku <String>]
        [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort <Boolean>] [-ShardCount
        <Integer>] [<CommonParameters>]
    
    DESCRIPTION
        The Set-AzureRmRedisCache cmdlet sets redis cache parameters.
    
    PARAMETERS
        -Name <String>
            Name of the redis cache to update.
    
        -ResourceGroupName <String>
            Name of the resource group for the cache.
    
        -Size <String>
            Size of the redis cache. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.
    
        -Sku <String>
            Sku of redis cache. The default value is Standard. Possible values are Basic, Standard and Premium.
    
        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}
    
        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value.
    
        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Redis Cache. The default value is null and no change will be made to the
            currently configured value. Possible values are true and false.
    
        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

O `Set-AzureRmRedisCache` cmdlet pode ser utilizada para atualizar as propriedades tal como `Size`, `Sku`, `EnableNonSslPort`bem como a `RedisConfiguration` valores. 

O seguinte comando atualiza a política de maxmemory para a Cache Redis denominada myCache.

    Set-AzureRmRedisCache -ResourceGroupName "myGroup" -Name "myCache" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random"}

<a name="scale"></a>
## <a name="to-scale-a-redis-cache"></a>Para dimensionar uma cache Redis

`Set-AzureRmRedisCache`podem ser utilizados para dimensionar uma cache Azure Redis instância quando o `Size`, `Sku`, ou `ShardCount` propriedades são modificadas. 

>[AZURE.NOTE]Dimensionamento uma cache através do PowerShell está sujeito a mesma limites e diretrizes como dimensionamento uma cache a partir do portal Azure. É possível dimensionar para uma camada diferente comparar com as seguintes restrições.
>
>-  Não consigo dimensionar a partir de uma camada preços mais elevada a uma camada comparar inferior.
>    -    Não consigo dimensionar a partir de uma cache **Premium** para baixo até um **padrão** ou de uma cache **básica** .
>    -    Não consigo dimensionar a partir de uma cache **padrão** para baixo até uma cache **básica** .
>-  Pode dimensionar a partir de uma cache **básicas** para uma cache **padrão** , mas não é possível alterar o tamanho ao mesmo tempo. Se precisar de um tamanho diferente, que pode fazer uma operação de dimensionamento subsequente para o tamanho pretendido.
>-  Não consigo dimensionar a partir de uma cache **básicas** diretamente para uma cache **Premium** . Tem Dimensionar **básicas** para **padrão** numa operação de dimensionamento e, em seguida, a partir do **padrão** **Premium** numa operação de dimensionamento subsequente.
>-  Não consigo dimensionar a partir de um tamanho de maior para baixo até o **C0 (250 MB)** tamanho.
>
>Para mais informações, consulte o artigo [como escala Azure Redis Cache](cache-how-to-scale.md).

O exemplo seguinte mostra como dimensionar uma cache denominada `myCache` para uma cache de 2,5 GB. Tenha em atenção que este comando funciona para um Basic ou uma cache padrão.

    Set-AzureRmRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

Depois deste comando é emitido, é devolvido o estado da cache (semelhante a chamada `Get-AzureRmRedisCache`). Tenha em atenção que a `ProvisioningState` é `Scaling`.

    PS C:\> Set-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup -Size 2.5GB
    
    
    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/mygroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Scaling
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 150], [notify-keyspace-events, KEA],
                         [maxmemory-delta, 150]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : mygroup
    PrimaryKey         : ....
    SecondaryKey       : ....
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :

Quando a operação de dimensionamento estiver concluída, o `ProvisioningState` muda para `Succeeded`. Se precisa de efetuar uma operação de dimensionamento subsequente, tais como alterar a partir básicas para padrão e, em seguida, alterar o tamanho, tem de aguardar até está concluída a operação anterior ou recebe um erro semelhante ao seguinte.

    Set-AzureRmRedisCache : Conflict: The resource '...' is not in a stable state, and is currently unable to accept the update request.

## <a name="to-get-information-about-a-redis-cache"></a>Para obter informações sobre uma cache Redis

Pode obter informações sobre uma cache utilizando o cmdlet [Get-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634514.aspx) .

Para ver uma lista dos parâmetros disponíveis e as respectivas descrições para `Get-AzureRmRedisCache`, execute o seguinte comando.

    PS C:\> Get-Help Get-AzureRmRedisCache -detailed
    
    NAME
        Get-AzureRmRedisCache
    
    SYNOPSIS
        Gets details about a single cache or all caches in the specified resource group or all caches in the current
        subscription.
    
    SYNTAX
        Get-AzureRmRedisCache [-Name <String>] [-ResourceGroupName <String>] [<CommonParameters>]
    
    DESCRIPTION
        The Get-AzureRmRedisCache cmdlet gets the details about a cache or caches depending on input parameters. If both
        ResourceGroupName and Name parameters are provided then Get-AzureRmRedisCache will return details about the
        specific cache name provided.
    
        If only ResourceGroupName is provided than it will return details about all caches in the specified resource group.
    
        If no parameters are given than it will return details about all caches the current subscription.
    
    PARAMETERS
        -Name <String>
            The name of the cache. When this parameter is provided along with ResourceGroupName, Get-AzureRmRedisCache
            returns the details for the cache.
    
        -ResourceGroupName <String>
            The name of the resource group that contains the cache or caches. If ResourceGroupName is provided with Name
            then Get-AzureRmRedisCache returns the details of the cache specified by Name. If only the ResourceGroup
            parameter is provided, then details for all caches in the resource group are returned.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Para devolver informações sobre todas as caches na subscrição atual, execute o `Get-AzureRmRedisCache` sem quaisquer parâmetros.

    Get-AzureRmRedisCache

Para devolver informações sobre todas as caches num grupo de recursos específico, execute o `Get-AzureRmRedisCache` com o `ResourceGroupName` parâmetro.

    Get-AzureRmRedisCache -ResourceGroupName myGroup

Para devolver informações acerca de uma cache específica, execute o `Get-AzureRmRedisCache` com o `Name` parâmetro que contém o nome da cache bem como a `ResourceGroupName` parâmetro com o grupo de recursos que contém essa cache.

    PS C:\> Get-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup
    
    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/myGroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Succeeded
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 62], [notify-keyspace-events, KEA],
                         [maxclients, 1000]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : myGroup
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :

## <a name="to-retrieve-the-access-keys-for-a-redis-cache"></a>Para obter as teclas de acesso para uma cache Redis

Para obter as teclas de acesso para a sua cache, pode utilizar o cmdlet [Get-AzureRmRedisCacheKey](https://msdn.microsoft.com/library/azure/mt634516.aspx) .

Para ver uma lista dos parâmetros disponíveis e as respectivas descrições para `Get-AzureRmRedisCacheKey`, execute o seguinte comando.

    PS C:\> Get-Help Get-AzureRmRedisCacheKey -detailed
    
    NAME
        Get-AzureRmRedisCacheKey
    
    SYNOPSIS
        Gets the accesskeys for the specified redis cache.
    
    
    SYNTAX
        Get-AzureRmRedisCacheKey -Name <String> -ResourceGroupName <String> [<CommonParameters>]
    
    DESCRIPTION
        The Get-AzureRmRedisCacheKey cmdlet gets the access keys for the specified cache.
    
    PARAMETERS
        -Name <String>
            Name of the redis cache.
    
        -ResourceGroupName <String>
            Name of the resource group for the cache.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Para obter as teclas para a sua cache, contacte o `Get-AzureRmRedisCacheKey` cmdlet e passam na nome a cache o nome do grupo de recursos que contém a cache.

    PS C:\> Get-AzureRmRedisCacheKey -Name myCache -ResourceGroupName myGroup
    
    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : ABhfB757JgjIgt785JgKH9865eifmekfnn649303JKL=

## <a name="to-regenerate-access-keys-for-your-redis-cache"></a>Para gerar chaves de acesso para a cache de Redis

Para gerar as teclas de acesso para a sua cache, pode utilizar o cmdlet [AzureRmRedisCacheKey novo](https://msdn.microsoft.com/library/azure/mt634512.aspx) .

Para ver uma lista dos parâmetros disponíveis e as respectivas descrições para `New-AzureRmRedisCacheKey`, execute o seguinte comando.

    PS C:\> Get-Help New-AzureRmRedisCacheKey -detailed
    
    NAME
        New-AzureRmRedisCacheKey
    
    SYNOPSIS
        Regenerates the access key of a redis cache.
    
    SYNTAX
        New-AzureRmRedisCacheKey -Name <String> -ResourceGroupName <String> -KeyType <String> [-Force] [<CommonParameters>]
    
    DESCRIPTION
        The New-AzureRmRedisCacheKey cmdlet regenerate the access key of a redis cache.
    
    PARAMETERS
        -Name <String>
            Name of the redis cache.
    
        -ResourceGroupName <String>
            Name of the resource group for the cache.
    
        -KeyType <String>
            Specifies whether to regenerate the primary or secondary access key. Possible values are Primary or Secondary.
    
        -Force
            When the Force parameter is provided, the specified access key is regenerated without any confirmation prompts.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).
    
Para gerar a chave primária ou secundária para a cache, contacte o `New-AzureRmRedisCacheKey` cmdlet e passar o nome do grupo de recursos e especificar `Primary` ou `Secondary` para o `KeyType` parâmetro. No seguinte exemplo, a tecla de acesso secundário para uma cache é geradas novamente.

    PS C:\> New-AzureRmRedisCacheKey -Name myCache -ResourceGroupName myGroup -KeyType Secondary
    
    Confirm
    Are you sure you want to regenerate Secondary key for redis cache 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
    
    
    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : c53hj3kh4jhHjPJk8l0jji785JgKH9865eifmekfnn6=

## <a name="to-delete-a-redis-cache"></a>Para eliminar uma cache Redis

Para eliminar uma cache Redis, utilize o cmdlet [AzureRmRedisCache remover](https://msdn.microsoft.com/library/azure/mt634515.aspx) .

Para ver uma lista dos parâmetros disponíveis e as respectivas descrições para `Remove-AzureRmRedisCache`, execute o seguinte comando.

    PS C:\> Get-Help Remove-AzureRmRedisCache -detailed
    
    NAME
        Remove-AzureRmRedisCache
    
    SYNOPSIS
        Remove redis cache if exists.
    
    SYNTAX
        Remove-AzureRmRedisCache -Name <String> -ResourceGroupName <String> [-Force] [-PassThru] [<CommonParameters>
    
    DESCRIPTION
        The Remove-AzureRmRedisCache cmdlet removes a redis cache if it exists.
    
    PARAMETERS
        -Name <String>
            Name of the redis cache to remove.
    
        -ResourceGroupName <String>
            Name of the resource group of the cache to remove.
    
        -Force
            When the Force parameter is provided, the cache is removed without any confirmation prompts.
    
        -PassThru
            By default Remove-AzureRmRedisCache removes the cache and does not return any value. If the PassThru par
            is provided then Remove-AzureRmRedisCache returns a boolean value indicating the success of the operatio
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

No seguinte exemplo, a cache denominada `myCache` é removido.

    PS C:\> Remove-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup
    
    Confirm
    Are you sure you want to remove redis cache 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


## <a name="to-import-a-redis-cache"></a>Para importar uma cache Redis

Pode importar dados para uma instância do Azure Redis Cache utilizando o `Import-AzureRmRedisCache` cmdlet.

>[AZURE.IMPORTANT] Importar/exportar apenas está disponível para caches [camada premium](cache-premium-tier-intro.md) . Para mais informações sobre importar/exportar, consulte o artigo [Importar e exportar os dados na Cache de Redis Azure](cache-how-to-import-export-data.md).

Para ver uma lista dos parâmetros disponíveis e as respectivas descrições para `Import-AzureRmRedisCache`, execute o seguinte comando.

    PS C:\> Get-Help Import-AzureRmRedisCache -detailed
    
    NAME
        Import-AzureRmRedisCache
    
    SYNOPSIS
        Import data from blobs to Azure Redis Cache.
    
    
    SYNTAX
        Import-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Files <String[]> [-Format <String>] [-Force]
        [-PassThru] [<CommonParameters>]
    
    
    DESCRIPTION
        The Import-AzureRmRedisCache cmdlet imports data from the specified blobs into Azure Redis Cache.
    
    
    PARAMETERS
        -Name <String>
            The name of the cache.
    
        -ResourceGroupName <String>
            The name of the resource group that contains the cache.
    
        -Files <String[]>
            SAS urls of blobs whose content should be imported into the cache.
    
        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.
    
        -Force
            When the Force parameter is provided, import will be performed without any confirmation prompts.
    
        -PassThru
            By default Import-AzureRmRedisCache imports data in cache and does not return any value. If the PassThru
            parameter is provided then Import-AzureRmRedisCache returns a boolean value indicating the success of the
            operation.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).
    

O seguinte comando importa dados a partir do blob especificado pelo uri SA para Azure Redis Cache.


    PS C:\>Import-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Files @("https://mystorageaccount.blob.core.windows.net/mycontainername/blobname?sv=2015-04-05&sr=b&sig=caIwutG2uDa0NZ8mjdNJdgOY8%2F8mhwRuGNdICU%2B0pI4%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwd") -Force

## <a name="to-export-a-redis-cache"></a>Para exportar uma cache Redis

Pode exportar dados a partir de uma instância do Azure Redis Cache utilizando o `Export-AzureRmRedisCache` cmdlet.

>[AZURE.IMPORTANT] Importar/exportar apenas está disponível para caches [camada premium](cache-premium-tier-intro.md) . Para mais informações sobre importar/exportar, consulte o artigo [Importar e exportar os dados na Cache de Redis Azure](cache-how-to-import-export-data.md).

Para ver uma lista dos parâmetros disponíveis e as respectivas descrições para `Export-AzureRmRedisCache`, execute o seguinte comando.

    PS C:\> Get-Help Export-AzureRmRedisCache -detailed
    
    NAME
        Export-AzureRmRedisCache
    
    SYNOPSIS
        Exports data from Azure Redis Cache to a specified container.
    
    
    SYNTAX
        Export-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Prefix <String> -Container <String> [-Format
        <String>] [-PassThru] [<CommonParameters>]
    
    
    DESCRIPTION
        The Export-AzureRmRedisCache cmdlet exports data from Azure Redis Cache to a specified container.
    
    
    PARAMETERS
        -Name <String>
            The name of the cache.
    
        -ResourceGroupName <String>
            The name of the resource group that contains the cache.
    
        -Prefix <String>
            Prefix to use for blob names.
    
        -Container <String>
            SAS url of container where data should be exported.
    
        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.
    
        -PassThru
            By default Export-AzureRmRedisCache does not return any value. If the PassThru parameter is provided
            then Export-AzureRmRedisCache returns a boolean value indicating the success of the operation.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).


O seguinte comando exporta dados a partir de uma instância do Azure Redis Cache para o contentor especificado pelo uri SA.


        PS C:\>Export-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Prefix "blobprefix"
        -Container "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2015-04-05&sr=c&sig=HezZtBZ3DURmEGDduauE7
        pvETY4kqlPI8JCNa8ATmaw%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwdl"

## <a name="to-reboot-a-redis-cache"></a>Para reiniciar uma cache Redis

Pode reiniciar o seu instância do Azure Redis Cache utilizando o `Reset-AzureRmRedisCache` cmdlet.

>[AZURE.IMPORTANT] Reinicie só está disponível para caches [camada premium](cache-premium-tier-intro.md) . Para mais informações sobre a cache reiniciar o computador, consulte o artigo [Administração de Cache - reiniciar o computador](cache-administration.md#reboot).

Para ver uma lista dos parâmetros disponíveis e as respectivas descrições para `Reset-AzureRmRedisCache`, execute o seguinte comando.

    PS C:\> Get-Help Reset-AzureRmRedisCache -detailed
    
    NAME
        Reset-AzureRmRedisCache
    
    SYNOPSIS
        Reboot specified node(s) of an Azure Redis Cache instance.
    
    
    SYNTAX
        Reset-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -RebootType <String> [-ShardId <Integer>]
        [-Force] [-PassThru] [<CommonParameters>]
    
    
    DESCRIPTION
        The Reset-AzureRmRedisCache cmdlet reboots the specified node(s) of an Azure Redis Cache instance.
    
    
    PARAMETERS
        -Name <String>
            The name of the cache.
    
        -ResourceGroupName <String>
            The name of the resource group that contains the cache.
    
        -RebootType <String>
            Which node to reboot. Possible values are "PrimaryNode", "SecondaryNode", "AllNodes".
    
        -ShardId <Integer>
            Which shard to reboot when rebooting a premium cache with clustering enabled.
    
        -Force
            When the Force parameter is provided, reset will be performed without any confirmation prompts.
    
        -PassThru
            By default Reset-AzureRmRedisCache does not return any value. If the PassThru parameter is provided
            then Reset-AzureRmRedisCache returns a boolean value indicating the success of the operation.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).
    

O seguinte comando reinicia ambos os nós da cache especificado.

    
        PS C:\>Reset-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -RebootType "AllNodes"
        -Force
    

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre como utilizar o Windows PowerShell com o Azure, consulte os seguintes recursos:

- [Documentação do cmdlet Redis Cache Azure no MSDN](https://msdn.microsoft.com/library/azure/mt634513.aspx)
- [Cmdlets de Gestor de recursos do Azure](http://go.microsoft.com/fwlink/?LinkID=394765): Saiba como utilizar os cmdlets no módulo AzureResourceManager.
- [Grupos de utilizar recurso para gerir os seus recursos Azure](../resource-group-template-deploy-portal.md): Saiba como criar e gerir grupos de recursos no portal do Azure.
- [Blogue do Azure](http://blogs.msdn.com/windowsazure): Saiba mais sobre novas funcionalidades no Azure.
- [Blogue do Windows PowerShell](http://blogs.msdn.com/powershell): Saiba mais sobre novas funcionalidades do Windows PowerShell.
- ["Olhe, Scripting boneco!" Blogue](http://blogs.technet.com/b/heyscriptingguy/): obter reais sugestões e truques na Comunidade do Windows PowerShell do.
