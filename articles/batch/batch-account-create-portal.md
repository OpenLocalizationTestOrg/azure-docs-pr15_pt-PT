<properties
    pageTitle="Criar uma conta Azure lote | Microsoft Azure"
    description="Saiba como criar uma conta Azure lote no portal do Azure para ser executado em grande escala das cargas de trabalho paralelas na nuvem"
    services="batch"
    documentationCenter=""
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.workload="big-compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/21/2016"
    ms.author="marsma"/>

# <a name="create-an-azure-batch-account-using-the-azure-portal"></a>Criar uma conta de lote Azure utilizando o portal do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](batch-account-create-portal.md)
- [Gestão de batch .NET](batch-management-dotnet.md)

Saiba como criar uma conta Azure lote no [portal do Azure][azure_portal]e onde encontrar importantes propriedades da conta, como as teclas de acesso e URLs de conta. Vamos também discutir lote preços e ligar uma conta de armazenamento do Windows Azure a sua conta do lote para que possa utilizar [pacotes de aplicações](batch-application-packages.md) e a [manter saída projecto e a tarefa](batch-task-output.md).

## <a name="create-a-batch-account"></a>Criar uma conta do lote

1. Inicie sessão no [portal do Azure][azure_portal].

2. Clique em **Novo** > **Calcular** > **lote serviço**.

    ![Lote no mercado][marketplace_portal]

3. A **Nova conta do lote** pá é apresentada. Consulte o artigo itens *um* através de *"e"* abaixo para as descrições de cada elemento pá.

    ![Criar uma conta do lote][account_portal]

    um. **Nome da conta**: um nome exclusivo para a sua conta do lote. Este nome tem de ser exclusivo dentro de uma região Azure que a conta estiver criada (ver a *localização* abaixo). -Pode conter apenas caracteres em minúsculas, números e tem de ser 3-24 carateres de comprimento.

    b. **Subscrição**: uma subscrição na qual pretende criar a conta do lote. Se tiver apenas uma subscrição, está selecionada por predefinição.

    c. **Grupo de recursos**: um recurso existente para a sua conta lote de grupo ou, opcionalmente, crie um novo.

    d. **Localização**: região An Azure na qual pretende criar a conta do lote. Apenas as regiões suportadas pela sua subscrição e o grupo de recursos são apresentadas como opções.

    "e". **Conta de armazenamento** (opcional): uma conta de armazenamento **Geral finalidade** associar (ligação) para a sua nova conta do lote. Consulte o artigo de [conta de armazenamento do Windows Azure ligadas](#linked-azure-storage-account) abaixo para obter mais detalhes.

4. Clique em **Criar** para criar a conta.

  O portal indica que é **Implementar** a conta e, após a conclusão, é apresentada uma notificação de **implementações foi concluída com êxito** em *notificações*.

## <a name="view-batch-account-properties"></a>Ver propriedades da conta do lote

Assim que a conta foi criada, pode abrir o **pá de conta do lote** para aceder às suas definições e propriedades. Pode aceder a todas as propriedades e as definições de conta ao utilizar o menu à esquerda da pá de conta lote.

![Pá de conta do lote no portal do Azure][account_blade]

* **URL de conta do lote**: aplicações que criar com o [desenvolvimento de lote APIs](batch-technical-overview.md#batch-development-apis) necessitam de um URL de conta para gerir recursos e executar tarefas na conta. Um URL de conta do lote tem o seguinte formato:

    `https://<account_name>.<region>.batch.azure.com`

![URL de conta do lote no portal][account_url]

* **Teclas de acesso**: suas aplicações também precisa de uma tecla de acesso ao trabalhar com recursos na sua conta do lote. Para ver ou gerar teclas de acesso da sua conta lote, introduza `keys` na caixa de **pesquisa** do menu à esquerda no pá de conta lote, em seguida, selecione **teclas**.

    ![Teclas de conta do lote no portal do Azure][account_keys]

## <a name="pricing"></a>Preços

Contas do lote são oferecidas apenas de uma "gratuito camada," o que significa que não é cobrados para a conta do lote. São cobradas para os recursos cluster Azure subjacentes que as soluções do lote consumam e para os recursos média consumidos por outros serviços quando executar a sua das cargas de trabalho. Por exemplo, lhe vai ser cobrada para os nós de cluster no seu agrupamentos de e para os dados que armazenou no Azure armazenamento como entrada ou saída para as suas tarefas. Da mesma forma, se utilizar a funcionalidade de [pacotes de aplicações](batch-application-packages.md) do lote, são cobrados para os recursos de armazenamento do Windows Azure utilizados para armazenar os pacotes de aplicações. Consulte o artigo [lote preços] [ batch_pricing] para obter mais informações.

## <a name="linked-azure-storage-account"></a>Conta de armazenamento do Windows Azure associada

Como mencionado anteriormente, pode ligar uma conta de armazenamento de **objectivo geral** (opcionalmente) à sua conta do lote. A funcionalidade de [pacotes de aplicações](batch-application-packages.md) do lote utiliza armazenamento de BLOBs num objetivo geral ligado conta de armazenamento, tal como é que a biblioteca [Lote ficheiro convenções .NET](batch-task-output.md) . Estas funcionalidades opcionais ajudam a implementar aplicações que executar as tarefas de lote e persistência os dados que produzam.

Batch atualmente suporta *apenas* o tipo de conta de armazenamento **Geral finalidade** conforme descrito no passo 5, [criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account), nas [contas do Azure sobre o armazenamento](../storage/storage-create-storage-account.md). Ao associar uma conta de armazenamento do Windows Azure à sua conta do lote, ser-se de que ligação *apenas* uma conta de armazenamento de **objectivo geral** .

![Criar uma conta de armazenamento "Finalidade geral"][storage_account]

Recomendamos que cria uma conta de armazenamento para utilizar em modo exclusivo pela sua conta do lote.

>[AZURE.WARNING] Tomar cuidado quando voltar a gerar as teclas de acesso de uma conta de armazenamento ligada. Gerar apenas uma chave de conta de armazenamento e clique em **Sincronizar teclas** na ligadas pá de conta de armazenamento. Aguarde cinco minutos para permitir que as teclas para serem propagadas para os nós de cluster de conjuntos de dados, em seguida, voltar a gerar e sincronizar a chave de outras se for necessário. Se gerar ambas as chaves ao mesmo tempo, os nós de cluster não conseguir sincronizar um dos chave e irão perder o acesso à conta de armazenamento.

  ![Voltar a gerar chaves de conta de armazenamento][4]

## <a name="batch-service-quotas-and-limits"></a>As quotas de serviço do lote e limites

Tenha em atenção que tal como acontece com a sua subscrição Azure e outros serviços do Azure, determinados [quotas e limites](batch-quota-limit.md) aplicam-se a contas do lote. As quotas de atuais de uma conta do lote aparecem no portal na conta de **Propriedades**.

![Quotas de conta do lote no portal do Azure][quotas]

Tenha estes quotas em mente à medida que é estruturar e das cargas de trabalho lote de dimensionamento. Por exemplo, se o conjunto de não está a atingir o número de destino de nós de cluster que especificou, poderá atingiu o limite de quota principais para a sua conta do lote.

Tenha em atenção que não está restrito para uma única conta de lote para a sua subscrição Azure. Pode executar várias lote das cargas de trabalho numa única conta de lote ou distribuir a sua das cargas de trabalho entre contas lote na mesma subscrição, mas em diferentes regiões Azure.

Muitos destas quotas podem ser aumentados simplesmente com um pedido de suporte do produto gratuitas submetido no portal do Azure. Consulte o artigo [Quotas e os limites para o serviço do Azure lote](batch-quota-limit.md) para obter detalhes sobre como pedir aumentos de quotas.

## <a name="other-batch-account-management-options"></a>Outras opções de gestão de conta do lote

Além de utilizar o portal do Azure, também pode criar e gerir contas de lote com o seguinte:

* [Cmdlets do PowerShell do lote](batch-powershell-cmdlets-get-started.md)
* [Clip Azure](../xplat-cli-install.md)
* [Gestão de batch .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Próximos passos

* Consulte o artigo [Descrição geral de funcionalidade do Azure lote](batch-api-basics.md) para saber mais sobre os conceitos de serviço do lote e funcionalidades. O artigo aborda os recursos lote principais como conjuntos de dados, cluster nós, tarefas e tarefas e fornece uma descrição geral das funcionalidades do serviço que permitir a execução de carga de trabalho em grande escala cluster.

* Conheça os princípios básicos de programação de uma aplicação com capacidade de lote utilizando a [biblioteca de cliente do lote .NET](batch-dotnet-get-started.md). O [artigo introdutório](batch-dotnet-get-started.md) orienta-o através de uma aplicação de trabalho que utiliza o serviço de lote para executar uma carga de trabalho em vários nós de cluster e inclui a utilizar o armazenamento do Windows Azure para transição de ficheiro de carga de trabalho e obtenção.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[4]: ./media/batch-account-create-portal/batch_acct_04.png "Voltar a gerar chaves de conta de armazenamento"
[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG
[account_url]: ./media/batch-account-create-portal/account_url.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[quotas]: ./media/batch-account-create-portal/quotas.png
