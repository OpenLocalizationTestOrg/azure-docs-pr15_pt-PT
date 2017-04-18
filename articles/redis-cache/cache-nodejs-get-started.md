<properties
    pageTitle="Como utilizar o Azure Redis Cache com Node.js | Microsoft Azure"
    description="Introdução ao Azure Redis Cache utilizando Node.js e node_redis."
    services="redis-cache"
    documentationCenter=""
    authors="steved0x"
    manager="douge"
    editor="v-lincan"/>

<tags
    ms.service="cache"
    ms.devlang="nodejs"
    ms.topic="hero-article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="10/25/2016"
    ms.author="sdanie"/>

# <a name="how-to-use-azure-redis-cache-with-nodejs"></a>Como utilizar o Azure Redis Cache com Node.js

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [NODE.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Azure Redis Cache dá-lhe acesso a uma cache de Redis segura, dedicada, gerida pela Microsoft. A cache está acessível a partir de qualquer aplicação do Microsoft Azure.

Este tópico mostra-lhe como começar com a Cache de Redis Azure utilizando Node.js. Outro exemplo de utilização do Azure Redis Cache com Node.js, consulte o artigo [criar uma aplicação de Chat para Node.js com Socket.IO num Web site Azure](../app-service-web/web-sites-nodejs-chat-app-socketio.md).


## <a name="prerequisites"></a>Pré-requisitos

Instale [node_redis](https://github.com/mranney/node_redis):

    npm install redis

Neste tutorial, utiliza [node_redis](https://github.com/mranney/node_redis). Para obter exemplos da utilização de outros clientes Node.js, consulte a documentação individual para os clientes de Node.js listados no [Node.js Redis clientes](http://redis.io/clients#nodejs).

## <a name="create-a-redis-cache-on-azure"></a>Criar uma cache Redis no Azure

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>Obter as teclas de acesso e o nome do anfitrião

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="connect-to-the-cache-securely-using-ssl"></a>Ligar a cache de forma segura através de SSL

As compilações mais recentes do [node_redis](https://github.com/mranney/node_redis) fornecem suporte para estabelecer ligação ao Azure Redis Cache através de SSL. O exemplo seguinte mostra como ligar a Cache de Redis Azure utilizando o ponto final de SSL de 6380. Substituir `<name>` com o nome da cache e `<key>` com quer a chave primária ou secundária como descrito na secção anterior [obter as teclas de acesso e o nome do anfitrião](#retrieve-the-host-name-and-access-keys) .

     var redis = require("redis");
    
      // Add your cache name and access key.
    var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});


## <a name="add-something-to-the-cache-and-retrieve-it"></a>Adicionar um elemento para a cache e recuperá-la

O exemplo seguinte mostra como ligar a uma instância de Cache Redis do Azure e armazenar e obter um item da cache de. Para obter mais exemplos de utilização de Redis com o cliente de [node_redis](https://github.com/mranney/node_redis) , consulte o artigo [http://redis.js.org/](http://redis.js.org/).

     var redis = require("redis");
    
      // Add your cache name and access key.
    var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});
    
    client.set("key1", "value", function(err, reply) {
            console.log(reply);
        });
    
    client.get("key1",  function(err, reply) {
            console.log(reply);
        });

Resultado:

    OK
    value


## <a name="next-steps"></a>Próximos passos

- [Activar diagnósticos de cache](cache-how-to-monitor.md#enable-cache-diagnostics) para que possa [Monitorizar](cache-how-to-monitor.md) o estado de funcionamento da cache.
- Leia oficial [Redis documentação](http://redis.io/documentation).



