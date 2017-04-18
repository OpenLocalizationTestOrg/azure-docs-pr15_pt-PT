<properties 
    pageTitle="Utilizar a biblioteca do cliente da base de dados flexível com Dapper | Microsoft Azure" 
    description="Utilizar a biblioteca do cliente da base de dados flexível com Dapper." 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="torsteng"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="torsteng"/>

# <a name="using-elastic-database-client-library-with-dapper"></a>Utilizar a biblioteca do cliente da base de dados flexível com Dapper 

Este documento é para programadores que dependem Dapper a criação de aplicações, mas também pretende Aproveite a [ferramenta de base de dados flexível](sql-database-elastic-scale-introduction.md) para criar aplicações que implementam sharding para escala-out camada os respetivos dados.  Este documento ilustra a alterações em aplicações baseadas em Dapper que são necessárias para integrar com ferramentas de base de dados flexível. Os nossos foco está no compor a gestão de shard flexível da base de dados e os dados dependentes a encaminhar com Dapper. 

**Código de exemplo**: [Ferramentas de base de dados flexível para Azure base de dados SQL - integração Dapper](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-e19fc77f).
 
Integrar o **Dapper** e **DapperExtensions** a biblioteca do cliente da base de dados flexível para a base de dados do SQL Azure é fácil. As aplicações poderá utilizar dependentes encaminhamento alterando a criação e de abertura de novos [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) objetos de dados para a chamada [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) a partir da [biblioteca de cliente](http://msdn.microsoft.com/library/azure/dn765902.aspx). Isto limita a alterações na sua aplicação para apenas onde novas ligações são criadas e abertas. 

## <a name="dapper-overview"></a>Descrição geral dapper
**Dapper** é um objeto relacionais mapeamento de pontos. Mapas de objetos .NET a partir da sua aplicação de uma base de dados relacional (e vice-versa). A primeira parte do código de exemplo ilustra como pode integrar a biblioteca do cliente da base de dados flexível com aplicações baseadas em Dapper. A segunda parte do código de exemplo ilustra como integrar o ao utilizar Dapper e DapperExtensions.  

A funcionalidade de mapeamento de pontos do Dapper fornece métodos de extensão ligações de base de dados que simplificam a evoluir declarações de T-SQL para execução ou consultar a base de dados. Por exemplo, Dapper torna mais fácil para mapear entre os objetos .NET e os parâmetros de instruções SQL para chamadas de **Executar** ou consumir os resultados das suas consultas SQL para objectos de .NET utilizando chamadas de **consulta** a partir do Dapper. 

Ao utilizar DapperExtensions, já não tem de fornecer as instruções SQL. Métodos de extensões como **GetList** ou **Inserir** através da ligação de base de dados crie as instruções SQL nos bastidores.
 
Outra vantagem de Dapper e também DapperExtensions é que a aplicação controla a criação de ligação de base de dados. Isto ajuda a interagir com a biblioteca do cliente da base de dados flexível que corretor ligações com base no mapeamento das shardlets a bases de dados de base de dados.

Para obter os conjuntos de Dapper, consulte o artigo [Dapper ponto líquido](http://www.nuget.org/packages/Dapper/). Para as extensões de Dapper, consulte o artigo [DapperExtensions](http://www.nuget.org/packages/DapperExtensions).

## <a name="a-quick-look-at-the-elastic-database-client-library"></a>Um breve olhar sobre a biblioteca do cliente da base de dados flexível

Com a biblioteca do cliente da base de dados flexível, definir a partições dos seus dados de aplicação denominados *shardlets* , mapeá-los para bases de dados e identificá-las por *chaves sharding*. Pode ter quantas bases de dados à medida que precisa e distribuir a sua shardlets por estas bases de dados. O mapeamento de valores de chave sharding para as bases de dados é armazenado por um mapa de shard fornecido pela APIs da biblioteca. Esta funcionalidade é denominada **shard mapear gestão**. O mapa shard também serve o corretor de ligações de base de dados para pedidos de conter uma chave de sharding. Esta funcionalidade é referida como **dependentes encaminhamento de dados**.

![Mapas de shard e encaminhamento dependente de dados][1]

O Gestor de mapa shard protege os utilizadores de vistas inconsistentes shardlet dados que podem ocorrer ao operações de gestão de shardlet em simultâneo são passa nas bases de dados. Para fazê-lo, os mapas shard broker as ligações de base de dados para uma aplicação criadas com a biblioteca. Quando operações de gestão de shard podem afetar o shardlet, esta opção permite-a funcionalidade de mapa shard para eliminar automaticamente uma ligação de base de dados. 

Em vez de utilizar a forma tradicional de criar ligações para Dapper, é necessário utilizar o [método de OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn824099.aspx). Isto garante que todos os a validação ocorre o mais e ligações são geridas correctamente quando move quaisquer dados entre shards.

### <a name="requirements-for-dapper-integration"></a>Requisitos para a integração Dapper

Quando trabalha com a biblioteca do cliente flexível da base de dados e as APIs Dapper, podemos pretende manter as seguintes propriedades:

* **Scaleout**: queremos adicionar ou remover bases de dados a partir da camada de dados da aplicação sharded conforme necessário para os pedidos de capacidade da aplicação. 

-    **Consistência**: uma vez que a nossa aplicação está dimensionada saída utilizando sharding, precisamos de executar dependentes encaminhamento de dados. Recomendamos que utilize as dados dependentes capacidades de encaminhamento da biblioteca para fazê-lo. Em particular, Queremos manter a validação e consistência garantias fornecidas pelo ligações que controlada através do Gestor de mapa de shard para evitar danos ou resultados da consulta errado. Este procedimento garante que as ligações para um determinado shardlet são rejeitadas ou paradas se (por exemplo) a shardlet atualmente for movida para uma shard diferentes utilizando APIs dividido/em série.

-    **Objeto de mapeamento**: Queremos manter a conveniência dos mapeamentos fornecida pela Dapper para traduzir entre classes na aplicação e as estruturas de base de dados subjacentes. 

A secção seguinte fornece orientações para estes requisitos para aplicações com base em **Dapper** e **DapperExtensions**.

## <a name="technical-guidance"></a>Orientação técnica
### <a name="data-dependent-routing-with-dapper"></a>Dependente de encaminhamento com Dapper dos dados 

Com Dapper, a aplicação está normalmente responsável pela criação e abrir ligações à base de dados subjacente. Dada um tipo de T pela aplicação, Dapper devolve resultados da consulta como coleções .NET tipo t Dapper executa o mapeamento das linhas de resultado T-SQL para os objetos do tipo T. Da mesma forma, Dapper mapas objetos .NET para valores de SQL ou parâmetros para instruções de language (LMG) de manipulação de dados. Dapper oferece esta funcionalidade através de métodos de extensão no objeto [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) normal a partir das bibliotecas ADO .NET SQL Client. A ligação de SQL devolvida pelas APIs da escala flexível para DDR também sejam regulares [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) objetos. Esta opção permite--na diretamente utilizar extensões Dapper sobre o tipo devolvido pela DDR API a biblioteca de cliente, como também é uma ligação de SQL Client simples.

Estas observações torná-lo simples utilizar ligações controladas pela biblioteca do cliente da base de dados flexível para Dapper.

Este exemplo de código (do exemplo acompanhamento) ilustra a abordagem onde a chave de sharding é fornecida pela aplicação para a biblioteca para broker a ligação para o shard à direita.   

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                     key: tenantId1, 
                     connectionString: connStrBldr.ConnectionString, 
                     options: ConnectionOptions.Validate))
    {
        var blog = new Blog { Name = name };
        sqlconn.Execute(@"
                      INSERT INTO
                            Blog (Name)
                            VALUES (@name)", new { name = blog.Name }
                        );
    }

A chamada para [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) API substitui a criação de predefinida e a abertura de uma ligação de cliente de SQL. A chamada [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) assume os argumentos que são necessários para o encaminhamento dependente de dados: 

-    O mapa de shard para aceder às interfaces de encaminhamento dependentes dados
-    A tecla de sharding para identificar o shardlet
-    As credenciais (nome de utilizador e palavra-passe) para ligar para o shard

O objeto de mapa shard cria uma ligação para o shard que detém shardlet na chave sharding determinado. O cliente de base de dados flexível APIs também marcar a ligação para implementar o respetivas garantias consistência. Uma vez que a chamada para [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) devolve um objeto de ligação de SQL Client normal, a chamada subsequente para o método de extensão **Execute** a partir do Dapper segue exercício prático Dapper padrão.

Consultas funcionam muito da mesma forma – abre a ligação utilizar [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) do cliente a API pela primeira vez. Em seguida, utilize os métodos de extensão Dapper normal para mapear os resultados da consulta SQL para objetos .NET:

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId1, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate ))
    {    
           // Display all Blogs for tenant 1
           IEnumerable<Blog> result = sqlconn.Query<Blog>(@"
                                SELECT * 
                                FROM Blog
                                ORDER BY Name");

           Console.WriteLine("All blogs for tenant id {0}:", tenantId1);
           foreach (var item in result)
           {
                Console.WriteLine(item.Name);
            }
    }

Tenha em atenção que a **utilizar o** bloco com a ligação DDR âmbitos de todas as operações de base de dados no bloco de um shard onde é mantido tenantId1. A consulta devolve apenas blogues armazenados na shard atual, mas não aqueles armazenado no quaisquer outros shards. 

## <a name="data-dependent-routing-with-dapper-and-dapperextensions"></a>Dependente de encaminhamento com Dapper e DapperExtensions dos dados

Dapper vem com um ecossistema de extensões adicionais que podem fornecer mais conveniência e produção da base de dados quando desenvolver aplicações de base de dados. DapperExtensions é um exemplo. 

Utilizar DapperExtensions na sua aplicação não irá alterar como ligações de base de dados são criadas e geridas. Cabe ainda a aplicação para abrir as ligações e objetos de ligação de SQL Client regulares são esperados através dos métodos de extensão. Vamos pode depender de [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) conforme descrito acima. Como mostram os exemplos seguintes do código, a única alteração é que já não temos que escrever as instruções SQL de T:

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate))
    {
           var blog = new Blog { Name = name2 };
           sqlconn.Insert(blog);
    }

E aqui está o código de exemplo para a consulta: 

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate))
    {
           // Display all Blogs for tenant 2
           IEnumerable<Blog> result = sqlconn.GetList<Blog>();
           Console.WriteLine("All blogs for tenant id {0}:", tenantId2);
           foreach (var item in result)
           {
               Console.WriteLine(item.Name);
           }
    }

### <a name="handling-transient-faults"></a>Processamento breves falhas

A equipa do Microsoft Patterns & práticas publicado o [Bloco de aplicações processamento breves falhas](http://msdn.microsoft.com/library/hh680934.aspx) para ajudar os programadores de aplicação mitigar condições breves falhas comuns que se tenha deparado ou quando a ser executado na nuvem. Para obter mais informações, consulte o artigo [Perseverance, Triumphs secreta todos os: utilizar o bloco de aplicação de processamento de falhas breves](http://msdn.microsoft.com/library/dn440719.aspx).

O código de exemplo baseia-se na biblioteca de falhas breves para proteger contra falhas breves. 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
    {
       using (SqlConnection sqlconn = 
          shardingLayer.ShardMap.OpenConnectionForKey(tenantId2, connStrBldr.ConnectionString, ConnectionOptions.Validate))
          {
              var blog = new Blog { Name = name2 };
              sqlconn.Insert(blog);
          }
    });

**SqlDatabaseUtils.SqlRetryPolicy** no código de acima é definido como um **SqlDatabaseTransientErrorDetectionStrategy** com uma contagem de repetição de 10 e tempo de espera de 5 segundos entre tentativas. Se estiver a utilizar as transações, certifique-se de que o âmbito de repetir volta para o início da transação no caso de uma falha de breves.

## <a name="limitations"></a>Limitações

Abordagens descritas neste documento implicam algumas limitações:

* O código de exemplo para este documento não demonstram como gerir o esquema em shards.
* Dada um pedido, podemos partem do princípio de que todos os seu processamento de base de dados encontra-se uma única shard como identificado pela sharding chave fornecida pelo pedido. No entanto, este pressuposto não sempre mantenha, por exemplo, quando não é possível disponibilizar uma chave de sharding. Para resolver este problema, a biblioteca do cliente da base de dados flexível inclui a [classe de MultiShardQuery](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardexception.aspx). A classe implementa produção uma ligação para consultar através de vários shards. Utilizar MultiShardQuery em conjunto com Dapper é para além do âmbito deste documento.

## <a name="conclusion"></a>Conclusão

Aplicações utilizando Dapper e DapperExtensions beneficiar facilmente as ferramentas de base de dados flexível para a base de dados do SQL Azure. Através dos passos descritos neste documento, nessas aplicações poderá utilizar capacidade a ferramenta para dependentes encaminhamento alterando a criação e de abertura de novos [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) objetos de dados para a chamada [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) da biblioteca de cliente da base de dados flexível. Este procedimento limita as alterações à aplicação necessárias para os locais onde novas ligações são criadas e abertas. 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-working-with-dapper/dapperimage1.png
 