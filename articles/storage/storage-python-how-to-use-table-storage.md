<properties
    pageTitle="Como utilizar o armazenamento de tabela a partir do Python | Microsoft Azure"
    description="Armazene dados estruturados na nuvem através do armazenamento de tabela do Azure, um arquivo de dados NoSQL."
    services="storage"
    documentationCenter="python"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="how-to-use-table-storage-from-python"></a>Como utilizar o armazenamento de tabela a partir do Python

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Descrição geral

Este guia mostra como executar cenários comuns utilizando o serviço de armazenamento de tabela do Azure. Os exemplos são gravados no Python e utilizam o [Microsoft Azure armazenamento SDK para Python]. Os cenários abrangidos incluem criar e eliminar uma tabela, para além de inserir e consultar entidades numa tabela.

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-table"></a>Criar uma tabela

O objeto **TableService** permite-lhe funcionam com serviços de tabela. O código seguinte cria um objeto de **TableService** . Adicione o código na parte superior de qualquer ficheiro Python onde pretender aceder programaticamente armazenamento do Windows Azure:

    from azure.storage.table import TableService, Entity

O código seguinte cria um objeto de **TableService** ao utilizar a chave de nome e a conta da conta de armazenamento.  Substitua 'minha conta' e 'mykey' com o seu nome de conta e chave.

    table_service = TableService(account_name='myaccount', account_key='mykey')

    table_service.create_table('tasktable')

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela

Para adicionar uma entidade, crie primeiro um dicionário ou entidade que define os valores e nomes de propriedades de entidade. Tenha em atenção que para cada entidade, tem de especificar **PartitionKey** e **RowKey**. Estes são os identificadores exclusivos das entidades. Pode consultar a utilizar estes valores muito mais depressa do que pode consultar o seu outras propriedades. O sistema utiliza **PartitionKey** para distribuir automaticamente as entidades de tabela sobre demasiados nós de armazenamento.
Entidades que tenham o mesmo **PartitionKey** são armazenadas no mesmo nó. **RowKey** é o ID exclusivo da entidade dentro da partição que pertence.

Para adicionar uma entidade à sua tabela, passar um objeto de dicionário para o **Inserir\_entidade** método.

    task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the trash', 'priority' : 200}
    table_service.insert_entity('tasktable', task)

Também pode ser efetuada com uma instância da classe **entidade** para o **Inserir\_entidade** método.

    task = Entity()
    task.PartitionKey = 'tasksSeattle'
    task.RowKey = '2'
    task.description = 'Wash the car'
    task.priority = 100
    table_service.insert_entity('tasktable', task)

## <a name="update-an-entity"></a>Atualizar uma entidade

Este código mostra como substituir a versão antiga de uma entidade existente por uma versão atualizada.

    task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the garbage', 'priority' : 250}
    table_service.update_entity('tasktable', 'tasksSeattle', '1', task, content_type='application/atom+xml')

Se a entidade que está a ser atualizada não existir, em seguida, a operação de atualização falhará. Se pretender armazenar uma entidade, independentemente de se existia antes, utilize **Inserir\_ou\_replace_entity**.
No seguinte exemplo, a primeira chamada irá substituir a entidade existente. A segunda chamada irá inserir uma nova entidade, desde que não existe uma entidade com o especificado **PartitionKey** e **RowKey** existe na tabela.

    task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the garbage again', 'priority' : 250}
    table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task, content_type='application/atom+xml')

    task = {'PartitionKey': 'tasksSeattle', 'RowKey': '3', 'description' : 'Buy detergent', 'priority' : 300}
    table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task, content_type='application/atom+xml')

## <a name="change-a-group-of-entities"></a>Alterar um grupo de entidades

Por vezes, faz sentido para submeter várias operações em conjunto num lote para se certificar de atómica processamento pelo servidor. Para efetuar a que, utilize a classe de **TableBatch** . Quando pretender apresentar o lote, ligar para o **consolidar\_lote**. Tenha em atenção que todas as entidades tem de ser na mesma partição para ser alteradas como um lote de. O exemplo abaixo adiciona duas entidades em conjunto num lote.

    from azure.storage.table import TableBatch
    batch = TableBatch()
    task10 = {'PartitionKey': 'tasksSeattle', 'RowKey': '10', 'description' : 'Go grocery shopping', 'priority' : 400}
    task11 = {'PartitionKey': 'tasksSeattle', 'RowKey': '11', 'description' : 'Clean the bathroom', 'priority' : 100}
    batch.insert_entity(task10)
    batch.insert_entity(task11)
    table_service.commit_batch('tasktable', batch)

Secções podem também ser utilizadas com a sintaxe de gestor ao contexto:

    task12 = {'PartitionKey': 'tasksSeattle', 'RowKey': '12', 'description' : 'Go grocery shopping', 'priority' : 400}
    task13 = {'PartitionKey': 'tasksSeattle', 'RowKey': '13', 'description' : 'Clean the bathroom', 'priority' : 100}

    with table_service.batch('tasktable') as batch:
        batch.insert_entity(task12)
        batch.insert_entity(task13)


## <a name="query-for-an-entity"></a>Consulta para uma entidade

Para consultar uma entidade numa tabela, utilize o **obter\_entidade** método, passando **PartitionKey** e **RowKey**.

    task = table_service.get_entity('tasktable', 'tasksSeattle', '1')
    print(task.description)
    print(task.priority)

## <a name="query-a-set-of-entities"></a>Um conjunto de entidades de consulta

Este exemplo localiza que todas as tarefas de Seattle com base em **PartitionKey**.

    tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'")
    for task in tasks:
        print(task.description)
        print(task.priority)

## <a name="query-a-subset-of-entity-properties"></a>Um subconjunto de propriedades entidade de consulta

Uma consulta a uma tabela pode obter propriedades apenas alguns a partir de uma entidade.
Esta técnica, denominada *projecções*, reduz largura de banda e pode melhorar o desempenho da consulta, especialmente para entidades muito grandes. Utilizar o parâmetro **Selecione** e passam os nomes das propriedades que pretende trazer ao longo para o cliente.

A consulta no código que se segue devolve apenas as descrições dos entidades na tabela.

[AZURE.NOTE] O fragmento que se segue funciona apenas contra o serviço de armazenamento na nuvem. Não é suportado pelo emulador armazenamento.

    tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'", select='description')
    for task in tasks:
        print(task.description)

## <a name="delete-an-entity"></a>Eliminar uma entidade

Pode eliminar uma entidade utilizando a sua chave partição e linha.

    table_service.delete_entity('tasktable', 'tasksSeattle', '1')

## <a name="delete-a-table"></a>Eliminar uma tabela

O código seguinte elimina uma tabela a partir de uma conta de armazenamento.

    table_service.delete_table('tasktable')

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu os princípios básicos de armazenamento de tabela, siga estas ligações para mais informações.

- [Centro de programadores do Python](/develop/python/)
- [Serviços de armazenamento Azure REST API](http://msdn.microsoft.com/library/azure/dd179355)
- [Blogue da equipa do armazenamento Azure]
- [Armazenamento do Microsoft Azure SDK para Python]

[Blogue da equipa de armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Armazenamento do Microsoft Azure SDK para Python]: https://github.com/Azure/azure-storage-python
