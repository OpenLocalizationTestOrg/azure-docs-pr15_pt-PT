<properties
   pageTitle="Fazer uma pergunta pode atender com dados - elaborar perguntas | Microsoft Azure"
   description="Saiba como elaborar uma pergunta de ciências dados na ciência de dados para principiantes vídeo 3. Inclui uma comparação de classificação e regressão perguntas."
   keywords="perguntas de ciências de dados, elaborar perguntas, perguntas de regressão, perguntas de classificação, nítidos pergunta"
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

# <a name="ask-a-question-you-can-answer-with-data"></a>Fazer uma pergunta que pode atender com dados

## <a name="video-3-data-science-for-beginners-series"></a>Vídeo 3: Dados ciência para série principiantes

Saiba como elaborar uma pergunta de ciências dados na ciência de dados para principiantes vídeo 3. Este vídeo inclui uma comparação de perguntas para algoritmos classificação e regressão.

Para tirar o máximo partido da série, veja-los a todos. [Ir para a lista de vídeos](#other-videos-in-this-series)

> [AZURE.VIDEO data-science-for-beginners-ask-a-question-you-can-answer-with-data]

## <a name="other-videos-in-this-series"></a>Outros vídeos nesta série

*Dados ciência para principiantes* é uma breve introdução às science de dados na cinco vídeos de curtos duração.

  * Vídeo 1: [As respostas a 5 perguntas dados ciência](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 mínimo 14 seg.)*
  * Vídeo 2: [está os seus dados pronta para ser ciência de dados?](machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 mínimo 56 seg.)*
  * Vídeo 3: Fazer uma pergunta que pode atender com dados
  * Vídeo 4: [Prever uma resposta com um modelo de simple](machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 mínimo 42 seg.)*
  * Vídeo 5: [Copiar o trabalho de outras pessoas para fazer ciência de dados](machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 mínimo 18 seg.)*

## <a name="transcript-ask-a-question-you-can-answer-with-data"></a>Transcrição: Fazer uma pergunta que pode atender com dados

Bem-vindo ao terceiro vídeo na série "Dados ciência para principiantes".  

Este um, obterá algumas sugestões para elaboração uma pergunta que pode atender com os dados.

Poderá obter mais terminar neste vídeo, se que pela primeira vez, ver os vídeos anteriores duas nesta série: "pode atender a 5 perguntas dados de ciência" e "É os seus dados estão prontos para ser ciência de dados?"

## <a name="ask-a-sharp-question"></a>Fazer uma pergunta nítida

Tenha falámos sobre como ciência de dados é o processo de utilização de nomes (também denominados categorias ou etiquetas) e números para prever uma resposta a uma pergunta. Mas não pode ser apenas qualquer questão; tem de ser um *pergunta nítida.*

Uma pergunta vaga não tem de ser respondida com um nome ou um número. Tem uma pergunta nítida.

Imagine que se encontram uma luz mágico com um genie quem vai com respeito pela verdade correspondem a qualquer pergunta. Mas é uma genie mischievous e ele irá tentar fazer dele answer como vaga e confuso à medida que ele pode obter ausente com. Que pretende afixar contactá-la para baixo com uma pergunta por isso, hermético ele não pode ajudar a mas indicar-lhe que pretender saber.

Se fosse fazer uma pergunta vaga, tal como "O que vai acontecer com minhas ações?", o genie poderá responder, "irá alterar o preço". Que é uma resposta truthful, mas não é muito útil.

Mas se foram fazer uma pergunta nítida, como "O que vai preço de venda minhas ações ser semana seguinte?", o genie não pode ajudar a mas dar-lhe um específico responder e prever um preço de venda.

## <a name="examples-of-your-answer-target-data"></a>Exemplos de uma resposta: dados de destino

Assim que elaborar a pergunta, verifique se tem exemplos da resposta nos seus dados.

Se os nossos pergunta é "O que vai preço de venda minhas ações ser semana seguinte?" em seguida, temos para se certificar de que os nossos dados incluem o histórico do preço de cotações.

Se os nossos pergunta for "qual carro no meu frota vai pela primeira vez a falhar?" em seguida, temos para se certificar de que os nossos dados incluem informações sobre falhas anterior.

![Dados de destino - exemplos de uma resposta. Elaborar uma pergunta de ciências de dados.](./media/machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data/machine-learning-data-science-target-data.png)

Estes exemplos de respostas são denominados um alvo. Um destino é o que recomendamos está a tentar prever sobre os pontos de dados futuras, quer seja uma categoria ou um número.

Se não tiver quaisquer dados de destino, terá de obter algumas. Não poderá responder à pergunta sem ele.

## <a name="reformulate-your-question"></a>Reformulate a pergunta

Por vezes, pode reescrever a pergunta para obter uma resposta mais útil.

A pergunta "É deste ponto de dados A ou B?" prevê, com a categoria (ou nome ou etiqueta) de algo. Para atendê-lo, utilizamos um *algoritmo de classificação*.

A pergunta "Quanto custa?" ou "Quantos?" prevê, com um montante. Para atender-Utilizamos um *algoritmo de regressão*.

Para ver como podemos podem transformar a estas, vamos ver a pergunta, "qual notícia é mais interessantes para este leitor?" -Pede uma previsão de uma única opção a partir de muitas possibilidades - por outras palavras "É este A ou B ou C ou D?" - e utilizaria um algoritmo de classificação.

Mas, esta pergunta pode ser mais fácil responder se que reescrevê-la como "como interessante é cada história nesta lista para este leitor?" Agora pode atribuir a cada artigo uma pontuação numérica e, em seguida, é fácil identificar o artigo com pontuação mais alta. Este é um reformular da pergunta de classificação para uma pergunta de regressão ou quanto?

![Reformulate a pergunta. Pergunta de classificação vs. pergunta de regressão.](./media/machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data/machine-learning-data-science-classification-question-vs-regression-question.png)

Como pedir a que uma pergunta é uma pista para o qual o algoritmo pode dar-lhe uma resposta.

Encontrará determinadas famílias de tipos dos algoritmos - como as apresentadas no nosso exemplo da história de notícias - estreitamente relacionados com. Pode reformulate a pergunta para utilizar o algoritmo que lhe dá a resposta mais úteis.

Mas, mais importantes, essa pergunta nítida - na pergunta que pode atender com os dados. E certifique-se de que tem os dados corretos para atendê-lo.

Tenha falámos sobre algumas princípios básicos para fazer uma pergunta que pode atender com os dados.

Certifique-se de que dar saída os outros vídeos "Dados ciência para principiantes" do Microsoft Azure máquina Learning.


## <a name="next-steps"></a>Próximos passos

  * [Experimente uma experiência de ciências dados primeira com o computador aprendizagem Studio](machine-learning-create-experiment.md)
  * [Obter uma introdução à máquina formação no Microsoft Azure](machine-learning-what-is-machine-learning.md)
