<properties 
    pageTitle="Como criar e gerir a Cache de Redis de Azure utilizando a Interface da linha de comandos do Azure (Azure CLI) | Microsoft Azure" 
    description="Saiba como instalar o clip do Azure em qualquer plataforma, como utilizá-lo para ligar à sua conta Azure e como criar e gerir uma cache Redis a partir do clip Azure." 
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
    ms.date="09/15/2016" 
    ms.author="sdanie"/>

# <a name="how-to-create-and-manage-azure-redis-cache-using-the-azure-command-line-interface-azure-cli"></a>Como criar e gerir a Cache de Redis de Azure utilizando a Interface da linha de comandos do Azure (Azure CLI)

> [AZURE.SELECTOR]
- [PowerShell](cache-howto-manage-redis-cache-powershell.md)
- [Clip Azure](cache-manage-cli.md)

O clip do Azure é uma excelente forma de gerir a sua infraestrutura Azure a partir de qualquer plataforma. Este artigo mostra-lhe como criar e gerir o seu instâncias de Cache de Redis Azure utilizando o clip do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para criar e gerir instâncias de Cache de Redis Azure utilizando o clip do Azure, tem de concluir os passos seguintes.

-   Tem de ter uma conta Azure. Se não tiver uma, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.
-   [Instalar o clip Azure](../xplat-cli-install.md).
-   Ligar a sua instalação do Azure clip com uma conta pessoal do Azure ou com uma trabalho ou escola conta Azure e iniciar sessão a partir da linha de clip Azure utilizando o `azure login` comando. Para compreender as diferenças e selecionar, consulte o artigo [ligar a uma subscrição Azure a partir da linha de comandos Interface o Azure (Azure CLI)](../xplat-cli-connect.md).
-   Antes de executar qualquer um dos seguintes comandos, mudar o clip do Azure para o modo de Gestor de recursos ao executar o `azure config mode arm` comando. Para mais informações, consulte o artigo [configurar o modo de Gestor de recursos do Azure](../xplat-cli-azure-resource-manager.md#set-the-azure-resource-manager-mode).

## <a name="redis-cache-properties"></a>Redis propriedades de Cache

As seguintes propriedades são utilizadas quando cria e atualizar instâncias de Redis Cache.

| Propriedade            | Mudar                                  | Descrição                                                                                                                                                                                                                                          |
|---------------------|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| nome                | -n, – o nome                              | Nome da Redis Cache.                                                                                                                                                                                                                             |
| grupo de recursos      | -g, – grupo de recursos                    | Nome do grupo de recursos.                                                                                                                                                                                                                          |
| localização            | -l, – localização                          | Localização para criar a cache.                                                                                                                                                                                                                            |
| tamanho                | -z, – tamanho                              | Tamanho da Redis Cache. Os valores válidos: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, E4]                                                                                                                                                                  |
| SKU                 | -x, – sku                               | Redis SKU. Deve ser um dos: [básicas, padrão, Premium]                                                                                                                                                                                             |
| EnableNonSslPort    | -"e", – ativar-não--porta de ssl               | Propriedade EnableNonSslPort da Redis Cache. Se pretender ativar a porta de SSL não para a sua cache, adicione este sinalizador                                                                                                                                    |
| Redis configuração | -c, – redis configuração               | Redis configuração. Introduza uma cadeia JSON formatado de chaves de configuração e valores aqui. Formato de: "{" ":""," ":" "}"                                                                                                                                     |
| Redis configuração | -f, - ficheiro de configuração de redis          | Redis configuração. Introduza o caminho de um ficheiro que contém valores aqui e chaves de configuração. Formato para a entrada de ficheiro: {"": "","": ""}                                                                                                                |
| Contagem de shard         | -r, – shard contagem                       | Número de Shards para criar uma Cache de Cluster Premium com clusters.                                                                                                                                                                               |
| Rede virtual     | + v, – rede virtual                   | Quando a alojar o seu cache num VNET, especifica o exato processador ID do recurso da rede virtual para implementar a cache do redis na. Formato de exemplo: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| tipo de chave            | -t, – tipo de chave                          | Tipo de chave para renovar. Os valores válidos: [primário, secundário]                                                                                                                                                                                             |
| StaticIP            | -p, – ip estático < ip estático >             | Quando a alojar o seu cache num VNET, especifica um endereço IP exclusivo na sub-rede para a cache. Se não for fornecido, um é escolhido por si da sub-rede.                                                                                                |
| Sub-rede              | t, – sub-rede<subnet>                    | Quando a alojar o seu cache num VNET, especifica o nome da sub-rede na qual pretende implementar a cache.                                                                                                                                                    |
| VirtualNetwork      | + v, – rede virtual <-rede virtual > | Quando a alojar o seu cache num VNET, especifica o exato processador ID do recurso da rede virtual para implementar a cache do redis na. Formato de exemplo: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Subscrição        | -s, – subscrição                      | O identificador de subscrição.                                                                                                                                                                                                                         |

## <a name="see-all-redis-cache-commands"></a>Ver todos os comandos de Redis Cache

Para ver todos os comandos da Redis Cache e os respetivos parâmetros, utilize o `azure rediscache -h` comando.

    C:\>azure rediscache -h
    help:    Commands to manage your Azure Redis Cache(s)
    help:
    help:    Create a Redis Cache
    help:      rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Delete an existing Redis Cache
    help:      rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    List all Redis Caches within your Subscription or Resource Group
    help:      rediscache list [options]
    help:
    help:    Show properties of an existing Redis Cache
    help:      rediscache show [--name <name> --resource-group <resource-group>]
    help:
    help:    Change settings of an existing Redis Cache
    help:      rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Renew the authentication key for an existing Redis Cache
    help:      rediscache renew-key [--name <name> --resource-group <resource-group> ]
    help:
    help:    Lists Primary and Secondary key of an existing Redis Cache
    help:      rediscache list-keys [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help  output usage information
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="create-a-redis-cache"></a>Criar uma Cache Redis

Para criar uma Cache Redis, utilize o seguinte comando:

    azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]

Para mais informações sobre este comando, execute o `azure rediscache create -h` comando.

    C:\>azure rediscache create -h
    help:    Create a Redis Cache
    help:
    help:    Usage: rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -l, --location <location>                                Location to create cache.
    help:      -z, --size <size>                                        Size of the Redis Cache. Valid values: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4]
    help:      -x, --sku <sku>                                          Redis SKU. Should be one of : [Basic, Standard, Premium]
    help:      -e, --enable-non-ssl-port                                EnableNonSslPort property of the Redis Cache. Add this flag if you want to enable the Non SSL Port for your cache
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here. Format:"{"<key1>":"<value1>","<key2>":"<value2>"}"
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here. Format for the file entry: {"<key1>":"<value1>","<key2>":"<value2>"}
    help:      -r, --shard-count <shard-count>                          Number of Shards to create on a Premium Cluster Cache
    help:      -v, --virtual-network <virtual-network>                  The exact ARM resource ID of the virtual network to deploy the redis cache in. Example format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1
    help:      -t, --subnet <subnet>                                    Required when deploying a redis cache inside an existing Azure Virtual Network
    help:      -p, --static-ip <static-ip>                              Required when deploying a redis cache inside an existing Azure Virtual Network
    help:      -s, --subscription <id>                                  the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="delete-an-existing-redis-cache"></a>Eliminar uma Cache Redis existente

Para eliminar uma Cache Redis, utilize o seguinte comando:

    azure rediscache delete [--name <name> --resource-group <resource-group> ]

Para mais informações sobre este comando, execute o `azure rediscache delete -h` comando.

    C:\>azure rediscache delete -h
    help:    Delete an existing Redis Cache
    help:
    help:    Usage: rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which the cache exists
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-all-redis-caches-within-your-subscription-or-resource-group"></a>Lista todas as Caches Redis dentro da sua subscrição ou grupo de recursos

Para listar todas as Caches Redis dentro da sua subscrição ou grupo de recursos, utilize o seguinte comando:

    azure rediscache list [options]

Para mais informações sobre este comando, execute o `azure rediscache list -h` comando.

    C:\>azure rediscache list -h
    help:    List all Redis Caches within your Subscription or Resource Group
    help:
    help:    Usage: rediscache list [options]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -g, --resource-group <resource-group>  Name of the Resource Group
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="show-properties-of-an-existing-redis-cache"></a>Mostrar as propriedades de uma Cache Redis existente

Para apresentar as propriedades de uma Cache Redis existente, utilize o seguinte comando:

    azure rediscache show [--name <name> --resource-group <resource-group>]

Para mais informações sobre este comando, execute o `azure rediscache show -h` comando.

    C:\>azure rediscache show -h
    help:    Show properties of an existing Redis Cache
    help:
    help:    Usage: rediscache show [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

<a name="scale"></a>
## <a name="change-settings-of-an-existing-redis-cache"></a>Alterar definições de uma Cache Redis existente

Para alterar as definições de uma Cache Redis existente, utilize o seguinte comando:

    azure rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]

Para mais informações sobre este comando, execute o `azure rediscache set -h` comando.

    C:\>azure rediscache set -h
    help:    Change settings of an existing Redis Cache
    help:
    help:    Usage: rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here.
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here.
    help:      -s, --subscription <subscription>                        the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="renew-the-authentication-key-for-an-existing-redis-cache"></a>Renovar a chave de autenticação para uma Cache Redis existente

Para renovar a chave de autenticação para uma Cache Redis existente, utilize o seguinte comando:

    azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]

Especificar `Primary` ou `Secondary` para `key-type`.

Para mais informações sobre este comando, execute o `azure rediscache renew-key -h` comando.

    C:\>azure rediscache renew-key -h
    help:    Renew the authentication key for an existing Redis Cache
    help:
    help:    Usage: rediscache renew-key [--name <name> --resource-group <resource-group> ]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which cache exists
    help:      -t, --key-type <key-type>              type of key to renew. Valid values are: 'Primary', 'Secondary'.
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-primary-and-secondary-keys-of-an-existing-redis-cache"></a>Teclas de lista principal e secundário de uma Cache Redis existente

Para chaves primárias e secundário lista de uma Cache Redis existente, utilize o seguinte comando:

    azure rediscache list-keys [--name <name> --resource-group <resource-group>]

Para mais informações sobre este comando, execute o `azure rediscache list-keys -h` comando.

    C:\>azure rediscache list-keys -h
    help:    Lists Primary and Secondary key of an existing Redis Cache
    help:
    help:    Usage: rediscache list-keys [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which Cache exists
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)
