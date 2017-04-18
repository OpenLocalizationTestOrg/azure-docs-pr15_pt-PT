<properties
    pageTitle="Como criar, gerir ou eliminar uma conta de armazenamento no Portal do Azure | Microsoft Azure"
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

Uma conta de armazenamento Azure fornece um espaço de nomes exclusivo para armazenar e aceder ao seu objetos de dados de armazenamento do Windows Azure. Todos os objetos numa conta de armazenamento estão faturados em conjunto, como um grupo. Por predefinição, os dados na sua conta estão disponíveis apenas para si, o proprietário da conta.

[AZURE.INCLUDE [storage-account-types-include](../../includes/storage-account-types-include.md)]

## <a name="storage-account-billing"></a>Faturação de conta de armazenamento

[AZURE.INCLUDE [storage-account-billing-include](../../includes/storage-account-billing-include.md)]

> [AZURE.NOTE] Quando cria uma máquina virtual Azure, uma conta de armazenamento é criada por si automaticamente na localização implementação se que ainda não tiver uma conta de armazenamento nessa localização. Para que não é necessário seguir os passos abaixo para criar uma conta de armazenamento para os seus discos máquina virtual. O nome da conta de armazenamento será baseado no nome da máquina virtual. Consulte a [documentação de máquinas virtuais do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) para obter mais detalhes.

## <a name="storage-account-endpoints"></a>Pontos finais de conta de armazenamento

Cada objeto que armazena no armazenamento do Windows Azure tem um endereço de URL exclusivo. O nome da conta de armazenamento de formulários o subdomínio desse endereço. A combinação de nome de domínio e subdomínio, que é específico de cada serviço, um *ponto final* para a sua conta de armazenamento de formulários.

Por exemplo, se a sua conta de armazenamento se chamar *mystorageaccount*, em seguida, os pontos finais predefinido para a sua conta de armazenamento são:

- Serviço de blob: http://*mystorageaccount*. blob.core.windows.net

- Serviço de tabela: http://*mystorageaccount*. table.core.windows.net

- Fila de serviço: http://*mystorageaccount*. queue.core.windows.net

- Serviço de ficheiros: http://*mystorageaccount*. file.core.windows.net

> [AZURE.NOTE] Uma conta de armazenamento de BLOBs apenas expõe o ponto final de serviço de Blobs.

O URL para aceder a um objeto numa conta de armazenamento é compilado por acrescentar a localização do objecto na conta de armazenamento para o ponto final. Por exemplo, um endereço de BLOBs poderá ter neste formato: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

Também pode configurar um nome de domínio personalizado para utilizar com a sua conta de armazenamento. Para contas clássico de armazenamento, consulte o artigo [configurar um nome para o ponto final de armazenamento de Blobs do domínio personalizado](storage-custom-domain-name.md) para obter detalhes. Para contas de armazenamento do Gestor de recursos, esta funcionalidade não foi adicionada ao [Azure portal](https://portal.azure.com) ainda, mas pode configurá-la com o PowerShell. Para obter mais informações, consulte o cmdlet [Set-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607146.aspx) .  

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. No menu concentrador, selecione **Novo** -> **dados + armazenamento** -> **conta de armazenamento**.

3. Introduza um nome para a sua conta de armazenamento. Consulte o artigo [pontos finais de conta de armazenamento](#storage-account-endpoints) para obter detalhes sobre como o nome da conta de armazenamento irão ser utilizado para os objetos no Azure armazenamento de endereços.

    > [AZURE.NOTE] Nomes de conta de armazenamento tem de estar entre 3 e 24 carateres de comprimento e podem conter números e letras em minúsculas apenas.
    >  
    > O nome da sua conta de armazenamento tem de ser exclusivo Azure. Portal do Azure irá indicar se o nome da conta de armazenamento que selecionar já está em utilização.

4. Especificar o modelo de implementação para ser utilizado: **Gestor de recursos** ou **clássico**. **Gestor de recursos** é o modelo de implementação recomendadas. Para mais informações, consulte [Noções sobre o Gestor de recursos de implementação e implementação clássica](../resource-manager-deployment-model.md).

    > [AZURE.NOTE] Contas de armazenamento de BLOBs apenas podem ser criadas utilizando o modelo de implementação do Gestor de recursos.

5. Selecione o tipo de conta de armazenamento: **objectivo geral** ou o **armazenamento de BLOBs**. **Objectivo geral** é a predefinição.

    Se tiver sido selecionado **finalidade geral** , em seguida, especifique a camada de desempenho: **padrão** ou **Premium**. A predefinição é **padrão**. Para obter mais detalhes sobre contas de armazenamento padrão e premium, consulte o artigo [Introdução ao Microsoft Azure armazenamento](storage-introduction.md) e [armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho do Azure Máquina Virtual](storage-premium-storage.md).

    Se o **Armazenamento de BLOBs** foi selecionado, em seguida, especifique a camada de acesso: **quente** ou **modernos**. A predefinição é **quente**. Consulte o artigo [armazenamento de Blobs do Azure: arrefecer e quente camadas](storage-blob-storage-tiers.md) para obter mais detalhes.

6. Selecione a opção de replicação para a conta de armazenamento: **LRS**, **GRS**, **GRS RT**ou **ZRS**. A predefinição é **GRS RT**. Para obter mais detalhes sobre as opções de replicação de armazenamento do Windows Azure, consulte o artigo [replicação de armazenamento do Windows Azure](storage-redundancy.md).

7. Selecione a subscrição na qual pretende criar a nova conta de armazenamento.

8. Especificar um novo grupo de recursos ou selecione um grupo de recursos existente. Para mais informações sobre os grupos de recursos, consulte o artigo [Descrição geral do Gestor de recursos do Azure](../azure-resource-manager/resource-group-overview.md).

9. Selecione a localização geográfica para a sua conta de armazenamento. Consulte o artigo [Azure regiões](https://azure.microsoft.com/regions/#services) para obter mais informações sobre que serviços estão disponíveis no qual o região.

10. Clique em **Criar** para criar a conta de armazenamento.

## <a name="manage-your-storage-account"></a>Gerir a sua conta de armazenamento

### <a name="change-your-account-configuration"></a>Alterar a configuração da sua conta

Depois de criar a sua conta de armazenamento, pode modificar a sua configuração, tal como alterar a opção de replicação utilizada para a conta ou alterar a camada de acesso de uma conta de armazenamento de Blobs. No [portal do Azure](https://portal.azure.com), navegue até à sua conta de armazenamento, clique em **todas as definições** e, em seguida, clique em **configuração** para ver e/ou alterar a configuração de conta.

> [AZURE.NOTE] Consoante a camada de desempenho que escolheu quando criar a conta de armazenamento, algumas opções de replicação podem não estar disponíveis.

Alterar a opção de replicação irá alterar a sua preços. Para obter mais detalhes, consulte o artigo [preços de armazenamento Azure](https://azure.microsoft.com/pricing/details/storage/) página.

Para contas de armazenamento de BLOBs, alterar a camada do access pode assumir taxas para que a alteração além da alteração seu preços. Consulte o artigo as [contas de armazenamento de BLOBs - preços e de faturação](storage-blob-storage-tiers.md#pricing-and-billing) para obter mais detalhes.

### <a name="manage-your-storage-access-keys"></a>Gerir as teclas de acesso de armazenamento

Quando cria uma conta de armazenamento, Azure gera duas teclas de acesso de armazenamento de 512 bits, que são utilizadas para autenticação, quando a conta de armazenamento é acedida. Fornecendo dois teclas de acesso de armazenamento, Azure permite-lhe gerar chaves com acesso para esse serviço ou sem interrupções no seu serviço de armazenamento.

> [AZURE.NOTE] Recomendamos que evite partilha as teclas de acesso de armazenamento com outra pessoa. Para permitir o acesso aos recursos de armazenamento sem dar saída de teclas de acesso, pode utilizar uma *assinatura de acesso partilhado*. Uma assinatura de acesso partilhado oferece um acesso a um recurso na sua conta para obter um intervalo que definir e com as permissões que especificar. Para mais informações, consulte [Utilizar partilhado acesso assinaturas (SA)](storage-dotnet-shared-access-signature-part-1.md) .

#### <a name="view-and-copy-storage-access-keys"></a>Ver e copiar teclas de acesso de armazenamento

No [portal do Azure](https://portal.azure.com), navegue até à sua conta de armazenamento, clique em **todas as definições** e, em seguida, clique em **teclas de acesso** para ver, copiar e gerar as teclas de acesso de conta. A **Teclas de acesso** pá também inclui cadeias de ligação pré-configurada utilizando as suas chaves principais e secundárias que pode copiar para utilizar nas suas aplicações.

#### <a name="regenerate-storage-access-keys"></a>Gerar teclas de acesso de armazenamento

Recomendamos que altere as teclas de acesso à sua conta de armazenamento periodicamente para ajudar a manter as suas ligações de armazenamento seguro. Teclas de acesso duas são atribuídas para que possa pode manter ligações para a conta de armazenamento utilizando uma tecla de acesso, enquanto que gerar a tecla de acesso.

> [AZURE.WARNING] Voltar a gerar teclas de acesso pode afetar serviços no Azure, bem como a suas própria aplicações que são dependentes a conta de armazenamento. Todos os clientes que utilizam a tecla do access para aceder à conta de armazenamento tem de ser atualizados para utilizar a nova chave.

**Dos serviços de multimédia** - se tiver dos serviços de multimédia que são dependentes na sua conta de armazenamento, tem novamente sincronizar as teclas de acesso com o seu serviço de multimédia depois de voltar a gerar as teclas.

**Aplicações** - se tiver aplicações web ou serviços em nuvem que utilizam a conta de armazenamento, perderá as ligações se voltar a gerar chaves, a menos que as suas chaves filmar.

**Explorador de armazenamento** - se estiver a utilizar as [aplicações do Explorador de armazenamento](storage-explorers.md), provavelmente terá de atualizar a chave de armazenamento utilizada nessas aplicações.

Eis o processo de rodar as teclas de acesso de armazenamento:

1. Atualize as cadeias de ligação no seu código de aplicação para fazer referência a tecla de acesso secundário da conta de armazenamento.

2. Voltar a gerar a chave primária acesso para a sua conta de armazenamento. No pá **Teclas de acesso** , clique em **Gerar chave1**e, em seguida, clique em **Sim** para confirmar que pretende gerar uma nova chave.

3. Atualize as cadeias de ligação no seu código para fazer referência a nova chave de acesso principal.

4. Voltar a gerar a tecla de acesso secundário da mesma forma.

## <a name="delete-a-storage-account"></a>Eliminar uma conta de armazenamento

Para remover uma conta de armazenamento que já não está a utilizar, navegue para a conta de armazenamento no [portal do Azure](https://portal.azure.com)e clique em **Eliminar**. Eliminar uma conta de armazenamento elimina toda a conta, incluindo todos os dados na conta.

> [AZURE.WARNING] Não é possível restaurar uma conta de armazenamento eliminado ou obter qualquer conteúdo que continha antes da eliminação. Certifique-se de que agregar nada que pretende guardar antes de eliminar a conta. Isto também se aplica para qualquer recursos na conta — quando elimina um blob, tabela, fila ou ficheiro, será eliminado permanentemente.

Para eliminar uma conta de armazenamento que está associada com uma máquina virtual Azure, primeiro tem de se certificar de que quaisquer discos máquina virtual terem sido eliminados. Se pela primeira vez, não elimine seus discos máquina virtual, em seguida, quando tenta eliminar a sua conta de armazenamento, verá uma mensagem de erro semelhante a:

    Failed to delete storage account <vm-storage-account-name>. Unable to delete storage account <vm-storage-account-name>: 'Storage account <vm-storage-account-name> has some active image(s) and/or disk(s). Ensure these image(s) and/or disk(s) are removed before deleting this storage account.'.

Se a conta de armazenamento utiliza o modelo de implementação clássico, pode remover o disco de máquina virtual seguindo estes passos no [portal do Azure](https://manage.windowsazure.com):

1. Navegue para o [portal Azure clássica](https://manage.windowsazure.com).
2. Navegue até ao separador máquinas virtuais.
3. Clique no separador de discos.
4. Seleccionar o disco de dados e, em seguida, clique em eliminar disco.
5. Para eliminar imagens de disco, navegue para o separador de imagens e elimine as imagens que são armazenadas na conta.

Para mais informações, consulte a [documentação do Azure Virtual Machine](http://azure.microsoft.com/documentation/services/virtual-machines/).

## <a name="next-steps"></a>Próximos passos

- [Armazenamento de Blobs do Azure: Camadas de interessantes e quente](storage-blob-storage-tiers.md)
- [Azure replicação de armazenamento](storage-redundancy.md)
- [Configurar armazenamento Azure cadeias de ligação](storage-configure-connection-string.md)
- [Transferir dados com o utilitário AzCopy da linha de comandos](storage-use-azcopy.md)
- Visite o [blogue da equipa do armazenamento Azure](http://blogs.msdn.com/b/windowsazurestorage/).
