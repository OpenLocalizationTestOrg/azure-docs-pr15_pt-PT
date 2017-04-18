<properties
    pageTitle="Consulta em bases de dados de nuvem com diferentes esquemas | Microsoft Azure"
    description="como configurar consultas de base de dados em várias sobre a partições verticais"    
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
    ms.author="torsteng" />

# <a name="query-across-cloud-databases-with-different-schemas-preview"></a>Consulta em bases de dados na nuvem com diferentes esquemas (pré-visualização)

![Consulta em tabelas de bases de dados diferentes][1]

Bases de dados divididos verticalmente utilizam diferentes conjuntos de tabelas no bases de dados diferentes. Isto significa que o esquema é diferente em bases de dados diferentes. Por exemplo, todas as tabelas para o inventário estão numa base de dados enquanto todas as tabelas relacionadas com a gestão de contas estão numa base de dados segundo. 

## <a name="prerequisites"></a>Pré-requisitos

* O utilizador tem de possuir permissão de alterar qualquer origem de dados externa. Esta permissão está incluído com a permissão de alterar a base de dados.
* Permissões de alterar qualquer origem de dados EXTERNOS são necessários para se referir à origem de dados subjacentes.

## <a name="overview"></a>Descrição geral

**Nota**: ao contrário com a partições horizontal, estas instruções DDL não dependem que define uma camada de dados com um mapa de shard através da biblioteca de cliente flexível da base de dados.

1. [CRIAR A CHAVE DE MODELO GLOBAL](https://msdn.microsoft.com/library/ms174382.aspx)
2. [CRIAR A BASE DE DADOS CONFINADO CREDENCIAIS](https://msdn.microsoft.com/library/mt270260.aspx)
3. [CRIAR A ORIGEM DE DADOS EXTERNA](https://msdn.microsoft.com/library/dn935022.aspx)
4. [CRIAR TABELA EXTERNA](https://msdn.microsoft.com/library/dn935021.aspx) 


## <a name="create-database-scoped-master-key-and-credentials"></a>Criar a chave de modelo global de base de dados confinado e as credenciais 

A credencial é utilizada pela consulta flexível para ligar à sua bases de dados remotos.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]
 
**Nota**    Certifique-se de que o *<username>* não inclui qualquer *"@servername"* sufixo. 

## <a name="create-external-data-sources"></a>Criar origens de dados externas

Sintaxe:

    <External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH 
               (TYPE = RDBMS,
                LOCATION = ’<fully_qualified_server_name>’,
                DATABASE_NAME = ‘<remote_database_name>’,  
                CREDENTIAL = <credential_name> 
                ) [;] 

**Importante**   O parâmetro de tipo tem de estar definido para **RDBMS**. 

### <a name="example"></a>Exemplo 

O exemplo seguinte ilustra a utilização da instrução CREATE para origens de dados externas. 

    CREATE EXTERNAL DATA SOURCE RemoteReferenceData 
    WITH 
    ( 
        TYPE=RDBMS, 
        LOCATION='myserver.database.windows.net', 
        DATABASE_NAME='ReferenceData', 
        CREDENTIAL= SqlUser 
    ); 
 
Para obter a lista de origens de dados externas atual: 

    select * from sys.external_data_sources; 

### <a name="external-tables"></a>Tabelas externas 

Sintaxe:

    CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name . ] table_name  
    ( { <column_definition> } [ ,...n ])     
    { WITH ( <rdbms_external_table_options> ) } 
    )[;] 
    
    <rdbms_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>, 
      [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 

### <a name="example"></a>Exemplo  

    CREATE EXTERNAL TABLE [dbo].[customer]( 
        [c_id] int NOT NULL, 
        [c_firstname] nvarchar(256) NULL, 
        [c_lastname] nvarchar(256) NOT NULL, 
        [street] nvarchar(256) NOT NULL, 
        [city] nvarchar(256) NOT NULL, 
        [state] nvarchar(20) NULL, 
        [country] nvarchar(50) NOT NULL, 
    ) 
    WITH 
    ( 
           DATA_SOURCE = RemoteReferenceData 
    ); 

O exemplo seguinte mostra como obter a lista de tabelas externas a partir da base de dados atual: 

    select * from sys.external_tables; 

### <a name="remarks"></a>Observações

A sintaxe de tabela externa existente para definir tabelas externas que utilizem origens de dados externas tipo RDBMS expande a consulta flexível. Uma definição de tabela externa para criar a partições vertical abrange os seguintes aspetos: 

* **Esquema**: A tabela externa DDL define um esquema que podem utilizar as suas consultas. O esquema fornecido na sua definição de tabela externa tem de corresponder ao esquema das tabelas na base de dados remota onde os dados reais estão armazenados. 

* **Referência de base de dados remota**: A tabela externa DDL que se refere a uma origem de dados externos. Origem de dados externa Especifica o nome do servidor lógicos e o nome da base de dados da base de dados remota localização onde armazenou os dados da tabela real. 

A sintaxe para criar tabelas externas utilizar uma origem de dados externa, conforme descrito na secção anterior, é da seguinte forma: 

A cláusula DATA_SOURCE define a origem de dados externa (ou seja, base de dados remota em caso de criar a partições vertical) que é utilizada para a tabela externa.  

Cláusulas SCHEMA_NAME e nome_objeto fornecem a capacidade de mapear a definição de tabela externa a uma tabela num esquema diferente na base de dados remota ou a uma tabela com um nome diferente, respetivamente. Isto é útil se pretender definir uma tabela externa para uma vista de catálogo ou das DMV na sua base de dados remota – ou qualquer outra situação onde o nome da tabela remoto já é disponibilizado localmente.  

A seguinte instrução DDL desce uma definição de tabela externa existente do catálogo local. Não-causam impacto na base de dados remota. 

    DROP EXTERNAL TABLE [ [ schema_name ] . | schema_name. ] table_name[;]  

**Permissões para criar/LARGAR a tabela externa**: permissões de alterar qualquer origem de dados EXTERNOS são necessários para tabela externa DDL que também é necessário para se referir à origem de dados subjacentes.  

## <a name="security-considerations"></a>Considerações de segurança
Utilizadores com acesso para a tabela externa automaticamente obter acesso a tabelas subjacentes remotos em credenciais indicadas na definição de origem de dados externos. Deve atentamente para gerir o acesso para a tabela externa para evitar indesejada elevação de privilégios através de credenciais da origem de dados externos. Permissões de SQL normais podem ser utilizadas para conceder ou REVOGAR o acesso a uma tabela externa como se fosse uma tabela normal.  


## <a name="example-querying-vertically-partitioned-databases"></a>Exemplo: consultar verticalmente a partições bases de dados 

A seguinte consulta executa uma associação de três-se entre as duas tabelas locais para ordens de linhas e de encomenda e a tabela remota para clientes. Este é um exemplo de casos de utilização de dados referência para a consulta flexível: 

    SELECT      
     c_id as customer,
     c_lastname as customer_name,
     count(*) as cnt_orderline, 
     max(ol_quantity) as max_quantity,
     avg(ol_amount) as avg_amount,
     min(ol_delivery_d) as min_deliv_date
    FROM customer 
    JOIN orders 
    ON c_id = o_c_id
    JOIN  order_line 
    ON o_id = ol_o_id and o_c_id = ol_c_id
    WHERE c_id = 100


## <a name="stored-procedure-for-remote-t-sql-execution-spexecuteremote"></a>Procedimento para remota T-SQL armazenado: sp\_execute_remote

Consulta flexível também apresenta um procedimento armazenado que fornece acesso direto às shards. O procedimento armazenado chama [sp\_executar \_remoto](https://msdn.microsoft.com/library/mt703714) e pode ser utilizado para executar remotos procedimentos armazenados ou o código T SQL nas bases de dados remotos. Bastam seguintes parâmetros: 

* Nome da origem de dados (nvarchar): O nome da origem de dados externa do tipo RDBMS. 
* Consulta (nvarchar): consulta a T-SQL para ser executado em cada shard. 
* Declaração de parâmetro (nvarchar) - opcional: cadeia com definições de tipo de dados para os parâmetros utilizados para o parâmetro de consulta (como sp_executesql). 
* Lista de valores de parâmetro - opcional: separados por vírgulas lista de valores de parâmetros (como sp_executesql).

O sp\_executar\_remoto utiliza a origem de dados externos fornecida nos parâmetros de invocação para executar a instrução de T SQL determinada nas bases de dados remotos. Utiliza a credencial da origem de dados externas para ligar à base de dados de manager shardmap e as bases de dados remotos.  

Exemplo: 

    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse' 


  
## <a name="connectivity-for-tools"></a>Conectividade de ferramentas

Pode utilizar cadeias de ligação normais do SQL Server para ligar as ferramentas de integração de BI e dados para bases de dados no servidor SQL DB que tem consulta flexível ativado e tabelas externas definidas. Certifique-se de que é suportado do SQL Server como uma origem de dados de uma ferramenta. Em seguida, referem-se para a base de dados de consulta flexível e respectivas tabelas externas tal como faria com qualquer outra do SQL Server base de dados que pretende ligar a com a ferramenta. 

## <a name="best-practices"></a>Melhores práticas 
 
* Certifique-se de que a base de dados de ponto final de consulta flexível tenha sido dado acesso à base de dados remota ao ativar o acesso para serviços de Azure na sua configuração do SQL DB firewall. Também garantir que a credencial fornecida na definição de origem de dados externos pode iniciar sessão com êxito para a base de dados remota e tem as permissões para aceder à tabela remota.  

* Consulta flexível funciona melhor para consultas onde a maior parte do cálculo pode ser efetuado nas bases de dados remotos. Normalmente, a obter o melhor desempenho de consulta com predicados de filtro selective que possa ser avaliado no bases de dados remotos ou associações que podem ser executadas completamente base de dados remota. Outras padrões de consulta poderão ter de carregar grandes quantidades de dados a partir da base de dados remota e podem executar mal. 


## <a name="next-steps"></a>Próximos passos

Para consultar horizontalmente com a partições bases de dados (também conhecido como sharded bases de dados), consulte o artigo [consultas em nuvem sharded bases de dados (horizontalmente divididos)](sql-database-elastic-query-horizontal-partitioning.md).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Image references-->
[1]: ./media/sql-database-elastic-query-vertical-partitioning/verticalpartitioning.png


<!--anchors-->
