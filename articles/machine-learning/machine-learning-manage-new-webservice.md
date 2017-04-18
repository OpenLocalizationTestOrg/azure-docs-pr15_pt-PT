<properties
    pageTitle="Gerir um serviço Web utilizando o portal do Azure máquina aprendizagem Web Serivces | Microsoft Azure"
    description="Gerir o acesso às áreas de trabalho de aprendizagem do Azure máquina e implementar e gerir os serviços de web ML API"
    services="machine-learning"
    documentationCenter=""
    authors="vDonGlover"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="v-donglo"/>


# <a name="manage-a-web-service-using-the-azure-machine-learning-web-services-portal"></a>Gerir um serviço Web utilizando o portal de serviços Web do Azure máquina aprendizagem

Pode gerir máquina aprendizagem novas e serviços Web clássica utilizando o portal do Microsoft Azure máquina aprendizagem Web Services. Uma vez que os serviços Web clássica e serviços Web novo são baseados no tecnologias subjacentes diferentes, tem as capacidades da gestão ligeiramente diferente para cada um deles.

No portal do máquina aprendizagem Web Services, pode:

- Monitorize como está a ser utilizado o serviço web.
- Configurar a descrição, as teclas de atualizar para a web de serviço (apenas para o novo), atualize o seu armazenamento conta chave (apenas para o novo,) ativar registo e ativar ou desativar dados de exemplo.
- Elimine o serviço web.
- Criar, eliminar ou atualizar faturação planos (apenas para o novo).
- Adicionar e eliminar os pontos finais (apenas clássico)

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="manage-new-web-services"></a>Gerir serviços do novo Web site

Para gerir os seus serviços de novo Web site:

1.  Inicie sessão portal do [Microsoft Azure máquina aprendizagem Web Services](https://services.azureml.net/quickstart) utilizando a sua conta do Microsoft Azure - utilizar a conta que está associada a subscrição Azure.
2.  No menu, clique em **Serviços Web**.

Isto apresenta uma lista de serviços Web implementados para a sua subscrição. 

Para gerir um serviço Web, clique em serviços Web. A partir da página de serviços Web, pode:

- Clique no serviço web para geri-lo.
- Clique na faturação plano para o serviço web para actualizá-lo.
- Elimine um serviço web.
- Copie um serviço web e implementá-lo para outro região.

Quando clica num serviço web, é aberta a página de guia de introdução do serviço web. A página de guia de introdução do serviço web tem duas opções no menu que lhe permitem gerir o seu serviço web:

- **DASHBOARD** - permite-lhe ver a utilização de serviço Web.
- **Configurar** - permite-lhe adicionar texto descritivo, atualize a chave da conta de armazenamento associado ao serviço Web e ativar ou desativar dados de exemplo.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Monitorização como está a ser utilizado o serviço web ###

Clique no separador **DASHBOARD** .

A partir do dashboard, pode ver a utilização geral do seu serviço Web durante um período de tempo. Pode selecionar o período para ver a partir do menu pendente período no canto superior direito dos gráficos da utilização. O dashboard mostra as seguintes informações:

- **Pedidos ao longo do tempo** apresenta um gráfico de passo do número de pedidos durante o período de tempo selecionado. Pode ajudar a identificar se ocorrerem picos em utilização.
- **Pedidos de pedido de resposta** apresenta o número total de chamadas de resposta do pedido de que serviço tem recebidas ao longo do período de tempo selecionado e falhou quantos-las.
- **Tempo de calcular de pedido de resposta médio** apresenta uma média do tempo necessário para executar pedidos recebidos.
- **Pedidos de lote** apresenta o número total de pedidos de lote o serviço tenha recebido ao longo do período de tempo selecionado e falhou quantos dos mesmos.
- **Tarefa de latência de em média** apresenta uma média do tempo necessário para executar pedidos recebidos.
- **Erros** apresenta o número de agregação de erros que tenham ocorrido no chamadas para o serviço web.
- **Serviços de custos** apresenta os encargos para o plano de faturação associado ao serviço.

### <a name="configuring-the-web-service"></a>Configurar o serviço web ###

Clique na opção de menu **Configurar** .

Pode atualizar as seguintes propriedades:

* **Descrição** permite-lhe introduzir uma descrição para o serviço Web.
* **Título** permite-lhe introduzir um título para o serviço Web
* **Teclas** permite-lhe rodar as suas chaves de API principais e secundárias.
* **Chave de conta de armazenamento** permite-lhe atualizar a chave da conta de armazenamento associados com as alterações de serviço Web. 
* **Dados de exemplo ativar** permite-lhe fornecer dados de exemplo que pode utilizar para testar o serviço de pedido de resposta. Se tiver criado o serviço web na máquina aprendizagem Studio, os dados de exemplo são disponibilizados dos dados de seu utilizados para preparar o seu modelo. Se tiver criado o serviço através de programação, os dados são tirados dos dados de exemplo fornecidos como parte do pacote JSON.

### <a name="managing-billing-plans"></a>Gerir a faturaçãohttps planos ###

Clique na opção de menu **planos** a partir da página de guia de introdução de serviços web. Também pode clicar no plano de associado ao serviço de Web específico para gerir esse plano.

* **Novo** permite-lhe criar um novo plano.
* **Adicionar/remover plano instância** permite-lhe "Dimensionar saída" um plano existente para adicionar a capacidade.
* **Atualização/redução** permite-lhe "escalar para cima" um plano existente para adicionar a capacidade.
* **Eliminar** permite-lhe eliminar um plano.

Clique num plano para ver o seu dashboard. O dashboard dá-lhe a utilização de instantâneo ou plano durante um período de tempo selecionado. Para selecionar o período de tempo para ver, clique no menu pendente **período** no canto superior direito do dashboard. 

Dashboard de plano fornece as seguintes informações:

* **Descrição do plano** apresenta informações sobre os custos e capacidade associado ao plano.
* **A utilização de plano** apresenta o número de transações e horas de cluster que foram cobradas o plano.
* **Serviços Web** apresenta o número de serviços Web que está a utilizar este plano.
* **Início por chamadas de serviço Web** apresenta os serviços Web início quatro que estão a efetuar chamadas que são cobradas contra o plano.
* **Serviços de Web do início ao calcular Hrs** apresenta os serviços de Web do início quatro que estiver a utilizar o cluster recursos que lhe são cobrados contra o plano.

## <a name="manage-classic-web-services"></a>Gerir serviços Web clássico

> [AZURE.NOTE] Os procedimentos nesta secção são relevantes para a gestão de serviços de web clássica através do portal de serviços Web do Azure máquina aprendizagem. Para obter informações sobre como gerir serviços Web clássica através a Studio de aprendizagem automática e o portal de clássico Azure, consulte [Gerir uma área de trabalho do Azure máquina aprendizagem](machine-learning-manage-workspace.md).

Para gerir os serviços Web clássico:

1.  Inicie sessão portal do [Microsoft Azure máquina aprendizagem Web Services](https://services.azureml.net/quickstart) utilizando a sua conta do Microsoft Azure - utilizar a conta que está associada a subscrição Azure.
2.  No menu, clique em **Serviços Web clássica**.

Para gerir um serviço Web clássico, clique em **Serviços Web clássica**. A partir da página de serviços Web clássico, pode:

- Clique no serviço web para ver os pontos finais associados.
- Elimine um serviço web.

Ao gerir um serviço Web clássico, faz a gestão cada uma dos pontos finais separadamente. Quando clica num serviço web na página Serviços Web, abre-se a lista de pontos finais associado ao serviço. 

Na página de ponto final de serviço Web clássico, pode adicionar e eliminar os pontos finais o serviço. Para mais informações sobre como adicionar pontos finais, consulte o artigo [Criar os pontos finais](machine-learning-create-endpoint.md).

Clique dos pontos finais para abrir a página de guia de introdução do serviço web. Na página do guia de introdução, existem duas opções no menu que lhe permitem gerir o seu serviço web:

- **DASHBOARD** - permite-lhe ver a utilização de serviço Web.
- **Configurar** - permite-lhe adicionar texto descritivo, ativar o registo de erros e desativar, atualização a chave para o armazenamento de conta associado ao serviço Web e activar e desactivar a dados de exemplo.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Monitorização como está a ser utilizado o serviço web ###

Clique no separador **DASHBOARD** .

A partir do dashboard, pode ver a utilização geral do seu serviço Web durante um período de tempo. Pode selecionar o período para ver a partir do menu pendente período no canto superior direito dos gráficos da utilização. O dashboard mostra as seguintes informações:

- **Pedidos ao longo do tempo** apresenta um gráfico de passo do número de pedidos durante o período de tempo selecionado. Pode ajudar a identificar se ocorrerem picos em utilização.
- **Pedidos de pedido de resposta** apresenta o número total de chamadas de resposta do pedido de que serviço tem recebidas ao longo do período de tempo selecionado e falhou quantos-las.
- **Tempo de calcular de pedido de resposta médio** apresenta uma média do tempo necessário para executar pedidos recebidos.
- **Pedidos de lote** apresenta o número total de pedidos de lote o serviço tenha recebido ao longo do período de tempo selecionado e falhou quantos dos mesmos.
- **Tarefa de latência de em média** apresenta uma média do tempo necessário para executar pedidos recebidos.
- **Erros** apresenta o número de agregação de erros que tenham ocorrido no chamadas para o serviço web.
- **Serviços de custos** apresenta os encargos para o plano de faturação associado ao serviço.

### <a name="configuring-the-web-service"></a>Configurar o serviço web ###

Clique na opção de menu **Configurar** .

Pode atualizar as seguintes propriedades:

* **Descrição** permite-lhe introduzir uma descrição para o serviço Web. Descrição o campo é necessário.
* **Registo** permite-lhe ativar ou desativar o ponto final de início de sessão do erro. Para mais informações sobre a funcionalidade de registo, consulte o artigo ativar o [registo para os serviços web de aprendizagem automática](machine-learning-web-services-logging.md).
* **Dados de exemplo ativar** permite-lhe fornecer dados de exemplo que pode utilizar para testar o serviço de pedido de resposta. Se tiver criado o serviço web na máquina aprendizagem Studio, os dados de exemplo são disponibilizados dos dados de seu utilizados para preparar o seu modelo. Se tiver criado o serviço através de programação, os dados são tirados dos dados de exemplo fornecidos como parte do pacote JSON.

## <a name="grant-or-suspend-access-to-web-services-for-users-in-the-portal"></a>Conceder ou suspender acesso aos serviços Web para utilizadores no portal

Utilizando o portal clássico Azure, pode permitir ou negar o acesso a utilizadores específicos.

### <a name="access-for-users-of-new-web-services"></a>Acesso a utilizadores dos serviços do novo Web site

Para permitir que outros utilizadores trabalhar com os seus serviços Web no portal do Azure máquina aprendizagem Web Services, tem de adicioná-los como co-administradores na sua subscrição do Azure.

Inicie sessão no [portal clássica Azure](https://manage.windowsazure.com/) utilizando a sua conta do Microsoft Azure - utilizar a conta que está associada a subscrição Azure.

1. No painel de navegação, clique em **Definições**, em seguida, clique em **administradores**.
2. Na parte inferior da janela, clique em **Adicionar**. 
3. Na caixa de diálogo Adicionar um administrador de cocriação, escreva o endereço de e-mail da pessoa que pretende adicionar como colega administrador e, em seguida, selecione a subscrição que pretende que o administrador para aceder a cocriação.
4. Clique em **Guardar**.

### <a name="access-for-users-of-classic-web-services"></a>Acesso a utilizadores dos serviços Web clássico

Para gerir uma área de trabalho:

Inicie sessão no [portal clássica Azure](https://manage.windowsazure.com/) utilizando a sua conta do Microsoft Azure - utilizar a conta que está associada a subscrição Azure.

1. No painel de serviços do Microsoft Azure, clique em **Formação do computador**.
1. Clique em área de trabalho que pretende gerir.
1. Clique no separador **Configurar** .

No separador Configuração, pode suspender acesso à área de trabalho máquina formação ao clicar em **NEGAR**. Os utilizadores já não poderão abrir a área de trabalho no máquina aprendizagem Studio. Para restaurar o acesso, clique em **Permitir**.

Para utilizadores específicos:

Para gerir contas adicionais que tem acesso à área de trabalho no Studio de formação de máquina, clique em **Iniciar sessão ML Studio** no separador **DASHBOARD** . Esta ação abre a área de trabalho no máquina aprendizagem Studio. A partir daqui, clique no separador **Definições** e, em seguida, **os utilizadores**. Pode clicar em **CONVIDAR mais os utilizadores** para dar aos utilizadores acesso à área de trabalho, ou selecione um utilizador e clique em **Remover**.

> [AZURE.NOTE] A ligação de **início de sessão no ML Studio** abre máquina aprendizagem Studio utilizando a Account Microsoft iniciada atualmente no. A Microsoft Account que utilizou para iniciar sessão no portal do Azure clássico para criar uma área de trabalho automaticamente não tem permissão para abrir a área de trabalho. Para abrir uma área de trabalho, tem de ter sessão iniciada na Account Microsoft que tenha sido definida como o proprietário da área de trabalho ou é necessário receber um convite ao proprietário para aderir a área de trabalho.
