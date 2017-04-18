<properties
    pageTitle="Passo 2: Carregar dados para uma experiência de aprendizagem máquina | Microsoft Azure"
    description="Passo 2 da desenvolver instruções solução aspeto do Office: carregar armazenados dados públicos para Azure máquina aprendizagem Studio."
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016" 
    ms.author="garye"/>


# <a name="walkthrough-step-2-upload-existing-data-into-an-azure-machine-learning-experiment"></a>Tutorial passo 2: Carregar dados existentes para uma experiência de formação de máquina do Azure

Este é o segundo passo do tutorial, [desenvolver uma solução de análise de aspeto do Office no Azure máquina aprendizagem](machine-learning-walkthrough-develop-predictive-solution.md)


1.  [Criar uma área de trabalho de aprendizagem automática](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  **Carregar dados existentes**
3.  [Criar uma nova experiência](machine-learning-walkthrough-3-create-new-experiment.md)
4.  [Dar formação e avaliar os modelos](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  [Implementar o serviço web](machine-learning-walkthrough-5-publish-web-service.md)
6.  [Aceder ao serviço web](machine-learning-walkthrough-6-access-web-service.md)

----------

Para desenvolver um modelo de aspeto do Office para o risco de crédito, precisamos de dados que pode utilizamos para formar e testar o modelo. Para este tutorial, vamos utilizar o "UCI Statlog (dados de crédito alemão) conjunto de dados" a partir do repositório UCI máquina aprendizagem. Pode localizá-lo aqui:  
<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">http://Archive.ICS.uci.edu/ml/DataSets/Statlog+(German+Credit+data)</a>

Vamos utilizar o ficheiro denominado **german.data**. Transfira este ficheiro no seu disco rígido local.  

Este conjunto de dados contiver linhas de 20 variáveis para os últimos 1000 candidatos para crédito. Estas 20 variáveis representam vector de funcionalidade o conjunto de dados, que fornece um características identificativa para cada candidato de crédito. Uma coluna adicional em cada linha representa o risco de crédito calculado o candidato, com os 700 candidatos identificados como um risco de crédito baixa e 300 como um alto risco.

O Web site UCI fornece uma descrição dos atributos da vector funcionalidade, que incluem informações financeiras, histórico de crédito, estado de emprego e informações pessoais. Para cada candidato, uma classificação binária foi determinado que indica se são uma baixa ou alto risco de crédito.  

Vamos utilizar estes dados para formar um modelo de análise de aspeto do Office. Quando podemos terminados, o nosso modelo deve ser conseguirá aceitar informações para o novos indivíduos e prever se forem um risco de crédito alta ou baixa.  

Eis uma torção interessante. A descrição do dataset explica que classifique incorrectamente uma pessoa como um risco de crédito baixa quando são realmente um risco de crédito alta está 5 vezes mais dispendioso instituição financeira que classifique incorrectamente um risco de crédito baixa como alta. Uma forma simple para ter em consideração no nossa experiência este é duplicar (5 vezes) essas entradas que representam alguém com um risco de crédito alta. Em seguida, se o modelo de misclassifies um risco de crédito alta como baixa,-executará essa classificação 5 vezes, uma vez para cada duplicado. Isto irá aumentar o custo deste erro nos resultados de formação.  

##<a name="convert-the-dataset-format"></a>Converter o formato de conjunto de dados
O conjunto de dados original utiliza um formato separados em branco. Studio de aprendizagem automática funciona melhor com um ficheiro de valores separados por vírgulas (CSV), para que recomendamos irá converter o conjunto de dados, substituindo espaços por vírgulas.  

Existem várias formas para converter estes dados. Uma forma é utilizando o seguinte comando Windows PowerShell:   

    cat german.data | %{$_ -replace " ",","} | sc german.csv  

Outra forma é utilizando o comando de sed Unix:  

    sed 's/ /,/g' german.data > german.csv  

Em ambos os casos, criámos uma versão separados por vírgulas os dados num ficheiro denominado **german.csv** , vamos utilizar no nossa experiência.

##<a name="upload-the-dataset-to-machine-learning-studio"></a>Carregar o conjunto de dados para máquina aprendizagem Studio

Assim que foi convertidos os dados para o formato CSV, precisamos de carregá-lo para máquina aprendizagem Studio. Para mais informações sobre como começar com o computador aprendizagem Studio, consulte o artigo [Microsoft Azure máquina aprendizagem Studio casa](https://studio.azureml.net/).

1.  Abra Studio ([https://studio.azureml.net](https://studio.azureml.net)) de aprendizagem automática. Quando lhe for pedido para iniciar sessão, utilize a conta que especificou como o proprietário da área de trabalho.
1.  Clique no separador **Studio** na parte superior da janela.
1.  Clique em **+ Novo** na parte inferior da janela.
1.  Selecione o **conjunto de dados**.
1.  Selecione o **ficheiro LOCAL FROM**.
1.  Na caixa de diálogo **carregar um novo conjunto de dados** , clique em **Procurar** e localize o ficheiro de **german.csv** que criou.
1.  Introduza um nome para o conjunto de dados. Para este tutorial, irá chamamos-"Dados de cartão de crédito UCI alemão".
1.  Para tipo de dados, selecione **genérico ficheiro CSV com sem cabeçalho (. nh.csv)**.
1.  Se quiser, adicione uma descrição.
1.  Clique em **OK**.  

![Carregar o conjunto de dados][1]  


Isto envia os dados num módulo do conjunto de dados que pode utilizamos numa experiência.

> [AZURE.TIP] Para gerir conjuntos de dados que carregou para Studio, clique no separador de **conjuntos de dados** para a esquerda da janela do Studio.

Para mais informações sobre como importar vários tipos de dados para uma experiência, consulte o artigo [Importar dados formação para o Azure máquina aprendizagem Studio](machine-learning-data-science-import-data.md).

**Seguinte: [criar uma nova experiência](machine-learning-walkthrough-3-create-new-experiment.md)**

[1]: ./media/machine-learning-walkthrough-2-upload-data/upload1.png
