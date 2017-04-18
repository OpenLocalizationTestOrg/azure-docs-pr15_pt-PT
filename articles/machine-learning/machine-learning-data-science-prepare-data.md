<properties
    pageTitle="Tarefas para preparar os dados para as funcionalidades de aprendizagem de máquina | Microsoft Azure"
    description="Preparação do processo e limpar os dados para prepará-lo para a aprendizagem máquina."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016" 
    ms.author="bradsev" />


# <a name="tasks-to-prepare-data-for-enhanced-machine-learning"></a>Tarefas para preparar os dados para as funcionalidades de aprendizagem do computador

Pré-processamento e limpeza de dados são tarefas importantes que devem ser normalmente realizadas antes de conjunto de dados pode ser utilizado eficazmente para formação de máquina. Dados não processados são frequentemente com ruído e não fiáveis e poderão estar em falta valores. Utilizar esses dados para modelação pode produzir resultados enganadoras. Estas tarefas fazem parte da equipa dados ciência processo (TDSP) e, normalmente, siga uma informações detalhadas inicial de um conjunto de dados utilizada para descobrir e planear o processamento de pré-lançamento necessário. Para obter informações mais detalhadas sobre o processo de TDSP, consulte os passos descritos no [Processo de ciências de dados de equipa](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

Pré-processamento e limpeza tarefas, como a tarefa de informações detalhadas de dados, podem ser realizados numa grande variedade de ambientes, tal como SQL ou ramo ou Azure máquina aprendizagem Studio e com várias ferramentas e idiomas, tais como R ou Python, consoante a localização onde armazenou os seus dados e como está formatado. Uma vez que TDSP iterativo natureza, estas tarefas podem ter lugar na vários passos no fluxo de trabalho do processo.

Este artigo apresenta vários conceitos de processamento de dados e tarefas que podem ser realizadas antes ou depois de ingesting dados para o Azure máquina aprendizagem.

Para um exemplo de exploração de dados e pré-processamento concluído dentro de formação do Azure máquina studio, consulte o vídeo de [pré-processamento dados no Azure máquina aprendizagem Studio](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/) .


## <a name="why-pre-process-and-clean-data"></a>Por que motivo previamente processar e limpar os dados?

Recolhidas a partir de várias origens de dados reais e processos e podem conter irregularidade ou de dados danificados comprometer a qualidade do conjunto de dados. Os problemas de qualidade de dados típico que surja são:

* **Incompleto**: não possui dados atributos ou que contém valores em falta.
* **Noisy**: contém dados inválidos registos ou aberrantes.
* **Inconsistente**: dados contém registos em conflito ou discrepâncias.

Dados de qualidade são um pré-requisito para modelos de previsão de qualidade de. Para evitar "lixo no lixo saída" e melhorar a qualidade dos dados e, consequentemente, do modelo de desempenho, é obrigatório para realizar um ecrã de estado de funcionamento de dados para spot problemas de dados mais cedo e decidir no processamento de dados correspondente e limpeza passos.

## <a name="what-are-some-typical-data-health-screens-that-are-employed"></a>Quais são algumas ecrãs de estado de funcionamento de dados típico que são utilizadas?

Vamos pode verificar a qualidade geral dos dados ao selecionar:

* O número de **registos**.
* O número de **atributos** (ou **funcionalidades**).
* O atributo **tipos de dados** (nominal, ordinal ou contínua).
* O número de **em falta valores**.
* **Bem-formedness** dos dados.
    * Se os dados estiverem numa TSV ou CSV, verifique que os separadores de coluna e separadores de linha sempre corretamente separam colunas e linhas.
    * Se os dados estiverem em formato HTML ou XML, verifique se os dados são formados bem com base no seus respectivas normas.
    * Análise também poderá ser necessária para extrair informações estruturadas dos dados semiestruturados ou não estruturados.
* **Registos de dados inconsistentes**. Verifique o intervalo de valores são permitidos. Por exemplo, se os dados contiverem estudantes GPA, verificar se o GPA está no intervalo designado, diga 0 ~ 4.

Quando encontrar problemas com os dados, **passos de processamento** são necessárias frequentemente que envolve limpeza valores em falta, normalização de bases de dados, discretization, para remover e/ou substituir carateres incorporados que possam afetar o alinhamento dos dados de processamento de texto, mistos tipos de dados em comum campos e outras pessoas.

**Formação do azure máquina consome correcto os dados de tabela**.  Se os dados já estiverem no formato tabular, processamento de pré-lançamento de dados pode ser executado diretamente com Azure máquina aprendizagem na Studio de aprendizagem automática.  Se os dados não estão no formato tabular, diga é em XML, de análise que devem ser seguida para converter os dados para formato tabular.  

## <a name="what-are-some-of-the-major-tasks-in-data-pre-processing"></a>Quais são algumas das principais tarefas na pré-processamento dos dados?

* **Limpeza de dados**: preencher ou valores em falta, detetar e remover dados de ruído e aberrantes.
* **Transformação de dados**: normalizar os dados para reduzir as dimensões e ruído.
* **Redução de dados**: os registos de dados ou atributos de processamento de dados mais fácil de exemplo.
* **Discretization de dados**: converter atributos contínuos atributos categorias para fácil utilização com determinadas métodos de aprendizagem automática.
* **Limpeza de texto**: remover caracteres incorporados que podem causar o erro de alinhamento de dados, para, por exemplo, separadores incorporados num ficheiro de dados separado por tabulações, incorporado novas linhas que poderão quebrar registos, etc.

As secções abaixo de detalhe alguns destes passos de processamento de dados.

## <a name="how-to-deal-with-missing-values"></a>Como lidar com valores em falta?

Para lidar com valores em falta, é melhor identificar pela primeira vez o motivo para os valores em falta a alça de melhor o problema. Os métodos de processamento de valor em falta típica são:

* **Eliminação**: remover registos com valores em falta
* **Substituição manequim**: substituir valores em falta com um valor fictício: por exemplo, _desconhecido_ para categorias ou 0 para valores numéricos.
* **Vermelha substituição**: se os dados em falta não forem numéricos, substitua os valores em falta a média.
* **Substituição frequente**: se os dados em falta forem categorias, substitua os valores em falta o item mais frequente
* **Substituição de tipos de regressão**: utilizar um método de regressão para substituir valores em falta com valores incompatíveis.  

## <a name="how-to-normalize-data"></a>Como normalizar os dados?

Normalização dados novamente escalas valores numéricos para um intervalo especificado. Os métodos de normalização de bases de dados mais populares incluem:

* **Máximo-mínimo normalização**: linear transformar os dados num intervalo, diga entre 0 e 1, onde o valor mínimo está dimensionado a 0 e max valor para 1.
* **Pontuação Z normalização**: dimensionar os dados com base em média e o desvio-padrão: a diferença entre os dados e a média de divisão pelo desvio-padrão.
* **Dimensionamento decimal**: dimensionar os dados ao mover o ponto decimal do valor do atributo.  

## <a name="how-to-discretize-data"></a>Como discretize dados?

Podem ser discretized dados ao converter valores contínuas atributos nominais ou intervalos. Algumas formas de o fazer são:

* **Largura igual Binning**: dividir o intervalo de todos os valores possíveis de um atributo em grupos de N do mesmo tamanho e atribuir os valores que se encontrem numa posição com o número de reciclagem.
* **Altura igual Binning**: dividir o intervalo de todos os valores possíveis de um atributo em grupos de N, cada uma com o mesmo número de instâncias, em seguida, atribuir os valores que se encontrem numa posição com o número de reciclagem.  

## <a name="how-to-reduce-data"></a>Como reduzir os dados?

Existem vários métodos para reduzir o tamanho de dados para os dados mais fácil de processamento. Dependendo do tamanho dos dados e o domínio, podem ser aplicados seguintes métodos:

* **Recolha de registo**: os registos de dados de exemplo e selecione apenas o subconjunto representativo dos dados.
* **Atributo amostragem**: seleccionar apenas um subconjunto dos atributos mais importantes a partir dos dados.  
* **Agregação**: dividir os dados em grupos e armazenar os números para cada grupo. Por exemplo, os números de receitas diárias de uma cadeia de restaurante ao longo dos últimos anos 20 podem ser agregados a receita mensal para reduzir o tamanho dos dados.  

## <a name="how-to-clean-text-data"></a>Como limpar os dados de texto?

**Campos de texto em dados de tabela** pode incluir carateres que afetam os limites de alinhamento e/ou registo de colunas. Por exemplo, incorporado separadores um erro de alinhamento de coluna do ficheiro separado por tabulações causa e incorporado caracteres de nova linha quebrar linhas registos. Processamento de codificação de texto incorreta enquanto o texto de escrita/leitura orienta a perda de informações, inadvertida introdução de carateres legível, por exemplo, valores nulos e poderá também afeta texto de análise. De análise cuidada e de edição que devem ser seguidos para limpar os campos de texto para inicial maiúscula alinhamento e/ou a extrair estruturada dados a partir de dados de texto não estruturados ou semiestruturados.

**Informações detalhadas de dados** oferece uma vista inicial dos dados. Um número de problemas de dados pode ser descoberto durante este passo e métodos correspondentes podem ser aplicados a resolver esses problemas.  É importante fazer perguntas como o que é a origem do problema e como o problema poderá ter sido introduzido. Isto também ajuda-o a decidir sobre os passos de processamento de dados que precisam de ser encaminhado para resolvê-los. O tipo de informações de um tenciona deriva os dados também pode ser utilizado para atribuir prioridades o esforço de processamento de dados.

## <a name="references"></a>Referências

>*Extração de dados: conceitos e técnicas*, terceira edição, Machado Kaufmann, 2011, Jiawei Han, Micheline Kamber e Jian Pei
