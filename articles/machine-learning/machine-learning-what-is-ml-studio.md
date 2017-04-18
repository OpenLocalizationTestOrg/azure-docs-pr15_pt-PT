<properties 
    pageTitle="O que é o Azure máquina aprendizagem Studio? | Microsoft Azure"
    description="Descrição geral do Azure ML Studio, uma ferramenta de arrastar e largar para rapidamente criar modelos de uma biblioteca de prontos a utilizar dos algoritmos e módulos."
    keywords="Azure máquina de formação, azure ml, ml studio"
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
    ms.topic="get-started-article"
    ms.date="09/09/2016"
    ms.author="garye"/>

# <a name="what-is-azure-machine-learning-studio"></a>O que é o Azure máquina aprendizagem Studio?

Studio de formação do Microsoft Azure máquina é uma ferramenta de colaboração, arrastar e largar, que pode utilizar para criar, testar e implementar soluções de análise de aspeto do Office nos seus dados. Máquina aprendizagem Studio publica modelos como serviços web que podem ser facilmente consumidos por aplicações personalizadas ou ferramentas de BI como o Excel.

Máquina aprendizagem Studio é sempre que satisfaçam ciência de dados, a análise de aspeto do Office, recursos na nuvem e os seus dados.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="the-machine-learning-studio-interactive-workspace"></a>A área de trabalho interativa máquina aprendizagem Studio

Para desenvolver um modelo de análise de previsão, é normalmente utiliza os dados a partir de uma ou mais origens, transforma e analisar os dados através de vários manipulação de dados e funções de estatística e gera um conjunto de resultados. Desenvolver um modelo, como este é um processo iterativo. Como modificar nas várias funções e os respetivos parâmetros, os resultados da sua convergirem até estar satisfeito que tem um modelo de formação e eficientes.

**Azure máquina aprendizagem Studio** dá-lhe uma área de trabalho interativa e visual para construir facilmente, testar e iteramos sobre um modelo de análise de previsão. Pode arrastar e largar ***conjuntos de dados*** e análise ***módulos*** para uma tela interativo, ligá-las em conjunto para formar uma ***experimentar***, que são executadas no computador aprendizagem Studio. Para iteramos sobre a estrutura de modelo, edite a experiência, guarde uma cópia se pretender e executá-la novamente. Quando estiver pronto, pode converter o seu ***experimentar de formação*** para uma ***experiência de aspeto do Office***e, em seguida, publicá-lo como um ***serviço web*** para que o seu modelo pode ser acedido por outras pessoas.

>[AZURE.TIP] Para transferir e imprimir um diagrama que apresenta uma visão geral das capacidades do máquina aprendizagem Studio, consulte o [diagrama de descrição geral das capacidades do Azure máquina aprendizagem Studio](machine-learning-studio-overview-diagram.md).

Não existe sem ser necessária programação, basta visualmente ligar conjuntos de dados e módulos para construir o seu modelo de análise de previsão.

![Diagrama de ML Studio Azure: experiências de criar, ler os dados de várias origens, escrever dados visita, escrever modelos.][ml-studio-overview]

## <a name="get-started-with-machine-learning-studio"></a>Introdução ao máquina aprendizagem Studio

Quando introduz pela primeira vez [Máquina aprendizagem Studio](https://studio.azureml.net) , verá a **Home** page do. A partir daqui pode ver documentação, vídeos, webinars e localizar outros recursos útil.

Existem três separadores na parte superior: **Home** (onde iniciar), **Studio**e o **Gallery**.

### <a name="studio"></a>Studio

Clique no separador **Studio** e ser-lhe-á pedido para iniciar sessão a sua conta Microsoft, ou a sua conta escolar ou profissional. Assim que iniciar sessão, verá os seguintes separadores no lado esquerdo:

- **PROJETOS** - coleções de experiências, conjuntos de dados, blocos de notas e outros recursos que representa um único projecto
- **EXPERIÊNCIAS** - experiências que foram criados, executar e guardados como rascunhos
- **Serviços WEB** - serviços Web que tenham implementado a partir do seu experiências
- **Blocos de notas** - Jupyter blocos de notas criados por si
- **Conjuntos de dados** - conjuntos de dados que carregou para Studio
- **Modelos de formação** - modelos que tenham formação em experiências e guardado no Studio
- **Definições** - um conjunto de definições que pode utilizar para configurar a sua conta e recursos.

### <a name="gallery"></a>Galeria

Clique no separador **Galeria** e vai ser encaminhado para a Galeria de informações da empresa Cortana. A galeria é um local onde uma Comunidade de cientistas de dados e os programadores pode partilhar as soluções criadas utilizando os componentes do conjunto de informações da empresa Cortana.

Para mais informações sobre o Gallery, consulte o artigo [partilhar e Descubra soluções na Galeria de análise de Cortana](machine-learning-gallery-how-to-use-contribute-publish.md).

## <a name="components-of-an-experiment"></a>Componentes de uma experiência

Uma experiência consiste em conjuntos de dados que fornece dados à analytical módulos, que pode ligar-se em conjunto para construir um modelo de análise de previsão. Especificamente, uma experiência válida tem estas características:

- A experiência tem pelo menos um conjunto de dados e um módulo
- Conjuntos de dados poderão estar ligados apenas ao módulos
- Módulos podem ser ligados para conjuntos de dados ou outros módulos
- Todas as portas de entrada para módulos tem de ter alguns ligação para o fluxo de dados
- Todos os necessários parâmetros para cada módulo tem de estar definidos

Pode criar uma experiência de raiz, ou pode utilizar uma experiência de exemplo existente como um modelo. Para mais informações, consulte o artigo [experiências de exemplo de utilização para criar novas experiências](machine-learning-sample-experiments.md).

Para obter um exemplo de criação de uma experiência simple, consulte o artigo [criar uma experiência simple no Azure máquina aprendizagem Studio](machine-learning-create-experiment.md).

Para mais completa obter instruções sobre criar uma solução de análise de aspeto do Office, consulte o artigo [desenvolver uma solução de aspeto do Office com o Azure máquina aprendizagem](machine-learning-walkthrough-develop-predictive-solution.md).

### <a name="datasets"></a>Conjuntos de dados

Um conjunto de dados é os dados que foi carregados para máquina aprendizagem Studio para que possa ser utilizado no processo de modelação. Um número de conjuntos de dados de exemplo está incluído no máquina aprendizagem Studio que pode experimentar e pode carregar mais conjuntos de dados à medida que são necessários. Eis alguns exemplos de conjuntos de dados incluídos:

- **Dados MPG para vários automóveis** - milhas por valores de galão (MPG) para automóveis identificadas por números de cilindros, potência, etc.
- **Dados de cancro peito** - dados de diagnóstico peito cancro.
- **Dados da incêndios floresta** - tamanhos de fogo de floresta em Portugal da Nordeste.

À medida que constrói uma experiência pode escolher a partir da lista de conjuntos de dados disponíveis para a esquerda da tela.

Para obter uma lista de conjuntos de dados de exemplo incluídos no máquina aprendizagem Studio, consulte o artigo [utilizar os conjuntos de dados de exemplo no Azure máquina aprendizagem Studio](machine-learning-use-sample-datasets.md).

### <a name="modules"></a>Módulos

Um módulo é um algoritmo que pode realizar dos seus dados. Máquina aprendizagem Studio tem um número de módulos que se situa entre funções de penetração dados formação, pontuação e processos de validação. Eis alguns exemplos de módulos incluídos:

- [Converter ARFF] [ convert-to-arff] -converte um conjunto de dados .NET serializado para o formato de ficheiro de atributo relação (ARFF).
- [Calcular estatísticas do ensino básico] [ elementary-statistics] -calcula estatísticas do ensino básico como média, o desvio-padrão, etc.
- [Regressão linear] [ linear-regression] -cria um modelo de regressão linear gradação online com base em descida.
- [Modelo de pontuação] [ score-model] -ganha um modelo de classificação ou regressão qualificado.

À medida que constrói uma experiência pode escolher a partir da lista de módulos disponíveis para a esquerda da tela.  

Um módulo poderá ter um conjunto de parâmetros que pode utilizar para configurar algoritmos interno o módulo. Quando seleciona um módulo na tela, parâmetros o módulo são apresentados no painel **Propriedades** à direita da tela. Pode modificar os parâmetros desse painel para ajustar o seu modelo.

Para alguns ajuda navegar através da biblioteca grande dos algoritmos de aprendizagem máquina disponíveis, consulte o artigo [como escolher algoritmos para conhecer o Microsoft Azure máquina](machine-learning-algorithm-choice.md).

## <a name="deploying-a-predictive-analytics-web-service"></a>Implementar um serviço de web analytics aspeto do Office

Assim que o seu modelo de análise de aspeto do Office estiver pronto, pode implementá-lo como um serviço web a partir do computador aprendizagem Studio. Para obter mais detalhes sobre este processo, consulte o artigo [Implementar um serviço web de aprendizagem do Azure máquina](machine-learning-publish-a-machine-learning-web-service.md).

[ml-studio-overview]:./media/machine-learning-what-is-ml-studio/azure-ml-studio-diagram.jpg

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
