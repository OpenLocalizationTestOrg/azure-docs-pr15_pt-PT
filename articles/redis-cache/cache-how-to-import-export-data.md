<properties 
    pageTitle="Importar e exportar os dados na Cache de Redis Azure | Microsoft Azure" 
    description="Saiba como importar e exportar dados de armazenamento de Blobs do seu instâncias de Cache de Redis Azure premium e para" 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/15/2016" 
    ms.author="sdanie"/>

# <a name="import-and-export-data-in-azure-redis-cache"></a>Importar e exportar os dados na Cache Redis do Azure

Importar/exportar é uma operação de gestão de dados do Azure Redis Cache, que permite-lhe importar dados para o Azure Redis Cache ou exportar dados a partir do Azure Redis Cache por importar e exportar um instantâneo de base de dados de Cache Redis (RDB) a partir de uma cache premium para um blob de página numa conta de armazenamento do Azure. Importar/exportar permite-lhe migrar entre instâncias Azure Redis Cache diferentes ou preencher a cache com dados antes do utilizar.

Este artigo fornece um guia para importação e exportação de dados com a Cache de Redis Azure e as respostas às perguntas mais frequentes.

>[AZURE.IMPORTANT] Importar/exportar está na pré-visualização e só está disponível para caches [camada premium](cache-premium-tier-intro.md) .

## <a name="import"></a>Importar

Importar pode ser utilizado para trazer Redis compatíveis RDB (s) de qualquer servidor Redis a ser executada em qualquer na nuvem ou ambiente, incluindo Redis em execução no Linux, Windows ou qualquer fornecedor de nuvem, como Amazon Web Services e outras pessoas. A importação de dados é uma forma fácil de criar uma cache com dados preenchidos. Durante o processo de importação, Azure Redis Cache carrega os ficheiros RDB a partir do armazenamento Azure na memória e, em seguida, insere as teclas a cache.

>[AZURE.NOTE] Antes de iniciar a operação de importação, certifique-se de que o seu ficheiro de base de dados Redis (RDB) ou ficheiros são carregados no blobs de página no Azure armazenamento, na mesma subscrição como a instância do Azure Redis Cache e região. Para mais informações, consulte o artigo [Introdução ao armazenamento de Blobs do Azure](../storage/storage-dotnet-how-to-use-blobs.md). Se exportado o ficheiro RDB através da funcionalidade [Azure Redis Cache de exportação](#export) , o seu ficheiro RDB já armazenado na blob página e está pronto para importar.

1. Para importar blobs de cache exportado um ou mais, [Procurar para a cache](cache-configure.md#configure-redis-cache-settings) no portal do Azure e clique em **Importar dados** a partir de pá **Definições** da sua instância de cache.

    ![Importar dados][cache-import-data]

2. Clique em **Escolher Blob(s)** e selecione a conta de armazenamento que contém os dados para importar.

    ![Selecione a conta de armazenamento][cache-import-choose-storage-account]

3. Clique no contentor que contém os dados para importar.

    ![Escolher contentor][cache-import-choose-container]

4. Selecione um ou mais blobs para importar, clicando na área à esquerda do nome do blob e, em seguida, clique em **Selecionar**.

    ![Selecione blobs][cache-import-choose-blobs]

5. Clique em **Importar** para iniciar o processo de importação.

    >[AZURE.IMPORTANT] A cache não está acessível por clientes de cache durante o processo de importação e quaisquer dados existentes na cache são eliminados.

    ![Importar][cache-import-blobs]

    Pode monitorizar o progresso da operação de importação, seguindo as notificações a partir do portal Azure ou ao visualizar os eventos da [auditoria iniciar sessão](cache-configure.md#support-amp-troubleshooting-settings).

    ![Importar o progresso][cache-import-data-import-complete] 


## <a name="export"></a>Exportar

Exportar permite-lhe exportar os dados armazenados na Cache Redis Azure para Redis compatíveis RDB ficheiros específicos. Pode utilizar esta funcionalidade para mover os dados a partir de uma instância do Azure Redis Cache para outra ou para outro servidor Redis. Durante o processo de exportação, é criado um ficheiro temporário na VM que aloja a instância de servidor do Azure Redis Cache e o ficheiro é carregado para a conta de armazenamento designado. Quando a operação de exportação é concluída com um de um Estado de sucesso ou falha, o ficheiro temporário é eliminado.

1. Para exportar o conteúdo atual da cache de armazenamento, [Navegue para a cache](cache-configure.md#configure-redis-cache-settings) no portal do Azure e clique em **Exportar dados** de pá as **Definições** da sua instância de cache.

    ![Selecione o contentor de armazenamento][cache-export-data-choose-storage-container]

2. Clique em **Escolher o contentor de armazenamento** e selecione a conta de armazenamento pretendida. A conta de armazenamento tem de ser na mesma subscrição e região como a cache.

    >[AZURE.IMPORTANT] Importar/exportar funciona com blobs de página, que são suportadas pelo clássica e contas de armazenamento de processador, mas, neste momento, não são suportadas pelo [Blob contas de armazenamento](../storage/storage-blob-storage-tiers.md#blob-storage-accounts) .

    ![Conta de armazenamento][cache-export-data-choose-account]

3. Escolha o contentor blob pretendido e clique em **Selecionar**. Para utilizar o novo um contentor, clique em **Adicionar contentor** para adicioná-la pela primeira vez e, em seguida, selecione-a partir da lista.

    ![Selecione o contentor de armazenamento][cache-export-data-container]

4. Escreva um **prefixo de Blobs do nome** e clique em **Exportar** para iniciar o processo de exportação. O prefixo do nome blob é utilizado para os nomes dos ficheiros gerados por esta operação de exportação do prefixo.

    ![Exportar][cache-export-data]

    Pode monitorizar o progresso da operação de exportação, seguindo as notificações a partir do portal Azure ou ao visualizar os eventos da [auditoria iniciar sessão](cache-configure.md#support-amp-troubleshooting-settings).

    ![][cache-export-data-export-complete]

    Caches permanecem disponíveis para utilização durante o processo de exportação.


## <a name="importexport-faq"></a>Importar/exportar perguntas mais frequentes

Esta secção contém as perguntas mais frequentes sobre a funcionalidade de importar/exportar.

-   [O que preços camadas, podem utilizar Importar/exportar?](#what-pricing-tiers-can-use-importexport)
-   [Pode importar dados a partir de qualquer servidor Redis?](#can-i-import-data-from-any-redis-server)
-   [Meu cache estará disponível durante uma operação de importação/exportação?](#will-my-cache-be-available-during-an-importexport-operation)
-   [Pode utilizar Importar/exportar com Redis cluster?](#can-i-use-importexport-with-redis-cluster)
-   [Como funciona importar/exportar com uma definição de bases de dados personalizado?](#how-does-importexport-work-with-a-custom-databases-setting)
-   [Importar/exportar é diferente do Redis persistente?](#how-is-importexport-different-from-redis-persistence)
-   [Posso automatizar a utilizar o PowerShell, clip ou outros clientes de gestão de importar/exportar?](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
-   [Recebi um erro de limite de tempo durante a operação meu importar/exportar. O que significa?](#i-received-a-timeout-error-during-my-importexport-operation.-what-does-it-mean)
-   [Recebi um erro ao exportar os meus dados ao armazenamento de Blobs do Azure. O que aconteceu?](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage.-what-happened)


### <a name="what-pricing-tiers-can-use-importexport"></a>O que preços camadas, podem utilizar Importar/exportar?

Importar/exportar só está disponível no premium preços de camadas.

### <a name="can-i-import-data-from-any-redis-server"></a>Pode importar dados a partir de qualquer servidor Redis?

Sim, para além de importar os dados exportados de instâncias de Cache Redis do Azure, pode importar ficheiros RDB a partir de qualquer servidor Redis a ser executada em qualquer na nuvem ou ambiente, como Linux, Windows, ou na nuvem fornecedores como Amazon Web Services. Para executar esta tarefa, carregar o ficheiro RDB do servidor Redis pretendido para um blob de página numa conta de armazenamento do Azure e, em seguida, importe-o para a instância do Azure Redis Cache premium. Por exemplo, poderá querer exportar os dados da sua cache de produção e importe-o para uma cache utilizada como parte de um ambiente de teste para testar ou migração. 

### <a name="will-my-cache-be-available-during-an-importexport-operation"></a>Meu cache estará disponível durante uma operação de importação/exportação?

-   **Exportar** - Caches permanecem disponíveis e pode continuar a utilizar a cache durante uma operação de exportação.
-   **Importar** - Caches tornam-se disponível quando inicia uma operação de importação e ficam disponíveis para utilização quando a operação de importação estiver concluída.


### <a name="can-i-use-importexport-with-redis-cluster"></a>Pode utilizar Importar/exportar com Redis cluster?

Sim, e pode importar/exportar entre uma cache agrupada e de uma cache que não sejam agrupadas. Desde Redis cluster [suporta apenas a base de dados 0](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering), não serão importados quaisquer dados nas bases de dados diferente de 0. Quando são importados dados em cache agrupadas, as teclas são redistribuídas pelos shards do cluster. 

### <a name="how-does-importexport-work-with-a-custom-databases-setting"></a>Como funciona importar/exportar com uma definição de bases de dados personalizado?

Algumas camadas comparar têm diferentes de [limites de bases de dados](cache-configure.md#databases), pelo que existem algumas considerações ao importar se tiver configurado um valor personalizado para o `databases` definição durante a criação de cache.

-   Ao importar para uma camada comparar com um valor mais baixo `databases` limite de que a camada a partir do qual exportou:
    -   Se estiver a utilizar o número predefinido de `databases` que é 16 para todas as camadas comparar, dados não são perdidos.
    -   Se estiver a utilizar um número personalizado de `databases` essa diminuição dentro dos limites para a camada à qual que está a importar, os dados não são perdidos.
    -   Se os dados exportados continham dados numa base de dados que ultrapassa os limites da nova camada, os dados a partir destas bases de dados mais elevados não são importados.

### <a name="how-is-importexport-different-from-redis-persistence"></a>Importar/exportar é diferente do Redis persistente?

Azure persistente de Redis Cache permite-lhe persistirem dados armazenados num Redis ao armazenamento do Azure. Quando persistente está configurado, Azure Redis Cache persiste um instantâneo da cache Redis num formato binário Redis no disco com base numa configurável frequência cópia de segurança. Caso ocorra um evento grave que desativa a principal e uma réplica cache, os dados de cache são restaurados automaticamente, utilizando o instantâneo mais recente. Para mais informações, consulte o artigo [como configurar a recorrência de dados para uma Cache de Redis do Azure Premium](cache-how-to-premium-persistence.md).

Importar / exportar permite-lhe trazer os dados para o ou exportar do Azure Redis Cache. Não configurar cópia de segurança e restaurar utilizando Redis persistente.


### <a name="can-i-automate-importexport-using-powershell-cli-or-other-management-clients"></a>Posso automatizar a utilizar o PowerShell, clip ou outros clientes de gestão de importar/exportar?

Sim, para PowerShell instruções consulte [para importar uma cache Redis](cache-howto-manage-redis-cache-powershell.md#to-import-a-redis-cache) e [para exportar uma cache Redis](cache-howto-manage-redis-cache-powershell.md#to-export-a-redis-cache).



### <a name="i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean"></a>Recebi um erro de limite de tempo durante a operação meu importar/exportar. O que significa?

Se permanecem no pá a **dados de importar** ou **Exportar dados** durante mais de 15 minutos antes de iniciar a operação, receberá um erro semelhante ao seguinte.

    The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.

Para resolver esta questão, iniciar a importação ou exportação operação antes de 15 minutos expirou.

### <a name="i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened"></a>Recebi um erro ao exportar os meus dados ao armazenamento de Blobs do Azure. O que aconteceu?

Importar/exportar só funciona com RDB ficheiros armazenados como blobs de página. Outros tipos de BLOBs não são suportados neste momento, incluindo as contas de armazenamento de Blobs com camadas quentes e frias.


## <a name="next-steps"></a>Próximos passos
Saiba como utilizar mais funcionalidades de cache premium.

-   [Introdução à camada Azure Redis Cache Premium](cache-premium-tier-intro.md)    

  
<!-- IMAGES -->
[cache-settings-import-export-menu]: ./media/cache-how-to-import-export-data/cache-settings-import-export-menu.png
[cache-export-data-choose-account]: ./media/cache-how-to-import-export-data/cache-export-data-choose-account.png
[cache-export-data-choose-storage-container]: ./media/cache-how-to-import-export-data/cache-export-data-choose-storage-container.png
[cache-export-data-container]: ./media/cache-how-to-import-export-data/cache-export-data-container.png
[cache-export-data-export-complete]: ./media/cache-how-to-import-export-data/cache-export-data-export-complete.png
[cache-export-data]: ./media/cache-how-to-import-export-data/cache-export-data.png
[cache-import-data]: ./media/cache-how-to-import-export-data/cache-import-data.png
[cache-import-choose-storage-account]: ./media/cache-how-to-import-export-data/cache-import-choose-storage-account.png
[cache-import-choose-container]: ./media/cache-how-to-import-export-data/cache-import-choose-container.png
[cache-import-choose-blobs]: ./media/cache-how-to-import-export-data/cache-import-choose-blobs.png
[cache-import-blobs]: ./media/cache-how-to-import-export-data/cache-import-blobs.png
[cache-import-data-import-complete]: ./media/cache-how-to-import-export-data/cache-import-data-import-complete.png








