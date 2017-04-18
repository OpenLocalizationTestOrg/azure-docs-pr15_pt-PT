<properties 
    pageTitle="Introdução ao DocumentDB, uma base de dados JSON | Microsoft Azure" 
    description="Saiba mais sobre Azure DocumentDB, uma base de dados NoSQL JSON. Esta base de dados do documento é criada para dados grandes, escalabilidade flexível e disponibilidade de alta." 
    keywords="base de dados JSON, base de dados do documento"
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
    ms.topic="get-started-article" 
    ms.date="09/13/2016" 
    ms.author="mimig"/>

# <a name="introduction-to-documentdb-a-nosql-json-database"></a>Introdução ao DocumentDB: uma base de dados NoSQL JSON

##<a name="what-is-documentdb"></a>O que é DocumentDB?

DocumentDB é um serviço de base de dados de NoSQL totalmente gerido criado para desempenho alternativa rápido e previsível, elevada disponibilidade, dimensionamento flexível, distribuição globais e de Facilidade de desenvolvimento. Como um esquema de royalties NoSQL base de dados, DocumentDB fornece avançadas e familiares SQL as capacidades de consulta com consistentes latências baixos dados JSON - assegurar que 99% de seu lê são servida 10 em milissegundos e 99% das gravações são servidas 15 em milissegundos. Tornar estas vantagens exclusivo DocumentDB um grande ajustar para web, móvel, jogos e IoT e muitas outras aplicações que necessitam de escala de forma totalmente integrada e replicação global.

## <a name="how-can-i-learn-about-documentdb"></a>Como posso aprender DocumentDB? 

Uma forma rápida de saber mais sobre DocumentDB e vê-la na ação é siga estes três passos: 

1. Ver os dois minutos [o que é DocumentDB?](https://azure.microsoft.com/documentation/videos/what-is-azure-documentdb/) vídeo, que introduz os benefícios da utilização DocumentDB.
2. Veja os minutos três [Criar DocumentDB no Azure](https://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) vídeo, que realça como começar com DocumentDB utilizando o Portal do Azure.
3. Visite a [Consulta parques](http://www.documentdb.com/sql/demo), onde pode percorrer atividades diferentes para saber mais sobre a funcionalidade avançada de consultar disponível no DocumentDB. Em seguida, head ao longo para o separador Sandbox e executar a suas própria consultas SQL personalizadas e experimentar DocumentDB.

Em seguida, regresse neste artigo, onde podemos irá procurar forma mais aprofundada.  

## <a name="what-capabilities-and-key-features-does-documentdb-offer"></a>Quais os principais funcionalidades e capacidades DocumentDB oferecidas?  

Azure DocumentDB oferece as seguintes capacidades chaves e vantagens:

-   **Débito elastically dimensionável e armazenamento:** Facilmente Dimensionar para cima ou dimensionar para baixo da sua base de dados DocumentDB JSON para satisfazer as necessidades da aplicação. Os dados são armazenados em discos estado sólido (SSD) para baixos latências previsíveis. DocumentDB suporta contentores para armazenar dados JSON denominados colecções podem Dimensionar para tamanhos de armazenamento praticamente ilimitado e aprovisionado débito. Pode elastically Dimensionar DocumentDB com o desempenho previsível totalmente à medida que aumenta a sua aplicação. 

-   **Replicação da região com várias:** DocumentDB replica transparente os seus dados para todas as regiões que associou com a sua conta de DocumentDB, permitindo-lhe desenvolver aplicações que requerem global acesso aos dados ao fornecer concessões entre consistência, disponibilidade e desempenho, tudo com garantias correspondentes. DocumentDB fornece transparente activação regional com APIs multi-homing e a capacidade de elastically Dimensionar débito e armazenamento em todo o mundo. Saiba mais do mais [distribuir dados globalmente com DocumentDB](documentdb-distribute-data-globally.md).

-   **Consultas Ad hoc com familiar sintaxe SQL:** Armazenar documentos JSON heterogéneos dentro DocumentDB e estes documentos através de uma sintaxe SQL comuns de consulta. DocumentDB utiliza um cadeado altamente em simultâneo, livre, registo estruturadas tecnologia de indexação para indexar automaticamente todo o conteúdo de documento. Permite que as consultas em tempo real ricos sem ser necessário para especificar as sugestões de esquema, índices secundários ou vistas. Saiba mais em [DocumentDB de consulta](documentdb-sql-query.md). 

-   **JavaScript execução da base de dados:** Exprima lógica da aplicação procedimentos armazenados, accionadores e funções definidas pelo utilizador (UDFs) utilizando JavaScript padrão. Esta opção permite-lógica da aplicação trabalhar com sobre os dados sem se preocupar o erro de correspondência entre a aplicação e o esquema da base de dados. DocumentDB fornece completa transaccional execução dos JavaScript lógica da aplicação diretamente no interior do motor de base de dados. A integração abrangente de JavaScript permite a execução de inserir, substituir, eliminar e SELECIONADAS operações a partir de um programa de JavaScript como uma transação isolada. Saiba mais em [programação do lado do servidor DocumentDB](documentdb-programming.md).

-   **Níveis de consistência ajustável:** Selecione a partir de quatro níveis de consistência bem definido para atingir compromisso ideal entre a consistência e desempenho. Para consultas e operações de leitura, DocumentDB oferece quatro níveis de consistência distintas: sessão forte, staleness delimitada, e eventuais. Estes níveis de consistência granular, bem definidos permitem-lhe tornar o som compromissos entre consistência, disponibilidade e latência. Saiba mais ao utilizar [níveis de consistência para maximizar a disponibilidade e desempenho no DocumentDB](documentdb-consistency-levels.md).

-   **Completamente geridas:** Elimine a necessidade para gerir os recursos de base de dados e máquina. Como serviço Microsoft Azure completamente geridas, não precisa de gerir máquinas virtuais, implementar e configurar o software, gerir dimensionamento ou lidar com actualizações de camada de dados complexos. Cada base de dados é automaticamente cópias de segurança e protegida contra falhas regionais. Pode adicionar uma conta de DocumentDB facilmente e aprovisionar capacidade que precisar, permitindo-lhe concentrar-se na sua aplicação em vez de funcionamento e gerir a sua base de dados. 

-   **Aberto por estrutura:** Começar rapidamente com ferramentas e competências que já possui. Programação contra DocumentDB é acessível, simples e não requer que adotar novas ferramentas ou adira extensões personalizadas ao JSON ou JavaScript. Pode aceder a todas as funcionalidades de base de dados, incluindo os CRUD, consultas e JavaScript processamento através de uma interface de RESTful HTTP simples. DocumentDB formatos existentes, idiomas e padrões mulheres abrange durante a perguntar se o valor elevado capacidades de base de dados na parte superior-los.

-   **Indexação automática:** Por predefinição, DocumentDB [índices automaticamente](documentdb-indexing.md) todos os documentos na base de dados e não esperar ou exigir qualquer esquema ou a criação de índices secundários. Não pretende incluir no índice remissivo tudo? Não se preocupe, pode [optar por terminar caminhos nos seus ficheiros JSON](documentdb-indexing-policies.md) demasiado.

##<a name="data-management"></a>Como é que o DocumentDB gerir dados?

Azure DocumentDB gere dados JSON através de recursos base de dados bem definidos. Estes recursos são replicados para elevada disponibilidade e são exclusivamente endereçáveis pelo respetivo URI lógico. DocumentDB oferece que uma HTTP simple com base no modelo de programação RESTful para todos os recursos. 

A conta de base de dados DocumentDB é um espaço de nomes exclusivo que dá-lhe acesso a Azure DocumentDB. Antes de poder criar uma conta de base de dados, tem de ter uma subscrição do Azure, que dá-lhe acesso a uma variedade de serviços Azure. 

Todos os recursos dentro DocumentDB são modelados e armazenados como documentos JSON. Os recursos são geridos como itens, que são JSON documentos que contém metadados e como feeds que são coleções de itens. Conjuntos de itens estão contidos no seus feeds respetivos.

A imagem abaixo mostra as relações entre os recursos DocumentDB:

![A relação entre os recursos no DocumentDB, uma base de dados NoSQL JSON hierárquica][1] 

Uma conta de base de dados é composta por um conjunto de bases de dados, cada uma contendo várias colecções, cada uma das quais pode conter procedimentos armazenados, accionadores, UDFs, documentos e anexos relacionados. Uma base de dados também tem associados utilizadores, cada uma com um conjunto de permissões para aceder a várias outras coleções de sites, procedimentos armazenados, accionadores, UDFs, documentos ou anexos. Enquanto bases de dados, os utilizadores, permissões e coleções de sites são definidos pelo sistema recursos com esquemas famoso – documentos, procedimentos armazenados, accionadores, UDFs e anexos contêm arbitrário, o utilizador definidos conteúdo JSON.  

##<a name="develop"></a>Como pode a desenvolver aplicações com DocumentDB?

Azure DocumentDB expõe recursos através de um REST API que pode ser chamados por qualquer idioma capaz de fazer pedidos HTTP/HTTPS. Para além disso, DocumentDB oferece programação bibliotecas para vários idiomas populares. Estas bibliotecas simplificam muitos aspetos de trabalhar com Azure DocumentDB através do tratamento de detalhes, como endereço colocação em cache, gestão de exceção, número de tentativas automáticos e assim sucessivamente. Bibliotecas estão atualmente disponíveis para os idiomas e plataformas seguintes:  

Transferir | Documentação
--- | ---
[.NET SDK](http://go.microsoft.com/fwlink/?LinkID=402989) | [Biblioteca de .NET](https://msdn.microsoft.com/library/azure/dn948556.aspx)
[NODE.js SDK](http://go.microsoft.com/fwlink/?LinkID=402990) | [Biblioteca de node.js](http://azure.github.io/azure-documentdb-node/)
[Java SDK](http://go.microsoft.com/fwlink/?LinkID=402380) | [Biblioteca de Java](http://azure.github.io/azure-documentdb-java/)
[JavaScript SDK](http://go.microsoft.com/fwlink/?LinkID=402991) | [Biblioteca de JavaScript](http://azure.github.io/azure-documentdb-js/)
n/d | [Do lado do servidor JavaScript SDK](http://azure.github.io/azure-documentdb-js-server/)
[Python SDK](https://pypi.python.org/pypi/pydocumentdb) | [Biblioteca de Python](http://azure.github.io/azure-documentdb-python/)

Além dos básica criar, ler, atualizar e eliminar operações, DocumentDB fornece uma interface de consulta avançada do SQL para obter documentos JSON e do lado do servidor de suporte para execução transaccional JavaScript de lógica de aplicações. As interfaces de execução de consulta e script estão disponíveis através de todas as bibliotecas de plataforma, bem como os REST APIs. 

### <a name="sql-query"></a>Consulta SQL
Azure DocumentDB suporta documentos ao consultar a utilizar um idioma SQL, qual é raiz no sistema de tipo de JavaScript e expressões com suporte de consultas relacionais, hierárquicas e espaciais. O idioma de consulta DocumentDB é uma interface simple e poderoso para documentos JSON de consulta. O idioma suporta um subconjunto de gramática ANSI SQL e adiciona abrangente integração do objeto JavaScript, matrizes, construção de objeto e invocação de função. DocumentDB fornece o seu modelo de consulta sem qualquer esquema explícita ou sugestões de indexação a partir do programador.

Funções definidas pelo utilizador (UDFs) pode ser registadas com a DocumentDB e referenciadas como parte de uma consulta SQL, portanto expandir a gramática para suportar a lógica da aplicação personalizada. Estes UDFs estão escritos como JavaScript programas e são executados dentro da base de dados. 

Para programadores do .NET, DocumentDB também oferece um fornecedor de consulta LINQ como parte do [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.aspx). 

### <a name="transactions-and-javascript-execution"></a>Transações e a execução de JavaScript
DocumentDB permite-lhe escrever lógica da aplicação como os programas com nome escritos totalmente JavaScript. Estes programas registados para uma coleção de e podem emitir operações de base de dados em documentos dentro de uma determinada coleção. Pode ser registado JavaScript para execução como accionador, procedimento armazenado ou função definidas pelo utilizador. Accionadores e procedimentos armazenados podem criar, ler, atualizar e eliminar documentos, Considerando que executar funções definidas pelo utilizador como parte da lógica de execução de consulta sem acesso de escrita para a coleção.

Execução JavaScript dentro DocumentDB é baseada os conceitos suportados pelos sistemas de base de dados relacionais, com JavaScript como substituição moderna para Transact-SQL. Todos os JavaScript lógica é executada dentro de uma transação ÁCIDA ambiente com isolamento instantâneo. Durante a sua execução, se o JavaScript inicia uma exceção, em seguida, a transação inteira é cancelada.

## <a name="next-steps"></a>Próximos passos
Já tem uma conta Azure? Em seguida, pode começar com DocumentDB no [Portal do Azure](https://portal.azure.com/#gallery/Microsoft.DocumentDB) através da [criação de uma conta de base de dados DocumentDB](documentdb-create-account.md).

Não tem uma conta Azure? Pode:

- Se inscrever para uma [versão de avaliação gratuita Azure](https://azure.microsoft.com/free/), que lhe dá 30 dias e $200 para experimentar a todos os serviços do Azure. 
- Se tiver uma subscrição do MSDN, está elegível [€](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) 150 créditos Azure gratuitos por mês utilizar em qualquer serviço Azure. 

Em seguida, quando estiver pronto para saber mais, visite a nossa [caminho de formação](https://azure.microsoft.com/documentation/learning-paths/documentdb/) para navegar todos os recursos de formação disponíveis para si. 


[1]: ./media/documentdb-introduction/json-database-resources1.png
 
