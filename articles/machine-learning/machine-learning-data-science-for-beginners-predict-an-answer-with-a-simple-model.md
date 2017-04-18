<properties
   pageTitle="Prever uma resposta com um modelo simple - modelo de regressão | Microsoft Azure"
   description="Como criar um modelo de regressão simples para prever um preço em ciência de dados para principiantes vídeo 4. Inclui uma regressão linear com dados de destino."                                  
   keywords="criar um modelo, modelo simples, predição do preço, modelo de regressão simples"
   services="machine-learning"
   documentationCenter="na"
   authors="cjgronlund"
   manager="jhubbard"
   editor="cjgronlund"/>

<tags
   ms.service="machine-learning"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/20/2016"
   ms.author="cgronlun;garye"/>

# <a name="predict-an-answer-with-a-simple-model"></a>Prever uma resposta com um modelo de simple

## <a name="video-4-data-science-for-beginners-series"></a>Vídeo 4: Dados ciência para série principiantes

Saiba como criar um modelo de regressão simples para prever o preço de um losango na ciência de dados para principiantes vídeo 4. Vamos irá desenhar um modelo de regressão com dados de destino.

Para tirar o máximo partido da série, veja-los a todos. [Ir para a lista de vídeos](#other-videos-in-this-series)

> [AZURE.VIDEO data-science-for-beginners-series-predict-an-answer-with-a-simple-model]

## <a name="other-videos-in-this-series"></a>Outros vídeos nesta série

*Dados ciência para principiantes* é uma breve introdução às science de dados na cinco vídeos de curtos duração.

  * Vídeo 1: [As respostas a 5 perguntas dados ciência](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 mínimo 14 seg.)*
  * Vídeo 2: [está os seus dados pronta para ser ciência de dados?](machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 mínimo 56 seg.)*
  * Vídeo 3: [Faça uma pergunta pode atender com dados](machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 mínimo 17 seg.)*
  * Vídeo 4: Prever uma resposta com um modelo de simple
  * Vídeo 5: [Copiar o trabalho de outras pessoas para fazer ciência de dados](machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 mínimo 18 seg.)*

## <a name="transcript-predict-an-answer-with-a-simple-model"></a>Transcrição: Prever uma resposta com um modelo de simple

Bem-vindo ao quarto vídeo na "dados ciência para principiantes" série. Este um, vamos construa um modelo de simple e fazer uma previsão.

Um *modelo* é uma história simplificada sobre os nossos dados. Posso mostrar-lhe o que posso vermelha.

## <a name="collect-relevant-accurate-connected-enough-data"></a>Recolher relevante, preciso, ligada, suficiente dados

Digamos que pretendo Centro de cópias de um losango. Tenho de ter um toque que pertenceram para a minha avó com uma definição de um losango quilates 1.35 e quiser obter uma ideia de quanto irá custar. Posso atender a um bloco de notas e caneta na loja joalharia e posso anote o preço de todos os losangos nas maiúsculas/minúsculas e quanto de avaliar no quilate. Começar com o primeiro losango - 1.01 quilate e $7,366.

Agora até e fazê-lo para todos os outros losangos na loja.

![Colunas de dados de losango](./media/machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model/diamond-data.png)

Repare que a nossa lista tem duas colunas. Cada coluna tem um atributo diferentes - espessura no quilate e preço - e cada linha é um ponto de dados simples que representa um losango único.

Já criámos realmente um pequeno aqui - conjunto de dados numa tabela. Repare que satisfaz os nossos critérios de qualidade:

* Os dados **relevantes** - espessura é altamente relacionado ao preço
* É **preciso** - podemos verificou novamente os preços que vamos escrever para baixo
* É **ligado** - não existem sem espaços em branco de qualquer uma destas colunas
* E, como vamos ver, é **suficiente** dados de responder a nossa pergunta

## <a name="ask-a-sharp-question"></a>Fazer uma pergunta nítida

Agora, vamos irá representar os nossos pergunta de forma nítida: "quanto custa para comprar um losango quilates 1.35?"

Nossa lista não têm um losango quilates 1.35, pelo que irá temos utilizar o resto dos nossos dados para obter uma resposta à pergunta.

## <a name="plot-the-existing-data"></a>Desenhar dados existentes

A primeira coisa que podemos faça é desenhar uma linha horizontal do número, denominada um eixo, colocar no gráfico a espessura. O intervalo da espessura é 0 para 2, por isso Pedimos irá desenhar uma linha que abrange que o intervalo e colocar as marcas de escala para cada quilates metade.

Seguinte podemos irá desenhar um eixo vertical para gravar o preço e ligá-lo para o eixo horizontal espessura. Este será em unidades de euros. Agora que temos um conjunto de eixos das coordenadas.

![Peso e preço de eixos](./media/machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model/weight-and-price-axes.png)

Iremos para tirar estes dados agora e torná-lo numa *dispersão desenhar*. Este é uma excelente forma de visualizar os conjuntos de dados numéricos.

Para o primeiro ponto de dados, podemos visualizar uma linha vertical na quilate 1.01. Em seguida, podemos visualizar uma linha horizontal na $7,366. Sempre que satisfizerem, podemos desenhar um ponto. Isto representa os nossos losango primeiro.

Agora, vamos vá através de cada losango nesta lista e desempenham as mesmas. Quando estamos através de, este é o que recebemos: um conjunto de pontos, uma para cada losango.

![Desenho de dispersão](./media/machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model/scatter-plot.png)

## <a name="draw-the-model-through-the-data-points"></a>Desenhar o modelo através dos pontos de dados

Agora se observe pontos e squint, a coleção de aspeto uma linha fat, nítida. Vamos pode tirar o nosso marcador e desenhar uma linha direita através do mesmo.

Desenhando uma linha, criámos um *modelo*. Pense esta informação como tirar o mundo real e fazer uma versão brincar simplistic da mesma. Agora a brincar está errado - a linha não percorrer os pontos de dados. Mas, é uma simplificação úteis.

![Linha de regressão linear](./media/machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model/linear-regression-line.png)

É o facto de que todos os pontos não aceda exatamente através da linha. Cientistas dados explicam este por indicando que existe o modelo - o que é a linha - o e, em seguida, cada ponto tem algumas *ruído* ou *variância* associada. Existe a relação perfeita subjacente e, em seguida, existe a nível mundial abrasivo, real que adiciona ruído e incerteza.

Uma vez que estamos a tentar responder à pergunta *quanto custa?* esta opção é denominada uma *regressão*. E uma vez que estamos a utilizar uma linha reta, é uma *regressão linear*.

## <a name="use-the-model-to-find-the-answer"></a>Utilizar o modelo para encontrar a resposta

Agora temos um modelo e Solicitamos-lhe os nossos pergunta: quanto irá um losango quilates 1.35 custar?

Para atender a nossa pergunta, visualizar quilate 1.35 e desenhar uma linha vertical. Sempre que este se cruza a linha de modelo, podemos visualizar uma linha horizontal para o eixo de moeda. Acertos-lo para a direita na 10.000. Braço! Já está a resposta: 10.000 com cerca de custos de um losango quilates 1.35.

![Encontrar a resposta no modelo](./media/machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model/find-the-answer.png)

## <a name="create-a-confidence-interval"></a>Criar um intervalo de confiança

É natural a questionar-se de que preciso como este predição está. É útil saber se o losango quilates 1.35 vai ser muito perto 10.000, ou um lote superiores ou inferiores. Para calcular este problema, vamos desenhar um envelope à volta de linha de regressão que inclui a maioria dos pontos. Este envelope chama-se os nossos *intervalo de confiança*: Estamos muito certeza de que os preços de se situar dentro este envelope, porque no mais antiga-las tem. Vamos pode desenhar duas linhas horizontais mais do que a linha de quilates 1.35 atravessa na parte superior e inferior desse envelope.

![Intervalo de confiança](./media/machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model/confidence-interval.png)

Agora, vamos pode dizer algo sobre os nossos intervalo de confiança: Recomendamos pode dizer confiante que é o preço de um losango quilates 1.35 cerca $10.000 – mas poderá ser tão baixo quanto 8.000 e poderá ser tão elevado como 12.000.

## <a name="were-done-with-no-math-or-computers"></a>Vamos terminar, sem matemática nem computadores

Fizemos que cientistas dados pago fazer e fizemos apenas desenhando:

* Vamos pedido uma pergunta que recomendamos poderia responder com dados
* Criámos um *modelo* de regressão *linear*
* Foram efetuadas uma *previsão*, completa com um *intervalo de confiança*

E não utilizamos matemática ou computadores para fazê-lo.

Agora se tinha tivemos obter mais informações, como...

* Cortar do losango
* variações de cor (como fechar o losango é a ser branco)
* o número de inclusões no losango

… e podemos teria mais colunas. Nesse caso, matemática torna-se útil. Se tiver mais de duas colunas, é difícil desenhar pontos em papel. Os cálculos matemáticos permite-lhe se ajustar nessa linha ou nesse plano para os seus dados muito corretamente.

Além disso, se, em vez de apenas alguns losangos, tivemos duas milhar ou dois milhões, em seguida, que pode fazer que funcionam muito mais rapidamente com um computador.

Hoje em dia, tenha falámos sobre como o fazer regressão linear e foram efetuadas uma previsão de utilização de dados.

Certifique-se de que dar saída os outros vídeos "Dados ciência para principiantes" do Microsoft Azure máquina Learning.



## <a name="next-steps"></a>Próximos passos

  * [Experimente uma experiência de ciências dados primeira com o computador aprendizagem Studio](machine-learning-create-experiment.md)
  * [Obter uma introdução à máquina formação no Microsoft Azure](machine-learning-what-is-machine-learning.md)
