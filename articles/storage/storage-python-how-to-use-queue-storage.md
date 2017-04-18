<properties
    pageTitle="Como utilizar o armazenamento de fila de Python | Microsoft Azure"
    description="Saiba como utilizar o serviço de Azure fila do Python para criar e eliminar filas e inserir, obtenha e eliminar mensagens."
    services="storage"
    documentationCenter="python"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="robinsh"/>

# <a name="how-to-use-queue-storage-from-python"></a>Como utilizar o armazenamento de fila de Python

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Descrição geral

Este guia mostra-lhe como efetuar cenários comuns utilizando o serviço de armazenamento do Azure fila de espera. Os exemplos são gravados no Python e utilizam o [Microsoft Azure armazenamento SDK para Python]. Os cenários abrangidos incluem **Inserir**, **observação**, **Introdução**e **Eliminar** mensagens de fila de espera, bem como **criar e eliminar filas**. Para mais informações sobre filas, consulte a secção [próximos passos].

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="how-to-create-a-queue"></a>Como: Criar uma fila

O objeto **QueueService** permite-lhe trabalhar com filas. O código seguinte cria um objeto de **QueueService** . Adicione o seguinte na parte superior de qualquer ficheiro Python na qual pretende aceder programaticamente armazenamento do Windows Azure:

    from azure.storage.queue import QueueService

O código seguinte cria um objeto de **QueueService** utilizando a chave de nome e a conta da conta de armazenamento. Substitua 'minha conta' e 'mykey' com o seu nome de conta e chave.

    queue_service = QueueService(account_name='myaccount', account_key='mykey')

    queue_service.create_queue('taskqueue')


## <a name="how-to-insert-a-message-into-a-queue"></a>Como: Inserir uma mensagem de uma fila

Para inserir uma mensagem para uma fila de espera, utilize o **colocar\_mensagem** método para criar uma nova mensagem e adicioná-lo para a fila.

    queue_service.put_message('taskqueue', u'Hello World')


## <a name="how-to-peek-at-the-next-message"></a>Como: Observar a mensagem seguinte

Pode observar a mensagem no início de uma fila sem removê-lo da fila de espera ao contactar o **pré-visualização\_mensagens** método. Por predefinição, **pré-visualização\_mensagens** observa uma única mensagem.

    messages = queue_service.peek_messages('taskqueue')
    for message in messages:
        print(message.content)


## <a name="how-to-dequeue-messages"></a>Como: Descarregado mensagens

O código remove uma mensagem de uma fila em dois passos. Quando ligar para o **obter\_mensagens**, obtém a mensagem seguinte numa fila por predefinição. Uma mensagem devolvida por **obter\_mensagens** invisível para qualquer outro código ler mensagens a partir desta fila. Por predefinição, esta mensagem permanece invisível para 30 segundos. Para concluir a remoção da mensagem de fila de espera, tem de chamar também **Eliminar\_mensagem**. Este processo de dois passos de remoção de uma mensagem assegura que quando o seu código falha processar uma mensagem devido a falha do software ou hardware, noutra instância do seu código pode aceder a mesma mensagem e tente novamente. As chamadas de código **Eliminar\_mensagem** logo depois da mensagem foi processada.

    messages = queue_service.get_messages('taskqueue')
    for message in messages:
        print(message.content)
        queue_service.delete_message('taskqueue', message.id, message.pop_receipt)

Existem duas formas, pode personalizar a obtenção de mensagem a partir de uma fila.
Em primeiro lugar, pode obter um lote de mensagens (até 32). Em segundo lugar, pode definir um limite de tempo invisibility ou mais comprida, permitindo que o seu código mais ou menos tempo para processar totalmente cada mensagem. O seguinte código de exemplo utiliza a **obter\_mensagens** método para receber mensagens de 16 numa chamada. Em seguida, processa cada mensagem utilizando um para a ligação. Também define o tempo limite invisibility para cinco minutos para cada mensagem.

    messages = queue_service.get_messages('taskqueue', num_messages=16, visibility_timeout=5*60)
    for message in messages:
        print(message.content)
        queue_service.delete_message('taskqueue', message.id, message.pop_receipt)      


## <a name="how-to-change-the-contents-of-a-queued-message"></a>Como: Alterar o conteúdo de uma mensagem em fila de espera

Pode alterar os conteúdos de um mensagem no local na fila de espera. Se a mensagem representa uma tarefa de trabalho, pode utilizar esta funcionalidade para atualizar o estado da tarefa de trabalho. O código abaixo utiliza o **Atualizar\_mensagem** método para actualizar uma mensagem. O tempo limite visibilidade está definido como 0, o que significa que a mensagem é apresentada imediatamente e o conteúdo é atualizado.

    messages = queue_service.get_messages('taskqueue')
    for message in messages:
        queue_service.update_message('taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')

## <a name="how-to-get-the-queue-length"></a>Como: Obter o comprimento da fila

Pode obter uma estimativa do número de mensagens de uma fila. O **obter\_fila\_metadados** método pede o serviço de fila de espera para devolver metadados sobre fila de espera e o **approximate_message_count**. O resultado é aproximado apenas uma vez que podem ser adicionadas ou removidas depois do serviço de fila responde ao seu pedido de mensagens.

    metadata = queue_service.get_queue_metadata('taskqueue')
    count = metadata.approximate_message_count

## <a name="how-to-delete-a-queue"></a>Como: Eliminar uma fila

Para eliminar uma fila e todas as mensagens contidas na mesma, contacte o **Eliminar\_fila** método.

    queue_service.delete_queue('taskqueue')

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu os princípios básicos de armazenamento de fila de espera, siga estas ligações para mais informações.

- [Centro de programadores do Python](/develop/python/)
- [Serviços de armazenamento Azure REST API](http://msdn.microsoft.com/library/azure/dd179355)
- [Blogue da equipa do armazenamento Azure]
- [Armazenamento do Microsoft Azure SDK para Python]

[Blogue da equipa do armazenamento Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Armazenamento do Microsoft Azure SDK para Python]: https://github.com/Azure/azure-storage-python
