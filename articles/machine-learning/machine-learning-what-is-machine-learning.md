<properties
    pageTitle="O que é a aprendizagem de máquinas em Azure? | Microsoft Azure"
    description="Explica os conceitos básicos de aprendizagem de máquinas na nuvem, descreve o que pode utilizá-la e define os termos de aprendizagem do computador."
    keywords="o que é a aprendizagem de máquinas, máquina aprendizagem termos, previsão, o que é previsível analytics, operationalize"
    services="machine-learning"
    documentationCenter=""
    authors="cjgronlund"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/17/2016"
    ms.author="cgronlun;tedway;olgali"/>


# <a name="introduction-to-machine-learning-in-the-cloud"></a>Introdução à aprendizagem na nuvem de máquina

## <a name="what-is-machine-learning"></a>O que é a aprendizagem de máquinas?

Aprendizagem de máquinas é uma técnica da ciência de dados que ajuda os computadores a aprender a partir de dados existentes para prever futuros comportamentos, resultados e tendências.  

Estas previsões ou previsões de aprendizagem de máquinas podem tornar aplicações e dispositivos de forma mais inteligente. Quando faz compras online, ajuda a aprendizagem de máquinas a recomendar a outros produtos que lhe poderão interessar com base no que tenha comprado. Quando swiped o seu cartão de crédito, aprendizagem de máquinas compara a transacção numa base de dados de transacções e ajuda a detecta as fraudes. Quando o aspirador robot vacuums uma sala, ajuda a aprendizagem de máquinas-lo a decidir se a tarefa está concluída.

Para obter uma descrição breve, tente a série de vídeo [Ciência de dados para os principiantes](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md). Sem utilizar gíria ou matemática, ciências de dados para os principiantes apresenta aprendizagem de máquinas e orienta-o por um modelo de previsão simples.

## <a name="what-is-machine-learning-in-the-microsoft-azure-cloud"></a>O que é a aprendizagem de máquinas na nuvem Microsoft Azure?

Aprendizagem de máquinas Azure é um serviço de poderosas analytics preditiva baseada na nuvem que lhe permite criar e implementar modelos de previsão como soluções analytics rapidamente. Quando utiliza a nuvem Azure a execução de experiências de aprendizagem do computador ou criar soluções, não tem de adquirir hardware dispendioso ou infra-estrutura.

![O que é a aprendizagem de máquinas? Fluxo de trabalho básico para operationalize analytics preditiva Azure máquina aprendizagem.](./media/machine-learning-what-is-machine-learning/machine-learning-service-parts-and-workflow.png)

Aprendizagem de máquinas Azure não só fornece ferramentas para modelar analytics preventiva, mas também fornece um serviço totalmente gerido que pode utilizar para implementar os modelos de previsão como serviços web prontos a consumir. Aprendizagem de máquinas Azure fornece ferramentas para criar soluções do analytics preditiva completa na nuvem: rapidamente criar, testar, operationalize e gerir modelos de previsão.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="what-is-predictive-analytics"></a>O que é previsível analytics?

Previsão analytics utiliza várias fórmulas matemáticas chamadas algoritmos que analisam dados históricos ou actuais para padrões e tendências para prever os acontecimentos futuros.

Aprendizagem de máquinas Azure é uma forma particularmente eficaz para efectuar a previsão analytics: pode trabalhar a partir de uma biblioteca de prontos para utilização de algoritmos, utilize os algoritmos para criar modelos de um computador ligado à internet e implementar rapidamente a sua solução de previsão. Para obter resultados rapidamente, comece com exemplos prontos a utilizar e soluções na [Galeria de análise de Cortana](http://gallery.cortanaintelligence.com/).

## <a name="build-complete-machine-learning-solutions-in-the-cloud"></a>Compilação concluída máquina soluções na nuvem de aprendizagem

Aprendizagem de máquinas Azure tem tudo o que precisa para criar soluções de previsão analytics na nuvem, de uma biblioteca de algoritmo de grandes dimensões, para um studio para criar modelos, para uma forma fácil de implementar o modelo como um serviço web.

### <a name="machine-learning-studio-create-predictive-models"></a>Máquina aprendizagem Studio: Criar modelos de previsão

No [Computador aprendizagem Studio](machine-learning-what-is-ml-studio.md), pode criar rapidamente modelos de previsão arrastar, largar e de módulos de ligação. É fácil de experimentar diferentes combinações e [Experimente-la gratuitamente](https://studio.azureml.net/?selectAccess=true&o=2).

* Na [Galeria de análise de Cortana](machine-learning-gallery-how-to-use-contribute-publish.md), pode experimentar soluções analytics criadas por outros utilizadores ou contribuir próprias. Publicar perguntas ou comentários sobre experiências para a Comunidade, ou partilhar hiperligações para experiências através de redes sociais, tais como LinkedIn e Twitter.

  ![Tente experiências previsão ou contribuir próprias na Galeria de análise de Cortana Azure](./media/machine-learning-what-is-machine-learning/machine-learning-cortana-intelligence-gallery.png)

* Utilize uma biblioteca grande de [algoritmos de aprendizagem de máquinas e os módulos](https://msdn.microsoft.com/library/azure/f5c746fd-dcea-4929-ba50-2a79c4c067d7) na máquina aprendizagem Studio para começar rapidamente os modelos de previsão. Escolha a partir de experiências de exemplo, R e o Python pacotes e algoritmos de melhor na classe da Microsoft que as empresas como a Xbox e Bing. Expandir módulos Studio com os próprios scripts personalizados de [R](machine-learning-r-quickstart.md) e o [Python](machine-learning-execute-python-scripts.md) .

  ![O que é previsível analytics: exemplo de uma experiência de analytics previsão no Azure máquina aprendizagem Studio](./media/machine-learning-what-is-machine-learning/azure-machine-learning-studio-predictive-score-experiment.png)


### <a name="operationalize-predictive-analytics-solutions-purchase-web-services-or-publish-your-own"></a>Operationalize de soluções de previsão analytics: adquirir serviços web ou publicar o seu próprio

* Comprar serviços web prontos a consumir de [Serviços electrónicos do Microsoft Azure](https://datamarket.azure.com/browse?query=machine+learning), como recomendações, texto Analytics e detecção de anomalias.

* Operationalize os seus modelos de previsão analytics:
    * [Implementar serviços web](machine-learning-publish-a-machine-learning-web-service.md)
    * [Preparar e modelos através de API de retransmissão](machine-learning-retrain-models-programmatically.md)
    * [Gerir os pontos finais de serviço web](machine-learning-create-endpoint.md)
    * [Escala de um serviço web](machine-learning-scaling-webservice.md)
    * [Consumir serviços web](machine-learning-consume-web-services.md)

## <a name="key-machine-learning-terms-and-concepts"></a>Máquina chave termos e conceitos de aprendizagem

Termos de aprendizagem de máquina podem ser confusos. Seguem-se as definições de termos chaves para ajudá-lo. Utilizar comentários seguinte nos vai para falar sobre qualquer outro termo que gostaria de ter definido.

### <a name="data-exploration-descriptive-analytics-and-predictive-analytics"></a>Exploração de dados, analytics descritivo e analytics previsão

**Exploração de dados** é o processo de recolha de informações sobre um conjunto de dados grande e muitas vezes não estruturado para localizar as características de análise centrada. **Extracção de dados** refere-se a exploração de dados automatizada.

**Analytics descritivo** é o processo de análise de um conjunto de dados para resumir o que aconteceu. A grande maioria de analytics de negócio, tais como relatórios de vendas, métricas de web e análise de redes sociais - são descritivo.

**Analytics previsão** é o processo de criação de modelos de dados históricos ou actuais para a previsão de futuros resultados.


### <a name="supervised-and-unsupervised-learning"></a>Aprendizagem controlada e deficiente
 Algoritmos de **aprendizagem Supervised** recebam formação com rotulada dados - por outras palavras, dados composto por exemplos as respostas que pretendia. Por exemplo, um modelo que identifica a utilização fraudulenta de cartão de crédito deverá receber formação de um conjunto de dados com pontos de dados com etiquetas de conhecidos encargos fraudulentos e válidos. A maior parte dos aprendizagem de máquinas é controlada.

 **Unsupervised de aprendizagem** é utilizado em dados com sem etiquetas e o objectivo é encontrar relações nos dados. Por exemplo, poderá encontrar os agrupamentos de factores demográficos de cliente com os hábitos de compras semelhantes.

### <a name="model-training-and-evaluation"></a>Modelo formação e avaliação
Um modelo de aprendizagem do computador é uma abstracção a questão que está a tentar responder ou o resultado que deseja prever. Os modelos são formação e avaliados a partir de dados existentes.

#### <a name="training-data"></a>Dados de formação
Quando prepara um modelo de dados, utilize um conjunto de dados conhecido e efectuar ajustes ao modelo com base nas características de dados para obter a resposta mais precisa. Na aprendizagem de máquinas Azure, um modelo baseia-se de um módulo de algoritmo que processa os dados de formação e módulos funcionais, tal como um módulo de pontuação.

Na aprendizagem controlada, se estiver a formação um modelo de detecção de fraudes, utilize um conjunto de operações que são identificados como fraudulentos ou é válido. Dividir aleatoriamente o conjunto de dados e utilizar a peça para preparar o modelo e peça para testar ou avaliar o modelo.

#### <a name="evaluation-data"></a>Dados da avaliação
Depois de ter um modelo de formação, avalie o modelo utilizando os restantes dados de ensaio. Utilizar dados que já conhece os resultados, isso pode indicar se o modelo prevê com precisão.

## <a name="other-common-machine-learning-terms"></a>Outros termos de aprendizagem comum do computador

* **algoritmo**: um conjunto independente de regras utilizados para resolver problemas através do processamento de dados, matemática ou raciocínio automatizado.
* **detecção de anomalias**: um modelo de sinalizadores de eventos pouco comuns ou valores e ajuda-o a identificar problemas. Por exemplo, detecção de fraudes de cartões de crédito procura compras invulgares.
* **dados categorias**: dados que está organizado por categorias e que podem ser divididas em grupos. Por exemplo um conjunto de dados de categorias para automóveis especificar ano, marca, modelo e preço.
* **classificação**: um modelo para organizar os pontos de dados em categorias com base num conjunto de dados para a categoria agrupamentos já são conhecidos.
* **engenharia de funcionalidade**: O processo de extracção ou seleccionar funcionalidades relacionadas com um conjunto de dados de forma a melhorar o conjunto de dados e melhorar os resultados. Por exemplo, dados de tarifa aérea podem ser reforçados por dias da semana e feriados. Consulte a [funcionalidade de selecção e de engenharia na aprendizagem de máquinas Azure](machine-learning-feature-selection-and-engineering.md).
* **módulo**: um elemento funcional num modelo de máquina aprendizagem Studio, por exemplo, o módulo de introduzir dados que permite introduzir e editar pequenos conjuntos de dados. Um algoritmo também é um tipo de módulo no computador aprendizagem Studio.
* **modelo**: um modelo de aprendizagem controlado é o produto de uma máquina composta por dados de formação, um módulo de algoritmo e módulos funcionais, tal como um módulo de modelo de classificação de experiência de aprendizagem.
* **dados numéricos**: dados que tenha significado que as medições (dados contínuas) ou conta (dados discretos). Também referida como *dados quantitativos*.
* **partição**: O método pelo qual é possível dividir dados em amostras. Para mais informações, consulte [partição e exemplo](https://msdn.microsoft.com/library/azure/dn905960.aspx) .
* **previsão**: uma previsão é uma previsão de um valor ou valores de um modelo de aprendizagem do computador. Também poderá ver a termo "previsível pontuação." No entanto, as pontuações previsíveis não estão a saída final de um modelo. Uma avaliação do modelo a seguir a pontuação.
* **regressão**: um modelo de previsão de um valor com base nas variáveis independentes, por exemplo, para prever o preço de um automóvel com base na respectiva ano e a criar.
* **pontuação**: um valor previsto gerado a partir de um modelo classificação ou regressão formado, utilizando o [módulo de modelo de pontuação](https://msdn.microsoft.com/library/azure/dn905995.aspx) na máquina aprendizagem Studio. Modelos de classificação também devolvem uma classificação para a probabilidade do valor previsto. Depois de ter gerado pontuações de um modelo, pode avaliar a precisão dos modelos utilizando o [módulo de avaliar o modelo](https://msdn.microsoft.com/library/azure/dn905915.aspx).
* **exemplo**: uma parte de um conjunto de dados destinado a ser representativa do conjunto. Amostras podem ser seleccionadas aleatoriamente ou com base nas funcionalidades específicas do conjunto de dados.



## <a name="next-steps"></a>Passos seguintes
Pode aprender as noções básicas do analytics preventiva e utilização de uma [iniciação passo a passo](machine-learning-create-experiment.md) de aprendizagem de máquinas e através da [criação de amostras](machine-learning-sample-experiments.md).  


<!-- Module References -->
[learning-with-counts]: https://msdn.microsoft.com/library/azure/81c457af-f5c0-4b2d-922c-fdef2274413c/
