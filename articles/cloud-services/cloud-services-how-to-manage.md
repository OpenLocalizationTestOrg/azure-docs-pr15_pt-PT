<properties 
    pageTitle="Tarefas de gestão comuns serviço de nuvem (clássico) | Microsoft Azure" 
    description="Saiba como gerir serviços em nuvem no portal do Azure clássico." 
    services="cloud-services" 
    documentationCenter="" 
    authors="Thraka" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/10/2016"
    ms.author="adegeo"/>





# <a name="how-to-manage-cloud-services"></a>Como gerir serviços em nuvem

> [AZURE.SELECTOR]
- [Portal do Azure](cloud-services-how-to-manage-portal.md)
- [Azure portal clássico](cloud-services-how-to-manage.md)

Na área do portal clássica Azure **Serviços em nuvem** , pode atualizar uma função de serviço ou uma implementação, promover uma implementação faseada produção, ligar recursos no seu serviço de nuvem para que o pode ver as dependências de recursos e dimensionar os recursos em conjunto e eliminar um serviço na nuvem ou uma implementação.


## <a name="how-to-update-a-cloud-service-role-or-deployment"></a>Como: atualizar uma função de serviço de nuvem ou implementação

Se precisar de atualizar o código da aplicação do seu serviço de nuvem, utilize a **Atualizar** na página **Serviços em nuvem** , de dashboard ou página de **instâncias** . Pode atualizar uma única função ou todas as funções. Terá de carregar um novo pacote de serviço e ficheiro de configuração de serviço.

1. No [portal clássica Azure](https://manage.windowsazure.com/), na página **Serviços em nuvem** , de dashboard ou **instâncias de** página, clique em **Atualizar**.

    ![UpdateDeployment](./media/cloud-services-how-to-manage/CloudServices_UpdateDeployment.png)

2. Na **etiqueta de implementação**, introduza um nome para identificar a implementação (por exemplo, mycloudservice4). Encontrará a etiqueta de implementação em **Guia de introdução** no dashboard.

3. No **pacote**, utilize a **Procurar** para carregar o ficheiro de pacote de serviço (.cspkg).

4. **Configuração**, utilize **Procurar** para carregar o ficheiro de configuração do serviço (.cscfg).

5. **Função**, selecione **todos os** se pretende atualizar todas as funções no serviço em nuvem. Para efetuar uma atualização única função, selecione a função que quer atualizar. Mesmo se selecionar uma função específica para atualizar, as atualizações no ficheiro de configuração de serviço são aplicadas a todas as funções.

6. Se a atualização mudarem o número de funções ou o tamanho de qualquer função, selecione a caixa de verificação **Permitir atualizar se tamanhos de papel ou o número de funções alterações** para ativar a atualização para continuar. 

    Tenha em atenção que se alterar o tamanho de uma função (ou seja, o tamanho de uma máquina de virtual que aloja a instância de uma função) ou o número de funções, cada instância de função (máquina virtual) tem de ser novamente imagens e quaisquer dados locais serão perdidos.

7. Se qualquer funções de serviço tem apenas uma instância de função, selecione a **atualizar, mesmo se uma ou mais funções contêm uma única ocorrência de caixa de verificação** para ativar a atualização continuar. 

    Azure só pode garantir a disponibilidade de serviço de percentagem 99.95 durante uma atualização de serviço de nuvem se cada função tem, pelo menos, duas funções instâncias (máquinas virtuais). Permite que uma máquina de virtual processar pedidos de cliente, enquanto o outro está a ser atualizado.

8. Clique em **OK** (marca de verificação) para começar a actualizar o serviço.



## <a name="how-to-swap-deployments-to-promote-a-staged-deployment-to-production"></a>Como: trocar implementações para promover uma implementação faseada produção

Utilize **trocar** para promover uma implementação transição de um serviço na nuvem para produção. Se decidir implementar uma nova versão num serviço na nuvem, pode fase e testar a sua nova versão no seu ambiente de teste de serviço de nuvem enquanto os seus clientes estiver a utilizar a versão atual de produção. Quando estiver pronto para promover a nova versão produção, pode utilizar o **trocar** para mudar os URLs pelo qual as duas implementações estão endereçadas. 

Pode trocar implementações a partir da página de **Serviços em nuvem** ou o dashboard.

1. No [portal clássica Azure](https://manage.windowsazure.com/), clique em **Serviços em nuvem**.

2. Na lista de serviços em nuvem, clique em serviço em nuvem para selecioná-la.

3. Clique em **trocar**.

    É apresentada a seguinte mensagem de confirmação.

    ![Trocar de serviços na nuvem](./media/cloud-services-how-to-manage/CloudServices_Swap.png)

4. Depois de verificar as informações de implementação, clique em **Sim** para trocar as híbridas.

    Trocar a implementação acontece rapidamente uma vez que os endereços IP virtuais (VIP) de é a única coisa que muda para as implementações.

    Para guardar os custos de cluster, pode eliminar a implementação num ambiente de teste quando tem a certeza de que está a executar a implementação de produção novo conforme esperado.

## <a name="how-to-link-a-resource-to-a-cloud-service"></a>Como: ligar um recurso num serviço na nuvem

Para mostrar o cloud dependências do serviço de outros recursos, pode ligar uma instância de base de dados do Azure SQL ou uma conta de armazenamento para o serviço de nuvem. Pode associar e desassociar recursos na página de **Recursos ligadas** e, em seguida, monitorizar a sua utilização no dashboard de serviço de nuvem. Se a uma conta de armazenamento ligada tiver monitorização ativada, pode monitorizar Total de pedidos no dashboard de serviço de nuvem.

Utilize a **ligação** para criar uma nova ou existente base de dados SQL instância armazenamento conta ou uma ligação no seu serviço de nuvem. Em seguida, pode dimensionar a base de dados juntamente com a função de serviço de nuvem que está a utilizar na página de **escala** . (Uma conta de armazenamento automaticamente escalas medida que aumenta a utilização). Para mais informações, consulte o artigo [como dimensionar um serviço na nuvem e recursos ligadas](cloud-services-how-to-scale.md). 

Também pode monitorizar, gerir e dimensionar a base de dados no nó de **bases de dados** do portal clássico Azure. 

"Ligação" um recurso neste sentido não ligar a sua aplicação para o recurso. Se criar uma nova base de dados utilizando a **ligação**, terá de adicionar as cadeias de ligação ao seu código da aplicação e, em seguida, atualize o serviço de nuvem. Também terá de adicionar as cadeias de ligação se a sua aplicação utiliza recursos numa conta ligada armazenamento.

O procedimento seguinte descreve como associar uma nova instância de base de dados SQL, implementada num servidor de base de dados SQL novo, num serviço na nuvem.

### <a name="to-link-a-sql-database-instance-to-a-cloud-service"></a>Para ligar uma instância de base de dados SQL num serviço na nuvem

1. No [portal clássica Azure](http://manage.windowsazure.com/), clique em **Serviços em nuvem**. Em seguida, clique no nome do serviço em nuvem para abrir o dashboard.

2. Clique em **recursos ligados**.

    É aberta a página de **Recursos ligadas** .

    ![LinkedResourcesPage](./media/cloud-services-how-to-manage/CloudServices_LinkedResourcesPage.png)

3. Clique em **ligação um recurso** ou **ligação**.

    O Assistente de **Ligação de um recurso** é iniciado.

    ![Ligação Página1](./media/cloud-services-how-to-manage/CloudServices_LinkedResources_LinkPage1.png)

4. Clique em **criar um novo recurso** ou uma **ligação de um recurso existente**.

5. Selecione o tipo de recurso para criar uma ligação. No [portal clássica Azure](http://manage.windowsazure.com/), clique em **Base de dados SQL**. (O portal de clássico de pré-visualização Azure não suporta a ligar de uma conta de armazenamento num serviço na nuvem.)

6. Para concluir a configuração de base de dados, siga as instruções no artigo ajuda para a área de **Bases de dados SQL** do portal clássica Azure.

    Pode seguir o progresso da operação de ligação na área de mensagem.

    ![Progresso da ligação](./media/cloud-services-how-to-manage/CloudServices_LinkedResources_LinkProgress.png)

    Quando a ligação estiver concluída, pode monitorizar o estado do recurso ligado no dashboard de serviço de nuvem. Para informações sobre dimensionamento uma base de dados do SQL ligadas, consulte o artigo [como dimensionar um serviço na nuvem e recursos ligadas](cloud-services-how-to-scale.md).

### <a name="to-unlink-a-linked-resource"></a>Para desassociar um recurso ligado

1. No [portal clássica Azure](http://manage.windowsazure.com/), clique em **Serviços em nuvem**. Em seguida, clique no nome do serviço em nuvem para abrir o dashboard.

2. Clique em **Recursos ligadas**e, em seguida, selecione o recurso.

3. Clique em **Desassociar**. Em seguida, clique em **Sim** no pedido de confirmação.

    Desassociar a uma base de dados do SQL não afeta a base de dados ligações da aplicação ou para a base de dados. Ainda pode gerir a base de dados na área de **Bases de dados SQL** do portal clássica Azure.



## <a name="how-to-delete-deployments-and-a-cloud-service"></a>Como: eliminar implementações e um serviço na nuvem

Pode eliminar um serviço na nuvem, tem de eliminar cada implementação existente.

Para guardar os custos de cluster, pode eliminar a sua implementação transição depois de verificar a sua implementação de produção está a funcionar como esperado. São cluster faturado custos de instâncias de uma função mesmo que não está em execução num serviço na nuvem.

Utilize o seguinte procedimento para eliminar uma implementação ou o seu serviço de nuvem. 

1. No [portal clássica Azure](http://manage.windowsazure.com/), clique em **Serviços em nuvem**.

2. Selecione o serviço de nuvem e, em seguida, clique em **Eliminar**. (Para selecionar um serviço na nuvem sem abrir o dashboard, clique em qualquer lugar, exceto o nome na entrada de serviço de nuvem.)

    Se tiver uma implementação no teste ou de produção, verá um menu de opções semelhantes ao seguinte na parte inferior da janela. Antes de poder eliminar o serviço de nuvem, tem de eliminar qualquer implementações existentes.

    ![Eliminar Menu](./media/cloud-services-how-to-manage/CloudServices_DeleteMenu.png)


3. Para eliminar uma implementação, clique em **Eliminar implementação de produção** ou **Eliminar transição implementação**. Em seguida, no pedido de confirmação, clique em **Sim**. 

4. Se planeia eliminar o serviço de nuvem, repita o passo 3, se necessário, para eliminar a implementação como.

5. Para eliminar o serviço de nuvem, clique em **Eliminar serviço em nuvem**. Em seguida, no pedido de confirmação, clique em **Sim**.

> [AZURE.NOTE]
> Se a monitorização verboso estiver configurado para o seu serviço de nuvem, Azure não elimina os dados de monitorização da sua conta de armazenamento ao eliminar o serviço de nuvem. Irá precisar de eliminar os dados manualmente. Para obter informações sobre onde encontrar as tabelas de métricas, consulte o artigo "como: acesso verboso monitorizar os dados fora do Azure portal clássico" no [artigo como aos serviços em nuvem Monitor](cloud-services-how-to-monitor.md).

## <a name="next-steps"></a>Próximos passos

 * [Configuração geral do seu serviço de nuvem](cloud-services-how-to-configure.md).
* Saiba como [Implementar um serviço na nuvem](cloud-services-how-to-create-deploy.md).
* Configure um [nome de domínio personalizado](cloud-services-custom-domain-name.md).
* Configure [os certificados ssl](cloud-services-configure-ssl-certificate.md).
