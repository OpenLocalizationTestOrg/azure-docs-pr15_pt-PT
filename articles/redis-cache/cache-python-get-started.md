<properties
    pageTitle="Como utilizar o Azure Redis Cache com Python | Microsoft Azure"
    description="Introdução ao Azure Redis Cache utilizando Python"
    services="redis-cache"
    documentationCenter=""
    authors="steved0x"
    manager="douge"
    editor="v-lincan"/>

<tags
    ms.service="cache"
    ms.devlang="python"
    ms.topic="hero-article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="08/16/2016"
    ms.author="sdanie"/>

# <a name="how-to-use-azure-redis-cache-with-python"></a>Como utilizar o Azure Redis Cache com Python

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [NODE.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Este tópico mostra-lhe como começar com a Cache de Redis Azure utilizando Python.


## <a name="prerequisites"></a>Pré-requisitos

Instale [redis copiar](https://github.com/andymccurdy/redis-py).


## <a name="create-a-redis-cache-on-azure"></a>Criar uma cache Redis no Azure

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>Obter as teclas de acesso e o nome do anfitrião

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-the-non-ssl-endpoint"></a>Ativar o ponto final que não sejam SSL

Alguns clientes Redis não suportar SSL e, por predefinição [porta não SSL está desactivada para novas instâncias do Azure Redis Cache](cache-configure.md#access-ports). No momento deste escrita, o cliente [redis copiar](https://github.com/andymccurdy/redis-py) não suporta SSL. 

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]


## <a name="add-something-to-the-cache-and-retrieve-it"></a>Adicionar um elemento para a cache e recuperá-la


    >>> import redis
    >>> r = redis.StrictRedis(host='<name>.redis.cache.windows.net',
          port=6380, db=0, password='<key>', ssl=True)
    >>> r.set('foo', 'bar')
    True
    >>> r.get('foo')
    b'bar'


Substituir `<name>` com o seu nome de cache e `key` com a sua chave de acesso.


<!--Image references-->
[1]: ./media/cache-python-get-started/redis-cache-new-cache-menu.png
[2]: ./media/cache-python-get-started/redis-cache-cache-create.png
