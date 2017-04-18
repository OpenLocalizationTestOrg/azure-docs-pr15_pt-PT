<properties
    pageTitle="Gerir simultaneidade no armazenamento do Microsoft Azure"
    description="Como gerir simultaneidade para os serviços BLOBs, fila de espera, tabela e ficheiro"
    services="storage"
    documentationCenter=""
    authors="jasonnewyork"
    manager="tadb"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="jahogg"/>

# <a name="managing-concurrency-in-microsoft-azure-storage"></a>Gerir simultaneidade no armazenamento do Microsoft Azure

## <a name="overview"></a>Descrição geral

Aplicações de baseados na Internet modernas normalmente têm vários utilizadores visualizar e atualizar os dados em simultâneo. Isto requer que os programadores de aplicações pensar cuidadosamente sobre como fornecer uma experiência previsível aos seus utilizadores finais, especialmente para cenários onde vários utilizadores podem atualizar os mesmos dados. Existem três estratégias de simultaneidade de dados principal, normalmente, irão considerá-os programadores:  


1.  Simultaneidade optimista – última uma aplicação de efetuar uma atualização como parte da sua atualização verificará se os dados foi alterado desde a aplicação de leitura esses dados. Por exemplo, se dois utilizadores a visualizar uma página wiki efectuam uma atualização para a mesma página, em seguida, a plataforma wiki tem de garantir que a segunda actualização não substituir a primeira actualização – e que ambos os utilizadores compreendem se a sua atualização foi efetuada com êxito ou não. Esta estratégia com mais frequência é utilizada nas aplicações web.
2.  Simultaneidade pessimista – uma aplicação está à procura para efetuar uma atualização demorar um cadeado num objeto impedir que outros utilizadores atualizar os dados até que o bloqueio é disponibilizado. Por exemplo, num cenário de replicação de dados principal/subordinado onde apenas o modelo global de irá efetuar atualizações o modelo global de será, normalmente, mantenha a tecla um bloqueio exclusivo por um determinado período de tempo na Certifique-se de que ninguém pode atualizar os dados.
3.  Último a escrever ganha – uma abordagem de que permite que quaisquer operações de atualização continuar sem verificar se qualquer outra aplicação atualizou os dados desde a aplicação pela primeira vez ler os dados. Este estratégia (ou falta de uma estratégia de formal) é geralmente utilizada onde os dados estiver dividido em partições forma a que não existe nenhuma probabilidade que vários utilizadores poderão aceder os mesmos dados. Pode também ser útil onde sequências de dados curto estão a ser processadas.  

Este artigo fornece uma descrição geral de como a plataforma de armazenamento do Windows Azure simplifica o desenvolvimento ao fornecer suporte primeira classe para todos os três destas estratégias de simultaneidade.  

## <a name="azure-storage--simplifies-cloud-development"></a>Armazenamento Azure – simplifica o desenvolvimento de nuvem
O serviço de armazenamento Azure suporta todos os estratégias de três, apesar de ser distintivo na sua capacidade para fornecer suporte completo para simultaneidade optimista e pessimista porque foi concebido para Aproveite a um modelo de consistência forte que garante que, quando o serviço de armazenamento é inserir um dados consolidada ou operação de atualização todos os acede ainda mais para que os dados irão ver a atualização mais recente. Plataformas de armazenamento que utilizam um modelo de consistência eventual ter um tempo de desfasamento entre quando um processo de escrita é executado por um utilizador e quando os dados atualizados podem ver vistos por outros utilizadores obrigações, por conseguinte, desenvolvimento de aplicações de cliente para impedir que inconsistências a afetar os utilizadores finais.  

Para além de selecionar uma estratégia de simultaneidade adequado os programadores devem também ter em mente de como uma plataforma de armazenamento isola alterações – particularmente alterações ao mesmo objeto em transações. O serviço de armazenamento Azure utiliza isolamento instantâneo para permitir operações de leitura para ocorrer em simultâneo com operações de escrita dentro de uma única partição. Ao contrário de outros níveis de isolamento isolamento instantâneo garante que lê todos os ver um instantâneo dos dados consistente mesmo ocorrem atualizações – essencialmente, devolvendo os últimos valores consolidados enquanto uma atualização transação está a ser processada.  

## <a name="managing-concurrency-in-blob-storage"></a>A gestão de simultaneidade do armazenamento de BLOBs
Pode optar por utilizar um dos modelos de simultaneidade optimista ou pessimista para gerir o acesso a blobs e contentores no serviço blob. Se não especificar explicitamente um wins escritas última estratégia é a predefinição.  

### <a name="optimistic-concurrency-for-blobs-and-containers"></a>Simultaneidade optimista para blobs e contentores  

O serviço de armazenamento atribui um identificador para cada objeto armazenado. Este identificador é atualizado, sempre que é efetuada uma operação de atualização num objeto. É devolvido o identificador para o cliente como parte de uma resposta HTTP GET, utilizando o cabeçalho (tag entidade) ETag que é definido no âmbito do protocolo HTTP. Um utilizador efetuar uma atualização no como um objeto pode enviar o ETag original juntamente com um cabeçalho condicional para se certificar de que uma atualização apenas ocorrerá se numa determinada condição que tenha sido respeitada – neste caso a condição é um cabeçalho "Se CORRESP" que exige o serviço de armazenamento garantir que o valor de ETag especificado no pedido de atualização é igual que armazenados no serviço de armazenamento.  

O contorno deste processo é da seguinte forma:  

1.  Obter um blob a partir do serviço de armazenamento, a resposta inclui um valor de um cabeçalho ETag HTTP que identifica a versão atual do objeto no serviço de armazenamento.
2.  Quando atualiza o blob, inclua o valor de ETag que recebeu no passo 1 no cabeçalho da condicional de **Correspondência de se** o pedido de que enviar para o serviço.
3.  O serviço compara o valor de ETag no pedido de com o valor de ETag atual do blob.
4.  Se o valor de ETag atual do blob ETag no cabeçalho condicional **Se CORRESP** no pedido de uma versão diferente, o serviço devolve um erro 412 para o cliente. Isto indica ao cliente que outro processo tiver atualizado o blob uma vez que o cliente obtidos-lo.
5.  Se o valor de ETag atual da BLOBs for a mesma versão que ETag no cabeçalho condicional **Se CORRESP** no pedido, o serviço executa a operação pedida e atualiza o valor de ETag atual da BLOBs para mostrar que tenha criado uma nova versão.  

O seguinte c# fragmento (utilizando a biblioteca de armazenamento do cliente 4.2.0) mostra uma simples exemplo de como construir uma **Correspondência se AccessCondition** baseado no valor ETag que é acedido a partir das propriedades de um blob que estava anteriormente obtidas ou inserida. Em seguida, utiliza o objeto **AccessCondition** quando-atualizar o blob: o objeto **AccessCondition** adiciona o cabeçalho **Se CORRESP** ao pedido de. Se outro processo tiver atualizado o blob, o serviço de BLOBs devolve uma mensagem de estado de HTTP 412 (condição prévia falhou). Pode transferir a amostra completa aqui: [Gerir simultaneidade utilizando o armazenamento do Windows Azure](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).  

    // Retrieve the ETag from the newly created blob
    // Etag is already populated as UploadText should cause a PUT Blob call
    // to storage blob service which returns the etag in response.
    string orignalETag = blockBlob.Properties.ETag;

    // This code simulates an update by a third party.
    string helloText = "Blob updated by a third party.";

    // No etag, provided so orignal blob is overwritten (thus generating a new etag)
    blockBlob.UploadText(helloText);
    Console.WriteLine("Blob updated. Updated ETag = {0}",
    blockBlob.Properties.ETag);

    // Now try to update the blob using the orignal ETag provided when the blob was created
    try
    {
        Console.WriteLine("Trying to update blob using orignal etag to generate if-match access condition");
        blockBlob.UploadText(helloText,accessCondition:
        AccessCondition.GenerateIfMatchCondition(orignalETag));
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        {
            Console.WriteLine("Precondition failure as expected. Blob's orignal etag no longer matches");
            // TODO: client can decide on how it wants to handle the 3rd party updated content.
        }
        else
            throw;
    }  

O serviço de armazenamento também inclui suporte para cabeçalhos condicional adicionais tal como **Se-modificados-desde**, **If-inalterados-desde** e **Se nenhum-correspondência** , bem como combinações das mesmas. Para obter mais informações, consulte [Especificar condicional cabeçalhos para operações de serviço de BLOBs](http://msdn.microsoft.com/library/azure/dd179371.aspx) no MSDN.  

A tabela seguinte resume as operações de contentor que aceitar cabeçalhos condicional como **Se CORRESP** no pedido de e que devolvem um valor de ETag na resposta.  

| Operação                | Devolve o valor ETag contentor | Aceita cabeçalhos condicional |
|:-------------------------|:-----------------------------|:----------------------------|
| Criar o contentor         | Sim                          | N                          |
| Obter as propriedades de contentor | Sim                          | N                          |
| Obter o contentor metadados   | Sim                          | N                          |
| Definir contentor metadados   | Sim                          | Sim                         |
| Obtenha contentor ACL        | Sim                          | N                          |
| Definir o contentor ACL        | Sim                          | Sim (*)                     |
| Eliminar contentor         | N                           | Sim                         |
| Contentor de locação financeira          | Sim                          | Sim                         |
| Lista Blobs               | N                           | N                          |

(*) As permissões definidas pela SetContainerACL são armazenadas em cache e atualizações para estas permissões demoram 30 segundos a serem propagadas durante as quais as atualizações não estão garante que sejam consistentes.  

A tabela seguinte resume as operações de BLOBs que aceitar cabeçalhos condicional como **Se CORRESP** no pedido de e que devolvem um valor de ETag na resposta.

| Operação           | Devolve o valor ETag | Aceita cabeçalhos condicional           |
|:--------------------|:-------------------|:--------------------------------------|
| Colocar Blob            | Sim                | Sim                                   |
| Obter Blob            | Sim                | Sim                                   |
| Obter as propriedades de BLOBs | Sim                | Sim                                   |
| Definir as propriedades de BLOBs | Sim                | Sim                                   |
| Obter Blob metadados   | Sim                | Sim                                   |
| Definir BLOBs metadados   | Sim                | Sim                                   |
| Locação financeira BLOBs (*)      | Sim                | Sim                                   |
| Instantâneo Blob       | Sim                | Sim                                   |
| Copiar Blob           | Sim                | Sim (para blob de origem e destino) |
| Interrupção Blob cópia     | N                 | N                                    |
| Eliminar Blob         | N                 | Sim                                   |
| Colocar o bloco           | N                 | N                                    |
| Colocar a lista de blocos      | Sim                | Sim                                   |
| Obter a lista de blocos      | Sim                | N                                    |
| Colocar a página            | Sim                | Sim                                   |
| Obter intervalos de página     | Sim                | Sim                                   |


(*) Locação financeira Blob não irá alterar ETag num blob.  

### <a name="pessimistic-concurrency-for-blobs"></a>Simultaneidade pessimista para blobs
Para bloquear um blob para utilização exclusiva, pode adquirir uma [locação financeira](http://msdn.microsoft.com/library/azure/ee691972.aspx) no mesmo. Quando adquire uma locação financeira, tem de especificar quanto tempo terá da locação: este pode ser para entre 15 a 60 segundos ou infinito quais os montantes para um bloqueio exclusivo. Pode renovar uma locação finita para expandi-lo e, pode liberte qualquer arrendar quando tiver terminado com o mesmo. O serviço de BLOBs liberta automaticamente concessões finitas quando expiram.  

Concessões permitir estratégias de sincronização diferente serão suportadas, incluindo a escrita / partilhado escrita exclusiva, leia / exclusiva ler e partilhadas escrita / leitura exclusiva. Sempre que exista uma locação o serviço de armazenamento impõe exclusiva escritas (colocar, definir e eliminar operações) no entanto, garantindo que exclusividade para operações de leitura requer o programador para se certificar de que todas as aplicações de cliente utilizam um ID de locação financeira e que apenas um cliente uma vez tem um ID arrendar válida. Operações que não incluam um resultado de ID de locação financeira lê partilhada de leitura.  

O fragmento de c# seguinte mostra um exemplo de adquirir uma locação exclusivo para 30 segundos num blob, atualizar o conteúdo do blob e, em seguida, soltar locação. Se já existir uma locação válida na BLOBs quando tenta para adquirir uma nova locação, o serviço de blob devolve um resultado de estado "HTTP (409) conflito". O fragmento de abaixo utiliza um objeto de **AccessCondition** para incorporar as informações de locação financeira, quando faz com que um pedido para atualizar o blob no serviço de armazenamento.  Pode transferir a amostra completa aqui: [Gerir simultaneidade utilizando o armazenamento do Windows Azure](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

    // Acquire lease for 15 seconds
    string lease = blockBlob.AcquireLease(TimeSpan.FromSeconds(15), null);
    Console.WriteLine("Blob lease acquired. Lease = {0}", lease);

    // Update blob using lease. This operation will succeed
    const string helloText = "Blob updated";
    var accessCondition = AccessCondition.GenerateLeaseCondition(lease);
    blockBlob.UploadText(helloText, accessCondition: accessCondition);
    Console.WriteLine("Blob updated using an exclusive lease");

    //Simulate third party update to blob without lease
    try
    {
        // Below operation will fail as no valid lease provided
        Console.WriteLine("Trying to update blob without valid lease");
        blockBlob.UploadText("Update without lease, will fail");
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
            Console.WriteLine("Precondition failure as expected. Blob's lease does not match");
        else
            throw;
    }  

Se tentar uma operação de escrita um BLOBs dedicada sem passar o ID de locação financeira, o pedido falhar com um erro 412. Tenha em atenção que se locação expira antes de telefonar para o método de **UploadText** , mas ainda ser efetuada com o ID de locação financeira, o pedido também irá falhar com um erro **412** . Para mais informações sobre a gestão de ids de locação financeira e horas de termo locação financeira, consulte a documentação de resto [Arrendar Blob](http://msdn.microsoft.com/library/azure/ee691972.aspx) .  

As seguintes operações de BLOBs podem utilizar as concessões para gerir simultaneidade pessimista:  


-   Colocar Blob
-   Obter Blob
-   Obter as propriedades BLOBs
-   Definir as propriedades de BLOBs
-   Obter Blob metadados
-   Definir Blob metadados
-   Eliminar Blob
-   Colocar o bloco
-   Colocar a lista de blocos
-   Obter a lista de blocos
-   Colocar a página
-   Obter intervalos de página
-   Instantâneo Blob - ID arrendar opcional se existir uma locação
-   Copiar Blob - arrendar ID obrigatório se uma locação existe no blob de destino
-   Cancelar cópia Blob - locação financeira ID obrigatório se existir uma locação infinita no blob de destino
-   Locação financeira Blob  

### <a name="pessimistic-concurrency-for-containers"></a>Simultaneidade pessimista de membros em contentores
Concessões em contentores de ativar as mesmo estratégias de sincronização serão suportadas como no blobs (exclusiva escrever / partilhado escrita exclusiva, leia / exclusiva ler e partilhadas escrita / leitura exclusiva) no entanto ao contrário blobs o serviço de armazenamento apenas impõe exclusividade de operações de eliminação. Para eliminar um contentor com uma locação ativo, um cliente tem de incluir o ID do arrendar ativos com o pedido de eliminação. Todas as outras operações de contentor teve êxito num contentor dedicado sem incluindo o ID de locação financeira caso em que são partilhados operações. Se for necessária a exclusividade de atualização (local ou conjunto) ou operações de leitura, em seguida, os programadores devem Certifique-se que todos os clientes de utilizam um ID de locação financeira e que apenas um cliente de cada vez tem um ID arrendar válida.  

As seguintes operações de contentor podem utilizar as concessões para gerir simultaneidade pessimista:  

-   Eliminar contentor
-   Obter as propriedades de contentor
-   Obter o contentor metadados
-   Definir contentor metadados
-   Obtenha contentor ACL
-   Definir o contentor ACL
-   Contentor de locação financeira  

Para obter mais informações, consulte:  

- [Especificar os cabeçalhos condicional para operações de serviço BLOBs](http://msdn.microsoft.com/library/azure/dd179371.aspx)
- [Contentor de locação financeira](http://msdn.microsoft.com/library/azure/jj159103.aspx)
- [Locação financeira Blob](http://msdn.microsoft.com/library/azure/ee691972.aspx)

## <a name="managing-concurrency-in-the-table-service"></a>Gerir simultaneidade no serviço de tabela
O serviço de tabela utiliza optimista simultaneidade verifica como o comportamento predefinido quando está a trabalhar com entidades, ao contrário o serviço de BLOBs onde tem de escolher explicitamente para executar verificações de simultaneidade optimista. Diferença entre os serviços de tabela e blob é que apenas pode gerir o comportamento de simultaneidade do entidades Considerando que com o serviço de BLOBs pode gerir simultaneidade de contentores e blobs.  

Para utilizar simultaneidade optimista e para verificar se o outro processo modificado uma entidade desde que obtidos a partir do serviço de armazenamento de tabela, pode utilizar o valor de ETag que recebe quando o serviço de tabela devolve uma entidade. O contorno deste processo é da seguinte forma:  

1.  Obter uma entidade a partir do serviço de armazenamento de tabela, a resposta inclui um valor de ETag que identifica o identificador atual associado a essa entidade no serviço de armazenamento.
2.  Quando atualiza a entidade, inclua o valor de ETag que recebeu no passo 1 no cabeçalho do pedido que enviar para o serviço de **Correspondência se** obrigatório.
3.  O serviço compara o valor de ETag no pedido de com o valor de ETag atual da entidade.
4.  Se o valor de ETag atual da entidade for diferente do AnonymousUserName ETag no cabeçalho no pedido de **Correspondência se** obrigatória, o serviço devolve um erro de 412 para o cliente. Isto indica ao cliente que outro processo tem atualizado a entidade de uma vez que o cliente obtidos-lo.
5.  Se o valor de ETag atual da entidade é igual a ETag no cabeçalho obrigatório **Se CORRESP** no pedido de ou o cabeçalho de **Correspondência se** contém caráter universal (*), o serviço executa a operação pedida e atualiza o valor de ETag atual da entidade para mostrar que foi atualizado.  

Tenha em atenção que, ao contrário de serviço blob, o serviço de tabela requer o cliente para incluir um cabeçalho de **Correspondência se** pedidos de actualização. No entanto, é possível forçar uma incondicional atualizar (última sénior wins estratégia) e ignorar simultaneidade verifica se o cliente define o cabeçalho **Se CORRESP** como caráter universal (*) no pedido de.  

O fragmento de c# seguinte mostra uma entidade de cliente que quer anteriormente foi criada ou obtidos ter o respetivo endereço de e-mail atualizado. Inicial inserir ou obter operação armazena o valor de ETag no objeto cliente e porque o exemplo utiliza a mesma instância do objeto quando-executa a operação de substituir, envia-lhe automaticamente o valor ETag novamente para o serviço de tabela, ativar o serviço de verificar a existência de violações de simultaneidade. Se outro processo atualizou a entidade de armazenamento de tabela, o serviço devolve uma mensagem de estado de HTTP 412 (condição prévia falhou).  Pode transferir amostra total aqui: [Gerir simultaneidade utilizando o armazenamento do Windows Azure](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

    try
    {
        customer.Email = "updatedEmail@contoso.org";
        TableOperation replaceCustomer = TableOperation.Replace(customer);
        customerTable.Execute(replaceCustomer);
        Console.WriteLine("Replace operation succeeded.");
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == 412)
            Console.WriteLine("Optimistic concurrency violation – entity has changed since it was retrieved.");
        else
            throw;
    }  

Para explicitamente desativar a verificação de simultaneidade, deverá definir a propriedade **ETag** do objeto **empregado** para "*" antes de executar a operação de substituição.  

    customer.ETag = "*";  

A seguinte tabela resume como as operações de entidade tabela utilizam ETag valores:

| Operação                | Devolve o valor ETag | Requer o cabeçalho do pedido se CORRESP |
|:-------------------------|:-------------------|:---------------------------------|
| Entidades de consulta           | Sim                | N                               |
| Inserir entidade            | Sim                | N                               |
| Actualizar uma entidade            | Sim                | Sim                              |
| Intercalar entidade             | Sim                | Sim                              |
| Eliminar uma entidade            | N                 | Sim                              |
| Inserir ou substituir entidade | Sim                | N                               |
| Inserir ou entidade de impressão em série   | Sim                | N                               |

Tenha em atenção que as operações de **Inserir ou substituir entidade** e **Inserir ou entidade de impressão em série** efetue *não* executar verificações de simultaneidade porque não enviarem a um valor de ETag para o serviço de tabela.  

Em geral os programadores utilizando tabelas devem confiar simultaneidade optimista quando desenvolver aplicações dimensionáveis. Se for necessário o bloqueio pessimista, os programadores de uma abordagem podem tirar quando aceder a tabelas é atribuir uma BLOBs designada para cada tabela e tente manter uma locação a BLOBs antes de a funcionar com a tabela. Esta abordagem requer a aplicação para assegurar a todos os caminhos de acesso a dados obtenham a locação antes de a funcionar com a tabela. Deverá também notar que a hora de mínimos da locação é 15 segundos solicitar uma análise cuidada para escalabilidade.  

Para obter mais informações, consulte:  

- [Operações em entidades](http://msdn.microsoft.com/library/azure/dd179375.aspx)  

## <a name="managing-concurrency-in-the-queue-service"></a>Gerir simultaneidade no serviço de fila de espera
Um cenário que no qual simultaneidade é um problema no serviço de colocação em fila é onde vários clientes estiver a obter as mensagens a partir de uma fila. Quando uma mensagem é obtida a partir de fila de espera, a resposta inclui a mensagem e um valor de recibo pop, que é necessário para eliminar a mensagem. A mensagem não é eliminada automaticamente a partir de fila de espera, mas depois de terem sido obtido, não é visível para os outros clientes para o intervalo de tempo especificado pelo parâmetro visibilitytimeout. O cliente de que obtém a mensagem é esperado para eliminar a mensagem depois foi processada e antes da hora especificada pela TimeNextVisible elemento da resposta, que é calculado baseado no valor do parâmetro visibilitytimeout. O valor de visibilitytimeout é adicionado à hora em que a mensagem é obtida para determinar o valor de TimeNextVisible.  

O serviço de fila não existe suporte para simultaneidade optimista ou pessimista e para que esta razão clientes processamento mensagens obtidas a partir de uma fila devem Certifique-se as mensagens são processadas de forma idempotent. Uma estratégia de wins sénior última é utilizada para operações de atualização, tais como SetQueueServiceProperties, SetQueueMetaData, SetQueueACL e UpdateMessage.  

Para obter mais informações, consulte:  

- [API REST do serviço de fila de espera](http://msdn.microsoft.com/library/azure/dd179363.aspx)
- [Recebe as mensagens](http://msdn.microsoft.com/library/azure/dd179474.aspx)  

## <a name="managing-concurrency-in-the-file-service"></a>Gerir simultaneidade no serviço de ficheiro
O serviço de ficheiros pode ser acedido através de dois protocolo diferentes pontos finais – SMB e coloque o PONTEIRO. O serviço do resto não existe suporte para o bloqueio optimista ou bloqueio pessimista e todas as atualizações vai ter de seguir uma estratégia de wins sénior última. Clientes SMB montagem partilhas de ficheiros podem tirar partido mecanismos de bloqueio de sistema de ficheiros para gerir o acesso aos ficheiros partilhados – incluindo a capacidade de executar o bloqueio pessimista. Quando um cliente SMB abre um ficheiro, especifica o acesso ao ficheiro e a partilha modo. Definir uma opção de acesso aos ficheiros de "Escrever" ou "Leitura/escrita" juntamente com um modo de partilha de ficheiros de "Nenhum" irá resultar no ficheiro a ser bloqueado por um cliente SMB até que o ficheiro está fechado. Se está tentada operação de resto num ficheiro onde um cliente SMB tem um ficheiro bloqueado o serviço do resto irá devolver o código de estado 409 (conflito) com código de erro SharingViolation.  

Quando um cliente SMB abre um ficheiro para eliminar, marca o ficheiro como pendentes eliminar até todas as outras cliente SMB identificadores abertos nesse ficheiro estarem fechados. Enquanto um ficheiro está marcado como com eliminação pendente, qualquer operação resto sobre esse ficheiro irá devolver o código de estado 409 (conflito) com código de erro SMBDeletePending. Código de estado 404 (não encontrado) não é devolvido uma vez que é possível para o cliente SMB remover o sinalizador de eliminação pendente antes de fechar o ficheiro. Por outras palavras, código de estado 404 (não encontrado) só é esperado quando o ficheiro foi removido. Tenha em atenção que enquanto está um SMB pendentes estado de eliminar um ficheiro, não será incluída nos resultados da lista de ficheiros. Tenha também em atenção que as operações de resto eliminar ficheiro e o resto da eliminação de directório estão consolidadas atomicamente e não resultar em pendentes eliminar estado.  

Para obter mais informações, consulte:  

- [Gestão de ficheiro bloqueios](http://msdn.microsoft.com/library/azure/dn194265.aspx)  

## <a name="summary-and-next-steps"></a>Resumo e os passos seguintes
O serviço de armazenamento do Windows Azure foi concebido para satisfazer as necessidades das aplicações do onlinehttps mais complexas sem forçar os programadores comprometer ou rethink pressupostos estrutura chave como consistência simultaneidade e os dados que entrem para tomar para concedido.  

Para a aplicação de exemplo completos referenciada neste blogue:  

- [Gerir simultaneidade utilizando o armazenamento do Windows Azure - aplicação de exemplo](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)  

Para obter mais informações sobre consulte de armazenamento do Windows Azure:  

- [Página de base de armazenamento do Microsoft Azure](https://azure.microsoft.com/services/storage/)
- [Introdução ao Azure armazenamento](storage-introduction.md)
- Introdução para [Blob](storage-dotnet-how-to-use-blobs.md), [tabela](storage-dotnet-how-to-use-tables.md), [filas](storage-dotnet-how-to-use-queues.md)e [ficheiros](storage-dotnet-how-to-use-files.md) de armazenamento
- Arquitetura de armazenamento – [armazenamento Azure: um serviço de armazenamento na nuvem altamente disponível com consistência forte](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
