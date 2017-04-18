<properties 
    pageTitle="DocumentDB escala e testes de desempenho | Microsoft Azure" 
    description="Saiba como desempenhar escala e testar com Azure DocumentDB desempenho"
    keywords="testes de desempenho"
    services="documentdb" 
    authors="arramac" 
    manager="jhubbard" 
    editor="" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/27/2016" 
    ms.author="arramac"/>

# <a name="performance-and-scale-testing-with-azure-documentdb"></a>Desempenho e a escala testar com Azure DocumentDB
Teste de escala de desempenho e é um passo chave no desenvolvimento de aplicações. Para muitas aplicações, a camada de base de dados tem um impacto significativo no desempenho e escalabilidade geral e, por isso, é um componente crítico de testes de desempenho. [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) é área propositadamente criada para escala flexível e desempenho previsível e, consequentemente, um grande mínimos para aplicações de que necessita de uma camada de alto desempenho base de dados. 

Este artigo é uma referência para programadores execução conjuntos de aplicações de teste de desempenho para as cargas de trabalho DocumentDB ou avaliar DocumentDB para cenários de aplicação de alto desempenho. Foca-se principalmente em testes de desempenho isolado da base de dados, mas também inclui práticas recomendadas para aplicações de produção.

Depois de ler este artigo, será capaz de responder às seguintes questões:   

- Onde posso localizar uma aplicação de cliente do .NET de exemplo para testes de desempenho do Azure DocumentDB? 
- Como posso alcançar níveis de débito alta com o Azure DocumentDB da minha aplicação de cliente?

Para começar a utilizar com o código, transfira o projeto a partir de [Amostra de testes de desempenho em DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark). 

> [AZURE.NOTE] O objetivo desta aplicação é demonstrar melhores práticas para extrair um melhor desempenho terminar DocumentDB com um pequeno número de computadores cliente. Isto não é efetuado para demonstrar a capacidade de pico do serviço, que pode dimensionar limitlessly.

Se procura opções de configuração do lado do cliente melhorar o desempenho DocumentDB, consulte o artigo [sugestões sobre o desempenho DocumentDB](documentdb-performance-tips.md).

## <a name="run-the-performance-testing-application"></a>Executar o desempenho testar aplicação
É a forma mais rápida para começar a utilizar compilar e executar a amostra de .NET abaixo, tal como descrito nos passos abaixo. Também pode rever o código de origem e implementar configurações semelhantes para as suas próprias aplicações de cliente.

**Passo 1:** Transfira o projeto de [Exemplo de testes de desempenho em DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)ou bifurcam do repositório de Github.

**Passo 2:** Modificar as definições para EndpointUrl, AuthorizationKey, CollectionThroughput e DocumentTemplate (opcional) na App.

> [AZURE.NOTE] Antes de Aprovisiona coleções de sites com débito alta, consulte a [Página de preços](https://azure.microsoft.com/pricing/details/documentdb/) para estimar custos por coleção. Armazenamento de faturas DocumentDB e débito independentemente de hora a hora, para que pode guardar os custos ao eliminar ou baixar o débito das suas coleções de DocumentDB após o ensaio.

**Passo 3:** Compilar e executar a aplicação de consola da linha de comandos. Deverá visualizar saída semelhante ao seguinte:

    Summary:
    ---------------------------------------------------------------------
    Endpoint: https://docdb-scale-demo.documents.azure.com:443/
    Collection : db.testdata at 50000 request units per second
    Document Template*: Player.json
    Degree of parallelism*: 500
    ---------------------------------------------------------------------

    DocumentDBBenchmark starting...
    Creating database db
    Creating collection testdata
    Creating metric collection metrics
    Retrying after sleeping for 00:03:34.1720000
    Starting Inserts with 500 tasks
    Inserted 661 docs @ 656 writes/s, 6860 RU/s (18B max monthly 1KB reads)
    Inserted 6505 docs @ 2668 writes/s, 27962 RU/s (72B max monthly 1KB reads)
    Inserted 11756 docs @ 3240 writes/s, 33957 RU/s (88B max monthly 1KB reads)
    Inserted 17076 docs @ 3590 writes/s, 37627 RU/s (98B max monthly 1KB reads)
    Inserted 22106 docs @ 3748 writes/s, 39281 RU/s (102B max monthly 1KB reads)
    Inserted 28430 docs @ 3902 writes/s, 40897 RU/s (106B max monthly 1KB reads)
    Inserted 33492 docs @ 3928 writes/s, 41168 RU/s (107B max monthly 1KB reads)
    Inserted 38392 docs @ 3963 writes/s, 41528 RU/s (108B max monthly 1KB reads)
    Inserted 43371 docs @ 4012 writes/s, 42051 RU/s (109B max monthly 1KB reads)
    Inserted 48477 docs @ 4035 writes/s, 42282 RU/s (110B max monthly 1KB reads)
    Inserted 53845 docs @ 4088 writes/s, 42845 RU/s (111B max monthly 1KB reads)
    Inserted 59267 docs @ 4138 writes/s, 43364 RU/s (112B max monthly 1KB reads)
    Inserted 64703 docs @ 4197 writes/s, 43981 RU/s (114B max monthly 1KB reads)
    Inserted 70428 docs @ 4216 writes/s, 44181 RU/s (115B max monthly 1KB reads)
    Inserted 75868 docs @ 4247 writes/s, 44505 RU/s (115B max monthly 1KB reads)
    Inserted 81571 docs @ 4280 writes/s, 44852 RU/s (116B max monthly 1KB reads)
    Inserted 86271 docs @ 4273 writes/s, 44783 RU/s (116B max monthly 1KB reads)
    Inserted 91993 docs @ 4299 writes/s, 45056 RU/s (117B max monthly 1KB reads)
    Inserted 97469 docs @ 4292 writes/s, 44984 RU/s (117B max monthly 1KB reads)
    Inserted 99736 docs @ 4192 writes/s, 43930 RU/s (114B max monthly 1KB reads)
    Inserted 99997 docs @ 4013 writes/s, 42051 RU/s (109B max monthly 1KB reads)
    Inserted 100000 docs @ 3846 writes/s, 40304 RU/s (104B max monthly 1KB reads)

    Summary:
    ---------------------------------------------------------------------
    Inserted 100000 docs @ 3834 writes/s, 40180 RU/s (104B max monthly 1KB reads)
    ---------------------------------------------------------------------
    DocumentDBBenchmark completed successfully.


**Passo 4 (se necessário):** O débito comunicado (RU/s) da ferramenta de deve ser igual ou superior ao débito aprovisionado da coleção de. Caso contrário, aumentar DegreeOfParallelism em pequenos incrementos pode ajudá-lo ao limite. Se o débito da sua aplicação de cliente plateaus, o lançamento várias instâncias da aplicação nos computadores mesmo ou diferentes, irá ajudá-lo atingir o limite aprovisionado em várias instâncias de diferentes. Se precisar de ajuda com este passo, por favor, escrever um e-mail para askdocdb@microsoft.com ou preencher uma bilhetes de suporte.

Assim que tiver a aplicação em execução, pode experimentar diferentes [políticas de indexação](documentdb-indexing-policies.md) e [níveis de consistência](documentdb-consistency-levels.md) para compreender o impacto na débito e latência. Também pode rever o código de origem e implementar configurações semelhantes ao seu próprio teste conjuntos de aplicações ou aplicações de produção.

## <a name="next-steps"></a>Próximos passos
Neste artigo, iremos visualizou como pode executar o desempenho e a escala testar com DocumentDB utilizar uma aplicação de consola do .NET. Consulte as ligações abaixo para obter informações adicionais sobre como trabalhar com DocumentDB.

* [Exemplo de teste de desempenho de DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)
* [Opções de configuração do cliente para melhorar o desempenho DocumentDB](documentdb-performance-tips.md)
* [Lado do servidor DocumentDB de criação de partições](documentdb-partition-data.md)
* [Níveis de desempenho e coleções de DocumentDB](documentdb-performance-levels.md)
* [Documentação DocumentDB .NET SDK no MSDN](https://msdn.microsoft.com/library/azure/dn948556.aspx)
* [Exemplos de DocumentDB .NET](https://github.com/Azure/azure-documentdb-net)
* [Blogue de DocumentDB no sugestões sobre o desempenho](https://azure.microsoft.com/blog/2015/01/20/performance-tips-for-azure-documentdb-part-1-2/)
