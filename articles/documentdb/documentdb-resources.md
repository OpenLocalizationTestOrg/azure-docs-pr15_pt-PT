<properties 
    pageTitle="DocumentDB modelo hierárquico de recurso e conceitos | Microsoft Azure" 
    description="Saiba mais sobre o modelo de hierárquico do DocumentDB de bases de dados, coleções de sites, função definidas pelo utilizador (UDF), documentos, permissões para gerir recursos e muito mais."
    keywords="Modelo hierárquico, documentdb, azure, Microsoft azure"   
    services="documentdb" 
    documentationCenter="" 
    authors="AndrewHoh" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/28/2016" 
    ms.author="anhoh"/>

# <a name="documentdb-hierarchical-resource-model-and-concepts"></a>Modelo de Hierárquico Recurso DocumentDB e conceitos

As entidades de base de dados que faz a gestão dos DocumentDB são referidas como **recursos**. Cada recurso exclusivamente é identificado por um URI lógico. Pode interagir com os recursos com verbos HTTP padrão, cabeçalhos de pedido/resposta e os códigos de estado. 

Ao ler este artigo, poderá atender as seguintes questões:

- O que é o modelo de recursos do DocumentDB?
- O que são sistema definidos recursos por oposição a recursos definidas pelo utilizador?
- Como endereço um recurso?
- Como trabalhar com coleções de sites?
- Como trabalhar com os procedimentos armazenados, accionadores e funções definidas pelo utilizador (UDFs)?

## <a name="hierarchical-resource-model"></a>Modelo de hierárquico de recurso
Como o diagrama seguinte ilustra, hierárquica de DocumentDB **modelo recurso** consiste em conjuntos de recursos numa conta de base de dados, cada endereçável através de um URI lógico e estável. Um conjunto de recursos será referido como um **feed** neste artigo. 

>[AZURE.NOTE] DocumentDB oferece um protocolo TCP altamente eficiente que também é RESTful no seu modelo de comunicação disponível através do [cliente .NET SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx).

![Modelo do DocumentDB hierárquico de recurso][1]  
**Modelo de hierárquico de recurso**   

Para começar a trabalhar com recursos, tem de [criar uma conta de base de dados DocumentDB](documentdb-create-account.md) utilizar a sua subscrição Azure. Uma conta de base de dados pode ter um conjunto de **bases de dados**, cada que contém várias **coleções de sites**, cada um dos quais sucessivamente contêm de **procedimentos, armazenados accionadores UDFs, documentos** e relacionadas com **anexos** (funcionalidade de pré-visualização). Uma base de dados também tem associados **aos utilizadores**, cada uma com um conjunto de **permissões** de acesso a coleções de sites, procedimentos armazenados, accionadores, UDFs, documentos ou anexos. Enquanto bases de dados, os utilizadores, permissões e coleções de sites são definidos pelo sistema recursos com esquemas famoso, documentos e anexos contêm arbitrário, conteúdo JSON definidas pelo utilizador.  

|Recurso   |Descrição
|-----------|-----------
|Conta de base de dados   |Uma conta de base de dados está associada um conjunto de bases de dados e um valor fixo de armazenamento de BLOBs para anexos (funcionalidade de pré-visualização). Pode criar um ou mais contas de base de dados, utilizar a sua subscrição Azure. Para obter mais informações, visite a nossa [página de preços](https://azure.microsoft.com/pricing/details/documentdb/).
|Base de dados   |Uma base de dados é um contentor lógico de divididos em coleções de armazenamento no documento. Também é um contentor de utilizadores.
|Utilizador   |Espaço de nomes lógico para controlar o âmbito permissões. 
|Permissão |Um token de autorização associado a um utilizador para aceder a um recurso específico.
|Coleções de sites |Uma coleção de é um contentor de documentos JSON e lógica da aplicação de JavaScript associada. Uma coleção de é uma entidade cobrar, onde o [custo](documentdb-performance-levels.md) é determinada pelo nível de desempenho associado a coleção. Coleções de sites podem abranger um ou mais partições/servidores e podem Dimensionar para processar volumes praticamente ilimitados de armazenamento ou débito.
|Procedimento armazenado   |Lógica da aplicação escrita JavaScript que está registado com a uma coleção de e forma transaccional executado dentro do motor de base de dados.
|Accionador    |Lógica da aplicação escrita JavaScript executada antes ou depois de um dos insert, substituir ou operação de eliminação.
|UDF    |Lógica da aplicação escrita JavaScript. UDFs permitem-lhe um operador de consulta personalizadas do modelo e portanto estender o essencial DocumentDB linguagem de consulta.
|Documento   |Conteúdo JSON (arbitrário) definidos pelo utilizador. Por predefinição, sem esquema tem de ser definidas nem índices secundários precisam de ser fornecido para todos os documentos adicionados a uma colecção.
|(Pré-visualização) Anexo   |Um anexo é um documento especial que contêm referências e metadados associados para blob/suporte de dados externos. O programador pode optar por ter o blob gerido pelo DocumentDB ou armazená-lo com um fornecedor de serviços externos blob como o OneDrive, Dropbox, etc. 


## <a name="system-vs-user-defined-resources"></a>Sistema vs. recursos definidas pelo utilizador
Recursos, tais como contas de base de dados, bases de dados, coleções de sites, os utilizadores, as permissões, procedimentos armazenados, accionadores e UDFs - todos têm um esquema fixo e chamam-se os recursos do sistema. Em contrapartida, recursos, tais como documentos e anexos não têm restrições no esquema e alguns exemplos de recursos definidas pelo utilizador. No DocumentDB, sistema e definidas pelo utilizador recursos são representados e geridos como JSON compatível com padrão. Todos os recursos, sistema ou utilizador definido, tem as seguintes propriedades comuns.

> [AZURE.NOTE] Nota que todos os gerado pelo sistema propriedades de um recurso são o prefixo com um sublinhado (_) na sua representação JSON.

<table>
    <tbody>
        <tr>
            <td valign="top"><p><strong>Propriedade</strong></p></td>
            <td valign="top"><p><strong>Utilizador pode ser definida ou gerada pelo sistema?</strong></p></td>
            <td valign="top"><p><strong>Objetivo</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>_rid</p></td>
            <td valign="top"><p>Sistema gerado</p></td>
            <td valign="top"><p>Gerado pelo sistema, identificador exclusivo e hierárquico do recurso</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_etag</p></td>
            <td valign="top"><p>Sistema gerado</p></td>
            <td valign="top"><p>ETag do recurso necessário para o controlo de simultaneidade optimista</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_ts</p></td>
            <td valign="top"><p>Sistema gerado</p></td>
            <td valign="top"><p>Última data/hora atualizada do recurso</p></td>
        </tr>
        <tr>
            <td valign="top"><p>auto</p></td>
            <td valign="top"><p>Sistema gerado</p></td>
            <td valign="top"><p>URI endereçável exclusivo do recurso</p></td>
        </tr>
        <tr>
            <td valign="top"><p>ID</p></td>
            <td valign="top"><p>Sistema gerado</p></td>
            <td valign="top"><p>Nome exclusivo do recurso (com o mesmo partição valor de chave) definidos pelo utilizador. Se o utilizador não especificar um id, um id será gerada pelo sistema</p></td>
        </tr>
    </tbody>
</table>

### <a name="wire-representation-of-resources"></a>Representação de esboço de recursos
DocumentDB não define a quaisquer extensões de proprietários para codificações JSON padrão ou especiais; funciona com documentos JSON compatível com padrão.  
 
### <a name="addressing-a-resource"></a>Endereçar um recurso
Todos os recursos são URI endereçável. O valor da propriedade de um recurso **auto** representa o URI relativo do recurso. O formato do URI consiste o /\<feed\>/ segmentos de caminho {_rid}:  

|Valor da auto |Descrição
|-------------------|-----------
|/DBS   |Feed de bases de dados numa conta de base de dados
|/DBS/ {dbName}  |Base de dados com um id correspondem ao valor {dbName}
|/colls/ /DBS/ {dbName}   |Feed das coleções numa base de dados
|/colls/ /DBS/ {dbName} {collName} |Coleções de sites com um id correspondem ao valor {collName}
|/colls/ /DBS/ {dbName} {collName} / documentos    |Feed de documentos numa coleção de
|/DBS/ {dbName} /colls/ {collName} /docs/ {ID do documento}    |Documento com um id correspondem ao valor {doc}
|/utilizadores/ /DBS/ {dbName}   |Feed de utilizadores numa base de dados
|/utilizadores/ /DBS/ {dbName} {ID de utilizador}   |Utilizador com um id correspondem ao valor {user}
|/utilizadores/ /DBS/ {dbName} {ID de utilizador} / permissões   |Feed das permissões de um utilizador
|/DBS/ {dbName} /utilizadores/ {ID de utilizador} /permissions/ {permissionId}    |Permissão com um id correspondem ao valor {permissão}
  
Cada recurso tem um nome de utilizador exclusivo definido exposto através da propriedade id. Nota: para documentos, se o utilizador não especificar um id nosso SDK suportado gera automaticamente um id exclusivo para o documento. O id é uma cadeia de definidas pelo utilizador, de até 256 caracteres que seja exclusivo dentro do contexto de um recurso principal específico. 

Cada recurso tem também um identificador de hierárquica de recursos de sistema gerado (também conhecido como um RID), que se encontra disponível através da propriedade _rid. O RID codifica a hierarquia inteira de um determinado recurso e é uma representação interna conveniente utilizada para impor integridade referencial de uma forma distribuída. O RID é exclusivo dentro de uma conta de base de dados e internamente é utilizado pelo DocumentDB para encaminhamento eficiente sem necessidade de pesquisas partição cruzada. Os valores da auto e as propriedades de _rid são alternativas e canónicos representações de um recurso. 

API do resto do DocumentDB suporta endereçamento de recursos e encaminhamento de pedidos de pelo id e as propriedades de _rid.

## <a name="database-accounts"></a>Contas de base de dados
Pode aprovisionar um ou mais DocumentDB da base de dados contas utilizar a sua subscrição Azure.

Pode [criar e gerir contas de base de dados DocumentDB](documentdb-create-account.md) através do Portal do Azure em [http://portal.azure.com/](https://portal.azure.com/). Criar e gerir uma conta de base de dados requer acesso administrativo e só podem ser executada em sua subscrição do Azure. 

### <a name="database-account-properties"></a>Propriedades da conta de base de dados
Como parte do aprovisionamento e gerir uma conta de base de dados pode configurar e leia as seguintes propriedades:  

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Nome da propriedade</strong></p></td>
            <td valign="top"><p><strong>Descrição</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Política de consistência</p></td>
            <td valign="top"><p>Defina esta propriedade para configurar o nível de consistência predefinida para todas as colecções sob a sua conta de base de dados. Pode substituir o nível de consistência numa base pedido por, utilizando o cabeçalho do pedido de [x ms consistência-nível-]. <p><p>Tenha em atenção que esta propriedade só se aplica a <i>recursos definidas pelo utilizador</i>. Todos os recursos definidos estão configurados para suportar lê/consultas sistema com consistência forte.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Teclas de autorização</p></td>
            <td valign="top"><p>Estas são as principais e secundárias chaves principal e só de leitura que fornecem acesso administrativo ao todos os recursos sob a conta de base de dados.</p></td>
        </tr>
    </tbody>
</table>

Tenha em atenção que, para além de aprovisionamento, configurar e gerir a sua conta de base de dados a partir do Portal do Azure, através de programação também pode criar e gerir contas de base de dados DocumentDB utilizando o [Azure DocumentDB REST APIs](https://msdn.microsoft.com/library/azure/dn781481.aspx) bem como [cliente SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx).  

## <a name="databases"></a>Bases de dados
Uma base de dados DocumentDB é um contentor lógico de um ou mais coleções de sites e utilizadores, conforme mostrado no seguinte diagrama. Pode criar qualquer número de bases de dados numa conta de base de dados DocumentDB sujeito a oferta de limites.  

![Modelo de hierárquico conta e colecções de base de dados][2]  
**Uma base de dados é um contentor lógico de utilizadores e coleções de sites**

Uma base de dados pode conter armazenamento de documentos praticamente ilimitado dividido por coleções de sites, que formam os domínios da transação para os documentos nelas contidas. 

### <a name="elastic-scale-of-a-documentdb-database"></a>Escala flexível de uma base de dados DocumentDB
Uma base de dados DocumentDB é flexível por predefinição – que se situa entre alguns GB para petabytes de armazenamento de documentos SSD cópia e aprovisionado débito. 

Ao contrário de uma base de dados RDBMS tradicional, uma base de dados DocumentDB não está confinado a um único computador. Com DocumentDB, tal como a escala da sua aplicação necessita de crescimento, pode criar coleções de mais, bases de dados ou ambas. Na verdade, várias aplicações de terceiros primeiros no interior da Microsoft têm utilizado com DocumentDB numa escala consumidor ao criar bases de dados de DocumentDB demasiado grandes cada contendo milhares de colecções com terabytes de armazenamento de documentos. Pode aumentar ou encolher uma base de dados ao adicionar ou remover coleções de sites para cumprir requisitos de escala da sua aplicação. 

Pode criar qualquer número de coleções de dentro de uma base de dados sujeito a oferta. Cada uma das coleções tem armazenamento SSD cópia e débito aprovisionada para si, consoante a camada de desempenho seleccionados.

Uma base de dados DocumentDB também é um contentor de utilizadores. Um utilizador, na ativar, é um espaço de nomes lógico para um conjunto de permissões que fornece extensivamente autorização e o acesso a coleções de sites, documentos e anexos.  
 
Tal como acontece com outros recursos no modelo de recursos de DocumentDB, bases de dados podem ser criados, substituído, eliminados, ler ou enumerado facilmente utilizando [Azure DocumentDB REST APIs](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou qualquer um do [SDK do cliente](https://msdn.microsoft.com/library/azure/dn781482.aspx). DocumentDB garante a consistência forte para ler ou consultar os metadados de um recurso de base de dados. Eliminar uma base de dados automaticamente assegura que não é possível aceder a qualquer uma das coleções de sites ou utilizadores nela contidas.   

## <a name="collections"></a>Coleções de sites
Uma coleção de DocumentDB é um contentor para os seus documentos JSON. Uma coleção de também é uma unidade de escala para transações e consultas. 

### <a name="elastic-ssd-backed-document-storage"></a>Armazenamento de documentos SSD cópia flexível
Uma coleção de é condições flexível - cresce automaticamente e encolhe à medida que adiciona ou remove documentos. Coleções de sites são recursos lógicos e podem abranger um ou mais partições físicas ou servidores. O número de partições dentro de uma coleção de é determinado pela DocumentDB baseada o tamanho de armazenamento e o débito aprovisionado da sua coleção. Todas as partições no DocumentDB tem um valor fixo de armazenamento de cópias SSD associado e são replicada para elevada disponibilidade. Gestão de partição totalmente é gerido pelo Azure DocumentDB e que não tem de escrever código complexo ou gerir as partições. Coleções de DocumentDB são **praticamente ilimitado** em termos de armazenamento e débito. 

### <a name="automatic-indexing-of-collections"></a>A indexação automática das coleções de sites
DocumentDB é um sistema de base de dados de esquema de royalties verdadeiro. Não partem do pressuposto ou exigir qualquer esquema para os documentos JSON. À medida que adiciona documentos para uma coleção de, DocumentDB automaticamente os índices e estiverem disponíveis para a consulta. Indexação automática de documentos sem necessidade de esquema ou índices secundários é uma funcionalidade de chave do DocumentDB e é activado por técnicas de manutenção de índice remissivo otimizada de escrita, bloquear de royalties e estruturados de registo. DocumentDB suporta constante volume de escritas extremamente rápidas enquanto ainda funcionar consultas consistentes. Armazenamento de documento e índice remissivo são utilizadas para calcular o armazenamento média consumido por cada coleção. Pode controlar os armazenamento e um desempenho compromissos associados ao configurar a política de indexação para uma coleção de indexação. 

### <a name="configuring-the-indexing-policy-of-a-collection"></a>Configurar a política de uma coleção de indexação
A política de cada coleção de indexação permite-lhe tornar o desempenho e armazenamento compromissos associados a indexação. Estão disponíveis para si como parte da configuração da indexação as seguintes opções:  

-   Escolha se a coleção de automaticamente índices todos os documentos ou não. Por predefinição, todos os documentos são indexados automaticamente. Pode escolher desativar a indexação automática e adicionar seletivamente apenas documentos específicos ao índice. Por outro lado, pode escolher seletivamente excluir apenas documentos específicos. É possível alcançar isto a definição da propriedade automática para ser VERDADEIRO ou FALSO no indexingPolicy de uma coleção de e utilizando o cabeçalho do pedido de [x-ms-indexingdirective] enquanto inserindo, substituir ou eliminar um documento.  
-   Escolha se pretende incluir ou excluir caminhos específicos ou padrões nos seus documentos do índice. É possível alcançar isto ao definição includedPaths e excludedPaths no indexingPolicy de uma coleção de respetivamente. Também pode configurar os armazenamento e um desempenho compromissos para consultas de intervalo e hash para padrões de caminho específico. 
-   Escolher entre síncrono (consistentes) e índice remissivo (preguiça) assíncrona atualizações. Por predefinição, o índice é atualizado modo síncrono em cada inserir, substituir ou eliminar de um documento para a coleção. Permite que as consultas satisfazer mesmo nível consistência como que o lê do documento. Enquanto DocumentDB está escrita otimizada e suporta constante volumes de documento escritas juntamente com a manutenção de índice remissivo síncrono e servir consultas consistentes, pode configurar determinadas coleções de sites para atualizar o seu índice lazily. A indexação lenta aumenta ainda mais o desempenho de escrita e é ideal para cenários de ingestão em volume para principalmente coleções de grande volume de leitura.

A política de indexação pode ser alterada através da execução de um local na coleção de. Isto pode ser obtido através do [SDK do cliente](https://msdn.microsoft.com/library/azure/dn781482.aspx), o [Portal do Azure](https://portal.azure.com) ou do [Azure DocumentDB REST APIs](https://msdn.microsoft.com/library/azure/dn781481.aspx).

### <a name="querying-a-collection"></a>Consultar uma coleção de
Os documentos numa coleção de podem ter arbitrários esquemas e pode consultar documentos numa coleção de sem facultar qualquer esquema ou upfront índices secundários. Pode consultar a coleção de utilizando o [sintaxe SQL de DocumentDB](https://msdn.microsoft.com/library/azure/dn782250.aspx), que fornece rich hierárquicas e relacionais e operadores espaciais e expansão através do UDFs com base em JavaScript. Gramática JSON permite a modelação documentos JSON como árvores com etiquetas de como os nós de árvore. Isto é tirar partido da tanto por técnicas de indexação automáticas do DocumentDB, bem como a linguagem SQL do DocumentDB. O idioma de consulta DocumentDB é constituído por três aspetos principais:   

1.  Em conjuntos pequenos de operações de consulta que transmitem mapear para a estrutura de árvore, incluindo consultas hierárquicas e projecções. 
2.  Um subconjunto de operações relacionais incluindo composição, filtrar, projecções, agregados e autonomamente associações. 
3.  JavaScript puro baseada UDFs que funcionam com (1) e (2).  

O modelo de consulta DocumentDB tentativas de obter um equilíbrio entre funcionalidade, a eficiência e simplificar. O motor de base de dados DocumentDB vierem compila e executa as instruções de consulta SQL. Pode consultar uma coleção de utilizar o [Azure DocumentDB REST APIs](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou de qualquer página do [cliente SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx). O .NET SDK vem com um fornecedor LINQ.

> [AZURE.TIP] Pode experimentar DocumentDB e executar consultas SQL contra a nossa conjunto de dados na [Consulta parques](https://www.documentdb.com/sql/demo).

### <a name="multi-document-transactions"></a>Transações com vários documentos
As transações de base de dados fornecem um modelo de programação seguro e previsível lidar com as alterações em simultâneo dos dados. No RDBMS, a forma tradicional de escrever a lógica empresarial é escrever **procedimentos armazenados** e/ou **Accionadores** e enviá-lo para o servidor de base de dados para transaccional execução. No RDBMS, o Programador de aplicação é necessário para lidar com duas linguagens de programação diferentes: 

- A aplicação (não transaccional) (por exemplo, JavaScript, Python, c#, Java, etc.) de linguagem de programação
- T-SQL, a linguagem de programação transaccional que vierem é executada pela base de dados

Nos termos do seu compromisso abrangente para JavaScript e JSON diretamente no motor de base de dados, DocumentDB fornece um modelo de programação intuitivo para lógica da aplicação JavaScript com base em execução diretamente nas coleções de ficheiros em termos de procedimentos armazenados e accionadores. Esta opção permite-para ambos os seguintes procedimentos:

- Controlar a execução eficaz de simultaneidade, recuperação, indexação dos gráficos do objecto JSON diretamente no motor da base de dados automática
- Expressar naturalmente o fluxo de controlo, a variável do controlo do âmbito, atribuição e integração de exceção processamento primitivas com as transações de base de dados diretamente em termos de JavaScript linguagem de programação

A lógica JavaScript registada num nível de coleções de sites, em seguida, pode emitir operações de base de dados nos documentos da coleção de determinado. DocumentDB implicitamente é moldado JavaScript com base procedimentos armazenados e accionadores dentro de um ambiente transações ácido com isolamento instantâneo nos documentos numa coleção. Durante a sua execução, se o JavaScript inicia uma exceção, em seguida, a transação inteira é cancelada. O modelo de programação resultante é muito simples mas poderosos. Os programadores de JavaScript obtém um modelo de programação "resistente" enquanto ainda utilizar as respetivas construções de linguagem familiares e primitivas de biblioteca.   

A capacidade de executar JavaScript diretamente no motor de base de dados no mesmo espaço de endereços do conjunto de memória intermédia permite performant e transaccional execução de operações de base de dados contra os documentos de uma coleção de. Além disso, motor de base de dados DocumentDB torna um compromisso abrangente para o JSON e JavaScript elimina qualquer erro de correspondência impedância entre os sistemas de tipo de aplicação e a base de dados.   

Depois de criar uma coleção, pode registar procedimentos armazenados, accionadores e UDFs uma coleção de utilizar o [Azure DocumentDB REST APIs](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou de qualquer página do [cliente SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx). Após o registo, pode referenciar e executá-los. Considere o seguinte procedimento armazenado escrito completamente em JavaScript, o código abaixo necessita de dois argumentos (nome do livro e nome do autor) e cria um novo documento, consultas para um documento e, em seguida, atualiza-la todas dentro de uma transação ÁCIDA implícita. Em qualquer ponto durante a execução, se uma exceção JavaScript é devolvida, interrompe a transação inteira.

    function businessLogic(name, author) {
        var context = getContext();
        var collectionManager = context.getCollection();        
        var collectionLink = collectionManager.getSelfLink()
            
        // create a new document.
        collectionManager.createDocument(collectionLink,
            {id: name, author: author},
            function(err, documentCreated) {
                if(err) throw new Error(err.message);
                
                // filter documents by author
                var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
                collectionManager.queryDocuments(collectionLink,
                    filterQuery,
                    function(err, matchingDocuments) {
                        if(err) throw new Error(err.message);
                        
                        context.getResponse().setBody(matchingDocuments.length);
                       
                        // Replace the author name for all documents that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].author = "George R. R. Martin";
                            // we don’t need to execute a callback because they are in parallel
                            collectionManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);   
                        }
                    })
            })
    };

O cliente pode "Enviar" a lógica JavaScript acima para a base de dados transaccional execução através de HTTP POST. Para mais informações sobre como utilizar métodos HTTP, consulte o artigo [RESTful interações com DocumentDB recursos](https://msdn.microsoft.com/library/azure/mt622086.aspx). 

    client.createStoredProcedureAsync(collection._self, {id: "CRUDProc", body: businessLogic})
       .then(function(createdStoredProcedure) {
            return client.executeStoredProcedureAsync(createdStoredProcedure.resource._self,
                "NoSQL Distilled",
                "Martin Fowler");
        })
        .then(function(result) {
            console.log(result);
        },
        function(error) {
            console.log(error);
        });


Repare que uma vez que a base de dados vierem JSON compreende e pode utilizar JavaScript, não há nenhum correspondência de sistema, "Mapeamento ou" ou mágico de geração código necessário.   

Procedimentos armazenados e accionadores interagem com uma colecção de e os documentos numa coleção de através de um modelo de objeto bem definido, expõe o contexto atual de coleções de sites.  

Coleções no DocumentDB podem ser criadas, eliminados, ler ou enumerado facilmente a utilizar o [Azure DocumentDB REST APIs](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou de qualquer página do [cliente SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx). DocumentDB sempre fornece consistência forte para ler ou consultar os metadados de uma coleção de. Eliminar uma coleção de automaticamente assegura que que não é possível aceder a qualquer dos documentos, anexos, procedimentos armazenados, accionadores e UDFs nela contidas.   

## <a name="stored-procedures-triggers-and-user-defined-functions-udf"></a>Procedimentos armazenados, accionadores e funções definidas pelo utilizador (UDF)
Conforme descrito na secção anterior, pode escrever lógica da aplicação para executar diretamente dentro de uma transação dentro do motor de base de dados. Lógica da aplicação pode ser escrita totalmente na JavaScript e pode ser considerada um procedimento armazenado, accionador ou um UDF. O código JavaScript dentro de um procedimento armazenado ou um accionador pode inserir, substituir, eliminar, documentos de leitura ou de consulta numa coleção. Por outro lado, JavaScript dentro de um UDF não é possível inserir, substituir ou eliminar documentos. UDFs enumerar os documentos do conjunto de resultados de uma consulta e produzem outro conjunto de resultados. Para multitenancy, DocumentDB impõe uma governação de recursos de reserva estritamente com base. Cada um procedimento armazenado, accionador ou um UDF obtém um quantum fixo dos recursos de sistema operativo para fazer o seu trabalho. Além disso, procedimentos armazenados, accionadores ou UDFs não podem ligar-se contra externas JavaScript bibliotecas e na lista negra se excederem os orçamentos recurso atribuídos às mesmas. Pode registar, anular o registo de procedimentos armazenados, accionadores ou UDFs uma coleção de utilizando as API REST.  Durante o registo um procedimento armazenado, accionador ou um UDF é compilado previamente e armazenado como código de bytes que obtém executado mais tarde. A secção que se segue mostram como pode utilizar o SDK do JavaScript DocumentDB para registar, executar e anular registo um procedimento armazenado, acionador e um UDF. O SDK JavaScript é um dispositivo de moldagem simple ao longo do [DocumentDB REST APIs](https://msdn.microsoft.com/library/azure/dn781481.aspx). 

### <a name="registering-a-stored-procedure"></a>Registar um procedimento armazenado
Registo de um procedimento armazenado cria um novo recurso procedimento armazenado numa coleção de através de HTTP POST.  

    var storedProc = {
        id: "validateAndCreate",
        body: function (documentToCreate) {
            documentToCreate.id = documentToCreate.id.toUpperCase();
            
            var collectionManager = getContext().getCollection();
            collectionManager.createDocument(collectionManager.getSelfLink(),
                documentToCreate,
                function(err, documentCreated) {
                    if(err) throw new Error('Error while creating document: ' + err.message;
                    getContext().getResponse().setBody('success - created ' + 
                            documentCreated.name);
                });
        }
    };
    
    client.createStoredProcedureAsync(collection._self, storedProc)
        .then(function (createdStoredProcedure) {
            console.log("Successfully created stored procedure");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-stored-procedure"></a>Executar um procedimento armazenado
Execução de um procedimento armazenado é efectuada por emissão uma mensagem de HTTP relativamente a um recurso de procedimento armazenado existente, passando parâmetros para o procedimento descrito no corpo do pedido.

    var inputDocument = {id : "document1", author: "G. G. Marquez"};
    client.executeStoredProcedureAsync(createdStoredProcedure.resource._self, inputDocument)
        .then(function(executionResult) {
            assert.equal(executionResult, "success - created DOCUMENT1");
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-stored-procedure"></a>Anular o registo de um procedimento armazenado
Anular o registo de um procedimento armazenado simplesmente é executado por emitir um eliminar HTTP relativamente a um recurso de procedimento armazenado existente.   

    client.deleteStoredProcedureAsync(createdStoredProcedure.resource._self)
        .then(function (response) {
            return;
        }, function(error) {
            console.log("Error");
        });


### <a name="registering-a-pre-trigger"></a>Registar um acionador de pré-lançamento
Registo de um accionador é feito criando um novo recurso accionador numa coleção de através de HTTP POST. Pode especificar se o accionador é um tipo de ficheiro ou um accionador de mensagem e o tipo de operação podem ser associado a (por exemplo, criar, substituir, eliminar ou todos).   

    var preTrigger = {
        id: "upperCaseId",
        body: function() {
                var item = getContext().getRequest().getBody();
                item.id = item.id.toUpperCase();
                getContext().getRequest().setBody(item);
        },
        triggerType: TriggerType.Pre,
        triggerOperation: TriggerOperation.All
    }
    
    client.createTriggerAsync(collection._self, preTrigger)
        .then(function (createdPreTrigger) {
            console.log("Successfully created trigger");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-pre-trigger"></a>Executar um acionador de pré-lançamento
Execução de um accionador é feita, especificando o nome de um accionador existente no momento da emitir o pedido de mensagem/colocar/eliminar de um recurso de documento através do cabeçalho do pedido.  
 
    client.createDocumentAsync(collection._self, { id: "doc1", key: "Love in the Time of Cholera" }, { preTriggerInclude: "upperCaseId" })
        .then(function(createdDocument) {
            assert.equal(createdDocument.resource.id, "DOC1");
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-pre-trigger"></a>Anular o registo de um acionador de pré-lançamento
Anular o registo de um accionador simplesmente é feito através de emissão de eliminar um HTTP relativamente a um recurso de accionador existente.  

    client.deleteTriggerAsync(createdPreTrigger._self);
        .then(function(response) {
            return;
        }, function(error) {
            console.log("Error");
        });

### <a name="registering-a-udf"></a>Registar um UDF
Registo de um UDF é feito criando um novo recurso UDF numa coleção de através de HTTP POST.  

    var udf = { 
        id: "mathSqrt",
        body: function(number) {
                return Math.sqrt(number);
        },
    };
    client.createUserDefinedFunctionAsync(collection._self, udf)
        .then(function (createdUdf) {
            console.log("Successfully created stored procedure");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-udf-as-part-of-the-query"></a>Executar um UDF como parte da consulta
Um UDF pode ser especificado como parte da consulta SQL e será utilizado como uma forma para alargar o [idioma de consulta SQL do DocumentDB](https://msdn.microsoft.com/library/azure/dn782250.aspx)core.

    var filterQuery = "SELECT udf.mathSqrt(r.Age) AS sqrtAge FROM root r WHERE r.FirstName='John'";
    client.queryDocuments(collection._self, filterQuery).toArrayAsync();
        .then(function(queryResponse) {
            var queryResponseDocuments = queryResponse.feed;
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-udf"></a>Anular o registo de um UDF 
Anular o registo de um UDF simplesmente é executado por emitir um eliminar HTTP relativamente a um recurso UDF existente.  

    client.deleteUserDefinedFunctionAsync(createdUdf._self)
        .then(function(response) {
            return;
        }, function(error) {
            console.log("Error");
        });

Apesar de fragmentos acima mostrava o registo (POST), anulação do registo (local), ler/lista (GET) e execução (POST) através do [DocumentDB JavaScript SDK](https://github.com/Azure/azure-documentdb-js), também pode utilizar os [REST APIs](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou de outro [cliente SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx). 

## <a name="documents"></a>Documentos
Pode inserir, substituir, eliminar, ler, enumerar e consultar arbitrários JSON documentos numa coleção. DocumentDB não define a qualquer esquema e não requer índices secundários para poder consultar sobre documentos numa coleção de suporte.   

A ser um serviço de base de dados verdadeiramente aberta, DocumentDB não inventem quaisquer tipos de dados especializadas (por exemplo, data hora) ou codificações específicas para documentos JSON. Tenha em atenção que DocumentDB não requer quaisquer convenções JSON especiais para codificar as relações entre vários documentos; a sintaxe SQL de DocumentDB fornece muito poderosa consulta hierárquica e relacional operadores a consulta e o project documentos sem qualquer anotações especiais ou necessidade de codificar relações entre documentos utilizando distinguem propriedades.  
 
Como com todos os outros recursos, documentos podem ser criados, substituído, eliminadas, leia, enumerados e consultado facilmente utilizando REST APIs ou qualquer um do [SDK do cliente](https://msdn.microsoft.com/library/azure/dn781482.aspx). Eliminar um documento instantaneamente liberta a quota de correspondentes a todos os anexos aninhados. O nível de leitura consistência de documentos a seguir a política de consistência da conta de base de dados. Esta política pode ser substituída numa base por pedido dependendo requisitos de consistência de dados da sua aplicação. Quando consultar documentos, a leitura consistência segue o modo de indexação definir na coleção de. Para "consistente", isto segue política de consistência da conta. 

## <a name="attachments-and-media"></a>Anexos e conteúdo multimédia
>[AZURE.NOTE] Recursos do anexo e conteúdo multimédia são funcionalidades de pré-visualização.
 
DocumentDB permite-lhe armazenar binária blobs/multimédia com DocumentDB ou ao seu próprio arquivo remota de multimédia. -Lo também permite-lhe representar os metadados da multimédia em termos de um documento especial denominado anexo. Um anexo no DocumentDB é um documento (JSON) especial que referencia multimédia/blob armazenado noutro local. Um anexo é simplesmente um documento especial que captura (por exemplo, localização, autor, etc.) os metadados da armazenados num armazenamento remota de multimédia de multimédia. 

Considere uma aplicação de leitura de rede social que utiliza DocumentDB para armazenar anotações a tinta e metadados incluindo comentários, realça, marcadores, classificações, etc., de um livro de "e" de um determinado utilizador associado ao gostos/agrada.   

-   O conteúdo do livro propriamente dito está armazenado no armazenamento de multimédia quer disponíveis como parte da conta de base de dados DocumentDB ou um arquivo de multimédia remoto. 
-   Uma aplicação poderão armazenar metadados de cada utilizador como um documento distinto – por exemplo, os metadados de João para Livro1 está armazenado num documento referenciado por /colls/joe/docs/book1. 
-   Anexos apontar para as páginas de conteúdo de um determinado livro de um utilizador são armazenados em documento correspondente, por exemplo, /colls/joe/docs/book1/chapter1 /colls/joe/docs/book1/chapter2, etc. 

Note que os exemplos listados acima utilizam ids amigáveis para transmitir a hierarquia de recursos. Recursos são acedidos através da API do resto através de ids exclusivos recurso. 

Para que os elementos de multimédia que são geridos pelo DocumentDB, a propriedade _media do anexo irá referenciar os elementos multimédia pelo respetivo URI. DocumentDB irá Certifique-se para o lixo recolher os elementos multimédia quando todas as referências pendentes desaparecem. DocumentDB gera o anexo quando carregar o novo suporte e preenche _media para apontarem para os elementos de multimédia recentemente adicionados automaticamente. Se escolher armazenar os elementos multimédia num arquivo remoto de BLOBs gerido pelo utilizador (por exemplo, o OneDrive, o armazenamento do Windows Azure, DropBox etc), ainda pode utilizar anexos para fazer referência aos elementos multimédia. Neste caso, será criado o anexo por si e preencher a respetiva propriedade _media.   

Tal como acontece com todos os outros recursos, anexos podem ser criados, substituído, eliminados, ler ou enumerado facilmente utilizando REST APIs ou qualquer um do SDK do cliente. Tal como acontece com documentos, o nível de leitura consistência de anexos segue-se a política de consistência da conta de base de dados. Esta política pode ser substituída numa base por pedido dependendo requisitos de consistência de dados da sua aplicação. Quando estiver a consultar de anexos, a leitura consistência segue o modo de indexação definir na coleção de. Para "consistente", isto segue política de consistência da conta. 
 
## <a name="users"></a>Utilizadores
Um utilizador DocumentDB representa um espaço de nomes lógico para agrupar as permissões. Um utilizador DocumentDB pode corresponder a um utilizador no sistema de gestão de identidades ou de uma função de aplicação predefinidos. Para DocumentDB, um utilizador simplesmente representa uma abstracção para agrupar um conjunto de permissões numa base de dados.   

Para implementar multitenancy na sua aplicação, pode criar utilizadores no DocumentDB que corresponde para os seus utilizadores reais ou inquilinos da sua aplicação. Em seguida, pode criar permissões para um determinado utilizador que correspondem ao controlo de acesso através de vários coleções de sites, documentos, anexos, etc.   

Como as suas aplicações necessitarem dimensionar com o crescimento de utilizador, pode tomar várias formas de shard os seus dados. Pode modelar cada um dos seus utilizadores da seguinte forma:   

-   Cada utilizador liga a uma base de dados.
-   Para uma coleção de cada utilizador mapas. 
-   Documentos correspondente a vários utilizadores Ir para uma coleção de dedicada. 
-   Documentos correspondente a vários utilizadores aceda a um conjunto de coleções de sites.   

Independentemente da estratégia sharding específicos que escolher, pode modelar os seus utilizadores reais como utilizadores DocumentDB base de dados e associar permissões detalhadas ajustar a cada utilizador.  

![Coleções de utilizador][3]  
**Estratégias de sharding e modelação de utilizadores**

Como todos os outros recursos, os utilizadores na DocumentDB podem ser criados, substituído, eliminados, ler ou enumerado facilmente utilizando REST APIs ou qualquer um do SDK do cliente. DocumentDB sempre fornece consistência forte para ler ou consultar os metadados de um recurso de utilizador. Vale destacam que eliminar um utilizador automaticamente garante que as que não é possível aceder a qualquer uma das permissões nela contidas. Apesar da DocumentDB recupera a quota das permissões como parte do utilizador eliminado no fundo, as permissões eliminadas está disponível instantaneamente novamente para utilizar.  

## <a name="permissions"></a>Permissões
A partir de uma perspetiva de controlo de acesso, recursos, tais como contas de base de dados, bases de dados, os utilizadores e permissões são considerados recursos *administrativos* uma vez que estas requerem permissões administrativas. Por outro lado, recursos, incluindo as coleções de sites, documentos, anexos, procedimentos armazenados, accionadores e UDFs são confinados numa determinada base de dados e considerados *recursos da aplicação*. Correspondente para os dois tipos de recursos e as funções que aceder aos mesmos (nomeadamente o administrador e o utilizador), o modelo de autorização define dois tipos de *teclas de acesso*: *chave principal* e a *chave de recurso*. A chave principal é uma parte da conta à base de dados e é fornecida para o programador (ou administrador) que é de aprovisionamento de conta de base de dados. Esta chave do modelo global de tem semântica de administrador, que pode ser utilizada para autorizar o acesso aos recursos administrativos e de aplicação. Em contrapartida, uma chave de recurso é uma tecla de acesso granulares que permita o acesso a um recurso de aplicação *específico* . Capturar por conseguinte, a relação entre o utilizador de uma base de dados e as permissões que o utilizador tem para um recurso específico (por exemplo, coleção, documento, anexo, procedimento armazenado, accionador ou UDF).   

A única forma para obter uma chave de recurso é através da criação de um recurso de permissão num determinado utilizador. Tenha em atenção que para criar ou obter uma permissão, uma chave de modelo global de deve ser apresentada no cabeçalho da autorização. Um recurso de permissão une recurso, o access e o utilizador. Depois de criar um recurso de permissão, o utilizador tem apenas apresentar a chave de recursos associados para poder aceder ao recurso relevante. Por conseguinte, uma chave de recurso pode ser visualizada como uma representação lógica e compacta do recurso permissão.  

Tal como acontece com todos os outros recursos, permissões no DocumentDB podem ser criadas, substituído, eliminados, ler ou enumerado facilmente utilizando REST APIs ou qualquer um do SDK do cliente. DocumentDB sempre fornece consistência forte para ler ou consultar os metadados de uma permissão. 

## <a name="next-steps"></a>Próximos passos
Saiba mais sobre como trabalhar com recursos através dos comandos HTTP no [RESTful interações com DocumentDB recursos](https://msdn.microsoft.com/library/azure/mt622086.aspx).


[1]: media/documentdb-resources/resources1.png
[2]: media/documentdb-resources/resources2.png
[3]: media/documentdb-resources/resources3.png

