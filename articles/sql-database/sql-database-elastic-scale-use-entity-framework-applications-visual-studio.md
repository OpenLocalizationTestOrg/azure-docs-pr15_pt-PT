<properties 
    pageTitle="Utilizar a biblioteca do cliente da base de dados flexível com entidade Framework | Microsoft Azure" 
    description="Utilizar a base de dados flexível cliente biblioteca entidade Framework para codificação de bases de dados" 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="torsteng" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="torsteng"/>

# <a name="elastic-database-client-library-with-entity-framework"></a>Flexível biblioteca de base de dados do cliente com entidade Framework 
 
Este documento mostra as alterações numa aplicação do quadro entidade que são necessários para integrar com as [Ferramentas de base de dados flexível](sql-database-elastic-scale-introduction.md). O foco estiver no [shard mapear gestão](sql-database-elastic-scale-shard-map-management.md) e [Encaminhamento dependentes de dados](sql-database-elastic-scale-data-dependent-routing.md) com a entidade Framework **Código primeira** abordagem a compor. O tutorial [Primeiro código – nova base de dados](http://msdn.microsoft.com/data/jj193542.aspx) para EF serve nosso exemplo em execução ao longo deste documento. O código de exemplo que acompanham este documento faz parte das ferramentas de base de dados flexível conjunto de amostras nos exemplos de código do Visual Studio.
  
## <a name="downloading-and-running-the-sample-code"></a>Transferir e executar o código de exemplo
Para transferir o código para este artigo:

* É necessário o Visual Studio 2012 ou posterior. 
* Inicie o Visual Studio. 
* No Visual Studio, selecione ficheiro -> novo projeto. 
* Na caixa de diálogo 'Novo projeto', navegue para os **Exemplos Online** para **Visual c#** e tipo "db flexível" na caixa de pesquisa no canto superior direito.
    
    ![Quadro de entidade e aplicação de exemplo de base de dados flexível][1] 

    Selecione o exemplo chamado **Flexível ferramentas DB para SQL Azure – entidade Framework integração**. Depois de aceitar a licença, a amostra for carregada. 

Para executar a amostra, terá de criar bases de dados vazias três numa base de dados do Azure SQL:

* Base de dados do shard Manager de mapa
* Base de dados shard 1
* Base de dados shard 2

Depois de ter criado estas bases de dados, preencha a ainda na **Program.cs** com o seu nome de servidor do Azure SQL DB, os nomes de base de dados e as suas credenciais para ligar para as bases de dados. Crie a solução no Visual Studio. Visual Studio irá transferir os NuGet pacotes necessários para a biblioteca de cliente de base de dados flexível entidade Framework e falhas breves processamento como parte do processo de compilação. Certifique-se de que restaurar NuGet pacotes está ativado para a sua solução. Pode ativar esta definição clicando no ficheiro solução no Explorador de solução do Visual Studio. 

## <a name="entity-framework-workflows"></a>Fluxos de trabalho do entidade Framework 

Os programadores de Framework entidade dependem um dos seguintes quatro fluxos de trabalho para criar aplicações e para garantir persistente para objectos de aplicação: 

* **Código primeiro (a nova base de dados)**: Programador de EF cria o modelo no código da aplicação e, em seguida, EF gera a base de dados a partir dos mesmos. 
* **Código primeiro (base de dados existente)**: O programador permite EF gerar o código da aplicação para o modelo a partir de uma base de dados existente.
* **Modelo primeiro**: O programador cria o modelo no estruturador de EF e, em seguida, EF cria a base de dados do modelo.
* **Primeira base de dados**: O programador utiliza EF ferramenta inferir o modelo a partir de uma base de dados existente. 

Todas as seguintes abordagens dependem a classe de DbContext transparente gerir ligações de base de dados e o esquema da base de dados para uma aplicação. À medida que vai discutimos mais detalhadamente mais tarde no documento, construtores diferentes na classe de base DbContext permitem para diferentes níveis de controlo sobre a criação da ligação, base de dados a criação de arranque do sistema e esquema. Desafios surgirem principalmente a partir do facto de que a gestão de ligação de base de dados fornecida pelo EF intersecte com as capacidades de gestão de ligação de dados dependentes de interfaces de encaminhamento fornecidas pela biblioteca do cliente flexível da base de dados. 

## <a name="elastic-database-tools-assumptions"></a>Pressupostos de ferramentas da base de dados flexível 

Para obter definições de termos, consulte o artigo [Glossário de ferramentas da base de dados flexível](sql-database-elastic-scale-glossary.md).

Biblioteca de base de dados flexível do cliente, pode definir a partições dos seus dados de aplicação denominados shardlets. Shardlets são identificadas por uma chave de sharding e são mapeados para bases de dados específicos. Uma aplicação poderá ter quantas bases de dados, conforme necessário e distribuir shardlets para fornecer suficiente capacidade ou desempenho tendo em conta as necessidades de negócio atual. O mapeamento de valores de chave sharding para as bases de dados é armazenado por um mapa de shard fornecido pelo cliente da base de dados flexível APIs. Chamamos esta capacidade de **Gestão de mapa Shard**ou SMM para abreviada. O mapa shard também serve o corretor de ligações de base de dados para pedidos de conter uma chave de sharding. Vamos referir-se a esta capacidade como **Encaminhamento dependentes de dados**. 
 
O Gestor de mapa shard protege os utilizadores de vistas inconsistentes shardlet dados que podem ocorrer quando são passa operações de gestão de shardlet em simultâneo (como reposicionar dados a partir de um shard para outro). Para fazê-lo, os mapas shard gerido pelo corretor de biblioteca de cliente, as ligações de base de dados para uma aplicação. Esta opção permite-a funcionalidade de mapa shard para eliminar automaticamente uma ligação de base de dados quando operações de gestão de shard podem afetar o shardlet que foi criada para a ligação. Esta abordagem precisa de integrar com algumas das funcionalidades do EF, tal como criar novas ligações a partir de existente para verificar a existência de base de dados. Em geral, o nossa observação foi a que os construtores DbContext padrão apenas escolar sujeito para ligações de fechada da base de dados que podem ser clonar em segurança para EF funcionam. O princípio de estrutura da base de dados flexível em vez disso, é de apenas broker ligações abertas. Um poderá pensar que fecho uma ligação controlada pela biblioteca do cliente antes de entrega para o DbContext EF pode resolver este problema. No entanto, fechando a ligação e depender de EF para voltar a abrir, um foregoes as verificações de validação e consistência realizadas por da biblioteca. No entanto, a funcionalidade de migrações do EF, utiliza estas ligações para gerir o esquema da base de dados subjacentes de uma forma que é transparente para a aplicação. Idealmente, gostaríamos reter e combinar todas estas capacidades a partir da biblioteca de base de dados flexível do cliente e o EF na aplicação do mesmo. A secção seguinte explica estas propriedades e requisitos mais detalhadamente. 


## <a name="requirements"></a>Requisitos de 

Quando trabalha com a biblioteca de base de dados flexível do cliente e o entidade Framework APIs, podemos pretende manter as seguintes propriedades: 

* **Escala de saída**: para adicionar ou remover bases de dados a partir da camada de dados da aplicação sharded conforme necessário para os pedidos de capacidade da aplicação. Isto significa que o controlo sobre a criação e eliminação de bases de dados e utilizar o shard de base de dados flexível mapear APIs manager para gerir bases de dados e os mapeamentos de shardlets. 

* **Consistência**: A aplicação utiliza sharding e utiliza as dados dependentes capacidades de encaminhamento da biblioteca de cliente. Para evitar danos ou resultados da consulta mal, as ligações são controladas através do Gestor de mapa de shard. Retém também consistência e validação.
 
* **Código primeiro**: para manter a conveniência da paradigma primeira de código do EF. No código em primeiro lugar, classes na aplicação são mapeadas transparente para as estruturas de base de dados subjacentes. O código da aplicação interage com DbSets máscara maioria dos aspectos envolvidos no processamento de base de dados subjacentes.
 
* **Esquema**: entidade Framework alças de criação de esquemas de base de dados inicial e evolução de esquema subsequentes através de migrações. Por reter estas capacidades, adaptar a sua aplicação é fácil como os dados evolução deste. 

As seguintes orientações instrui como de cumprir estes requisitos para aplicações código primeiro utilizando ferramentas de base de dados flexível. 

## <a name="data-dependent-routing-using-ef-dbcontext"></a>Dependente de encaminhamento utilizar EF DbContext dos dados 

Ligações de base de dados com entidade Framework normalmente são geridas através de subclasses **DbContext**. Crie estes subclasses ao decorrentes da **DbContext**. Este é onde pode definir o seu **DbSets** implementar as coleções de ficheiros da base de dados com cópia de segurança de objetos do CLR para a sua aplicação. O contexto for dependentes encaminhamento de dados, pode identificamos várias propriedades útil que não seja necessariamente mantenha a tecla para outros EF código primeiros cenários de aplicações: 

* A base de dados já existe e foi registado no mapa shard flexível da base de dados. 
* O esquema da aplicação já foi implementado à base de dados (explicado abaixo). 
* Ligações de encaminhamento de dependentes de dados à base de dados são controladas pelo mapa shard. 

Para integrar **DbContexts** com o encaminhamento de dependentes de dados para escala saída:

1. Criar ligações de base de dados física através das interfaces do cliente de base de dados flexível do Gestor de mapa shard, 
2. Moldar a ligação com a subclasse **DbContext**
3. Passe a ligação para baixo para as classes base **DbContext** para garantir que todos os processamento no lado EF acontece também. 

Exemplo de código seguinte ilustra esta abordagem. (Este código também é do projeto do Visual Studio acompanhamento)

    public class ElasticScaleContext<T> : DbContext
    {
    public DbSet<Blog> Blogs { get; set; }
    …

        // C'tor for data dependent routing. This call will open a validated connection 
        // routed to the proper shard by the shard map manager. 
        // Note that the base class c'tor call will fail for an open connection
        // if migrations need to be done and SQL credentials are used. This is the reason for the 
        // separation of c'tors into the data-dependent routing case (this c'tor) and the internal c'tor for new shards.
        public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
            : base(CreateDDRConnection(shardMap, shardingKey, connectionStr), 
            true /* contextOwnsConnection */)
        {
        }

        // Only static methods are allowed in calls into base class c'tors.
        private static DbConnection CreateDDRConnection(
        ShardMap shardMap, 
        T shardingKey, 
        string connectionStr)
        {
            // No initialization
            Database.SetInitializer<ElasticScaleContext<T>>(null);

            // Ask shard map to broker a validated connection for the given key
            SqlConnection conn = shardMap.OpenConnectionForKey<T>
                                (shardingKey, connectionStr, ConnectionOptions.Validate);
            return conn;
        }    

## <a name="main-points"></a>Principais pontos
* Um novo construtor substitui o construtor de predefinido na subclasse DbContext 
* O construtor de novo leva-o até os argumentos que são necessários para dados encaminhamento dependentes através da biblioteca de base de dados flexível do cliente:
    * o mapa de shard para aceder às interfaces de encaminhamento dependentes de dados
    * a tecla de sharding para identificar shardlet,
    * uma cadeia de ligação com as credenciais para a ligação de encaminhamento dependentes de dados para o shard. 
 
* A chamada para o construtor de classe base leva-o até uma detour para um método estático que efetua todos os passos necessários para o encaminhamento de dependentes de dados. 
   * Utiliza a chamada OpenConnectionForKey das interfaces de cliente flexível da base de dados no mapa shard para estabelecer uma ligação aberta.
   * O mapa shard cria a ligação para o shard que detém shardlet na chave sharding determinado abrir.
   * Esta ligação Abrir lhe é transmitida novamente o construtor de classe base da DbContext para indicar que esta ligação está a ser utilizado por EF em vez de permitindo que EF criar uma nova ligação automaticamente. A ligação da seguinte forma tenha sido sinalizada pelo cliente da base de dados flexível API para que pode garantir a consistência em operações de gestão de mapa shard.
 
  
Utilize o construtor de novo para o seu subclasse DbContext em vez do construtor de predefinido no seu código. Eis um exemplo: 

    // Create and save a new blog.

    Console.Write("Enter a name for a new blog: "); 
    var name = Console.ReadLine(); 

    using (var db = new ElasticScaleContext<int>( 
                            sharding.ShardMap,  
                            tenantId1,  
                            connStrBldr.ConnectionString)) 
    { 
        var blog = new Blog { Name = name }; 
        db.Blogs.Add(blog); 
        db.SaveChanges(); 

        // Display all Blogs for tenant 1 
        var query = from b in db.Blogs 
                    orderby b.Name 
                    select b; 
     … 
    }

O construtor de novo abre-se a ligação para o shard que contém os dados de shardlet identificada pelo valor de **tenantid1**. O código no bloco de **utilizar** permanece inalterado para aceder a **DbSet** para blogues utilizar EF na shard para **tenantid1**. Isto altera a semântica da utilizar o código de bloquear assim que todas as operações de base de dados agora estão limitadas para o um shard onde é mantido **tenantid1** . Por exemplo, uma consulta LINQ sobre os blogues **DbSet** só seria devolver blogues armazenados no shard atual, mas não aqueles armazenado no outras shards.  

#### <a name="transient-faults-handling"></a>Breves falhas de processamento
A equipa do Microsoft Patterns & práticas publicado o [O breves falhas tratamento de bloco de aplicações](https://msdn.microsoft.com/library/dn440719.aspx). A biblioteca é utilizada com a biblioteca de escala flexível do cliente em conjunto com EF. No entanto, certifique-se de que qualquer exceção breves devolve para um local onde podemos pode garantir que está a ser utilizado o construtor de novo após uma falha de breves para que a nova tentativa de ligação é efetuada utilizando os construtores que podemos ter optimizar. Caso contrário, não se garante uma ligação para o shard correto e não existem sem garantias que a ligação é mantida que ocorrem as alterações ao mapa de shard. 

O código seguinte de exemplo ilustra como uma política de repetir SQL pode ser utilizada em torno os construtores de subclasse **DbContext** novos: 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
    { 
        using (var db = new ElasticScaleContext<int>( 
                                sharding.ShardMap,  
                                tenantId1,  
                                connStrBldr.ConnectionString)) 
            { 
                    var blog = new Blog { Name = name }; 
                    db.Blogs.Add(blog); 
                    db.SaveChanges(); 
            … 
            } 
        }); 

**SqlDatabaseUtils.SqlRetryPolicy** no código de acima é definido como um **SqlDatabaseTransientErrorDetectionStrategy** com uma contagem de repetição de 10 e tempo de espera de 5 segundos entre tentativas. Esta abordagem é semelhante a seta de quatro pontas EF e transações iniciado pelo utilizador (consulte [limitações com a repetir estratégias de execução (diante EF6)](http://msdn.microsoft.com/data/dn307226). Ambas as situações exigem que o programa de aplicação controla o âmbito para o qual a exceção breves devolve: a voltar a abrir a transação ou (como mostrado) recriar o contexto a partir do construtor bom que utiliza a biblioteca do cliente flexível da base de dados.

A necessidade de controlar onde exceções breves demorar novamente no âmbito exclua também a utilização de incorporados **SqlAzureExecutionStrategy** incluído no EF. **SqlAzureExecutionStrategy** seria voltar a abrir uma ligação, mas não utilize **OpenConnectionForKey** e ignorar, por conseguinte, a validação que é executada como parte da chamada **OpenConnectionForKey** . Em vez disso, o código de exemplo utiliza incorporados **DefaultExecutionStrategy** incluído também EF. Por oposição a **SqlAzureExecutionStrategy**, funciona corretamente em conjunto com a política de repetir a partir de processamento de falhas breves. A política de execução é definida da turma **ElasticScaleDbConfiguration** . Tenha em atenção que recomendamos decidido não utilizar **DefaultSqlExecutionStrategy** desde sugere a utilizar **SqlAzureExecutionStrategy** se ocorrerem exceções breves - qual seria conduzir a um comportamento mal, tal como é abordado. Para mais informações sobre as políticas de repetir diferente e EF, consulte o artigo [Ligação RDP no EF](http://msdn.microsoft.com/data/dn456835.aspx).     

#### <a name="constructor-rewrites"></a>Construtor de gravações
Os exemplos de código acima ilustram a predefinição construtor novamente escreve necessário para a sua aplicação para utilizar dados dependentes encaminhamento com a arquitetura de entidade. A tabela seguinte generaliza esta abordagem para outros construtores. 


Construtor atual  | Construtor rescrito para os dados | Construtor base | Notas
---------- | ----------- | ------------|----------
MyContext() |ElasticScaleContext (ShardMap, TKey) |DbContext (DbConnection, booleano) |A ligação tem de estar uma função do mapa shard e a chave de encaminhamento dependentes de dados. Precisar de criação de ligação automática by-pass por EF e em vez disso, utilizar o mapa de shard para broker a ligação. 
MyContext(string)|ElasticScaleContext (ShardMap, TKey) |DbContext (DbConnection, booleano) |A ligação é uma função do mapa shard e a chave de encaminhamento dependentes de dados. Uma cadeia de ligação ou nome da base de dados fixo não irá funcionar como eles validação by-pass pelo shard map. 
MyContext(DbCompiledModel) |ElasticScaleContext (ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection, DbCompiledModel, booleano) |A ligação será obter criada para o mapa e sharding a chave shard determinado com o modelo fornecido. O modelo compilado irá ser transmitido para o c'tor base.
MyContext (DbConnection, booleano) |ElasticScaleContext (ShardMap, TKey, booleano) |DbContext (DbConnection, booleano) |A ligação tem de ser inferida a partir do mapa shard e a chave. Não pode ser fornecido como uma entrada (a não ser que entrada já estava a utilizar o mapa shard e a chave). O valor booleano será transmitido. 
MyContext (cadeia, DbCompiledModel) |ElasticScaleContext (ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection, DbCompiledModel, booleano) |A ligação tem de ser inferida a partir do mapa shard e a chave. Não pode ser fornecido como uma entrada (a não ser que entrada estava a utilizar o mapa shard e a chave). O modelo compilado será transmitido. 
MyContext (ObjectContext, booleano) |ElasticScaleContext (ShardMap, TKey, ObjectContext, booleano) |DbContext (ObjectContext, booleano) |O construtor de novo tem de garantir que quaisquer ligação de ObjectContext transmitido como uma entrada é novamente encaminhada para uma ligação gerida pelo escala flexível. É um debate detalhado de ObjectContexts para além do âmbito deste documento.
MyContext (DbConnection, DbCompiledModel, booleano) |ElasticScaleContext (ShardMap, TKey, DbCompiledModel, booleano)| DbContext (DbConnection, DbCompiledModel, booleano); |A ligação tem de ser inferida a partir do mapa shard e a chave. A ligação não pode ser fornecida como uma entrada (a não ser que entrada já estava a utilizar o mapa shard e a chave). Modelo e booleano são transferidas para o construtor de classe base. 

## <a name="shard-schema-deployment-through-ef-migrations"></a>Implementação de esquema shard através de migrações de EF 

Gestão de esquemas automática é uma conveniência fornecida pelo Framework entidade. No contexto de aplicações utilizando ferramentas de base de dados flexível, podemos pretende manter esta capacidade de aprovisionamento de esquema para shards recentemente criados automaticamente quando bases de dados são adicionados à aplicação sharded. É o caso de utilização principal aumentar a capacidade na camada dados para aplicações sharded utilizando EF. Depender de funcionalidades do EF para gestão de esquemas reduz o esforço de administração de base de dados com uma aplicação sharded criada com base em EF. 

Implementação de esquema através de migrações de EF funciona melhor **ligações não abertas**. Isto é contrariamente ao cenário para os dados dependentes encaminhamento que depende da ligação aberta fornecida pelo cliente da base de dados flexível API. Outra diferença é o requisito de consistência: enquanto conveniente para garantir a consistência para todas as ligações de encaminhamento dependentes de dados proteger contra a manipulação de mapa shard em simultâneo, não é um problema com a implementação de esquema inicial para uma nova base de dados que tenham ainda não foram registadas no mapa shard e ainda não foram atribuídas para colocar em espera shardlets. Vamos pode, por conseguinte, dependem de ligações de base de dados normal para este cenários, por oposição encaminhamento dependentes de dados.  

Isto leva a uma abordagem de onde implementação de esquema através de migrações de EF está intimamente ao forma com o registo do nova base de dados como uma shard no mapa de shard a aplicação. Isto depende os pré-requisitos seguintes: 

* A base de dados já foi criada. 
* A base de dados está vazia – -detém sem esquema de utilizador e os dados não utilizador.
* Ainda não pode ser acedida a base de dados através do cliente de base de dados flexível APIs para o encaminhamento de dependentes de dados. 

Estes pré-requisitos no local, podemos criar uma barra aberta **SqlConnection** normal para iniciar migrações EF para implementação de esquema. O código de exemplo seguinte ilustra esta abordagem. 

        // Enter a new shard - i.e. an empty database - to the shard map, allocate a first tenant to it  
        // and kick off EF intialization of the database to deploy schema 

        public void RegisterNewShard(string server, string database, string connStr, int key) 
        { 

            Shard shard = this.ShardMap.CreateShard(new ShardLocation(server, database)); 

            SqlConnectionStringBuilder connStrBldr = new SqlConnectionStringBuilder(connStr); 
            connStrBldr.DataSource = server; 
            connStrBldr.InitialCatalog = database; 

            // Go into a DbContext to trigger migrations and schema deployment for the new shard. 
            // This requires an un-opened connection. 
            using (var db = new ElasticScaleContext<int>(connStrBldr.ConnectionString)) 
            { 
                // Run a query to engage EF migrations 
                (from b in db.Blogs 
                    select b).Count(); 
            } 

            // Register the mapping of the tenant to the shard in the shard map. 
            // After this step, data-dependent routing on the shard map can be used 

            this.ShardMap.CreatePointMapping(key, shard); 
        } 
 

Este exemplo mostra o método **RegisterNewShard** que regista o shard no mapa do shard, implementa o esquema através do migrações EF e armazena um mapeamento de uma chave de sharding para o shard. Depende de um construtor do subclasse **DbContext** (**ElasticScaleContext** na amostra) do feed de uma cadeia de ligação de SQL como entrada. O código deste construtor é rectas reencaminhar, como demonstrado no seguinte exemplo: 


        // C'tor to deploy schema and migrations to a new shard 
        protected internal ElasticScaleContext(string connectionString) 
            : base(SetInitializerForConnection(connectionString)) 
        { 
        } 

        // Only static methods are allowed in calls into base class c'tors 
        private static string SetInitializerForConnection(string connnectionString) 
        { 
            // We want existence checks so that the schema can get deployed 
            Database.SetInitializer<ElasticScaleContext<T>>( 
        new CreateDatabaseIfNotExists<ElasticScaleContext<T>>()); 

            return connnectionString; 
        } 
 
Um pode ter utilizado a versão do construtor herdada a classe base. Mas o código que necessita para se certificar de que o inicializador predefinido para EF é utilizado quando ligar. Por conseguinte, a curto detour para o método estático antes de telefonar para o construtor de classe base com a cadeia de ligação. Tenha em atenção que o registo de shards deve ser executado num domínio de aplicação diferente ou o processo para se certificar de que as definições de inicializador para EF não entram em conflito. 


## <a name="limitations"></a>Limitações 

Abordagens descritas neste documento implicam algumas limitações: 

* Aplicações de EF que utilizam **LocalDb** pela primeira vez precisam de migrar uma base de dados do SQL Server normal antes de utilizar a biblioteca de base de dados flexível do cliente. Não é possível com **LocalDb**escalar para fora de uma aplicação através de sharding com escala flexível. Tenha em atenção que desenvolvimento ainda pode utilizar **LocalDb**. 

* Quaisquer alterações à aplicação o que significam as alterações do esquema da base de dados necessário seguir migrações EF no shards todos os. O código de exemplo para este documento não demonstram como o fazer. Considere a utilização da base de dados a atualização com um parâmetro ConnectionString para iteramos sobre todos os shards; ou extrair o script T-SQL para a migração pendente utilizar base de dados de atualização com o – Script opção e aplicar o script T-SQL ao seu shards.  

* Dada um pedido, é considerada que todas as sua processamento de base de dados encontra-se uma única shard como identificado pela sharding chave fornecida pelo pedido. No entanto, este pressuposto não sempre mantenha a tecla verdadeiro. Por exemplo, quando-não é possível disponibilizar uma chave de sharding. Para resolver este problema, a biblioteca do cliente fornece a classe de **MultiShardQuery** implementa produção uma ligação para consultar através de vários shards. Aprender a utilizar o **MultiShardQuery** em conjunto com EF está fora do âmbito deste documento

## <a name="conclusion"></a>Conclusão

Os passos descritos neste documento, aplicações de EF podem utilizar capacidade a biblioteca de cliente de base de dados flexível para dados dependentes encaminhamento clicando no ícone refactorização construtores das subclasses **DbContext** utilizados na aplicação EF. Este procedimento limita as alterações necessárias para os locais onde **DbContext** classes já existem. Além disso, podem continuar aplicações EF beneficiar de implementação de esquema automático, combinando os passos que é apresentado as migrações EF necessárias com o registo de novos shards e mapeamentos no mapa do shard. 


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-use-entity-framework-applications-visual-studio/sample.png
 