<properties
    pageTitle="Funcionalidade de engenharia e seleção no Azure máquina aprendizagem | Microsoft Azure"
    description="Explica os efeitos da seleção de funcionalidades e engenharia de funcionalidade e fornece exemplos da sua função no processo de melhoramento de dados da formação de máquina."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/12/2016"
    ms.author="zhangya;bradsev" />


# <a name="feature-engineering-and-selection-in-azure-machine-learning"></a>Seleção no Azure máquina aprendizagem e engenharia de funcionalidade

Este tópico explica o propósito de seleção de funcionalidade no processo de melhoramento de dados de aprendizagem automática e engenharia de funcionalidade. Ilustra a que envolvam estes processos utilizando exemplos fornecidos pelo Azure máquina aprendizagem Studio.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Os dados de formação utilizados em aprender máquina com frequência podem ser melhorados pela seleção ou extração de funcionalidades dos dados não processados recolhidos. Um exemplo de uma funcionalidade de engenharia no contexto de aprendendo a classificar as imagens de carateres escritas à mão é um mapa de bits densidade fórmula criado a partir de dados não processados bit distribuição. Este mapa pode ajudar a localizar as margens dos carateres mais eficazmente do que a distribuição não processada.

Funcionalidades de engenharia e selecionadas aumentar a eficiência do processo de formação, tentativas deverão ser extraídas as informações da chave contidas nos dados. Estes também melhoram o poder destes modelos para classificar os dados de entrada com exatidão e para prever sólida mais resultados de interesse. Também podem combinar funcionalidade engenharia e seleção para tornar o ensino mais inviabiliza tractable. Para o fazer, melhorar e, em seguida, reduzindo o número de funcionalidades necessárias para calibre ou formar um modelo. Matematicamente falar, as funcionalidades seleccionadas para formar o modelo são um conjunto mínimo de variáveis independentes que explicam os padrões dos dados e, em seguida, prever resultados com êxito.

A engenharia e seleção de funcionalidades é uma parte de um processo de maior, normalmente consiste em quatro passos:

* Recolha de dados
* Melhoramento de dados
* Construção de modelo
* Pós-processamento

Engenharia e seleção constituem o passo de melhoramento de dados de aprendizagem automática. Três aspetos deste processo podem distinguir-se para as nossas finalidades:

* **Pré-processamento dos dados**: Este processo tenta garantir que os dados recolhidos são simples e consistentes. Inclui tarefas como integrar vários conjuntos de dados, processamento de dados em falta, processamento de dados inconsistentes e converter tipos de dados.
* **Engenharia de funcionalidades**: tentativas este processo para criar funcionalidades relevantes adicionais a partir das funcionalidades não processadas existentes nos dados e para aumentar o aspeto do Office power para o algoritmo de aprendizagem.
* **Seleção de funcionalidades**: Este processo seleciona o subconjunto chave original de funcionalidades de dados para reduzir a dimensão do problema de formação.

Este tópico abrange apenas os aspectos de seleção de funcionalidade do processo de melhoramento de dados e de engenharia de funcionalidade. Para mais informações sobre o passo anterior à transformação de dados, consulte o artigo [pré-processamento dados no Azure máquina aprendizagem Studio](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/).


## <a name="creating-features-from-your-data--feature-engineering"></a>Criar funcionalidades dos seus dados – funcionalidade engenharia

Os dados de formação é composta por uma matriz composto por exemplos (registos ou observações armazenadas nas linhas), cada um dos quais tem um conjunto de funcionalidades (variáveis ou campos armazenados nas colunas). As funcionalidades especificadas na experimental estrutura são esperadas para caracterizar padrões dos dados. Apesar de muitas os campos de dados não processados podem ser diretamente incluídas no conjunto de funcionalidade seleccionada utilizado para formar um modelo, funcionalidades adicionais de engenharia necessitam muitas vezes construídos de funcionalidades nos dados não processados para gerar um conjunto de dados de formação melhorado.

Que tipo de funcionalidades deve ser criado para melhorar o conjunto de dados quando um modelo de formação? Funcionalidades de engenharia que melhoram a formação fornecem informações que diferencia melhor os padrões dos dados. Espera as novas funcionalidades para fornecer informações adicionais que não são claramente capturadas ou facilmente evidente no conjunto de funcionalidades original ou já existente, mas este processo for algo sem uma ClipArt. Sons e produtivas decisões requerem frequentemente alguns conhecimentos de domínio.

Ao iniciar o Azure máquina formação, é mais fácil perceber este processo concretamente utilizando exemplos fornecidos na máquina aprendizagem Studio. Dois exemplos são apresentados aqui:

* Um exemplo de regressão ([predição do número de aluguer de bicicleta](http://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4)) numa experiência controlado onde os valores alvo são conhecidos
* Um exemplo de classificação da extração de texto utilizando a [Funcionalidade Hashing][feature-hashing]

### <a name="example-1-adding-temporal-features-for-a-regression-model"></a>Exemplo 1: Adicionar funcionalidades temporais para um modelo de regressão ###

Para demonstrar como efectuar a engenharia funcionalidades para uma tarefa de regressão, vamos utilizar a experiência "pedido previsão de bicicletas" no Azure máquina aprendizagem Studio. Objectivo desta experiência é prever pedido para bicicletas, ou seja, o número de aluguer de bicicleta situada num mês específico, dia ou hora. O **conjunto de dados de bicicleta aluguer UCI** do conjunto de dados é utilizado como os dados não processados de entrada.

Este conjunto de dados é baseado dados reais da empresa Bikeshare maiúscula que mantém numa rede de aluguer bicicleta no Centro de dados de Washington nos Estados Unidos. O conjunto de dados representa o número de aluguer de bicicleta dentro de uma hora específica de um dia, a partir de 2011 para 2012 e contém 17379 linhas e 17 colunas. O conjunto de funcionalidades não processado contém condições Meteorologia (temperatura, humidade, velocidade do vento) e o tipo do dia (feriado ou dia). O campo a prever é **Not**, uma contagem que representa o aluguer de bicicleta dentro de uma hora específica e que varia entre 1 e 977.

Para construir funcionalidades eficazes dos dados de formação, modelos de regressão quatro foram criados ao utilizar o mesmo algoritmo, mas com quatro conjuntos de dados de formação diferente. Os conjuntos de dados quatro representar os mesmos dados não processados de entrada, mas com um número crescente de funcionalidades definido. Estas funcionalidades estão agrupadas em quatro categorias:

1. A = Meteorologia + férias + weekday + fim de semana funcionalidades para o dia previsto
2. B = número de bicicletas que foram arrendadas em cada uma das 12 horas anterior
3. C = número de bicicletas que foram arrendadas em cada uma dos dias 12 anterior na mesma hora
4. D = número de bicicletas que foram arrendadas em cada uma das semanas 12 anterior na mesma hora e o mesmo dia

Para além de um conjunto de funcionalidade, que já existe nos dados não processados originais, os conjuntos de três das funcionalidades são criados através da funcionalidade engenharia processo. Funcionalidade definida capturas de B a procura recente para as bicicletas. Funcionalidade definida C capturas pedido para bicicletas de hora específica. Funcionalidade definida D capturas pedido para bicicletas hora específica e determinado dia da semana. Cada uma das quatro conjuntos de dados de formação inclui conjuntos de funcionalidade respostas, A + B, A + B + C e A + B + C + D, respetivamente.

Na experiência Azure máquina formação, estes quatro conjuntos de dados de formação são formados através de quatro ramificações do conjunto de dados de entrada previamente transformado. Exceto o ramo mais à esquerda, cada um desses ramos contém um [Script de R executar] [ execute-r-script] módulo em que um conjunto de derivado funcionalidades (funcionalidade define B, C e D) respetivamente é construído e acrescentado ao conjunto de dados importado. A figura seguinte demonstra o script R utilizado para criar o conjunto de funcionalidades B segunda ramo para a esquerda.

![Criar um conjunto de funcionalidade](./media/machine-learning-feature-selection-and-engineering/addFeature-Rscripts.png)

A tabela seguinte resume a comparação dos resultados de desempenho dos modelos de quatro. Obter os melhores resultados são apresentados por funcionalidades A + B + C. Tenha em atenção que diminui a taxa de erro quando conjuntos de funcionalidades adicionais são incluídos nos dados de formação. Este procedimento verifica os nossos pressuposto de que os conjuntos de funcionalidade B e C fornecem informações relevantes adicionais para a tarefa de regressão. Adicionar o conjunto de funcionalidades D parece não fornecer qualquer redução adicional da taxa de erro.

![Comparar os resultados do desempenho](./media/machine-learning-feature-selection-and-engineering/result1.png)

### <a name="example2"></a>Exemplo 2: Criar funcionalidades no extração de texto  

Funcionalidade engenharia amplamente é aplicada no tarefas relacionadas com a extração de texto, como análise de classificação e sentimento do documento. Por exemplo, quando pretender classificar documentos em várias categorias, um pressupostos típico são que as palavras ou frases incluídas uma categoria de documento são menos provável que ocorrem em outra categoria de documento. Por outras palavras, a frequência da distribuição palavra ou expressão é possível caracterizar categorias de documento diferente. Em aplicações de extração de texto, a funcionalidade de engenharia processo é necessária para criar as funcionalidades que envolvam frequência palavra ou expressão, porque individuais partes de conteúdo de texto servem normalmente, os dados de entrada.

Para realizar esta tarefa, uma técnica denominada *funcionalidade hashing* é aplicada a eficazmente transformar funcionalidades de texto arbitrários índices. Em vez de a associação de cada funcionalidade de texto (palavras ou frases) a um índice específico, este funções método ao aplicar uma função de hash às funcionalidades e ao utilizar os respetivos valores hash como índices diretamente.

Formação do Azure máquina, existe uma [Funcionalidade Hashing] [ feature-hashing] módulo que cria estas funcionalidades palavra ou expressão. A figura seguinte mostra um exemplo de utilizar este módulo. O conjunto de dados de entrada contém duas colunas: a classificação de livro de endereços que se situa entre 1 a 5 e o valor real rever conteúdo. O objetivo desta [Funcionalidade Hashing] [ feature-hashing] módulo é obter novas funcionalidades que mostram a frequência de ocorrência de correspondentes palavras ou expressões dentro de rever a determinado livro de endereços. Para utilizar este módulo, é necessário concluir os passos seguintes:

1. Selecione a coluna que contém o texto de entrada (**Col2** neste exemplo).
2. Defina *Hashing bitsize* para 8, que significa que 2 ^ 8 = 256 funcionalidades são criadas. A palavra ou expressão no texto, em seguida, é atribuído aos 256 índices. O parâmetro *Hashing bitsize* varia entre 1 e 31. Se o parâmetro estiver definido para um número maior, as palavras ou frases serão menos provável que atribuídos para o mesmo índice.
3. Defina o parâmetro *g N* para 2. Isto obtém a frequência de ocorrência dos unigrams (uma funcionalidade de cada única palavra) e bigrams (uma funcionalidade para cada par de palavras adjacentes) a partir do texto de entrada. O parâmetro *N g* intervalos de 0 a 10, que indica o número máximo de palavras sequenciais devem ser incluídas numa funcionalidade.  

![Módulo hashing de funcionalidade](./media/machine-learning-feature-selection-and-engineering/feature-Hashing1.png)

A figura seguinte mostra o aspeto que estas novas funcionalidades.

![Exemplo de hashing de funcionalidade](./media/machine-learning-feature-selection-and-engineering/feature-Hashing2.png)

## <a name="filtering-features-from-your-data--feature-selection"></a>Funcionalidades dos seus dados – seleção de funcionalidades de filtragem  ##

*Seleção de funcionalidades* é um processo que normalmente é aplicado de construção de conjuntos de dados de formação para as tarefas de modelação de aspeto do Office, tal como tarefas de classificação ou regressão. O objetivo é selecionar um subconjunto das funcionalidades do conjunto de dados original que reduz as suas dimensões utilizando um conjunto de funcionalidades mínimo para representar a quantidade máxima de variância dos dados. Este subconjunto de funcionalidades contém as funcionalidades apenas para ser incluídas para formar o modelo. Seleção de funcionalidades tem duas finalidades principais:

* Seleção de funcionalidades frequentemente aumenta a precisão de classificação eliminando irrelevante, redundantes ou altamente associados funcionalidades.
* Seleção de funcionalidades reduz o número de funcionalidades, que torna o processo de formação do modelo mais eficaz. Isto é particularmente importante para formandos que estão dispendiosos de formar como máquinas de vetor de suporte.

Apesar de seleção de funcionalidades destina-se reduzir o número de funcionalidades no conjunto de dados utilizados para formar o modelo, não é normalmente designado pelo termo *redução de dimensão.* Métodos de selecção de funcionalidade extrair um subconjunto de funcionalidades originais nos dados sem alterá-las.  Métodos de redução de dimensão empregam engenharia funcionalidades que podem transformar as funcionalidades originais e, por conseguinte, modificá-las. Métodos de redução de dimensão exemplos de análise de componente principal, análise de correlação canónico e decomposição no singular valor.

Uma categoria amplamente aplicada dos métodos de seleção de funcionalidade num contexto controlados é selecção de funcionalidade baseada em filtro. Avaliar a correlação entre cada funcionalidade e o atributo de destino, estes métodos aplicam uma medida de estatística para atribuir uma classificação a cada funcionalidade. As funcionalidades, em seguida, estão classificadas por pontuação, que pode utilizar para definir o limiar de para manter ou eliminar uma funcionalidade específica. As medidas de estatísticas utilizadas nestes métodos de exemplos de correlação Pearson, comum informações e o teste de chi-quadrado.

Azure máquina aprendizagem Studio fornece módulos para a seleção de funcionalidades. Como apresentado na figura seguinte, estes módulos incluem a [seleção de funcionalidades do filtro de baseado] [ filter-based-feature-selection] e [Fisher Linear Discriminant análise][fisher-linear-discriminant-analysis].

![Exemplo de seleção de funcionalidade](./media/machine-learning-feature-selection-and-engineering/feature-Selection.png)


Por exemplo, utilize a [seleção de funcionalidades do filtro de baseado] [ filter-based-feature-selection] módulo com o exemplo de extração de texto descrito anteriormente. Vamos assumir que pretende criar um modelo de regressão depois de um conjunto de 256 funcionalidades é criado através da [Funcionalidade Hashing] [ feature-hashing] módulo, que a variável de resposta é e **Col1** representa um livro rever classificação que se situa entre 1 a 5. Definir a **funcionalidade de pontuação método** **Pearson correlação**, a **coluna de destino** para **Col1**e **várias funcionalidades pretendidas** para **50**. Módulo de [seleção de funcionalidades do filtro de baseado] [ filter-based-feature-selection] , em seguida, produz um conjunto de dados que contém 50 funcionalidades juntamente com o atributo **Col1**de destino. A figura seguinte mostra o fluxo desta experiência e os parâmetros de entrada.

![Exemplo de seleção de funcionalidade](./media/machine-learning-feature-selection-and-engineering/feature-Selection1.png)

A figura seguinte mostra os conjuntos de dados resultantes. Cada funcionalidade é classificada com base na correlação Pearson entre si próprio e o atributo **Col1**de destino. As funcionalidades com as pontuações de superiores são mantidas.

![Conjuntos de dados de seleção de funcionalidade baseada em filtro](./media/machine-learning-feature-selection-and-engineering/feature-Selection2.png)

A figura seguinte apresenta as pontuações correspondentes das funcionalidades selecionadas.

![Pontuações de funcionalidade seleccionada](./media/machine-learning-feature-selection-and-engineering/feature-Selection3.png)

Aplicando esta [seleção de funcionalidades do filtro de baseado] [ filter-based-feature-selection] módulo, 50 fora do 256 funcionalidades estão selecionadas porque têm mais funcionalidades associadas o destino da variável **Col1** com base no método de pontuação **Pearson correlação**.

## <a name="conclusion"></a>Conclusão
Engenharia funcionalidade e seleção de funcionalidades são executados frequentemente para preparar os dados de formação durante a criação de um modelo de formação de máquina de dois passos. Normalmente, engenharia funcionalidade é aplicada primeiro para gerar funcionalidades adicionais, e, em seguida, o passo de seleção da funcionalidade é efetuado para eliminar o irrelevantes, redundantes ou altamente correlacionadas funcionalidades.

Não é sempre necessariamente para efetuar a seleção de engenharia ou funcionalidade de funcionalidade. Se é necessária depende de dados tiver ou recolher, o algoritmo que escolher e sobre o objetivo da experiência.


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/
