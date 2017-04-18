<properties
    pageTitle="Instalação da aplicação fácil e gestão no Azure lote | Microsoft Azure"
    description="Utilizar a funcionalidade de pacotes de aplicações do Azure lote para gerir facilmente várias aplicações e versões instalação no lote calcular nós."
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor="" />

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="big-compute"
    ms.date="10/21/2016"
    ms.author="marsma" />

# <a name="application-deployment-with-azure-batch-application-packages"></a>Implementação da aplicação com pacotes de aplicações do Azure Batch

A funcionalidade de pacotes de aplicações do Azure lote fornece fácil gestão das aplicações de tarefa e a sua implementação para os nós de cluster no seu conjunto. Com os pacotes de aplicações, pode carregar e gerir várias versões das aplicações do que executar as tarefas, incluindo os seus ficheiros de suporte. Pode, em seguida, automaticamente implementar um ou mais destas aplicações para os nós de cluster no seu conjunto.

Este artigo vai aprender carregar e gerir pacotes de aplicações no portal do Azure. Irá, em seguida, Aprenda a instalá-las em nós de cluster de um conjunto com o [Lote .NET] [ api_net] biblioteca.

> [AZURE.NOTE] A funcionalidade de pacotes de aplicação descrita aqui substitui a funcionalidade de "Lote aplicações" disponível em versões anteriores do serviço.

## <a name="application-package-requirements"></a>Requisitos de pacote de aplicação

[Ligação de uma conta de armazenamento do Windows Azure](#link-a-storage-account) , tem à sua conta do lote para utilizar os pacotes de aplicações.

A funcionalidade de pacotes de aplicação outros fabricantes referida neste artigo compatíveis está *apenas* com agrupamentos de lote que foram criadas após 10 de Março de 2016. Pacotes de aplicações não serão implementadas para calcular nós nos conjuntos de dados criados antes dessa data.

Esta funcionalidade foi introduzida numa [Lote REST API] [ api_rest] versão 2015-12-01.2.2 e o correspondente [Lote .NET] [ api_net] versão 3.1.0 da biblioteca. Recomendamos que utilize sempre a versão mais recente do API quando trabalhar com lote.

> [AZURE.IMPORTANT] Atualmente, só *CloudServiceConfiguration* agrupamentos suportam pacotes de aplicações. Não é possível utilizar pacotes de aplicações conjuntos de dados criados utilizando VirtualMachineConfiguration imagens. Consulte a secção de [configuração de Máquina Virtual](batch-linux-nodes.md#virtual-machine-configuration) de [aprovisionar Linux calcular nós no Azure lote conjuntos de dados](batch-linux-nodes.md) para obter mais informações sobre as duas configurações diferentes.

## <a name="about-applications-and-application-packages"></a>Sobre aplicações e pacotes de aplicações

Dentro de lote Azure, uma *aplicação* que se refere a um conjunto de versões binários que podem ser transferidos automaticamente os nós de cluster no seu conjunto. Um *pacote de aplicações* que se refere a um *conjunto específico* dessas binários e representa um determinado *versão* da aplicação.

![Diagrama de alto nível de aplicações e pacotes de aplicações][1]

### <a name="applications"></a>Aplicações

Uma aplicação no lote contém um ou mais aplicação pacotes e especifica as opções de configuração para a aplicação. Por exemplo, uma aplicação pode especificar a versão do pacote de aplicação predefinida instalar em nós cluster e se os pacotes podem ser atualizados ou eliminados.

### <a name="application-packages"></a>Pacotes de aplicações

Um pacote de aplicações é um ficheiro. zip que contém os binários da aplicação e ficheiros de suporte que são necessários para execução pelas suas tarefas. Cada pacote de aplicação representa uma versão específica da aplicação.

Pode especificar pacotes de aplicações no nível de agrupamento e a tarefa. Pode especificar um ou mais dos seguintes pacotes e (opcionalmente) uma versão quando cria um conjunto de dados ou a tarefa.

* **Pacotes de aplicações do conjunto de dados** são implementados para *cada* nó no conjunto. As aplicações são implementadas quando um nó adere um conjunto de dados e, quando for reiniciado ou reimaged.

    Pacotes de aplicações do conjunto de dados são adequados quando todos os nós num conjunto de dados executam tarefas de uma tarefa. Pode especificar um ou mais pacotes de aplicações quando cria um conjunto de dados e pode adicionar ou atualizar os pacotes de um conjunto existente. Se atualizar pacotes de aplicação de um conjunto existente, tem de reiniciar o seus nós para instalar o pacote de novo.

* **Pacotes de aplicações da tarefa** são implementados apenas para um nó de cluster agendado para ser executada uma tarefa, basta antes de executar a linha de comandos da tarefa. Se o pacote de aplicação especificado e a versão já estiver no nó, não é novamente implementada e é utilizado o pacote existente.

    Pacotes de aplicações da tarefa são úteis em ambientes de agrupamento partilhados, onde diferentes tarefas são executadas num conjunto de dados e o agrupamento não é eliminado quando uma tarefa é concluída. Se pretende que a tarefa tem menos tarefas que nós no conjunto, pacotes de aplicações da tarefa podem minimizar transferência de dados, uma vez que a sua aplicação é implementada apenas para os nós que executar tarefas.

    Outros cenários que beneficiam pacotes de aplicações da tarefa são tarefas que utilizam uma aplicação particularmente grande, mas para apenas um pequeno número de tarefas. Por exemplo, uma fase de pré-processamento ou uma tarefa de impressão em série, onde a aplicação de pré-processamento ou em série é pesado.

> [AZURE.IMPORTANT] Existem restrições quanto ao número de aplicações e pacotes de aplicações dentro de uma conta do lote, bem como o tamanho máximo da aplicação pacote. Consulte o artigo [Quotas e os limites para o serviço do Azure lote](batch-quota-limit.md) para obter detalhes sobre estes limites.

### <a name="benefits-of-application-packages"></a>Vantagens de pacotes de aplicações

Pacotes de aplicações podem simplificar o código na sua solução lote e reduzir o overhead necessário para gerir as aplicações que são executadas as suas tarefas.

Tarefa de início do seu conjunto não tem de especificar uma longa lista de ficheiros de recurso individual para instalar em nós. Não tem de gerir várias versões dos seus ficheiros de aplicação no armazenamento do Windows Azure, ou na sua nós manualmente. E, não precisa de se preocupar gerar [SA URLs](../storage/storage-dotnet-shared-access-signature-part-1.md) para fornecer acesso aos ficheiros na sua conta de armazenamento. Lote funciona em segundo plano com armazenamento do Windows Azure para armazenar os pacotes de aplicações e implementá-los para calcular nós.

## <a name="upload-and-manage-applications"></a>Carregar e gerir aplicações

Pode utilizar o [Azure portal] [ portal] ou a biblioteca de [Lote gestão .NET](batch-management-dotnet.md) para gerir os pacotes da aplicação na sua conta do lote. Nas secções alguns seguintes, podemos pela primeira vez associar uma conta de armazenamento, em seguida, discutir adição de aplicações e pacotes e geri-los com o portal.

### <a name="link-a-storage-account"></a>Associar uma conta de armazenamento

Para utilizar os pacotes de aplicações, tem primeiro de associar uma conta de armazenamento do Windows Azure à sua conta do lote. Se ainda não tiver configurado uma conta de armazenamento da sua conta do lote, o portal do Azure irá exibir um aviso a primeira vez que clique no mosaico de **aplicações** no pá a **conta do lote** .

> [AZURE.IMPORTANT] Batch atualmente suporta *apenas* o tipo de conta de armazenamento **Geral finalidade** conforme descrito no passo 5, [criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account), nas [contas do Azure sobre o armazenamento](../storage/storage-create-storage-account.md). Quando liga uma conta de armazenamento do Windows Azure à sua conta de lote, ligação *apenas* uma conta de armazenamento **Geral objetivo** .

![Sem aviso de conta configurada de armazenamento no portal do Azure][9]

O serviço de lote utiliza a conta associada de armazenamento para o armazenamento e a obtenção de pacotes de aplicações. Depois de se tenha ligado as duas contas, lote automaticamente pode implementar os pacotes guardados na conta de armazenamento ligada para os nós de cluster. Clique em **definições de conta de armazenamento** no pá **aviso** e, em seguida, clique em **Conta de armazenamento** no pá a **Conta de armazenamento** para ligar uma conta de armazenamento à sua conta do lote.

![Selecione pá de conta de armazenamento no portal do Azure][10]

Recomendamos que criar um armazenamento conta *especificamente* para utilização com a sua conta do lote e selecione-o aqui. Para obter detalhes sobre como criar uma conta de armazenamento, consulte o artigo "Criar uma conta de armazenamento" no [Azure sobre contas de armazenamento](../storage/storage-create-storage-account.md). Após ter criado uma conta de armazenamento, pode, em seguida, ligá-lo à sua conta do lote utilizando a **Conta de armazenamento** pá.

> [AZURE.WARNING] Uma vez que o lote utiliza armazenamento do Windows Azure para armazenar os pacotes de aplicação, é [cobrados como normal] [ storage_pricing] para os dados de Blobs do bloco. Certifique-se de que considere o tamanho e o número de pacotes de aplicações e periodicamente remover pacotes preteridas para minimizar o custo.

### <a name="view-current-applications"></a>Ver aplicações atuais

Para ver as aplicações na sua conta do lote, clique no item de menu **aplicações** no menu à esquerda enquanto vê a **conta do lote** pá.

![Mosaico de aplicações][2]

Esta ação abre a pá de **aplicações** :

![Lista de aplicações][3]

A **aplicações** pá apresenta o ID de cada aplicação na sua conta e as seguintes propriedades:

* **Pacotes**– o número de versões associados com esta aplicação.
* A **versão predefinida**– a versão que irão ser instalada se não especificar uma versão quando define a aplicação para um conjunto de dados. Esta definição é opcional.
* **Permitir atualizações**– o valor que especifica se o pacote de atualizações, eliminações e adições são permitidos. Se este estiver definido para **não**, o pacote de atualizações e eliminações são desativadas para a aplicação. Podem ser adicionadas apenas nova aplicação pacote as versões. A predefinição é **Sim**.

### <a name="view-application-details"></a>Ver detalhes de aplicação

Clique numa aplicação no pá a **aplicações** para abrir o pá que inclui os detalhes dessa aplicação.

![Detalhes da aplicação][4]

No pá de detalhes de aplicação, pode configurar as seguintes definições para a sua aplicação.

* **Permitir atualizações**– especificar se os pacotes de aplicações podem ser atualizados ou eliminados. Consulte o artigo "Atualizar ou eliminar um pacote de aplicações" mais adiante.
* **Versão predefinida**– especificar um pacote de aplicação predefinida para implementar para calcular nós.
* **Nome a apresentar**– especificar "amigável" atribuir um nome que o lote de solução pode utilizar quando apresenta informações sobre a aplicação, tal como na interface de utilizador de um serviço que fornece os seus clientes através do lote.

### <a name="add-a-new-application"></a>Adicionar uma nova aplicação

Para criar uma nova aplicação, adicione um pacote de aplicações e especifique um ID exclusivo, nova aplicação. O primeiro pacote de aplicação adicionar com o novo ID da aplicação também irá criar a nova aplicação.

Clique em **Adicionar** a pá **aplicações** para abrir o pá **nova aplicação** .

![Nova Pá de aplicação no portal do Azure][5]

A **nova aplicação** pá fornece os seguintes campos para especificar as definições dos seus nova aplicação e o pacote de aplicações.

**Id da aplicação**

Este campo especifica o ID da sua nova aplicação, está sujeito às regras de validação de ID do Azure lote padrão:

* Pode conter qualquer combinação de carateres alfanuméricos, incluindo hífenes e de sublinhado.
* Não pode conter mais do que 64 carateres.
* Tem de ser exclusivos dentro da conta do lote.
* É preservação de maiúsculas/minúsculas e caso.

**Versão**

Especifica a versão do pacote de aplicação que está a carregar. Cadeias de versão estão sujeitas as seguintes regras de validação:

* Pode conter qualquer combinação de carateres alfanuméricos, incluindo hífenes, sublinhado e períodos.
* Não pode conter mais do que 64 carateres.
* Tem de ser exclusivos dentro da aplicação.
* Maiúsculas/minúsculas preservação e maiúsculas e minúsculas.

**Pacote de aplicações**

Este campo especifica o ficheiro. zip que contém os binários da aplicação e ficheiros de suporte que são necessários para executar a aplicação. Clique em caixa de **selecionar um ficheiro** ou o ícone de pasta para procurar e selecione um ficheiro. zip que contém os ficheiros da sua aplicação.

Depois de selecionar um ficheiro, clique em **OK** para começar a carregar para o armazenamento do Windows Azure. Quando a operação de carregamento estiver concluída, será notificado e o pá irá fechar. Consoante o tamanho do ficheiro que está a carregar e a velocidade da sua ligação de rede, esta operação poderá demorar algum tempo.

> [AZURE.WARNING] Não feche a **nova aplicação** pá antes da operação de carregamento está concluída. Fazê-lo irá parar o processo de carregamento.

### <a name="add-a-new-application-package"></a>Adicionar um novo pacote de aplicação

Para adicionar uma nova versão do pacote de aplicação para uma aplicação existente, selecione uma aplicação no pá **aplicações** , clique em **pacotes**, em seguida, clique em **Adicionar** para abrir o pá **Adicionar pacote** .

![Adicionar pá do pacote de aplicação no portal do Azure][8]

Como pode ver, os campos corresponderem das pá a **nova aplicação** , mas a caixa **id da aplicação** está desactivada. Como fez para a nova aplicação, especifique a **versão** para o seu novo pacote, procure o ficheiro. zip do **pacote de aplicações** , em seguida, clique em **OK** para carregar o pacote.

### <a name="update-or-delete-an-application-package"></a>Atualizar ou eliminar um pacote de aplicações

Para atualizar ou eliminar um pacote de aplicação existente, abra o pá detalhes para a aplicação, clique em **pacotes** para abrir o pá **pacotes** , clique nas **reticências** na linha do pacote de aplicação que pretende modificar e selecione a ação que pretende executar.

![Atualizar ou eliminar pacote no portal do Azure][7]

**Actualização**

Quando clica em **atualização**, é apresentada pá o *pacote de atualizações* . Este pá é semelhante a pá *novo pacote de aplicação* , no entanto apenas o campo de seleção do pacote está ativado, permitindo-lhe especificar um novo ficheiro de postal para carregar.

![Pá do pacote de atualização no portal do Azure][11]

**Eliminar**

Quando clicar em **Eliminar**, lhe for pedido para confirmar a eliminação da versão do pacote e lote elimina o pacote de armazenamento do Windows Azure. Se eliminar a versão predefinida de uma aplicação, a definição de **versão predefinida** é removida para a aplicação.

![Eliminar aplicação][12]

## <a name="install-applications-on-compute-nodes"></a>Instalar aplicações em nós de cluster

Agora que visualizou como gerir pacotes de aplicações com o portal do Azure, pode falarmos sobre a implementá-los para calcular nós e executá-los com tarefas lote.

### <a name="install-pool-application-packages"></a>Instalar pacotes de aplicações do conjunto de dados

Para instalar um pacote de aplicações em todos os nós de cluster de um conjunto de dados, especifique uma ou mais aplicação pacote *referências* para o conjunto. Os pacotes da aplicação que especificou para um conjunto de dados são instalados em cada nó cluster quando esse nó adere o agrupamento e, quando o nó é reiniciado ou reimaged.

No lote .NET, especificar um ou mais [CloudPool][net_cloudpool]. [ApplicationPackageReferences] [net_cloudpool_pkgref] quando cria um novo conjunto de dados ou para um conjunto existente. O [ApplicationPackageReference] [ net_pkgref] classe especifica um ID da aplicação e versão instalar num agrupamento calcular nós.

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicated: "1",
        virtualMachineSize: "small",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

// Specify the application and version to install on the compute nodes
myCloudPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "litware",
        Version = "1.1001.2b" }
};

// Commit the pool so that it's created in the Batch service. As the nodes join
// the pool, the specified application package will be installed on each.
await myCloudPool.CommitAsync();
```

>[AZURE.IMPORTANT] Se uma implementação do pacote de aplicação falhar por qualquer motivo, o serviço do lote assinala o de nó [inutilizável][net_nodestate], e não existem tarefas serão agendadas para execução nesse nó. Neste caso, deverá **reiniciar** o nó para reiniciar a migração de implementação do pacote. Reiniciar o nó também ativa a agendamento de tarefas novamente no nó.

### <a name="install-task-application-packages"></a>Instalar pacotes de aplicações de tarefa

Semelhante a um conjunto de dados, pode especificar de pacote de aplicação *referências* para uma tarefa. Quando uma tarefa está agendada para ser executado num nó, o pacote é transferido e extraído antes de linha de comandos da tarefa é executada. Se já estiver instalado no nó um pacote especificado e versão, o pacote não é transferido e é utilizado o pacote existente.

Para instalar um pacote de aplicação de tarefa, configure a tarefa [CloudTask][net_cloudtask]. [ApplicationPackageReferences] [net_cloudtask_pkgref] propriedade:

```csharp
CloudTask task =
    new CloudTask(
        "litwaretask001",
        "cmd /c %AZ_BATCH_APP_PACKAGE_LITWARE%\\litware.exe -args -here");

task.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference
    {
        ApplicationId = "litware",
        Version = "1.1001.2b"
    }
};
```

## <a name="execute-the-installed-applications"></a>Executar as aplicações instaladas

Os pacotes especificados para um conjunto de dados ou de uma tarefa são transferidos e extraídos para um diretório com nome dentro de `AZ_BATCH_ROOT_DIR` do nó. Lote também cria uma variável de ambiente que contém o caminho para o diretório com nome. As linhas do comando tarefa utilize esta variável de ambiente ao referenciar a aplicação no nó. É a variável no seguinte formato:

`AZ_BATCH_APP_PACKAGE_APPLICATIONID#version`

`APPLICATIONID`e `version` são valores que correspondem à versão de aplicação de igual e pacote que especificou para implementação. Por exemplo, se especificado essa versão 2.7 da aplicação *blender* deve ser instalado, as linhas do comando tarefa utilizaria esta variável de ambiente para aceder aos seus ficheiros:

`AZ_BATCH_APP_PACKAGE_BLENDER#2.7`

Se especificar uma versão predefinida para uma aplicação, pode omitir o sufixo de versão. Por exemplo, se configurou "2.7" como a versão predefinida para aplicação *blender*, as tarefas podem fazer referência a seguinte variável de ambiente e executará versão 2.7:

`AZ_BATCH_APP_PACKAGE_BLENDER`

O fragmento de código seguinte mostra uma linha de comandos de tarefa de exemplo que inicia a versão predefinida da aplicação *blender* :

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [AZURE.TIP] Consulte o artigo [definições de ambiente de tarefas](batch-api-basics.md#environment-settings-for-tasks) na [Descrição geral de funcionalidade do lote](batch-api-basics.md) para mais informações sobre definições de ambiente de nó cluster.

## <a name="update-a-pools-application-packages"></a>Pacotes de aplicação de um conjunto de actualização

Se já tiver sido configurado um conjunto existente com um pacote de aplicações, pode especificar um novo pacote para o conjunto. Se especificar uma nova referência de pacote para um conjunto, aplicar seguintes:

* Todos os novos nós participar o agrupamento e qualquer nó existente que está reiniciado ou reimaged irão instalar o pacote recentemente especificado.
* Calcule nós que já estão no conjunto de quando atualiza as referências de pacote não instalar automaticamente o novo pacote de aplicação. Estes calcular nós tem de ser reiniciados ou reimaged para receber o novo pacote.
* Quando um novo pacote é implementado, as variáveis de ambiente criado refletem as novas referências de pacote de aplicação.

Neste exemplo, o agrupamento de existente tem a versão 2.7 da aplicação *blender* configurada como um dos seus [CloudPool][net_cloudpool]. [ApplicationPackageReferences] [net_cloudpool_pkgref]. Para atualizar nós do conjunto com a versão 2.76b, especifique um novo [ApplicationPackageReference] [ net_pkgref] com a nova versão e consolidar a alteração.

```csharp
string newVersion = "2.76b";
CloudPool boundPool = await batchClient.PoolOperations.GetPoolAsync("myPool");
boundPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "blender",
        Version = newVersion }
};
await boundPool.CommitAsync();
```

Agora que a nova versão ter sido configurada, qualquer *Novo* nó associa o agrupamento terão versão 2.76b implementado à mesma. Para instalar 2.76b em nós que sejam encontram *já* no conjunto, reinicie ou reimage-los. Tenha em atenção que nós reiniciado irão manter os ficheiros a partir de implementações do pacote anterior.

## <a name="list-the-applications-in-a-batch-account"></a>Listar as aplicações numa conta Batch

Pode listar as aplicações e os respetivos pacotes numa conta lote utilizando o [ApplicationOperations][net_appops]. [ListApplicationSummaries] [net_appops_listappsummaries] método.

```csharp
// List the applications and their application packages in the Batch account.
List<ApplicationSummary> applications = await batchClient.ApplicationOperations.ListApplicationSummaries().ToListAsync();
foreach (ApplicationSummary app in applications)
{
    Console.WriteLine("ID: {0} | Display Name: {1}", app.Id, app.DisplayName);

    foreach (string version in app.Versions)
    {
        Console.WriteLine("  {0}", version);
    }
}
```

## <a name="wrap-up"></a>Moldar

Com os pacotes de aplicações, pode ajudar os seus clientes, selecione as aplicações para o seu emprego e especifique a versão exata utilizar quando processar tarefas com o seu serviço com capacidade de lote. Também poderá fornecer a capacidade para os seus clientes carregar e controlar os seus próprios aplicações no seu serviço.

## <a name="next-steps"></a>Próximos passos

* O [Lote REST API] [ api_rest] também fornece suporte para trabalhar com os pacotes de aplicações. Por exemplo, consulte o artigo o [applicationPackageReferences] [ rest_add_pool_with_packages] elemento em [Adicionar um agrupamento de uma conta] [ rest_add_pool] para obter informações sobre como especificar pacotes instalar ao utilizar a API REST. Consulte [aplicações] [ rest_applications] para obter detalhes sobre como obter informações sobre a aplicação, utilizando o lote REST API.

* Saiba como através de programação [Gerir contas do Azure lote e quotas com o lote de gestão de .NET](batch-management-dotnet.md). O [Lote de gestão de .NET] [ api_net_mgmt] biblioteca pode ativar funcionalidades de criação e a eliminação de conta para a sua aplicação do lote ou serviço.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[github_samples]: https://github.com/Azure/azure-batch-samples
[storage_pricing]: https://azure.microsoft.com/pricing/details/storage/
[net_appops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.aspx
[net_appops_listappsummaries]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.listapplicationsummaries.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.applicationpackagereferences.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.aspx
[net_cloudtask_pkgref]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.applicationpackagereferences.aspx
[net_nodestate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.state.aspx
[net_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationpackagereference.aspx
[portal]: https://portal.azure.com
[rest_applications]: https://msdn.microsoft.com/library/azure/mt643945.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_pool_with_packages]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_apkgreference

[1]: ./media/batch-application-packages/app_pkg_01.png "Diagrama de alto nível de pacotes de aplicação"
[2]: ./media/batch-application-packages/app_pkg_02.png "Mosaico de aplicações no portal do Azure"
[3]: ./media/batch-application-packages/app_pkg_03.png "Pá de aplicações no portal do Azure"
[4]: ./media/batch-application-packages/app_pkg_04.png "Pá de detalhes de aplicação no portal do Azure"
[5]: ./media/batch-application-packages/app_pkg_05.png "Nova Pá de aplicação no portal do Azure"
[7]: ./media/batch-application-packages/app_pkg_07.png "Atualizar ou eliminar pacotes pendente no portal do Azure"
[8]: ./media/batch-application-packages/app_pkg_08.png "Nova pá do pacote de aplicação no portal do Azure"
[9]: ./media/batch-application-packages/app_pkg_09.png "Sem ligadas alerta de conta de armazenamento"
[10]: ./media/batch-application-packages/app_pkg_10.png "Selecione pá de conta de armazenamento no portal do Azure"
[11]: ./media/batch-application-packages/app_pkg_11.png "Pá do pacote de atualização no portal do Azure"
[12]: ./media/batch-application-packages/app_pkg_12.png "Eliminar a caixa de diálogo de confirmação de pacote no portal do Azure"
