<properties 
    pageTitle="Guia para o líquido # redes nervoso especificação idioma | Microsoft Azure" 
    description="Sintaxe para o líquido # nervoso redes idioma especificação, juntamente com exemplos de como criar um modelo de rede nervoso personalizada no Microsoft Azure ML utilizando líquido#" 
    services="machine-learning" 
    documentationCenter="" 
    authors="jeannt" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="jeannt"/>



# <a name="guide-to-net-neural-network-specification-language-for-azure-machine-learning"></a>Guia para a linguagem de especificação líquido # rede nervoso para Azure máquina aprendizagem

## <a name="overview"></a>Descrição geral
Líquido # é uma linguagem desenvolvida pela Microsoft, que é utilizado para definir arquitecturas de rede nervoso para módulos de rede nervoso no Microsoft Azure máquina aprendizagem. Neste artigo, vai aprender:  

-   Conceitos básicos relacionados com a nervoso redes
-   Requisitos de rede nervoso e como definir os principais componentes
-   A sintaxe e palavras-chave da linguagem de especificação líquido #
-   Exemplos de redes nervoso personalizados criados utilizando líquido# 
    
[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]  

## <a name="neural-network-basics"></a>Noções básicas da rede nervoso
Uma estrutura de rede nervoso consiste em ***nós*** que estão organizadas em ***camadas***e ponderada ***ligações*** (ou ***suaves***) entre os nós. As ligações são direccionais e tem de cada ligação de um nó de ***origem*** e um nó de ***destino*** .  

Cada ***camada trainable*** (um ocultas ou uma camada de saída) tem um ou mais ***conjuntos de ligação***. Um pacote de ligação consiste numa camada de origem e uma especificação de ligações a partir dessa camada de origem. Todas as ligações no pacote determinado partilham a mesma ***camada de origem*** e a mesma ***camada de destino***. Na líquido #, um pacote de ligação é considerado como pertencentes a camada de destino do pacote.  
 
Líquido # suporta vários tipos de ligação conjuntos, que permite-lhe personalizar as entradas de forma são mapeados para camadas ocultas e mapeados às saídas de.   

A predefinição ou pacote padrão é um **pacote completo**, em que cada nó na camada de origem está ligada a cada nó na camada de destino.  

Para além disso, líquido # suporta os seguintes quatro tipos de conjuntos de ligação avançadas:  

-   **Conjuntos de filtrada**. O utilizador pode definir um predicado utilizando as localizações do nó de camada de origem e o nó de camada de destino. Nós estão ligados sempre que o predicado for VERDADEIRO.
-   **Conjuntos convolutional**. O utilizador pode definir bairros pequenas de nós na camada de origem. Cada nó na camada de destino está ligado a uma vizinhança de nós na camada de origem.
-   **Quadros de agrupamento** e **conjuntos de normalização de resposta**. Estas são semelhantes aos conjuntos convolutional em que o utilizador define bairros pequenas de nós na camada de origem. A diferença é que a espessura das extremidades nestes conjuntos não está trainable. Em vez disso, uma função predefinida é aplicada para os valores de nó de origem para determinar o valor de nó de destino.  

Utilizar líquido # para definir a estrutura de uma rede nervoso torna possível definir estruturas complexas como redes profundas nervoso ou convolutions das dimensões arbitrários, que são conhecidas para melhorar a aprendizagem nos dados como imagem, áudio ou vídeo.  

## <a name="supported-customizations"></a>Personalizações suportadas
A arquitetura de modelos de rede nervoso que criar no Azure máquina formação pode ser personalizada extensivamente utilizando líquido #. Pode:  

-   Crie camadas ocultas e controlar o número de nós nos cada camada.
-   Especificar o modo como camadas de estar ligado umas às outras.
-   Defina as estruturas de conectividade especiais, tais como convolutions e espessura de conjuntos de partilha.
-   Especifique as funções de ativação diferente.  

Para obter detalhes sobre a sintaxe da linguagem especificação, consulte o artigo [Especificação de estrutura](#Structure-specifications).  
 
Para obter exemplos da definição de redes nervoso para algumas tarefas, a partir do simplex para complexa, de aprendizagem automática que se comuns veja [exemplos](#Examples-of-Net#-usage).  

## <a name="general-requirements"></a>Requisitos gerais
-   Tem de ter camada exatamente uma saída, pelo menos uma camada de entrada e zero ou mais camadas ocultas. 
-   Cada camada tem um número fixo de nós, conceptual dispostas numa matriz retangular das dimensões arbitrários. 
-   Camadas de entrada tem sem parâmetros de formação associados e representam o ponto onde os dados da instância introduz a rede. 
-   Camadas trainable (ocultas e de saída camadas) tem associados parâmetros qualificados, conhecidos como espessura e desvios. 
-   Os nós de origem e destino tem de ser camadas separadas. 
-   Ligações devem ser incluindo; por outras palavras, não podem ser uma cadeia de ligações voltado para o nó de origem inicial.
-   A camada de saída não pode ser uma camada de origem de um pacote de ligação.  

## <a name="structure-specifications"></a>Especificações de estrutura
Uma especificação de estrutura da rede nervoso é composto por três secções: a **declaração de constante**, a **declaração de camadas**, a **declaração de ligação**. Também existe uma secção opcional **partilhar declaração** . As secções podem ser especificadas por qualquer ordem.  

## <a name="constant-declaration"></a>Declaração de constante 
Uma constante declaração é opcional. Fornece um meio para definir valores noutro local utilizadas na definição nervoso rede. A instrução de declaração é constituída por um identificador seguido por um sinal de igual e uma expressão de valor.   

Por exemplo, a seguinte instrução define uma constante **x**:  


    Const X = 28;  

Para definir as constantes de duas ou mais em simultâneo, incluir os valores e nomes dos identificadores chavetas e separá-las utilizando o ponto e vírgula. Por exemplo:  

    Const { X = 28; Y = 4; }  

No lado direito de cada expressão atribuição pode ser um número inteiro, um número real, um valor booleano (VERDADEIRO ou FALSO) ou numa expressão matemática. Por exemplo:  

    Const { X = 17 * 2; Y = true; }  

## <a name="layer-declaration"></a>Declaração de camada
A declaração de camada é necessária. Define o tamanho e a origem da camada, incluindo os seus conjuntos de ligação e atributos de. A instrução de declaração começa com o nome da camada (de entrada, ocultas ou de saída), seguido as dimensões da camada (uma cadeia de identificação de números inteiros positivos). Por exemplo:  

    input Data auto;
    hidden Hidden[5,20] from Data all;
    output Result[2] from Hidden all;  

-   O produto das dimensões é o número de nós na camada. Neste exemplo, existem duas dimensões [5,20], que significa que existem 100 nós na camada.
-   As camadas podem ser declaradas por qualquer ordem, com uma exceção: Se estiver definida mais do que uma camada de entrada, a ordem na qual são declarados tem de corresponder a ordem das funcionalidades nos dados de entrada.  


Para especificar que o número de nós numa camada ser determinado automaticamente, utilize a palavra-chave **automática** . A palavra-chave **automática** tem efeitos diferentes, consoante a camada:  

-   Uma declaração de camada de entrada, o número de nós é o número de funcionalidades nos dados de entrada.
-   Através de uma declaração camada oculta, o número de nós é o número especificado pelo valor do parâmetro de **número de nós ocultos**. 
-   Uma declaração de camada de saída, o número de nós é 2 para a classe de duas classificação, 1 para regressão e igual ao número de nós de saída para multiclass classificação.   

Por exemplo, a seguinte definição de rede permite que o tamanho de todas as camadas para determinado automaticamente:  

    input Data auto;
    hidden Hidden auto from Data all;
    output Result auto from Hidden all;  


Uma declaração de camada para uma camada trainable (ocultas ou saídas camadas), opcionalmente, pode incluir a saída função (também designada de uma função de ativação), que assume a **sigmoid** para os modelos de classificação e **linear** para os modelos de regressão. (Mesmo se utilizar o predefinido, pode pode mencionar explicitamente a função de ativação, se pretender para maior clareza.)

As seguintes funções de saída são suportadas:  

-   sigmoid
-   linear
-   softmax
-   rlinear
-   quadrado
-   RAIZQ
-   srlinear
-   Abs
-   TANH 
-   brlinear  

Por exemplo, a declaração seguinte utiliza a função **softmax** :  

    output Result [100] softmax from Hidden all;  

## <a name="connection-declaration"></a>Declaração de ligação
Imediatamente depois de definir a camada trainable, tem de declarar ligações entre as camadas que definiu. A declaração de pacote ligação começa com a palavra-chave **partir de**, seguido do nome da camada de origem do pacote e o tipo de pacote de ligação para criar.   

Atualmente, são suportados cinco tipos de conjuntos de ligação:  

-   Conjuntos **completo** , indicados por palavra-chave **todos os**
-   Conjuntos de **filtrada** , indicados pela palavra-chave **onde**, seguido de uma expressão predicada
-   Conjuntos de **Convolutional** , indicados pela palavra-chave **convolve**, seguido os atributos de espira
-   Conjuntos de **pooling** , indicados por palavras-chave **máximo do conjunto** ou **vermelha agrupamento**
-   Conjuntos de **normalização de resposta** , indicados por palavra-chave **norma de resposta**      

## <a name="full-bundles"></a>Conjuntos completos  

Um pacote de ligação completa inclui uma ligação a partir de cada nó na camada de origem para cada nó na camada de destino. Este é o tipo de ligação de rede predefinido.  

## <a name="filtered-bundles"></a>Conjuntos filtrados
Uma especificação de pacote ligação filtrada inclui um muito predicado, sintaxe, expresso como uma expressão de lambda c#. O exemplo seguinte define dois conjuntos de filtradas:  

    input Pixels [10, 20];
    hidden ByRow[10, 12] from Pixels where (s,d) => s[0] == d[0];
    hidden ByCol[5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;  

-   O predicado para _ByRow_, **s** é um parâmetro que representa um índice remissivo para a matriz retangular de nós da camada de entrada, _pixéis_, e **d** é um parâmetro que representa um índice remissivo para a matriz de nós de camada oculta, _ByRow_. O tipo de **s** e **d** é uma cadeia de identificação de números inteiros de comprimento duas. Em termos conceptuais, **s** intervalos sobre todos os pares de números inteiros com _0 < = s [0] < 10_ e _0 < = s[1] < 20_, e **d** intervalos sobre todos os pares de números inteiros, com _0 < = d [0] < 10_ e _0 < = d[1] < 12_. 
-   No lado direito da expressão predicado, existe uma condição. Neste exemplo, para todos os valores de **s** e **d** assim que a condição for VERDADEIRO, existe um limite a partir do nó de camada de origem para o nó de camada de destino. Assim sendo, esta expressão de filtro indica que o pacote inclui uma ligação a partir do nó definida pela **s** para o nó definido pela **d** em todos os casos em que s [0] é igual a d [0].  

Opcionalmente, pode especificar um conjunto de espessura de um pacote filtrada. O valor do atributo **espessuras** tem de ser uma cadeia de identificação de valores de vírgula flutuante com um comprimento que corresponde ao número de ligações definidas pelo pacote. Por predefinição, espessuras são geradas aleatoriamente.  

Valores de peso são agrupadas pelo índice de nó de destino. Isto é, se o primeiro nó de destino estiver ligado a K nós de origem, os elementos de _K_ primeiros de cadeia de identificação a **espessura** estão espessura para o primeiro nó de destino, na ordem dos índices de origem. O mesmo aplica-se para os restantes nós de destino.  

É possível especificar espessuras diretamente como valores constantes. Por exemplo, se a espessura que aprendeu anteriormente, pode especificá-las como constantes utilizando esta sintaxe:

    const Weights_1 = [0.0188045055, 0.130500451, ...]


## <a name="convolutional-bundles"></a>Conjuntos convolutional
Quando os dados de formação tem uma estrutura homogénea, ligações convolutional são geralmente utilizadas para obter informações sobre funcionalidades de alto nível dos dados. Por exemplo, na imagem, podem ser bastante uniformes dados de áudio ou vídeo, espacial ou temporal dimensão.  

Conjuntos convolutional empregam retangular **kernels** deslizam através das dimensões. Basicamente, cada kernel define um conjunto de espessura aplicada no locais bairros, designados de **aplicações kernel**. Cada aplicação kernel corresponde a um nó na camada de origem, o que é referido como o **nó central**. Espessura de um kernel é partilhada entre muitas ligações. Um pacote convolutional, cada kernel é retangular e todas as aplicações de kernel têm o mesmo tamanho.  

Conjuntos convolutional suportam os seguintes atributos:

**InputShape** define a dimensão da camada de origem para fins deste pacote convolutional. O valor tem de ser uma cadeia de identificação de números inteiros positivos. O produto dos números inteiros tem de ser igual o número de nós na camada de origem, mas caso contrário, não precisa corresponder ao dimensão declarada para a camada de origem. O comprimento da cadeia de identificação, torna-se o valor de **aridade** para o pacote convolutional. (Normalmente aridade refere-se para o número de argumentos ou operandos que pode tomar uma função.)  

Para definir a forma e localizações do miolo, utilize os atributos **KernelShape**, **transpor**, **Preenchimento**, **LowerPad**e **UpperPad**:   

-   **KernelShape**: (obrigatório) define a dimensão de cada kernel para o pacote convolutional. O valor tem de ser uma cadeia de identificação de números inteiros positivos com um comprimento que é igual à aridade do pacote. Cada componente desta cadeia de identificação tem de ser não maior que o componente de **InputShape**correspondente. 
-   **Transpor**: (opcional) define as dimensões de passo deslizante dos espira (tamanho de um passo para cada dimensão), que é a distância entre os nós centrais. O valor tem de ser uma cadeia de identificação de números inteiros positivos com um comprimento que corresponde à aridade do pacote. Cada componente desta cadeia de identificação tem de ser não maior que o componente de **KernelShape**correspondente. O valor predefinido é uma cadeia de identificação com todos os componentes iguais a um. 
-   **Partilha**: (opcional) define a espessura de partilha para cada dimensão da espira. O valor pode ser um valor booleano único ou de uma cadeia de identificação de valores booleanos com um comprimento que corresponde à aridade do pacote. Um valor booleano única é expandido para ser uma cadeia de identificação do comprimento correto com todos os componentes iguais ao valor especificado. O valor predefinido é uma cadeia de identificação que consiste em todos os valores verdadeiros. 
-   **MapCount**: (opcional) define o número da funcionalidade mapas para o pacote convolutional. O valor pode ser um número inteiro positivo único ou de uma cadeia de identificação de números inteiros positivos com um comprimento que corresponde à aridade do pacote. Um valor único inteiro é expandido para ser uma cadeia de identificação do comprimento correto com os componentes primeiros iguais ao valor especificado e todos os componentes restantes igual a um. O valor predefinido é um. O número total de funcionalidade mapas é o produto dos componentes da cadeia de identificação. Factoring este número total de entre os componentes determina a forma como os valores de mapa da funcionalidade são agrupados em nós de destino. 
-   **Espessura**: (opcional) define as espessuras iniciais para o pacote. O valor tem de ser uma cadeia de identificação de flutuantes ponto valores com um comprimento que é o número de vezes kernels o número de espessura por kernel, tal como foi definido neste artigo. A espessura predefinido é gerada aleatoriamente.  

Existem dois conjuntos de propriedades que controlam o preenchimento, as propriedades a ser mutuamente exclusivos:

-   **Preenchimento**: determina (opcional) se deve ser preenchida a entrada de dados utilizando um **esquema de preenchimento predefinido**. O valor pode ser um único valor booleano ou pode ser uma cadeia de identificação de valores booleanos com um comprimento que corresponde à aridade do pacote. Um valor booleano única é expandido para ser uma cadeia de identificação do comprimento correto com todos os componentes iguais ao valor especificado. Se o valor para uma dimensão for VERDADEIRO, a origem logicamente é preenchida nessa dimensão com células de valor de zero para suportar aplicações kernel adicionais, assim que os nós centrais do primeiro e último miolo nessa dimensão são os nós primeiros e últimos nessa dimensão na camada de origem. Assim, o número de nós "fictícios" em cada dimensão é determinado automaticamente, para se ajustar exatamente _(InputShape [d] - 1) / transpor [d] + 1_ kernels para a camada de origem preenchidos. Se o valor para uma dimensão for FALSO, miolo é definidos para que o número de nós em cada lado que ficam fora é a mesma (até uma diferença de 1). O valor predefinido deste atributo é uma cadeia de identificação com todos os componentes iguais a FALSO.
-   **UpperPad** e **LowerPad**: (opcional) fornecer maior controlo sobre a quantidade de preenchimento para utilizar. **Importantes:** Estes atributos podem ser definidos apenas se a propriedade de **Preenchimento** acima é ***não*** definido. Os valores devem ser o valor de número inteiro cadeia de identificação com comprimentos que estão à aridade do pacote. Quando estes atributos são especificados, nós "fictícios" são adicionados às extremidades inferiores e superiores de cada dimensão da camada entrada. O número de nós adicionados às extremidades inferiores e superiores em cada dimensão é determinado pela **LowerPad**[i] e **UpperPad**[i], respetivamente. Para se certificar de que kernels correspondem apenas a nós "reais" e não a "fictícios" nós, devem ser cumpridas as seguintes condições:
    -   Cada componente de **LowerPad** deve ser estritamente inferior KernelShape [d] / 2. 
    -   Cada componente do **UpperPad** tem de ser não maior KernelShape [d] / 2. 
    -   O valor predefinido destes atributos é uma cadeia de identificação com todos os componentes iguais a 0. 

A definição de **Preenchimento** = verdadeiro permite quanto preenchimento conforme necessário para manter o "Centro" do kernel dentro "real" de entrada. Isto altera os cálculos matemáticos um pouco para computação do tamanho de saída. Em geral, o tamanho de saída _D_ é calculado como _D = (I - K) / S + 1_, onde é __ o tamanho de entrada, _K_ é o tamanho da kernel, _S_ é a transposição e _/_ é divisão de número inteiro (Arred por defeito até zero). Se definir UpperPad = [1, 1], o tamanho de entrada _posso_ eficazmente é 29 e, consequentemente, _D = (29-5) / 2 + 1 = 13_. No entanto, quando **preenchidos** = verdadeiro, essencialmente _posso_ obtém aumenta por _K - 1_; Por conseguinte _D = ((28 + 4) - 5) / 2 + 1 = 27 / 2 + 1 = 13 + 1 = 14_. Ao especificar valores para **UpperPad** e **LowerPad** obter muito maior controlo sobre o preenchimento que se acabou de configurar **preenchidos** = true.

Para obter mais informações sobre as suas aplicações e redes convolutional, consulte estes artigos:  

-   [http://deeplearning.NET/tutorial/lenet.HTML](http://deeplearning.net/tutorial/lenet.html )
-   [http://Research.microsoft.com/pubs/68920/icdar03.pdf](http://research.microsoft.com/pubs/68920/icdar03.pdf) 
-   [http://people.csail.MIT.edu/jvb/Papers/cnn_tutorial.pdf](http://people.csail.mit.edu/jvb/papers/cnn_tutorial.pdf)  

## <a name="pooling-bundles"></a>Quadros de agrupamento
Um **agrupamento de pacote** aplica-se geometria semelhante a conectividade convolutional, mas utiliza funções predefinidas para os valores de nó de origem para obter o valor de nó de destino. Por conseguinte, conjuntos de agrupamento têm sem estado trainable (espessura ou desvios). Quadros de agrupamento suportam todos os atributos convolutional exceto **partilha**, **MapCount**e **espessuras**.  

Normalmente, kernels resumidos por unidades agrupamento adjacentes não se sobrepõem. Se for igual a KernelShape [d] em cada dimensão passo [d], a camada obtida é a tradicional camada agrupamento local, que é geralmente utilizada em redes nervoso convolutional. Cada nó de destino fórmula calcula o valor máximo ou a média das actividades dos seu kernel na camada de origem.  

O exemplo seguinte ilustra um pacote agrupamento: 

    hidden P1 [5, 12, 12]
      from C1 max pool {
        InputShape  = [ 5, 24, 24];
        KernelShape = [ 1,  2,  2];
        Stride      = [ 1,  2,  2];
      }  

-   À aridade do pacote é 3 (o comprimento da cadeia de identificação **InputShape**, **KernelShape**e **transpor**). 
-   O número de nós na camada de origem for _5 *24* 24 = 2880_. 
-   Esta é uma camada de agrupamento local tradicional porque **KernelShape** e **transpor** são iguais. 
-   O número de nós na camada de destino é _5 *12* 12 = 1440_.  
    
Para obter mais informações acerca de agrupamento de camadas, consulte estes artigos:  

-   [http://www.cs.Toronto.edu/~hinton/absps/imagenet.pdf](http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf) (Secção 3.4)
-   [http://CS.Nyu.edu/~koray/publis/lecun-iscas-10.pdf](http://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf) 
-   [http://CS.Nyu.edu/~koray/publis/jarrett-iccv-09.pdf](http://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf)
    
## <a name="response-normalization-bundles"></a>Conjuntos de normalização de resposta
**Resposta normalização** é um esquema de normalização local que foi introduzido pela primeira vez por Geoffrey Hinton, ai. e, em papel [ImageNet Classiﬁcation com abrangente redes nervoso Convolutional](http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf). Normalização resposta é utilizada para generalização no nervoso redes de ajuda. Quando é accionar um neurónio num nível de ativação muito alta, uma camada de normalização resposta local suprime o nível de ativação dos neurónios adjacentes. Isto é feito utilizando três parâmetros (***α***, ***β***e ***k***) e uma estrutura convolutional (ou Vizinhança na forma). Cada neurónio na camada de destino ***y*** corresponde a um neurónio ***x*** na camada de origem. O nível de ativação de ***y*** é dado pela fórmula seguinte, onde ***f*** for o nível de ativação de um neurónio e ***Nx*** for o kernel (ou o conjunto que contém os neurónios Vizinhança de ***x***na), tal como foi definido pela estrutura de convolutional seguinte:  

![][1]  

Conjuntos de normalização resposta suportam todos os atributos convolutional exceto **partilha**, **MapCount**e **espessuras**.  
 
-   Se o kernel contiver neurónios no mapa do mesmo como ***x***, o esquema de normalização é referido **mesmo normalização de mapa**. Para definir o mesmo normalização de mapa, coordenada primeira no **InputShape** tem de ter o valor 1.
-   Se o kernel contém neurónios na mesma posição espacial como ***x***, mas os neurónios são nos outros mapas, o esquema de normalização denomina-se **ao longo de normalização mapas**. Este tipo de normalização resposta implementa um formulário de inibição lateral inspirada por tipo que se encontram no neurónios reais, criar concorrência para níveis de ativação grande se saídas neurónio calculadas em mapas diferentes. Para definir o ao longo de normalização mapas, a coordenada primeira tem de ser um número inteiro maior do que um e não maior que o número de mapas e o resto das coordenadas tem de ter o valor 1.  

Uma vez que conjuntos de normalização resposta aplicam uma função predefinida para os valores de nó de origem para determinar o valor de nó de destino, possuem sem estado trainable (espessura ou desvios).   

**Alerta**: os nós na camada de destino correspondem aos neurónios que são os nós do miolo centrais. Por exemplo, se KernelShape [d] for ímpar, em seguida, _KernelShape [d] / 2_ corresponde a nó central kernel. Se _KernelShape [d]_ for par, o nó central está na _KernelShape [d] / 2-1_. Por isso, se **preenchidos**[d] for FALSO, o primeiro e último _KernelShape [d] / 2_ nós não possui nós correspondentes na camada de destino. Evitar esta situação, pode definir **preenchidos** como [true, VERDADEIRO,..., VERDADEIRO].  

Além dos quatro atributos descritos anteriormente, conjuntos de normalização resposta também suportam os seguintes atributos:  

-   **Alfa**: Especifica (obrigatória) um valor de vírgula flutuante que corresponde ao ***α*** na fórmula anterior. 
-   **Beta**: Especifica (obrigatória) um valor de vírgula flutuante que corresponde ao ***β*** na fórmula anterior. 
-   **Deslocamento**: Especifica (opcional) um valor de vírgula flutuante que corresponde ao ***k*** na fórmula anterior. Esta assume a predefinição 1.  

O exemplo seguinte define um pacote de normalização resposta utilizando os seguintes atributos:  

    hidden RN1 [5, 10, 10]
      from P1 response norm {
        InputShape  = [ 5, 12, 12];
        KernelShape = [ 1,  3,  3];
        Alpha = 0.001;
        Beta = 0.75;
      }  

-   A camada de origem inclui cinco mapas, cada uma com aof dimensão de 12 x 12, Somatório em nós 1440. 
-   O valor de **KernelShape** indica que se trata de uma camada de normalização mesmo mapa, onde a Vizinhança é um retângulo de 3x3. 
-   O valor predefinido de **Preenchimento** for FALSO, pelo que a camada de destino tem apenas 10 nós em cada dimensão. Para incluir um nó na camada de destino que corresponde ao cada nó na camada de origem, adicione preenchimento = [verdadeiro, VERDADEIRO, VERDADEIRO]; e altere o tamanho da M1 para [5, 12, 12].  

## <a name="share-declaration"></a>Declaração de partilhar 
Líquido # opcionalmente suporta a definição de múltiplos conjuntos com espessuras partilhadas. Podem ser partilhadas espessuras de qualquer dois conjuntos de correspondentes se suas estruturas são os mesmos. A seguinte sintaxe define conjuntos com espessuras partilhadas:  

    share-declaration:
        share    {    layer-list    }
        share    {    bundle-list    }
       share    {    bias-list    }
    
    layer-list:
        layer-name    ,    layer-name
        layer-list    ,    layer-name
    
    bundle-list:
       bundle-spec    ,    bundle-spec
        bundle-list    ,    bundle-spec
    
    bundle-spec:
       layer-name    =>     layer-name
    
    bias-list:
        bias-spec    ,    bias-spec
        bias-list    ,    bias-spec
    
    bias-spec:
        1    =>    layer-name
    
    layer-name:
        identifier  

Por exemplo, a declaração de partilhar seguinte especifica os nomes de camadas, indicando que devem ser partilhados espessuras e desvios:  

    Const {
      InputSize = 37;
      HiddenSize = 50;
    }
    input {
      Data1 [InputSize];
      Data2 [InputSize];
    }
    hidden {
      H1 [HiddenSize] from Data1 all;
      H2 [HiddenSize] from Data2 all;
    }
    output Result [2] {
      from H1 all;
      from H2 all;
    }
    share { H1, H2 } // share both weights and biases  

-   As funcionalidades de entrada estão divididas em duas camadas de entrada com dimensões iguais. 
-   As camadas ocultas, em seguida, calcular funcionalidades de nível superiores nas duas camadas de entrada. 
-   A declaração de partilhar Especifica que _H1_ e _H2_ devem ser calculadas sempre da mesma forma a partir da suas respetivas entradas do tipo.  
 
Em alternativa, este pode ser especificado com duas declarações de partilhar separadas da seguinte forma:  

    share { Data1 => H1, Data2 => H2 } // share weights  

<!-- -->

    share { 1 => H1, 1 => H2 } // share biases  

Pode utilizar o formulário breve apenas quando as camadas contém um única pacote. Em geral, partilha é possível apenas quando a estrutura relevante é idêntica, o que significa que tem o mesmo tamanho, mesmo geometria convolutional e assim sucessivamente.  

## <a name="examples-of-net-usage"></a>Exemplos de utilização líquido #
Esta secção fornece alguns exemplos de como pode utilizar líquido # para adicionar camadas ocultas, definir a forma como que camadas ocultas interagem com outros camadas e redes convolutional.   

### <a name="define-a-simple-custom-neural-network-hello-world-example"></a>Definir uma rede nervoso personalizada simples: "Olá" exemplo
Este exemplo simple demonstra como criar um modelo de rede nervoso que tem uma única camada oculta.  

    input Data auto;
    hidden H [200] from Data all;
    output Out [10] sigmoid from H all;  

O exemplo ilustra algumas comandos básicos da seguinte forma:  

-   A primeira linha define a camada de entrada (denominada _dados_). Quando utiliza a palavra-chave **automática** , a rede nervoso inclui automaticamente todas as colunas de funcionalidade nos exemplos de entrada. 
-   A segunda linha cria a camada oculta. O nome _H_ está atribuído à camada oculta, que possui 200 nós. Esta camada totalmente está ligada à camada de entrada.
-   A terceira linha define a saída camada (denominada _the_), que contém 10 nós de saída. Se a rede nervoso é utilizada para a classificação, existe um nó de saída por escolares. A palavra-chave **sigmoid** indica que a função de saída é aplicada à camada de saída.   

### <a name="define-multiple-hidden-layers-computer-vision-example"></a>Definir várias camadas ocultas: exemplo de visão do computador
O exemplo seguinte demonstra como definir uma rede nervoso ligeiramente mais complexa, com várias camadas ocultas personalizadas.  

    // Define the input layers 
    input Pixels [10, 20];
    input MetaData [7];
    
    // Define the first two hidden layers, using data only from the Pixels input
    hidden ByRow [10, 12] from Pixels where (s,d) => s[0] == d[0];
    hidden ByCol [5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;
    
    // Define the third hidden layer, which uses as source the hidden layers ByRow and ByCol
    hidden Gather [100] 
    {
      from ByRow all;
      from ByCol all;
    }
    
    // Define the output layer and its sources
    output Result [10]  
    {
      from Gather all;
      from MetaData all;
    }  

Este exemplo ilustra várias funcionalidades do idioma especificação redes nervoso:  

-   A estrutura tem duas camadas de entrada, _Pixels_ e _metadados_.
-   A camada _Pixels_ é uma camada de origem para dois conjuntos de ligação, com camadas de destino, _ByRow_ e _ByCol_.
-   As camadas _recolher_ e o _resultado_ são camadas de destino no vários conjuntos de ligação.
-   A camada de saída, o _resultado_, é uma camada de destino em dois conjuntos de ligação; uma com o segundo nível oculta (reunir) como uma camada de destino e o outro com a camada de entrada (metadados) como uma camada de destino.
-   As camadas ocultas, _ByRow_ e _ByCol_, especifique a conectividade filtrada através de expressões predicados. Com mais precisão, a nó _ByRow_ na [x, y] está ligada aos nós em _pixéis_ que têm a primeira coordenada de índice remissivo igual a coordenadas primeira o nó, x. Da mesma forma, a nó _ByCol na [x, y] está ligada aos nós em _pixéis_ que têm o segundo índice coordenar dentro de uma das coordenadas segunda o nó, y.  

### <a name="define-a-convolutional-network-for-multiclass-classification-digit-recognition-example"></a>Definir uma rede convolutional para classificação multiclass: exemplo de reconhecimento de dígito
A definição da rede seguinte foi concebida para reconhecer números e ilustra algumas técnicas avançadas para personalizar uma rede nervoso.  

    input Image [29, 29];
    hidden Conv1 [5, 13, 13] from Image convolve 
    {
       InputShape  = [29, 29];
       KernelShape = [ 5,  5];
       Stride      = [ 2,  2];
       MapCount    = 5;
    }
    hidden Conv2 [50, 5, 5]
    from Conv1 convolve 
    {
       InputShape  = [ 5, 13, 13];
       KernelShape = [ 1,  5,  5];
       Stride      = [ 1,  2,  2];
       Sharing     = [false, true, true];
       MapCount    = 10;
    }
    hidden Hid3 [100] from Conv2 all;
    output Digit [10] from Hid3 all;  


-   A estrutura tem uma camada de entrada única _imagem_.
-   A palavra-chave **convolve** indica que as camadas com o nome _Conv1_ e _Conv2_ são convolutional camadas. Cada uma das seguintes declarações de camada é seguida de uma lista dos atributos espira.
-   O líquido ocultou um terceiro camada, _Hid3_, que totalmente está ligado à segunda oculta camada _Conv2_.
-   A camada de saída, _dígitos_, está ligada apenas para a terceira camada oculta, _Hid3_. Palavra-chave **todos os** indicam que a camada de saída totalmente está ligada à _Hid3_.
-   À aridade da espira é três (o comprimento da cadeia de identificação **InputShape**, **KernelShape**, **transpor**e **partilha**). 
-   O número de espessura por kernel é _1 + **KernelShape**\[0] * * *KernelShape**\[1]* **KernelShape**\[2] = 1 + 1 *5* 5 = 26. Ou 26 * 50 = 1300_.
-   Pode calcular os nós de cada camada oculto da seguinte forma:
    -   **NodeCount**\[0] = (5 - 1) / 1 + 1 = 5.
    -   **NodeCount**\[1] = (13-5) / 2 + 1 = 5. 
    -   **NodeCount**\[2] = (13-5) / 2 + 1 = 5. 
-   O número total de nós pode ser calculado utilizando a dimensão declarado da camada, [50, 5, 5], da seguinte forma: _ **MapCount** * * *NodeCount**\[0]* **NodeCount**\[1] * * *NodeCount**\[2] = 10* 5 *5* 5_
-   Uma vez que **partilha**[d] é falso apenas para _d = = 0_, o número de kernels é _ **MapCount** * * *NodeCount**\[0] = 10* 5 = 50_. 


## <a name="acknowledgements"></a>Confirmações

O idioma líquido # para personalizar a arquitetura das redes nervoso foi desenvolvido na Microsoft pelo Shon Katzenberger (Architect, formação de computador) e Alexey Kamenev (engenharia de Software, Microsoft Research). Este é utilizado para projetos e aplicações que se situa entre deteção de imagem para a análise de texto de aprendizagem automática. Para obter mais informações, consulte o artigo [Redes nervoso no Azure ML - introdução ao líquido #](http://blogs.technet.com/b/machinelearning/archive/2015/02/16/neural-nets-in-azure-ml-introduction-to-net.aspx)


[1]:./media/machine-learning-azure-ml-netsharp-reference-guide/formula_large.gif
 
