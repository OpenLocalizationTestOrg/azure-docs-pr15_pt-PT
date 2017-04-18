<properties
    pageTitle="Como utilizar o armazenamento de tabela do Azure a partir do Node.js | Microsoft Azure"
    description="Armazene dados estruturados na nuvem através do armazenamento de tabela do Azure, um arquivo de dados NoSQL."
    services="storage"
    documentationCenter="nodejs"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="tamram"/>


# <a name="how-to-use-azure-table-storage-from-nodejs"></a>Como utilizar o armazenamento de tabela do Azure a partir do Node.js

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Descrição geral

Este tópico mostra como efetuar cenários comuns utilizando o serviço de tabela do Azure numa aplicação Node.js.

Os exemplos de código neste tópico partem do pressuposto de que já tem uma aplicação de Node.js. Para obter informações sobre como criar uma aplicação de Node.js no Azure, consulte o artigo qualquer um destes tópicos:

- [Criar uma aplicação web do Node.js no serviço de aplicação do Azure](../app-service-web/web-sites-nodejs-develop-deploy-mac.md)
- [Criar e implementar uma aplicação web do Node.js para Azure utilizando WebMatrix](../app-service-web/web-sites-nodejs-use-webmatrix.md)
- [Criar e implementar uma aplicação de Node.js num serviço de nuvem do Azure](../cloud-services/cloud-services-nodejs-develop-deploy-app.md) (através do Windows PowerShell)


[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]


## <a name="configure-your-application-to-access-azure-storage"></a>Configurar a aplicação para aceder ao armazenamento do Windows Azure

Para utilizar o armazenamento do Windows Azure, tem o SDK do armazenamento Azure para Node.js, que inclui um conjunto de bibliotecas de conveniência que comunicar com os serviços de resto de armazenamento.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Utilizar o Gestor de pacote nó (NPM) para instalar o pacote

1.  Utilize uma interface de comandos como **PowerShell** (Windows), **Terminal** (Mac) ou **festa** (Unix) e navegue para a pasta onde a criou a aplicação.

2.  Na janela de comandos, escreva a **npm instalar o armazenamento do Windows azure** . Resultado do comando é semelhante ao seguinte exemplo.

        azure-storage@0.5.0 node_modules\azure-storage
        +-- extend@1.2.1
        +-- xmlbuilder@0.4.3
        +-- mime@1.2.11
        +-- node-uuid@1.4.3
        +-- validator@3.22.2
        +-- underscore@1.4.4
        +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
        +-- xml2js@0.2.7 (sax@0.5.2)
        +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)

3.  Pode executar o comando **ls** para verificar que manualmente uma **nó\_módulos** pasta foi criada. Nessa pasta irá encontrar o pacote de **armazenamento do Windows azure** , que contém as bibliotecas que precisa para aceder ao armazenamento.

### <a name="import-the-package"></a>Importar o pacote

Adicione o seguinte código para a parte superior do ficheiro **server.js** na sua aplicação:

    var azure = require('azure-storage');

## <a name="set-up-an-azure-storage-connection"></a>Configurar uma ligação de armazenamento do Windows Azure

O módulo azure vai ler as variáveis de ambiente AZURE\_armazenamento\_conta e AZURE\_armazenamento\_acesso\_chave ou AZURE\_armazenamento\_ligação\_cadeia para as informações necessárias para ligar à sua conta de armazenamento Azure. Se não estiver definidos estas variáveis de ambiente, tem de especificar as informações de conta quando chamar **TableService**.

Para obter um exemplo de definir as variáveis de ambiente no [Portal do Azure](https://portal.azure.com) para um Web site Azure, consulte o artigo [Node.js web app utilizando o serviço de tabela do Azure].

## <a name="create-a-table"></a>Criar uma tabela

O código seguinte cria um objeto de **TableService** e utiliza-lo para criar uma nova tabela. Adicione o seguinte na parte superior do **server.js**.

    var tableSvc = azure.createTableService();

A chamada para **createTableIfNotExists** irá criar uma nova tabela com o nome especificado se ainda não existir. O exemplo seguinte cria uma nova tabela com o nome 'MinhaTabela' se não existir já:

    tableSvc.createTableIfNotExists('mytable', function(error, result, response){
      if(!error){
        // Table exists or created
      }
    });

O `result.created` será `true` se é criada uma nova tabela, e `false` se já existe na tabela. O `response` irá conter informações sobre o pedido.

### <a name="filters"></a>Filtros

Operações de filtragem opcionais podem ser aplicadas a operações realizadas utilizando **TableService**. Operações de filtragem pode incluir registo, automaticamente a repetir, etc. Filtros são objetos que implementam um método com a assinatura:

    function handle (requestOptions, next)

Após fazer a sua pré-processamento no menu Opções de pedido, o método precisa de chamar "next", prisma uma chamada de retorno com a assinatura seguinte:

    function (returnObject, finalCallback, next)

Esta chamada de retorno e depois de processamento de returnObject (a resposta do pedido de servidor), a chamada de retorno tem invocar, em seguida, se existir para continuar a outros filtros de processamento ou simplesmente invocar finalCallback caso contrário, para terminar a chamada do serviço.

Dois filtros implementam lógica de repetição são incluídos com o SDK do Azure para Node.js, **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. A seguinte cria um objeto de **TableService** que utiliza o **ExponentialRetryPolicyFilter**:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var tableSvc = azure.createTableService().withFilter(retryOperations);

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela

Para adicionar uma entidade, crie primeiro um objeto que define as propriedades de entidade. Todas as entidades tem de conter um **PartitionKey** e **RowKey**, que são identificadores exclusivos para a entidade.

* **PartitionKey** - determina a partição armazenados na entidade

* **RowKey** - identifica exclusivamente a entidade dentro da partição

**PartitionKey** e **RowKey** tem de ser valores de cadeia. Para mais informações, consulte o artigo [Compreender o modelo de dados do serviço de tabela](http://msdn.microsoft.com/library/azure/dd179338.aspx).

Segue-se um exemplo de definição de uma entidade. Tenha em atenção que **dueDate** é definida como um tipo de **Edm.DateTime**. Especificar o tipo é opcional e tipos de vai ser inferidos se não for especificados.

    var task = {
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '1'},
      description: {'_':'take out the trash'},
      dueDate: {'_':new Date(2015, 6, 20), '$':'Edm.DateTime'}
    };

> [AZURE.NOTE] Também existe um campo de **hora** para cada registo que está definido por Azure quando uma entidade é inserida ou atualizada.

Também pode utilizar o **entityGenerator** para criar entidades. O exemplo seguinte cria a mesma entidade tarefa utilizando o **entityGenerator**.

    var entGen = azure.TableUtilities.entityGenerator;
    var task = {
      PartitionKey: entGen.String('hometasks'),
      RowKey: entGen.String('1'),
      description: entGen.String('take out the trash'),
      dueDate: entGen.DateTime(new Date(Date.UTC(2015, 6, 20))),
    };

Para adicionar uma entidade à sua tabela, passe o objeto de entidade para o método de **insertEntity** .

    tableSvc.insertEntity('mytable',task, function (error, result, response) {
      if(!error){
        // Entity inserted
      }
    });

Se for concluído com êxito, a operação `result` irá conter a [ETag](http://en.wikipedia.org/wiki/HTTP_ETag) do registo inserido e `response` irá conter informações acerca da operação.

Resposta de exemplo:

    { '.metadata': { etag: 'W/"datetime\'2015-02-25T01%3A22%3A22.5Z\'"' } }

> [AZURE.NOTE] Por predefinição, **insertEntity** não devolve a entidade inserida como parte da `response` informações. Se planeia executar outras operações nesta entidade ou pretende colocar as informações em cache, podem ser úteis para que seja devolvido como parte da `result`. Pode fazê-lo ao ativar **echoContent** da seguinte forma:
>
> `tableSvc.insertEntity('mytable', task, {echoContent: true}, function (error, result, response) {...}`

## <a name="update-an-entity"></a>Atualizar uma entidade

Existem vários métodos disponíveis para atualizar uma entidade existente:

* **replaceEntity** - atualiza os uma entidade existente ao substituí-la

* **mergeEntity** - actualiza uma entidade existente por intercalar novos valores de propriedade na entidade existente

* **insertOrReplaceEntity** - atualiza uma entidade existente ao substituí-la. Se não existe uma entidade não existir, será inserida uma nova

* **insertOrMergeEntity** - atualiza uma entidade existente ao intercalar novos valores de propriedade na existente. Se não existe uma entidade não existir, será inserida uma nova

O exemplo seguinte demonstra atualizar uma entidade utilizando **replaceEntity**:

    tableSvc.replaceEntity('mytable', updatedTask, function(error, result, response){
      if(!error) {
        // Entity updated
      }
    });

> [AZURE.NOTE] Por predefinição, atualizar uma entidade não verifique se os dados de ser actualizados anteriormente foi modificados por outro processo. Para suportar atualizações em simultâneo:
>
> 1. Obter ETag do objeto a ser atualizado. Isto é devolvido como parte da `response` para qualquer entidade relacionados com a operação e pode ser obtida através de `response['.metadata'].etag`.
>
> 2. Quando executar uma operação de atualização numa entidade, adicione as informações de ETag anteriormente obtidas para a nova entidade. Por exemplo:
>
>     `entity2['.metadata'].etag = currentEtag;`
>
> 3. Execute a operação de atualização. Se tiver sido modificada a entidade desde que recuperou valor ETag, tal como outra instância da sua aplicação, um `error` será devolvido indicando que não tenha sido satisfeita a condição de atualização especificada no pedido.

Com **replaceEntity** e **mergeEntity**, se não existe a entidade que está a ser atualizada, em seguida, a operação de atualização falhará. Por isso, se pretender armazenar uma entidade independentemente de se já existe, utilize **insertOrReplaceEntity** ou **insertOrMergeEntity**.

O `result` para actualização com êxito operações irão conter a **Etag** da entidade atualizada.

## <a name="work-with-groups-of-entities"></a>Trabalhar com grupos de entidades

Por vezes, faz sentido para submeter várias operações em conjunto num lote para se certificar de atómica processamento pelo servidor. Para realizar que, utilize a classe de **TableBatch** para criar um lote de e, em seguida, utilize o método de **executeBatch** do **TableService** para executar as operações por lotes.

 O exemplo seguinte demonstra duas entidades num lote de a submeter:

    var task1 = {
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '1'},
      description: {'_':'Take out the trash'},
      dueDate: {'_':new Date(2015, 6, 20)}
    };
    var task2 = {
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '2'},
      description: {'_':'Wash the dishes'},
      dueDate: {'_':new Date(2015, 6, 20)}
    };

    var batch = new azure.TableBatch();

    batch.insertEntity(task1, {echoContent: true});
    batch.insertEntity(task2, {echoContent: true});

    tableSvc.executeBatch('mytable', batch, function (error, result, response) {
      if(!error) {
        // Batch completed
      }
    });

Para operações de lote bem sucedida, `result` irá conter informações para cada operação no lote de.

### <a name="work-with-batched-operations"></a>Trabalhar com operações por lotes

Operações adicionadas a um lote podem que seja inspeccionadas visualizando a `operations` propriedade. Também pode utilizar os seguintes métodos para trabalhar com operações:

* **desmarque** - limpa todas as operações a partir de uma secção

* **getOperations** - obtém uma operação do lote

* **hasOperations** - devolve VERDADEIRO se o lote contém operações

* **removeOperations** - remove uma operação

* **tamanho** - devolve o número de operações no lote de

## <a name="retrieve-an-entity-by-key"></a>Obter uma entidade por chave

Para devolver uma entidade específica com base no **PartitionKey** e **RowKey**, utilize o método de **retrieveEntity** .

    tableSvc.retrieveEntity('mytable', 'hometasks', '1', function(error, result, response){
      if(!error){
        // result contains the entity
      }
    });

Uma vez esta operação concluída, `result` irá conter a entidade.

## <a name="query-a-set-of-entities"></a>Um conjunto de entidades de consulta

Para consultar uma tabela, utilize o objeto **TableQuery** para construir uma expressão de consulta utilizando as seguintes cláusulas:

* **Selecione** - os campos a ser devolvidos pela consulta

* **onde** - onde cláusula

    * **e** - um `and` condição onde

    * **ou** - um `or` condição onde

* **início** - o número de itens para obter


O exemplo seguinte cria uma consulta que irá devolver os cinco itens principais com um PartitionKey de 'hometasks'.

    var query = new azure.TableQuery()
      .top(5)
      .where('PartitionKey eq ?', 'hometasks');

Uma vez que não é utilizada a **Selecionar** , serão devolvidos todos os campos. Para executar a consulta de uma tabela, utilize **queryEntities**. O exemplo seguinte utiliza esta consulta para devolver entidades de 'AMinhaTabela'.

    tableSvc.queryEntities('mytable',query, null, function(error, result, response) {
      if(!error) {
        // query was successful
      }
    });

Se tiver êxito, `result.entries` irá conter uma matriz de entidades que corresponde à consulta. Se a consulta não foi possível devolver todas as entidades, `result.continuationToken` será não*Nulo* e podem ser utilizados como o terceiro parâmetro de **queryEntities** para obter mais resultados. A consulta inicial, utilize *Nulo* de parâmetro de terceiro.

### <a name="query-a-subset-of-entity-properties"></a>Um subconjunto de propriedades entidade de consulta

Uma consulta a uma tabela pode obter apenas alguns campos a partir de uma entidade.
Isto reduz a largura de banda e pode melhorar o desempenho da consulta, especialmente para entidades muito grandes. Utilize a cláusula **Selecione** e passam os nomes dos campos a ser devolvido. Por exemplo, a seguinte consulta devolverá apenas os campos **Descrição** e **dueDate** .

    var query = new azure.TableQuery()
      .select(['description', 'dueDate'])
      .top(5)
      .where('PartitionKey eq ?', 'hometasks');

## <a name="delete-an-entity"></a>Eliminar uma entidade

Pode eliminar uma entidade utilizando suas chaves partição e linha. Neste exemplo, o objeto **task1** contém os valores de **RowKey** e **PartitionKey** da entidade a ser eliminada. Em seguida, o objeto é transmitido para o método de **deleteEntity** .

    var task = {
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '1'}
    };

    tableSvc.deleteEntity('mytable', task, function(error, response){
      if(!error) {
        // Entity deleted
      }
    });

> [AZURE.NOTE] Considere utilizar ETags ao eliminar itens, para se certificar de que o item foi modificado por outro processo. Consulte o artigo [atualizar uma entidade](#update-an-entity) para obter informações sobre como utilizar ETags.

## <a name="delete-a-table"></a>Eliminar uma tabela

O código seguinte elimina uma tabela a partir de uma conta de armazenamento.

    tableSvc.deleteTable('mytable', function(error, response){
        if(!error){
            // Table deleted
        }
    });

Se não tiver a certeza se existe a tabela, utilize **deleteTableIfExists**.

## <a name="use-continuation-tokens"></a>Utilizar tokens de continuação de notas

Quando estiver a consultar tabelas para grandes quantidades de resultados, procure tokens de continuação de notas. Poderão estar disponíveis para a sua consulta que poderá não perceber se não construir reconheçam quando existe um token de continuação de notas grandes quantidades de dados.

Os resultados do objeto devolvidas durante a consultar conjuntos de entidades um `continuationToken` propriedade quando esse um token está presente. Pode, em seguida, utilizar este quando executar uma consulta para continuar para mover entre as entidades partição e tabela.

Quando consultar, poderá ser fornecido um parâmetro continuationToken entre a instância do objeto de consulta e a função de chamada de retorno:

```
var nextContinuationToken = null;
dc.table.queryEntities(tableName,
    query,
    nextContinuationToken,
    function (error, results) {
        if (error) throw error;

        // iterate through results.entries with results

        if (results.continuationToken) {
            nextContinuationToken = results.continuationToken;
        }

    });
```

Se lhe inspecionar a `continuationToken` objeto, irá encontrar as propriedades tal como `nextPartitionKey`, `nextRowKey` e `targetLocation`, que podem ser utilizado para fazer iteração em todos os resultados.

Também existe uma amostra de continuação de notas dentro de repo Azure armazenamento Node.js no GitHub. Procure `examples/samples/continuationsample.js`.

## <a name="work-with-shared-access-signatures"></a>Trabalhar com assinaturas acesso partilhado

Acesso partilhado assinaturas (SA) são uma forma segura para fornecer acesso granular a tabelas sem facultar o seu nome de conta de armazenamento ou teclas. SA são utilizadas com frequência para fornecer acesso limitado para os seus dados, tais como permitir que uma aplicação móvel a registos de consulta.

Uma aplicação de confiança como um serviço baseado na nuvem gera uma SA utilizando o **generateSharedAccessSignature** do **TableService**e fornece-o para uma aplicação ou não fidedigna ou não fidedigna semiestruturados como uma aplicação móvel. As associações de segurança são gerada utilizando uma política que descreve as datas de início e de fim durante o qual as associações de segurança são válida, bem como o nível de acesso ao titular SA.

O exemplo seguinte gera uma nova política de acesso partilhado que irá permitir que o titular SA consulta ('r') a tabela e expira 100 minutos após a hora que é criada.

    var startDate = new Date();
    var expiryDate = new Date(startDate);
    expiryDate.setMinutes(startDate.getMinutes() + 100);
    startDate.setMinutes(startDate.getMinutes() - 100);

    var sharedAccessPolicy = {
      AccessPolicy: {
        Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
        Start: startDate,
        Expiry: expiryDate
      },
    };

    var tableSAS = tableSvc.generateSharedAccessSignature('mytable', sharedAccessPolicy);
    var host = tableSvc.host;

Tenha em atenção que as informações de anfitrião devem ser fornecidas de além disso, conforme for necessário quando o titular SA tenta aceder a tabela.

Em seguida, a aplicação de cliente utiliza as associações de segurança com **TableServiceWithSAS** para efetuar operações contra a tabela. O exemplo seguinte liga para a tabela e executa uma consulta.

    var sharedTableService = azure.createTableServiceWithSas(host, tableSAS);
    var query = azure.TableQuery()
      .where('PartitionKey eq ?', 'hometasks');

    sharedTableService.queryEntities(query, null, function(error, result, response) {
      if(!error) {
        // result contains the entities
      }
    });

Uma vez que as associações de segurança foram criada com apenas acesso à consulta, se foram feita uma tentativa para inserir, atualizar ou eliminar entidades, deverá ser devolvido um erro.

### <a name="access-control-lists"></a>Listas de controlo de acesso

Também pode utilizar uma lista de controlo de acesso (ACL) para definir a política de acesso para uma SA. Isto é útil se pretender permitir que vários clientes aceder a tabela, mas não fornecer políticas de acesso diferentes para cada cliente.

Uma ACL é implementada utilizando uma matriz de políticas de acesso, com um ID associado a cada política. O exemplo seguinte define duas políticas, uma para 'Utilizador1' e outra para 'Utilizador2':

    var sharedAccessPolicy = {
      user1: {
        Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
        Start: startDate,
        Expiry: expiryDate
      },
      user2: {
        Permissions: azure.TableUtilities.SharedAccessPermissions.ADD,
        Start: startDate,
        Expiry: expiryDate
      }
    };

O exemplo seguinte obtém a ACL atual para a tabela **hometasks** e, em seguida, adiciona as novas políticas utilizando **setTableAcl**. Esta abordagem permite:

    var extend = require('extend');
    tableSvc.getTableAcl('hometasks', function(error, result, response) {
    if(!error){
        var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
        tableSvc.setTableAcl('hometasks', newSignedIdentifiers, function(error, result, response){
          if(!error){
            // ACL set
          }
        });
      }
    });

Assim que tiver sido definida a ACL, em seguida, pode criar uma SA com base no ID de uma política. O exemplo seguinte cria uma nova SA para 'Utilizador2':

    tableSAS = tableSvc.generateSharedAccessSignature('hometasks', { Id: 'user2' });

## <a name="next-steps"></a>Próximos passos

Para obter mais informações, consulte os seguintes recursos.

-   [Armazenamento azure blogue da equipa][].
-   Repositório de [Azure SDK de armazenamento para nó][] no GitHub.
-   [Centro de programadores do node.js](/develop/nodejs/)

  [Armazenamento SDK para o nó do Azure]: https://github.com/Azure/azure-storage-node
  [OData.org]: http://www.odata.org/
  [Using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
  [Azure Portal]: portal.azure.com

  [Node.js Cloud Service]: ../cloud-services-nodejs-develop-deploy-app.md
  [Blogue da equipa do armazenamento Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Website with WebMatrix]: ../web-sites-nodejs-use-webmatrix.md
  [Node.js Cloud Service with Storage]: ../storage-nodejs-use-table-storage-cloud-service-app.md
  [NODE.js web app utilizando o serviço de tabela do Azure]: ../storage-nodejs-use-table-storage-web-site.md
  [Create and deploy a Node.js application to an Azure website]: ../web-sites-nodejs-develop-deploy-mac.md
