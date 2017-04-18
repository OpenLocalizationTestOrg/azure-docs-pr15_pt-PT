<properties
    pageTitle="Funcionalidade de seleção no processo de ciências de dados de equipa | Microsoft Azure" 
    description="Explica a finalidade de seleção de funcionalidades e fornece exemplos da sua função no processo de melhoramento de dados da formação de máquina."
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


# <a name="feature-selection-in-the-team-data-science-process-tdsp"></a>Seleção de funcionalidades no equipa dados ciência processo (TDSP)

Este artigo explica o propósito de seleção de funcionalidades e fornece exemplos do seu papel no processo de melhoramento de dados da formação de máquina. Estes exemplos são desenhados a partir do Azure máquina aprendizagem Studio. 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


Este tópico explica a finalidade de seleção de funcionalidades e fornece exemplos do seu papel no processo de melhoramento de dados da formação de máquina. Estes exemplos são desenhados a partir do Azure máquina aprendizagem Studio. 

A engenharia e seleção de funcionalidades é uma parte de TDSP destacada a [qual é o processo de ciências de dados de equipa?](data-science-process-overview.md). Funcionalidade engenharia e seleção são partes do passo **desenvolver funcionalidades** do TDSP.

* **engenharia de funcionalidade**: Este processo tentativas para criar funcionalidades relevantes adicionais a partir das funcionalidades não processadas existentes nos dados e para aumentar o aspeto do Office power para o algoritmo de aprendizagem.

* **seleção de funcionalidades**: Este processo seleciona o subconjunto chave original de funcionalidades de dados numa tentativa para reduzir a dimensão do problema de formação.

Normalmente **engenharia funcionalidade** é aplicada primeiro para gerar funcionalidades adicionais e, em seguida, o passo de **seleção de funcionalidades** é efetuado para eliminar o irrelevantes, redundantes ou altamente correlacionadas funcionalidades.


## <a name="filtering-features-from-your-data---feature-selection"></a>Funcionalidades de filtragem dos seus dados - selecção de funcionalidade 

Seleção de funcionalidades é um processo que foi aplicado frequentemente de construção de conjuntos de dados de formação para as tarefas de modelação de aspeto do Office, tal como tarefas de classificação ou regressão. O objetivo é selecionar um subconjunto das funcionalidades do conjunto de dados original que reduzir as suas dimensões utilizando um conjunto de funcionalidades mínimo para representar a quantidade máxima de variância dos dados. Este subconjunto de funcionalidades são, em seguida, as funcionalidades apenas para ser incluída para formar o modelo. Seleção de funcionalidades tem duas finalidades principais.

* Em primeiro lugar, seleção de funcionalidades frequentemente aumenta a precisão de classificação eliminando irrelevante, redundantes ou altamente associados funcionalidades.
* Em segundo lugar,-reduz o número de funcionalidades que torna o processo de formação do modelo mais eficaz. Isto é particularmente importante para formandos que estão dispendiosos de formar como máquinas de vetor de suporte.

Apesar de seleção de funcionalidade atingir reduzir o número de funcionalidades no conjunto de dados utilizados para formar o modelo, não é normalmente referida pelo termo "redução de dimensão". Métodos de selecção de funcionalidade extrair um subconjunto de funcionalidades originais nos dados sem alterá-las.  Métodos de redução de dimensão empregam engenharia funcionalidades que podem transformar as funcionalidades originais e, por conseguinte, modificá-las. Exemplos de métodos de redução de dimensão incluem capital componente análise, análise de correlação canónico e o valor de decomposição.

Entre outras coisas, uma categoria amplamente aplicada dos métodos de seleção de funcionalidade num contexto controlados chama-se "seleção de funcionalidades de filtro com base". Avaliar a correlação entre cada funcionalidade e o atributo de destino, estes métodos aplicam uma medida de estatística para atribuir uma classificação a cada funcionalidade. As funcionalidades, em seguida, estão classificadas por pontuação, que pode ser utilizadas para ajudar a definir o limiar de para manter ou eliminar uma funcionalidade específica. As medidas de estatísticas utilizadas nestes métodos de exemplos de correlação, informações comum e o teste ao quadrado Chi pessoa.

No Azure máquina aprendizagem Studio, existem módulos fornecidos para a seleção de funcionalidades. Como apresentado na figura seguinte, estes módulos incluem a [seleção de funcionalidades do filtro de baseado] [ filter-based-feature-selection] e [Fisher Linear Discriminant análise][fisher-linear-discriminant-analysis].

![Exemplo de seleção de funcionalidade](./media/machine-learning-data-science-select-features/feature-Selection.png)


Considere, por exemplo, a utilização da [seleção de funcionalidades do filtro de baseado] [ filter-based-feature-selection] módulo. Para sua comodidade, podemos continuar a utilizar o exemplo de extração de texto descrito acima. Partem do princípio de que pretendemos criar um modelo de regressão após a criação de um conjunto de 256 funcionalidades através da [Funcionalidade Hashing] [ feature-hashing] módulo, que a variável de resposta é e "Col1" representa um livro rever classificações que se situa entre 1 a 5. Ao definir "Funcionalidade método pontuação" para ser "Pearson correlação", "coluna de destino" para ser "Col1" e "Número de funcionalidades pretendidas" a 50. Em seguida, o módulo de [seleção de funcionalidades do filtro de baseado] [ filter-based-feature-selection] vão produzir um conjunto de dados que contém 50 funcionalidades juntamente com o atributo de destino "Col1". A figura seguinte mostra o fluxo desta experiência e os parâmetros de entrada que acabámos de descrever.

![Exemplo de seleção de funcionalidade](./media/machine-learning-data-science-select-features/feature-Selection1.png)

A figura seguinte mostra os conjuntos de dados resultantes. Cada funcionalidade é classificada com base na correlação Pearson entre si próprio e o atributo de destino "Col1". As funcionalidades com as pontuações de superiores são mantidas.

![Exemplo de seleção de funcionalidade](./media/machine-learning-data-science-select-features/feature-Selection2.png)

As pontuações correspondentes das funcionalidades selecionadas são apresentadas na figura seguinte.

![Exemplo de seleção de funcionalidade](./media/machine-learning-data-science-select-features/feature-Selection3.png)

Aplicando esta [seleção de funcionalidades do filtro de baseado] [ filter-based-feature-selection] módulo, 50 fora do 256 funcionalidades estão selecionadas porque que tenham as funcionalidades mais correlacionadas com a variável de destino "Col1", com base no método de pontuação "Pearson correlação".

## <a name="conclusion"></a>Conclusão
Engenharia funcionalidade e seleção de funcionalidades são duas frequentemente de engenharia e funcionalidades seleccionadas aumentar a eficiência do processo de formação que tentativas deverão ser extraídas as informações da chave contidas nos dados. Estes também melhoram o poder destes modelos para classificar os dados de entrada com exatidão e para prever sólida mais resultados de interesse. Também podem combinar funcionalidade engenharia e seleção para tornar o ensino mais inviabiliza tractable. Para o fazer, melhorar e, em seguida, reduzindo o número de funcionalidades necessárias para calibre ou formar um modelo. Matematicamente falar, as funcionalidades seleccionadas para formar o modelo são um conjunto mínimo de variáveis independentes que explicam os padrões dos dados e, em seguida, prever resultados com êxito.

Tenha em atenção que nem sempre é necessariamente para efetuar a seleção de engenharia ou funcionalidade de funcionalidade. É necessária ou não depende o podemos ter ou recolher dados, o algoritmo que podemos escolha e sobre o objetivo da experiência.

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/
 
