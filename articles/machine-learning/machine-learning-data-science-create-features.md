<properties
    pageTitle="Funcionalidade de engenharia no processo de análise de Cortana | Microsoft Azure" 
    description="Explica o propósito de engenharia de funcionalidade e fornece exemplos do seu papel no processo de melhoramento de dados da formação de máquina."
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
    ms.date="09/19/2016"
    ms.author="zhangya;bradsev" />


# <a name="feature-engineering-in-the-cortana-analytics-process"></a>Funcionalidade de engenharia no processo de análise de Cortana 

Este tópico explica o propósito de engenharia de funcionalidade e fornece exemplos do seu papel no processo de melhoramento de dados da formação de máquina. Os exemplos utilizados para ilustrar este processo são desenhados a partir do Azure máquina aprendizagem Studio. 

[AZURE.INCLUDE [cap-create-features-data-selector](../../includes/cap-create-features-selector.md)]

Este **menu** contém ligações para tópicos que descrevem como criar funcionalidades para os dados em vários ambientes. Esta tarefa é um passo o [Processo de ciências de dados de equipa (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

Funcionalidade de engenharia tentativas para aumentar o aspeto do Office potência de aprendizagem algoritmos através da criação de funcionalidades a partir de dados não processados que o ajudam a facilitar o processo de aprendizagem. A engenharia e seleção de funcionalidades é uma parte de TDSP destacada a [qual é o processo de ciências de dados de equipa?](data-science-process-overview.md) Funcionalidade engenharia e seleção são partes do passo **desenvolver funcionalidades** do TDSP. 

* **engenharia de funcionalidade**: Este processo tentativas para criar funcionalidades relevantes adicionais a partir das funcionalidades não processadas existentes nos dados e para aumentar o aspeto do Office potência do algoritmo de aprendizagem.

* **seleção de funcionalidades**: Este processo seleciona o subconjunto chave original de funcionalidades de dados numa tentativa para reduzir a dimensão do problema de formação.

Normalmente **engenharia funcionalidade** é aplicada primeiro para gerar funcionalidades adicionais e, em seguida, o passo de **seleção de funcionalidades** é efetuado para eliminar o irrelevantes, redundantes ou altamente correlacionadas funcionalidades.

Os dados de formação utilizados em aprender máquina com frequência podem ser melhorados por extração de funcionalidades dos dados não processados recolhidos. Um exemplo de uma funcionalidade de engenharia no contexto de aprendendo a classificar as imagens de carateres escritas à mão é a criação de um pouco densidade mapa de fórmula criada a partir de dados não processados bit distribuição. Este mapa pode ajudar a localizar as margens dos carateres mais eficazmente do que simplesmente com a distribuição observou diretamente.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


## <a name="creating-features-from-your-data---feature-engineering"></a>Criar funcionalidades dos seus dados - engenharia de funcionalidades

Os dados de formação é composta por uma matriz composto por exemplos (registos ou observações armazenadas nas linhas), cada um dos quais tem um conjunto de funcionalidades (variáveis ou campos armazenados nas colunas). As funcionalidades especificadas na experimental estrutura são esperadas para caracterizar padrões dos dados. Apesar de muitas os campos de dados não processados podem ser diretamente incluídas no conjunto de funcionalidade seleccionada utilizado para formar um modelo, é frequentemente as maiúsculas/minúsculas que funcionalidades (engenharia) adicionais necessitam construídos de funcionalidades nos dados não processados para gerar um conjunto de dados de formação melhorado.

Que tipo de funcionalidades deve ser criado para melhorar o conjunto de dados quando um modelo de formação? Funcionalidades de engenharia que melhoram a formação fornecem informações que diferencia melhor os padrões dos dados. Vamos esperar que as novas funcionalidades para fornecer informações adicionais que não seja claramente capturada ou facilmente evidente no conjunto de funcionalidade original ou existente. Mas este processo for algo sem uma ClipArt. Sons e produtivas decisões requerem frequentemente alguns conhecimentos de domínio.

Ao iniciar o Azure máquina formação, é mais fácil perceber este processo concretamente utilizando exemplos fornecidos no Studio. Dois exemplos são apresentados aqui:

* Um exemplo de regressão [predição do número de aluguer de bicicleta](http://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4) numa experiência controlado onde os valores alvo são conhecidos
* Um exemplo de classificação de extração de texto utilizando a [Funcionalidade Hashing](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/)

### <a name="example-1-adding-temporal-features-for-regression-model"></a>Exemplo 1: Adicionar funcionalidades Temporal para o modelo de regressão ###

Vamos utilizar a experiência "pedido previsão de bicicletas" no Azure máquina aprendizagem Studio para demonstrar como efectuar a engenharia funcionalidades para uma tarefa de regressão. Objectivo desta experiência é prever pedido para bicicletas, ou seja, o número de aluguer de bicicleta dentro de uma mês/dia/hora específica. O conjunto de dados "bicicleta aluguer UCI conjunto de dados" é utilizada como os dados não processados de entrada. Este conjunto de dados é baseado dados reais da empresa Bikeshare maiúscula que mantém numa rede de aluguer bicicleta no Centro de dados de Washington nos Estados Unidos. O conjunto de dados representa o número de aluguer de bicicleta dentro de uma hora específica de um dia em anos 2011 e ano de 2012 e contém 17379 linhas e 17 colunas. O conjunto de funcionalidades não processado contém condições Meteorologia (temperatura/humidade/vento) e o tipo do dia (férias/dia). O campo a prever é "not", uma contagem que representa o aluguer de bicicleta dentro de uma hora específica e intervalos que intervalos de 1 para 977.

Com o propósito de construir funcionalidades eficazes os dados de formação, quatro regressão modelos foram criados utilizando o mesmo algoritmo mas com quatro formação diferentes conjuntos de dados. Os conjuntos de quatro dados representam os mesmos dados não processados de entrada, mas com um número crescente de funcionalidades definido. Estas funcionalidades estão agrupadas em quatro categorias:

1. A = Meteorologia + férias + weekday + fim de semana funcionalidades para o dia previsto
2. B = número de bicicletas que foram arrendadas em cada uma das 12 horas anterior
3. C = número de bicicletas que foram arrendadas em cada uma dos dias 12 anterior na mesma hora
4. D = número de bicicletas que foram arrendadas em cada uma das semanas 12 anterior na mesma hora e o mesmo dia

Para além de um conjunto de funcionalidade, que já existam nos dados não processados originais, os conjuntos de três das funcionalidades são criados através da funcionalidade engenharia processo. Funcionalidade definida B capturas pedido muito recente para os bikes. Funcionalidade definida C capturas pedido para bicicletas de hora específica. Funcionalidade definida D capturas pedido para bicicletas hora específica e determinado dia da semana. Os quatro formação conjuntos de dados cada incluem um conjunto de funcionalidade, A + B, A + B + C e A + B + C + D, respetivamente.

Na experiência Azure máquina formação, estes quatro conjuntos de dados de formação são formados através de quatro ramificações a partir do conjunto de dados de entrada previamente transformado. Exceto à esquerda, a maior parte dos ramo, cada um desses ramos contém um módulo de [Executar R Script](https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/) , em que um conjunto de derivado funcionalidades (funcionalidade definido B, C e D) respetivamente são construídos e acrescentado para o conjunto de dados importado. A figura seguinte demonstra o script R utilizado para criar o conjunto de funcionalidades B segunda ramo para a esquerda.

![criar funcionalidades](./media/machine-learning-data-science-create-features/addFeature-Rscripts.png)

Comparação dos resultados de desempenho dos modelos de quatro estão resumidos na tabela seguinte. Obter os melhores resultados são apresentados por funcionalidades A + B + C. Nota que diminui a taxa de erro quando conjunto de funcionalidades adicionais são incluídos nos dados de formação. Verifica a nossa pressuposto de que o conjunto de funcionalidades B, C fornece informações relevantes adicionais para a tarefa de regressão. Mas adicionar a funcionalidade de D parece não fornecer qualquer redução adicional da taxa de erro.

![comparação de resultado](./media/machine-learning-data-science-create-features/result1.png)

### <a name="example2"></a>Exemplo 2: Criar funcionalidades no extração de texto  

Funcionalidade engenharia amplamente é aplicada no tarefas relacionadas com a extração de texto, como análise de classificação e sentimento do documento. Por exemplo, quando queremos classificar documentos em várias categorias, um pressupostos típico são que o word/frases incluídas uma categoria de documento são menos provável que ocorrem em outra categoria de documento. No outro palavras, a frequência da distribuição palavras/expressões é possível caracterizar categorias de documento diferente. Em aplicações de extração de texto, uma vez que individuais partes de conteúdo de texto servem normalmente, os dados de entrada, a funcionalidade de engenharia processo é necessária, para criar as funcionalidades que envolvam word/expressão frequência.

Para realizar esta tarefa, uma técnica denominada **funcionalidade hashing** é aplicada a eficazmente transformar funcionalidades de texto arbitrários índices. Em vez de a associação de cada funcionalidade de texto (palavras/frases) a um índice específico, funções este método ao aplicar uma função de hash às funcionalidades e utilizar diretamente os respetivos valores hash como índices.

Formação do Azure máquina, existe um módulo de [Funcionalidade Hashing](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) cria estes word/expressão funcionalidades de forma conveniente. Figura seguinte mostra um exemplo de utilizar este módulo. O conjunto de dados de entrada contém duas colunas: a classificação de livro de endereços que se situa entre 1 a 5 e o conteúdo de rever real. O objetivo do presente módulo [Funcionalidade Hashing](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) é obter um conjunto de novas funcionalidades que mostram a frequência de ocorrência da palavra (s) correspondente / rever ou frases dentro do livro específico. Para utilizar este módulo, precisamos de concluir os passos seguintes:

* Em primeiro lugar, selecione a coluna que contém o texto de entrada ("Col2" neste exemplo).
* Em segundo lugar, defina o "bitsize Hashing" para 8, que significa que 2 ^ 8 = 256 funcionalidades serão criadas. O word fase em todo o texto será possível converter os índices 256. O parâmetro "Hashing bitsize" varia entre 1 e 31. A palavra (s) / ou frases provavelmente menor possível converter o mesmo índice, se defini-la para ser um número maior.
* Em terceiro lugar, defina o parâmetro "G N" para 2. Isto obtém a frequência de ocorrência dos unigrams (uma funcionalidade de cada única palavra) e bigrams (uma funcionalidade para cada par de palavras adjacentes) a partir do texto de entrada. O parâmetro "G N" intervalos de 0 a 10, que indica o número máximo de sequenciais palavras que devem ser incluídas numa funcionalidade.  

![Módulo "Funcionalidade Hashing"](./media/machine-learning-data-science-create-features/feature-Hashing1.png)

A figura seguinte mostra o que estes nova funcionalidade aspeto como.

![Exemplo de "Funcionalidade Hashing"](./media/machine-learning-data-science-create-features/feature-Hashing2.png)


## <a name="conclusion"></a>Conclusão

Funcionalidades de engenharia e selecionadas aumentar a eficiência do processo de formação que tentativas deverão ser extraídas as informações da chave contidas nos dados. Estes também melhoram o poder destes modelos para classificar os dados de entrada com exatidão e para prever sólida mais resultados de interesse. Também podem combinar funcionalidade engenharia e seleção para tornar o ensino mais inviabiliza tractable. Para o fazer, melhorar e, em seguida, reduzindo o número de funcionalidades necessárias para calibre ou formar um modelo. Matematicamente falar, as funcionalidades seleccionadas para formar o modelo são um conjunto mínimo de variáveis independentes que explicam os padrões dos dados e, em seguida, prever resultados com êxito.

Tenha em atenção que nem sempre é necessariamente para efetuar a seleção de engenharia ou funcionalidade de funcionalidade. É necessária ou não depende o podemos ter ou recolher dados, o algoritmo que podemos escolha e sobre o objetivo da experiência.
 
