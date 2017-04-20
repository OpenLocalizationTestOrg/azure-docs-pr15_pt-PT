<properties
    pageTitle="Como escolher os algoritmos de aprendizagem de máquina | Microsoft Azure"
    description="Como escolher a aprendizagem de máquina Azure algoritmos para aprendizagem deficiente e controlado em experiências de clustering, classificação ou regressão."
    services="machine-learning"
    documentationCenter=""
    authors="brohrer"
    manager="jhubbard"
    editor="cgronlun"
    tags=""/>
    
<tags
    ms.service="machine-learning"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="08/09/2016"
    ms.author="brohrer;garye" />

# <a name="how-to-choose-algorithms-for-microsoft-azure-machine-learning"></a>Como escolher os algoritmos de aprendizagem de máquinas de Azure Microsoft

A resposta à pergunta "Qual o computador algoritmo de aprendizagem deve utilizar?" é sempre "Depende." Depende do tamanho, a qualidade e a natureza dos dados. O pretende fazer com a resposta depende. Depende de como as expressões matemáticas do algoritmo foi convertida em instruções para o computador que estiver a utilizar. E depende a quantidade de tempo tiver. Mesmo as cientistas de dados com mais experiência podem saber qual o algoritmo terá um desempenho melhor antes de tentá-los.

## <a name="the-machine-learning-algorithm-cheat-sheet"></a>O algoritmo de aprendizagem de máquinas fazer batota folha

A **Microsoft Azure máquina aprendizagem algoritmo fazer batota folha** ajudam-na escolher a máquina direita algoritmo para soluções analytics previsão da biblioteca Microsoft Azure máquina aprendizagem de algoritmos de aprendizagem.
Este artigo orienta-como utilizá-la.

> [AZURE.NOTE] Para transferir a folha de batota e siga juntamente com o presente artigo, vá para [aprender a folha de batota algoritmo para o Microsoft Azure máquina aprendizagem Studio de máquina](machine-learning-algorithm-cheat-sheet.md).

Esta folha de batota tem uma audiência específica muito em mente: uma início dados cientista com aprendizagem de máquinas de nível de estudante universitário, tentar escolher um algoritmo para começar no Azure máquina aprendizagem Studio. Isto significa que o torna alguns generalizações e oversimplifications, mas ele indicará numa direcção segura. Significa também que existem muitos algoritmos não listados aqui. À medida que cresce Azure aprendizagem de máquinas para abranger um conjunto mais completo de métodos disponíveis, adicionaremos-los.

Estas recomendações são compilados comentários e sugestões de uma grande quantidade de dados cientistas e peritos de aprendizagem de máquina. A Microsoft não concordar em tudo, mas experimentei harmonizar os nossos pareceres num consenso em bruto. A maior parte das demonstrações de desacordo começa com "Depende..."

### <a name="how-to-use-the-cheat-sheet"></a>Como utilizar a folha de batota

Ler os caminho e o algoritmo de etiquetas no gráfico como "para * &lt;nome do caminho&gt; * utilizar * &lt;algoritmo&gt;*." Por exemplo, "para a *velocidade* utilizar *uma regressão logística classe dois*." Por vezes, mais do que uma sucursal será aplicada.
Por vezes, nenhum deles será um ajuste perfeito. Se estiver a destinados a ser recomendações de regra de miniatura, pelo que não se preocupe que seja exacto.
Várias cientistas de dados que é talked com o mesmo que a forma só-se para localizar o melhor algoritmo é tentar todos os.

Eis um exemplo da [Galeria de análise de Cortana](http://gallery.cortanaintelligence.com/) de uma experiência que tenta vários algoritmos contra os mesmos dados e compara os resultados: [Comparar classificadores de classe múltipla: letra reconhecimento](http://gallery.cortanaintelligence.com/Details/a635502fc98b402a890efe21cec65b92).

>[AZURE.TIP] Para transferir e imprimir um diagrama que dá uma visão geral das capacidades do computador aprendizagem Studio, consulte o [diagrama de descrição geral das capacidades de Azure máquina aprendizagem Studio](machine-learning-studio-overview-diagram.md).

## <a name="flavors-of-machine-learning"></a>Tipos de aprendizagem de máquinas

### <a name="supervised"></a>Controlado

Algoritmos de aprendizagem vigiadas fazer previsões baseados num conjunto de exemplos. Por exemplo, os preços das cotações históricos podem ser utilizado para tentativas de risco a preços futuros. Cada exemplo utilizado para a formação é rotulado com o valor dos juros — neste caso o preço das acções. Um algoritmo de aprendizagem vigiadas procura padrões nesses rótulos de valor. Pode utilizar quaisquer informações que podem ser relevantes — o dia da semana, a época, dados financeiros da empresa, o tipo de indústria, a presença de geopolicitical incómodas eventos — e cada algoritmo de procura de diferentes tipos de padrões. Depois do algoritmo encontrou o padrão melhor possível, utiliza esse padrão para fazer previsões para dados de ensaios sem etiqueta, preços de amanhã.

Este é um tipo de popular e úteis de aprendizagem de máquinas. Com uma excepção, todos os módulos na aprendizagem de máquina Azure sejam controlados algoritmos de aprendizagem. Existem vários tipos específicos de aprendizagem controlado que estão representados na aprendizagem de máquina Azure: detecção de classificação e anomalia regressão.

* **Classificação**. Quando os dados estão a ser utilizados para prever uma categoria, aprendizagem vigiada também é designado por classificação. Isto acontece quando atribuir uma imagem como uma imagem de um gato ou um cão. Quando existem apenas duas opções, isto é chamado **duas classes** ou **classificação binomial**. Quando existem mais categorias, como aquando da previsão o vencedor do torneio NCAA Março Madness, este problema é conhecido como **várias classe classificação**.

* **Regressão**. Quando é que está a ser previsto um valor, tal como acontece com preços de cotações, aprendizagem controlada é designado por regressão.

* **Detecção de anomalias**. Por vezes, o objectivo é identificar os pontos de dados que são simplesmente invulgares. Por exemplo, na detecção de fraudes, quaisquer padrões de despesas do cartão de crédito usual forem suspeitas. As variações possíveis são tão numerosas e os exemplos de formação assim alguns, que não é possível obter informações sobre as actividades fraudulentas aspecto. A abordagem que demora a detecção de anomalias é simplesmente aprender a que actividade normal assemelha (utilizando um transacções não fraudulentas do histórico) e identificar tudo o que é significativamente diferente.

### <a name="unsupervised"></a>Deficiente

Na aprendizagem deficiente, pontos de dados não tem rótulos associados. Em vez disso, o objectivo de um algoritmo de aprendizagem deficiente é para organizar os dados de alguma forma ou para descrever a respectiva estrutura. Isto pode significar agrupando-o em clusters ou para localizar formas diferentes de visualizar dados complexos para que apareça mais simples ou mais organizado.

### <a name="reinforcement-learning"></a>Aprendizagem de reforço

No reforço de aprendizagem, o algoritmo obtém escolher uma acção em resposta a cada ponto de dados. O algoritmo de aprendizagem também recebe um sinal de recompensa um momento posterior, indicando a boa como a decisão foi.
Com base neste, o algoritmo modifica sua estratégia para atingir a recompensa mais elevada. Actualmente não existem nenhum reforço aprendizagem módulos de algoritmo de aprendizagem de máquina Azure. Aprendizagem de reforço é comum em robotics, em que o conjunto de leituras de sensor, um ponto no tempo é um ponto de dados e o algoritmo tem de escolher a acção seguinte o robot. É também que um natural apto para Internet coisas aplicações.

## <a name="considerations-when-choosing-an-algorithm"></a>Considerações sobre a selecção um algoritmo

### <a name="accuracy"></a>Precisão

Obter a resposta mais exacta possível nem sempre é necessário.
Por vezes, uma aproximação é adequada, consoante o que pretende utilizá-la. Se for esse o caso, poderá conseguir cortar drasticamente o tempo de processamento por aderente com mais aproximados métodos. Outra vantagem dos métodos aproximadas mais é que eles naturalmente têm tendência para evitar [overfitting](https://youtu.be/DQWI1kvmwRg).

### <a name="training-time"></a>Tempo de formação

O número de minutos ou horas necessárias para preparar um modelo bastante varia entre algoritmos. Tempo de formação é muitas vezes fortemente associada a exactidão — um normalmente acompanha o outro. Além disso, alguns algoritmos são mais sensíveis ao número de pontos de dados do que outros utilizadores.
Quando o tempo é limitado, pode unidade à escolha do algoritmo, especialmente quando o conjunto de dados for grande.

### <a name="linearity"></a>Linearidade

Lotes de algoritmos de aprendizagem do computador que utilize de linearidade. Algoritmos de classificação linear partem do princípio de que as classes podem ser separados por uma linha recta (ou respectiva superior dimensional analógica). Estas incluem uma regressão logística e suportem máquinas de vector (conforme implementados Azure aprendizagem de máquinas).
Algoritmos de regressão linear partem do princípio de que as tendências dos dados seguem uma linha recta. Estas hipóteses não são danificados para alguns problemas, mas outros trazer precisão para baixo.

![Bounday de classe não linear][1]

***Limite de classe não linear*** *-depender de um algoritmo de classificação linear resultaria precisão muito baixa*

![Dados com uma tendência linear][2]

***Dados com uma tendência linear*** *-utilizando um método de regressão linear iria gerar muito maiores erros que o necessário*

Não obstante os perigos, algoritmos lineares são muito populares como a primeira linha de ataque. Tendem a ser algorithmically simples e rápido para efectuá-la.

### <a name="number-of-parameters"></a>Número de parâmetros

Os parâmetros são botões que obtém um investigador de dados para activar quando configura um algoritmo. São números que afectam o comportamento do algoritmo, tolerância a erros ou número de iterações ou opções entre as variantes do modo como se comporta o algoritmo. O tempo de formação e a precisão do algoritmo, por vezes, podem ser muito sensíveis para obter apenas as definições correctas. Normalmente, os algoritmos com parâmetros de números grandes requerem o maior parte dos tentativa e erro para encontrar uma boa combinação.

Em alternativa, existe um bloco de módulo de [remoção do parâmetro](machine-learning-algorithm-parameters-optimize.md) na aprendizagem de máquinas de Azure tenta automaticamente todas as combinações de parâmetros em qualquer granularidade que escolher. Enquanto esta é uma excelente forma de certificar-se de que ter expandido o espaço de parâmetro, o tempo necessário para preparar um modelo aumenta exponencialmente com o número de parâmetros.

A cabeça é que ter muitos dos parâmetros normalmente indica que um algoritmo tem uma maior flexibilidade. Muitas vezes pode atingir muito boa precisão. Desde que pode encontrar a combinação adequada de definições de parâmetros.

### <a name="number-of-features"></a>Número de funcionalidades

Para determinados tipos de dados, o número de funcionalidades pode ser muito grande em comparação com o número de pontos de dados. Isto acontece frequentemente com avanços ou dados textuais. Pode bog o grande número de funcionalidades para baixo alguns algoritmos de aprendizagem, tornando tempo unfeasibly tempo de formação. Máquinas de Vector de suporte são particularmente adequadas a este caso (ver infra).

### <a name="special-cases"></a>Casos especiais

Alguns algoritmos de aprendizagem fazer suposições nomeadamente sobre a estrutura dos dados ou os resultados pretendidos. Se conseguir localizar um adequado às suas necessidades, pode dar-lhe resultados mais útil, previsões mais precisas ou mais rápidas horas de formação.

|**Algoritmo**|**Precisão**|**Tempo de formação**|**Linearidade**|**Parâmetros**|**Notas**|
|---|:---:|:---:|:---:|:---:|---|
|**Duas classes classificação**| | | | | |
|[regressão logística](https://msdn.microsoft.com/library/azure/dn905994.aspx)                    | |●|●|5| |
|[floresta de decisão](https://msdn.microsoft.com/library/azure/dn906008.aspx)|●|○| |6| |
|[jungle de decisão](https://msdn.microsoft.com/library/azure/dn905976.aspx)|●|○| |6|Requisitos de espaço de memória insuficiente|
|[árvore de decisão aumentada](https://msdn.microsoft.com/library/azure/dn906025.aspx)|●|○| |6|Requisitos de espaço de memória de grandes dimensões|
|[rede de sistema nervoso](https://msdn.microsoft.com/library/azure/dn905947.aspx)|●| | |9|[É possível adicionais de personalização](http://go.microsoft.com/fwlink/?LinkId=402867)|
|[perceptron médio](https://msdn.microsoft.com/library/azure/dn906036.aspx)|○|○|●|4| |
|[máquina de vector do suporte](https://msdn.microsoft.com/library/azure/dn905835.aspx)| |○|●|5|Ideal para conjuntos de funcionalidades de grandes dimensões|
|[máquina de vector do suporte alargado localmente](https://msdn.microsoft.com/library/azure/dn913070.aspx)|○| | |8|Ideal para conjuntos de funcionalidades de grandes dimensões|
|[Máquina de ponto dos Bayes](https://msdn.microsoft.com/library/azure/dn905930.aspx)| |○|●|3| |
|**Classificação de classe múltiplas**| | | | | |
|[regressão logística](https://msdn.microsoft.com/library/azure/dn905853.aspx)| |●|●|5| |
|[floresta de decisão](https://msdn.microsoft.com/library/azure/dn906015.aspx)|●|○| |6| |
|[jungle de decisão](https://msdn.microsoft.com/library/azure/dn905963.aspx)|●|○| |6|Requisitos de espaço de memória insuficiente|
|[rede de sistema nervoso](https://msdn.microsoft.com/library/azure/dn906030.aspx)|●| | |9|[É possível adicionais de personalização](http://go.microsoft.com/fwlink/?LinkId=402867)|
|[um-v-tudo](https://msdn.microsoft.com/library/azure/dn905887.aspx)|-|-|-|-|Ver propriedades do método da classe de dois seleccionada|
|**Regressão**| | | | | |
|[linear](https://msdn.microsoft.com/library/azure/dn905978.aspx)| |●|●|4| |
|[Bayesian linear](https://msdn.microsoft.com/library/azure/dn906022.aspx)| |○|●|2| |
|[floresta de decisão](https://msdn.microsoft.com/library/azure/dn905862.aspx)|●|○| |6| |
|[árvore de decisão aumentada](https://msdn.microsoft.com/library/azure/dn905801.aspx)|●|○| |5|Requisitos de espaço de memória de grandes dimensões|
|[quantile de floresta rápida](https://msdn.microsoft.com/library/azure/dn913093.aspx)|●|○| |9|Distribuições em vez de previsões de ponto|
|[rede de sistema nervoso](https://msdn.microsoft.com/library/azure/dn905924.aspx)|●| | |9|[É possível adicionais de personalização](http://go.microsoft.com/fwlink/?LinkId=402867)|
|[POISSON](https://msdn.microsoft.com/library/azure/dn905988.aspx)| | |●|5|Tecnicamente registo linear. Para a estimativa da conta|
|[ordinal](https://msdn.microsoft.com/library/azure/dn906029.aspx)| | | |0|Para a estimativa da ordem de classificação|
|**Detecção de anomalias**| | | | | |
|[máquina de vector do suporte](https://msdn.microsoft.com/library/azure/dn913103.aspx)|○|○| |2|Especialmente ideal para conjuntos de funcionalidades de grandes dimensões|
|[Detecção de anomalias baseado em APC](https://msdn.microsoft.com/library/azure/dn913102.aspx)| |○|●|3| |
|[Meios de K](https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/)| |○|●|4|Um algoritmo de clustering|


**Propriedades de algoritmo:**

**●** - mostra precisão excelente, horas de formação rápida e a utilização de linearidade

**○** - mostra boa precisão e as horas de formação moderado

## <a name="algorithm-notes"></a>Notas de algoritmo

### <a name="linear-regression"></a>Regressão linear

Conforme mencionado anteriormente, a [regressão linear](https://msdn.microsoft.com/library/azure/dn905978.aspx) ajusta uma linha (ou plano ou hyperplane) para o conjunto de dados. É um workhorse, simple e rápida, mas poderá ser demasiado simplistic para alguns problemas.
Marque aqui para obter uma [Iniciação de regressão linear](machine-learning-linear-regression-in-azure.md).

![Dados com uma tendência linear][3]

***Dados com uma tendência linear***

### <a name="logistic-regression"></a>Regressão logística

Embora confuso inclui 'regressão' no nome, uma regressão logística é realmente uma ferramenta poderosa para a classificação de [classe de duas](https://msdn.microsoft.com/library/azure/dn905994.aspx) e [multiclass](https://msdn.microsoft.com/library/azure/dn905853.aspx) . É rápido e simples. O facto de que utiliza da '-forma curva em vez de uma linha recta torna um ajuste natural para dividir os dados em grupos. Limites de classe linear de dá uma regressão logística, pelo que quando o utilizar, certifique-se uma aproximação linear algo que pode live com.

![A classe de dois dados com apenas uma funcionalidade uma regressão logística][4]

***Uma regressão logística para dados de duas-classe com apenas uma funcionalidade*** *-o limite de classe é o ponto em que a curva logística é apenas tão perto ambas as classes*

### <a name="trees-forests-and-jungles"></a>Jungles, árvores e florestas

Florestas de decisão ([regressão](https://msdn.microsoft.com/library/azure/dn905862.aspx), [classe de duas](https://msdn.microsoft.com/library/azure/dn906008.aspx)e [multiclass](https://msdn.microsoft.com/library/azure/dn906015.aspx)), jungles de decisão ([dois-class](https://msdn.microsoft.com/library/azure/dn905976.aspx) e [multiclass](https://msdn.microsoft.com/library/azure/dn905963.aspx)) e árvores de decisão aumentado ([regressão](https://msdn.microsoft.com/library/azure/dn905801.aspx) e [duas classes](https://msdn.microsoft.com/library/azure/dn906025.aspx)) sejam baseiam nos árvores de decisões, uma máquina de base destinada conceito de aprendizagem. Existem muitas variantes de árvores de decisão, mas desempenham todos a mesma coisa — subdividir o espaço de funcionalidade em regiões com principalmente a mesma etiqueta. Estes podem ser regiões da categoria consistente ou do valor constante, dependendo se está a fazer classificação ou regressão.

![Árvore de decisão subdivida um espaço de funcionalidade][5]

***Uma árvore de decisão subdivida um espaço de funcionalidade em regiões de valores aproximadamente uniformes***

Uma vez que um espaço de funcionalidade pode ser subdividido em regiões arbitrariamente pequenos, é fácil imagine dividi-lo finamente suficiente para que um ponto de dados por região, um exemplo extremo de overfitting. Para evitar esta situação, um conjunto grande de árvores são construídos com especial atenção matemática tomada, que as árvores não correlacionadas. A média nesta floresta"decisão" é uma árvore que evite overfitting. Florestas de decisão, podem utilizar uma grande quantidade de memória. Jungles de decisão são uma variante que consome menos memória cargo ligeiramente mais tempo de formação.

As árvores de decisão aumentado evitar overfitting, limitando o número de vezes que podem subdividir e como poucos pontos de dados são permitidos em cada região. O algoritmo constrói uma sequência de árvores, cada um dos quais aprende para compensar o erro para a esquerda da árvore antes. O resultado é um aprendiz muito precisa que tende a utilizar uma grande quantidade de memória. Para obter a descrição técnica completa, dar saída de [papel original de Friedman](http://www-stat.stanford.edu/~jhf/ftp/trebst.pdf).

[Regressão de quantile de floresta rápida](https://msdn.microsoft.com/library/azure/dn913093.aspx) é uma variante de árvores de decisão para o caso especial onde pretende saber não apenas o valor normal (médio) dos dados dentro de uma região, mas também sua distribuição sob a forma de quantiles.

### <a name="neural-networks-and-perceptrons"></a>Redes nervoso e perceptrons

Redes nervoso são inspirados cérebro algoritmos que abrangem problemas de [multiclass](https://msdn.microsoft.com/library/azure/dn906030.aspx), [classe de duas](https://msdn.microsoft.com/library/azure/dn905947.aspx)e [regressão](https://msdn.microsoft.com/library/azure/dn905924.aspx) de aprendizagem. Provêm de uma variedade infinita, mas as redes nervoso na aprendizagem de máquinas Azure estão todos a forma de gráficos acíclicos directo. Isto significa que funcionalidades de introdução são passadas reencaminhar (nunca para trás) através de uma sequência de camadas antes de a ser transformado saídas. Em cada camada, factores de produção são ponderados em várias combinações, somados e passadas para a próxima camada. Esta combinação de cálculos simples como resultado a capacidade para obter informações sobre tendências de limites e dados de classe sofisticados, aparentemente por magia. Redes em camadas muitos deste género efectuam a "aprendizagem abrangente" que combustíveis tanta comunicação do tech e ficção.

Este elevado desempenho não é fornecido gratuitamente, embora. Redes nervoso podem demorar muito tempo para preparar, particularmente para conjuntos de dados grandes com muitas das funcionalidades. Além disso, poderão ter mais parâmetros do que a maior parte dos algoritmos, o que significa que o chaminés de parâmetro expande o tempo de formação bastante.
E para os overachievers que desejem para [especificar sua própria estrutura de rede](http://go.microsoft.com/fwlink/?LinkId=402867), as possibilidades são inexhaustible.

<a name="boundaries-learned-by-neural-networks6"></a>![Limites extraídos por redes sistema nervoso][6]
---------------------------

***Os limites extraídos por redes nervoso podem ser complexas e irregular***

[Média de duas-classe perceptron](https://msdn.microsoft.com/library/azure/dn906036.aspx) é a resposta nervoso redes skyrocketing horas de formação. Utiliza uma estrutura de rede que fornece limites de classe linear. É quase primitivo pelas normas de hoje, mas tem uma longa história de trabalhar sólida e é suficientemente pequeno para aprender rapidamente.

### <a name="svms"></a>SVMs

Máquinas de vector de suporte (SVMs) localizar o limite que separa as classes por como largura de uma margem quanto possível. Quando duas classes não podem ser claramente separadas, os algoritmos de localizar o limite de melhor possível. Tal como consta Azure aprendizagem de máquinas, a [classe de dois SVM](https://msdn.microsoft.com/library/azure/dn905835.aspx) fá-lo apenas uma linha recta. (No falar de SVM, utiliza um kernel linear.) Porque torna esta aproximação linear, é possível a execução seja relativamente rápida. Sempre que realmente brilha é com funcionalidade intensa dados, como texto ou do genoma. Nestes casos SVMs conseguem separar classes mais rapidamente e com menos overfitting a maior parte dos outros algoritmos, para além de requerer um montante modesto de memória.

![Limite de classe da máquina de vector de suporte][7]

***Um limite de classes normais do suporte vector máquina maximiza a margem de separar as duas classes***

Outro produto da Microsoft Research, a [classe dois localmente abrangente SVM](https://msdn.microsoft.com/library/azure/dn913070.aspx) é uma variante não linear de SVM que retém a maior parte da memória e velocidade eficiência da versão linear. É ideal para os casos em que a abordagem linear não dá respostas precisas o suficiente. Os programadores na posse-fast decompor o problema para uma série de pequenos problemas SVM lineares. Leia a [Descrição completa](http://research.microsoft.com/um/people/manik/pubs/Jose13.pdf) para os obter detalhes sobre como são extraídos desactivar este truque também.

Utilizar uma extensão de inteligente de SVMs não lineares, o [SVM de uma classe](https://msdn.microsoft.com/library/azure/dn913103.aspx) desenha um limite que descreve fortemente todo o conjunto de dados. É útil para detecção de anomalias. Quaisquer novos pontos de dados que não correspondam ao momento limite são invulgares ser digno de nota.

### <a name="bayesian-methods"></a>Métodos de Bayesian

Os métodos de Bayesian ter uma qualidade altamente desejável: evitar a overfitting. Fazem analisando alguns previamente mais prováveis de distribuição da resposta. Byproduct outro desta abordagem é que têm muito poucos parâmetros. Aprendizagem de máquinas Azure tem os algoritmos de Bayesian de classificação ([máquina de ponto dos dois classe Bayes](https://msdn.microsoft.com/library/azure/dn905930.aspx)) e regressão ([Bayesian regressão linear](https://msdn.microsoft.com/library/azure/dn906022.aspx)).
Tenha em atenção que estes partem do princípio de que os dados podem ser divididos ou munir de uma linha recta.

Uma nota históricos, máquinas de ponto dos Bayes foram desenvolvidas em Microsoft Research. Têm algum trabalho teórico excepcionalmente beautiful atrás das mesmas. O interessado aluno é direccionado para o [artigo original em JMLR](http://jmlr.org/papers/volume1/herbrich01a/herbrich01a.pdf) e um [blogue perceptível pelo Moreira o Carlos](http://blogs.technet.com/b/machinelearning/archive/2014/10/30/embracing-uncertainty-probabilistic-inference.aspx).

### <a name="specialized-algorithms"></a>Algoritmos especializados

Se tiver um objectivo muito específico pode ser com sorte. Na colecção de aprendizagem de máquinas Azure existem algoritmos especializados na previsão de classificação ([regressão ordinal](https://msdn.microsoft.com/library/azure/dn906029.aspx)), contagem de previsão ([regressão de Poisson](https://msdn.microsoft.com/library/azure/dn905988.aspx)) e detecção de anomalias (uma com base na [análise de componentes principais](https://msdn.microsoft.com/library/azure/dn913102.aspx) e nas s [suporta a máquina de vector](https://msdn.microsoft.com/library/azure/dn913103.aspx)).
E existe um isolado seja substituído clustering algoritmo de bem ([meios K](https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/)).

![Detecção de anomalias baseado em APC][8]

***Detecção de anomalias baseado em APC*** *-a maioria dos dados pertencem a uma distribuição stereotypical; pontos afastem significativamente que distribuição forem suspeitas*

![Conjunto de dados agrupados utilizando meios K][9]

***Um conjunto de dados está agrupado em 5 clusters utilizando meios K***

Também é um de ensemble [uma-v-todos os multiclass classificador](https://msdn.microsoft.com/library/azure/dn905887.aspx), que divide o problema de classificação de N-classe em problemas de classificação de duas-classe de N-1. A exactidão, tempo de formação e propriedades de linearidade são determinadas pela classe dois classificadores utilizados.

![Classe de dois classificadores combinados para formar um classificador de classe de três][10]

***Combinar um par de classificadores de classe de dois para formar um classificador de classe de três***

Também Azure máquina aprendizagem inclui acesso a um quadro de aprendizagem poderoso computador com o título de [Vowpal Wabbit](https://msdn.microsoft.com/library/azure/8383eb49-c0a3-45db-95c8-eb56a1fef5bf).
VW esqueça categorização aqui, uma vez que pode obter informações sobre problemas de classificação e regressão e ainda pode aprender dados parcialmente sem nome. Pode configurar para utilizar qualquer um dos vários algoritmos, funções de perdas e os algoritmos de optimização de aprendizagem. Foi concebido em relação ao solo para cima para ser eficaz, paralela e extremamente rápido. Este processa os conjuntos de funcionalidades ridiculously grande com pouco esforço aparente.
Iniciado e conduzido pela John Langford do Microsoft Research próprio, VW é uma entrada de uma fórmula num campo de algoritmos de carro de stock. Nem todos os problemas caiba VW mas, se o seu tiver, poderá ser compensa o tempo a transpor a curva de aprendizagem na respectiva interface. Também está disponível como [código de origem aberto autónomo](https://github.com/JohnLangford/vowpal_wabbit) em vários idiomas.


<!-- Media -->

[1]: ./media/machine-learning-algorithm-choice/image1.png
[2]: ./media/machine-learning-algorithm-choice/image2.png
[3]: ./media/machine-learning-algorithm-choice/image3.png
[4]: ./media/machine-learning-algorithm-choice/image4.png
[5]: ./media/machine-learning-algorithm-choice/image5.png
[6]: ./media/machine-learning-algorithm-choice/image6.png
[7]: ./media/machine-learning-algorithm-choice/image7.png
[8]: ./media/machine-learning-algorithm-choice/image8.png
[9]: ./media/machine-learning-algorithm-choice/image9.png
[10]: ./media/machine-learning-algorithm-choice/image10.png
