<properties
    pageTitle="Utilizar o Azure PowerShell com armazenamento Azure | Microsoft Azure"
    description="Saiba como utilizar os cmdlets do Azure PowerShell para o armazenamento do Windows Azure para criar e gerir contas de armazenamento; trabalhar com blobs, tabelas, filas e ficheiros; configurar e a análise de armazenamento de consulta e criar assinaturas acesso partilhado."
    services="storage"
    documentationCenter="na"
    authors="robinsh"
    manager="carmonm"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>

# <a name="using-azure-powershell-with-azure-storage"></a>Utilizar o Azure PowerShell com armazenamento Azure

## <a name="overview"></a>Descrição geral

Azure PowerShell é um módulo que fornece cmdlets para gerir Azure através do Windows PowerShell. É uma baseado em tarefas da linha de comandos shell e linguagem concebido especialmente para a administração do sistema. Com o PowerShell, pode facilmente controlar e automatizar a administração dos seus serviços Azure e aplicações. Por exemplo, pode utilizar os cmdlets para efetuar as mesmas tarefas que pode efetuar através do [Portal do Azure](https://portal.azure.com).

Neste guia, vamos explorar como utilizar os [Cmdlets do Azure armazenamento](https://msdn.microsoft.com/library/azure/mt269418.aspx) para executar uma variedade de tarefas de administração e desenvolvimento com armazenamento do Windows Azure.

Este guia assume que tem prévia experiência na utilização de [Armazenamento do Windows Azure](https://azure.microsoft.com/documentation/services/storage/) e do [Windows PowerShell](http://technet.microsoft.com/library/bb978526.aspx). O guia fornece um número de scripts para demonstrar a utilização do PowerShell com o armazenamento do Windows Azure. Deverá actualizar as variáveis de script com base na sua configuração antes de executar cada script.

A primeira secção neste guia fornece uma vista rápida armazenamento do Windows Azure e PowerShell. Para obter informações detalhadas e instruções, inicie a partir de [Pré-requisitos para utilizar o PowerShell Azure com armazenamento do Windows Azure](#prerequisites-for-using-azure-powershell-with-azure-storage).


## <a name="getting-started-with-azure-storage-and-powershell-in-5-minutes"></a>Introdução ao armazenamento do Windows Azure e PowerShell em 5 minutos

Esta secção mostra-lhe como aceder ao armazenamento do Windows Azure através do PowerShell em 5 minutos.

**Familiarizado com o Azure:** Obter uma subscrição do Microsoft Azure e uma conta Microsoft associada a essa subscrição. Para obter informações sobre as opções de compra Azure, consulte o artigo [Versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/), [Opções de compra](https://azure.microsoft.com/pricing/purchase-options/)e [Membro oferece](https://azure.microsoft.com/pricing/member-offers/) (para os membros do MSDN, Microsoft Partner Network e BizSpark e outros programas da Microsoft).

Consulte [atribuir funções de administrador no Azure Active Directory (Azure AD)](https://msdn.microsoft.com/library/azure/hh531793.aspx) para obter mais informações sobre as subscrições Azure.

**Depois de criar uma subscrição do Microsoft Azure e conta:**

1.  Transfira e instale o [Azure PowerShell](http://go.microsoft.com/?linkid=9811175&clcid=0x409).
2.  Iniciar Windows PowerShell Scripting ambiente integrado (ISE): No seu computador local, aceda ao menu **Iniciar** . Escreva **As ferramentas administrativas** e clique em executá-la. Na janela **Ferramentas administrativas** , **O Windows PowerShell ISE**com o botão direito, clique em **Executar como administrador**.
3.  No **Windows PowerShell ISE**, clique em **ficheiro** > **Novo** para criar um novo ficheiro de script.
4.  Agora, vamos dar-lhe um script simples que mostra os comandos do PowerShell básicos para aceder ao armazenamento do Windows Azure. O script primeiro irá pedir o Azure as credenciais de conta para adicionar o seu Azure conta para o ambiente do PowerShell local. Em seguida, o script irá predefinir a subscrição do Azure e criar uma nova conta de armazenamento no Azure. Em seguida, o script irá criar um novo contentor na nova conta de armazenamento e carregar um ficheiro de imagem existente (BLOBs) para desse contentor. Depois do script listas de todos os blobs no contentor, irá criar um novo directório de destino no seu computador local e transferir o ficheiro de imagem.
5.  Na secção de código seguinte, selecione o script entre os comentários **#begin** e **#end**. Prima CTRL + C para copiá-lo para a área de transferência.

        #begin
        # Update with the name of your subscription.
        $SubscriptionName = "YourSubscriptionName"

        # Give a name to your new storage account. It must be lowercase!
        $StorageAccountName = "yourstorageaccountname"

        # Choose "West US" as an example.
        $Location = "West US"

        # Give a name to your new container.
        $ContainerName = "imagecontainer"

        # Have an image file and a source directory in your local computer.
        $ImageToUpload = "C:\Images\HelloWorld.png"

        # A destination directory in your local computer.
        $DestinationFolder = "C:\DownloadImages"

        # Add your Azure account to the local PowerShell environment.
        Add-AzureAccount

        # Set a default Azure subscription.
        Select-AzureSubscription -SubscriptionName $SubscriptionName –Default

        # Create a new storage account.
        New-AzureStorageAccount –StorageAccountName $StorageAccountName -Location $Location

        # Set a default storage account.
        Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName

        # Create a new container.
        New-AzureStorageContainer -Name $ContainerName -Permission Off

        # Upload a blob into a container.
        Set-AzureStorageBlobContent -Container $ContainerName -File $ImageToUpload

        # List all blobs in a container.
        Get-AzureStorageBlob -Container $ContainerName

        # Download blobs from the container:
        # Get a reference to a list of all blobs in a container.
        $blobs = Get-AzureStorageBlob -Container $ContainerName

        # Create the destination directory.
        New-Item -Path $DestinationFolder -ItemType Directory -Force  

        # Download blobs into the local destination directory.
        $blobs | Get-AzureStorageBlobContent –Destination $DestinationFolder
        #end

5.  No **Windows PowerShell ISE**, prima CTRL + V para copiar o script. Clique em **ficheiro** > **Guardar**. Na janela de diálogo **Guardar como** , escreva o nome do ficheiro de script, tal como "mystoragescript". Clique em **Guardar**.

6.  Agora, tem de atualizar as variáveis de script com base nas suas definições de configuração. Tem de atualizar a variável de **$SubscriptionName** com a sua própria subscrição. Pode manter outras variáveis conforme especificado no script ou atualizá-las conforme pretender.

    - **$SubscriptionName:** Tem de actualizar esta variável com o seu próprio nome da subscrição. Siga um dos três seguintes formas para localizar o nome da sua subscrição:

        um. No **Windows PowerShell ISE**, clique em **ficheiro** > **Novo** para criar um novo ficheiro de script. Copie o seguinte script para o novo ficheiro de script e clique em **Depurar** > **Executar**. O seguinte script do primeiro irá pedir o Azure as credenciais de conta para adicionar o seu Azure conta para o ambiente do PowerShell local e, em seguida, Mostrar todas as subscrições que estão ligadas à sessão do PowerShell local. Tome nota do nome da subscrição que pretende utilizar enquanto seguir este tutorial:

            Add-AzureAccount
                Get-AzureSubscription | Format-Table SubscriptionName, IsDefault, IsCurrent, CurrentStorageAccountName

        b. Para localizar e copiar o nome da sua subscrição no [Portal do Azure](https://portal.azure.com), no menu concentrador à esquerda, clique em **subscrições**. Copie o nome da subscrição que pretende utilizar ao executar os scripts neste guia.

        ![Portal do Azure][Image2]

        c. Para localizar e copiar o nome da sua subscrição no [Portal clássica Azure](https://manage.windowsazure.com/), desloque para baixo e clique em **Definições** no lado esquerdo do portal. Clique em **subscrições** para ver uma lista das suas subscrições. Copie o nome da subscrição que pretende utilizar ao executar os scripts indicados neste guia.

        ![Azure Portal clássico][Image1]

    - **$StorageAccountName:** Utilize o nome indicado no script ou introduza um novo nome para a sua conta de armazenamento. **Importantes:** O nome da conta de armazenamento tem de ser exclusivo no Azure. Tem de ser minúscula, demasiado!

    - **$Location:** Utilizar determinado "oeste EUA" no script ou selecione de outras localizações Azure, tal como Leste dos EUA, Europa Norte e assim sucessivamente.

    - **$ContainerName:** Utilize o nome indicado no script ou introduza um novo nome para o contentor.

    - **$ImageToUpload:** Introduza um caminho para uma imagem no seu computador local, tais como: "C:\Images\HelloWorld.png".

    - **$DestinationFolder:** Introduza um caminho para um diretório local para armazenar ficheiros transferidos a partir do armazenamento do Windows Azure, tais como: "C:\DownloadImages".

7.  Depois de actualizar as variáveis de script no ficheiro "mystoragescript.ps1", clique em **ficheiro** > **Guardar**. Em seguida, clique em **Depurar** > **Executar** ou prima **F5** para executar o script.  

Depois de executa o script, deve ter uma pasta de destino local que inclui o ficheiro de imagem transferido. A captura de ecrã seguinte mostra um resultado de exemplo:

![Transferir Blobs][Image3]


> [AZURE.NOTE] A secção "Introdução com armazenamento Azure e PowerShell 5 minutos" fornecida uma breve introdução sobre como utilizar o Azure PowerShell com armazenamento do Windows Azure. Para obter informações detalhadas e instruções, aconselhamos a ler as secções seguintes.

## <a name="prerequisites-for-using-azure-powershell-with-azure-storage"></a>Pré-requisitos para utilizar o Azure PowerShell com armazenamento do Windows Azure
Precisa de uma subscrição do Azure e conta para executar os cmdlets do PowerShell indicadas neste guia, conforme descrito acima.

Azure PowerShell é um módulo que fornece cmdlets para gerir Azure através do Windows PowerShell. Para obter informações sobre como instalar e configurar o Azure PowerShell, consulte o artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md). Recomendamos que transferir e instalar ou atualizar para o módulo Azure PowerShell mais recente antes de utilizar este guia.

Pode executar os cmdlets na consola do Windows PowerShell padrão ou o Windows PowerShell integrada Scripting ambiente (ISE). Por exemplo, para abrir **O Windows PowerShell ISE**, aceda ao menu Iniciar, escreva as ferramentas administrativas e clique em executá-la. Na janela Ferramentas administrativas, o Windows PowerShell ISE com o botão direito, clique em executar como administrador.

## <a name="how-to-manage-storage-accounts-in-azure"></a>Como gerir contas de armazenamento no Azure

### <a name="how-to-set-a-default-azure-subscription"></a>Como configurar uma subscrição do Azure predefinida
Para gerir armazenamento do Windows Azure através do Azure PowerShell, tem de autenticar o seu ambiente de cliente com Azure através do Azure Active Directory ou a autenticação baseada em certificados. Para obter informações detalhadas, consulte o artigo tutorial [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) . Este guia utiliza a autenticação do Azure Active Directory.

1.  No Windows PowerShell ISE, escreva o seguinte comando para adicionar o seu Azure conta para o ambiente do PowerShell local:

    `Add-AzureAccount`

2.  Na janela do "Início de sessão ao Microsoft Azure", escreva o endereço de e-mail e palavra-passe associada à sua conta. Azure autentica e guarda as informações de credenciais e, em seguida, fecha a janela.

3.  Em seguida, execute o seguinte comando para ver as contas do Azure no seu ambiente do PowerShell local e verifique se a sua conta está listada:

    `Get-AzureAccount`

4.  Em seguida, execute o cmdlet seguinte para ver todas as subscrições que estão ligadas à sessão do PowerShell local e verifique se está listada a sua subscrição:

    `Get-AzureSubscription | Format-Table SubscriptionName, IsDefault, IsCurrent, CurrentStorageAccountName`

5.  Para predefinir uma subscrição do Azure, execute o cmdlet AzureSubscription selecione:

        $SubscriptionName = 'Your subscription Name'
        Select-AzureSubscription -SubscriptionName $SubscriptionName –Default

6.  Verificar o nome da subscrição predefinido ao executar o cmdlet Get-AzureSubscription:

    `Get-AzureSubscription -Default`

7.  Para ver todos os cmdlets do PowerShell disponíveis para o armazenamento do Windows Azure, execute:

    `Get-Command -Module Azure -Noun *Storage*`

### <a name="how-to-create-a-new-azure-storage-account"></a>Como criar uma nova conta de armazenamento Azure
Para utilizar o armazenamento Azure, terá uma conta de armazenamento. Pode criar uma nova conta de armazenamento Azure depois de ter configurado o seu computador para ligar à sua subscrição.

1.  Execute o cmdlet Get-AzureLocation para localizar todas as localizações do Centro de dados disponíveis:

    `Get-AzureLocation | Format-Table -Property Name, AvailableServices, StorageAccountTypes`

2.  Em seguida, execute o cmdlet de AzureStorageAccount novo para criar uma nova conta de armazenamento. O exemplo seguinte cria uma nova conta de armazenamento no Centro de dados "US Ocidental".

        $location = "West US"
        $StorageAccountName = "yourstorageaccount"
        New-AzureStorageAccount –StorageAccountName $StorageAccountName -Location $location

> [AZURE.IMPORTANT] O nome da sua conta de armazenamento tem de ser exclusivo Azure e tem de estar em minúsculo. Convenções de nomenclatura e restrições, consulte o artigo [Sobre contas de armazenamento do Azure](storage-create-storage-account.md) e de [atribuição de nomes e referenciar contentores, Blobs e metadados](http://msdn.microsoft.com/library/azure/dd135715.aspx).

### <a name="how-to-set-a-default-azure-storage-account"></a>Como configurar uma conta de armazenamento Azure predefinida
Pode ter várias contas de armazenamento na sua subscrição. Pode escolher um deles e defini-lo como a conta de armazenamento predefinida para todos os comandos de armazenamento na mesma sessão PowerShell. Permite-lhe executar os comandos do Azure PowerShell armazenamento sem especificar explicitamente o contexto de armazenamento.

1.  Para configurar uma conta de armazenamento de predefinido para a sua subscrição, pode executar o cmdlet Set-AzureSubscription.

        $SubscriptionName = "Your subscription name"
        $StorageAccountName = "yourstorageaccount"  
        Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName

2.  Em seguida, execute o cmdlet de obter AzureSubscription para verificar se a conta de armazenamento é associada com a sua conta de subscrição predefinido. Este comando devolve as propriedades da subscrição da subscrição atual, incluindo a sua conta de armazenamento atual.

        Get-AzureSubscription –Current

### <a name="how-to-list-all-azure-storage-accounts-in-a-subscription"></a>Como listar todas as contas de armazenamento Azure uma subscrição
Cada subscrição Azure pode ter até 100 contas de armazenamento. Para obter informações mais atualizadas sobre limites, consulte o artigo [Azure subscrição e limites de serviço, Quotas e as restrições](../azure-subscription-service-limits.md).

Execute o cmdlet seguinte para descobrir o nome e o estado das contas de armazenamento na subscrição atual:

    Get-AzureStorageAccount | Format-Table -Property StorageAccountName, Location, AccountType, StorageAccountStatus

### <a name="how-to-create-an-azure-storage-context"></a>Como criar um contexto de armazenamento Azure
Contexto de armazenamento Azure é um objeto no PowerShell para incorporar as credenciais de armazenamento. Utilizar um contexto de armazenamento enquanto a executar o cmdlet qualquer subsequente permite-lhe autenticar o pedido sem especificar a conta de armazenamento e a sua chave de acesso explicitamente. Pode criar um contexto de armazenamento de várias formas, tais como utilizando chave de acesso e nome de conta de armazenamento, token de acesso partilhada de assinatura (SA), a cadeia de ligação, ou anónimos. Para mais informações, consulte o artigo [AzureStorageContext novo](http://msdn.microsoft.com/library/azure/dn806380.aspx).  

Utilize uma das três seguintes formas para criar um contexto de armazenamento:

- Execute o cmdlet [Get-AzureStorageKey](http://msdn.microsoft.com/library/azure/dn495235.aspx) para descobrir a tecla de acesso de armazenamento principal para a sua conta de armazenamento Azure. Em seguida, ligue para o cmdlet [AzureStorageContext novo](http://msdn.microsoft.com/library/azure/dn806380.aspx) para criar um contexto de armazenamento:

        $StorageAccountName = "yourstorageaccount"
        $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
        $Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary


- Gerar um token de assinatura acesso partilhado um contentor de armazenamento Azure e utilizá-la para criar um contexto de armazenamento:

        $sasToken = New-AzureStorageContainerSASToken -Container abc -Permission rl
        $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -SasToken $sasToken

    Para mais informações, consulte o artigo [AzureStorageContainerSASToken novo](http://msdn.microsoft.com/library/azure/dn806416.aspx) e [Utilizar partilhado acesso assinaturas (SA)](storage-dotnet-shared-access-signature-part-1.md).

- Em alguns casos, poderá querer especificar os pontos finais de serviço quando cria um novo contexto de armazenamento. Poderá ser necessário quando registou o nome de domínio personalizado para a sua conta de armazenamento com o serviço de BLOBs ou que pretende utilizar uma assinatura de acesso partilhado para aceder a recursos de armazenamento. Defina os pontos finais de serviço de uma cadeia de ligação e utilizá-la para criar um novo contexto de armazenamento, conforme apresentado abaixo:

        $ConnectionString = "DefaultEndpointsProtocol=http;BlobEndpoint=<blobEndpoint>;QueueEndpoint=<QueueEndpoint>;TableEndpoint=<TableEndpoint>;AccountName=<AccountName>;AccountKey=<AccountKey>"
        $Ctx = New-AzureStorageContext -ConnectionString $ConnectionString

Para mais informações sobre como configurar uma cadeia de ligação de armazenamento, consulte o artigo [Configurar cadeias de ligação](storage-configure-connection-string.md).

Agora que tem de configurar o seu computador e aprendeu como gerir contas de armazenamento através do PowerShell do Azure e subscrições, vá para a secção seguinte para obter informações sobre como gerir blobs do Azure e blob instantâneos.

### <a name="how-to-retrieve-and-regenerate-azure-storage-keys"></a>Como obter e gerar chaves de armazenamento Azure

Uma conta de armazenamento do Windows Azure vem com duas chaves de conta. Pode utilizar o cmdlet seguinte para obter as suas chaves.

    Get-AzureStorageKey -StorageAccountName "yourstorageaccount"

Utilize o cmdlet seguinte para obter uma chave específica. Valores válidos são primário e secundário.  

    (Get-AzureStorageKey -StorageAccountName $StorageAccountName).Primary

    (Get-AzureStorageKey -StorageAccountName $StorageAccountName).Secondary

Se pretender para gerar a suas chaves, utilize o cmdlet seguinte. Valores válidos para - KeyType são "Principal" e "Secundários"

    New-AzureStorageKey -StorageAccountName $StorageAccountName -KeyType “Primary”

    New-AzureStorageKey -StorageAccountName $StorageAccountName -KeyType “Secondary”

## <a name="how-to-manage-azure-blobs"></a>Como gerir blobs do Azure
Armazenamento de Blobs do Azure é um serviço para armazenar grandes quantidades de dados não estruturados, como texto ou dados binários, que podem ser acedidos a partir de qualquer parte do mundo através de HTTP ou HTTPS. Esta secção assume que já está familiarizado com os conceitos de serviço de armazenamento de Blobs do Azure. Para obter informações detalhadas, consulte o artigo [Introdução ao armazenamento Blob do utilizando o .NET](storage-dotnet-how-to-use-blobs.md) e [Blob conceitos de serviço](http://msdn.microsoft.com/library/azure/dd179376.aspx).

### <a name="how-to-create-a-container"></a>Como criar um contentor
Cada blob no Azure armazenamento tem de ser um contentor. Pode criar um contentor privado utilizando o cmdlet AzureStorageContainer novo:

    $StorageContainerName = "yourcontainername"
    New-AzureStorageContainer -Name $StorageContainerName -Permission Off

> [AZURE.NOTE] Existem três níveis de acesso de leitura anónimo: **desativar**, **Blob**e **contentor**. Para impedir o acesso anónimo blobs, defina o parâmetro de permissão para **desativar**. Por predefinição, o novo contentor for privado e pode ser acedido apenas pelo proprietário da conta. Para permitir que o público leitura acesso anónimo para recursos de BLOBs, mas não contentor metadados ou para a lista de blobs no contentor, defina o parâmetro de permissão para um **Blob**. Para permitir que o acesso de leitura completa público blob recursos, contentor metadados e a lista de blobs no contentor, defina o parâmetro de permissão ao **contentor**. Para mais informações, consulte o artigo [Gerir anónimo acesso de leitura contentores e blobs](storage-manage-access-to-resources.md).

### <a name="how-to-upload-a-blob-into-a-container"></a>Como carregar um blob para um contentor
Armazenamento de Blobs do Azure suporta bloco blobs e blobs de página. Para mais informações, consulte o artigo [Noções sobre bloco Blobs, acrescentar BLobs e Blobs de página](http://msdn.microsoft.com/library/azure/ee691964.aspx).

Para carregar blobs na um contentor, pode utilizar o cmdlet [Set-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806379.aspx) . Por predefinição, este comando carrega os ficheiros locais de um blob bloco. Para especificar o tipo de para o blob, pode utilizar o parâmetro - BlobType.

O exemplo seguinte é executado o cmdlet [Get-ChildItem](http://technet.microsoft.com/library/hh849800.aspx) para obter todos os ficheiros na pasta especificada e, em seguida, transmite-os para o seguinte cmdlet, utilizando o operador de tubagem. O cmdlet [Conjunto AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806379.aspx) os carregamentos pendentes os ficheiros locais para o contentor:

    Get-ChildItem –Path C:\Images\* | Set-AzureStorageBlobContent -Container "yourcontainername"

### <a name="how-to-download-blobs-from-a-container"></a>Como transferir blobs a partir de um contentor
O exemplo seguinte demonstra como transferir blobs a partir de um contentor. O exemplo pela primeira vez estabelece uma ligação para o armazenamento do Windows Azure utilizando o contexto de conta de armazenamento, que inclui o nome da conta de armazenamento e a respectiva chave primária de acesso. Em seguida, o exemplo obtém uma referência de BLOBs utilizando o cmdlet [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) . Em seguida, o exemplo que utiliza o cmdlet [Get-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806418.aspx) para transferir blobs para a pasta de destino local.

    #Define the variables.
    $ContainerName = "yourcontainername"
    $DestinationFolder = "C:\DownloadImages"

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #List all blobs in a container.
    $blobs = Get-AzureStorageBlob -Container $ContainerName -Context $Ctx

    #Download blobs from a container.
    New-Item -Path $DestinationFolder -ItemType Directory -Force
    $blobs | Get-AzureStorageBlobContent -Destination $DestinationFolder -Context $Ctx

### <a name="how-to-copy-blobs-from-one-storage-container-to-another"></a>Como copiar blobs de um contentor de armazenamento para outro
Pode copiar blobs modo assíncrono através de contas de armazenamento e regiões. O exemplo seguinte demonstra como copiar blobs a partir de um contentor de armazenamento para outra em duas contas de armazenamento diferente. O exemplo pela primeira vez define as variáveis para contas de armazenamento de origem e de destino e, em seguida, cria um contexto de armazenamento para cada conta. Em seguida, o exemplo copia blobs o contentor de origem para o contentor de destino utilizando o cmdlet [AzureStorageBlobCopy iniciar](http://msdn.microsoft.com/library/azure/dn806394.aspx) . O exemplo assume que as contas de armazenamento de origem e de destino e contentores já a existentes.

    #Define the source storage account and context.
    $SourceStorageAccountName = "yoursourcestorageaccount"
    $SourceStorageAccountKey = "Storage key for yoursourcestorageaccount"
    $SrcContainerName = "yoursrccontainername"
    $SourceContext = New-AzureStorageContext -StorageAccountName $SourceStorageAccountName -StorageAccountKey $SourceStorageAccountKey

    #Define the destination storage account and context.
    $DestStorageAccountName = "yourdeststorageaccount"
    $DestStorageAccountKey = "Storage key for yourdeststorageaccount"
    $DestContainerName = "destcontainername"
    $DestContext = New-AzureStorageContext -StorageAccountName $DestStorageAccountName -StorageAccountKey $DestStorageAccountKey

    #Get a reference to blobs in the source container.
    $blobs = Get-AzureStorageBlob -Container $SrcContainerName -Context $SourceContext

    #Copy blobs from one container to another.
    $blobs| Start-AzureStorageBlobCopy -DestContainer $DestContainerName -DestContext $DestContext

Tenha em atenção que este exemplo executa uma cópia assíncrona. Pode monitorizar o estado de cada cópia ao executar o cmdlet [Get-AzureStorageBlobCopyState](http://msdn.microsoft.com/library/azure/dn806406.aspx) .

### <a name="how-to-copy-blobs-from-a-secondary-location"></a>Como copiar blobs a partir de uma localização secundária
Pode copiar blobs desde a localização de uma conta com RT GRS ativado secundária.

    #define secondary storage context using a connection string constructed from secondary endpoints.
    $SrcContext = New-AzureStorageContext -ConnectionString "DefaultEndpointsProtocol=https;AccountName=***;AccountKey=***;BlobEndpoint=http://***-secondary.blob.core.windows.net;FileEndpoint=http://***-secondary.file.core.windows.net;QueueEndpoint=http://***-secondary.queue.core.windows.net; TableEndpoint=http://***-secondary.table.core.windows.net;"
    Start-AzureStorageBlobCopy –Container *** -Blob *** -Context $SrcContext –DestContainer *** -DestBlob *** -DestContext $DestContext

### <a name="how-to-delete-a-blob"></a>Como eliminar um blob
Para eliminar um blob, obtenha primeiro uma referência de BLOBs e, em seguida, ligar o cmdlet AzureStorageBlob remover. O exemplo seguinte elimina todos os blobs num determinado contentor. O exemplo pela primeira vez define as variáveis de uma conta de armazenamento e, em seguida, cria um contexto de armazenamento. Em seguida, o exemplo obtém uma referência de BLOBs utilizando o cmdlet [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) e executa o cmdlet [AzureStorageBlob remover](http://msdn.microsoft.com/library/azure/dn806399.aspx) para remover blobs de um contentor no Azure armazenamento.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $ContainerName = "containername"
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Get a reference to all the blobs in the container.
    $blobs = Get-AzureStorageBlob -Container $ContainerName -Context $Ctx

    #Delete blobs in a specified container.
    $blobs| Remove-AzureStorageBlob

## <a name="how-to-manage-azure-blob-snapshots"></a>Como gerir instantâneos de Blobs do Azure
Azure permite-lhe criar um instantâneo de um blob. Um instantâneo é a versão só de leitura de um blob que é disponibilizado num ponto no tempo. Assim que um instantâneo ter sido criado,-pode ser ler, copiado, ou eliminado, mas não modificado. Instantâneos fornecem uma maneira de fazer cópia de segurança um blob tal como é apresentado um momento. Para mais informações, consulte o artigo [criar um instantâneo de um Blob](http://msdn.microsoft.com/library/azure/hh488361.aspx).

### <a name="how-to-create-a-blob-snapshot"></a>Como criar um instantâneo de BLOBs
Para criar um snaphot de um blob, pela primeira vez obter uma referência de BLOBs e, em seguida, ligar a `ICloudBlob.CreateSnapshot` método no mesmo. O exemplo seguinte pela primeira vez define as variáveis de uma conta de armazenamento e, em seguida, cria um contexto de armazenamento. Em seguida, o exemplo obtém uma referência de BLOBs utilizando o cmdlet [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) e executa o método de [ICloudBlob.CreateSnapshot](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.icloudblob.aspx) para criar um instantâneo.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $ContainerName = "yourcontainername"
    $BlobName = "yourblobname"
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Get a reference to a blob.
    $blob = Get-AzureStorageBlob -Context $Ctx -Container $ContainerName -Blob $BlobName

    #Create a snapshot of the blob.
    $snap = $blob.ICloudBlob.CreateSnapshot()

### <a name="how-to-list-a-blobs-snapshots"></a>Como listar instantâneos um blob
Pode criar tantas instantâneos à medida que pretende para um blob. Pode listar os instantâneos associados à sua blob para controlar o seu instantâneos atuais. O exemplo seguinte utiliza um blob predefinido e chamadas do cmdlet [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) para listar os instantâneos desse blob.  

    #Define the blob name.
    $BlobName = "yourblobname"

    #List the snapshots of a blob.
    Get-AzureStorageBlob –Context $Ctx -Prefix $BlobName -Container $ContainerName  | Where-Object  { $_.ICloudBlob.IsSnapshot -and $_.Name -eq $BlobName }

### <a name="how-to-copy-a-snapshot-of-a-blob"></a>Como copiar um instantâneo de um blob
Pode copiar um instantâneo de um blob para restaurar o instantâneo. Para obter informações detalhadas e restrições, consulte o artigo [criar um instantâneo de um Blob](http://msdn.microsoft.com/library/azure/hh488361.aspx). O exemplo seguinte pela primeira vez define as variáveis de uma conta de armazenamento e, em seguida, cria um contexto de armazenamento. Em seguida, o exemplo define as variáveis de nome contentor e blob. O exemplo obtém uma referência de BLOBs utilizando o cmdlet [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) e executa o método de [ICloudBlob.CreateSnapshot](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.icloudblob.aspx) para criar um instantâneo. Em seguida, o exemplo executa o cmdlet [Iniciar AzureStorageBlobCopy](http://msdn.microsoft.com/library/azure/dn806394.aspx) para copiar o instantâneo de um blob utilizando o objeto de ICloudBlob para o blob de origem. Não se esqueça de atualizar as variáveis com base na sua configuração antes de executar o exemplo. Repare que o exemplo seguinte pressupõe que a origem e contentores de destino e o blob origem já a existentes.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Define the variables.
    $SrcContainerName = "yoursourcecontainername"
    $DestContainerName = "yourdestcontainername"
    $SrcBlobName = "yourblobname"
    $DestBlobName = "CopyBlobName"

    #Get a reference to a blob.
    $blob = Get-AzureStorageBlob -Context $Ctx -Container $SrcContainerName -Blob $SrcBlobName

    #Create a snapshot of a blob.
    $snap = $blob.ICloudBlob.CreateSnapshot()

    #Copy the snapshot to another container.
    Start-AzureStorageBlobCopy –Context $Ctx -ICloudBlob $snap -DestBlob $DestBlobName -DestContainer $DestContainerName

Agora que aprendeu como gerir blobs do Azure e blob instantâneos com PowerShell do Azure, vá para a secção seguinte para obter informações sobre como gerir tabelas, filas e ficheiros.

## <a name="how-to-manage-azure-tables-and-table-entities"></a>Como gerir tabelas Azure e entidades de tabela
Serviço de armazenamento de tabela Azure é um arquivo de dados NoSQL, que pode utilizar para armazenar e enormes conjuntos de dados estruturados e não relacionais de consulta. Principais componentes do serviço de estão tabelas, entidades e propriedades. Uma tabela é uma coleção de entidades. Uma entidade é um conjunto de propriedades. Cada entidade pode ter até 252 propriedades, o que são todos os pares valor do nome. Esta secção assume que já está familiarizado com os conceitos de serviço de armazenamento de tabela do Azure. Para obter informações detalhadas, consulte o artigo [Compreender o modelo de dados do serviço de tabela](http://msdn.microsoft.com/library/azure/dd179338.aspx) e [comece a trabalhar com o armazenamento de tabela do Azure utilizando o .NET](storage-dotnet-how-to-use-tables.md).

As seguintes subsecções, vai aprender a gerir o serviço de armazenamento de tabela do Azure através do Azure PowerShell. Os cenários abrangidos incluam **Criar**, **Eliminar**e **obtenção de** **tabelas**, bem como **Adicionar**, **consultar**e **eliminação de entidades de tabela**.

### <a name="how-to-create-a-table"></a>Como criar uma tabela
Todas as tabelas têm residem numa conta de armazenamento Azure. O exemplo seguinte demonstra como criar uma tabela no armazenamento do Windows Azure. O exemplo pela primeira vez estabelece uma ligação para o armazenamento do Windows Azure utilizando o contexto de conta de armazenamento, que inclui o nome da conta de armazenamento e a sua chave de acesso. Em seguida, utiliza o cmdlet [AzureStorageTable novo](http://msdn.microsoft.com/library/azure/dn806417.aspx) para criar uma tabela no armazenamento do Windows Azure.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Create a new table.
    $tabName = "yourtablename"
    New-AzureStorageTable –Name $tabName –Context $Ctx

### <a name="how-to-retrieve-a-table"></a>Como obter uma tabela
Pode consultar e obter uma ou todas as tabelas numa conta de armazenamento. O exemplo seguinte demonstra como obter uma tabela determinada utilizando o cmdlet [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) .

    #Retrieve a table.
    $tabName = "yourtablename"
    Get-AzureStorageTable –Name $tabName –Context $Ctx

Se chamar o cmdlet Get-AzureStorageTable sem quaisquer parâmetros, obtém todas as tabelas de armazenamento para uma conta de armazenamento.

### <a name="how-to-delete-a-table"></a>Como eliminar uma tabela
Pode eliminar uma tabela a partir de uma conta de armazenamento utilizando o cmdlet [AzureStorageTable remover](http://msdn.microsoft.com/library/azure/dn806393.aspx) .  

    #Delete a table.
    $tabName = "yourtablename"
    Remove-AzureStorageTable –Name $tabName –Context $Ctx

### <a name="how-to-manage-table-entities"></a>Como gerir entidades de tabela
Atualmente, Azure PowerShell não fornece cmdlets para gerir entidades de tabela diretamente. Para efetuar operações em entidades de tabela, pode utilizar as classes tendo em conta na [Biblioteca de cliente do Azure armazenamento para .NET](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx).

#### <a name="how-to-add-table-entities"></a>Como adicionar entidades de tabela
Para adicionar uma entidade a uma tabela, crie primeiro um objeto que define as propriedades de entidade. Uma entidade pode ter até 255 propriedades, incluindo 3 Propriedades do sistema: **PartitionKey**, **RowKey**e **data/hora**. É responsável para inserir e atualizar os valores de **PartitionKey** e **RowKey**. O servidor gere o valor de **data/hora**, que não podem ser modificadas. Em conjunto a **PartitionKey** e **RowKey** identificam exclusivamente cada entidade dentro de uma tabela.

-   **PartitionKey**: determina a partição armazenados na entidade.
-   **RowKey**: identifica exclusivamente a entidade dentro da partição.

Pode definir até 252 propriedades personalizadas para uma entidade. Para mais informações, consulte o artigo [Compreender o modelo de dados do serviço de tabela](http://msdn.microsoft.com/library/azure/dd179338.aspx).

O exemplo seguinte demonstra como adicionar entidades a uma tabela. O exemplo mostra como obter a tabela Empregados e adicionar várias entidades na mesma. Em primeiro lugar,-estabelece uma ligação para o armazenamento do Windows Azure utilizando o contexto de conta de armazenamento, que inclui o nome da conta de armazenamento e a sua chave de acesso. Em seguida, obtém a tabela determinada utilizando o cmdlet [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) . Se a tabela que se não existir, o cmdlet [Novo AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806417.aspx) é utilizado para criar uma tabela no armazenamento do Windows Azure. Em seguida, o exemplo define uma função personalizada entidade-adicionar para adicionar entidades para a tabela ao especificar partição de cada entidade e chave de linha. A função de adicionar entidade chamadas o cmdlet [Novo objeto](http://technet.microsoft.com/library/hh849885.aspx) na classe [Microsoft.WindowsAzure.Storage.Table.DynamicTableEntity](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.dynamictableentity.aspx) para criar um objeto de entidade. Mais tarde, o exemplo chama o método de [Microsoft.WindowsAzure.Storage.Table.TableOperation.Insert](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.insert.aspx) neste objecto entidade para adicioná-lo a uma tabela.

    #Function Add-Entity: Adds an employee entity to a table.
    function Add-Entity() {
        [CmdletBinding()]
        param(
           $table,
           [String]$partitionKey,
           [String]$rowKey,
           [String]$name,
           [Int]$id
        )

      $entity = New-Object -TypeName Microsoft.WindowsAzure.Storage.Table.DynamicTableEntity -ArgumentList $partitionKey, $rowKey
      $entity.Properties.Add("Name", $name)
      $entity.Properties.Add("ID", $id)

      $result = $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Insert($entity))
    }

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary
    $TableName = "Employees"

    #Retrieve the table if it already exists.
    $table = Get-AzureStorageTable –Name $TableName -Context $Ctx -ErrorAction Ignore

    #Create a new table if it does not exist.
    if ($table -eq $null)
    {
       $table = New-AzureStorageTable –Name $TableName -Context $Ctx
    }

    #Add multiple entities to a table.
    Add-Entity -Table $table -PartitionKey Partition1 -RowKey Row1 -Name Chris -Id 1
    Add-Entity -Table $table -PartitionKey Partition1 -RowKey Row2 -Name Jessie -Id 2
    Add-Entity -Table $table -PartitionKey Partition2 -RowKey Row1 -Name Christine -Id 3
    Add-Entity -Table $table -PartitionKey Partition2 -RowKey Row2 -Name Steven -Id 4

#### <a name="how-to-query-table-entities"></a>Como entidades de tabela de consulta
Para consultar uma tabela, utilize a classe de [Microsoft.WindowsAzure.Storage.Table.TableQuery](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tablequery.aspx) . O exemplo seguinte assume que já tiver executado o script tendo em conta na como para adicionar secção entidades deste guia. O exemplo pela primeira vez estabelece uma ligação para o armazenamento do Windows Azure utilizando o contexto de armazenamento, que inclui o nome da conta de armazenamento e a respectiva chave do access. Em seguida, tentará obter a tabela "Empregados" previamente criada utilizando o cmdlet [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) . Chamar o cmdlet [Novo objeto](http://technet.microsoft.com/library/hh849885.aspx) na classe Microsoft.WindowsAzure.Storage.Table.TableQuery cria um novo objeto de consulta. O exemplo procura as entidades que tenham uma coluna de 'ID' cujo valor é 1 conforme especificado num filtro de cadeia. Para obter informações detalhadas, consulte o artigo [consultar tabelas e entidades](http://msdn.microsoft.com/library/azure/dd894031.aspx). Quando executar esta consulta, esta devolverá todas as entidades que correspondem aos critérios de filtro.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary
    $TableName = "Employees"

    #Get a reference to a table.
    $table = Get-AzureStorageTable –Name $TableName -Context $Ctx

    #Create a table query.
    $query = New-Object Microsoft.WindowsAzure.Storage.Table.TableQuery

    #Define columns to select.
    $list = New-Object System.Collections.Generic.List[string]
    $list.Add("RowKey")
    $list.Add("ID")
    $list.Add("Name")

    #Set query details.
    $query.FilterString = "ID gt 0"
    $query.SelectColumns = $list
    $query.TakeCount = 20

    #Execute the query.
    $entities = $table.CloudTable.ExecuteQuery($query)

    #Display entity properties with the table format.
    $entities  | Format-Table PartitionKey, RowKey, @{ Label = "Name"; Expression={$_.Properties["Name"].StringValue}}, @{ Label = "ID"; Expression={$_.Properties["ID"].Int32Value}} -AutoSize

#### <a name="how-to-delete-table-entities"></a>Como eliminar entidades de tabela
Pode eliminar uma entidade utilizando suas chaves partição e linha. O exemplo seguinte assume que já tiver executado o script tendo em conta na como para adicionar secção entidades deste guia. O exemplo pela primeira vez estabelece uma ligação para o armazenamento do Windows Azure utilizando o contexto de armazenamento, que inclui o nome da conta de armazenamento e a sua chave de acesso. Em seguida, tentará obter a tabela "Colaboradores" previamente criada utilizando o cmdlet [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) . Se a tabela que se existir, o exemplo chama o método de [Microsoft.WindowsAzure.Storage.Table.TableOperation.Retrieve](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.retrieve.aspx) para obter uma entidade com base nos seus valores chaves partição e linha. Em seguida, passe a entidade para o método de [Microsoft.WindowsAzure.Storage.Table.TableOperation.Delete](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.delete.aspx) para eliminar.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary

    #Retrieve the table.
    $TableName = "Employees"
    $table = Get-AzureStorageTable -Name $TableName -Context $Ctx -ErrorAction Ignore

    #If the table exists, start deleting its entities.
    if ($table -ne $null) {
       #Together the PartitionKey and RowKey uniquely identify every  
       #entity within a table.
       $tableResult = $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Retrieve("Partition2", "Row1"))
       $entity = $tableResult.Result
    if ($entity -ne $null)
    {
       #Delete the entity.
       $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Delete($entity))
    }
    }

## <a name="how-to-manage-azure-queues-and-queue-messages"></a>Como gerir filas Azure e mensagens de fila de espera
Armazenamento de fila Azure é um serviço para armazenar grandes quantidades de mensagens que podem ser acedidas a partir de qualquer parte do mundo através de chamadas autenticadas utilizando HTTP ou HTTPS. Esta secção assume que já está familiarizado com os conceitos de serviço de armazenamento do Azure fila de espera. Para obter informações detalhadas, consulte o artigo [Introdução ao armazenamento de fila Azure utilizando o .NET](storage-dotnet-how-to-use-queues.md).

Esta secção mostrar-lhe como gerir o serviço de armazenamento de fila Azure através do Azure PowerShell. Os cenários abrangidos incluem **Inserir** e **Eliminar** mensagens de fila de espera, bem como **Criar**, **Eliminar**e **obter filas**.

### <a name="how-to-create-a-queue"></a>Como criar uma fila
O exemplo seguinte pela primeira vez estabelece uma ligação para o armazenamento do Windows Azure utilizando o contexto de conta de armazenamento, que inclui o nome da conta de armazenamento e a sua chave de acesso. Em seguida, chama cmdlet [AzureStorageQueue novo](http://msdn.microsoft.com/library/azure/dn806382.aspx) para criar uma fila 'NomeFila' com o nome.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary
    $QueueName = "queuename"
    $Queue = New-AzureStorageQueue –Name $QueueName -Context $Ctx

Para obter informações sobre convenções de nomenclatura Azure fila de serviço, consulte o artigo [atribuir nomes a filas e metadados](http://msdn.microsoft.com/library/azure/dd179349.aspx).

### <a name="how-to-retrieve-a-queue"></a>Como obter uma fila
Pode consultar e obter uma fila específica ou uma lista de todas as filas numa conta de armazenamento. O exemplo seguinte demonstra como obter uma fila especificada utilizando o cmdlet [Get-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806377.aspx) .

    #Retrieve a queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue –Name $QueueName –Context $Ctx

Se chamar o cmdlet [Get-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806377.aspx) sem quaisquer parâmetros, obtém a uma lista de todas as filas.

### <a name="how-to-delete-a-queue"></a>Como eliminar uma fila
Para eliminar uma fila e todas as mensagens contidas na mesma, contacte o cmdlet AzureStorageQueue remover. O exemplo seguinte mostra como eliminar uma fila especificada utilizando o cmdlet AzureStorageQueue remover.

    #Delete a queue.
    $QueueName = "yourqueuename"
    Remove-AzureStorageQueue –Name $QueueName –Context $Ctx

#### <a name="how-to-insert-a-message-into-a-queue"></a>Como inserir uma mensagem numa fila
Para inserir uma mensagem uma fila existente, primeiro crie uma nova instância da classe [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx) . Em seguida, chame o método de [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx) . Um CloudQueueMessage pode ser criado a partir de uma cadeia (em formato UTF-8) ou uma matriz de bytes.

O exemplo seguinte demonstra como adicionar a mensagem para uma fila. O exemplo pela primeira vez estabelece uma ligação para o armazenamento do Windows Azure utilizando o contexto de conta de armazenamento, que inclui o nome da conta de armazenamento e a sua chave de acesso. Em seguida, obtém a fila especificada utilizando o cmdlet [Get-AzureStorageQueue](https://msdn.microsoft.com/library/azure/dn806377.aspx) . Se não existir fila de espera, o cmdlet [Novo objeto](http://technet.microsoft.com/library/hh849885.aspx) é utilizado para criar uma instância da classe [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx) . Mais tarde, o exemplo chama o método de [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx) num objeto mensagem para adicioná-lo para uma fila. Aqui é um código que obtém uma fila e insere a mensagem «MessageInfo»:

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary

    #Retrieve the queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue -Name $QueueName -Context $ctx

    #If the queue exists, add a new message.
    if ($Queue -ne $null) {
       # Create a new message using a constructor of the CloudQueueMessage class.
       $QueueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage -ArgumentList MessageInfo

       #Add a new message to the queue.
       $Queue.CloudQueue.AddMessage($QueueMessage)
    }


#### <a name="how-to-de-queue-at-the-next-message"></a>Como retirar fila na mensagem seguinte
Anular o seu código filas uma mensagem a partir de uma fila em dois passos. Quando ligar para o método de [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.GetMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.getmessage.aspx) , receberá a mensagem seguinte numa fila. Uma mensagem devolvida por **GetMessage** torna-se invisível para qualquer outro código ler mensagens a partir desta fila. Para concluir a remoção da mensagem de fila de espera, também tem de chamar o método de [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.DeleteMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.deletemessage.aspx) . Este processo de dois passos de remoção de uma mensagem assegura que se o seu código falha processar uma mensagem devido a falha do software ou hardware, noutra instância do seu código pode aceder a mesma mensagem e tente novamente. O código chama **DeleteMessage** direita depois da mensagem foi processada.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary

    #Retrieve the queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue -Name $QueueName -Context $ctx

    $InvisibleTimeout = [System.TimeSpan]::FromSeconds(10)

    #Get the message object from the queue.
    $QueueMessage = $Queue.CloudQueue.GetMessage($InvisibleTimeout)
    #Delete the message.
    $Queue.CloudQueue.DeleteMessage($QueueMessage)

## <a name="how-to-manage-azure-file-shares-and-files"></a>Como gerir partilhas de ficheiros Azure e ficheiros
Armazenamento de ficheiros Azure oferece armazenamento partilhado para aplicações utilizando o protocolo SMB padrão. Máquinas virtuais do Microsoft Azure e serviços em nuvem que podem partilhar dados do ficheiro em componentes da aplicação através do partilhas montadas e aplicações de no local podem aceder a dados do ficheiro de uma partilha através da API do armazenamento de ficheiros ou Azure PowerShell.

Para mais informações sobre o armazenamento de ficheiros do Azure, consulte o artigo [Introdução ao armazenamento de ficheiros do Azure no Windows](storage-dotnet-how-to-use-files.md) e os [REST API do serviço de ficheiro](http://msdn.microsoft.com/library/azure/dn167006.aspx).

## <a name="how-to-set-and-query-storage-analytics"></a>Como configurar e análises de armazenamento de consultas
Pode utilizar [A análise de armazenamento do Azure](storage-analytics.md) para recolher métricas para as suas contas de armazenamento Azure e os dados do registo acerca dos pedidos de enviados para a conta de armazenamento. Pode utilizar métricas de armazenamento para monitorizar o estado de funcionamento de uma conta de armazenamento e registo de armazenamento para diagnosticar e resolução de problemas com a sua conta de armazenamento.
Por predefinição, métricas de armazenamento não estiver ativada para os seus serviços de armazenamento. Pode ativar a monitorização de utilizando o Portal do Azure ou o Windows PowerShell ou através de programação utilizando a biblioteca de cliente de armazenamento. Registo de armazenamento acontece lado do servidor e permite-lhe detalhes de registo para pedidos de efetuada com êxito e falhados na sua conta de armazenamento. Estes registos permitem-lhe ver os detalhes de ler, escrever e eliminar operações contra suas tabelas, filas e blobs, bem como os motivos para pedidos de falhados.

Para saber como ativar e visualizar dados de métricas de armazenamento através do PowerShell, consulte o artigo [como ativar métricas de armazenamento através do PowerShell](http://msdn.microsoft.com/library/azure/dn782843.aspx#HowtoenableStorageMetricsusingPowerShell).

Para saber como ativar e obter dados de armazenamento registo através do PowerShell, consulte o artigo [como ativar o registo de armazenamento através do PowerShell](http://msdn.microsoft.com/library/azure/dn782840.aspx#HowtoenableStorageLoggingusingPowerShell) e [localizar os registo de armazenamento os dados do registo](http://msdn.microsoft.com/library/azure/dn782840.aspx#FindingyourStorageLogginglogdata).
Para obter informações detalhadas sobre como utilizar métricas de armazenamento e registo de armazenamento para resolução de problemas de armazenamento, consulte o artigo [monitorização, diagnosticar e resolução de problemas de armazenamento do Windows Azure](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="how-to-manage-shared-access-signature-sas-and-stored-access-policy"></a>Como gerir assinatura partilhadas do Access (SA) e armazenados política de acesso
Acesso partilhado assinaturas são uma parte importante do modelo de segurança para qualquer aplicação utilizando o armazenamento do Windows Azure. São úteis para fornecer permissões limitadas à sua conta de armazenamento para clientes que não devem ter a chave de conta. Por predefinição, apenas o proprietário da conta de armazenamento poderá aceder blobs, tabelas e filas dentro dessa conta. Se o seu serviço ou aplicação tem de disponibilizar estes recursos para outros clientes sem partilhar a sua chave de acesso, tem três opções:

- Defina permissões de um contentor para permitir o acesso anónimo de leitura para o contentor e respetivos blobs. Não é permitida para tabelas ou filas.
- Utilize uma assinatura de acesso partilhado que concede restrita direitos de acesso para contentores, blobs, filas e tabelas para um intervalo de tempo específico.
- Utilize uma política de acesso armazenados para obter um nível adicional de controlo sobre assinaturas de acesso partilhado para um contentor ou o respetivos blobs, para uma fila ou para uma tabela. A política de acesso armazenados permite-lhe alterar a hora de início, hora de expiração ou as permissões para uma assinatura ou revogar a seguir tenha sido emitido.

Uma assinatura de acesso partilhado pode ser de uma das duas formas:

- **SA ad hoc**: Quando cria uma SA ad hoc, a hora de início, a hora de expiração, e as permissões para as SA estão especificadas no URI SA. Este tipo de SA pode ser criado num contentor, blob, tabela ou fila de espera e este é que não sejam revokable.
- **SA com política de acesso armazenado**: é definida uma política de acesso armazenados num contentor de recursos num contentor de BLOBs, tabela ou fila - e pode utilizá-la para gerir restrições para uma ou mais assinaturas acesso partilhado. Ao associar uma SA uma política de acesso armazenado, as associações de segurança herda as restrições - a hora de início, hora de expiração e permissões - definidas para a política de acesso armazenado. Este tipo de SA é revokable.

Para mais informações, consulte [Utilizar partilhado acesso assinaturas (SA)](storage-dotnet-shared-access-signature-part-1.md) e [Gerir anónimo acesso de leitura contentores e blobs](storage-manage-access-to-resources.md).

As secções seguintes, vai aprender a criar uma política de acesso armazenado e token de assinatura acesso partilhado para tabelas Azure. Azure PowerShell fornece cmdlets semelhantes para contentores, blobs e também filas. Para executar os scripts nesta secção, transfira o [Azure PowerShell versão 0.8.14](http://go.microsoft.com/?linkid=9811175&clcid=0x409) ou posterior.

### <a name="how-to-create-a-policy-based-shared-access-signature-token"></a>Como criar um token de partilhado assinatura de acesso baseado em políticas
Utilize o cmdlet AzureStorageTableStoredAccessPolicy novo para criar uma nova política de acesso armazenado. Em seguida, ligue para o cmdlet [AzureStorageTableSASToken novo](http://msdn.microsoft.com/library/azure/dn806400.aspx) para criar um novo token de acesso partilhado baseadas em política de assinatura para uma tabela de armazenamento do Windows Azure.

    $policy = "policy1"
    New-AzureStorageTableStoredAccessPolicy -Name $tableName -Policy $policy -Permission "rd" -StartTime "2015-01-01" -ExpiryTime "2016-01-01" -Context $Ctx
    New-AzureStorageTableSASToken -Name $tableName -Policy $policy -Context $Ctx

### <a name="how-to-create-an-ad-hoc-non-revokable-shared-access-signature-token"></a>Como criar um ad hoc (que não sejam revokable) partilhado acesso um token de assinatura
Utilize o cmdlet [AzureStorageTableSASToken novo](http://msdn.microsoft.com/library/azure/dn806400.aspx) para criar um novo ad hoc (que não sejam revokable) partilhado acesso token de assinatura para uma tabela de armazenamento do Windows Azure:

    New-AzureStorageTableSASToken -Name $tableName -Permission "rqud" -StartTime "2015-01-01" -ExpiryTime "2015-02-01" -Context $Ctx

### <a name="how-to-create-a-stored-access-policy"></a>Como criar uma política de acesso armazenado
Utilize o cmdlet AzureStorageTableStoredAccessPolicy novo para criar uma nova política de acesso armazenados para uma tabela de armazenamento do Windows Azure:

    $policy = "policy1"
    New-AzureStorageTableStoredAccessPolicy -Name $tableName -Policy $policy -Permission "rd" -StartTime "2015-01-01" -ExpiryTime "2016-01-01" -Context $Ctx

### <a name="how-to-update-a-stored-access-policy"></a>Como atualizar uma política de acesso armazenado
Utilize o cmdlet Set-AzureStorageTableStoredAccessPolicy para actualizar uma política de acesso armazenados existentes para uma tabela de armazenamento do Windows Azure:

    Set-AzureStorageTableStoredAccessPolicy -Policy $policy -Table $tableName -Permission "rd" -NoExpiryTime -NoStartTime -Context $Ctx

### <a name="how-to-delete-a-stored-access-policy"></a>Como eliminar uma política de acesso armazenado
Utilize o cmdlet AzureStorageTableStoredAccessPolicy remover para eliminar uma política de acesso armazenados numa tabela de armazenamento do Windows Azure:

    Remove-AzureStorageTableStoredAccessPolicy -Policy $policy -Table $tableName -Context $Ctx


## <a name="how-to-use-azure-storage-for-us-government-and-azure-china"></a>Como utilizar o armazenamento do Windows Azure para Governo dos Estados Unidos e Azure China
Um ambiente do Azure é uma implementação independente do Microsoft Azure, como o [Azure Governo para Governo dos Estados Unidos](https://azure.microsoft.com/features/gov/), [AzureCloud para global Azure](https://portal.azure.com)e [AzureChinaCloud para Azure operado pela 21Vianet na China](http://www.windowsazure.cn/). Pode implementar os novos ambientes Azure para administração pública de E.U.A e Azure China.

Para utilizar o armazenamento do Windows Azure com AzureChinaCloud, precisa de criar um contexto de armazenamento que está associado a AzureChinaCloud. Siga estes passos para começar a utilizar:

1.  Execute o cmdlet [Get-AzureEnvironment](https://msdn.microsoft.com/library/azure/dn790368.aspx) para ver os ambientes Azure disponíveis:

    `Get-AzureEnvironment`

2.  Adicione uma conta do Azure China ao Windows PowerShell:

    `Add-AzureAccount –Environment AzureChinaCloud`

3.  Crie um contexto de armazenamento para uma conta do AzureChinaCloud:

        $Ctx = New-AzureStorageContext -StorageAccountName $AccountName -StorageAccountKey $AccountKey> -Environment AzureChinaCloud

Para utilizar o armazenamento do Windows Azure com [Governo do Azure dos Estados Unidos](https://azure.microsoft.com/features/gov/), deve definem um novo ambiente e, em seguida, criar um novo contexto de armazenamento com este ambiente:

1.  Execute o cmdlet [Get-AzureEnvironment](https://msdn.microsoft.com/library/azure/dn790368.aspx) para ver os ambientes Azure disponíveis:

    `Get-AzureEnvironment`

2.  Adicione uma conta do Governo do Azure-ao Windows PowerShell:

    `Add-AzureAccount –Environment AzureUSGovernment`

3.  Crie um contexto de armazenamento para uma conta do AzureUSGovernment:

        $Ctx = New-AzureStorageContext -StorageAccountName $AccountName -StorageAccountKey $AccountKey> -Environment AzureUSGovernment

Para obter mais informações, consulte:

- [Guia de programador do Microsoft Azure administração pública](../azure-government-developer-guide.md).
- [Descrição geral das diferenças ao criar uma aplicação do serviço de China](https://msdn.microsoft.com/library/azure/dn578439.aspx)

## <a name="next-steps"></a>Próximos passos
Neste guia, aprendeu como gerir armazenamento do Windows Azure com PowerShell do Azure. Eis alguns artigos relacionados e recursos para saber mais sobre os mesmos.

- [Documentação do armazenamento Azure](https://azure.microsoft.com/documentation/services/storage/)
- [Cmdlets do PowerShell do armazenamento Azure](http://msdn.microsoft.com/library/azure/dn806401.aspx)
- [Referência do Windows PowerShell](https://msdn.microsoft.com/library/ms714469.aspx)

[Image1]: ./media/storage-powershell-guide-full/Subscription_currentportal.png
[Image2]: ./media/storage-powershell-guide-full/Subscription_Previewportal.png
[Image3]: ./media/storage-powershell-guide-full/Blobdownload.png


[Getting started with Azure Storage and PowerShell in 5 minutes]: #getstart
[Prerequisites for using Azure PowerShell with Azure Storage]: #pre
[How to manage storage accounts in Azure]: #manageaccount
[How to set a default Azure subscription]: #setdefsub
[How to create a new Azure storage account]: #createaccount
[How to set a default Azure storage account]: #defaultaccount
[How to list all Azure storage accounts in a subscription]: #listaccounts
[How to create an Azure storage context]: #createctx
[How to manage Azure blobs and blob snapshots]: #manageblobs
[How to create a container]: #container
[How to upload a blob into a container]: #uploadblob
[How to download blobs from a container]: #downblob
[How to copy blobs from one storage container to another]: #copyblob
[How to delete a blob]: #deleteblob
[How to manage Azure blob snapshots]: #manageshots
[How to create a blob snapshot]: #createshot
[How to list snapshots of a blob]: #listshot
[How to copy a snapshot of a blob]: #copyshot
[How to manage Azure tables and table entities]: #managetables
[How to create a table]: #createtable
[How to retrieve a table]: #gettable
[How to delete a table]: #remtable
[How to manage table entities]: #mngentity
[How to add table entities]: #addentity
[How to query table entities]: #queryentity
[How to delete table entities]: #deleteentity
[How to manage Azure queues and queue messages]: #managequeues
[How to create a queue]: #createqueue
[How to retrieve a queue]: #getqueue
[How to delete a queue]: #remqueue
[How to manage queue messages]: #mngqueuemsg
[How to insert a message into a queue]: #addqueuemsg
[How to de-queue at the next message]: #dequeuemsg
[How to manage Azure file shares and files]: #files
[How to set and query storage analytics]: #stganalytics
[How to manage Shared Access Signature (SAS) and Stored Access Policy]: #sas
[How to use Azure Storage for U.S. government and Azure China]: #gov
[Next Steps]: #next
