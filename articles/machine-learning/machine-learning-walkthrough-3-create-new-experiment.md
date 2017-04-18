<properties
    pageTitle="Passo 3: Criar uma nova experiência de aprendizagem máquina | Microsoft Azure"
    description="Passo 3 da desenvolver instruções solução aspeto do Office: criar uma nova experiência de formação no Azure máquina aprendizagem Studio."
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


# <a name="walkthrough-step-3-create-a-new-azure-machine-learning-experiment"></a>Tutorial passo 3: Criar uma nova experiência de formação de máquina do Azure

Este é o terceiro passo a instruções passo a passo, [desenvolver uma solução de análise de aspeto do Office no Azure máquina aprendizagem](machine-learning-walkthrough-develop-predictive-solution.md)


1.  [Criar uma área de trabalho de aprendizagem automática](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [Carregar dados existentes](machine-learning-walkthrough-2-upload-data.md)
3.  **Criar uma nova experiência**
4.  [Dar formação e avaliar os modelos](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  [Implementar o serviço web](machine-learning-walkthrough-5-publish-web-service.md)
6.  [Aceder ao serviço web](machine-learning-walkthrough-6-access-web-service.md)

----------

Neste tutorial, o próximo passo é criar uma experiência no máquina aprendizagem Studio que utiliza o conjunto de dados que são carregados.  

1.  No Studio, clique em **+ Novo** na parte inferior da janela.
2.  Selecione **experiência**e, em seguida, selecione "Experiência em branco". Selecione o nome de experiência predefinido na parte superior da tela e mude o nome para algo com significado

    > [AZURE.TIP] É aconselhável para preencher **Resumo** e **Descrição** para a experiência no painel de **Propriedades** . Estas propriedades dar-lhe a oportunidade de experiência do documento para que qualquer pessoa que analisa-la mais tarde compreendam seus objetivos e metodologia.

3.  Na paleta de módulo à esquerda da experiência da tela, expanda **Conjuntos de dados guardado**.
4.  Localize o conjunto de dados criado no **Meu conjuntos de dados** e arraste-o para a tela. Também pode encontrar o conjunto de dados ao introduzir o nome na caixa de **pesquisa** acima da paleta.  

##<a name="prepare-the-data"></a>Preparar os dados
Pode ver as primeiros 100 linhas dos dados e algumas informações estatísticas de todo o conjunto de dados ao clicar na porta de saída do conjunto de dados (o círculo pequeno na parte inferior) e selecionar a **visualizar**.  

Uma vez que o ficheiro de dados não for fornecido com cabeçalhos de coluna, Studio forneceu cabeçalhos genéricos (Col1, Col2, *etc.*). Os títulos boas não são essenciais para a criação de um modelo, mas eles que seja mais fácil trabalhar com os dados na experiência. Além disso, quando eventualmente podemos publicar este modelo de um serviço web, cabeçalhos de irão ajudar a identificar as colunas para o utilizador do serviço de.  

Vamos pode adicionar cabeçalhos de coluna utilizando os [Metadados editar] [ edit-metadata] módulo.
Utilizar os [Metadados editar] [ edit-metadata] módulo para alterar os metadados associados um conjunto de dados. Neste caso, pode conferir mais nomes amigáveis para cabeçalhos de coluna. 

A utilização de [Metadados editar][edit-metadata], primeiro de especificar as colunas que pretende modificar (neste caso, todos eles.) Em seguida, especificar a ação a ser executado nessas colunas (neste caso, alterar cabeçalhos de coluna.)

1.  Na paleta de módulo, escreva "metadados" na caixa de **pesquisa** . Verá [Editar metadados] [ edit-metadata] aparecem na lista de módulo.
2.  Clique e arraste os [Metadados editar] [ edit-metadata] módulo para a tela e largue-o abaixo o conjunto de dados adicionámos anterior.
3.  Ligar o conjunto de dados para os [Editar metadados][edit-metadata]: clique na porta de saída do conjunto de dados (círculo pequeno na parte inferior do dataset.) Em seguida, arraste para a porta de entrada de [Editar metadados] [ edit-metadata] (círculo pequeno na parte superior do módulo), em seguida, solte o botão do rato. O conjunto de dados e módulo permanecem ligados, mesmo se mover quer à volta na tela.

    A experiência deverá agora aspeto da seguinte forma:  

    ![Adicionar Editar metadados][2]
    
    O ponto de exclamação vermelho indica que recomendamos ainda não tiver definido as propriedades para este módulo ainda. Vamos faça já a seguir.
    
    > [AZURE.TIP] Pode adicionar um comentário a um módulo fazendo duplo clique no módulo e introdução de texto. Isto pode ajudar a ver rapidamente o que o módulo está a fazer na sua experiência. Neste caso, faça duplo clique em [Editar metadados] [ edit-metadata] módulo e tipo de comentário "Adicionar cabeçalhos de coluna". Clique em qualquer outro na tela para fechar a caixa de texto. Para apresentar o comentário, clique na seta para baixo no módulo.

4.  Selecione [Editar metadados][edit-metadata], em seguida, no painel de **Propriedades** à direita da tela, clique em **Iniciação selector de coluna**.
5.  Na caixa de diálogo **Selecionar colunas** , selecione todas as linhas em **Colunas disponíveis** e clique em > para movê-las para as **Colunas selecionadas**.
A caixa de diálogo deverá ter este aspeto: ![Selector de coluna com todas as colunas seleccionadas][4]
7.  Clique na marca de verificação **OK** .
8.  No painel **Propriedades** , procure o parâmetro de **novos nomes de coluna** . Neste campo, introduza uma lista de nomes para as 21 colunas no conjunto de dados, separados por vírgulas e na ordem das colunas. Pode obter os nomes de colunas a partir a documentação do conjunto de dados no Web site da UCI ou para sua comodidade pode copiar e colar a seguinte lista:  

          Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  

    O painel de propriedades terá o seguinte aspeto:

    ![Propriedades para metadados de editar][1]

> [AZURE.TIP] Se pretende verificar os cabeçalhos de coluna, execute a experiência (clique em **Executar** por baixo da tela de experiência). Quando terminar a execução (uma marca de verificação verde serão apresentados na [Editar metadados][edit-metadata]), clique na porta de saída dos [Metadados editar] [ edit-metadata] módulo e selecione **visualizar**. Pode ver o resultado de qualquer módulo da mesma forma para ver o progresso de dados através da experiência.

##<a name="create-training-and-test-datasets"></a>Crie formação e conjuntos de dados de teste

O passo seguinte da experiência é gerar conjuntos de dados separados que vamos utilizar para a formação e testar o nosso modelo.

Para fazer isto, utilizamos os [Dados divididos] [ split] módulo.  

1.  Localizar os [Dados divididos] [ split] módulo, arraste-o para a tela e ligue-o para a última [Editar metadados] [ edit-metadata] módulo.
2.  Por predefinição, a razão de separação é 0,5 e o parâmetro **Randomized dividir** é definido. Isto significa que um meia aleatória dos dados é saída através de uma porta os [Dados divididos] [ split] módulo e metade através da outra. Pode ajustar estas, bem como o parâmetro de **propagação aleatório** , para alterar a divisão entre formação e testes de dados. Neste exemplo, vamos deixá-las como-é.
    
    > [AZURE.TIP] A propriedade **fração de linhas no primeiro conjunto de dados de saída** determina a quantidade de dados está saída através da porta de saída para a esquerda. Por exemplo, se definir o rácio para 0.7, em seguida, 70% dos dados é saída através da porta para a esquerda e 30% através da porta à direita.  
    
3. Faça duplo clique sobre os [Dados divididos] [ split] módulo e introduza o comentário, "formação/testes dados dividir 50%". 

Pode utilizamos saídas dos [Dados divididos] [ split] módulo no entanto podemos semelhante, mas vamos optar por utilizar o resultado da esquerda, tal como dados de formação e à direita de saída de dados como testes.  

Tal como mencionado no Web site da UCI, o custo de classifique incorrectamente um risco de crédito alta como baixa for cinco vezes maior que o custo de classifique incorrectamente um risco de crédito baixa como alta. Para ter em conta isto, podemos gerar um novo conjunto de dados que reflita esta função do custo. No conjunto de dados novo, cada exemplo de alto risco é replicado cinco vezes, enquanto cada exemplo baixo risco não é replicado.   

Podemos fazer esta replicação utilizando R código:  

1.  Localizar e arraste o [Executar scripts de R] [ execute-r-script] módulo para a experiência tela e ligue a porta de saída para a esquerda dos [Dados divididos] [ split] módulo para a primeira entrada porta ("Dataset1") do [Executar scripts de R] [ execute-r-script] módulo.
2. Faça duplo clique em [Executar scripts de R] [ execute-r-script] módulo e introduza o comentário, "Conjunto ajuste de custo".
2.  No painel de **Propriedades** , eliminar o texto predefinido no parâmetro de **Script de R** e introduza este script:

          dataset1 <- maml.mapInputPort(1)
          data.set<-dataset1[dataset1[,21]==1,]
          pos<-dataset1[dataset1[,21]==2,]
          for (i in 1:5) data.set<-rbind(data.set,pos)
          maml.mapOutputPort("data.set")


Precisamos de fazer esta operação de replicação mesmo para cada saída dos [Dados divididos] [ split] módulo para que os dados de formação e testes tenham o mesmo ajuste de custos.

1.  Botão direito do rato o [Executar scripts de R] [ execute-r-script] módulo e selecione **Copiar**.
2.  Tela de experiência com o botão direito e selecione **Colar**.
3.  Ligar a primeira entrada porta deste [Executar scripts de R] [ execute-r-script] porta os [Dados divididos] de saída de módulo para a direita[ split] módulo. 
4.  Na parte inferior da tela, clique em **Executar**. 

> [AZURE.TIP] A cópia do módulo executar scripts de R contém o mesmo script como o módulo original. Quando copiar e colar um módulo na tela, a cópia retém todas as propriedades da célula original.  

Os nossos experiência agora tenha este aspeto:

![Adicionar módulo dividida e R scripts][3]

Para mais informações sobre como utilizar R scripts nas experiências, consulte o artigo [expandir sua experiência com R](machine-learning-extend-your-experiment-with-r.md).

**Seguinte: [comboio e avaliar os modelos de](machine-learning-walkthrough-4-train-and-evaluate-models.md)**


[1]: ./media/machine-learning-walkthrough-3-create-new-experiment/create1.png
[2]: ./media/machine-learning-walkthrough-3-create-new-experiment/create2.png
[3]: ./media/machine-learning-walkthrough-3-create-new-experiment/create3.png
[4]: ./media/machine-learning-walkthrough-3-create-new-experiment/columnselector.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
