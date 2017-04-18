<properties
    pageTitle="Importar dados para o computador aprendizagem Studio a partir de um ficheiro local | Microsoft Azure"
    description="Como importar os dados de formação Azure máquina aprendizagem Studio a partir de um ficheiro local."
    keywords="Importar dados, formato de dados, tipos de dados, origens de dados, dados de formação"
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
    ms.date="09/16/2016"
    ms.author="garye;bradsev" />


# <a name="import-your-training-data-into-azure-machine-learning-studio-from-a-local-file"></a>Importar os dados de formação para Azure máquina aprendizagem Studio a partir de um ficheiro local

[AZURE.INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]


Para utilizar os seus próprios dados no máquina aprendizagem Studio, pode carregar um ficheiro de dados antecedência a partir do seu disco rígido local para criar um módulo de conjunto de dados na sua área de trabalho. 


## <a name="import-data-from-a-local-file"></a>Importar dados a partir de um ficheiro local

Pode importar dados a partir de um disco rígido local ao efetuar o seguinte:

1. Clique em **+ Novo** na parte inferior da janela de máquina aprendizagem Studio.
2. Selecione o **conjunto de dados** e **ficheiro LOCAL FROM**.
3. Na caixa de diálogo **carregar um novo conjunto de dados** , procure o ficheiro que pretende carregar
4. Introduza um nome, identificar o tipo de dados e, opcionalmente, introduza uma descrição. Uma descrição é recomendada - permite-lhe gravar todas as características sobre os dados que pretende a lembrar ao utilizar os dados no futuro.
5. A caixa de verificação **Esta é a nova versão de um conjunto de dados existente** permite-lhe atualizar um conjunto de dados existente por novos dados. Basta clicar esta caixa de verificação e, em seguida, introduza o nome de um conjunto de dados existente.

Durante a carregar, verá uma mensagem que está a ser carregado o ficheiro. Carregar tempo irá dependem o tamanho dos seus dados e a velocidade da sua ligação ao serviço.
Se souber que o ficheiro irá demorar muito tempo, que pode fazer outras coisas dentro de máquina aprendizagem Studio enquanto espera. No entanto, fechar o browser irão provocar o carregamento de dados a falha.

Assim que os seus dados são carregados, é armazenada num módulo do conjunto de dados e está disponível para qualquer experiência na área de trabalho.
Quando está a editar uma experiência, pode encontrar os conjuntos de dados que criou na lista de **Conjuntos de dados meus** na lista de **Conjuntos de dados guardado** na paleta de módulo. Pode arrastar e largar o conjunto de dados para a tela de experiência quando pretender utilizar o conjunto de dados para a aprendizagem automática e de análise mais.



