<properties
    pageTitle="O que é HBase no HDInsight? | Microsoft Azure"
    description="Uma introdução à Apache HBase no HDInsight, criar uma base de dados NoSQL no Hadoop. Saiba mais sobre casos de utilização e compare HBase com outros clusters Hadoop."
    keywords="bigtable nosql, o que é hbase"
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian" 
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/14/2016"
    ms.author="jgao"/>



# <a name="what-is-hbase-in-hdinsight-a-nosql-database-that-provides-bigtable-like-capabilities-for-hadoop"></a>O que é HBase no HDInsight: A NoSQL base de dados que fornece capacidades de gosto BigTable para Hadoop

Apache HBase é uma origem de abrir, NoSQL base de dados é criada com base em Hadoop e modelado Google BigTable. HBase fornece acesso aleatório e consistência forte para grandes quantidades de dados não estruturados e semistructured numa base de dados schemaless organizado por famílias de tipos de coluna.

Os dados são armazenados nas linhas de uma tabela e dados de uma linha estão agrupados pela família de coluna. HBase é uma base de dados schemaless no sentido em que nem as colunas nem o tipo de dados armazenados num-los necessitam de ser definidas antes de os utilizar. O código de origem abrir linear escalas para processar petabytes de dados na milhares de nós. Pode confiar redundância de dados, processamento batch e outras funcionalidades que são fornecidas por aplicações distribuídas no ecossistema Hadoop.

## <a name="how-is-hbase-implemented-in-azure-hdinsight"></a>Como é implementado HBase no Azure HDInsight?

HDInsight HBase é disponibilizado como um cluster gerido que está integrado no ambiente do Azure. Os clusters são configurados para armazenar dados diretamente no armazenamento de Blobs do Azure, que fornece latência baixa e elasticidade maior, em Opções de desempenho e custo. Permite que clientes criar Web sites interativos que funcionam com grandes conjuntos de dados para criar os serviços que armazenam dados sensor e de telemetria de milhões de pontos de fim e, para analisar estes dados com tarefas de Hadoop. HBase e Hadoop são bom ponto de partida para projetos de dados grande no Azure; em particular, pode permitem que as aplicações em tempo real trabalhar com conjuntos de dados grandes.

A aplicação HDInsight tira partido da arquitetura de escala de saída do HBase para fornecer sharding automática de tabelas, consistência forte para lê e escritas e mudança de recurso automática. O desempenho é melhorado na memória colocação em cache para operações de leitura e de elevado débito transmissão para operações de escrita. Aprovisionamento de rede virtual também está disponível para HDInsight HBase. Para obter detalhes, consulte [clusters de aprovisionar HDInsight rede Virtual Azure] [hbase-provision-vnet].

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>Como os dados são geridos no HDInsight HBase?

Dados podem ser geridos no HBase utilizando o `create`, `get`, `put`, e `scan` comandos a partir da shell de HBase. Dados destina-se à base de dados utilizando `put` e ler utilizando `get`. O `scan` comando é utilizado para obter dados a partir de várias linhas numa tabela. Também podem ser geridos dados utilizando a HBase c# API, que fornece uma biblioteca de cliente na parte superior a API do resto HBase. Uma base de dados HBase também pode ser consultado através da utilização ramo. Para obter uma introdução para estes modelos de programação, consulte o artigo [começar a utilizar o HBase com Hadoop no HDInsight][hbase-get-started]. Cocriação processadores também estão disponíveis, que permitem o processamento de dados em nós do anfitrião da base de dados.


## <a name="scenarios-use-cases-for-hbase"></a>Cenários: Casos de utilização para HBase
No caso de utilização canónico para que BigTable (e por extensão, HBase) foi criado foi procurar na web. Motores de busca a criar índices mapeiam termos para as páginas web que contêm-los. Mas existem muitos outros casos de utilização HBase é adequado para — vários dos quais são listados nesta secção.

- Valor de chave store

    HBase pode ser utilizado como um valor de chave de arquivo e é adequado para gerir os sistemas de mensagem. Facebook utiliza HBase para o seu sistema de mensagens e é ideal para armazenar e gerir comunicações pela Internet. WebTable utiliza HBase para procurar e gerir as tabelas que são extraídas de páginas Web.

- Dados sensor

    HBase é útil para capturar dados que são recolhidos incrementada a partir de várias origens. Isto inclui analytics social, séries de tempo, manter dashboards interativos atualizados com as tendências e contadores e gerir sistemas de registo de auditoria. Alguns exemplos incluem Bloomberg operador terminal e a hora série de base de dados aberta (OpenTSDB), que armazena e fornece acesso a métricas recolhidas sobre o estado de funcionamento de sistemas de servidor.

- Consulta em tempo real

    [Lisboa](http://phoenix.apache.org/) é um motor de consulta SQL para Apache HBase. É acedida como um controlador de JDBC e permite-consultar e gerir HBase tabelas utilizando SQL.

- HBase como uma plataforma

    Aplicações podem executar na parte superior de HBase por utilizá-lo como um arquivo de dados. Alguns exemplos incluem Portalegre, OpenTSDB, Kiji e Titã. Também podem integrar o aplicações com HBase. Exemplos incluem ramo, porco, Solr, tempestade, Flume, Impala, motores, gânglios das dorsais e desagregação.


##<a name="next-steps"></a>Próximos passos

- [Começar a utilizar HBase com Hadoop no HDInsight][hbase-get-started]
- [Aprovisionar HDInsight clusters rede Virtual do Azure] [hbase-provision-vnet]
- [Configurar a replicação de HBase no HDInsight](hdinsight-hbase-geo-replication.md)
- [Analisar Twitter sentimento com HBase no HDInsight][hbase-twitter-sentiment]
- [Utilizar Maven para criar aplicações de Java que utilizam HBase com HDInsight (Hadoop)][hbase-build-java-maven]

##<a name="see-also"></a>Consulte também

- [Apache HBase](https://hbase.apache.org/)
- [Bigtable: Um sistema de armazenamento distribuída para dados estruturados](http://research.google.com/archive/bigtable.html)




[hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md

[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hbase-build-java-maven]: hdinsight-hbase-build-java-maven.md

[hdinsight-use-hive]: hdinsight-use-hive.md

[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md

[hbase-get-started]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
