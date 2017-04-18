<properties 
    pageTitle="Programação DocumentDB: procedimentos armazenados, accionadores da base de dados e UDFs | Microsoft Azure" 
    description="Saiba como utilizar DocumentDB para escrever procedimentos armazenados, accionadores da base de dados e funções definidas pelo utilizador (UDFs) em JavaScript. Obter sugestões de programing de base de dados e muito mais." 
    keywords="Accionadores da base de dados, procedimento armazenado, procedimento armazenado, o programa de base de dados, sproc, documentdb, azure, Microsoft azure"
    services="documentdb" 
    documentationCenter="" 
    authors="aliuy" 
    manager="jhubbard" 
    editor="mimig"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/14/2016" 
    ms.author="andrl"/>

# <a name="documentdb-server-side-programming-stored-procedures-database-triggers-and-udfs"></a>Programação do lado do servidor DocumentDB: procedimentos armazenados, accionadores da base de dados e UDFs

Saiba como o idioma do Azure DocumentDB integradas, transaccional execução dos JavaScript permite que os programadores escrever **procedimentos armazenados**, **Accionadores** e **funções (UDFs) definidas pelo utilizador** vierem em JavaScript. Esta opção permite-lhe escrever lógica de aplicação de programa de base de dados que pode ser enviada e executada diretamente nas partições de armazenamento de base de dados 

Recomendamos que a introdução ao ver o vídeo seguinte, onde o André Liu fornece uma breve introdução ao modelo de programação do DocumentDB base de dados do lado do servidor. 

> [AZURE.VIDEO azure-demo-a-quick-intro-to-azure-documentdbs-server-side-javascript]

Em seguida, volte a este artigo qual irá aprender as respostas para as seguintes questões:  

- Como escrever uma um procedimento armazenado, accionador ou UDF utilizando JavaScript?
- Como é que o DocumentDB garante ácido?
- Como funcionam as transações na DocumentDB?
- O que são accionadores pré e pós accionadores e como escrever uma?
- Como registar e executar um procedimento armazenado, accionador ou UDF de uma forma RESTful através de HTTP?
- O que estão disponível para criar e executar DocumentDB SDK procedimentos armazenados, accionadores e UDFs?

## <a name="introduction-to-stored-procedure-and-udf-programming"></a>Introdução à programação de UDF e procedimento armazenado

Esta abordagem de *"JavaScript como um dia moderno T-SQL"* liberta os programadores de aplicações a partir de complexidade de erros de correspondência do tipo sistema e de tecnologias de mapeamento relacional do objeto. Também tem um número das vantagens intrínsecos que pode ser utilizada para criar aplicações completas:  

-   **Lógica de procedimento:** JavaScript como um nível de alto programação idioma, fornece uma interface familiar e formatada para expressar lógica empresarial. Pode executar complexas sequências de operações de perto aos dados.

-   **Transações atómica:** DocumentDB garante que as operações de base de dados executadas dentro de um único procedimento armazenado ou acionador são atómica. Permite uma aplicação combinar operações relacionadas num único lote para que todos os têm êxito ou nenhuma teve êxito. 

-   **Desempenho:** O facto de JSON condições é mapeado para o sistema de tipo de idioma Javascript e também é a unidade básica de armazenamento no DocumentDB permite de um número de otimizações como preguiça ocorrência de documentos JSON no conjunto de memória intermédia e tornando-as disponível a pedido para executar o código. Existem mais benefícios de desempenho associados a lógica empresarial envio à base de dados:
    -   Lotes – os programadores podem agrupar as operações como inserções e enviá-las em massa. A latência de tráfego de rede de custos e o overhead de arquivo para criar as transações separadas são reduzidos significativamente. 
    -   Pré-compilação – DocumentDB precompiles accionadores e funções definidas pelo utilizador (UDFs) para evitar custos de compilação JavaScript para cada invocação de procedimentos armazenados. A sobrecarga básicos para construir o código de byte para a lógica procedimento seja amortizada a um valor mínimo.
    -   Sequência – necessidade de muitas operações um lado-efeito ("accionador") que envolve potencialmente efetuar um ou vários operações de arquivo secundário. Para além da atomicidade, este é mais performant quando movidos para o servidor. 
-   **Encapsulamento:** Procedimentos armazenados podem ser utilizados para agrupar lógica empresarial num só local. Isto tem duas vantagens:
    -   Esta opção adiciona uma camada de produção na parte superior dos dados não processados, que permite aos arquitectura dados evoluir suas aplicações independentemente dos dados. Isto é particularmente vantajoso quando os dados são menos esquema, devido aos pressupostos frágil que poderão ter de alimentos para a aplicação se tiverem fazer em relação aos dados diretamente.  
    -   Este produção permite-mantenha os respetivos dados seguro, simplificar aceder a partir dos scripts de empresas.  

A criação e execução dos accionadores da base de dados, procedimento armazenado e operadores de consulta personalizadas é suportada através da [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx), [DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio/releases)e [cliente SDK](documentdb-sdk-dotnet.md) no muitas plataformas incluindo .NET, Node.js e JavaScript.

**Que neste tutorial utiliza o [SDK Node.js com as perguntas promessas](http://azure.github.io/azure-documentdb-node-q/) ** para ilustrar sintaxe e a utilização de procedimentos armazenados, accionadores e UDFs.   

## <a name="stored-procedures"></a>Procedimentos armazenados

### <a name="example-write-a-simple-stored-procedure"></a>Exemplo: Escrever um procedimento armazenado simple 
Vamos começar com um procedimento armazenado simple que devolve uma resposta "Olá mundo".

    var helloWorldStoredProc = {
        id: "helloWorld",
        body: function () {
            var context = getContext();
            var response = context.getResponse();
    
            response.setBody("Hello, World");
        }
    }


Procedimentos armazenados estão registados por coleção e podem funcionar em qualquer documento e o anexo presente nessa coleção. O fragmento que se segue mostra como registar o procedimento helloWorld armazenado com uma coleção de. 

    // register the stored procedure
    var createdStoredProcedure;
    client.createStoredProcedureAsync('dbs/testdb/colls/testColl', helloWorldStoredProc)
        .then(function (response) {
            createdStoredProcedure = response.resource;
            console.log("Successfully created stored procedure");
        }, function (error) {
            console.log("Error", error);
        });


Assim que o procedimento armazenado está registado, podemos pode executá-la contra a coleção e leia os resultados novamente no cliente. 

    // execute the stored procedure
    client.executeStoredProcedureAsync('dbs/testdb/colls/testColl/sprocs/helloWorld')
        .then(function (response) {
            console.log(response.result); // "Hello, World"
        }, function (err) {
            console.log("Error", error);
        });


O objeto de contexto fornece acesso a todas as operações que pode ser executada em armazenamento DocumentDB e acesso aos objectos pedidos e respostas. Neste caso, utilizámos o objeto de resposta para definir o corpo da resposta que foi enviada para o cliente. Para obter mais detalhes, consulte o [servidor de DocumentDB JavaScript documentação SDK](http://azure.github.io/azure-documentdb-js-server/).  

Diga-nos expandir on Este exemplo e adicione que mais da base de dados relacionada a funcionalidade para o procedimento armazenado. Procedimentos armazenados podem criar, atualizar, ler, de consulta e eliminar documentos e anexos dentro da coleção.    

### <a name="example-write-a-stored-procedure-to-create-a-document"></a>Exemplo: Escrever um procedimento armazenado para criar um documento 
O fragmento de seguinte mostra como utilizar o objeto de contexto para interagir com DocumentDB recursos.

    var createDocumentStoredProc = {
        id: "createMyDocument",
        body: function createMyDocument(documentToCreate) {
            var context = getContext();
            var collection = context.getCollection();

            var accepted = collection.createDocument(collection.getSelfLink(),
                  documentToCreate,
                  function (err, documentCreated) {
                      if (err) throw new Error('Error' + err.message);
                      context.getResponse().setBody(documentCreated.id)
                  });
            if (!accepted) return;
        }
    }


Este procedimento armazenado leva como entrada documentToCreate, o corpo de um documento a ser criada na coleção de atual. Todas as operações essas são assíncronas e dependem JavaScript função as chamadas de retorno. A função de chamada de retorno tem dois parâmetros, uma para o objeto de erro no caso de que falha a operação e outra para o objeto criado. Dentro de chamada de retorno, os utilizadores podem gerir a exceção ou accionar um erro. No caso de uma chamada de retorno não for fornecida e ocorre um erro, o runtime DocumentDB emitir um erro.   

No exemplo acima, a chamada de retorno emitir um erro se a operação falhou. Caso contrário, define o id do documento criado como o corpo de resposta para o cliente. Eis como este procedimento armazenado é executado com parâmetros de entrada.

    // register the stored procedure
    client.createStoredProcedureAsync('dbs/testdb/colls/testColl', createDocumentStoredProc)
        .then(function (response) {
            var createdStoredProcedure = response.resource;
    
            // run stored procedure to create a document
            var docToCreate = {
                id: "DocFromSproc",
                book: "The Hitchhiker’s Guide to the Galaxy",
                author: "Douglas Adams"
            };
    
            return client.executeStoredProcedureAsync('dbs/testdb/colls/testColl/sprocs/createMyDocument',
                  docToCreate);
        }, function (error) {
            console.log("Error", error);
        })
    .then(function (response) {
        console.log(response); // "DocFromSproc"
    }, function (error) {
        console.log("Error", error);
    });

    
Tenha em atenção que este procedimento armazenado pode ser modificado para tirar uma matriz de organismos documento como entrada e criá-los na execução do procedimento armazenado mesmo em vez de vários pedidos de rede para criar cada um deles individualmente. Isto pode ser utilizado para implementar um importador eficiente em volume para DocumentDB (outros fabricantes referido mais tarde neste tutorial).   

O exemplo descrito demonstrado como utilizar os procedimentos armazenados. Abordaremos accionadores e funções definidas pelo utilizador (UDFs) mais tarde no tutorial.

## <a name="database-program-transactions"></a>Transações do programa de base de dados
Transação numa base de dados típica pode ser definida como uma sequência de operações efetuadas como uma única unidade lógica de trabalho. Cada transação fornece **ácido garantias**. ÁCIDO é um acrónimo famoso que designa quatro propriedades - atomicidade, consistência, isolamento e durabilidade.  

Resumidamente, atomicidade garante que todos os o trabalho efetuado dentro de uma transação é tratado como uma única unidade onde quer-está empenhada ou nenhum. Consistência assegura que os dados são sempre em bom estado interno em transações. Isolamento garante que não existem duas operações interferem com outros – geralmente, sistemas mais comerciais fornecem vários níveis de isolamento que podem ser utilizados com base nas necessidades de aplicação. Durabilidade assegura que quaisquer alterações que está empenhada na base de dados será sempre apresentar.   

No DocumentDB, JavaScript está alojado no mesmo espaço de memória como a base de dados. Por conseguinte, os pedidos feitos dentro de procedimentos armazenados e accionadores executem no mesmo âmbito de uma sessão de base de dados. Permite que DocumentDB garantir ácido para todas as operações que fazem parte de um único procedimento/accionador armazenado. Considere a seguinte definição do procedimento armazenado:

    // JavaScript source code
    var exchangeItemsSproc = {
        name: "exchangeItems",
        body: function (playerId1, playerId2) {
            var context = getContext();
            var collection = context.getCollection();
            var response = context.getResponse();
    
            var player1Document, player2Document;
    
            // query for players
            var filterQuery = 'SELECT * FROM Players p where p.id  = "' + playerId1 + '"';
            var accept = collection.queryDocuments(collection.getSelfLink(), filterQuery, {},
                function (err, documents, responseOptions) {
                    if (err) throw new Error("Error" + err.message);
    
                    if (documents.length != 1) throw "Unable to find both names";
                    player1Document = documents[0];
    
                    var filterQuery2 = 'SELECT * FROM Players p where p.id = "' + playerId2 + '"';
                    var accept2 = collection.queryDocuments(collection.getSelfLink(), filterQuery2, {},
                        function (err2, documents2, responseOptions2) {
                            if (err2) throw new Error("Error" + err2.message);
                            if (documents2.length != 1) throw "Unable to find both names";
                            player2Document = documents2[0];
                            swapItems(player1Document, player2Document);
                            return;
                        });
                    if (!accept2) throw "Unable to read player details, abort ";
                });
    
            if (!accept) throw "Unable to read player details, abort ";
    
            // swap the two players’ items
            function swapItems(player1, player2) {
                var player1ItemSave = player1.item;
                player1.item = player2.item;
                player2.item = player1ItemSave;
    
                var accept = collection.replaceDocument(player1._self, player1,
                    function (err, docReplaced) {
                        if (err) throw "Unable to update player 1, abort ";
    
                        var accept2 = collection.replaceDocument(player2._self, player2,
                            function (err2, docReplaced2) {
                                if (err) throw "Unable to update player 2, abort"
                            });
    
                        if (!accept2) throw "Unable to update player 2, abort";
                    });
    
                if (!accept) throw "Unable to update player 1, abort";
            }
        }
    }
    
    // register the stored procedure in Node.js client
    client.createStoredProcedureAsync(collection._self, exchangeItemsSproc)
        .then(function (response) {
            var createdStoredProcedure = response.resource;
        }
    );

Este procedimento armazenado utiliza transações dentro de uma aplicação de jogos a itens de comércio entre dois intervenientes numa única operação. O procedimento armazenado tenta ler dois documentos cada correspondente para os IDs de leitor transmitido como um argumento. Se forem encontrados em ambos os documentos player, em seguida, o procedimento armazenado atualiza os documentos trocar os seus itens. Se forem encontrados erros ao longo da forma,-inicia uma exceção de JavaScript que implicitamente interrompe a transação.

Se a coleção de procedimento armazenado está registada contra é uma coleção de partição único, em seguida, a transação está confinada a todos os docuemnts dentro da coleção. Se tiver partições a coleção, procedimentos armazenados são executados no âmbito da transação de uma chave de partição única. Cada armazenado execução do procedimento, em seguida, tem de incluir um valor de chave partição correspondente para o âmbito a transação tem de ser executado. Para obter mais detalhes, consulte o artigo [Criar partições DocumentDB](documentdb-partition-data.md).

### <a name="commit-and-rollback"></a>Consolidar e anular
Transações estão fortemente e vierem integradas no modelo de programação do DocumentDB JavaScript. Dentro de uma função de JavaScript, todas as operações são automaticamente moldadas numa única transação. Se o JavaScript completar sem qualquer exceção, as operações à base de dados são consolidadas. Na verdade, os "Começar a operação" e "CONSOLIDAR operação" as instruções nas bases de dados relacionais são implícitas no DocumentDB.  
 
Se existir uma exceção que é propagada o script, runtime de JavaScript do DocumentDB irá recuperar a transação inteira. Como é mostrado no exemplo anterior, deitar uma exceção é eficazmente equivalente a uma "transação anulação" no DocumentDB.
 
### <a name="data-consistency"></a>Consistência dos dados
Procedimentos armazenados e accionadores sempre são executadas na réplica principal da coleção de DocumentDB. Este procedimento garante que lê a partir de dentro armazenados procedimentos oferta forte consistência. Consultas utilizando as funções definidas pelo utilizador podem ser executadas no principal ou qualquer réplica secundária, mas recomendamos Certifique-se para cumprir o nível de consistência pedida ao selecionar a réplica adequada.

## <a name="bounded-execution"></a>Execução delimitada
Todas as operações de DocumentDB tem de concluir dentro do servidor especificado pedir a duração de tempo limite. Este constrangimento também se aplica ao JavaScript funções (procedimentos armazenados, accionadores e as funções definidas pelo utilizador). Se uma operação não for concluído com esse limite de tempo, a transação é revertida. Funções de JavaScript tem de concluir dentro do prazo ou implementar um modelo de continuação de notas com base para lote/currículo execução.  

Para simplificar a programação de procedimentos armazenados e accionadores para processar limites de tempo, todas as funções no objeto de coleções de sites (para criar, ler, substituir e eliminar dos documentos e anexos) devolvem um valor booleano que representa se essa operação irá concluir. Se este valor for FALSO, é uma indicação de que o limite de tempo está prestes a expirar e que o procedimento tem moldar o execução.  Operações em fila de espera antes da operação de arquivo deles não aceites primeiro são garantir para concluir se o procedimento armazenado conclui em tempo e não em fila quaisquer pedidos mais.  

Funções de JavaScript também são delimitadas no consumo de recursos. DocumentDB reserva débito por coleção baseado no aprovisionada tamanho de uma conta de base de dados. Débito é expressa em termos de uma unidade normalizada de memória e CPU, a consumo IO denominada unidades pedido ou RUs. Funções de JavaScript potencialmente podem utilizar para cima um grande número de RUs dentro de uma hora abreviada e poderão obter limitado taxa se a coleção é atingido. Procedimentos armazenados intensivos de recursos também poderão ser colocada em quarentena para garantir a disponibilidade de operações de base de dados primitivos.  

### <a name="example-bulk-importing-data-into-a-database-program"></a>Exemplo: Em volume importação de dados para um programa de base de dados
Abaixo está um exemplo de um procedimento armazenado escrito em volume de importação documentos para uma coleção de. Tenha em atenção como o procedimento armazenado trata execução delimitada ao selecionar o valor booleano devolver o valor de createDocument e, em seguida, utiliza a contagem de documentos inseridas em cada invocação do procedimento armazenado para controlar e retomar o progresso através de secções.

    function bulkImport(docs) {
        var collection = getContext().getCollection();
        var collectionLink = collection.getSelfLink();
    
        // The count of imported docs, also used as current doc index.
        var count = 0;
    
        // Validate input.
        if (!docs) throw new Error("The array is undefined or null.");
    
        var docsLength = docs.length;
        if (docsLength == 0) {
            getContext().getResponse().setBody(0);
        }
    
        // Call the create API to create a document.
        tryCreate(docs[count], callback);
    
        // Note that there are 2 exit conditions:
        // 1) The createDocument request was not accepted. 
        //    In this case the callback will not be called, we just call setBody and we are done.
        // 2) The callback was called docs.length times.
        //    In this case all documents were created and we don’t need to call tryCreate anymore. Just call setBody and we are done.
        function tryCreate(doc, callback) {
            var isAccepted = collection.createDocument(collectionLink, doc, callback);
    
            // If the request was accepted, callback will be called.
            // Otherwise report current count back to the client, 
            // which will call the script again with remaining set of docs.
            if (!isAccepted) getContext().getResponse().setBody(count);
        }
    
        // This is called when collection.createDocument is done in order to process the result.
        function callback(err, doc, options) {
            if (err) throw err;
    
            // One more document has been inserted, increment the count.
            count++;
    
            if (count >= docsLength) {
                // If we created all documents, we are done. Just set the response.
                getContext().getResponse().setBody(count);
            } else {
                // Create next document.
                tryCreate(docs[count], callback);
            }
        }
    }

## <a id="trigger"></a>Accionadores da base de dados
### <a name="database-pre-triggers"></a>Accionadores anterior à base de dados
DocumentDB fornece accionadores que são executadas ou acionados a uma operação num documento. Por exemplo, pode especificar um acionador de pré-lançamento quando estiver a criar um documento – este accionador pré serão executadas antes do documento é criado. Segue-se um exemplo de como pré-accionadores podem ser utilizados para validar as propriedades de um documento que está a ser criado:

    var validateDocumentContentsTrigger = {
        name: "validateDocumentContents",
        body: function validate() {
            var context = getContext();
            var request = context.getRequest();
    
            // document to be created in the current operation
            var documentToCreate = request.getBody();
    
            // validate properties
            if (!("timestamp" in documentToCreate)) {
                var ts = new Date();
                documentToCreate["my timestamp"] = ts.getTime();
            }
    
            // update the document that will be created
            request.setBody(documentToCreate);
        },
        triggerType: TriggerType.Pre,
        triggerOperation: TriggerOperation.Create
    }


E o código de registo do lado do cliente Node.js correspondente para o accionador:

    // register pre-trigger
    client.createTriggerAsync(collection.self, validateDocumentContentsTrigger)
        .then(function (response) {
            console.log("Created", response.resource);
            var docToCreate = {
                id: "DocWithTrigger",
                event: "Error",
                source: "Network outage"
            };
    
            // run trigger while creating above document 
            var options = { preTriggerInclude: "validateDocumentContents" };
    
            return client.createDocumentAsync(collection.self,
                  docToCreate, options);
        }, function (error) {
            console.log("Error", error);
        })
    .then(function (response) {
        console.log(response.resource); // document with timestamp property added
    }, function (error) {
        console.log("Error", error);
    });


Pré-accionadores não podem ter quaisquer parâmetros de entrada. O objeto request pode ser utilizado para manipular a mensagem de pedido associada a operação. Aqui, o accionador pré está a ser executado com a criação de um documento e o corpo da mensagem de pedido contém o documento ser criada em formato JSON.   

Quando estão registados accionadores, os utilizadores podem especificar as operações que pode executar a com. Este accionador foi criado com TriggerOperation.Create, o que significa que o seguinte não é permitida.

    var options = { preTriggerInclude: "validateDocumentContents" };
    
    client.replaceDocumentAsync(docToReplace.self,
                  newDocBody, options)
    .then(function (response) {
        console.log(response.resource);
    }, function (error) {
        console.log("Error", error);
    });
    
    // Fails, can’t use a create trigger in a replace operation

### <a name="database-post-triggers"></a>Accionadores pós da base de dados
Pós accionadores, como a pré-accionadores, estão associadas uma operação num documento e não tomar quaisquer parâmetros de entrada. Executar **após** a que conclusão da operação e têm acesso à mensagem de resposta que é enviada para o cliente.   

O exemplo seguinte mostra accionadores pós-acordo em ação:

    var updateMetadataTrigger = {
        name: "updateMetadata",
        body: function updateMetadata() {
            var context = getContext();
            var collection = context.getCollection();
            var response = context.getResponse();
    
            // document that was created
            var createdDocument = response.getBody();
    
            // query for metadata document
            var filterQuery = 'SELECT * FROM root r WHERE r.id = "_metadata"';
            var accept = collection.queryDocuments(collection.getSelfLink(), filterQuery,
                updateMetadataCallback);
            if(!accept) throw "Unable to update metadata, abort";
     
            function updateMetadataCallback(err, documents, responseOptions) {
                if(err) throw new Error("Error" + err.message);
                         if(documents.length != 1) throw 'Unable to find metadata document';
                         
                         var metadataDocument = documents[0];
                         
                         // update metadata
                         metadataDocument.createdDocuments += 1;
                         metadataDocument.createdNames += " " + createdDocument.id;
                         var accept = collection.replaceDocument(metadataDocument._self,
                               metadataDocument, function(err, docReplaced) {
                                      if(err) throw "Unable to update metadata, abort";
                               });
                         if(!accept) throw "Unable to update metadata, abort";
                         return;                    
            }                                                                                           
        },
        triggerType: TriggerType.Post,
        triggerOperation: TriggerOperation.All
    }


Pode estar registado junto ao accionador conforme mostrado no seguinte exemplo.

    // register post-trigger
    client.createTriggerAsync('dbs/testdb/colls/testColl', updateMetadataTrigger)
        .then(function(createdTrigger) { 
            var docToCreate = { 
                name: "artist_profile_1023",
                artist: "The Band",
                albums: ["Hellujah", "Rotators", "Spinning Top"]
            };
    
            // run trigger while creating above document 
            var options = { postTriggerInclude: "updateMetadata" };
        
            return client.createDocumentAsync(collection.self,
                  docToCreate, options);
        }, function(error) {
            console.log("Error" , error);
        })
    .then(function(response) {
        console.log(response.resource); 
    }, function(error) {
        console.log("Error" , error);
    });


Este accionador consultas para o documento de metadados e actualiza-la com os detalhes sobre o documento recentemente criado.  

Uma coisa que é importante ter em atenção é a execução de avanço no DocumentDB **transaccionais** . Este accionador pós é executado como parte da transação mesmo como a criação de documento original. Por isso, se podemos gerar uma exceção a partir do accionador pós-acordo (diga se estamos não é possível atualizar o documento de metadados), a transação Toda irá falhar e ser revertida. Nenhum documento será criado e será devolvida uma exceção.  

##<a id="udf"></a>Funções definidas pelo utilizador
Funções definidas pelo utilizador (UDFs) são utilizadas para expandir a gramática de idioma de consulta DocumentDB SQL e implementar a lógica empresarial personalizada. Só pode ser chamados a partir de dentro de consultas. Se não tem acesso ao objeto de contexto e de destinam-se para ser utilizado como só de cluster JavaScript. Por conseguinte, UDFs podem ser executados em secundárias réplicas do serviço DocumentDB.  
 
O exemplo seguinte cria um UDF para calcular o imposto de rendimento com base em taxas para vários parênteses de rendimento e utiliza-la dentro de uma consulta para localizar todas as pessoas que pago mais do que $20.000 na impostos.

    var taxUdf = {
        name: "tax",
        body: function tax(income) {
    
            if(income == undefined) 
                throw 'no input';
    
            if (income < 1000) 
                return income * 0.1;
            else if (income < 10000) 
                return income * 0.2;
            else
                return income * 0.4;
        }
    }


UDF subsequentemente pode ser utilizado em consultas, como no exemplo seguinte:

    // register UDF
    client.createUserDefinedFunctionAsync('dbs/testdb/colls/testColl', taxUdf)
        .then(function(response) { 
            console.log("Created", response.resource);
    
            var query = 'SELECT * FROM TaxPayers t WHERE udf.tax(t.income) > 20000'; 
            return client.queryDocuments('dbs/testdb/colls/testColl',
                   query).toArrayAsync();
        }, function(error) {
            console.log("Error" , error);
        })
    .then(function(response) {
        var documents = response.feed;
        console.log(response.resource); 
    }, function(error) {
        console.log("Error" , error);
    });

## <a name="javascript-language-integrated-query-api"></a>Consulta integrada idioma JavaScript API
Para além de emissão consultas através de gramática SQL do DocumentDB, o SDK do lado do servidor permite-lhe executar consultas otimizadas utilizando uma interface JavaScript fluent sem quaisquer dados de conhecimento do SQL. A consulta de JavaScript que API permite-lhe através de programação construir consultas, passando predicadas funções numa função chainable chamadas com uma sintaxe familiar do ECMAScript5 built-ins de matriz e populares JavaScript bibliotecas como lodash. Consultas estão analisadas pelo runtime JavaScript para ser executada de forma eficiente com índices do DocumentDB.

> [AZURE.NOTE]`__` (sublinhado duplo) é um alias para `getContext().getCollection()`.
> <br/>
> Por outras palavras, pode utilizar `__` ou `getContext().getCollection()` para aceder à API de consulta JavaScript.

Funções suportadas incluem:
<ul>
<li>
<b>chain().... valor ([chamada de retorno] [, opções])</b>
<ul>
<li>
Iniciar uma chamada interligada qual deve ser terminada com value().
</li>
</ul>
</li>
<li>
<b>filtro (predicateFunction [, opções] [, chamada de retorno])</b>
<ul>
<li>
Filtra a entrada de dados utilizando uma função predicada que devolve verdadeiro/falso para filtrar documentos de entrada entrada/saída para o conjunto de resultante. Este comportamento semelhante a uma cláusula WHERE no SQL.
</li>
</ul>
</li>
<li>
<b>mapa (transformationFunction [, opções] [, chamada de retorno])</b>
<ul>
<li>
Aplica-se uma projecções dada uma função de transformação que mapas de cada item de introdução a um objeto de JavaScript ou valor. Este comportamento semelhante a uma cláusula SELECT no SQL.
</li>
</ul>
</li>
<li>
<b>pluck ([propertyName] [, opções] [, chamada de retorno])</b>
<ul>
<li>
Este é um atalho para o mapa que extrai o valor de uma única propriedade a partir de cada item de introdução.
</li>
</ul>
</li>
<li>
<b>aplanar ([isShallow] [, opções] [, chamada de retorno])</b>
<ul>
<li>
Combina e organiza matrizes de cada item entrada para uma única matriz. Este comportamento semelhante ao SelectMany no LINQ.
</li>
</ul>
</li>
<li>
<b>sortBy ([atributo] [, opções] [, chamada de retorno])</b>
<ul>
<li>
Produzir um novo conjunto de documentos por ordenar os documentos na sequência do documento de entrada por ordem utilizando o predicado determinado ascendente. Este comportamento semelhante a uma cláusula ORDER BY no SQL.
</li>
</ul>
</li>
<li>
<b>sortByDescending ([atributo] [, opções] [, chamada de retorno])</b>
<ul>
<li>
Produzir um novo conjunto de documentos por ordenar os documentos na sequência do documento de entrada de forma descendente, utilizando o predicado determinado. Este comportamento semelhante a uma cláusula ORDER BY x DESC no SQL.
</li>
</ul>
</li>
</ul>


Quando incluir dentro predicado e/ou Seletor de funções, as seguintes construções de JavaScript obtenham automaticamente optimizadas para ser executado diretamente no DocumentDB índices:

* Operadores simples: = + - * / % | ^ &amp; == != === !=== &lt; &gt; &lt;= &gt;= || &amp;&amp; &lt;&lt; &gt;&gt; &gt;&gt;&gt;! ~
* Literais, incluindo o objeto literal: {}
* var, ENTER

As seguintes construções de JavaScript não obter otimizadas para DocumentDB índices:

* Controlar o fluxo (por exemplo, se, enquanto)
* Chamadas de função

Para mais informações, consulte o artigo nosso [JSDocs do lado do servidor](http://azure.github.io/azure-documentdb-js-server/).

### <a name="example-write-a-stored-procedure-using-the-javascript-query-api"></a>Exemplo: Escrever um procedimento armazenado utilizando a consulta de JavaScript API

O código de exemplo seguinte é um exemplo de como a API JavaScript do consulta pode ser utilizada no contexto de um procedimento armazenado. O procedimento armazenado insere um documento, fornecido por um parâmetro de entrada e atualiza um metadados do documento, utilizando o `__.filter()` método, com minSize, maxSize e tamanho total baseadas em propriedade de tamanho do documento de entrada.

    /**
     * Insert actual doc and update metadata doc: minSize, maxSize, totalSize based on doc.size.
     */
    function insertDocumentAndUpdateMetadata(doc) {
      // HTTP error codes sent to our callback funciton by DocDB server.
      var ErrorCode = {
        RETRY_WITH: 449,
      }

      var isAccepted = __.createDocument(__.getSelfLink(), doc, {}, function(err, doc, options) {
        if (err) throw err;

        // Check the doc (ignore docs with invalid/zero size and metaDoc itself) and call updateMetadata.
        if (!doc.isMetadata && doc.size > 0) {
          // Get the meta document. We keep it in the same collection. it's the only doc that has .isMetadata = true.
          var result = __.filter(function(x) {
            return x.isMetadata === true
          }, function(err, feed, options) {
            if (err) throw err;

            // We assume that metadata doc was pre-created and must exist when this script is called.
            if (!feed || !feed.length) throw new Error("Failed to find the metadata document.");

            // The metadata document.
            var metaDoc = feed[0];

            // Update metaDoc.minSize:
            // for 1st document use doc.Size, for all the rest see if it's less than last min.
            if (metaDoc.minSize == 0) metaDoc.minSize = doc.size;
            else metaDoc.minSize = Math.min(metaDoc.minSize, doc.size);

            // Update metaDoc.maxSize.
            metaDoc.maxSize = Math.max(metaDoc.maxSize, doc.size);

            // Update metaDoc.totalSize.
            metaDoc.totalSize += doc.size;

            // Update/replace the metadata document in the store.
            var isAccepted = __.replaceDocument(metaDoc._self, metaDoc, function(err) {
              if (err) throw err;
              // Note: in case concurrent updates causes conflict with ErrorCode.RETRY_WITH, we can't read the meta again 
              //       and update again because due to Snapshot isolation we will read same exact version (we are in same transaction).
              //       We have to take care of that on the client side.
            });
            if (!isAccepted) throw new Error("replaceDocument(metaDoc) returned false.");
          });
          if (!result.isAccepted) throw new Error("filter for metaDoc returned false.");
        }
      });
      if (!isAccepted) throw new Error("createDocument(actual doc) returned false.");
    }

## <a name="sql-to-javascript-cheat-sheet"></a>SQL para Javascript batota folha
A tabela seguinte apresenta várias consultas SQL e as consultas de JavaScript correspondentes.

Como com consultas SQL, as teclas de propriedade do documento (por exemplo, `doc.id`) são entre maiúsculas e minúsculas.

<br/>
<table border="1" width="100%">
<colgroup>
<col span="1" style="width: 40%;">
<col span="1" style="width: 40%;">
<col span="1" style="width: 20%;">
</colgroup>
<tbody>
<tr>
<th>SQL</th>
<th>API de consulta JavaScript</th>
<th>Detalhes</th>
</tr>
<tr>
<td>
<pre>
SELECIONE * a partir de documentos
</pre>
</td>
<td>
<pre>
__.Map(Function(doc) {remetente doc};);
</pre>
</td>
<td>Em todos os documentos (paginados com token de continuação de notas), como os resultados são.</td>
</tr>
<tr>
<td>
<pre>
SELECIONE docs.id, docs.message como msg, docs.actions de documentos
</pre>
</td>
<td>
<pre>
__.Map(Function(doc) {devolvido {id: doc.id, msg: doc.message, acções: doc.actions};});
</pre>
</td>
<td>Projectos o id, a mensagem (com o alias msg) e a ação a partir de todos os documentos.</td>
</tr>
<tr>
<td>
<pre>
SELECIONE * a partir de documentos onde docs.id="X998_Y998"
</pre>
</td>
<td>
<pre>
__.Filter(Function(doc) {devolvido doc.id === "X998_Y998;"});
</pre>
</td>
<td>Consultas para documentos com o predicado: id = "X998_Y998".</td>
</tr>
<tr>
<td>
<pre>
SELECIONE * a partir de documentos onde ARRAY_CONTAINS(docs. Etiquetas, 123)
</pre>
</td>
<td>
<pre>
__.Filter(Function(x) {devolvido x.Tags & & x.Tags.indexOf(123) > -1;});
</pre>
</td>
<td>Consultas para documentos que tenham uma propriedade de etiquetas e etiquetas é uma matriz que contém o valor 123.</td>
</tr>
<tr>
<td>
<pre>
SELECIONE docs.id, docs.message como documentos do FROM msg onde docs.id="X998_Y998"
</pre>
</td>
<td>
<pre>
__.chain() .filter(function(doc) {devolvido doc.id === "X998_Y998;"}) .map(function(doc) {devolvido {id: doc.id, msg: doc.message};}) .value();
</pre>
</td>
<td>Consultas para documentos com um predicado id = "X998_Y998" e, em seguida, projectos o id e a mensagem (com o alias msg).</td>
</tr>
<tr>
<td>
<pre>
Marcar documentos do SELECIONE valor etiqueta de associação nos documentos. Etiquetas ORDER BY docs._ts
</pre>
</td>
<td>
<pre>
__.chain() .filter(function(doc) {documento remetente. Etiquetas & & Array.isArray (documento. Etiquetas); .sortBy(function(doc)}) {doc._ts devolvido;}) .pluck("tags") .flatten() .value()
</pre>
</td>
<td>Os filtros para documentos que tenham uma propriedade de matriz, etiquetas, e ordena os documentos resultantes _ts propriedade do sistema de data/hora e, em seguida, projetos + organiza a matriz de etiquetas.</td>
</tr>
</tbody>
</table>

## <a name="runtime-support"></a>Suporte de tempo de execução
[Do lado do servidor de DocumentDB JavaScript SDK](http://azure.github.io/azure-documentdb-js-server/) fornece suporte para a maioria das funcionalidades do idioma JavaScript base como padronizado por [ECMA 262](http://www.ecma-international.org/publications/standards/Ecma-262.htm).

### <a name="security"></a>Segurança
JavaScript procedimentos armazenados e accionadores são em sandbox para que os efeitos de um script evitar para a outra sem ter de aceder através de isolamento da transação instantâneo ao nível da base de dados. Os ambientes de runtime são agrupados mas limpo do contexto após cada execução. Por conseguinte, estes são garante seguros de eventuais efeitos inesperados do lado do outro.

### <a name="pre-compilation"></a>Pré-compilação
Procedimentos armazenados, accionadores e UDFs são implicitamente compiladas para o formato de código byte para evitar custos de compilação no momento da cada invocação de script. Este procedimento garante exe de procedimentos armazenados é rápida e têm um ambiental baixa.

## <a name="client-sdk-support"></a>Suporte para cliente SDK
Para além do cliente [Node.js](documentdb-sdk-node.md) , DocumentDB suporta [.NET](documentdb-sdk-dotnet.md), [Java](documentdb-sdk-java.md), [JavaScript](http://azure.github.io/azure-documentdb-js/)e [Python SDK](documentdb-sdk-python.md). Procedimentos armazenados, accionadores e UDFs podem ser criados e executados utilizar qualquer um dos seguinte SDK também. O exemplo seguinte mostra como criar e executar um procedimento armazenado utilizando o cliente do .NET. Tenha em atenção como os tipos de .NET são transmitidos para o procedimento armazenado como JSON e lido.

    var markAntiquesSproc = new StoredProcedure
    {
        Id = "ValidateDocumentAge",
        Body = @"
                function(docToCreate, antiqueYear) {
                    var collection = getContext().getCollection();    
                    var response = getContext().getResponse();    
    
                    if(docToCreate.Year != undefined && docToCreate.Year < antiqueYear){
                        docToCreate.antique = true;
                    }
    
                    collection.createDocument(collection.getSelfLink(), docToCreate, {}, 
                        function(err, docCreated, options) { 
                            if(err) throw new Error('Error while creating document: ' + err.message);                              
                            if(options.maxCollectionSizeInMb == 0) throw 'max collection size not found'; 
                            response.setBody(docCreated);
                    });
            }"
    };
    
    // register stored procedure
    StoredProcedure createdStoredProcedure = await client.CreateStoredProcedureAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), markAntiquesSproc);
    dynamic document = new Document() { Id = "Borges_112" };
    document.Title = "Aleph";
    document.Year = 1949;
    
    // execute stored procedure
    Document createdDocument = await client.ExecuteStoredProcedureAsync<Document>(UriFactory.CreateStoredProcedureUri("db", "coll", "sproc"), document, 1920);


Este exemplo mostra como utilizar o [.NET SDK](https://msdn.microsoft.com/library/azure/dn948556.aspx) para criar um accionador pré e criar um documento com o accionador ativado. 

    Trigger preTrigger = new Trigger()
    {
        Id = "CapitalizeName",
        Body = @"function() {
            var item = getContext().getRequest().getBody();
            item.id = item.id.toUpperCase();
            getContext().getRequest().setBody(item);
        }",
        TriggerOperation = TriggerOperation.Create,
        TriggerType = TriggerType.Pre
    };
    
    Document createdItem = await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), new Document { Id = "documentdb" },
        new RequestOptions
        {
            PreTriggerInclude = new List<string> { "CapitalizeName" },
        });


E o exemplo seguinte mostra como criar uma função definida pelo utilizador (UDF) e utilizá-la numa [consulta DocumentDB SQL](documentdb-sql-query.md).

    UserDefinedFunction function = new UserDefinedFunction()
    {
        Id = "LOWER",
        Body = @"function(input) 
        {
            return input.toLowerCase();
        }"
    };
    
    foreach (Book book in client.CreateDocumentQuery(UriFactory.CreateDocumentCollectionUri("db", "coll"),
        "SELECT * FROM Books b WHERE udf.LOWER(b.Title) = 'war and peace'"))
    {
        Console.WriteLine("Read {0} from query", book);
    }

## <a name="rest-api"></a>REST API
Todas as operações de DocumentDB podem ser executadas de forma RESTful. Funções definidas pelo utilizador, accionadores e procedimentos armazenados podem ser registadas numa coleção de utilizando HTTP POST. Segue-se um exemplo de como registar um procedimento armazenado:

    POST https://<url>/sprocs/ HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT
    
    
    var x = {
      "name": "createAndAddProperty",
      "body": function (docToCreate, addedPropertyName, addedPropertyValue) {
                var collectionManager = getContext().getCollection();
                collectionManager.createDocument(
                    collectionManager.getSelfLink(),
                    docToCreate,
                    function(err, docCreated) {
                      if(err) throw new Error('Error:  ' + err.message);
                      docCreated[addedPropertyName] = addedPropertyValue;
                      getContext().getResponse().setBody(docCreated);
                    });
            }
    }


O procedimento armazenado está registado através da execução de um pedido de mensagem contra a URI dbs/testdb/colls/testColl/sprocs com corpo que contém o procedimento armazenado para criar. Accionadores e UDFs podem estar registado junto da mesma forma através da emissão de uma mensagem contra /accionadores e /udfs respetivamente.
Isto armazenados podem procedimento, em seguida, ser executado por emitir um pedido de mensagem contra a sua ligação de recurso:

    POST https://<url>/sprocs/<sproc> HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:20 GMT
    
    
    [ { "name": "TestDocument", "book": "Autumn of the Patriarch"}, "Price", 200 ]


Aqui, a entrada para o procedimento armazenado é transmitida no corpo do pedido. Tenha em atenção que lhe é transmitida a entrada de dados como uma matriz JSON de parâmetros de entrada. O procedimento armazenado leva a primeira entrada de dados como um documento que está o corpo de uma resposta. A resposta que recebemos é da seguinte forma:

    HTTP/1.1 200 OK
     
    { 
      name: 'TestDocument',
      book: ‘Autumn of the Patriarch’,
      id: ‘V7tQANV3rAkDAAAAAAAAAA==‘,
      ts: 1407830727,
      self: ‘dbs/V7tQAA==/colls/V7tQANV3rAk=/docs/V7tQANV3rAkDAAAAAAAAAA==/’,
      etag: ‘6c006596-0000-0000-0000-53e9cac70000’,
      attachments: ‘attachments/’,
      Price: 200
    }


Accionadores, ao contrário de procedimentos armazenados, não podem ser executadas diretamente. Em vez disso, são executados como parte de uma operação num documento. Vamos pode especificar os accionadores para executar com um pedido de utilização de cabeçalhos de HTTP. Segue-se pedido para criar um documento.

    POST https://<url>/docs/ HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT
    x-ms-documentdb-pre-trigger-include: validateDocumentContents 
    x-ms-documentdb-post-trigger-include: bookCreationPostTrigger
    
    
    {
       "name": "newDocument",
       “title”: “The Wizard of Oz”,
       “author”: “Frank Baum”,
       “pages”: 92
    }


Aqui o accionador pré para executar com o pedido é especificado no cabeçalho x-ms-documentdb-pre-trigger-include. Modo correspondente, qualquer accionadores pós são apresentados no cabeçalho x-ms-documentdb-post-trigger-include. Tenha em atenção que pré e pós accionadores podem ser especificados para um determinado pedido.

## <a name="sample-code"></a>Código de exemplo

Pode encontrar mais exemplos de código do lado do servidor (incluindo [em volume-delete](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples/stored-procedures/bulkDelete.js)e [update](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples/stored-procedures/update.js)) no nosso [Github repositório](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples).

Pretende partilhar o seu Cibermagia procedimento armazenado? Suportasse, envie-num pedido de solicitação! 

## <a name="next-steps"></a>Próximos passos

Assim que tiver uma ou mais procedimentos armazenados, accionadores e funções definidas pelo utilizador criadas, pode carregá-los e vê-los no Portal do Azure através do Explorador de Script. Para mais informações, consulte o artigo [Ver procedimentos armazenados, accionadores e funções definidas pelo utilizador utilizando o Explorador de Script DocumentDB](documentdb-view-scripts.md).

Também pode encontrar as seguintes referências e recursos úteis no caminho para obter mais informações sobre a programação do lado do servidor DocumentDB:

- [Azure DocumentDB SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx)
- [DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio/releases)
- [JSON](http://www.json.org/) 
- [262 JavaScript ECMA](http://www.ecma-international.org/publications/standards/Ecma-262.htm)
- [JavaScript – sistema de tipo JSON](http://www.json.org/js.html) 
- [Base de dados seguro e Portable expansão](http://dl.acm.org/citation.cfm?id=276339) 
- [Serviço orientados arquitetura de base de dados](http://dl.acm.org/citation.cfm?id=1066267&coll=Portal&dl=GUIDE) 
- [Alojamento o .NET Runtime no Microsoft SQL server](http://dl.acm.org/citation.cfm?id=1007669)
