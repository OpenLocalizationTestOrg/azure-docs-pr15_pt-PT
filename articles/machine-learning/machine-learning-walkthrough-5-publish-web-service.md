<properties
    pageTitle="Passo 5: Implementar o serviço Web de aprendizagem máquina | Microsoft Azure"
    description="Passo 5 da desenvolver uma solução preditiva Tutorial: implementar uma experiência de previsão no Studio de aprendizagem do computador como um serviço Web."
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


# <a name="walkthrough-step-5-deploy-the-azure-machine-learning-web-service"></a>Instruções passo 5: Implementar o serviço Web de aprendizagem de máquina Azure

Este é o quinto escalão tutorial, [desenvolver uma solução de previsão analytics na aprendizagem de máquinas Azure](machine-learning-walkthrough-develop-predictive-solution.md)


1.  [Criar uma área de trabalho de aprendizagem de máquinas](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [Carregar os dados existentes](machine-learning-walkthrough-2-upload-data.md)
3.  [Criar uma experiência nova](machine-learning-walkthrough-3-create-new-experiment.md)
4.  [Preparar e avaliar os modelos](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  **Implementar o serviço Web**
6.  [Aceder ao serviço Web](machine-learning-walkthrough-6-access-web-service.md)

----------

Dar a outros a oportunidade de utilizar o modelo de previsão que tiver desenvolvemos neste tutorial, vamos implementá-la como um serviço Web no Azure.

Até este ponto é feito experiências com o nosso modelo de formação. Mas o serviço implementado já não vai fazer formação - gera previsões, a entrada do utilizador com base no nosso modelo de pontuação. Por isso vamos se preparar para converter esta experiência de uma experiência de ***formação*** para uma ***previsão*** experiências. 

Este é um processo composto por dois passos:  

1. Converter a *experiências de formação* Criámos uma *experiência de previsão*
2. Implementar a experiência de previsão como um serviço Web

Mas primeiro, precisamos de cortar esta experiência um pouco para baixo. Temos actualmente dois modelos diferentes na experiência, mas apenas pretendemos um modelo quando este é implementada como um serviço Web.  

Vamos supor que decidiu que o modelo de árvore aumentado era o melhor modelo a utilizar. Para a primeira coisa a fazer é remover a [Máquina de Vector de suporte de classe de dois] [ two-class-support-vector-machine] módulo e os módulos que foram utilizados para formação-lo. Poderá pretender efectuar uma cópia da experiência pela primeira vez, clicando em **Guardar como** na parte inferior da tela de experiência.

É necessário eliminar os seguintes módulos:  

- [Máquina de Vector do suporte de classe de dois][two-class-support-vector-machine]
- [Modelo do comboio] [ train-model] e o [Modelo de pontuação] [ score-model] módulos que estavam ligados
- [Normalizar os dados] [ normalize-data] (ambos delas)
- [Avaliar modelo][evaluate-model]

Seleccione o módulo e prima a tecla Delete, ou com o botão direito do módulo e seleccione **Eliminar**.

Agora que estamos prontos para implementar este modelo utilizando a [Árvore de decisão aumentado da classe de dois][two-class-boosted-decision-tree].

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Converter a experiência de formação para uma experiência de previsão

Converter para uma experiência preditiva envolve três passos:

1. Guardar o modelo que tenha recebido formação adequada e, em seguida, substituir nosso módulos de formação
2. A experiência para remover módulos que apenas foram necessários para a formação de corte
3. Definir em que o serviço Web será aceitam entradas e em que gera a saída

Felizmente, todos os três passos podem ser efectuados, fazendo clique sobre o **serviço Web de conjunto de cópias** na parte inferior da tela de experiência (seleccione a opção de **serviço Web preditiva** ).

Quando clica em **serviço Web de conjunto de cópias**, acontecem várias coisas:

- O modelo formado é guardado como um módulo de **Formação de modelo** único para a paleta de módulo à esquerda da tela de experiência (pode encontrá-la na **Formação de modelos**).
- Módulos que foram utilizados para a formação são removidos. Em especial:
  - [Árvore de decisão aumentado de dois classe][two-class-boosted-decision-tree]
  - [Modelo do comboio][train-model]
  - [Dividir dados][split]
  - o segundo [Executar Script de R] [ execute-r-script] módulo que foi utilizado para dados de ensaio
- O modelo guardado formado é adicionado novamente para o ensaio.
- Módulos de **entrada de serviço Web** e de **saída de serviço Web** são adicionados.

> [AZURE.NOTE] A experiência tenha sido guardada em duas partes em separadores que tenham sido adicionados à parte superior da tela de experiência: a experiência de formação original é no separador **experiência de formação**e experiência preditiva recentemente criada é em **Predictive experiências**.

É necessário ter um passo adicional com esta experiência específica.
Adicionámos dois [Executar Script de R] [ execute-r-script] módulos para fornecer uma função de ponderação para os dados de formação e de teste. Não é necessário fazê-lo no modelo final.

Máquina aprendizagem Studio removido um [Executar Script de R] [ execute-r-script] módulo quando removê-lo a [divisão] [ split] módulo. Agora vamos pode agora remover o outro e ligar o [Editor de metadados] [ metadata-editor] directamente para o [Modelo de pontuação][score-model].    

A nossa experiência deverá agora ter este aspecto:  

![O modelo com formação de pontuação][4]  

> [AZURE.NOTE] Poderá estar a pensar porque é que podemos esquerda o conjunto de dados de dados de cartão de crédito de alemão UCI na experiência previsão. O serviço irá utilizar os dados do utilizador, não o conjunto de dados original, por isso deixe porque é que o conjunto de dados original no modelo?
>
>É verdade que o serviço não tem dos dados originais do cartão de crédito. Mas se necessitar do esquema para que os dados, que inclui informações tais como o número de colunas não existem e quais as colunas são numéricas. Estas informações de esquema são necessárias para interpretar os dados do utilizador. Vamos deixar estes componentes ligados para que o módulo de pontuação tem o esquema de conjunto de dados quando o serviço está em execução. Os dados não utilizados, apenas o esquema.  

Executar a experiência pela última vez (clique em **Executar**). Se pretender verificar se o modelo ainda está a funcionar, faça clique sobre a saída do [Modelo de pontuação] [ score-model] módulo e seleccione **Ver resultados**. Verá que são apresentados os dados originais, juntamente com o valor de risco de crédito ("rótulos contabilizadas") e o valor de probabilidade de pontuação ("contabilizadas probabilidades".) 

## <a name="deploy-the-web-service"></a>Implementar o serviço Web

Pode implementar a experiência como um serviço Web clássico ou um novo serviço Web com base no Gestor de recursos do Azure.

### <a name="deploy-as-a-classic-web-service"></a>Implementar o como um serviço Web clássico ###

Para implementar um serviço Web clássico proveniente de nossa experiência, clique **Implementar o serviço Web** abaixo a tela e seleccione a **Implementar o serviço da Web [clássico]**. Máquina aprendizagem Studio implementa experiência como um serviço Web e leva-o para o dashboard para esse serviço Web. A partir daqui, pode voltar para o ensaio (**Ver instantâneo** ou **Ver mais recentes**) e executar um teste simples do serviço Web (consulte **o serviço Web de ensaio** abaixo). Também existe informação para a criação de aplicações que podem aceder ao serviço de Web (mais informações sobre que no passo seguinte destas instruções).

![Dashboard de serviço Web][6]

Pode configurar o serviço clicando no separador **configuração** . Aqui pode modificar o nome do serviço (que atribuiu o nome de experiência por predefinição) e atribua-lhe uma descrição. Também pode atribuir mais amigáveis etiquetas para as colunas de entrada e saídas.  

![Configurar o serviço Web][5]  

### <a name="deploy-as-a-new-web-service"></a>Implementar o como um serviço Web de novo

Para implementar um serviço da nova Web proveniente de nossa experiência, clique em **Implementar o serviço Web** abaixo a tela e **Implementar o serviço da Web [novo]**. Máquina aprendizagem Studio transfere, até à página de implementar experiência de serviços Web de aprendizagem Azure máquina.

Introduza um nome para o serviço Web e seleccionar um plano de preços. Se tiver um plano de preços existente, que pode seleccioná-lo, caso contrário, tem de criar um novo plano de preços para o serviço. 

1.  Na lista pendente de **Plano de preços** , seleccione um plano existente ou seleccione a opção de **Seleccionar o novo plano** .
2.  Em **Nome do plano**, escreva um nome que identifica o plano na factura.
3.  Seleccione uma das **Camadas de planeamento mensal**. A predefinição de camadas de plano para os planos para a região e o serviço Web é implementada para essa região.

Faça clique sobre a **implementação** e a página de **Iniciação** para o abre de serviço Web.

Pode configurar o serviço clicando na opção de menu **Configurar** . Aqui pode modificar o nome do serviço (que atribuiu o nome de experiência por predefinição) e atribua-lhe uma descrição. 

Para testar a selecção de serviço Web, clique na opção de menu **de ensaio** (ver a **testar o serviço Web** abaixo). Para obter informações sobre como criar aplicações que podem aceder ao serviço Web, clique na opção de menu **consumir** (mais informações sobre que no passo seguinte destas instruções).

> [AZURE.TIP] Pode actualizar o serviço Web depois de ter implementado. Por exemplo, se pretender alterar o modelo, editar a experiência de formação, optimizar os parâmetros de modelo e clique em **Implementar o serviço Web**. Em seguida, seleccione a **Implementar o serviço da Web [clássico]** ou **Implementar [novo] serviço Web**. Quando implementar novamente a experiência, substitui o serviço Web, utilizando agora o modelo actualizado.  

## <a name="test-the-web-service"></a>Testar o serviço Web

### <a name="test-a-classic-web-service"></a>Testar um serviço Web clássico

Pode testar o serviço na máquina aprendizagem Studio ou no portal Azure máquina aprendizagem Web Services. Ensaio no portal Azure máquina aprendizagem Web Services tem a vantagem de permitir a activação 

**Testar na máquina aprendizagem Studio**

Na página de **DASHBOARD** , clique no botão de **ensaio** nos termos do **Ponto final predefinido**. Uma caixa de diálogo irá pop-up e pedir-lhe os dados de entrada para o serviço. Estas são as mesmas colunas que tenham aparecido no dataset de risco de crédito alemão original.  

Introduza um conjunto de dados e, em seguida, clique em **OK**. 

**Testar no portal Azure máquina aprendizagem Web Services**

Na página de **DASHBOARD** , clique na hiperligação de pré-visualização de **ensaio** nos termos do **Ponto final predefinido**. A página de teste no portal Azure máquina aprendizagem Web Serviços de ponto final do serviço Web é aberta e pede-lhe os dados de entrada para o serviço. Estas são as mesmas colunas que tenham aparecido no dataset de risco de crédito alemão original.

Clique em **Teste pedido-resposta**. 

O serviço Web, os dados introduz através do módulo de **entrada de serviço Web** , através de do [Editor de metadados] [ metadata-editor] módulo e o [Modelo de pontuação] [ score-model] módulo onde é pontuada. Os resultados são depois exportados do serviço Web através da **produção de serviços Web**.

**Testar um novo serviço Web**

No portal de serviços Web de aprendizagem Azure máquina, clique em **teste** na parte superior da página. Abre a página de **teste** e pode introduzir dados para o serviço. Os campos de entrada apresentados correspondem às colunas que tenham aparecido no dataset de risco de crédito alemão original. 

Introduza um conjunto de dados e, em seguida, clique em **Teste pedido-resposta**.

Os resultados do ensaio serão apresentado no lado direito da página na coluna de saída. 

Quando testar no portal Azure máquina aprendizagem Web Services, pode activar os dados de exemplo que pode utilizar para testar o serviço pedido-resposta. Se criou o serviço Web no computador aprendizagem Studio, os dados de exemplo são retirados dos dados a utilizado para preparar o modelo.

> [AZURE.TIP] A forma como o temos experiência preditiva configurada, todo o resulta do [Modelo de pontuação] de[ score-model] módulo são devolvidos. Isto inclui todos os dados de entrada mais o valor de risco de crédito e a probabilidade de pontuação. Se pretender devolver algo diferente - por exemplo, apenas o crédito o risco de valor -, em seguida, foi possível inserir um [Projecto colunas] [ project-columns] módulo entre [Pontuação modelo] [ score-model] e a **produção de serviços Web** para eliminar colunas não quiser que o serviço Web para devolver. 

## <a name="manage-the-web-service"></a>Gerir o serviço Web

**Gerir um serviço Web clássico**

Depois de ter implementado o serviço Web clássico, pode geri-lo a partir do [portal clássico Azure](https://manage.windowsazure.com).

1. Iniciar sessão no [portal clássico Azure](https://manage.windowsazure.com).
2. No painel Serviços do Microsoft Azure, faça clique sobre **Aprendizagem de máquinas**.
3. Faça clique sobre a área de trabalho.
4. Clique no separador **Serviços Web** .
5. Clique no serviço Web que criámos.
6. Faça clique sobre o ponto final "predefinido".

A partir daqui, pode fazer coisas como monitorizar o desempenho do serviço Web e efectuar modificações de desempenho alterando o concorrente quantos chama o serviço podem processar.
Ainda é possível publicar o serviço Web nos serviços electrónicos do Azure.

Para obter mais detalhes, consulte:

- [Criação de pontos finais](machine-learning-create-endpoint.md)
- [Escala de serviço Web](machine-learning-scaling-webservice.md)
- [Serviço Web de aprendizagem de máquina Azure publicar nos serviços electrónicos do Azure](machine-learning-publish-web-service-to-azure-marketplace.md)

**Gerir um serviço Web no portal Azure máquina aprendizagem Web Services**

Depois de ter implementado o serviço Web, clássica ou novo, pode geri-lo a partir do [portal de serviços Web de aprendizagem Azure máquina](https://servics.azureml.net).

Para monitorizar o desempenho do serviço Web:

1. Iniciar sessão no [portal de serviços Web de aprendizagem Azure máquina](https://servics.azureml.net).
2. Clique em **Serviços Web**.
3. Faça clique sobre o serviço Web.
4. Clique no **Dashboard**.

----------

**Seguinte: [o serviço Web de acesso](machine-learning-walkthrough-6-access-web-service.md)**

[1]: ./media/machine-learning-walkthrough-5-publish-web-service/publish1.png
[2]: ./media/machine-learning-walkthrough-5-publish-web-service/publish2.png
[3]: ./media/machine-learning-walkthrough-5-publish-web-service/publish3.png
[4]: ./media/machine-learning-walkthrough-5-publish-web-service/publish4.png
[5]: ./media/machine-learning-walkthrough-5-publish-web-service/publish5.png
[6]: ./media/machine-learning-walkthrough-5-publish-web-service/publish6.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[project-columns]: https://msdn.microsoft.com/en-us/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/