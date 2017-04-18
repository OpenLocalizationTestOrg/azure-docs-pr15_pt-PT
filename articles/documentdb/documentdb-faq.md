<properties 
    pageTitle="Base de dados DocumentDB questiona - perguntas mais frequentes | Microsoft Azure" 
    description="Obtenha respostas às perguntas mais frequentes sobre Azure DocumentDB um serviço de base de dados de documento NoSQL para JSON. Responder a perguntas de base de dados sobre capacidade, níveis de desempenho e dimensionamento." 
    keywords="Perguntas de base de dados, perguntas mais frequentes, documentdb, azure, Microsoft azure"
    services="documentdb" 
    authors="mimig1" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/03/2016" 
    ms.author="mimig"/>


#<a name="frequently-asked-questions-about-documentdb"></a>Perguntas mais frequentes sobre DocumentDB

## <a name="database-questions-about-microsoft-azure-documentdb-fundamentals"></a>Perguntas de base de dados sobre os conceitos básicos da Microsoft Azure DocumentDB

### <a name="what-is-microsoft-azure-documentdb"></a>O que é o Microsoft Azure DocumentDB? 
Microsoft Azure DocumentDB é uma blazing rapidamente, planeta escala NoSQL documento da base de dados-como-a-service que oferece consultar rich sobre os dados de esquema de royalties, ajuda-o a entregar configurável e fiável desempenho e permite desenvolvimento rápido, all through uma plataforma gerido potência de segurança e atinja do Microsoft Azure. DocumentDB é a solução adequada para jogos móvel, da web, e aplicações, IoT quando são previsível débito, disponibilidade alta, baixa latência e um modelo de dados de esquema de royalties chave requisitos. DocumentDB entrega flexibilidade de esquema e RTF indexação através de um modelo de dados JSON nativo e inclui com vários documento suporte transaccional com JavaScript integrada.  
  
Para obter mais perguntas de base de dados, respostas e obter instruções sobre como implementar e utilizar este serviço, consulte a [página de documentação DocumentDB](https://azure.microsoft.com/documentation/services/documentdb/).

### <a name="what-kind-of-database-is-documentdb"></a>Que tipo de base de dados é DocumentDB?
DocumentDB é uma NoSQL documento com base de dados que armazena os dados no formato JSON.  DocumentDB suporta estruturas aninhadas, automática contained dados que podem ser consultadas através de uma DocumentDB rich [gramática de consulta SQL](documentdb-sql-query.md). DocumentDB fornece alto desempenho transaccional processamento do lado do servidor JavaScript através de [procedimentos armazenados, accionadores e funções definidas pelo utilizador](documentdb-programming.md). A base de dados também suporta níveis de consistência ajustável programador com associado [níveis de desempenho](documentdb-performance-levels.md).
 
### <a name="do-documentdb-databases-have-tables-like-a-relational-database-rdbms"></a>DocumentDB bases de dados possui tabelas como uma base de dados relacional (RDBMS)?
Não, DocumentDB armazena os dados nos conjuntos de documentos JSON.  Para obter informações sobre recursos DocumentDB, consulte o artigo [DocumentDB modelo de recurso e conceitos](documentdb-resources.md). Para mais informações sobre como soluções NoSQL como DocumentDB diferem relacionais soluções, consulte o artigo [NoSQL vs SQL](documentdb-nosql-vs-sql.md).

### <a name="do-documentdb-databases-support-schema-free-data"></a>Bases de dados de DocumentDB suportam o esquema de royalties dados?
Sim, DocumentDB permite que as aplicações armazenar arbitrários JSON documentos sem definição de esquema ou sugestões. Dados são imediatamente disponíveis para a consulta através da interface de consulta DocumentDB SQL.   

### <a name="does-documentdb-support-acid-transactions"></a>DocumentDB suporta transações ÁCIDAS?
Sim, DocumentDB suporta transações cruzadas documentos expressas como JavaScript armazenado procedimentos e accionadores. Transações são confinadas a uma única partição dentro de cada coleção e executadas com ácido semântica todos ou nada isolados a partir de outros pedidos em simultâneo execução de código e o utilizador.  Se exceções são devolvidas através da execução de lado do servidor do código da aplicação JavaScript, a transação inteira é revertida. Para mais informações sobre as transações, consulte o artigo [transações do programa de base de dados](documentdb-programming.md#database-program-transactions).

### <a name="what-are-the-typical-use-cases-for-documentdb"></a>Quais são os casos de utilização típica para DocumentDB?  
DocumentDB é uma boa escolha para novos jogos de dispositivos móveis, da web, e aplicações IoT ordem dos tempos de resposta milissegundos e a capacidade de consulta onde escala automática, previsível desempenho, rápida sobre os dados de esquema de royalties é importante. DocumentDB é utilizado pela para um rápido desenvolvimento e suporte a iteração contínua de modelos de dados de aplicação. As aplicações que gerir conteúdo gerado de utilizador e os dados são [comuns casos de utilização para DocumentDB](documentdb-use-cases.md).  

### <a name="how-does-documentdb-offer-predictable-performance"></a>Como é que o DocumentDB oferta previsível desempenho?
Uma [unidade de pedido](documentdb-request-units.md) é a medida de débito em DocumentDB. 1 RU corresponde ao débito de introdução de um documento de 1KB. Cada operação de DocumentDB, incluindo lê, escritas, consultas SQL e procedimento armazenado execuções tenha um valor de RU determinista com base nas débito necessário para concluir a operação. Em vez de pensar em, IO e memória e CPU como que cada afectem a sua débito de aplicação, poderá pensar em termos de uma medida de RU única.

Cada uma das coleções DocumentDB pode estar reservada com débito aprovisionado em termos RUs de débito por segundo. Para as aplicações de qualquer escala, pode de referência pedidos individuais para os respetivos valores RU e colecções de aprovisionar para processar o total da soma de unidades pedido ao longo de todos os pedidos de medida. Também pode dimensionar para cima ou dimensionar para baixo débito da sua coleção como as necessidades da sua evolve de aplicação. Para mais informações sobre unidades pedido e para obter ajuda para determinar a sua coleção de necessita, leia [Gerir desempenho e capacidade](documentdb-manage.md) e experimente a [Calculadora débito](https://www.documentdb.com/capacityplanner). 

### <a name="is-documentdb-hipaa-compliant"></a>DocumentDB HIPAA é compatível com?
Sim, DocumentDB é HIPAA em conformidade. HIPAA estabelece requisitos para utilizar, divulgação e salvaguardar os das informações de estado de funcionamento individualmente identificáveis. Para mais informações, consulte o [Centro de fidedignidade do Microsoft](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### <a name="what-are-the-storage-limits-of-documentdb"></a>Quais são os limites de armazenamento do DocumentDB? 
Não existe nenhum limite teórica até à quantidade total de dados que pode armazenar uma coleção de DocumentDB. Se gostaria armazenar mais de 250 GB de dados numa única coleção, utilize a [contactar o suporte](documentdb-increase-limits.md) para ter a quota de conta aumentada. 

### <a name="what-are-the-throughput-limits-of-documentdb"></a>Quais são os limites de débito do DocumentDB? 
Não existe nenhum limite teórica para a quantidade total de débito que suporta uma coleção de em DocumentDB, se a sua carga de trabalho pode ser distribuída aproximadamente uniformemente entre um suficientemente grande número de chaves de partição. Se pretender exceder pedido 250.000 unidades/segundo por coleção ou conta, utilize a [contactar o suporte](documentdb-increase-limits.md) para ter a quota de conta aumentada. 

### <a name="how-much-does-microsoft-azure-documentdb-cost"></a>Quanto custa o Microsoft Azure DocumentDB?
Consulte a página de [Detalhes do preços DocumentDB](https://azure.microsoft.com/pricing/details/documentdb/) para obter detalhes. Custos de utilização de DocumentDB são determinados pelo número de coleções de sites em utilizar, o número de horas as coleções de ficheiros estavam online, o armazenamento consumo e débito aprovisionado para cada coleção. 

### <a name="is-there-a-free-account-available"></a>Existe uma conta gratuita disponível?
Se estiver familiarizado com o Azure, pode inscrever-se para uma [conta gratuita Azure](https://azure.microsoft.com/free/), que fornece-lhe 30 dias e $200 para experimentar a todos os serviços do Azure. Ou, se tiver uma subscrição do Visual Studio, são elegíveis [€](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) 150 créditos Azure gratuitos por mês utilizar em qualquer serviço Azure.  

### <a name="how-can-i-get-additional-help-with-documentdb"></a>Como posso obter ajuda adicional com DocumentDB?
Se precisar de qualquer tipo de ajuda, comunique-na [Pilha de conteúdo adicional](http://stackoverflow.com/questions/tagged/azure-documentdb), os [Fóruns de programadores do Azure DocumentDB MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureDocumentDB)ou agendar uma [conversação de 1:1 com a equipa de engenharia DocumentDB](http://www.askdocdb.com/). Para se manter atualizado as notícias mais recentes do DocumentDB e funcionalidades, siga-no [Twitter](https://twitter.com/DocumentDB).

## <a name="set-up-microsoft-azure-documentdb"></a>Configurar o Microsoft Azure DocumentDB

### <a name="how-do-i-sign-up-for-microsoft-azure-documentdb"></a>Como inicio inscrição para o Microsoft Azure DocumentDB?
Microsoft Azure DocumentDB está disponível no [Portal do Azure][azure-portal].  Em primeiro lugar tem inscrever-se para uma subscrição do Microsoft Azure.  Depois de se inscrever para uma subscrição do Microsoft Azure, pode adicionar uma conta de DocumentDB à sua subscrição do Azure. Para obter instruções sobre como adicionar uma conta de DocumentDB, consulte o artigo [criar uma conta de base de dados DocumentDB](documentdb-create-account.md).   

### <a name="what-is-a-master-key"></a>O que é uma chave de modelo global?
Uma chave de modelo global de é um token de segurança para aceder a todos os recursos para uma conta. Indivíduos com a chave têm leitura e escreverem acesso a todos os recursos na conta de base de dados. Tome cuidado quando distribuir a teclas de modelo global. A chave primária do modelo global e chave de modelo global de secundária estão disponíveis no pá a **teclas **do [Portal do Azure][azure-portal]. Para mais informações sobre chaves, consulte o artigo [Ver, cópia e as teclas de acesso gerar](documentdb-manage-account.md#keys).

### <a name="how-do-i-create-a-database"></a>Como posso criar uma base de dados?
Pode criar bases de dados utilizando o [Portal do Azure]() , tal como descrito no artigo [criar uma base de dados DocumentDB](documentdb-create-database.md), uma do [SDK DocumentDB](documentdb-sdk-dotnet.md), ou através de do [REST APIs](https://msdn.microsoft.com/library/azure/dn781481.aspx).  

### <a name="what-is-a-collection"></a>O que é uma coleção de?
Uma coleção de é um contentor de documentos JSON e lógica da aplicação de JavaScript associada. Uma coleção de é uma entidade cobrar, onde o [custo](documentdb-performance-levels.md) é determinada pelo débito storaged utilizados e. Coleções de sites podem abranger um ou mais partições/servidores e podem Dimensionar para processar volumes praticamente ilimitados de armazenamento ou débito.

Coleções de sites também são as entidades de faturaçãohttps para DocumentDB. Cada uma das coleções é faturada por hora com base nas débito aprovisionado e o espaço de armazenamento utilizado. Para mais informações, consulte o artigo [DocumentDB preços](https://azure.microsoft.com/pricing/details/documentdb/).  

### <a name="how-do-i-set-up-users-and-permissions"></a>Como posso configurar utilizadores e permissões?
Pode criar utilizadores e permissões utilizando um do [SDK DocumentDB](documentdb-sdk-dotnet.md) ou através de do [REST APIs](https://msdn.microsoft.com/library/azure/dn781481.aspx).   

## <a name="database-questions-about-developing-against-microsoft-azure-documentdb"></a>Perguntas de base de dados sobre como desenvolver contra DocumentDB do Microsoft Azure

### <a name="how-to-do-i-start-developing-against-documentdb"></a>Como fazer inicio desenvolver contra DocumentDB?
[SDK](documentdb-sdk-dotnet.md) estão disponíveis para .NET, Python, Node.js, JavaScript e Java.  Os programadores também podem tirar partido do [RESTful HTTP APIs](https://msdn.microsoft.com/library/azure/dn781481.aspx) para interagir com DocumentDB recursos a partir de uma variedade de plataformas e idiomas. 

Exemplos de DocumentDB [.NET](documentdb-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](documentdb-nodejs-samples.md)e [Python](documentdb-python-samples.md) SDK estão disponíveis no GitHub.

### <a name="does-documentdb-support-sql"></a>DocumentDB suporta SQL?
O idioma de consulta DocumentDB SQL é um subconjunto da funcionalidade da consulta suportado pelo SQL melhorado. O idioma de consulta DocumentDB SQL fornece rich hierárquicos e operadores relacionais e expansão através do utilizador JavaScript com base definidos funções (UDFs). Gramática JSON permite a modelação documentos JSON como árvores com etiquetas de como os nós de árvore, que é utilizado pelo tanto as técnicas de indexação automáticas DocumentDB, bem como a linguagem de consulta SQL de DocumentDB.  Para obter detalhes sobre como utilizar a gramática SQL, consulte o artigo a [Consulta DocumentDB] [ query] artigo.

### <a name="what-are-the-data-types-supported-by-documentdb"></a>Quais são os tipos de dados suportados pelo DocumentDB?
Os tipos de dados primitivos suportados no DocumentDB são os mesmos como JSON. JSON tem um sistema de tipo simples que consiste em cadeias, números (IEEE754 de precisão dupla) e valores booleanos - for verdadeiros, FALSO e valores nulos.  Tipos de dados mais complexos como data/hora, Guid, Int64 e de forma geométrica podem ser representados tanto no JSON e DocumentDB através da criação de objetos aninhadas utilizando o operador {} e matrizes utilizando o operador ' []. 

### <a name="how-does-documentdb-provide-concurrency"></a>Como é que o DocumentDB fornecer simultaneidade?
DocumentDB suporta o controlo de simultaneidade optimista (OCC) através de HTTP entidade etiquetas ou etags. Todos os recursos DocumentDB tem um etag e o etag está definido no servidor sempre que um documento for atualizado. O cabeçalho etag e o valor atual são incluídos em todas as mensagens de resposta. Etags podem ser utilizados com o cabeçalho se CORRESP para permitir que o servidor decidir se um recurso deve ser atualizado. O valor de correspondência se é o valor de etag dada contra. Se o valor etag corresponde ao valor etag server, será atualizado o recurso. Se o etag já não se encontra atual, o servidor rejeita a operação com um "HTTP 412 pré-condicionar falha" código de resposta. O cliente, em seguida, terá de obter novamente o recurso para adquirir o valor de etag atual para o recurso. Além disso, etags podem ser utilizados com cabeçalho se nenhum-correspondência para determinar se é necessária uma novamente obter de um recurso. 

Para utilizar simultaneidade optimista .NET, utilize a classe de [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) . Para uma amostra de .NET, consulte o artigo [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) no exemplo DocumentManagement no github.

### <a name="how-do-i-perform-transactions-in-documentdb"></a>Como posso efectuar operações no DocumentDB?
DocumentDB suporta integrada idioma transações através de procedimentos JavaScript armazenado e accionadores. Todas as operações de base de dados no interior de scripts são executadas em isolamento instantâneo confinado à coleção de se este é uma coleção de partição única ou documentos com o mesmo valor de chave partição numa coleção, se tiver partições a coleção. Um instantâneo das versões de documentos (ETags) é redirecionado no início da transação e consolidado apenas se o script é concluída com êxito. Se o JavaScript emitir um erro, a transação é revertida. Para obter mais detalhes, consulte a [DocumentDB do lado do servidor de programação](documentdb-programming.md) .

### <a name="how-can-i-bulk-insert-documents-into-documentdb"></a>Como posso em volume inserir documentos para DocumentDB? 
Existem três formas de em volume inserir documentos para DocumentDB:

- A dados ferramenta de migração, tal como descrito em [Importar dados para DocumentDB](documentdb-import-data.md).
- Explorador de documentos no Portal do Azure, conforme descrito [em volume adicionar documentos com o Explorador de documento](documentdb-view-json-document-explorer.md#BulkAdd).
- Procedimentos armazenados do, tal como descrito em [DocumentDB do lado do servidor de programação](documentdb-programming.md).

### <a name="does-documentdb-support-resource-link-caching"></a>DocumentDB suporta colocação em cache de ligação de recurso?
Sim, uma vez que DocumentDB é um serviço RESTful, ligações de recursos são imutáveis e podem ser colocada em cache. Os clientes DocumentDB podem especificar um cabeçalho "Se nenhum-correspondência" para lê contra qualquer recurso como o documento ou recolha e atualizar os respetivos local copia apenas quando a versão do servidor tem alterar. 

### <a name="is-a-local-instance-of-documentdb-available"></a>Uma instância local do DocumentDB está disponível?
Neste momento, não está disponível uma instância local do DocumentDB. Pode controlar o estado de um local emulador e voto do mesmo no [Fórum de comentários](https://feedback.azure.com/forums/263030-documentdb/suggestions/6328798-standalone-local-instance).


[azure-portal]: https://portal.azure.com
[query]: documentdb-sql-query.md
 
