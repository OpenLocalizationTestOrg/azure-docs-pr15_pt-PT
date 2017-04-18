<properties
    pageTitle="Gerir uma área de trabalho de aprendizagem máquina | Microsoft Azure"
    description="Gerir o acesso às áreas de trabalho de aprendizagem do Azure máquina e implementar e gerir os serviços de web ML API"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="garye"/>


# <a name="manage-an-azure-machine-learning-workspace"></a>Gerir uma área de trabalho do Azure máquina aprendizagem

>[AZURE.NOTE] Os procedimentos neste artigo são relevantes para serviços Web clássica do Azure máquina aprendizagem. Para obter informações sobre como gerir serviços Web no portal do máquina aprendizagem Web Services, consulte [Gerir um serviço Web utilizando o portal de serviços Web do Azure máquina aprendizagem](machine-learning-manage-new-webservice.md).

Através do portal clássico Azure, pode gerir as suas áreas de trabalho de máquina de aprendizagem para:

- Monitorizar como a área de trabalho está a ser utilizada
- Configurar a área de trabalho para permitir ou negar o acesso
- Gerir serviços Web criados na área de trabalho
- Eliminar a área de trabalho

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Além disso, o separador dashboard fornece uma descrição geral da sua utilização de área de trabalho e uma vista rápida das suas informações de área de trabalho.  

> [AZURE.TIP] No Azure máquina aprendizagem Studio, no separador **Serviços WEB** , pode adicionar, atualizar ou eliminar um serviço Web de aprendizagem do computador.

Para gerir uma área de trabalho:

1.  Inicie sessão no [portal clássica Azure](https://manage.windowsazure.com/) utilizando a sua conta do Microsoft Azure - utilizar a conta que está associada a subscrição Azure.
2.  No painel de serviços do Microsoft Azure, clique em **Formação do computador**.
3.  Clique em área de trabalho que pretende gerir.

A página de área de trabalho tem três separadores:

- **DASHBOARD** - permite-lhe ver utilização de área de trabalho e informações
- **Configurar** - permite-lhe gerir o acesso à área de trabalho
- **Serviços WEB** - permite-lhe gerir serviços Web que tenham sido publicados desta área de trabalho

## <a name="to-monitor-how-the-workspace-is-being-used"></a>Para monitorizar a forma como a área de trabalho está a ser utilizada

Clique no separador **DASHBOARD** .

A partir do dashboard, pode ver a utilização geral da área de trabalho e obter uma vista rápida de informações da área de trabalho.

- O gráfico **Calcular** mostra os recursos de cluster a ser utilizados pela área de trabalho. Pode alterar a vista para apresentar relativa ou absoluto valores e, pode alterar o período de tempo apresentado no gráfico.
- **Descrição geral de utilização** apresenta armazenamento Azure a ser utilizado pela área de trabalho.
- **Vista rápida** fornece um resumo das informações de área de trabalho e hiperligações úteis.

> [AZURE.NOTE] A ligação de **início de sessão no ML Studio** abre máquina aprendizagem Studio utilizando a Account Microsoft iniciada atualmente no. A Microsoft Account que utilizou para iniciar sessão no portal do Azure clássico para criar uma área de trabalho automaticamente não tem permissão para abrir a área de trabalho. Para abrir uma área de trabalho, tem de ter sessão iniciada na Account Microsoft que tenha sido definida como o proprietário da área de trabalho ou é necessário receber um convite ao proprietário para aderir a área de trabalho.


## <a name="to-grant-or-suspend-access-for-users"></a>Para conceder ou suspender o acesso de utilizadores ##

Clique no separador **Configurar** .

A partir do separador de configuração, pode:

- Suspenda acesso à área de trabalho máquina formação ao clicar em NEGAR. Os utilizadores já não poderão abrir a área de trabalho no máquina aprendizagem Studio. Para restaurar o acesso, clique em permitir.

Para gerir contas adicionais que tem acesso à área de trabalho no Studio de formação de máquina, clique em **Iniciar sessão ML Studio** no separador **DASHBOARD** (ver a nota anterior relativamente ao **início de sessão no ML Studio**). Esta ação abre a área de trabalho no máquina aprendizagem Studio. A partir daqui, clique no separador **Definições** e, em seguida, **os utilizadores**. Pode clicar em **CONVIDAR mais os utilizadores** para dar aos utilizadores acesso à área de trabalho, ou selecione um utilizador e clique em **Remover**.


## <a name="to-manage-web-services-in-this-workspace"></a>Para gerir serviços web nesta área de trabalho

Clique no separador **Serviços WEB** .

Isto apresenta uma lista de serviços web publicada a partir desta área de trabalho.
Para gerir um serviço web, clique no nome da lista para abrir a página de serviço Web.

Um serviço Web poderá ter um ou mais pontos finais definidos.

- Pode definir os pontos finais mais além o ponto final "Predefinido". Para adicionar o ponto final, clique em **Gerir os pontos finais** de, em fundo do dashboard para abrir o portal de serviços Web do Azure máquina aprendizagem.

- Para eliminar um ponto final (não pode eliminar o ponto final "Predefinido"), clique na caixa de verificação no início da linha de ponto final e clique em **Eliminar**. Esta ação remove o ponto final de serviço Web.

    > [AZURE.NOTE] Se uma aplicação está a utilizar o ponto final de serviço de web quando o ponto final é eliminado, a aplicação irão receber um erro da próxima vez que tenta aceder ao serviço.

Clique no nome de um ponto final de serviço Web para o abrir. 

A partir do dashboard, pode ver a utilização geral do seu serviço Web durante um período de tempo. Pode selecionar o período para ver a partir do menu pendente período no canto superior direito dos gráficos da utilização. O dashboard mostra as seguintes informações:

- **Pedidos ao longo do tempo** apresenta um gráfico de passo do número de pedidos durante o período de tempo selecionado. Pode ajudar a identificar se ocorrerem picos em utilização.
- **Pedidos de pedido de resposta** apresenta o número total de chamadas de resposta do pedido de que serviço tem recebidas ao longo do período de tempo selecionado e falhou quantos-las.
- **Tempo de calcular de pedido de resposta médio** apresenta uma média do tempo necessário para executar pedidos recebidos.
- **Pedidos de lote** apresenta o número total de pedidos de lote o serviço tenha recebido ao longo do período de tempo selecionado e falhou quantos dos mesmos.
- **Tarefa de latência de em média** apresenta uma média do tempo necessário para executar pedidos recebidos.
- **Erros** apresenta o número de agregação de erros que tenham ocorrido no chamadas para o serviço web.
- **Serviços de custos** apresenta os encargos para o plano de faturação associado ao serviço.

Na página configurar, pode atualizar as seguintes propriedades:

* **Descrição** permite-lhe introduzir uma descrição para o serviço Web. Descrição o campo é necessário.
* **Registo** permite-lhe ativar ou desativar o ponto final de início de sessão do erro. Para mais informações sobre a funcionalidade de registo, consulte o artigo ativar o [registo para os serviços web de aprendizagem automática](machine-learning-web-services-logging.md).
* **Dados de exemplo ativar** permite-lhe fornecer dados de exemplo que pode utilizar para testar o serviço de pedido de resposta. Se tiver criado o serviço web na máquina aprendizagem Studio, os dados de exemplo são disponibilizados dos dados de seu utilizados para preparar o seu modelo. Se tiver criado o serviço através de programação, os dados são tirados dos dados de exemplo fornecidos como parte do pacote JSON.

[consume]: machine-learning-consume-web-services.md
[marketplace]: machine-learning-publish-web-service-to-azure-marketplace.md
