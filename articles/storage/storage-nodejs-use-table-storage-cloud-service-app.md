<properties 
    pageTitle="Web app com o armazenamento de tabela (Node.js) | Microsoft Azure" 
    description="Tutorial que constrói a aplicação Web com Express tutorial ao adicionar serviços de armazenamento do Windows Azure e o módulo Azure." 
    services="cloud-services, storage" 
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
    ms.date="10/18/2016" 
    ms.author="robmcm"/>

# <a name="nodejs-web-application-using-storage"></a>NODE.js aplicação Web armazenamento

## <a name="overview"></a>Descrição geral

Neste tutorial, irá expandir a aplicação criada no tutorial [Node.js aplicação Web Express] ao utilizar as bibliotecas de cliente do Microsoft Azure para Node.js para trabalhar com os serviços de gestão de dados. Irá expandir a sua aplicação para criar uma aplicação baseada na web-lista de tarefas que pode implementar Azure. A lista de tarefas permite que um utilizador obter tarefas, adicionar novas tarefas e tarefas de marcar como concluída.

Os itens de tarefas são armazenados no armazenamento do Windows Azure. Armazenamento Azure fornece armazenamento de dados não estruturados tolerância a falhas e altamente disponível. Armazenamento Azure inclui várias estruturas de dados onde pode armazenar e aceder a dados e, pode tirar partido dos serviços de armazenamento de APIs incluídos no Azure SDK para Node.js ou através do REST APIs. Para mais informações, consulte o artigo [armazenar e aceder a dados no Azure].

Neste tutorial assume que concluiu os [Aplicação de Web Node.js] e [Node.js com Express][Node.js Web aplicação utilizando Express] tutoriais.

Vai aprender:

-   Como trabalhar com o motor de modelo Jade
-   Como trabalhar com os serviços de gestão de dados do Azure

Uma captura de ecrã da aplicação concluída é abaixo:

![A página web concluída no internet explorer](./media/storage-nodejs-use-table-storage-cloud-service-app/getting-started-1.png)

## <a name="setting-storage-credentials-in-webconfig"></a>Definir as credenciais de armazenamento na Web. config

Para aceder ao armazenamento do Windows Azure, tem de ser efetuada com credenciais de armazenamento. Para fazer isto, use as definições da aplicação Web. config.
Estas definições serão transmitir como variáveis de ambiente à nó, que são lidos em seguida, pelo SDK do Azure.

> [AZURE.NOTE] Credenciais do armazenamento só são utilizadas quando a aplicação é implementada para Azure. Quando a executar o emulador, a aplicação irá utilizar o emulador de armazenamento.

Execute os seguintes passos para obter as credenciais da conta de armazenamento e adicioná-los às definições do Web. config:

1.  Se ainda não esteja abrir, comece o PowerShell Azure a partir do menu **Iniciar** por expandir **todos os programas, Azure**, com o botão direito **Do PowerShell do Azure**e, em seguida, selecione **Executar como administrador**.

2.  Alterar directórios para a pasta que contém a sua aplicação. Por exemplo, c:\\nó\\lista de tarefas\\WebRole1.

3.  A partir da janela do Azure Powershell introduza o seguinte cmdlet para obter as informações de conta de armazenamento:

        PS C:\node\tasklist\WebRole1> Get-AzureStorageAccounts

    Isto copia a lista de contas de armazenamento e conta teclas associado ao seu serviço alojado.

    > [AZURE.NOTE] Uma vez que o SDK do Azure cria uma conta de armazenamento quando implementar um serviço, uma conta de armazenamento deve já existir de implementar a aplicação nas guias anterior.

4.  Abra o ficheiro de **ServiceDefinition.csdef** que contém as definições de ambiente que são utilizadas quando a aplicação é implementada para Azure:

        PS C:\node\tasklist> notepad ServiceDefinition.csdef

5.  Inseri o bloco seguinte em elemento **ambiente** , substituindo {conta de armazenamento} e {tecla de acesso de armazenamento} com o nome da conta e a chave primária da conta de armazenamento que pretende utilizar para implementação:

        <Variable name="AZURE_STORAGE_ACCOUNT" value="{STORAGE ACCOUNT}" />
        <Variable name="AZURE_STORAGE_ACCESS_KEY" value="{STORAGE ACCESS KEY}" />

    ![O conteúdo do ficheiro web.cloud.config](./media/storage-nodejs-use-table-storage-cloud-service-app/node37.png)

6.  Guarde o ficheiro e feche o bloco de notas.

### <a name="install-additional-modules"></a>Instalar módulos adicionais

2. Utilize o seguinte comando para instalar [azure], [uuid nó], [nconf] e [assíncrona] módulos localmente, bem como para guardar uma entrada das mesmas no ficheiro **package.json** :

        PS C:\node\tasklist\WebRole1> npm install azure-storage node-uuid async nconf --save

    O resultado deste comando deve aparecer semelhante ao seguinte:

        node-uuid@1.4.1 node_modules\node-uuid

        nconf@0.6.9 node_modules\nconf
        ├── ini@1.1.0
        ├── async@0.2.9
        └── optimist@0.6.0 (wordwrap@0.0.2, minimist@0.0.8)

        azure-storage@0.1.0 node_modules\azure-storage
        ├── extend@1.2.1
        ├── xmlbuilder@0.4.3
        ├── mime@1.2.11
        ├── underscore@1.4.4
        ├── validator@3.1.0
        ├── node-uuid@1.4.1
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)

##<a name="using-the-table-service-in-a-node-application"></a>Utilizar o serviço de tabela numa aplicação nó

Nesta secção irá expandir a aplicação básica criada pelo comando **express** ao adicionar um ficheiro de **task.js** que contém o modelo para as suas tarefas. Também serão modificar o **app.js** existente e criar um novo ficheiro **tasklist.js** que utiliza o modelo.

### <a name="create-the-model"></a>Criar o modelo

1. No diretório **WebRole1** , crie um novo directório denominado **modelos**.

2. No directório de **modelos** , crie um novo ficheiro chamado **task.js**. Este ficheiro irá conter o modelo para as tarefas criados pela aplicação.

3. No início do ficheiro **task.js** , adicione o seguinte código para fazer referência a bibliotecas necessárias:

        var azure = require('azure-storage');
        var uuid = require('node-uuid');
        var entityGen = azure.TableUtilities.entityGenerator;

4. Em seguida, irá adicionar código para definir e exportar o objeto de tarefa. Este objeto é responsável por ligar-se para a tabela.

        module.exports = Task;

        function Task(storageClient, tableName, partitionKey) {
          this.storageClient = storageClient;
          this.tableName = tableName;
          this.partitionKey = partitionKey;
          this.storageClient.createTableIfNotExists(tableName, function tableCreated(error) {
            if(error) {
              throw error;
            }
          });
        };

5. Em seguida, adicione o seguinte código para definir métodos adicionais no objeto tarefa, que permitem interações com dados armazenados na tabela:

        Task.prototype = {
          find: function(query, callback) {
            self = this;
            self.storageClient.queryEntities(query, function entitiesQueried(error, result) {
              if(error) {
                callback(error);
              } else {
                callback(null, result.entries);
              }
            });
          },

          addItem: function(item, callback) {
            self = this;
            // use entityGenerator to set types
            // NOTE: RowKey must be a string type, even though
            // it contains a GUID in this example.
            var itemDescriptor = {
              PartitionKey: entityGen.String(self.partitionKey),
              RowKey: entityGen.String(uuid()),
              name: entityGen.String(item.name),
              category: entityGen.String(item.category),
              completed: entityGen.Boolean(false)
            };

            self.storageClient.insertEntity(self.tableName, itemDescriptor, function entityInserted(error) {
              if(error){  
                callback(error);
              }
              callback(null);
            });
          },

          updateItem: function(rKey, callback) {
            self = this;
            self.storageClient.retrieveEntity(self.tableName, self.partitionKey, rKey, function entityQueried(error, entity) {
              if(error) {
                callback(error);
              }
              entity.completed._ = true;
              self.storageClient.updateEntity(self.tableName, entity, function entityUpdated(error) {
                if(error) {
                  callback(error);
                }
                callback(null);
              });
            });
          }
        }

6. Guarde e feche o ficheiro **task.js** .

### <a name="create-the-controller"></a>Criar o controlador de

1. No diretório **WebRole1/rotas** , crie um novo ficheiro chamado **tasklist.js** e abra-o num editor de texto.

2. Adicione o seguinte código para **tasklist.js**. Este procedimento carrega a azure e assíncrona módulos, quais são utilizados pelo **tasklist.js**. Isto também define a função de **lista de tarefas** , que lhe é transmitida uma instância do objeto **tarefa** definido anteriormente:

        var azure = require('azure-storage');
        var async = require('async');

        module.exports = TaskList;

        function TaskList(task) {
          this.task = task;
        }

2. Continue a adicionar o ficheiro **tasklist.js** ao adicionar os métodos utilizados para **showTasks**, **addTask**e **completeTasks**:

        TaskList.prototype = {
          showTasks: function(req, res) {
            self = this;
            var query = azure.TableQuery()
              .where('completed eq ?', false);
            self.task.find(query, function itemsFound(error, items) {
              res.render('index',{title: 'My ToDo List ', tasks: items});
            });
          },

          addTask: function(req,res) {
            var self = this      
            var item = req.body.item;
            self.task.addItem(item, function itemAdded(error) {
              if(error) {
                throw error;
              }
              res.redirect('/');
            });
          },

          completeTask: function(req,res) {
            var self = this;
            var completedTasks = Object.keys(req.body);
            async.forEach(completedTasks, function taskIterator(completedTask, callback) {
              self.task.updateItem(completedTask, function itemsUpdated(error) {
                if(error){
                  callback(error);
                } else {
                  callback(null);
                }
              });
            }, function goHome(error){
              if(error) {
                throw error;
              } else {
               res.redirect('/');
              }
            });
          }
        }

3. Guarde o ficheiro **tasklist.js** .

### <a name="modify-appjs"></a>Modificar app.js

1. No diretório **WebRole1** , abra o ficheiro **app.js** num editor de texto. 

2. No início do ficheiro, adicione o seguinte para carregar o módulo azure e definir a chave de nome e a partições tabela:

        var azure = require('azure-storage');
        var tableName = 'tasks';
        var partitionKey = 'hometasks';

3. No ficheiro de app.js, desloque-se para baixo até onde ver a linha seguinte:

        app.use('/', routes);
        app.use('/users', users);

    Substitua as linhas acima com o código mostrado abaixo. Isto irá iniciar uma instância de <strong>tarefa</strong> com uma ligação à sua conta de armazenamento. Isto é transmitido para a <strong>lista de tarefas</strong>, que irá utilizá-lo para comunicar com o serviço de tabela:

        var TaskList = require('./routes/tasklist');
        var Task = require('./models/task');
        var task = new Task(azure.createTableService(), tableName, partitionKey);
        var taskList = new TaskList(task);

        app.get('/', taskList.showTasks.bind(taskList));
        app.post('/addtask', taskList.addTask.bind(taskList));
        app.post('/completetask', taskList.completeTask.bind(taskList));
    
4. Guarde o ficheiro **app.js** .

### <a name="modify-the-index-view"></a>Modificar a vista de índice remissivo

1. Alterar diretórios para o diretório de **vistas** e abra o ficheiro de **index.jade** num editor de texto.

2. Substitua o conteúdo do ficheiro **index.jade** com o código abaixo. Isto define a vista para apresentar as tarefas existentes, bem como um formulário para adicionar novas tarefas e já existentes de marcar como concluída.

        extends layout

        block content
          h1= title
          br
        
          form(action="/completetask", method="post")
            table.table.table-striped.table-bordered
              tr
                td Name
                td Category
                td Date
                td Complete
              if tasks != []
                tr
                  td 
              else
                each task in tasks
                  tr
                    td #{task.name._}
                    td #{task.category._}
                    - var day   = task.Timestamp._.getDate();
                    - var month = task.Timestamp._.getMonth() + 1;
                    - var year  = task.Timestamp._.getFullYear();
                    td #{month + "/" + day + "/" + year}
                    td
                      input(type="checkbox", name="#{task.RowKey._}", value="#{!task.completed._}", checked=task.completed._)
            button.btn(type="submit") Update tasks
          hr
          form.well(action="/addtask", method="post")
            label Item Name: 
            input(name="item[name]", type="textbox")
            label Item Category: 
            input(name="item[category]", type="textbox")
            br
            button.btn(type="submit") Add item

3. Guarde e feche o ficheiro **index.jade** .

### <a name="modify-the-global-layout"></a>Modificar o esquema global

O ficheiro **layout.jade** no diretório **vistas** é utilizado como um modelo global para outros ficheiros **.jade** . Neste passo será modificá-lo para utilizar o [Arranque Twitter](https://github.com/twbs/bootstrap), que é um toolkit que torna mais fácil criar um Web site com um aspecto totalmente.

1. Transfira e extrair os ficheiros para [Twitter arranque](http://getbootstrap.com/). Copie o ficheiro **bootstrap.min.css** a partir do **arranque\\EXPON\\css** pasta para a **público\\folhas de estilo** directório da sua aplicação de lista de tarefas.

2. A partir da pasta de **vistas** , abra o **layout.jade** no seu editor de texto e substituir o conteúdo com o seguinte:

        doctype html
        html
          head
            title= title
            link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')
            link(rel='stylesheet', href='/stylesheets/style.css')
          body.app
            nav.navbar.navbar-default
              div.navbar-header
                a.navbar-brand(href='/') My Tasks
            block content

3. Guarde o ficheiro **layout.jade** .

### <a name="running-the-application-in-the-emulator"></a>Executar a aplicação no emulador

Utilize o seguinte comando para iniciar a aplicação no emulador.

    PS C:\node\tasklist\WebRole1> start-azureemulator -launch

No browser será aberto e apresenta a página seguinte:

![Uma web paginada intitulado minha lista de tarefas com uma tabela que contém a tarefas e campos para adicionar uma nova tarefa.](./media/storage-nodejs-use-table-storage-cloud-service-app/node44.png)

Utilize o formulário para adicionar itens ou remover itens existentes marcando-os como concluída.

## <a name="publishing-the-application-to-azure"></a>A aplicação para Azure de publicação


Na janela do Windows PowerShell, ligue para o seguinte cmdlet a implementar o serviço de alojado Azure.

    PS C:\node\tasklist\WebRole1> Publish-AzureServiceProject -name myuniquename -location datacentername -launch

Substitua **myuniquename** com um nome exclusivo para esta aplicação. Substitua o nome de um centro de dados Azure, tal como **EUA Ocidental** **datacentername** .

Depois da implementação estiver concluída, deverá ver uma resposta semelhante ao seguinte:

    PS C:\node\tasklist> publish-azureserviceproject -servicename tasklist -location "West US"
    WARNING: Publishing tasklist to Microsoft Azure. This may take several minutes...
    WARNING: 2:18:42 PM - Preparing runtime deployment for service 'tasklist'
    WARNING: 2:18:42 PM - Verifying storage account 'tasklist'...
    WARNING: 2:18:43 PM - Preparing deployment for tasklist with Subscription ID: 65a1016d-0f67-45d2-b838-b8f373d6d52e...
    WARNING: 2:19:01 PM - Connecting...
    WARNING: 2:19:02 PM - Uploading Package to storage service larrystore...
    WARNING: 2:19:40 PM - Upgrading...
    WARNING: 2:22:48 PM - Created Deployment ID: b7134ab29b1249ff84ada2bd157f296a.
    WARNING: 2:22:48 PM - Initializing...
    WARNING: 2:22:49 PM - Instance WebRole1_IN_0 of role WebRole1 is ready.
    WARNING: 2:22:50 PM - Created Website URL: http://tasklist.cloudapp.net/.

Como antes, uma vez que especificou a **-Iniciação** opção, o browser abre e apresenta a sua aplicação em execução no Azure quando é concluída a publicação.

![Numa janela do browser a apresentar a página da minha lista de tarefas. O URL indica que a página está atualmente a ser alojada no Azure.](./media/storage-nodejs-use-table-storage-cloud-service-app/getting-started-1.png)

## <a name="stopping-and-deleting-your-application"></a>Parar a e eliminar a sua aplicação

Depois de o implementar a aplicação, poderá pretender desativá-lo para que possa evitar custos ou criar e implementar outras aplicações dentro do período de tempo de avaliação gratuita.

Faturas Azure web instâncias de função por hora de tempo de servidor consumida.
Hora do servidor é consumida assim que a sua aplicação é implementada, mesmo se as instâncias não estiver a executar e são no estado de parado.

Os passos seguintes mostram-lhe como parar e eliminar a aplicação.

1.  Na janela do Windows PowerShell, pare a implementação do service criada na secção anterior com o cmdlet seguinte:

        PS C:\node\tasklist\WebRole1> Stop-AzureService

    Parar o serviço poderá demorar vários minutos. Quando o serviço está parado, recebe uma mensagem que indica que deixou de.

3.  Para eliminar o serviço, contacte o cmdlet seguinte:

        PS C:\node\tasklist\WebRole1> Remove-AzureService contosotasklist

    Quando lhe for pedido, introduza **Y** para eliminar o serviço.

    Eliminar o serviço poderá demorar vários minutos. Depois do serviço foi eliminado recebe uma mensagem a indicar que o serviço foi eliminado.

  [NODE.js aplicação Web Express]: http://azure.microsoft.com/develop/nodejs/tutorials/web-app-with-express/
  [Armazenar e aceder a dados no Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
  [Aplicação node.js Web]: http://azure.microsoft.com/develop/nodejs/tutorials/getting-started/
 
 
