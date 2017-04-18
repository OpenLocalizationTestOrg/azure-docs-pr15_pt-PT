<properties
   pageTitle="Introdução ao Azure lote PowerShell | Microsoft Azure"
   description="Obter uma breve introdução aos cmdlets do Azure PowerShell que pode utilizar para gerir o serviço do Azure Batch"
   services="batch"
   documentationCenter=""
   authors="mmacy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="powershell"
   ms.workload="big-compute"
   ms.date="10/20/2016"
   ms.author="marsma"/>

# <a name="get-started-with-azure-batch-powershell-cmdlets"></a>Começar a trabalhar com os cmdlets do PowerShell do Azure Batch
Com os cmdlets do PowerShell do Azure lote, pode executar e o script de muitas das mesmas tarefas que realizar com as APIs lote, o portal do Azure e Azure Interface da linha de comandos (CLI). Esta é uma breve introdução aos cmdlets que pode utilizar para gerir as contas do lote e trabalhar com os recursos de lote como conjuntos de dados, tarefas e tarefas.

Para obter uma lista completa de lote cmdlets e sintaxe cmdlet detalhadas, consulte a [referência do cmdlet lote Azure](https://msdn.microsoft.com/library/azure/mt125957.aspx).

Este artigo é baseado os cmdlets no Azure PowerShell versão 3.0.0. Recomendamos que Atualize o seu PowerShell Azure com frequência para tirar partido das atualizações de serviço e os melhoramentos.

## <a name="prerequisites"></a>Pré-requisitos

Realizar as seguintes operações para utilizar o Azure PowerShell para gerir os seus recursos lote.

* [Instalar e configurar o PowerShell do Azure](../powershell-install-configure.md)

* Execute o cmdlet **AzureRmAccount de início de sessão** para ligar à sua subscrição (o envio de cmdlets de Azure lote no módulo Azure o Gestor de recursos):

    `Login-AzureRmAccount`

* **Registe-se com o espaço de nomes do lote fornecedor**. Esta operação só tem de ser executada **uma vez por subscrição**.

    `Register-AzureRMResourceProvider -ProviderNamespace Microsoft.Batch`

## <a name="manage-batch-accounts-and-keys"></a>Gerir contas de lote e chaves

### <a name="create-a-batch-account"></a>Criar uma conta do lote

**Novo AzureRmBatchAccount** cria uma conta do lote num grupo de recursos especificada. Se ainda não tiver um grupo de recursos, crie uma ao executar o cmdlet [AzureRmResourceGroup novo](https://msdn.microsoft.com/library/azure/mt603739.aspx) . Especificar uma das regiões Azure o parâmetro de **localização** , tal como "Central (EUA)". Por exemplo:

    New-AzureRmResourceGroup –Name MyBatchResourceGroup –location "Central US"

Em seguida, crie uma conta do lote no grupo de recursos, especificar um nome para a conta no <*nome_conta*> e a localização e o nome do seu grupo de recursos. Criar a conta do lote pode demorar algum tempo para concluir. Por exemplo:

    New-AzureRmBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName <res_group_name>

> [AZURE.NOTE] O nome da conta lote tem de ser exclusivo à região Azure, para o grupo de recursos, contêm entre carateres 3 e 24 e utilizar letras em minúsculas e apenas números.

### <a name="get-account-access-keys"></a>Obter as teclas de acesso de conta
**Get-AzureRmBatchAccountKeys** mostra as teclas de acesso associadas a uma conta Azure lote. Por exemplo, execute o seguinte para obter as principais e secundárias teclas da conta que criou.

    $Account = Get-AzureRmBatchAccountKeys –AccountName <account_name>

    $Account.PrimaryAccountKey

    $Account.SecondaryAccountKey

### <a name="generate-a-new-access-key"></a>Gerar uma nova chave de acesso
**Novo AzureRmBatchAccountKey** gera uma nova chave primária ou secundária conta para uma conta do Azure lote. Por exemplo, para gerar uma nova chave primária para a sua conta do lote, escreva:

    New-AzureRmBatchAccountKey -AccountName <account_name> -KeyType Primary

> [AZURE.NOTE] Para gerar uma nova chave secundária, especifique "Secundário" para o parâmetro **KeyType** . Tem de voltar a gerar as teclas principais e secundárias separadamente.

### <a name="delete-a-batch-account"></a>Eliminar uma conta do lote
**Remover AzureRmBatchAccount** elimina uma conta do lote. Por exemplo:

    Remove-AzureRmBatchAccount -AccountName <account_name>

Quando lhe for pedido, confirme que pretende remover a conta. Remoção da conta pode demorar algum tempo para concluir.

## <a name="create-a-batchaccountcontext-object"></a>Criar um objecto BatchAccountContext

Para autenticar utilizando os cmdlets do PowerShell lote quando criar e gerir conjuntos de dados, tarefas, tarefas e outros recursos lote, crie primeiro um objeto de BatchAccountContext para armazenar o seu nome de conta e chaves:

    $context = Get-AzureRmBatchAccountKeys -AccountName <account_name>

Passar o objeto BatchAccountContext para cmdlets que utilize o parâmetro **BatchContext** .

> [AZURE.NOTE] Por predefinição, a chave primária a conta é utilizada para autenticação, mas pode selecionar explicitamente a chave para utilizar ao alterar a propriedade do seu objecto BatchAccountContext **KeyInUse** : `$context.KeyInUse = "Secondary"`.

## <a name="create-and-modify-batch-resources"></a>Criar e modificar lote recursos
Utilize cmdlets como **Novo AzureBatchPool**, **Novo AzureBatchJob**e **AzureBatchTask novo** para criar recursos sob uma conta do lote. São correspondentes **Get -** e **Definir -** cmdlets para atualizar as propriedades dos recursos existentes e **Remover -** cmdlets para remover recursos sob uma conta do lote.

Quando utilizar muitas estes cmdlets, para além de prisma um objecto BatchContext, tem de criar ou passar objetos que contenham definições do recurso detalhadas, conforme mostrado no seguinte exemplo. Consulte a ajuda detalhada para cada cmdlet para obter exemplos adicionais.

### <a name="create-a-batch-pool"></a>Criar um conjunto de comandos

Quando criar ou atualizar um agrupamento de lote, selecione uma configuração do serviço na nuvem ou uma configuração de máquina virtual para o sistema operativo em nós cluster (consulte [Descrição geral do lote funcionalidade](batch-api-basics.md#pool)). Sua escolha determina se os nós de cluster são criada uma imagem com uma das [disponibilização de SO do Azure convidado](../cloud-services/cloud-services-guestos-update-matrix.md#releases) ou com uma das imagens Linux ou Windows VM suportadas no Azure Marketplace.

Quando executa **Novo AzureBatchPool**, passe as definições do sistema operativo num objeto PSCloudServiceConfiguration ou PSVirtualMachineConfiguration. Por exemplo, o cmdlet seguinte cria um novo conjunto de lote connosco de cluster pequenas tamanho na configuração do serviço de nuvem, criada uma imagem com a versão mais recente do sistema operativo da família 3 (Windows Server 2012). Aqui, o parâmetro **CloudServiceConfiguration** Especifica a variável de *$configuration* como o objeto PSCloudServiceConfiguration. O parâmetro **BatchContext** Especifica uma variável definido anteriormente *$context* como o objeto BatchAccountContext.

    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSCloudServiceConfiguration" -ArgumentList @(4,"*")

    New-AzureBatchPool -Id "AutoScalePool" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context

O número de destino de nós de cluster no conjunto de novo é determinado por uma fórmula de autoscaling. Neste caso, a fórmula é simplesmente **$TargetDedicated = 4**, que indica o número de nós de cluster no conjunto de é 4 no máximo.

## <a name="query-for-pools-jobs-tasks-and-other-details"></a>Consulta para conjuntos de dados, tarefas, tarefas e outros detalhes

Utilize cmdlets como **Obter AzureBatchPool**, **Get-AzureBatchJob**e **Obter AzureBatchTask** a consulta para entidades criadas sob uma conta do lote.

### <a name="query-for-data"></a>Consulta de dados

Por exemplo, utilize **Get-AzureBatchPools** para encontrar os agrupamentos de dados. Por predefinição esta consultas para todos os conjuntos de dados sob a sua conta, partindo do princípio de já armazenado no objeto BatchAccountContext na *$context*:

    Get-AzureBatchPool -BatchContext $context

### <a name="use-an-odata-filter"></a>Utilizar um filtro de OData

Pode fornecer um filtro de OData, utilizando o parâmetro de **filtro** para localizar apenas os objetos que lhe interessa. Por exemplo, pode encontrar todos os conjuntos de dados com ids a começar em "myPool":

    $filter = "startswith(id,'myPool')"

    Get-AzureBatchPool -Filter $filter -BatchContext $context

Este método não é tão flexível como utilizando "Onde-objeto" no pipeline local. No entanto, a consulta enviada para o serviço do lote diretamente para que todas as filtragens de acontece no lado do servidor, guardar a largura de banda da Internet.

### <a name="use-the-id-parameter"></a>Utilizar o parâmetro Id

Uma alternativa a um filtro de OData é utilizar o parâmetro **Id** . Para consultar um conjunto específico com id "myPool":

    Get-AzureBatchPool -Id "myPool" -BatchContext $context

O parâmetro de **Id** suporta apenas a pesquisa de id do total, não carateres universais ou filtros de estilo de OData.

### <a name="use-the-maxcount-parameter"></a>Utilizar o parâmetro MaxCount

Por predefinição, cada cmdlet devolve um máximo de objetos de 1000. Se atingir este limite, otimizar o seu filtro para trazer novamente menos objetos ou definidos explicitamente um máximo de utilizar o parâmetro **MaxCount** . Por exemplo:

    Get-AzureBatchTask -MaxCount 2500 -BatchContext $context

Para remover o limite do canto superior, defina **MaxCount** ou inferior a 0.

### <a name="use-the-powershell-pipeline"></a>Utilizar o PowerShell pipeline

Cmdlets do lote pode tirar partido de tubagem para enviar dados entre os cmdlets do PowerShell. Tem o mesmo efeito que especificar um parâmetro, mas faz com que trabalhar com várias entidades mais fácil.

Por exemplo, localizar e apresentar todas as tarefas sob a sua conta:

    Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context

Reiniciar (reinicialização) todos os nós de cluster num conjunto de dados:

    Get-AzureBatchComputeNode -PoolId "myPool" -BatchContext $context | Restart-AzureBatchComputeNode -BatchContext $context

## <a name="application-package-management"></a>Gestão de pacote de aplicações

Pacotes de aplicações fornecem uma maneira simplificada de implementação de aplicações para os nós de cluster no seu conjuntos de dados. Com os cmdlets do PowerShell lote, pode carregar e gerir pacotes de aplicações na sua conta do lote e implementar versões de pacote para calcular nós.

**Criar** uma aplicação:

    New-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

**Adicionar** um pacote de aplicações:

    New-AzureRmBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0" -Format zip -FilePath package001.zip

Defina a **versão predefinida** para a aplicação:

    Set-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -DefaultVersion "1.0"

**Lista de** pacotes de uma aplicação

    $application = Get-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

    $application.ApplicationPackages

**Eliminar** um pacote de aplicações

    Remove-AzureRmBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0"

**Eliminar** uma aplicação

    Remove-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

>[AZURE.NOTE] Tem de eliminar todas as versões de pacote de aplicação de uma aplicação antes de eliminar a aplicação. Vai receber um erro de 'Conflito' se tentar eliminar uma aplicação que tem atualmente pacotes de aplicações.

### <a name="deploy-an-application-package"></a>Implementar um pacote de aplicações

Pode especificar um ou mais pacotes de aplicação para implementação quando cria um conjunto de dados. Quando especificar um pacote na hora de criação de conjunto de dados, é implementada para cada nó como o conjunto de associações nó. Pacotes também são implementados quando um nó é reiniciado ou reimaged.

Especificar o `-ApplicationPackageReference` opção ao criar um agrupamento para implementar um pacote de aplicações nós do conjunto que participar o agrupamento. Em primeiro lugar, crie um objecto **PSApplicationPackageReference** e configurá-lo com a versão de Id e pacote de aplicação que pretende implementar a nós de cluster o agrupamento:

    $appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

    $appPackageReference.ApplicationId = "MyBatchApplication"

    $appPackageReference.Version = "1.0"

Agora, crie o agrupamento e especifique o objeto de referência do pacote, como o argumento do `ApplicationPackageReferences` opção:

    New-AzureBatchPool -Id "PoolWithAppPackage" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -BatchContext $context -ApplicationPackageReferences $appPackageReference

Pode encontrar mais informações sobre pacotes de aplicações na [implementação de aplicações com o Azure lote pacotes de aplicações](batch-application-packages.md).

>[AZURE.IMPORTANT] [Ligação de uma conta de armazenamento do Windows Azure](#linked-storage-account-autostorage) , tem à sua conta do lote para utilizar os pacotes de aplicações.

### <a name="update-a-pools-application-packages"></a>Pacotes de aplicação de um conjunto de actualização

Para atualizar as aplicações atribuídas a um conjunto existente, crie um objeto de PSApplicationPackageReference com as propriedades pretendidas (Id de igual e pacote versão da aplicação):

    $appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

    $appPackageReference.ApplicationId = "MyBatchApplication"

    $appPackageReference.Version = "2.0"

Em seguida, obter o conjunto de lote, desmarque saída quaisquer pacotes existentes, adicionar a nossa nova referência de pacote e actualizar o serviço de lote com as novas definições de conjunto de dados:

    $pool = Get-AzureBatchPool -BatchContext $context -Id "PoolWithAppPackage"

    $pool.ApplicationPackageReferences.Clear()

    $pool.ApplicationPackageReferences.Add($appPackageReference)

    Set-AzureBatchPool -BatchContext $context -Pool $pool

Se atualizou agora propriedades o agrupamento no serviço lote. Para implementar realmente o novo pacote de aplicação para calcular nós no conjunto, no entanto, tem de reiniciar o ou reimage em nós. Pode reiniciar todos os nós num conjunto de dados com este comando:

    Get-AzureBatchComputeNode -PoolId "PoolWithAppPackage" -BatchContext $context | Restart-AzureBatchComputeNode -BatchContext $context

>[AZURE.TIP] Pode implementar os vários pacotes de aplicações para os nós de cluster num conjunto de dados. Se pretender para *Adicionar* um pacote de aplicações em vez de substituir os pacotes atualmente implementados, omita a `$pool.ApplicationPackageReferences.Clear()` linha de cima.

## <a name="next-steps"></a>Próximos passos

* Para e exemplos de sintaxe de cmdlet detalhadas, consulte o artigo [referência do cmdlet lote Azure](https://msdn.microsoft.com/library/azure/mt125957.aspx).

* Para mais informações sobre aplicações e pacotes de aplicações no lote, consulte o artigo [implementação de aplicações com o Azure lote pacotes de aplicações](batch-application-packages.md).
