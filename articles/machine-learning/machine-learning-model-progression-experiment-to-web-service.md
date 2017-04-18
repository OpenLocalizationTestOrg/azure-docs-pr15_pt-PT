<properties
    pageTitle="Como um modelo de aprendizagem máquina avança a partir de uma experiência para um serviço Web operationalized | Microsoft Azure"
    description="Uma descrição geral do mecânica de como o seu progride de modelo de formação de máquina Azure a partir de um desenvolvimento experimentar a um serviço Web operationalized."
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


# <a name="how-a-machine-learning-model-progresses-from-an-experiment-to-an-operationalized-web-service"></a>Como um modelo de aprendizagem máquina avança a partir de uma experiência para um serviço Web operationalized

Azure Studio de aprendizagem automática fornece uma tela interativa que permite-lhe desenvolver, executar, testar e iteramos uma ***experimentar*** que representa um modelo de análise de previsão. Existem uma grande variedade de módulos que pode:

* Dados de entrada para a sua experiência
* Manipular os dados
* Dar formação a um modelo com algoritmos de aprendizagem automática
* O modelo de pontuação
* Avaliar os resultados
* Valores de finais de saída

Quando estiver satisfeito com a sua experiência, pode implementá-lo como um ***serviço Web de aprendizagem de máquina Azure clássico*** ou um ***serviço Web de aprendizagem de máquina Azure novo*** para que os utilizadores possam enviá-la novos dados e receber resultados anterior.

Neste artigo, vamos dar uma descrição geral do mecânica de como o seu progride de modelo de aprendizagem automática a partir de um desenvolvimento experimentar a um serviço Web operationalized.

>[AZURE.NOTE] Existem outras formas para desenvolver e implementar modelos de aprendizagem automática, mas este artigo foca como utilizar máquina de formação Studio. Para um debate de como criar um serviço Web aspeto do Office Clássico com R, consulte a mensagem [criar e implementar o aspeto do Office Web Apps utilizando RStudio e Azure ML](http://blogs.technet.com/b/machinelearning/archive/2015/09/25/build-and-deploy-a-predictive-web-app-using-rstudio-and-azure-ml.aspx)do blogue.

Enquanto Azure máquina aprendizagem Studio foi concebido para o ajudar a desenvolver e implementar um *modelo de análise de previsão*, é possível utilizar Studio para desenvolver uma experiência que não inclui um modelo de análise de previsão. Por exemplo, uma experiência poderá apenas os dados de entrada, manipulá-lo e, em seguida, os resultados de saída. Tal como uma experiência de análise de previsão, pode implementar esta experiência não previsão como um serviço Web, mas é um processo simples, porque a experiência não está formação ou um modelo de formação de máquina de pontuação. Embora não seja típicas para utilizar Studio desta forma, podemos irá inclui-lo debate para que pode apresentamos uma explicação completa do funcionamento Studio.

## <a name="developing-and-deploying-a-predictive-web-service"></a>Desenvolver e implementar um serviço Web aspeto do Office

Eis as fases que se segue uma solução típica à medida que desenvolvemos e implementá-lo utilizando máquina aprendizagem Studio:

![Fluxo de implementação](media\machine-learning-model-progression-experiment-to-web-service\model-stages-from-experiment-to-web-service.png)

*Figura 1 - fases de um modelo de análise de previsão típica*

### <a name="the-training-experiment"></a>A experiência de formação

***Formação experimentar*** é a fase inicial de desenvolver o serviço Web na máquina aprendizagem Studio. É o objetivo da experiência de formação que lhe fornece um local para desenvolver, teste, iteramos e eventualmente preparar uma modelo de aprendizagem automática. Pode até mesmo preparar vários modelos em simultâneo como procure a melhor solução, mas quando terminar de experiências irá selecionar um único formado do modelo e eliminar o resto da experiência. Para obter um exemplo de desenvolvimento de uma análise do aspeto do Office experimentar, consulte o artigo [desenvolver uma solução de análise de aspeto do Office para avaliação de riscos de crédito no Azure máquina aprendizagem](machine-learning-walkthrough-develop-predictive-solution.md).

### <a name="the-predictive-experiment"></a>A experiência de aspeto do Office

Assim que tiver um modelo de formação na sua experiência de formação, clique em **Configurar o serviço Web** e selecione o **Serviço Web aspeto do Office** no computador Studio de aprendizagem para iniciar o processo de converter a sua experiência de formação para uma ***experiência de aspeto do Office***. O objetivo da experiência aspeto do Office é utilizar o seu modelo de formação para pontuação novos dados, com o propósito de eventualmente a tornar-se operacionalizada como um serviço Azure Web.

Esta conversão é feito através dos seguintes passos:

-   Converter o conjunto de módulos utilizado para formação num módulo único e guardá-lo como um modelo de formação

-   Eliminar qualquer módulos estranhos não relacionados com a pontuação

-   Adicionar portas de entrada e saídas que irá utilizar o serviço Web eventual

Poderão existir mais alterações que pretende efetuar a preparar a sua experiência de aspeto do Office implementar o como um serviço Web. Por exemplo, se pretender que o serviço Web para apenas um subconjunto dos resultados de saída, pode adicionar um módulo de filtragem antes da porta de saída.

Este processo de conversão, a experiência de formação não é eliminada. Quando o processo for concluído, tem dois separadores no Studio: uma para a experiência de formação e outra para a experiência de aspeto do Office. Desta forma pode efetuar alterações a experiência formação antes de implementar o seu serviço Web e reconstruir a experiência de aspeto do Office. Ou pode guardar uma cópia da experiência de formação para começar a outra linha de experimentação.

>[AZURE.NOTE] Quando clica em **Aspeto do Office serviço Web** começar um processo automático para converter a sua experiência de formação para uma experiência de aspeto do Office e isto funciona bem na maioria dos casos. Se a sua experiência de formação é complexa (por exemplo, ter vários caminhos de cursos de formação juntar), poderá preferir fazer esta conversão manualmente. Para mais informações, consulte o artigo [converter uma experiência de formação de aprendizagem automática para uma experiência de aspeto do Office](machine-learning-convert-training-experiment-to-scoring-experiment.md).

### <a name="the-web-service"></a>O serviço Web

Quando estiver satisfeito que está pronta a experiência de aspeto do Office, pode implementar o seu serviço de como serviço Web clássico ou um serviço Web novos com base no Gestor de recursos do Azure. Para operationalize o seu modelo ao implementá-lo como um *serviço Web de aprendizagem máquina clássico*, clique em **Implementar o serviço Web** e selecione **Implementar o serviço Web [clássica]**. Para implementar como *serviço Web de aprendizagem do novo computador*, clique em **Implementar o serviço Web** e selecione **Implementar [novo] serviço Web**. Os utilizadores agora podem enviar dados para o seu modelo utilizando o serviço Web REST API e receber novamente os resultados. Para mais informações, consulte o artigo [como consumir um serviço Web de aprendizagem do Azure máquina que tenha sido implementado a partir de uma experiência de aprendizagem automática](machine-learning-consume-web-services.md).

## <a name="the-non-typical-case-creating-a-non-predictive-web-service"></a>As maiúsculas/minúsculas que não sejam típica: criar um serviço Web não previsão

Se a sua experiência não formar um modelo de análise de previsão, em seguida, que não precisa de criar uma experiência de formação e uma experiência de pontuação - existe apenas uma experiência e pode implementá-lo como um serviço Web. Máquina aprendizagem Studio Deteta se a sua experiência contém um modelo de aspeto do Office através da análise módulos que tenha utilizado.

Depois de ter sujeita a iteração na sua experiência e estiver satisfeito com o mesmo:

1.  Clique em **Configurar o serviço Web** e selecione **Reciclagem serviço Web** - nós de entrada e saídas são automaticamente adicionados

2.  Clique em **Executar**

3. Clique em **Implementar o serviço Web** e selecione **Implementar o serviço Web [clássica]** ou **Implementar [novo] serviço Web** consoante o ambiente ao qual pretende implementar.

O serviço Web é implementado e pode aceder e gerir-tal como um serviço Web aspeto do Office.

## <a name="updating-your-web-service"></a>Atualizar o seu serviço Web

Agora que já implementado a sua experiência como um serviço Web, o que acontece se necessita de atualizá-lo?

Que depende o que necessita de atualizar:

**Pretende alterar a entrada ou saída ou que pretende modificar a forma como o serviço Web manipula os dados**

Se não está a alterar o modelo, mas apenas pretende alterar a forma como o serviço Web processa dados, pode editar a experiência de aspeto do Office e, em seguida, clique em **Implementar o serviço Web** e selecione **Implementar o serviço Web [clássica]** ou **Implementar [novo] serviço Web** novamente. O serviço Web está parado, a experiência de aspeto do Office actualizada é implementada e o serviço Web for reiniciado.

Eis um exemplo: suponha que a sua experiência de previsão devolve toda a linha de dados de entrada com o resultado previsto. Pode decidir que pretende que o serviço Web para devolvem apenas o resultado. Por isso, pode adicionar um módulo de **Colunas de projeto** na experiência aspeto do Office, para a direita antes da porta de saída, excluir colunas que não seja o resultado. Quando clica em **Implementar o serviço Web** e selecione novamente a **Implementar o serviço Web [clássica]** ou **Implementar [novo] serviço Web** , o serviço Web é atualizado.

**Pretende Reciclagem o modelo com novos dados**

Se pretende manter o seu modelo de aprendizagem automática, mas que gostaria de reciclagem-la com os novos dados, tem duas opções:

1.  A **Reciclagem do modelo enquanto o serviço Web estiver em execução** - se pretender Reciclagem o seu modelo enquanto o aspeto do Office Web serviço está em execução, pode fazê-lo ao efetuar algumas alterações para a experiência de formação para o tornar uma ***experiência readaptação***, em seguida, pode implementá-lo como um **serviço *web readaptação* **. Para obter instruções sobre como fazer isto, consulte o artigo [modelos de formação de máquina de retransmissão através de programação](machine-learning-retrain-models-programmatically.md).

2.  **Voltar para a experiência de formação original e utilizar dados de diferentes formação para desenvolver o seu modelo** - a experiência de aspeto do Office é associado ao serviço Web, mas a experiência de formação não está ligada diretamente desta forma. Se modificar a experiência de formação original e clique em **Configurar o serviço Web**, irá criar um *Novo* aspeto do Office experimentar que, quando implementado, irá criar um *Novo* serviço Web. Apenas-não atualiza o serviço Web original.

    Se precisar de modificar a experiência de formação, abra-o e clique em **Guardar como** para fazer uma cópia. Isto irá manter intactos a experiência de formação original, experiência aspeto do Office e o serviço Web. Agora pode criar um novo serviço Web com as suas alterações. Depois de ter implementado o novo serviço Web, em seguida, pode decidir se pretende parar o serviço Web anterior ou mantê-lo a juntamente com um novo.

**Pretende dar formação a um modelo diferente**

Se pretende efetuar alterações a sua experiência de aspeto do Office original, tal como selecionar um algoritmo de aprendizagem máquina diferente, está a tentar um método de formação diferente, etc., em seguida, que tem de seguir o segundo procedimento descrito acima para a Reciclagem do seu modelo: abrir a experiência de formação, clique em **Guardar como** fazer uma cópia e, em seguida, comece para baixo o novo caminho do desenvolver o seu modelo , criar a experiência de aspeto do Office e implementar o serviço web. Isto vai criar uma nova Web serviço não relacionados ao original, -, pode decidir que uma ou ambas as, para manter em execução.

## <a name="next-steps"></a>Próximos passos

Para obter mais detalhes sobre o processo de desenvolver e experiência, consulte os artigos seguintes:

-   converter a experiência - [converter uma experiência de formação de aprendizagem automática para uma experiência de aspeto do Office](machine-learning-convert-training-experiment-to-scoring-experiment.md)

-   implementar o serviço Web - [Implementar um serviço web de aprendizagem de máquina do Azure](machine-learning-publish-a-machine-learning-web-service.md)

-   Reciclagem modelo - [modelos de formação de máquina de retransmissão através de programação](machine-learning-retrain-models-programmatically.md)

Para obter exemplos de todo o processo, consulte:

-   [Tutorial do máquina formação: criar a sua primeira experiência no Azure máquina de formação Studio](machine-learning-create-experiment.md)

-   [Tutorial: Desenvolver uma solução de análise de aspeto do Office para avaliação de riscos de crédito no Azure máquina aprendizagem](machine-learning-walkthrough-develop-predictive-solution.md)