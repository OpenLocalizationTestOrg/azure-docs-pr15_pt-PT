<properties
   pageTitle="Como utilizar o Azure Redis Cache com Java | Microsoft Azure"
    description="Introdução ao Azure Redis Cache utilizando Java"
    services="redis-cache"
    documentationCenter=""
    authors="steved0x"
    manager="douge"
    editor=""/>

<tags
    ms.service="cache"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="08/24/2016"
    ms.author="sdanie"/>

# <a name="how-to-use-azure-redis-cache-with-java"></a>Como utilizar o Azure Redis Cache com Java

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [NODE.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Azure Redis Cache dá-lhe acesso às dedicada Redis cache, gerida pela Microsoft. A cache está acessível a partir de qualquer aplicação do Microsoft Azure.

Este tópico mostra-lhe como começar com a Cache de Redis Azure utilizando Java.

## <a name="prerequisites"></a>Pré-requisitos

[Jedis](https://github.com/xetorthio/jedis) - cliente Java para Redis

Neste tutorial utiliza Jedis, mas pode utilizar qualquer cliente Java listado no [http://redis.io/clients](http://redis.io/clients).

## <a name="create-a-redis-cache-on-azure"></a>Criar uma cache Redis no Azure

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>Obter as teclas de acesso e o nome do anfitrião

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-the-non-ssl-endpoint"></a>Ativar o ponto final que não sejam SSL

Alguns clientes Redis não suportar SSL e, por predefinição [porta não SSL está desactivada para novas instâncias do Azure Redis Cache](cache-configure.md#access-ports). No momento deste escrita, o cliente [Jedis](https://github.com/xetorthio/jedis) não suporta SSL. 

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]




## <a name="add-something-to-the-cache-and-retrieve-it"></a>Adicionar um elemento para a cache e recuperá-la

    package com.mycompany.app;
    import redis.clients.jedis.Jedis;
    import redis.clients.jedis.JedisShardInfo;

    /* Make sure you turn on non-SSL port in Azure Redis using the Configuration section in the Azure Portal */
    public class App
    {
      public static void main( String[] args )
      {
        /* In this line, replace <name> with your cache name: */
        JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.windows.net", 6379);
        shardInfo.setPassword("<key>"); /* Use your access key. */
        Jedis jedis = new Jedis(shardInfo);
        jedis.set("foo", "bar");
        String value = jedis.get("foo");
      }
    }


## <a name="next-steps"></a>Próximos passos

- [Activar diagnósticos de cache](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics) para que possa [Monitorizar](https://msdn.microsoft.com/library/azure/dn763945.aspx) o estado de funcionamento da cache.
- Leia oficial [Redis documentação](http://redis.io/documentation).

