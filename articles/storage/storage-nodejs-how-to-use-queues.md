<properties
    pageTitle="Como utilizar o armazenamento de fila de Node.js | Microsoft Azure"
    description="Saiba como utilizar o serviço do Azure fila de espera para criar e eliminar filas e inserir, obtenha e eliminar mensagens. Amostras escritas Node.js."
    services="storage"
    documentationCenter="nodejs"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robinsh"/>


# <a name="how-to-use-queue-storage-from-nodejs"></a>Como utilizar o armazenamento de fila de Node.js

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Descrição geral

Este guia mostra-lhe como efetuar cenários comuns utilizando o serviço do Microsoft Azure fila. Os exemplos estão escritos com a API Node.js. Os cenários abrangidos incluem **Inserir**, **observação**, **Introdução**e **Eliminar** mensagens de fila de espera, bem como **criar e eliminar filas**.

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Criar uma aplicação de Node.js

Crie uma aplicação de Node.js em branco. Para obter instruções sobre como criar uma aplicação de Node.js, consulte o artigo [criar uma aplicação web do Node.js na aplicação de serviço de Azure], [criar e implementar uma aplicação de Node.js para um serviço em nuvem Azure] utilizar o Windows PowerShell, ou [criar e implementar uma aplicação web do Node.js para Azure utilizando Web matriz].

## <a name="configure-your-application-to-access-storage"></a>Configurar a aplicação para o armazenamento de acesso

Para utilizar o armazenamento Azure, precisa do SDK do armazenamento Azure para Node.js, que inclui um conjunto de bibliotecas de conveniência que comunicar com os serviços de resto de armazenamento.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Utilizar o Gestor de pacote nó (NPM) para obter o pacote

1.  Utilizar uma interface da linha de comandos como **PowerShell** (Windows), **Terminal** (Mac) ou **festa** (Unix), navegue para a pasta onde a criou a aplicação de exemplo.

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

Utilizar o bloco de notas ou outro editor de texto, adicione o seguinte para o ficheiro **server.js** da aplicação, onde tenciona utilizar o armazenamento de início:

    var azure = require('azure-storage');

## <a name="setup-an-azure-storage-connection"></a>Configurar uma ligação de armazenamento Azure

O módulo azure vai ler as variáveis de ambiente AZURE\_armazenamento\_conta e AZURE\_armazenamento\_acesso\_chave ou AZURE\_armazenamento\_ligação\_cadeia para as informações necessárias para ligar à sua conta de armazenamento Azure. Se não estiver definidos estas variáveis de ambiente, tem de especificar as informações de conta quando chamar **createQueueService**.

Para obter um exemplo de definir as variáveis de ambiente no [Portal do Azure](https://portal.azure.com) para um Web site Azure, consulte o artigo [Node.js web app utilizando o serviço de tabela do Azure].

## <a name="how-to-create-a-queue"></a>Como: Criar uma fila

O código seguinte cria um objeto de **QueueService** , que lhe permite trabalhar com filas.

    var queueSvc = azure.createQueueService();

Utilize o método de **createQueueIfNotExists** que devolve a fila especificada se já existir ou cria uma nova fila com o nome especificado se ainda não existir.

    queueSvc.createQueueIfNotExists('myqueue', function(error, result, response){
      if(!error){
        // Queue created or exists
      }
    });

Se for criada fila de espera, `result.created` é verdadeira. Se não existir fila de espera, `result.created` for falsa.

### <a name="filters"></a>Filtros

Operações de filtragem opcionais podem ser aplicadas a operações realizadas utilizando **QueueService**. Operações de filtragem pode incluir registo, automaticamente a repetir, etc. Filtros são objetos que implementam um método com a assinatura:

    function handle (requestOptions, next)

Após fazer a sua pré-processamento no menu Opções de pedido, o método precisa para ligar uma chamada de retorno com a seguinte assinatura a passar "seguinte" para:

    function (returnObject, finalCallback, next)

Esta chamada de retorno e depois de processamento de returnObject (a resposta do pedido de servidor), a chamada de retorno tem invocar, em seguida, se existir para continuar a outros filtros de processamento ou simplesmente invocar finalCallback caso contrário, para fazer a invocação de serviço.

Dois filtros implementam lógica de repetição são incluídos com o SDK do Azure para Node.js, **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. A seguinte cria um objeto de **QueueService** que utiliza o **ExponentialRetryPolicyFilter**:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var queueSvc = azure.createQueueService().withFilter(retryOperations);

## <a name="how-to-insert-a-message-into-a-queue"></a>Como: Inserir uma mensagem de uma fila

Para inserir uma mensagem uma fila de espera, utilize o método de **createMessage** para criar uma nova mensagem e adicioná-lo para a fila.

    queueSvc.createMessage('myqueue', "Hello world!", function(error, result, response){
      if(!error){
        // Message inserted
      }
    });

## <a name="how-to-peek-at-the-next-message"></a>Como: Observar a mensagem seguinte

Pode observar a mensagem no início de uma fila sem removê-lo da fila de espera ao contactar o método de **peekMessages** . Por predefinição, **peekMessages** lê uma única mensagem.

    queueSvc.peekMessages('myqueue', function(error, result, response){
      if(!error){
        // Message text is in messages[0].messageText
      }
    });

O `result` contém a mensagem.

> [AZURE.NOTE] Utilizar **peekMessages** quando existem que mensagens não na fila de espera não irão devolver um erro, no entanto mensagens não serão devolvidas.

## <a name="how-to-dequeue-the-next-message"></a>Como: Descarregado a mensagem seguinte

Processamento de uma mensagem é um processo de duas fases:

1. Descarregado a mensagem.

2. Elimine a mensagem.

Para descarregado uma mensagem, utilize **getMessages**. Isto faz com as mensagens invisíveis na fila de espera, para que não existem outros clientes podem processá-las. Assim que a aplicação tem processado uma mensagem, ligue **deleteMessage** para eliminá-la a partir de fila de espera. O exemplo seguinte obtém uma mensagem, em seguida, elimina-lo:

    queueSvc.getMessages('myqueue', function(error, result, response){
      if(!error){
        // Message text is in messages[0].messageText
        var message = result[0];
        queueSvc.deleteMessage('myqueue', message.messageId, message.popReceipt, function(error, response){
          if(!error){
            //message deleted
          }
        });
      }
    });

> [AZURE.NOTE] Por predefinição, uma mensagem é só oculto para 30 segundos, após o qual está visível para os outros clientes. Pode especificar um valor diferente, utilizando `options.visibilityTimeout` com **getMessages**.

> [AZURE.NOTE]
> Utilizar **getMessages** quando existem que mensagens não na fila de espera não irão devolver um erro, no entanto mensagens não serão devolvidas.

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Como: Alterar o conteúdo de uma mensagem em fila de espera

Pode alterar os conteúdos de um mensagem no local na fila de espera utilizando **updateMessage**. O exemplo seguinte atualiza o texto de uma mensagem:

    queueSvc.getMessages('myqueue', function(error, result, response){
      if(!error){
        // Got the message
        var message = result[0];
        queueSvc.updateMessage('myqueue', message.messageId, message.popReceipt, 10, {messageText: 'new text'}, function(error, result, response){
          if(!error){
            // Message updated successfully
          }
        });
      }
    });

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Como: Mensagens de opções adicionais para a opção de retirar fila

Existem duas formas, pode personalizar a obtenção de mensagem a partir de uma fila:

* `options.numOfMessages`-Obter um lote de mensagens (até 32.)
* `options.visibilityTimeout`-Defina um limite de tempo invisibility ou mais comprida.

O exemplo seguinte utiliza o método de **getMessages** para receber mensagens de 15 numa chamada. Em seguida, processa cada mensagem utilizando um para a ligação. Também define o tempo limite invisibility para cinco minutos para que todas as mensagens devolvidas por este método.

    queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, result, response){
      if(!error){
        // Messages retreived
        for(var index in result){
          // text is available in result[index].messageText
          var message = result[index];
          queueSvc.deleteMessage(queueName, message.messageId, message.popReceipt, function(error, response){
            if(!error){
              // Message deleted
            }
          });
        }
      }
    });

## <a name="how-to-get-the-queue-length"></a>Como: Obter o comprimento da fila

O **getQueueMetadata** devolve metadados sobre fila de espera, incluindo o número aproximado de mensagens, a aguardar na fila de espera.

    queueSvc.getQueueMetadata('myqueue', function(error, result, response){
      if(!error){
        // Queue length is available in result.approximateMessageCount
      }
    });

## <a name="how-to-list-queues"></a>Como: Filas de lista

Para obter uma lista de filas, utilize **listQueuesSegmented**. Para obter uma lista filtrada por um prefixo específico, utilize **listQueuesSegmentedWithPrefix**.

    queueSvc.listQueuesSegmented(null, function(error, result, response){
      if(!error){
        // result.entries contains the list of queues
      }
    });

Se não podem ser devolvidas todas as filas, `result.continuationToken` podem ser utilizados como primeiro parâmetro da **listQueuesSegmented** ou ao segundo parâmetro da **listQueuesSegmentedWithPrefix** para obter mais resultados.

## <a name="how-to-delete-a-queue"></a>Como: Eliminar uma fila

Para eliminar uma fila e todas as mensagens contidas na mesma, chame o método de **deleteQueue** no objeto fila de espera.

    queueSvc.deleteQueue(queueName, function(error, response){
      if(!error){
        // Queue has been deleted
      }
    });

Para limpar todas as mensagens de uma fila sem eliminá-la, utilize **clearMessages**.

## <a name="how-to-work-with-shared-access-signatures"></a>Como: trabalhar com acesso partilhado assinaturas

Partilhada acesso assinaturas (SA) são uma forma segura para fornecer acesso granular a filas sem facultar o seu nome de conta de armazenamento ou teclas. SA são utilizadas com frequência para fornecer acesso limitado ao seu filas, tais como permitir que uma aplicação móvel submeter mensagens.

Uma aplicação de confiança como um serviço baseado na nuvem gera uma SA utilizando o **generateSharedAccessSignature** do **QueueService**e fornece-o a uma aplicação de semiestruturados fidedigna ou não fidedigna. Por exemplo, uma aplicação móvel. As associações de segurança são gerada utilizando uma política que descreve as datas de início e de fim durante o qual as associações de segurança são válida, bem como o nível de acesso ao titular SA.

O exemplo seguinte gera uma nova política de acesso partilhado que irá permitir que o titular SA adicionar mensagens a fila de espera e expira 100 minutos após a hora que é criada.

    var startDate = new Date();
    var expiryDate = new Date(startDate);
    expiryDate.setMinutes(startDate.getMinutes() + 100);
    startDate.setMinutes(startDate.getMinutes() - 100);

    var sharedAccessPolicy = {
      AccessPolicy: {
        Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
        Start: startDate,
        Expiry: expiryDate
      }
    };

    var queueSAS = queueSvc.generateSharedAccessSignature('myqueue', sharedAccessPolicy);
    var host = queueSvc.host;

Tenha em atenção que as informações de anfitrião devem ser fornecidas de além disso, conforme for necessário quando o titular SA tenta aceder fila de espera.

Em seguida, a aplicação de cliente utiliza as associações de segurança com **QueueServiceWithSAS** para efetuar operações contra fila de espera. O exemplo seguinte liga para a fila e cria uma mensagem.

    var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
    sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
      if(!error){
        //message added
      }
    });

Uma vez que as associações de segurança foram criada com o acesso de adicionar, se foram feita uma tentativa para ler, atualizar ou eliminar mensagens, deverá ser devolvido um erro.

### <a name="access-control-lists"></a>Listas de controlo de acesso

Também pode utilizar uma lista de controlo de acesso (ACL) para definir a política de acesso para uma SA. Isto é útil se pretender permitir que vários clientes aceder a fila de espera, mas não fornecer políticas de acesso diferentes para cada cliente.

Uma ACL é implementada utilizando uma matriz de políticas de acesso, com um ID associado a cada política. O exemplo seguinte define duas políticas; um para 'Utilizador1' e outra para 'Utilizador2':

    var sharedAccessPolicy = {
      user1: {
        Permissions: azure.QueueUtilities.SharedAccessPermissions.PROCESS,
        Start: startDate,
        Expiry: expiryDate
      },
      user2: {
        Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
        Start: startDate,
        Expiry: expiryDate
      }
    };

O exemplo seguinte obtém a ACL atual **MinhaFila**, em seguida, adiciona as novas políticas utilizando **setQueueAcl**. Esta abordagem permite:

    var extend = require('extend');
    queueSvc.getQueueAcl('myqueue', function(error, result, response) {
      if(!error){
        var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
        queueSvc.setQueueAcl('myqueue', newSignedIdentifiers, function(error, result, response){
          if(!error){
            // ACL set
          }
        });
      }
    });

Assim que tiver sido definida a ACL, em seguida, pode criar uma SA com base no ID de uma política. O exemplo seguinte cria uma nova SA para 'Utilizador2':

    queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu os princípios básicos de armazenamento de fila de espera, siga estas ligações para saber mais sobre tarefas de armazenamento mais complexas.

-   Visite o [blogue da equipa do armazenamento Azure][].
-   Visite o repositório [Azure SDK de armazenamento para nó][] no GitHub.

  [Armazenamento SDK para o nó do Azure]: https://github.com/Azure/azure-storage-node
  [using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
  [Azure Portal]: https://portal.azure.com
  [Criar uma aplicação web do Node.js no serviço de aplicação do Azure]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
  [Node.js Cloud Service with Storage]: ../cloud-services/storage-nodejs-use-table-storage-cloud-service-app.md
  [NODE.js web app utilizando o serviço de tabela do Azure]: ../app-service-web/storage-nodejs-use-table-storage-web-site.md


  [Queue1]: ./media/storage-nodejs-how-to-use-queues/queue1.png
  [plus-new]: ./media/storage-nodejs-how-to-use-queues/plus-new.png
  [quick-create-storage]: ./media/storage-nodejs-how-to-use-queues/quick-storage.png



  [Criar e implementar uma aplicação de Node.js num serviço de nuvem do Azure]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
  [Blogue da equipa do armazenamento Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Criar e implementar uma aplicação web do Node.js para Azure utilizando Web matriz]: ../app-service-web/web-sites-nodejs-use-webmatrix.md
