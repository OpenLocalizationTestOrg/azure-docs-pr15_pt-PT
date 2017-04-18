<properties
    pageTitle="Criar um modelo com a IU Recommnendations | Microsoft Azure"
    description="Recomendações de máquina Learning Azure - criar um modelo com IU de recomendações"
    services="cognitive-services"
    documentationCenter=""
    authors="luiscabrer"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cognitive-services"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/11/2016"
    ms.author="luisca"/>

# <a name="building-a-model-with-the-recommendations-ui"></a>Criar um modelo com IU de recomendações

Este documento é um guia passo a passo. Sobre nosso objetivo é guiá-lo os passos necessários para formar um modelo com a [IU recomendações](https://recommendations-portal.azurewebsites.net/).
Percorrer o exercício permite-lhe compreender o processo para criar um modelo de antes de o fazer através de programação. -Lo também familiarizes com IU, que é útil quando começa a utilizar o serviço.

Este exercício demora cerca de 30 minutos.

<a name="Step1"></a>
## <a name="step-1---sign-in-to-the-recommendations-ui"></a>Passo 1 - início de sessão nas recomendações IU ##

1. Se ainda não o tiver o fez, tem de [inscrição](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1) para uma nova subscrição de [Recomendações API](https://www.microsoft.com/cognitive-services/en-us/recommendations-api) . Pode inscrever-se para o serviço no **Azure** na [http://portal.azure.com/](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1) e inicie sessão com a sua conta Azure. Obter instruções detalhadas sobre o processo de inscrição são fornecidas na *tarefa 1* do [Guia de introdução](cognitive-services-recommendations-quick-start.md) 

1. Após ter obtido uma **tecla** para a sua subscrição de recomendações API, aceda à [IU recomendações](https://recommendations-portal.azurewebsites.net/). 

1. Inicie sessão no portal ao introduzir a chave no campo de **Chave da conta** e, em seguida, clique no botão de **início de sessão** .

    ![Recomendações de IU: Início de sessão na caixa de diálogo.][reco_signin]


<a name="Step2"></a>
## <a name="step-2---lets-gather-some-training-data"></a>Passo 2 - vamos reunir alguns dados de formação ##

Antes de poder criar um construir, o motor tem dois blocos de informações: um ficheiro de catálogo e um conjunto de ficheiros de utilização. 

O ficheiro de catálogo contém informações sobre os itens que está a oferta ao seu cliente. Um ficheiro de utilização contém informações sobre como são utilizadas esses itens ou as transações da sua empresa.

Normalmente, teria de consulta a base de dados de arquivo para estas partes de informações. Hoje em dia, fornecemos alguns dados de exemplo para si, de modo a que pode aprender a utilizar a API recomendações.

Pode transferir os dados a partir do [http://aka.ms/RecoSampleData](http://aka.ms/RecoSampleData). Copiar e descompactar o ficheiro de **Books.Zip** para uma pasta no seu computador local. Por exemplo, **c:\data**.

Podem encontrar informações detalhadas sobre o esquema dos ficheiros catálogo e a utilização no artigo [recolher dados de formar o seu modelo](cognitive-services-recommendations-collecting-data.md) .
 
Para este exercício, vamos para trabalhar com um ficheiro pequeno, para que não tenha muito de esperar muito tempo de cursos de formação. Se quiser experimentar um ficheiro mais real, também podemos ter colocado **MsStoreData.zip** que contém as transações de exemplo a partir da Store Microsoft na [mesma localização](http://aka.ms/RecoSampleData).

<a name="Step3"></a>
## <a name="step-3---create-a-project-and-upload-catalog-and-usage-data"></a>Passo 3 - criar um projeto e carregar catálogo e a utilização de dados ##

Após iniciar sessão no [Recomendações IU](https://recommendations-portal.azurewebsites.net/), irá ver a página de projetos. Se tiver criado anteriormente quaisquer projectos, deverá vê-los aqui.
Um projeto (também conhecido como *um modelo* na referência API) é um contentor para os seus dados catálogo e a utilização. Pode criar vários *constrói* dentro do projeto. Vamos irá guiá-lo durante o processo nos próximos passos.

1. Para criar um novo projeto, escreva o nome na caixa de texto (algo como "MyFirstModel" funcionarão) e clique em **Adicionar projeto**.
 
    ![Recomendações de IU: Página de projectos.][reco_projects]

1. Assim que o projeto é criado, clique no botão **Procurar o ficheiro** na secção **Adicionar um ficheiro de catálogo** . Carregue o catálogo obtido no passo 2. Se tiver guardado na *c:\data*, tem de navegar para essa pasta.

    ![Recomendações de IU: Adicionar dados a um projeto.][reco_firstmodel]

1. Depois de é carregado o catálogo, clique no botão **Procurar o ficheiro** na secção que **Adicionar ficheiros de utilização** . Adicione o ficheiro usage_large.txt.

> **O que acontece se tiver um ficheiro de dados de utilização grande?**
>
> Apenas a utilização ficheiros menores do que pode ser carregado 200 MB. Desta forma que sugerem, o sistema pode conter para cima para património de 2 GB de dados da transação, para que possa carregar mais de um ficheiro, se necessário.
> Poderá não ter que quantidade de dados para gerar um bom modelo, não é apenas o tamanho dos dados que é importante, mas a qualidade dos dados. É comuns para ver a utilização dados onde a maioria das transações estão apenas no alguns itens mais populares e não existe "pouco sinal" para outros itens.

<a name="Step4"></a>
## <a name="step-4---lets-do-some-training"></a>Passo 4 - Vamos fazê-lo algumas formação! ##

Agora que carregou o catálogo e dados de utilização, podemos está prontos para preparar o motor de modo a que pode obter informações sobre padrões a partir dos nossos dados.

1.  Clique no botão **Compilar novo** .

1.  Selecione **recomendações** como o tipo de compilação. Repare que suportamos uma classificação construir um FBT (frequentemente comprou em conjunto) criar tipos de também.

    ![Recomendações de IU: A caixa de diálogo Criar.][reco_build_dialog.png]


    Construir uma FBT permite-lhe identificar padrões para os produtos que são normalmente comprado/consumidas na mesma operação.
    Criar uma classificação é utilizado para identificar funcionalidades de interesse. 
    Vamos não entram muito profundas FBT ou classificação constrói in este seminário, mas se estiver interessado deverá dar saída [criar tipos e página de documentação de qualidade do modelo](cognitive-services-recommendations-buildtypes.md).

1. Clique no botão **Compilar** . O processo de criação demora cerca de cinco minutos, se estiver a utilizar os dados de livros. Demora mais tempo no maiores conjuntos de dados.

<a name="Step5"></a>
## <a name="step-5---lets-find-out-what-the-machine-learned"></a>Passo 5 - a saber o que aprendeu a máquina vamos! ##

Depois da compilação está concluída, irá reparar uma nova compilação na lista de compilações. Esta compilação pode ser consultada para obter recomendações item e utilizador.

1. Depois da compilação está concluída, clique em **pontuação**. Esta opção permite-lhe reproduzir com o modelo e ver os itens que são recomendados.

    ![Recomendações IU: Botão de pontuação][reco_score_button]

1. Selecione um item para ver quais os itens são devolvidos como recomendações para esse item. Repare que se não estiverem suficiente transações para prever uma recomendação para um item específico, o sistema não irá devolver qualquer recomendações para esse item.  Se tiver um item que não devolve nenhum recomendações por algum motivo, experimente pontuação outros itens.

<a name="Step6"></a>
## <a name="step-6---next-steps"></a>Passo 6 - passos seguintes ##
Parabéns! possui formação um modelo e, em seguida, tem recomendações nesse modelo.  IU de recomendações é uma ferramenta útil, que permite-lhe ver o estado dos seus projetos e constrói. 

Agora que tem um modelo, poderá querer saber como efetuar os passos acima através de programação. Para obter informações sobre como ligar a API através de programação, podemos convidar para verificar se a [Referência da API recomendações](http://go.microsoft.com/fwlink/?LinkId=759348) e transferir a [Aplicação de exemplo recomendações](http://go.microsoft.com/fwlink/?LinkID=759344).


[reco_signin]:../media/cognitive-services/reco_signin.PNG
[reco_projects]:../media/cognitive-services/reco_projects.PNG
[reco_firstmodel]:../media/cognitive-services/reco_firstmodel.png
[reco_build_dialog.png]:../media/cognitive-services/reco_build_dialog.png
[reco_score_button]:../media/cognitive-services/reco_score_button.png
