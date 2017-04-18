<properties 
    pageTitle="Escala flexível Azure SQL FAQ | Microsoft Azure" 
    description="Perguntas mais frequentes sobre a escala de flexível de base de dados do Azure SQL." 
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
    ms.date="05/03/2016" 
    ms.author="ddove"/>

# <a name="elastic-database-tools-faq"></a>Ferramentas de base de dados flexível perguntas mais frequentes 

#### <a name="if-i-have-a-single-tenant-per-shard-and-no-sharding-key-how-do-i-populate-the-sharding-key-for-the-schema-info"></a>Se tiver um inquilino único por shard e sem tecla sharding, como posso preencher a chave de sharding para as informações do esquema?

O objeto de informações do esquema só é utilizado para dividir cenários de impressão em série. Se uma aplicação for implicitamente único inquilino, não requer a ferramenta de divisão intercalar e, por conseguinte, não é necessário para preencher o objeto de informações de esquema.

#### <a name="ive-provisioned-a-database-and-i-already-have-a-shard-map-manager-how-do-i-register-this-new-database-as-a-shard"></a>Posso ter aprovisionado uma base de dados e que já tenho um Gestor de mapa Shard, como registar esta nova base de dados como uma shard?

Consulte o artigo **[Adicionar um shard para uma aplicação utilizando a biblioteca do cliente flexível da base de dados](sql-database-elastic-scale-add-a-shard.md)**. 

#### <a name="how-much-do-elastic-database-tools-cost"></a>Como ferramentas da base de dados flexível custo?

Utilizar a biblioteca do cliente flexível da base de dados não assumir quaisquer custos. Imputação dos custos apenas para as bases de dados do Azure SQL que utilizar para criar shards e o Gestor de mapa de Shard, bem como as funções web/trabalhador que aprovisionar para a ferramenta de divisão impressão em série.

#### <a name="why-are-my-credentials-not-working-when-i-add-a-shard-from-a-different-server"></a>Por que motivo as minhas credenciais não estão a funcionar quando adiciono um shard a partir de um servidor diferente?
Não utilize credenciais em forma de "utilizador ID=username@servername”, em vez disso, simplesmente utilizar" ID de utilizador = nome de utilizador ".  Além disso, certifique-se de que o início de sessão "nome de utilizador" tem permissões na shard.

#### <a name="do-i-need-to-create-a-shard-map-manager-and-populate-shards-every-time-i-start-my-applications"></a>É preciso criar um Gestor de mapa Shard e povoar shards sempre que iniciar o meu aplicações?

Não — a criação do Gestor de mapa Shard (por exemplo, **[ShardMapManagerFactory.CreateSqlShardMapManager](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager.aspx)**) é uma operação única.  A aplicação deve utilizar a chamada **[ShardMapManagerFactory.TryGetSqlShardMapManager()](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx)** momento de arranque de aplicação.  Não existem deve apenas um desse chamada por domínio da aplicação.

#### <a name="i-have-questions-about-using-elastic-database-tools-how-do-i-get-them-answered"></a>Tenho perguntas sobre como utilizar ferramentas de base de dados flexível, como posso obtê-lo a resposta? 

Fórum comunique-no [Fórum de base de dados do SQL Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted).

#### <a name="when-i-get-a-database-connection-using-a-sharding-key-i-can-still-query-data-for-other-sharding-keys-on-the-same-shard--is-this-by-design"></a>Quando recebo uma ligação de base de dados com uma chave de sharding, posso ainda pode consultar dados para outras chaves sharding no shard mesmo.  Este é por predefinição?

A escala APIs flexível dar-lhe uma ligação à base de dados correto para a sua chave de sharding, mas não fornecem sharding chave filtragem.  Adicione cláusulas **onde** a sua consulta para restringir o âmbito à chave sharding fornecido, se necessário.

#### <a name="can-i-use-a-different-azure-database-edition-for-each-shard-in-my-shard-set"></a>Posso utilizar uma edição de base de dados Azure diferente para cada shard no meu conjunto shard?

Sim, um shard é uma base de dados individual e, por conseguinte, um shard dever uma edição Premium enquanto outro ser uma Standard edition. Além disso, a edição de um shard pode dimensionar para cima ou para baixo várias vezes durante o tempo de vida da shard.

#### <a name="does-the-split-merge-tool-provision-or-delete-a-database-during-a-split-or-merge-operation"></a>Faz a disposição de ferramenta de intercalação de divisão (ou eliminar) uma base de dados durante uma operação de divisão ou em série? 

Não. Para **Dividir** operações, a base de dados de destino tem de existir previamente com o esquema apropriado e estar registado junto do Gestor de mapa Shard.  Para operações de **impressão em série** , tem de eliminar a shard a partir do Gestor de mapa shard e, em seguida, eliminar a base de dados.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 