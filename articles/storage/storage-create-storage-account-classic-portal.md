<properties
    pageTitle="Como criar, gerir ou eliminar uma conta de armazenamento no Portal clássica do Azure | Microsoft Azure"
    description="Criar uma nova conta de armazenamento, gerir as teclas de acesso de conta ou eliminar uma conta de armazenamento no Portal do Azure. Saiba mais sobre contas de armazenamento padrão e premium."
    services="storage"
    documentationCenter=""
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/26/2016"
    ms.author="robinsh"/>


# <a name="about-azure-storage-accounts"></a>Sobre contas de armazenamento Azure

[AZURE.INCLUDE [storage-selector-portal-create-storage-account](../../includes/storage-selector-portal-create-storage-account.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools](../../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Descrição geral

Uma conta de armazenamento Azure dá-lhe acesso para os serviços de Blobs do Azure, fila de espera, tabela e ficheiro de armazenamento do Windows Azure. A conta de armazenamento fornece o espaço de nomes exclusivo para os objetos de dados de armazenamento do Windows Azure. Por predefinição, os dados na sua conta estão disponíveis apenas para si, o proprietário da conta.

Existem dois tipos de contas de armazenamento:

- Uma conta de armazenamento padrão inclui armazenamento de BLOBs, tabela, fila de espera e ficheiro.
- Uma conta de armazenamento premium atualmente suporta apenas a discos Azure máquina virtual. Consulte o artigo [armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho do Azure Máquina Virtual](storage-premium-storage.md) para obter uma descrição geral aprofundada de armazenamento de Premium.

## <a name="storage-account-billing"></a>Faturação de conta de armazenamento

São faturada para utilização de armazenamento do Windows Azure com base na sua conta de armazenamento. Os custos de armazenamento sejam baseiam quatro fatores: capacidade de armazenamento, esquema de replicação, as transações de armazenamento e saída de dados.

- Capacidade de armazenamento refere-se para a quantidade de alocação de conta seu armazenamento estão a utilizar para armazenar dados. O custo simplesmente do armazenamento dos seus dados é determinado pela quantidade de dados está a armazenar e como é replicada.
- Replicação determina quantas cópias dos seus dados são mantidas ao mesmo tempo e, em que localizações.
- Transações referir-se a todas as operações leitura e escrita para o armazenamento do Windows Azure.
- Saída de dados que se refere a dados transferidos terminar uma região Azure. Quando os dados na sua conta de armazenamento são acedidos por uma aplicação que não está em execução na mesma região, se a aplicação é um serviço na nuvem ou outro tipo de aplicação, são cobrados para saída de dados. (Para os serviços Azure, pode demorar passos para agrupar os seus dados e serviços nos mesmos centros de dados para reduzir ou eliminar os custos de saída de dados.)  

A página de [Preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage) fornece informações detalhadas sobre preços de capacidade de armazenamento, replicação e transações. A página de [Detalhes de preços de transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/) fornece informações detalhadas sobre preços de saída de dados.

Para obter detalhes sobre destinos de desempenho e capacidade de conta de armazenamento, consulte o artigo [escalabilidade de armazenamento do Azure e metas de desempenho](storage-scalability-targets.md).

> [AZURE.NOTE] Quando cria uma máquina virtual Azure, uma conta de armazenamento é criada por si automaticamente na localização implementação se que ainda não tiver uma conta de armazenamento nessa localização. Para que não é necessário seguir os passos abaixo para criar uma conta de armazenamento para os seus discos máquina virtual. O nome da conta de armazenamento será baseado no nome da máquina virtual. Consulte a [documentação de máquinas virtuais do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) para obter mais detalhes.

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

1. Inicie sessão no [Portal clássica Azure](https://manage.windowsazure.com).

2. Na barra de tarefas na parte inferior da página, clique em **Novo** . Selecione **Serviços de dados** | **armazenamento**e, em seguida, clique em **Criar rápida**.

    ![NewStorageAccount](./media/storage-create-storage-account-classic-portal/storage_NewStorageAccount.png)

3. No **URL**, introduza um nome para a sua conta de armazenamento.

    > [AZURE.NOTE] Nomes de conta de armazenamento tem de estar entre 3 e 24 carateres de comprimento e podem conter números e letras em minúsculas apenas.
    >  
    > O nome da sua conta de armazenamento tem de ser exclusivo Azure. Portal clássica do Azure irá indicar se o nome da conta de armazenamento que selecionar já é disponibilizado.

    Consulte o artigo [pontos finais de conta de armazenamento](#storage-account-endpoints) abaixo para obter detalhes sobre como o nome da conta de armazenamento irão ser utilizado para os objetos no Azure armazenamento de endereços.

4. No **Grupo localização/afinidade**, selecione uma localização para a sua conta de armazenamento é perto ou aos seus clientes. Se os dados na sua conta de armazenamento irão ser acedidos a partir de outro serviço de Azure, tal como uma máquina virtual Azure ou serviço na nuvem, poderá querer selecionar um grupo de afinidade a partir da lista para a sua conta de armazenamento no Centro de dados do mesmo com outros serviços do Azure que está a utilizar para melhorar o desempenho e reduzir os custos de grupo.

    Tenha em atenção que tem de selecionar um grupo de afinidade notificadas aquando da sua conta de armazenamento. Não pode mover uma conta existente a um grupo de afinidade. Para mais informações sobre os grupos de afinidade, consulte [a localização do serviço de com um grupo de afinidade](#service-co-location-with-an-affinity-group) .

    >[AZURE.IMPORTANT] Para determinar que localizações estão disponíveis para a sua subscrição, pode ligar a operação de [todos os fornecedores de recursos da lista](https://msdn.microsoft.com/library/azure/dn790524.aspx) . Lista de fornecedores do PowerShell, contacte a [Obter AzureLocation](https://msdn.microsoft.com/library/azure/dn757693.aspx). A partir do .NET, utilize o método de [lista](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.provideroperationsextensions.list.aspx) a classe de ProviderOperationsExtensions de.
    >
    >Para além disso, consulte o artigo [Azure regiões](https://azure.microsoft.com/regions/#services) para obter mais informações sobre que serviços estão disponíveis no qual o região.


5. Se tiver mais do que uma subscrição Azure, em seguida, é apresentado o campo de **subscrição** . Na sua **subscrição**, introduza a subscrição Azure que pretende utilizar com a conta de armazenamento.

6. Na **replicação**, selecione o nível de replicação para a sua conta de armazenamento pretendido. A opção de replicação recomendadas está replicação geo redundantes, que fornece durabilidade máxima para os seus dados. Para obter mais detalhes sobre as opções de replicação de armazenamento do Windows Azure, consulte o artigo [replicação de armazenamento do Windows Azure](storage-redundancy.md).

6. Clique em **criar a conta de armazenamento**.

    Poderá demorar alguns minutos para criar a sua conta de armazenamento. Para verificar o estado, pode monitorizar as notificações na parte inferior do Portal clássica do Azure. Depois de ter sido criada a conta de armazenamento, a sua nova conta de armazenamento tem o estado **Online** e está pronta para utilização.

![StoragePage](./media/storage-create-storage-account-classic-portal/Storage_StoragePage.png)


### <a name="storage-account-endpoints"></a>Pontos finais de conta de armazenamento

Cada objeto que armazena no armazenamento do Windows Azure tem um endereço de URL exclusivo. O nome da conta de armazenamento de formulários o subdomínio desse endereço. A combinação de nome de domínio e subdomínio, que é específico de cada serviço, um *ponto final* para a sua conta de armazenamento de formulários.

Por exemplo, se a sua conta de armazenamento se chamar *mystorageaccount*, em seguida, os pontos finais predefinido para a sua conta de armazenamento são:

- Serviço de blob: http://*mystorageaccount*. blob.core.windows.net

- Serviço de tabela: http://*mystorageaccount*. table.core.windows.net

- Fila de serviço: http://*mystorageaccount*. queue.core.windows.net

- Serviço de ficheiros: http://*mystorageaccount*. file.core.windows.net

Pode ver os pontos finais para a sua conta de armazenamento no dashboard de armazenamento no [Portal clássica Azure](https://manage.windowsazure.com) assim que a conta foi criada.

O URL para aceder a um objeto numa conta de armazenamento é compilado por acrescentar a localização do objecto na conta de armazenamento para o ponto final. Por exemplo, um endereço de BLOBs poderá ter neste formato: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

Também pode configurar um nome de domínio personalizado para utilizar com a sua conta de armazenamento. Consulte [configurar um nome de domínio personalizado para o ponto final de armazenamento de BLOBs](storage-custom-domain-name.md) para obter detalhes.

### <a name="service-co-location-with-an-affinity-group"></a>A localização do serviço de com um grupo de afinidade

Um *grupo de afinidade* é um agrupamento geográfico dos seus serviços Azure e VMs com a sua conta de armazenamento Azure. Um grupo de afinidade pode melhorar o desempenho do serviço por localizar das cargas de trabalho do computador no Centro de dados do mesmo ou junto à audiência de utilizador de destino. Além disso, sem taxas de faturaçãohttps são suportadas pelo saída quando numa conta de armazenamento de dados são acedidos a partir de outro serviço de que faz parte do mesmo grupo afinidade.

> [AZURE.NOTE]  Para criar um grupo de afinidade, abrir a área de <b>Definições</b> do [Portal clássica Azure](https://manage.windowsazure.com), clique em <b>grupos de afinidade</b>e, em seguida, clique em <b>Adicionar um grupo de afinidade</b> ou no botão <b>Adicionar</b> . Também pode criar e gerir grupos de afinidade ao utilizar a API de gestão do Azure serviço. Para mais informações, consulte <a href="http://msdn.microsoft.com/library/azure/ee460798.aspx">operações em afinidade grupos</a> .

## <a name="view-copy-and-regenerate-storage-access-keys"></a>Ver, copiar e gerar teclas de acesso de armazenamento

Quando cria uma conta de armazenamento, Azure gera duas teclas de acesso de armazenamento de 512 bits, que são utilizadas para autenticação, quando a conta de armazenamento é acedida. Fornecendo dois teclas de acesso de armazenamento, Azure permite-lhe gerar chaves com acesso para esse serviço ou sem interrupções no seu serviço de armazenamento.

> [AZURE.NOTE] Recomendamos que evite partilha as teclas de acesso de armazenamento com outra pessoa. Para permitir o acesso aos recursos de armazenamento sem dar saída de teclas de acesso, pode utilizar uma *assinatura de acesso partilhado*. Uma assinatura de acesso partilhado oferece um acesso a um recurso na sua conta para obter um intervalo que definir e com as permissões que especificar. Para mais informações, consulte [Utilizar partilhado acesso assinaturas (SA)](storage-dotnet-shared-access-signature-part-1.md) .

No [Portal clássica Azure](https://manage.windowsazure.com), utilize **Gerir chaves** no dashboard de ou para a página de **armazenamento** para ver, copiar e gerar as teclas de acesso de armazenamento que são utilizadas para aceder a serviços de BLOBs, tabela e fila de espera.

### <a name="copy-a-storage-access-key"></a>Copiar uma tecla de acesso de armazenamento  

Pode utilizar **Teclas de gerir** para copiar uma tecla de acesso de armazenamento para utilizar numa cadeia de ligação. A cadeia de ligação requer o nome da conta de armazenamento e uma chave para utilizar na autenticação. Para obter informações sobre como configurar as cadeias de ligação para aceder aos serviços do Azure armazenamento, consulte o artigo [Configurar cadeias de ligação do Azure armazenamento](storage-configure-connection-string.md).

1. No [Portal clássica Azure](https://manage.windowsazure.com), clique em **armazenamento**e, em seguida, clique no nome da conta de armazenamento para abrir o dashboard.

2. Clique em **Gerir teclas**.

    **Gerir as teclas de acesso** é aberta.

    ![Managekeys](./media/storage-create-storage-account-classic-portal/Storage_ManageKeys.png)


3. Para copiar uma tecla de acesso de armazenamento, selecione o texto da tecla. Em seguida, botão direito do rato e clique em **Copiar**.

### <a name="regenerate-storage-access-keys"></a>Gerar teclas de acesso de armazenamento
Recomendamos que altere as teclas de acesso à sua conta de armazenamento periodicamente para ajudar a manter as suas ligações de armazenamento seguro. Teclas de acesso duas são atribuídas para que possa pode manter ligações para a conta de armazenamento utilizando uma tecla de acesso, enquanto que gerar a tecla de acesso.

> [AZURE.WARNING] Voltar a gerar teclas de acesso pode afetar serviços no Azure, bem como a suas própria aplicações que são dependentes a conta de armazenamento. Todos os clientes que utilizam a tecla do access para aceder à conta de armazenamento tem de ser atualizados para utilizar a nova chave.

**Dos serviços de multimédia** - se tiver dos serviços de multimédia que são dependentes na sua conta de armazenamento, tem novamente sincronizar as teclas de acesso com o seu serviço de multimédia depois de voltar a gerar as teclas.

**Aplicações** - se tiver aplicações web ou serviços em nuvem que utilizam a conta de armazenamento, perderá as ligações se voltar a gerar chaves, a menos que as suas chaves filmar. 

**Explorador de armazenamento** - se estiver a utilizar as [aplicações do Explorador de armazenamento](storage-explorers.md), provavelmente terá de atualizar a chave de armazenamento utilizada nessas aplicações.

Eis o processo de rodar as teclas de acesso de armazenamento:

1. Atualize as cadeias de ligação no seu código de aplicação para fazer referência a tecla de acesso secundário da conta de armazenamento.

2. Voltar a gerar a chave primária acesso para a sua conta de armazenamento. No [Portal clássica Azure](https://manage.windowsazure.com), a partir do dashboard de ou página **Configurar** , clique em **Gerir chaves**. Clique em **Gerar** sob a chave primária de acesso e, em seguida, clique em **Sim** para confirmar que pretende gerar uma nova chave.

3. Atualize as cadeias de ligação no seu código para fazer referência a nova chave de acesso principal.

4. Voltar a gerar a tecla de acesso secundário.

## <a name="delete-a-storage-account"></a>Eliminar uma conta de armazenamento

Para remover uma conta de armazenamento que já não está a utilizar, utilize o **Eliminar** no dashboard de ou para a página de **Configurar** . **Delete** elimina a conta de armazenamento todo, incluindo todos os blobs, tabelas e filas na conta.

> [AZURE.WARNING] Não é possível restaurar uma conta de armazenamento eliminado ou obter qualquer conteúdo que continha antes da eliminação. Certifique-se de que agregar nada que pretende guardar antes de eliminar a conta. Isto também se aplica para qualquer recursos na conta — quando elimina um blob, tabela, fila ou ficheiro, será eliminado permanentemente.
>
> Se a sua conta de armazenamento contiver ficheiros VHD para uma máquina virtual Azure, em seguida, tem de eliminar as imagens e discos que estiver a utilizar os ficheiros VHD antes de poder eliminar a conta de armazenamento. Em primeiro lugar, pare a máquina virtual se estiver em execução e, em seguida, elimine. Para eliminar discos, navegue para o separador de **discos** e elimine quaisquer discos aí. Para eliminar imagens, navegue para o separador de **imagens** e elimine as imagens que são armazenadas na conta.

1. No [Portal clássica Azure](https://manage.windowsazure.com), clique em **armazenamento**.

2. Clique em qualquer parte na entrada de conta de armazenamento, exceto o nome e, em seguida, clique em **Eliminar**.

     - Ou -

    Clique no nome da conta de armazenamento para abrir o dashboard e, em seguida, clique em **Eliminar**.

3. Clique em **Sim** para confirmar que pretende eliminar a conta de armazenamento.

## <a name="next-steps"></a>Próximos passos

- Para saber mais sobre o armazenamento do Windows Azure, consulte a [documentação de armazenamento do Windows Azure](https://azure.microsoft.com/documentation/services/storage/).
- Visite o [blogue da equipa do armazenamento Azure](http://blogs.msdn.com/b/windowsazurestorage/).
- [Transferir dados com o utilitário AzCopy da linha de comandos](storage-use-azcopy.md)
