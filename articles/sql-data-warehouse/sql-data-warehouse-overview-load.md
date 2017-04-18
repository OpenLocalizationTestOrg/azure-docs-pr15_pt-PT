   <properties
   pageTitle="Carregar os dados para armazém de dados do SQL Azure | Microsoft Azure"
   description="Obter informações sobre os cenários comuns para dados carregar para armazém de dados SQL. Estas incluem utilizando PolyBase, armazenamento de Blobs do Azure, ficheiros simples e envio de disco. Também pode utilizar as ferramentas de terceiros."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/12/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="load-data-into-azure-sql-data-warehouse"></a>Carregar os dados para armazém de dados do SQL Azure

Um resumo das opções de cenário e recomendações para carregar os dados armazém de dados SQL.

A parte as de carregar dados normalmente está a preparar os dados para a carregar. Azure simplifica carregamento utilizar armazenamento de Blobs do Azure como um arquivo de dados comum para muitos dos serviços e utilizando Azure dados fábrica para orquestrar movimento de comunicação e dados entre os serviços Azure. Estes processos são integrados com a tecnologia de PolyBase que utiliza o processamento previstos excederem consideravelmente paralelo (MPP) para carregar os dados em paralelo a partir do armazenamento de Blobs do Azure para armazém de dados SQL. 

Para a tutoriais carregar bases de dados de exemplo, consulte o artigo [carregar bases de dados de exemplo][].

## <a name="load-from-azure-blob-storage"></a>Carregar a partir do armazenamento de Blobs do Azure
A maneira mais para importar dados para armazém de dados SQL é utilizar PolyBase para carregar os dados a partir do armazenamento de Blobs do Azure. PolyBase utiliza a estrutura de processamento previstos excederem consideravelmente paralelas (MPP) SQL Data Warehouse para carregar os dados em paralelo a partir do armazenamento de Blobs do Azure. Para utilizar PolyBase, pode utilizar comandos de T-SQL ou uma pipeline de fábrica do Azure dados.

### <a name="1-use-polybase-and-t-sql"></a>1. utilizar PolyBase e T-SQL

Resumo do processo de carregamento:

2. Formate os dados como UTF-8 desde PolyBase atualmente não suporta UTF-16.
2. Mover os seus dados ao armazenamento de Blobs do Azure e armazená-lo em ficheiros de texto.
3. Configurar objectos externos no armazém de dados SQL para definir a localização e o formato dos dados
4. Execute um comando de T SQL para carregar os dados em paralelo para uma nova tabela de base de dados.

<!-- 5. Schedule and run a loading job. --> 

Para obter um tutorial, consulte o artigo [carregar dados a partir do armazenamento de Blobs do Azure para armazém de dados SQL (PolyBase)][].

### <a name="2-use-azure-data-factory"></a>2. a utilizar dados Azure fábrica

Para uma forma mais simples de utilizar PolyBase, pode criar um pipeline de fábrica do Azure dados que utiliza PolyBase para carregar os dados a partir do armazenamento de Blobs do Azure para armazém de dados SQL. Este é rápido para configurar uma vez que não precisa de definir os objetos T-SQL. Se precisar de consultar dados externos sem importá-lo, utilize T-SQL. 

Resumo do processo de carregamento:

2. Formate os dados como UTF-8 desde PolyBase atualmente não suporta UTF-16.
2. Mover os seus dados ao armazenamento de Blobs do Azure e armazená-lo em ficheiros de texto.
3. Crie um pipeline Azure dados fábrica para ingerir esta última os dados. Utilize a opção PolyBase.
4. Agendar e executar as em curso.

Para obter um tutorial, consulte o artigo [carregar dados a partir do armazenamento de Blobs do Azure para armazém de dados SQL (Azure dados fábrica)][].


## <a name="load-from-sql-server"></a>Carregar a partir do SQL Server
Para carregar os dados do SQL Server para armazém de dados do SQL pode utilizar Integration Services (SSIS), transferência de ficheiros simples ou discos envio para a Microsoft. Leitura para ver um resumo dos diferentes carregar os processos e ligações a tutoriais.

Para planear uma migração de dados completo do SQL Server para armazém de dados SQL, consulte o artigo [Descrição geral de migração][]. 

### <a name="use-integration-services-ssis"></a>Utilizar os serviços de integração (SSIS)
Se já estiver a utilizar pacotes Integration Services (SSIS) para carregar para do SQL Server, pode atualizar os pacotes de utilizar o SQL Server como a origem e armazém de dados do SQL como o destino. Este é rápida e fácil de fazer, e é uma boa escolha se não estiver a tentar migrar o processo de carregamento para utilizar os dados já na nuvem. A variação está que a carregar será mais lenta do que utilizar PolyBase porque este SSIS não efetua a carga em paralelo.

Resumo do processo de carregamento:

1. Rever o pacote de Integration Services para apontar para a instância do SQL Server para a origem e a base de dados do SQL armazém de dados para o destino.
2. Migre o esquema para o armazenamento de dados SQL, se ainda não estiver lá.
3. Alterar o mapeamento no seu pacotes utilize apenas os tipos de dados que são suportados pelo armazém de dados SQL.
3. Agendar e executar o pacote.

Para obter um tutorial, consulte o artigo [carregar dados do SQL Server Azure SQL dados armazém (SSIS)][].

### <a name="use-azcopy-recommended-for--10-tb-data"></a>Utilizar AZCopy (recomendado para os dados de < 10 TB)
Se o tamanho de dados for < 10 TB, pode exportar os dados do SQL Server para ficheiros simples, copie os ficheiros ao armazenamento de Blobs do Azure e, em seguida, utilize PolyBase para carregar os dados para armazém de dados do SQL

Resumo do processo de carregamento:

1. Utilize o utilitário da linha de comandos bcp para exportar dados do SQL Server para ficheiros simples.
2. Utilize o utilitário da linha de comandos AZCopy para copiar dados a partir de ficheiros simples ao armazenamento de Blobs do Azure.
3. Utilize PolyBase para carregar no armazém de dados SQL.

Para obter um tutorial, consulte o artigo [carregar dados a partir do armazenamento de Blobs do Azure para armazém de dados SQL (PolyBase)][].

### <a name="use-bcp"></a>Utilizar bcp
Se tiver uma pequena quantidade de dados pode utilizar bcp ao carregar diretamente no armazém de dados do SQL Azure.

Resumo do processo de carregamento:
1. Utilize o utilitário da linha de comandos bcp para exportar dados do SQL Server para ficheiros simples.
2. Utilize bcp para carregar os dados a partir de ficheiros simples diretamente ao armazém de dados SQL.

Para obter um tutorial, consulte o artigo [carregar dados do SQL Server para armazém de dados do SQL Azure (bcp)][].


### <a name="use-importexport-recommended-for--10-tb-data"></a>Utilizar Importar/exportar (recomendado para os dados > 10 TB)
Se o tamanho dos dados é > 10 TB e pretende movê-la para Azure, recomendamos que utilize os nossos disco envio serviço [Importar/exportar][]. 

Resumo do processo de carregamento
2. Utilize o utilitário da linha de comandos bcp para exportar dados do SQL Server para ficheiros simples em sequência discos.
3. Envia discos para a Microsoft.
4. Microsoft carrega os dados para armazém de dados SQL

## <a name="load-from-hdinsight"></a>Carregar a partir do HDInsight
Armazém de dados SQL suporta carregar dados do HDInsight através do PolyBase. O processo é igual ao carregar os dados a partir do armazenamento de Blobs do Azure - utilizar PolyBase para ligar à HDInsight para carregar os dados. 

### <a name="1-use-polybase-and-t-sql"></a>1. utilizar PolyBase e T-SQL

Resumo do processo de carregamento:

2. Formate os dados como UTF-8 desde PolyBase atualmente não suporta UTF-16.
2. Mover os seus dados ao HDInsight e armazená-lo em ficheiros de texto, formato ORC ou Parquet.
3. Configure objectos externos no armazém de dados SQL para definir a localização e o formato dos dados.
4. Execute um comando de T SQL para carregar os dados em paralelo para uma nova tabela de base de dados.

Para obter um tutorial, consulte o artigo [carregar dados a partir do armazenamento de Blobs do Azure para armazém de dados SQL (PolyBase)][].

## <a name="recommendations"></a>Recomendações

Muitos dos nossos parceiros tem ao carregar soluções. Para saber mais, consulte uma lista dos nossos [parceiros de solução][]. 

Se os dados provenientes de uma origem não relacionais e pretende carregá-lo para armazém de dados SQL terá de transformam-linhas e colunas antes de carregá-lo. Os dados transformados com logaritmos não necessitam de ser armazenados numa base de dados, podem ser armazenado em ficheiros de texto.

Crie estatísticas dados carregado recentemente. Armazém de dados SQL Azure é que ainda não suporte automática criar ou actualizar as estatísticas de automaticamente.  Para obter o melhor desempenho de consultas, é importante criar estatísticas em todas as colunas de todas as tabelas após a primeira carregar ou quaisquer alterações substanciais ocorrerem dos dados.  Para obter detalhes, consulte o artigo [Estatísticas][].


## <a name="next-steps"></a>Próximos passos
Para obter mais sugestões de desenvolvimento, consulte o artigo [Descrição geral do desenvolvimento][].

<!--Image references-->

<!--Article references-->
[Carregar dados a partir do armazenamento de Blobs do Azure para armazém de dados SQL (PolyBase)]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[Carregar dados a partir do armazenamento de Blobs do Azure para armazém de dados SQL (Azure dados fábrica)]: ./sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md
[Carregar os dados do SQL Server Azure SQL dados armazém (SSIS)]: ./sql-data-warehouse-load-from-sql-server-with-integration-services.md
[Carregar os dados do SQL Server para armazém de dados do SQL Azure (bcp)]: ./sql-data-warehouse-load-from-sql-server-with-bcp.md
[Load data from SQL Server to Azure SQL Data Warehouse (AZCopy)]: ./sql-data-warehouse-load-from-sql-server-with-azcopy.md

[Carregar bases de dados de exemplo]: ./sql-data-warehouse-load-sample-databases.md
[Descrição geral de migração]: ./sql-data-warehouse-overview-migrate.md
[parceiros de solução]: ./sql-data-warehouse-partner-business-intelligence.md
[Descrição geral do desenvolvimento]: ./sql-data-warehouse-overview-develop.md
[Estatísticas]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->

<!--Other Web references-->
[Importar/exportar]: https://azure.microsoft.com/documentation/articles/storage-import-export-service/
