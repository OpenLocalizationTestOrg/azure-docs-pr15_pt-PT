<properties
   pageTitle="Os seus dados está pronto para ser ciência de dados? Avaliação dos dados | Microsoft Azure"
   description="Obter informações sobre os 4 critérios de dados para estar pronto para ser ciência de dados. Dados ciência para principiantes vídeo 2 tem betão exemplos para o ajudar com a avaliação de dados básico."
   keywords="dados relevantes, avaliar dados, prepare os dados, os critérios de dados, dados prontos"
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


# <a name="is-your-data-ready-for-data-science"></a>Os seus dados está pronto para ser ciência de dados?

## <a name="video-2-data-science-for-beginners-series"></a>Vídeo 2: Dados ciência para série principiantes

Saiba como avaliar os seus dados para se certificar de que satisfaça critérios fundamentais para estar pronto para ser ciência de dados.

Para tirar o máximo partido da série, veja-los a todos. [Ir para a lista de vídeos](#other-videos-in-this-series)

> [AZURE.VIDEO data-science-for-beginners-series-is-your-data-ready-for-data-science]

## <a name="other-videos-in-this-series"></a>Outros vídeos nesta série

*Dados ciência para principiantes* é uma breve introdução às science de dados na cinco vídeos de curtos duração.

  * Vídeo 1: [As respostas a 5 perguntas dados ciência](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 mínimo 14 seg.)*
  * Vídeo 2: É os seus dados está preparado para ciência de dados?
  * Vídeo 3: [Faça uma pergunta pode atender com dados](machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 mínimo 17 seg.)*
  * Vídeo 4: [Prever uma resposta com um modelo de simple](machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 mínimo 42 seg.)*
  * Vídeo 5: [Copiar o trabalho de outras pessoas para fazer ciência de dados](machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 mínimo 18 seg.)*

## <a name="transcript-is-your-data-ready-for-data-science"></a>Transcrição: Está pronto para ser ciência dados os seus dados?

Bem-vindo ao "É os seus dados está preparado para ciência de dados?" o segundo vídeo na série de *Dados ciência para principiantes*.  

Antes de ciências de dados podem fornecer-lhe as respostas que pretende, tem de dar-lhe algumas matéria de alta qualidade para trabalhar com. Tal como efetuar uma pizza, melhor componentes com melhor o produto final que começa.

## <a name="criteria-for-data"></a>Critérios para os dados

Por isso, no caso de ciências de dados, existem alguns componentes que precisamos de separar em conjunto.

Precisamos de dados que são:

  * Relevantes
  * Ligado
  * Precisão
  * Suficiente para trabalhar com

## <a name="is-your-data-relevant"></a>Os seus dados é relevante?

Para que o primeiro ingrediente - precisamos de dados que são relevantes.

![Dados relevantes vs. dados irrelevantes - avaliar dados](./media/machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science/machine-learning-data-science-relevant-and-irrelevant-data.png)

Veja a tabela à esquerda. Vamos cumpridos sete pessoas fora da barras Boston, medido o respetivo nível de álcool controlador de pressão, a média de lançadores Sox vermelho na sua último jogo e o preço de leite na loja conveniência mais próxima.

Esta é a todos os dados na perfeição legítimos. Falhas apenas são que não for relevante. Não existe nenhuma relação óbvia entre estes números. Se o lhe atribuiu o preço de leite e a média de lançadores vermelho Sox atual, não existe nenhuma forma poderá adivinhar o meu conteúdo do controlador de pressão álcool.

Agora, observe a tabela no lado direito. Desta vez, podemos medida de cada pessoa peso do corpo e contados o número de bebidas que tenha tido.  Os números em cada linha agora são relevantes para si. Se o lhe atribuiu meu corpo massa e o número de Margaritas que tenha tido, pode fazer uma estimativa na minha controlador de pressão álcool conteúdo.

## <a name="do-you-have-connected-data"></a>Ligados dados?

O seguinte ingrediente for dados ligados.

![Dados vs. dados desligados - critérios de dados, prontos dados ligados](./media/machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science/machine-learning-data-science-connected-vs-disconnected-data.png)

Eis alguns dados relevantes na qualidade da hamburgers: grelha temperatura, a espessura de patty e classificação no local alimento revista. Mas repare lacunas na tabela à esquerda.

A maior parte dos conjuntos de dados estão em falta alguns valores. É comum ter buracos assim e existem formas de trabalhar em torno das mesmas. Mas, se existir demasiado em falta, os seus dados começa aspeto suíço queijo.

Se veja a tabela à esquerda, existe pelo quantidade de dados em falta, que é difícil obter qualquer tipo de relação entre peso de temperatura e patty de grelha. Este é um exemplo de dados desligados.

A tabela à direita, no entanto, está cheio e preencha - um exemplo de dados ligados.

## <a name="is-your-data-accurate"></a>Os seus dados é preciso?

O seguinte ingrediente que precisamos for precisão. Aqui estão os quatro destinos que Gostaríamos visitas com setas.

![Dados precisos vs. dados imprecisos - critérios de dados](./media/machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science/machine-learning-data-science-inaccurate-vs-accurate-data.png)

Veja o destino no canto superior direito. Temos mais um agrupamento justo para a direita em redor de bullseye. Que, claro, é preciso. Forma estranha, o idioma da ciência de dados, os nossos desempenho à direita de destino abaixo do mesmo também é considerado como correctos.

Se fosse mapear fora do Centro destas setas, verá que é muito perto a bullseye. As setas são propagadas todos os à volta de destino, para que está a consideradas suficientemente precisos, mas está a centrado em torno de bullseye, pelo que está a consideradas correctos.

Agora, observe o canto superior esquerdo de destino. Aqui nossas setas clicada muito juntos, um agrupamento justo. Se precisa, mas encontra imprecisas porque o Centro de é a forma terminar a bullseye. E, claro, as setas na parte inferior esquerda de destino são imprecisas e suficientemente precisos. Este oliveira necessita de exercícios práticos mais.

## <a name="do-you-have-enough-data-to-work-with"></a>Tem dados suficientes para trabalhar com?

Por fim, ingrediente #4 - precisamos de ter suficiente dados.

![Tem suficiente dados para uma análise? Avaliação dos dados](./media/machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science/machine-learning-data-science-barely-enough-data.png)

Pense cada ponto de dados na tabela como sendo um traço de pincel uma apresentação com pintura. Se tiver apenas alguns dos mesmos, a pintura pode ser muito nítida - é difícil saber o que é.

Se adicionar alguns mais traços de pincel, em seguida, a pintura é iniciado obter um pouco mais nítidos.

Quando tiver praticamente não suficiente traços, pode ver apenas suficiente para tomar decisões algumas abrangentes. É algures que poderá a pretender visite? Parecer claro, que aspeto limpeza água – Sim, que é onde vou de férias.

À medida que adiciona mais dados, a imagem fica mais clara e poder tomar decisões mais detalhadas. Agora posso pode observe o três alojamento do banco para a esquerda. Sabe, posso realmente como as funcionalidades de arquitecturais daquele em primeiro plano. Posso irá permanecer aqui no piso terceiro.

Com os dados que são relevante, ligada, precisa e suficiente, podemos ter todos os componentes precisamos de fazer ciência alguns dados de alta qualidade.

Certifique-se de que dar saída os outros quatro vídeos *Ciência de dados para principiantes* do Microsoft Azure máquina Learning.




## <a name="next-steps"></a>Próximos passos

  * [Experimente uma experiência de ciências dados primeira com o computador aprendizagem Studio](machine-learning-create-experiment.md)
  * [Obter uma introdução à máquina formação no Microsoft Azure](machine-learning-what-is-machine-learning.md)
