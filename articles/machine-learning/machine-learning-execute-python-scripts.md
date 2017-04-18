<properties 
    pageTitle="Executar scripts de formação de máquina Python | Microsoft Azure" 
    description="Os destaques estruturar princípios subjacentes suporte para scripts Python no Azure máquina aprendizagem e cenários de utilização básica, funcionalidades e limitações." 
    keywords="máquina Python aprendizagem, pandas, python pandas, python scripts, executar scripts de python"
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
    ms.author="bradsev" />


# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio"></a>Executar scripts de formação de máquina de Python no Azure máquina de formação Studio

Este tópico descreve os princípios de estrutura subjacentes o suporte técnico da atual scripts Python no Azure máquina aprendizagem. As capacidades principais também são descritas, incluindo o suporte para importar código existente, exportar visualizações e, por fim, algumas das limitações e trabalho em curso são descritas.

[Python](https://www.python.org/) é uma ferramenta indispensável no tórax ferramenta de muitos cientistas de dados. Tem-lo:

-  sintaxe elegante e concisa, 
-  suporte de em diferentes plataformas, 
-  um vasto conjunto de bibliotecas eficientes, e 
-  ferramentas de desenvolvimento maduro. 

Python está a ser utilizado em todas as fases do fluxo de trabalho normalmente utilizadas na modelação de aprendizagem automática, a partir de dados ingerir esta última e processamento para construção de funcionalidade e formação de modelo e, em seguida, validação e implementação dos modelos. 

Azure máquina de formação Studio suporta incorporação de Python de scripts para várias partes de uma máquina experiência de formação e também totalmente publicá-los como serviços web dimensionáveis, operacionalizada no Microsoft Azure.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


## <a name="design-principles-of-python-scripts-in-machine-learning"></a>Princípios de estrutura de scripts Python em aprender máquina
A interface primária Python no Azure máquina aprendizagem Studio é através o [Script de Python executar] [ execute-python-script] módulo apresentado na figura 1.

![image1](./media/machine-learning-execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![image2](./media/machine-learning-execute-python-scripts/embedded-machine-learning-python-script.png)

Figura 1. Módulo **Executar scripts de Python** .

O [Script de Python executar] [ execute-python-script] módulo aceita até três introduções e produz até duas saídas (abordadas abaixo), tal como a sua analógicos R, o [Script de R executar] [ execute-r-script] módulo. O código de Python para ser executada é introduzido na caixa parâmetro especialmente designada como ponto de entrada função denominado `azureml_main`. Aqui estão os princípios chave estrutura utilizados para implementar este módulo:

1.  *Tem de ser idiomáticas para utilizadores Python.* A maioria dos utilizadores Python factor respectivo código como funções dentro de módulos, pelo que colocar em muitas demonstrações executáveis num módulo de nível superior é relativamente raro. Como resultado, a caixa de script também leva-o até uma função Python especialmente designada por oposição a apenas uma sequência de declarações. Os objetos expostos na função estão tipos de biblioteca padrão Python como [Pandas](http://pandas.pydata.org/) molduras de dados e [NumPy](http://www.numpy.org/) matrizes.
2.  *Tem de ter alta fidelidade entre local e na nuvem execuções.* O back-end utilizado para executar o código de Python é baseado [Anaconda](https://store.continuum.io/cshop/anaconda/) 2.1, uma distribuição amplamente utilizada para o em diferentes plataformas científica Python. Vem com perto 200 os pacotes de Python mais comuns. Por conseguinte, cientistas de dados podem depurar e qualificar respectivo código no seu ambiente de trabalho do Azure máquina compatível com o aprendizagem Anaconda local. Em seguida, utilize os ambientes de programação existentes como [IPython](http://ipython.org/) bloco de notas ou [Ferramentas de Python para Visual Studio](http://aka.ms/ptvs) executá-la como parte de uma experiência de aprendizagem do Azure máquina com confiança alta. Novas, o `azureml_main` ponto de entrada é uma função Python baunilha e pode ser criado sem código específico do Azure máquina formação ou o SDK instalado.
3.  *Tem de ser totalmente composta com outros módulos Azure máquina aprendizagem.* O [Script de Python executar] [ execute-python-script] módulo aceita, como entradas e saídas, conjuntos de dados de aprendizagem do Azure máquina padrão. A arquitetura subjacente transparente e eficiente preenche o Azure máquina aprendizagem e Python runtimes (suportar funcionalidades como valores em falta). Python, por conseguinte, pode ser utilizado em conjunto com o Azure máquina aprendizagem fluxos de trabalho existentes, incluindo aquelas que ligar para R e SQLite. Um pode, por conseguinte, encarar fluxos de trabalho que:
  * utilizar Python e Pandas para dados pré-processamento e limpeza, 
  * Feed de dados para uma transformação de SQL, participar vários conjuntos de dados para funcionalidades do formulário, 
  * Preparar modelos de utilização do conjunto amplo dos algoritmos no Azure máquina formação, e 
  * avaliar e pós processar os resultados usando R.


## <a name="basic-usage-scenarios-in-machine-learning-for-python-scripts"></a>Cenários de utilização básica em aprender máquina de Python scripts
Nesta secção, podemos respostas a algumas das utilizações básicas do [Executar scripts de Python] [ execute-python-script] módulo.
Como mencionado anteriormente, qualquer entradas no módulo Python são expostas como pacotes de dados Pandas. Podem encontrar mais informações sobre Python Pandas e como pode ser utilizado para manipular os dados eficaz no *Python para análise de dados* (o ' Reilly, 2012) por McKinney Ocidental. A função tem de devolver um fotograma de dados do Pandas empacotado dentro de uma [sequência](https://docs.python.org/2/c-api/sequence.html) de Python como uma cadeia de identificação, lista ou NumPy matriz. O primeiro elemento desta sequência, em seguida, é devolvido no primeiro porto saída do módulo. Este esquema é apresentado na figura 2.

![image3](./media/machine-learning-execute-python-scripts/map-of-python-script-inputs-outputs.png)

Figura 2. O mapeamento de portas para parâmetros de entrada e devolver o valor para porta de saída.

Mais detalhadas semântica de como as portas entradas mapeadas para parâmetros do `azureml_main` função são apresentadas na tabela 1:

![image1T](./media/machine-learning-execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

Tabela 1. Mapeamento de portas de entrada para parâmetros da função.

O mapeamento entre portas de entrada e parâmetros da função é posicional. A primeira porta de entrada ligada é mapeada para o primeiro parâmetro da função e o segunda de entrada (se ligado) é mapeada para o segundo parâmetro da função.

## <a name="translation-of-input-and-output-types"></a>Conversão de tipos de entrada e saídos
Como é explicado anteriormente, conjuntos de dados de entrada no Azure máquina aprendizagem são convertidas em dados molduras no Pandas e saída dados molduras são convertidas em novamente Azure máquina aprendizagem conjuntos de dados. As conversões seguintes são executadas:

1.  Colunas de cadeia e numérico são convertidas em como-é e valores em falta um conjunto de dados são convertidas em valores 'Disp' Pandas. A conversão mesmo acontece em volta da forma (disp valores no Pandas são convertidos em valores em falta no Azure máquina formação).
2.  Vectores de índice remissivo no Pandas não são suportadas no Azure máquina aprendizagem. Todos os pacotes de dados de entrada na função Python tem sempre um índice numérico de 64 bits entre 0 e o número de linhas, menos 1. 
3.  Conjuntos de dados de aprendizagem máquina Azure não podem ter os nomes das colunas duplicados e nomes de colunas que não são cadeias. Se uma moldura de dados de saída contiver colunas não numérico, a arquitetura chamadas `str` nos nomes das colunas. Da mesma forma, quaisquer nomes de coluna duplicado são automaticamente deturpados para garantir que os nomes são exclusivos. O sufixo (2) é adicionado para o primeiro duplicado, (3) para a segundo duplicado, etc.

## <a name="operationalizing-python-scripts"></a>Operationalizing Python scripts
Qualquer [Executar scripts de Python] [ execute-python-script] módulos utilizados numa experiência de pontuação são chamados quando publicado como um serviço web. Por exemplo, figura 3 mostra uma experiência pontuação, que contém o código para avaliar uma expressão Python única. 

![image4](./media/machine-learning-execute-python-scripts/figure3a.png)

![image5](./media/machine-learning-execute-python-scripts/python-script-with-python-pandas.png)

Figura 3. Serviço Web para avaliação de uma expressão de Python.

Um serviço web criado a partir deste experiência utiliza como uma expressão de Python (como uma cadeia de), envia-lo para o intérprete Python e devolve uma tabela que contém a expressão e o resultado avaliado de entrada.

## <a name="importing-existing-python-script-modules"></a>Importar módulos de script Python existentes
É um caso de utilização comum para muitos cientistas de dados de incorporar scripts Python existentes experiências Azure máquina aprendizagem. Em vez de concatenação e colando todo o código de uma caixa de único script, o [Script de Python executar] [ execute-python-script] módulo aceita uma terceira porta de entrada ao qual pode ser ligado um ficheiro zip que contém os módulos Python. O ficheiro, em seguida, é descompactado pelo framework execução o tempo de execução e os conteúdos são adicionados ao caminho da biblioteca de interpretação a Python. O `azureml_main` ponto de entrada função pode, em seguida, importar estes módulos diretamente.

Por exemplo, considere o ficheiro Hello.py que contém uma função de "Olá, mundo" simple.

![image6](./media/machine-learning-execute-python-scripts/figure4.png)

Figura 4. Função definida pelo utilizador.

Em seguida, podemos criar um ficheiro Hello.zip que contém Hello.py:

![image7](./media/machine-learning-execute-python-scripts/figure5.png)

Figura 5. Ficheiro zip que contém o código de Python definidos pelo utilizador.

Em seguida, carregar isto como um conjunto de dados para Azure máquina aprendizagem Studio. Criar e executar uma experiência simple que utiliza o código de Python no ficheiro Hello.zip por anexá-la para a terceira porta de entrada de Script de Python executar, conforme apresentado nesta figura.

![image8](./media/machine-learning-execute-python-scripts/figure6a.png)

![image9](./media/machine-learning-execute-python-scripts/figure6b.png)

Figura 6. Experiência de exemplo com o código de Python definidos pelo utilizador carregados como um ficheiro zip.

Módulo de saída mostra que o ficheiro zip foi e a função `print_hello` facto foi executado.
 
![image10](./media/machine-learning-execute-python-scripts/figure7.png)
 
Figura 7. Função definida pelo utilizador utilizado dentro o [Script de Python executar] [ execute-python-script] módulo.

## <a name="working-with-visualizations"></a>Trabalhar com visualizações
Representações criadas utilizando MatplotLib que pode ser visualizada no browser podem ser devolvidas pelo [Executar scripts de Python][execute-python-script]. Mas as representações não automaticamente redireccionadas para o imagens como estão ao utilizar R. Por isso, o utilizador tem explicitamente de guardar quaisquer representações para ficheiros PNG se estiverem a ser devolvido regressar ao Azure máquina aprendizagem. 

Para gerar imagens a partir do MatplotLib, tem de competir o seguinte procedimento:

* mudar o back-end "AGG" da composição de predefinido baseado em Qt 
* criar um novo objeto figura 
* obter o eixo e gerar todas as representações na mesma 
* Guardar na figura para um ficheiro PNG 

Este processo é ilustrado na figura 8 seguinte que cria uma matriz de desenho de gráficos de dispersão utilizando a função scatter_matrix no Pandas.
 
![image1v](./media/machine-learning-execute-python-scripts/figure-v1-8.png)

Figura 8. Ilustrações MatplotLib guardar imagens.



Figura 9 mostra uma experiência que utiliza o script apresentado anteriormente para devolver desenha através da segunda porta de saída.

![image2v](./media/machine-learning-execute-python-scripts/figure-v2-9a.png) 
     
![image2v](./media/machine-learning-execute-python-scripts/figure-v2-9b.png) 

Figura 9. Visualizar representações geradas a partir de código Python.

É possível devolver valores múltiplos ao guardá-los para imagens diferentes, o tempo de execução de aprendizagem do Azure máquina levantar o auscultador de todas as imagens e concatena-los para visualização.


## <a name="advanced-examples"></a>Exemplos de avançadas
O ambiente de Anaconda instalado no Azure máquina de formação contém pacotes comuns, tais como NumPy, SciPy e saiba Scikits e estes podem ser utilizados eficazmente para várias tarefas de processamento de dados no pipeline de aprendizagem máquina típica. Por exemplo, o experiência seguinte e o script ilustra a utilização dos formandos conjunto na saiba Scikits para calcular pontuações de importância da funcionalidade para um conjunto de dados. As pontuações, em seguida, podem ser utilizadas para executar a seleção de funcionalidades controlados antes de alimentação na outro modelo de aprendizagem automática.

A função Python para calcular as pontuações de importância e ordem as funcionalidades com base no mesmo é apresentada abaixo:

![image11](./media/machine-learning-execute-python-scripts/figure8.png)

Figura 10. Função para funcionalidades classificação por pontuações.
 A experiência seguinte, em seguida, fórmula calcula e devolve as pontuações de importância de funcionalidades no conjunto de dados "Pima indiano Diabetes" no Azure máquina formação:

![image12](./media/machine-learning-execute-python-scripts/figure9a.png)
![image13](./media/machine-learning-execute-python-scripts/figure9b.png)    
    
Figura 11. Experimente utilizar funcionalidades de classificação do conjunto de dados Pima indiano Diabetes.

## <a name="limitations"></a>Limitações 
O [Script de Python executar] [ execute-python-script] atualmente tem as seguintes limitações:

1.  *Execução em sandbox.* O tempo de execução Python está atualmente em sandbox e, como resultado, não permitir o acesso à rede ou para o sistema de ficheiros local de uma forma persistente. Todos os ficheiros que guardou localmente são isolados e eliminados quando o módulo é concluída. O código de Python não consegue aceder a maior parte dos directórios no computador que é executada, a exceção a ser o diretório atual e os seus subdiretórios.
2.  *Falta de desenvolvimento sofisticado e depuração de suporte.* O módulo Python atualmente não suporta funcionalidades IDE como intellisense e depuração. Além disso, se o módulo falhar o tempo de execução, o rastreio da pilha Python completo está disponível, mas tem de ser visualizado no registo de saída para o módulo. Atualmente, recomendamos que desenvolver e depurar os respetivos scripts Python num ambiente como IPython e, em seguida, importá o código do módulo.
3.  *Resultado de moldura dados único.* O ponto de entrada Python só é permitido para devolver uma moldura de dados simples como saída. Não é atualmente possível voltar arbitrários Python objetos tais como modelos com formação diretamente à runtime do Azure máquina aprendizagem. Como [Executar scripts de R][execute-r-script], que tem a mesma limitação, é contudo possível em muitos casos, a pickle objetos para uma matriz de bytes e, em seguida, ENTER esse interior de um pacote de dados.
4.  *Incapacidade personalizar Python instalação*. Atualmente, a única forma de adicionar personalizados Python módulos é através do mecanismo de ficheiro zip descrito anteriormente. Apesar de esta é viável para pequenas módulos, é complexo para módulos de grandes dimensões (especialmente aqueles com DLLs nativas) ou um grande número de módulos. 


##<a name="conclusions"></a>Conclusões
O [Script de Python executar] [ execute-python-script] módulo permite uma cientista de dados para incorporar código Python existente no fluxos de trabalho do alojado nuvem máquina formação na máquina de Azure formação e a forma totalmente integrada operationalize-los como parte de um serviço web. O módulo de script Python naturalmente interage com outros módulos no Azure máquina aprendizagem e pode ser utilizado para um intervalo de tarefas a partir de exploração de dados para processamento de pré-lançamento, para extração de funcionalidade, para avaliação e pós-processamento dos resultados. Runtime back-end utilizada para execução é baseado Anaconda, uma distribuição de Python bem testada e amplamente utilizada. Isto torna-simples para si para bordo recursos de código existente para a nuvem.

Vamos esperar fornecer funcionalidades adicionais para o [Script de Python executar] [ execute-python-script] módulo, tal como a capacidade para dar formação e operationalize modelos no Python e para adicionar um melhor suporte para o desenvolvimento e depurar código no Azure máquina aprendizagem Studio.

## <a name="next-steps"></a>Próximos passos

Para mais informações, consulte o [Centro de programadores do Python](/develop/python/).

<!-- Module References -->
[execute-python-script]: https://msdn.microsoft.com/library/azure/cdb56f95-7f4c-404d-bde7-5bb972e6f232/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
