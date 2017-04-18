##<a name="create-client"></a>Criar uma ligação de cliente

Criar uma ligação de cliente através da criação de um `WindowsAzure.MobileServiceClient` objeto.  Substituir `appUrl` com o URL para a sua aplicação móvel.

```
var client = WindowsAzure.MobileServiceClient(appUrl);
```

##<a name="table-reference"></a>Trabalhar com tabelas

Para o access ou uma atualização de dados, criar uma referência para a tabela de back-end. Substituir `tableName` com o nome da tabela

```
var table = client.getTable(tableName);
```

Assim que tiver uma referência de tabela, pode trabalhar mais com a tabela:

* [Uma tabela de consulta](#querying)
  * [Filtrar dados](#table-filter)
  * [Paginação através de dados](#table-paging)
  * [Ordenar dados](#sorting-data)
* [Inserir dados](#inserting)
* [Modificar os dados](#modifying)
* [Eliminação de dados](#deleting)

###<a name="querying"></a>Como: consultar uma referência de tabela

Assim que tiver uma referência de tabela, pode utilizá-lo para consultar dados no servidor.  As consultas são efectuadas num idioma "LINQ gosto".
Para devolver todos os dados a partir da tabela, utilize o seguinte:

```
/**
 * Process the results that are received by a call to table.read()
 *
 * @param {Object} results the results as a pseudo-array
 * @param {int} results.length the length of the results array
 * @param {Object} results[] the individual results
 */
function success(results) {
   var numItemsRead = results.length;

   for (var i = 0 ; i < results.length ; i++) {
       var row = results[i];
       // Each row is an object - the properties are the columns
   }
}

function failure(error) {
    throw new Error('Error loading data: ', error);
}

table
    .read()
    .then(success, failure);
```

A função de sucesso chama-se com os resultados.   Não utilize `for (var i in results)` no sucesso funcionar como que irá iteramos sobre informações de que estão incluídas nos resultados da quando outras funções de consulta (tais como `.includeTotalCount()`) são utilizados.

Para mais informações sobre a sintaxe da consulta, consulte a [objeto documentação da consulta].

####<a name="table-filter"></a>Filtrar dados no servidor

Pode utilizar uma `where` cláusula na referência de tabela:

```
table
    .where({ userId: user.userId, complete: false })
    .read()
    .then(success, failure);
```

Também pode utilizar uma função que filtra o objeto.  Neste caso o `this` variável é atribuído ao objeto atual a ser filtrado.  Segue-se funcional equivalente para o exemplo anterior:

```
function filterByUserId(currentUserId) {
    return this.userId === currentUserId && this.complete === false;
}

table
    .where(filterByUserId, user.userId)
    .read()
    .then(success, failure);
```

####<a name="table-paging"></a>Paginação através de dados

Os métodos de take() e skip() utilizam os mesmos.  Por exemplo, se pretender dividir a tabela em linha de 100 registos:

```
var totalCount = 0, pages = 0;

// Step 1 - get the total number of records
table.includeTotalCount().take(0).read(function (results) {
    totalCount = results.totalCount;
    pages = Math.floor(totalCount/100) + 1;
    loadPage(0);
}, failure);

function loadPage(pageNum) {
    let skip = pageNum * 100;
    table.skip(skip).take(100).read(function (results) {
        for (var i = 0 ; i < results.length ; i++) {
            var row = results[i];
            // Process each row
        }
    }
}
```

O `.includeTotalCount()` método é utilizado para adicionar um campo de totalCount para o objeto de resultados.  O campo totalCount é preenchido com o número total de registos que deve ser devolvido se sem paginação for utilizada.

Em seguida, pode utilizar a variável de páginas e alguns botões da IU para fornecer uma lista de página; Utilize loadPage() para carregar os novos registos para cada página.  Deverá implementar algum tipo de colocação em cache para acelerar o acesso a registos que já foi carregado.


####<a name="sorting-data"></a>Como: devolver dados ordenados

Utilize os métodos de consulta .orderBy() ou .orderByDescending():

```
table
    .orderBy('name')
    .read()
    .then(success, failure);
```

Para mais informações sobre o objeto de consulta, consulte a [objeto documentação da consulta].

###<a name="inserting"></a>Como: Inserir dados

Crie um objeto de JavaScript com a data adequada e chame table.insert() modo assíncrono:

```
var newItem = {
    name: 'My Name',
    signupDate: new Date()
};

table
    .insert(newItem)
    .done(function (insertedItem) {
        var id = insertedItem.id;
    }, failure);
```

No inserção bem sucedida, é devolvido o item inserido com os campos adicionais que são necessários para operações de sincronização.  Deverá actualizar o seu próprio cache com estas informações para atualizações posteriores.

Repare que o Azure Mobile aplicações Node.js Server SDK suporta esquema dinâmica para fins de programação.
No caso de esquema dinâmica, o esquema da tabela é atualizado no momento, permitindo-lhe adicionar colunas à tabela apenas ao especificá-los no artigo inserir ou atualizar a operação.  Recomendamos que desative esquema dinâmica antes de passar a sua aplicação para produção.

###<a name="modifying"></a>Como: modificar os dados

Semelhante ao método .insert(), deve criar um objeto de atualização e, em seguida, chamar .update().  O objeto de atualização tem de conter o ID do registo a ser atualizados - obtido quando o registo de leitura ou quando chamar .insert().

```
var updateItem = {
    id: '7163bc7a-70b2-4dde-98e9-8818969611bd',
    name: 'My New Name'
};

table
    .update(updateItem)
    .done(function (updatedItem) {
        // You can now update your cached copy
    }, failure);
```

###<a name="deleting"></a>Como: eliminar dados

Chame o método de .del() para eliminar um registo.  Passe o ID de uma referência de objeto:

```
table
    .del({ id: '7163bc7a-70b2-4dde-98e9-8818969611bd' })
    .done(function () {
        // Record is now deleted - update your cache
    }, failure);
```
