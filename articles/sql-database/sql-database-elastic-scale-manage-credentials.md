<properties 
    pageTitle="Gerir credenciais na biblioteca de cliente de base de dados flexível | Microsoft Azure" 
    description="Como configurar o nível adequado de credenciais de administrador para só de leitura, para as aplicações de base de dados flexível" 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="ddove" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="ddove"/>

# <a name="credentials-used-to-access-the-elastic-database-client-library"></a>Credenciais utilizadas para aceder à biblioteca de cliente da base de dados flexível

A [biblioteca de base de dados flexível do cliente](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) utiliza três tipos diferentes de credenciais para aceder ao [Gestor de mapa shard](sql-database-elastic-scale-shard-map-management.md). Consoante a necessidade de, utilize a credencial com o nível de acesso possível mais baixo.

* **Credenciais de gestão de**: para criar ou manipular um Gestor de mapa shard. (Consulte [Glossário](sql-database-elastic-scale-glossary.md).) 
* **Credenciais de acesso**: para aceder a um Gestor de mapa shard existente para obter informações sobre shards.
* **Credenciais de ligação**: para ligar à shards. 

Consulte também [Gerir bases de dados e inícios de sessão de base de dados do SQL Azure](sql-database-manage-logins.md). 
 
## <a name="about-management-credentials"></a>Informações sobre credenciais de gestão

Credenciais de gestão de são utilizadas para criar um objecto [**ShardMapManager**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) para as aplicações que manipular shard mapas. (Por exemplo, consulte o artigo [Adicionar um shard utilizando ferramentas de base de dados flexível](sql-database-elastic-scale-add-a-shard.md) e [Encaminhamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md)) O utilizador da biblioteca de cliente de escala flexível cria os utilizadores SQL e inícios de sessão SQL e torna-se de que cada é concedida as permissões de leitura/escrita na base de dados de mapa global shard e todos os shard bases de dados, assim. Estas credenciais são utilizadas para manter o mapa global shard e os mapas local shard quando as alterações ao mapa de shard são executadas. Por exemplo, utilizar as credenciais de gestão para criar o objeto de Gestor de mapa shard (utilizar [**GetSqlShardMapManager**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx): 

    // Obtain a shard map manager. 
    ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
            smmAdminConnectionString, 
            ShardMapManagerLoadPolicy.Lazy 
    ); 

A variável **smmAdminConnectionString** é uma cadeia de ligação que contém as credenciais de gestão. ID de utilizador e palavra-passe oferece um acesso de leitura/escrita para shard mapa da base de dados e shards individuais. A cadeia de ligação de gestão também inclui o nome de servidor e o nome da base de dados para identificar a base de dados de mapa global shard. Eis uma cadeia de ligação típicas para esse efeito:

     "Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;” 

Não utilize valores em forma de "username@server"—instead apenas utilize o valor de "nome de utilizador".  Isto ocorre porque as credenciais tem de trabalhar contra a base de dados do shard mapa manager e o shards individuais, que podem ser em servidores diferentes.

## <a name="access-credentials"></a>Credenciais de acesso
  
Ao criar um shard Gestor de mapa numa aplicação que não administrar shard mapas, utilize as credenciais que têm permissões só de leitura no mapa global shard. As informações obtidas a partir do mapa global shard nestas credenciais são utilizadas para o [Encaminhamento de dependentes de dados](sql-database-elastic-scale-data-dependent-routing.md) e para preencher a cache de mapa shard no cliente. As credenciais são fornecidas através do mesmo padrão de chamada para **GetSqlShardMapManager** conforme mostrado acima: 

    // Obtain shard map manager. 
    ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
            smmReadOnlyConnectionString, 
            ShardMapManagerLoadPolicy.Lazy
    );  

Tenha em atenção a utilização do **smmReadOnlyConnectionString** para refletir a utilização de credenciais diferentes para este acesso em nome de utilizadores **que não sejam administradores** : estas credenciais não devem fornecer as permissões de escrita no mapa global shard. 

## <a name="connection-credentials"></a>Credenciais de ligação 

Credenciais adicionais são necessários quando utilizando o método de [**OpenConnectionForKey**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx) para aceder a um shard associado com uma chave de sharding. Estas credenciais tem de fornecer as permissões de acesso só de leitura com as tabelas de mapa local shard que reside na shard. Isto é necessário para executar a validação de ligação para o encaminhamento de dependentes de dados na shard. Este fragmento de código permite que o acesso a dados no contexto de encaminhamento dependente de dados: 
 
    using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>( 
    targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate)) 

Neste exemplo, **smmUserConnectionString** mantém a cadeia de ligação para as credenciais de utilizador. Para DB do SQL Azure, eis uma cadeia de ligação típica para as credenciais de utilizador: 

    "User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  

Tal como acontece com as credenciais de administrador não valores em forma de "username@server". Em alternativa, utilize apenas "nome de utilizador".  Tenha em atenção que a cadeia de ligação não contém um nome de servidor e o nome da base de dados. Se ao facto da chamada **OpenConnectionForKey** reencaminhará automaticamente a ligação para o shard correta com base na chave. Por conseguinte, o nome da base de dados e o nome de servidor não são fornecidos. 

## <a name="see-also"></a>Consulte também
[Gerir bases de dados e inícios de sessão de base de dados do SQL Azure](sql-database-manage-logins.md)

[Proteger a sua base de dados SQL](sql-database-security.md)

[Começar a trabalhar com tarefas de base de dados flexível](sql-database-elastic-jobs-getting-started.md)

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 