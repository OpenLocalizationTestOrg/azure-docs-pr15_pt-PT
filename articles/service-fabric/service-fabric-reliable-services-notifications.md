<properties
   pageTitle="Notificações de serviços fiáveis | Microsoft Azure"
   description="Documentação conceptual para notificações de serviço ferro fiável serviços"
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="masnider,vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="mcoskun"/>

# <a name="reliable-services-notifications"></a>Notificações de serviços fiáveis

Notificações de permitir que os clientes controlar as alterações que estão a ser efetuadas a um objeto que interessa no. Notificações de suporte de dois tipos de objetos: *Fiável Gestor de estado* e *Fiável dicionário*.

Comuns razões para utilizar notificações são:

- Edifício ocorrência vistas, como índices secundários ou de um agregado vistas filtradas de estado a réplica. Um exemplo é um índice ordenado de todas as teclas no dicionário fiável.
- Enviar dados de monitorização, tais como o número de utilizadores adicionados na última hora.

Notificações são chama como parte da aplicação de operações. Devido a que, notificações devem ser processadas tão rapidamente como eventos possíveis e síncronos não devem incluir qualquer dispendiosas operações.

## <a name="reliable-state-manager-notifications"></a>Notificações de Gestor de estado de fiáveis

Gestor de estado de fiáveis fornece notificações para os seguintes eventos:

- Transação
    - Consolidar
- Gestor de estado
    - Reconstruir
    - Adição de um Estado fiável
    - Remoção de um Estado de membro fiável

Gestor de estado de fiáveis controla as transações de permitam atual. A única alteração no estado de transação que faz com que uma notificação é é uma operação a ser consolidada.

Gestor de estado de fiáveis mantém uma coleção de Estados-membros fiáveis, como o dicionário fiável e fiável fila de espera. Gestor de estado de fiáveis é acionada notificações quando altera nesta coleção: um Estado fiável é adicionado ou removido ou toda a colecção é recriada.
A coleção de fiável Gestor de estado é recriada em três casos:

- Recuperação: Quando uma réplica é iniciado, recupera estado anterior do disco. No final de recuperação, utiliza **NotifyStateManagerChangedEventArgs** seja acionada um evento que contém o conjunto de Estados-membros fiáveis recuperados.
- Total cópia: antes de uma réplica possam juntar-se o conjunto de configuração, tem de ser criado. Em alguns casos, é necessária uma cópia completa do Estado de fiável Gestor de estado a partir da réplica principal para serem aplicados a idle réplicas secundária. Gestor de estado fiáveis na réplica secundário utiliza **NotifyStateManagerChangedEventArgs** seja acionada um evento que contém o conjunto de Estados fiáveis que-adquirido a partir da réplica principal.
- Restaurar: Em cenários de recuperação de falhas, estado a réplica pode ser restaurado a partir de uma cópia de segurança através do **RestoreAsync**. Nestes casos, fiável Gestor de estado na réplica principal utiliza **NotifyStateManagerChangedEventArgs** seja acionada um evento que contém o conjunto de Estados fiáveis que este restaurado da cópia de segurança.

Para registar notificações da transação e/ou as notificações de Gestor de estado, tem de registar os eventos **TransactionChanged** ou **StateManagerChanged** fiável Gestor de estado. Um local para registar com estes processadores de eventos comuns é construtor do seu serviço de estado. Quando se registar no construtor, não perde qualquer notificação que é causada por uma alteração durante o tempo de vida de **IReliableStateManager**.

```C#
public MyService(StatefulServiceContext context)
    : base(MyService.EndpointName, context, CreateReliableStateManager(context))
{
    this.StateManager.TransactionChanged += this.OnTransactionChangedHandler;
    this.StateManager.StateManagerChanged += this.OnStateManagerChangedHandler;
}
```

Processador de eventos de **TransactionChanged** utiliza **NotifyTransactionChangedEventArgs** para fornecer detalhes sobre o evento. Contém a propriedade de ação (por exemplo, **NotifyTransactionChangedAction.Commit**) que especifica o tipo de alteração. Também contém a propriedade transaction que fornece uma referência para a transação que foi alterado.

>[AZURE.NOTE] Hoje em dia, **TransactionChanged** eventos são elevados apenas se a transação está empenhada em. A ação, em seguida, é igual ao **NotifyTransactionChangedAction.Commit**. Mas, no futuro, poderão ser elevados eventos para outros tipos de alterações de estado da transação. Recomendamos que verificar a ação e o evento de processamento apenas se trata de um que esperava.

Segue-se um processador de eventos de **TransactionChanged** de exemplo.

```C#
private void OnTransactionChangedHandler(object sender, NotifyTransactionChangedEventArgs e)
{
    if (e.Action == NotifyTransactionChangedAction.Commit)
    {
        this.lastCommitLsn = e.Transaction.CommitSequenceNumber;
        this.lastTransactionId = e.Transaction.TransactionId;

        this.lastCommittedTransactionList.Add(e.Transaction.TransactionId);
    }
}
```

Processador de eventos de **StateManagerChanged** utiliza **NotifyStateManagerChangedEventArgs** para fornecer detalhes sobre o evento.
**NotifyStateManagerChangedEventArgs** tem duas subclasses: **NotifyStateManagerRebuildEventArgs** e **NotifyStateManagerSingleEntityChangedEventArgs**.
Utilizar a propriedade de ação na **NotifyStateManagerChangedEventArgs** convertê **NotifyStateManagerChangedEventArgs** para a subclasse correta:

- **NotifyStateManagerChangedAction.Rebuild**: **NotifyStateManagerRebuildEventArgs**
- **NotifyStateManagerChangedAction.Add** e **NotifyStateManagerChangedAction.Remove**: **NotifyStateManagerSingleEntityChangedEventArgs**

Segue-se um processador de notificação de **StateManagerChanged** de exemplo.

```C#
public void OnStateManagerChangedHandler(object sender, NotifyStateManagerChangedEventArgs e)
{
    if (e.Action == NotifyStateManagerChangedAction.Rebuild)
    {
        this.ProcessStataManagerRebuildNotification(e);

        return;
    }

    this.ProcessStateManagerSingleEntityNotification(e);
}
```

## <a name="reliable-dictionary-notifications"></a>Notificações de dicionário fiáveis

Dicionário fiável fornece notificações para os seguintes eventos:

- Reconstruir: Chamado quando **ReliableDictionary** recuperou o seu estado de uma cópia de segurança ou recuperado ou copiado estado local.
- Limpar: Chamada quando o estado da **ReliableDictionary** foi desmarcado através do método **ClearAsync** .
- Adicione: Chamado quando um item foi adicionado ao **ReliableDictionary**.
- Actualização: Chamado quando um item numa **IReliableDictionary** foi atualizado.
- Remover: Chamado quando um item numa **IReliableDictionary** foi eliminado.

Para obter notificações de dicionário fiável, é necessário registar o processador de eventos **DictionaryChanged** no **IReliableDictionary**. Um local para registar com estes processadores de eventos comuns está na **ReliableStateManager.StateManagerChanged** adicionar notificação.
O registo quando **IReliableDictionary** é adicionado ao **IReliableStateManager** garante que não perde todas as notificações.

```C#
private void ProcessStateManagerSingleEntityNotification(NotifyStateManagerChangedEventArgs e)
{
    var operation = e as NotifyStateManagerSingleEntityChangedEventArgs;

    if (operation.Action == NotifyStateManagerChangedAction.Add)
    {
        if (operation.ReliableState is IReliableDictionary<TKey, TValue>)
        {
            var dictionary = (IReliableDictionary<TKey, TValue>)operation.ReliableState;
            dictionary.RebuildNotificationAsyncCallback = this.OnDictionaryRebuildNotificationHandlerAsync;
            dictionary.DictionaryChanged += this.OnDictionaryChangedHandler;
            }
        }
    }
}
```

>[AZURE.NOTE] **ProcessStateManagerSingleEntityNotification** é o método de exemplo chama **OnStateManagerChangedHandler** exemplo anterior.

O código precedente define a interface de **IReliableNotificationAsyncCallback** , juntamente com **DictionaryChanged**. Porque **NotifyDictionaryRebuildEventArgs** contém uma interface **IAsyncEnumerable** – que precisa de ser enumerado modo assíncrono – reconstruir notificações são chama através de **RebuildNotificationAsyncCallback** em vez de **OnDictionaryChangedHandler**.

```C#
public async Task OnDictionaryRebuildNotificationHandlerAsync(
    IReliableDictionary<TKey, TValue> origin,
    NotifyDictionaryRebuildEventArgs<TKey, TValue> rebuildNotification)
{
    this.secondaryIndex.Clear();

    var enumerator = e.State.GetAsyncEnumerator();
    while (await enumerator.MoveNextAsync(CancellationToken.None))
    {
        this.secondaryIndex.Add(enumerator.Current.Key, enumerator.Current.Value);
    }
}
```

>[AZURE.NOTE] Em primeiro lugar no código anterior, como parte do processamento de notificação de recompilação, está desmarcado o estado de agregado mantido. Uma vez que a coleção de fiável está a ser recriada com um novo Estado, todas as notificações anterior são irrelevantes.

Processador de eventos de **DictionaryChanged** utiliza **NotifyDictionaryChangedEventArgs** para fornecer detalhes sobre o evento.
**NotifyDictionaryChangedEventArgs** tem cinco subclasses. Utilize a propriedade de ação na **NotifyDictionaryChangedEventArgs** convertê **NotifyDictionaryChangedEventArgs** para a subclasse correta:

- **NotifyDictionaryChangedAction.Rebuild**: **NotifyDictionaryRebuildEventArgs**
- **NotifyDictionaryChangedAction.Clear**: **NotifyDictionaryClearEventArgs**
- **NotifyDictionaryChangedAction.Add** e **NotifyDictionaryChangedAction.Remove**: **NotifyDictionaryItemAddedEventArgs**
- **NotifyDictionaryChangedAction.Update**: **NotifyDictionaryItemUpdatedEventArgs**
- **NotifyDictionaryChangedAction.Remove**: **NotifyDictionaryItemRemovedEventArgs**

```C#
public void OnDictionaryChangedHandler(object sender, NotifyDictionaryChangedEventArgs<TKey, TValue> e)
{
    switch (e.Action)
    {
        case NotifyDictionaryChangedAction.Clear:
            var clearEvent = e as NotifyDictionaryClearEventArgs<TKey, TValue>;
            this.ProcessClearNotification(clearEvent);
            return;

        case NotifyDictionaryChangedAction.Add:
            var addEvent = e as NotifyDictionaryItemAddedEventArgs<TKey, TValue>;
            this.ProcessAddNotification(addEvent);
            return;

        case NotifyDictionaryChangedAction.Update:
            var updateEvent = e as NotifyDictionaryItemUpdatedEventArgs<TKey, TValue>;
            this.ProcessUpdateNotification(updateEvent);
            return;

        case NotifyDictionaryChangedAction.Remove:
            var deleteEvent = e as NotifyDictionaryItemRemovedEventArgs<TKey, TValue>;
            this.ProcessRemoveNotification(deleteEvent);
            return;

        default:
            break;
    }
}
```

## <a name="recommendations"></a>Recomendações

- *Efetue* concluídas mais rapidamente possível eventos de notificação.
- *Não faça* executar qualquer operações dispendiosas (por exemplo, operações e/s) como parte dos eventos síncronos.
- *Verificar o tipo de acção antes que o evento do processo.* Novos tipos de ação podem ser adicionados no futuro.

Eis algumas coisas a ter em conta:

- Notificações são chama como parte da execução de uma operação. Por exemplo, uma notificação de restaurar é desencadeada como o último passo de uma operação de restaurar. Um restauro não será concluída até que o evento de notificação é processado.
- Uma vez que são chama notificações como parte das operações de liquidação, clientes ver apenas as notificações de operações localmente consolidadas. E porque são garante operações apenas para ser localmente consolidada (por outras palavras, com sessão iniciada), poderão ou poderá não ser anuladas no futuro.
- No caminho Refazer, numa única notificação é desencadeada para cada operação aplicada. Isto significa que se transação T1 inclui Create(X), Delete(X) e Create(X), irá obter uma notificação para a criação de X, uma para a eliminação e outra para a criação novamente, por essa ordem.
- Para transações que contêm várias operações, operações são aplicadas pela ordem em que foram recebidos na réplica principal do utilizador.
- Como parte do processamento de progresso FALSO, algumas operações podem ser anuladas. Notificações são elevadas para essas operações de anular a reverter o estado da réplica a um ponto estável. Uma diferença importante das notificações de anular é que os eventos que tenham chaves duplicadas são agregados. Por exemplo, se está a ser revertida transação T1, verá uma única notificação para Delete(X).

## <a name="next-steps"></a>Próximos passos

- [Fiáveis coleções de sites](service-fabric-work-with-reliable-collections.md)
- [Guia de introdução do fiável serviços](service-fabric-reliable-services-quick-start.md)
- [Serviços fiáveis cópia de segurança e restaurar (recuperação de falhas)](service-fabric-reliable-services-backup-restore.md)
- [Referência para programadores para coleções de fiável](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
