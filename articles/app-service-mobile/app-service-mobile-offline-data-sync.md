<properties
    pageTitle="Sincronização de dados offline no Azure aplicações móveis | Microsoft Azure"
    description="Referência conceptual e descrição geral da funcionalidade de sincronização de dados offline para as aplicações móveis do Azure"
    documentationCenter="windows"
    authors="adrianhall"
    manager="dwrede"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="offline-data-sync-in-azure-mobile-apps"></a>Sincronização de dados offline no Azure aplicações móveis

## <a name="what-is-offline-data-sync"></a>O que é a sincronização de dados offline?

Sincronização de dados offline está um cliente e servidor funcionalidade SDK Azure Mobile das aplicações do que torna mais fácil para os programadores criar as aplicações que estão funcionais sem uma ligação de rede.

Quando a sua aplicação está em modo offline, os utilizadores ainda podem criar e modificar os dados, que serão guardados num arquivo local. Quando a aplicação estiver novamente online,-pode sincronizar alterações locais com o seu back-end da aplicação do Azure Mobile. A funcionalidade também inclui suporte para detetar conflitos quando o mesmo registo é alterado no cliente e o back-end. Em seguida, poderá ser resolvidos conflitos no servidor ou o cliente.

Sincronização offline tem um número de benefícios:

* Melhorar a capacidade de resposta da aplicação ao colocação em cache de dados do servidor localmente no dispositivo
* Criar aplicações robustas que permanecem útil quando existem problemas de rede
* Permitir que utilizadores finais criar e modificar dados mesmo quando não for possível aceder de rede, suportar cenários com pouca ou nenhuma conectividade
* Sincronizar dados através de vários dispositivos e detetar conflitos quando o mesmo registo é modificado por dois dispositivos
* Limitar a utilização da rede em redes com tráfego limitado ou de latência alta

Os seguintes tutoriais mostram como adicionar a sincronização offline para os seus clientes móveis utilizar aplicações do Azure Mobile:

* [Android: Ativar sincronização offline]
* [iOS: Ativar a sincronização offline]
* [Xamarin iOS: Ativar a sincronização offline]
* [Xamarin Android: Ativar sincronização offline]
* [Xamarin.Forms: Ativar a sincronização offline](app-service-mobile-xamarin-forms-get-started-offline-data.md)
* [Plataforma Windows universal: Ativar sincronização offline]

## <a name="what-is-a-sync-table"></a>O que é uma tabela de sincronização?

Para aceder ao ponto final de "/ tabelas", o cliente móvel do Azure SDK fornecer interfaces tais como `IMobileServiceTable` (.NET SDK do cliente) ou `MSTable` (iOS cliente). Estas APIs ligar diretamente para o aplicação do Azure Mobile back-end e falharão se o dispositivo de cliente não tiver uma ligação de rede.

Para suportar a utilização offline, a aplicação deverá em vez disso, utilize a *tabela de sincronização* APIs, tais como `IMobileServiceSyncTable` (.NET SDK do cliente) ou `MSSyncTable` (iOS cliente). As mesmas operações CRUD (criar, ler, atualizar, eliminar) funcionam contra a tabela de sincronização APIs, exceto agora vai ler a partir do ou escrever um *arquivo local*. Antes de qualquer operações de tabela de sincronização podem ser efetuadas, tem de ser inicializado o arquivo local.

## <a name="what-is-a-local-store"></a>O que é um arquivo local?

Um arquivo local é a camada de persistente de dados no dispositivo cliente. Os SDK do cliente de aplicações do Azure Mobile fornecem uma implementação do arquivo local predefinida. No Windows, Xamarin e Android, baseia-se SQLite; IOS, baseia-se nos dados Core.

Para utilizar a aplicação com base em SQLite no Windows Phone ou o Windows 8.1 de arquivo, tem de instalar uma extensão de SQLite. Para obter mais detalhes, consulte o artigo [Universal plataforma do Windows: Ativar a sincronização offline]. Android e iOS são fornecidos com uma versão do SQLite no sistema operativo dispositivo propriamente dito, para que não é necessário fazer referência a sua própria versão do SQLite.

Os programadores também podem implementar os seus próprios arquivo local. Por exemplo, se pretender armazenar dados num formato encriptado no cliente móvel, pode definir um arquivo local que utiliza SQLCipher para encriptação.

## <a name="what-is-a-sync-context"></a>O que é um contexto de sincronização?

Um *contexto de sincronização* está associado um objeto de cliente móvel (tais como `IMobileServiceClient` ou `MSClient`) e controla as alterações efetuadas com tabelas de sincronização. O contexto de sincronização mantém uma *fila de operação* que mantém uma lista ordenada de operações de CUD (criar, atualizar, eliminar) o que mais tarde serão enviadas para o servidor.

Um arquivo local está associado com o contexto de sincronização com o método de inicialização tal como `IMobileServicesSyncContext.InitializeAsync(localstore)` no [cliente .NET SDK].

## <a name="how-sync-works"></a>Offline como funciona a sincronização

Ao utilizar tabelas de sincronização, o código do cliente controla quando alterações locais serão sincronizadas com um aplicação do Azure Mobile do back-end. Nada é enviado para o back-end até que não haja uma chamada para alterações locais *push* . Do mesmo modo, o arquivo local é povoado com os novos dados apenas quando existe uma chamada para *solicitar* dados.

* **Push**: Push é uma operação o contexto de sincronização e envia todas as alterações CUD desde o último push. Tenha em atenção que não é possível enviar apenas numa tabela individual alterações, porque caso contrário operações podem ser enviadas de ordem. Push executa uma série de chamadas de resto para a sua aplicação do Azure Mobile do back-end, que por sua vez irá modificar a base de dados do servidor.

* **Separar**: solicitação é executada numa base por tabela e pode ser personalizada com uma consulta para recuperar apenas um subconjunto dos dados do servidor. Os SDK do cliente móvel do Azure, em seguida, inserir os dados resultantes o arquivo local.

* **Emite implícito**: se uma solicitação é executada em relação a uma tabela que tenha actualizações locais pendentes, a solicitação pela primeira vez executará um push no contexto de sincronização. Isto ajuda a minimizar conflitos entre as alterações que já estão em fila e novos dados a partir do servidor.

* **Sincronização utilizarão**: o primeiro parâmetro para a operação de solicitação é um *nome da consulta* que é utilizado apenas no cliente. Se utiliza o nome de uma consulta não nulas, o SDK do Azure móvel irá efetuar uma *sincronização utilizarão*.
  Sempre que uma operação de solicitação devolve um conjunto de resultados, o mais recente `updatedAt` carimbo de data/hora a partir do conjunto de resultados que está armazenado nas tabelas SDK sistema local. Operações de por solicitação subsequentes só irão obter registos após esse carimbo de data/hora.

  Para utilizar a sincronização elementar, o servidor tem de devolver significativo `updatedAt` os valores e também tem de suportar ordenar por este campo. No entanto, uma vez que o SDK adiciona as suas próprias ordenar no campo updatedAt, não é possível utilizar uma consulta de solicitação que tenha as suas próprias `$orderBy$` cláusula.

  O nome da consulta pode ser qualquer cadeia que escolher, mas tem de ser exclusivo para cada consulta lógica na sua aplicação.
  Caso contrário, operações solicitação diferente poderá substituir o carimbo de hora sincronização utilizarão mesmo e as suas consultas podem devolver resultados incorretos.

  Se a consulta tem um parâmetro, é uma forma de criar um nome exclusivo de consulta incorporar o valor do parâmetro.
  Por exemplo, se estiver a filtrar ID de utilizador, o nome da consulta pode ser da seguinte forma (no c#):

        await todoTable.PullAsync("todoItems" + userid,
            syncTable.Where(u => u.UserId == userid));

  Se pretende optar por terminar sincronização elementar, passar `null` como o ID da consulta. Neste caso, serão obtidos todos os registos em cada chamada para `PullAsync`, que é potencialmente ineficaz.

* **Purging**: pode limpar os conteúdos de utilizar o arquivo local `IMobileServiceSyncTable.PurgeAsync`.
  Poderá ser necessário se tiver dados obsoletos da base de dados do cliente, ou se pretender rejeitar todas as alterações pendentes.

  Limpar uma situada irá desmarque uma tabela a partir da loja local. Se existirem operações pendentes sincronização com a base de dados do servidor, a remoção irá gerar uma exceção, a menos que o parâmetro *Forçar limpar* é definido.

  Como um exemplo de dados obsoletos no cliente, imagine que no exemplo "lista a fazer", Device1 obtém apenas itens que não são concluídas. Em seguida, um todoitem "Comprar leite" está marcado como concluído no servidor por outro dispositivo. No entanto, Device1 continuará a ter o todoitem "Comprar leite" no arquivo local porque-é desligar apenas itens que não são marcadas como concluídas. Limpar uma situada irá limpar este item obsoleto.

## <a name="next-steps"></a>Próximos passos

* [iOS: Ativar a sincronização offline]
* [Xamarin iOS: Ativar a sincronização offline]
* [Xamarin Android: Ativar sincronização offline]
* [Plataforma Windows universal: Ativar sincronização offline]

<!-- Links -->
[Cliente .NET SDK]: app-service-mobile-dotnet-how-to-use-client-library.md
[Android: Ativar sincronização offline]: app-service-mobile-android-get-started-offline-data.md
[iOS: Ativar a sincronização offline]: app-service-mobile-ios-get-started-offline-data.md
[Xamarin iOS: Ativar a sincronização offline]: app-service-mobile-xamarin-ios-get-started-offline-data.md
[Xamarin Android: Ativar sincronização offline]: app-service-mobile-xamarin-ios-get-started-offline-data.md
[Plataforma Windows universal: Ativar sincronização offline]: app-service-mobile-windows-store-dotnet-get-started-offline-data.md
