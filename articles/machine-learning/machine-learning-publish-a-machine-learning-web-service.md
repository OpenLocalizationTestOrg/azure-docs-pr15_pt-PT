<properties
    pageTitle="Implementar um serviço web de aprendizagem de máquinas | Microsoft Azure"
    description="Como converter uma experiência de formação para uma experiência de previsão, prepará-lo para implementação, em seguida, implementá-la como um serviço web de aprendizagem de máquinas Azure."
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
    ms.date="10/04/2016"
    ms.author="garye"/>

# <a name="deploy-an-azure-machine-learning-web-service"></a>Implementar um serviço web de aprendizagem de máquinas Azure

Aprendizagem de máquinas Azure permite-lhe criar, testar e implementar soluções analíticas previsão.

De um ponto-de-vista de alto nível, isto é efectuado em três passos:

- **[Criar uma experiência de formação]** - Azure máquina aprendizagem Studio é um ambiente de desenvolvimento visual colaboração que utilizar para preparar e testar um modelo de previsão analytics utilizando dados de formação que fornecer.
- **[Convertê-la para uma experiência de previsão]** - uma vez o modelo foi preparado com dados existentes e está pronto para utilizá-lo para pontuar novos dados, preparar e simplificar a experiência para previsões.
- **Implementá-lo como um serviço web** - pode implementar a experiência de previsão como um serviço Azure web [novos] ou [clássico] . Os utilizadores podem enviar dados para o modelo e receber previsões do modelo.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="create-a-training-experiment"></a>Criar uma experiência de formação

Para preparar um modelo de previsão analytics, utilize Azure máquina aprendizagem Studio para criar uma experiência de formação onde incluir vários módulos para carregar dados de formação, preparar os dados conforme necessário, aplicar os algoritmos de aprendizagem de máquina e avaliar os resultados. Pode iterar numa experiência e tente a aprendizagem de máquinas diferentes algoritmos para comparar e avaliar os resultados.

O processo de criar e gerir experiências de formação é abrangido mais aprofundadamente noutro local. Para mais informações, consulte estes artigos:

- [Criar uma experiência simple no Azure máquina aprendizagem Studio](machine-learning-create-experiment.md)
- [Desenvolver uma solução Preditiva com Azure aprendizagem de máquinas](machine-learning-walkthrough-develop-predictive-solution.md)
- [Importar os dados de formação para Azure máquina aprendizagem Studio](machine-learning-data-science-import-data.md)
- [Gerir iterações experiência no Azure máquina aprendizagem Studio](machine-learning-manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Converter a experiência de formação para uma experiência de previsão

Depois de ter recebido formação adequada do modelo, está pronto para converter a experiência de formação uma experiência de previsão para pontuar novos dados.

Convertendo para uma experiência de previsão, está a obter o modelo com formação pronta a ser implementada como um serviço web pontuação. Os utilizadores do serviço web podem enviar dados de entrada para o modelo e modelo devolverá os resultados de previsão. Como converter para uma experiência de previsão, tenha em atenção como prevê que o modelo a ser utilizado por outros utilizadores.

Para converter a experiência de formação para uma experiência de previsão, clique em **Executar** na parte inferior da tela experiência de, clique em **Configurar serviço Web**, em seguida, seleccione o **Serviço Web de previsão**.

![Converter para o ensaio de pontuação](./media/machine-learning-publish-a-machine-learning-web-service/figure-1.png)

Para mais informações sobre como efectuar esta conversão, consulte a [converter uma experiência de formação de aprendizagem de máquinas para uma experiência de previsão](machine-learning-convert-training-experiment-to-scoring-experiment.md).

Os passos seguintes descrevem a implementação de uma experiência de previsão como um novo serviço web. Também pode implementar a experiência como serviço web de clássico.

## <a name="deploy-the-predictive-experiment-as-a-new-web-service"></a>Implementar a experiência de previsão como um novo serviço web

Agora que a experiência de previsão tiver sido preparada, é possível implementar como um serviço Azure web. Utilizar o serviço web, os utilizadores podem enviar dados para o modelo e o modelo irá devolver suas previsões.

Para implementar a experiência de previsão, clique em **Executar** na parte inferior da tela de experiência. Depois do ensaio tiver sido executado, clique em **Implementar o serviço Web** e seleccione **Implementar o serviço da Web [Novo]**.  Abre a página de implementação do portal do serviço de Web de aprendizagem do computador.

### <a name="machine-learning-web-service-portal-deploy-experiment-page"></a>Portal do serviço Web de aprendizagem implementar página de experiência de máquina

Na página implementar experiência, introduza um nome para o serviço web.
Seleccione um plano de preços. Se tiver um plano de preços existente, que pode seleccioná-lo, caso contrário, tem de criar um novo plano de preços para o serviço.

1.  No **Plano de preço** de lista pendente, seleccione um plano existente ou seleccione a opção de **Seleccionar o novo plano** .
2.  Em **Nome do plano**, escreva um nome que identificará o plano na factura.
3.  Seleccione uma das **Camadas de planeamento mensal**. A predefinição de camadas de plano para os planos para a região e o serviço web é implementada para essa região.

Faça clique sobre a **implementação** e a página de **Iniciação** para a serviço de web é aberta.

A página de iniciação do serviço web fornece acesso e orientações sobre as tarefas mais comuns que irá efectuar depois de criar um serviço web. A partir daqui, pode aceder facilmente a página de teste e a página de consumir.

<!-- ![Deploy the web service](./media/machine-learning-publish-a-machine-learning-web-service/figure-2.png)-->

### <a name="test-your-web-service"></a>Testar o serviço web

Para testar o novo serviço web, clique em **testar o serviço web** em tarefas comuns. Na página de teste, pode testar o serviço web como um serviço de pedido-resposta (RRS) ou um serviço de execução de comandos (BES).

A página de teste de RR apresenta os inputs, saídas e quaisquer parâmetros globais que definiu para o ensaio. Para testar o serviço web, pode introduzir valores apropriados para os factores de produção ou fornecer um ficheiro formatado de separados por vírgulas (CSV) de valor que contém os valores de teste manualmente.

Para testar a utilização de RR, do modo de vista de lista, introduza valores apropriados para os factores de produção e clique em **Testar pedido-resposta**. Apresentam os resultados da previsão da coluna de saída para a esquerda.

![Implementar o serviço web](./media/machine-learning-publish-a-machine-learning-web-service/figure-5-test-request-response.png)

Para testar o BES, clique em **lotes**. Na página de teste Batch, clique em Procurar em introdução de dados e seleccione um ficheiro CSV que contém valores de exemplo adequado. Se não tiver um ficheiro CSV e criou a experiência Preditiva com Studio de aprendizagem de máquina, pode transferir o conjunto de dados para a experiência de previsão e utilizá-lo.

Para transferir o conjunto de dados, abra Studio de aprendizagem de máquina. Abra a experiência de previsão e clique com o direito a entrada para a experiência. No menu de contexto, seleccione o **conjunto de dados** e, em seguida, seleccione a **Transferir**.

![Implementar o serviço web](./media/machine-learning-publish-a-machine-learning-web-service/figure-7-mls-download.png)

Clique em **Testar**. Apresenta o estado de tarefa a execução de comandos para a direita em **Rotinas do ensaio**.

![Implementar o serviço web](./media/machine-learning-publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/machine-learning-publish-a-machine-learning-web-service/figure-3.png)-->

Na página de **configuração** , pode alterar a descrição, título, actualizar a chave de conta de armazenamento e activar os dados de exemplo para o serviço web.

![Configurar o serviço web](./media/machine-learning-publish-a-machine-learning-web-service/figure-8-arm-configure.png)

Depois de ter implementado o serviço web, pode:

- **Acesso** através de API de serviço web.
- **Geri** -lo através do portal de serviços web de aprendizagem de máquinas Azure ou portal Azure clássico.
- **Actualização** , se o modelo alterado.

### <a name="access-the-web-service"></a>Aceder ao serviço web

Depois de implementar o serviço web do Studio de aprendizagem do computador, pode enviar dados para o serviço e receber respostas através de programação.

A página de **consumir** fornece todas as informações que necessita para aceder ao serviço web. Por exemplo, a chave de API é fornecida para permitir o acesso autorizado ao serviço.

Para mais informações sobre como aceder a um serviço web de aprendizagem de máquinas, ver [como a consumir um serviço de web de aprendizagem de máquinas Azure implementado](machine-learning-consume-web-services.md).

### <a name="manage-your-new-web-service"></a>Gerir o novo serviço web

Pode gerir portal web clássico serviços máquina aprendizagem Web Services. Na [página de portal principal](https://services.azureml-test.net/), clique em **Serviços Web**. Na página de serviços web, pode eliminar ou copiar um serviço. Para monitorizar um serviço específico, clique no serviço e, em seguida, clique em **Dashboard**. Para monitorizar tarefas de lote associadas ao serviço web, clique em **Registo de pedido de rotina**.

## <a name="deploy-the-predictive-experiment-as-a-classic-web-service"></a>Implementar a experiência de previsão como um serviço web de clássico

Agora que a experiência de previsão foi suficientemente preparada, pode implementá-lo como um serviço Azure web. Utilizar o serviço web, os utilizadores podem enviar dados para o modelo e o modelo irá devolver suas previsões.

Para implementar a experiência de previsão, clique em **Executar** na parte inferior da tela experiência de e, em seguida, clique em **Implementar o serviço Web**. O serviço web está configurado e é colocadas no dashboard de serviço web.

![Implementar o serviço web](./media/machine-learning-publish-a-machine-learning-web-service/figure-2.png)

Pode testar o serviço web no portal de serviços de Web de aprendizagem de máquinas ou máquina aprendizagem Studio.

Para testar o serviço web de resposta ao pedido, clique no botão **Testar** no dashboard de serviço web. É apresentada uma caixa de diálogo para lhe pedir os dados de entrada para o serviço. Estas são as colunas esperadas pela experiência pontuação. Introduza um conjunto de dados e, em seguida, clique em **OK**. Os resultados gerados pelo serviço da web são apresentados na parte inferior do dashboard.

Pode clicar na hiperligação de pré-visualização **de teste** para testar o serviço no portal Azure máquina aprendizagem Web Services, tal como é mostrado anteriormente na secção de serviço web nova.

Para testar o serviço de execução de comandos, clique em **Testar** ligação pré-visualizar. Na página de teste Batch, clique em Procurar em introdução de dados e seleccione um ficheiro CSV que contém valores de exemplo adequado. Se não tiver um ficheiro CSV e criou a experiência Preditiva com Studio de aprendizagem de máquina, pode transferir o conjunto de dados para a experiência de previsão e utilizá-lo.

![Testar o serviço web](./media/machine-learning-publish-a-machine-learning-web-service/figure-3.png)

Na página de **configuração** , pode alterar o nome a apresentar do serviço e atribua-lhe uma descrição. O nome e descrição é apresentada no [portal clássico Azure](http://manage.windowsazure.com/) onde gerir os serviços web.

Pode fornecer uma descrição para a entrada de dados, dados de saída e parâmetros do serviço web, introduzindo uma cadeia para cada coluna no **Esquema de entrada**, o **Esquema de saída**e o **Parâmetro do serviço Web**. Estas descrições são utilizadas na documentação de código de exemplo fornecida para o serviço web.

Pode activar o registo diagnosticar falhas que está a ver quando o serviço web for acedido. Para mais informações, consulte [Activar o registo para serviços web de aprendizagem de máquinas](machine-learning-web-services-logging.md).

![Configurar o serviço web](./media/machine-learning-publish-a-machine-learning-web-service/figure-4.png)

Também pode configurar os pontos finais para o serviço web no portal Azure máquina aprendizagem Web serviços semelhante para o procedimento indicado anteriormente a nova secção de serviço web. As opções são diferentes, pode adicionar ou alterar a descrição do serviço, activar o registo e activar os dados de exemplo para o ensaio.

### <a name="access-the-web-service"></a>Aceder ao serviço web

Depois de implementar o serviço web do Studio de aprendizagem do computador, pode enviar dados para o serviço e receber respostas através de programação.

O dashboard fornece todas as informações que necessita para aceder ao serviço web. Por exemplo, a chave de API é fornecida para permitir o acesso autorizado ao serviço e páginas de ajuda de API são fornecidas para ajudar a começar a escrever código.

Para mais informações sobre como aceder a um serviço web de aprendizagem de máquinas, ver [como a consumir um serviço de web de aprendizagem de máquinas Azure implementado](machine-learning-consume-web-services.md).

### <a name="manage-the-web-service"></a>Gerir o serviço web

Existem várias acções que pode efectuar para monitorizar um serviço web. Pode actualizá-lo e eliminá-la. Também pode adicionar pontos finais adicionais a um serviço web de clássico para além do ponto final predefinido que é criado quando implementar.

Para mais informações, consulte [Gerir uma área de trabalho de aprendizagem de máquinas Azure](machine-learning-manage-workspace.md) e [Gerir um serviço web utilizando o portal Azure máquina aprendizagem Web Services](machine-learning-manage-new-webservice.md).

<!-- When this article gets published, fix the link and uncomment
For more information on how to manage Azure Machine Learning web service endpoints using the REST API, see **Azure machine learning web service endpoints**.
-->

## <a name="update-the-web-service"></a>Actualizar o serviço web

Pode efectuar alterações ao serviço web, tais como a actualização do modelo com dados de preparação adicional e implementá-la novamente, substituindo o serviço web original.

Para actualizar o serviço web, abra a experiência de previsão original que utilizou para implementar o serviço web e efectuar uma cópia editável clicando em **Guardar como**. Efectue as alterações e, em seguida, clique em **Implementar o serviço Web**.

Uma vez que tenha implementado ensaio antes, é-lhe perguntado se pretende substituir (serviço Web clássico) ou actualizar (novo serviço web) o serviço existente. Clicando em **Sim** ou **actualização** pára o serviço web existente e implementa a nova experiência de previsão é implementada no seu lugar.

> [AZURE.NOTE] Se efectuou alterações de configuração no serviço web original, por exemplo, introduzir um novo nome ou descrição, terá de voltar a introduzir esses valores.

Uma opção para actualizar o serviço web é o modelo de retransmissão através de programação. Para mais informações, consulte [modelos de aprendizagem de máquinas de retransmissão através de programação](machine-learning-retrain-models-programmatically.md).


<!-- internal links -->
[Criar uma experiência de formação]: #create-a-training-experiment
[Converter para uma experiência de previsão]: #convert-the-training-experiment-to-a-predictive-experiment
[novo]: #deploy-the-predictive-experiment-as-a-new-Web-service
[clássico]: #deploy-the-predictive-experiment-as-a-new-Web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service
