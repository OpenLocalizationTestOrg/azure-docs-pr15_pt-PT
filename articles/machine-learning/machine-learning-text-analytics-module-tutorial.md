<properties
    pageTitle="Criar modelos de análise de texto no Azure máquina aprendizagem Studio | Microsoft Azure"
    description="Como criar modelos de análise de texto no Azure máquina aprendizagem Studio utilizando módulos para texto pré-processamento, g N ou funcionalidade hashing"
    services="machine-learning"
    documentationCenter=""
    authors="rastala"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="roastala" />


#<a name="create-text-analytics-models-in-azure-machine-learning-studio"></a>Criar modelos de análise de texto no Azure máquina aprendizagem Studio

Pode utilizar o Azure máquina formação para criar e operationalize modelos de análise de texto. Estes modelos podem ajudar a resolver, por exemplo, problemas de análise de classificação ou sentimento do documento.

Numa experiência de análise de texto, faria normalmente:

 1. Limpar e pré-processamento conjunto de dados de texto
 2. Extrair vectores funcionalidade numérico de texto previamente transformado
 3. Modelo de classificação ou regressão comboio
 4. Pontuação e validar o modelo
 5. Implementar o modelo de produção

Neste tutorial, aprendeu estes passos como iremos guiá através de um modelo de análise de sentimento com revisões de livro de endereços da Amazon conjunto de dados (consulte este papel de pesquisa "biografias, Bollywood, caixas de braço e misturas: adaptação de domínio para a classificação de sentimento" João Blitzer, marca Dredze e Fernando Pereira; Associação de linguística utilizaria (ACL), 2007.) Este conjunto de dados é composta por pontuações de revisão (1-2 ou 4-5) e um texto de forma livre. O objetivo é prever pontuação rever: baixa (1 - 2) ou do maior (4-5).

Pode encontrar experiências abordadas neste tutorial em Cortana Galeria de informações da empresa:

[Prever livro revisões] (https://gallery.cortanaintelligence.com/Experiment/Predict-Book-Reviews-1)

[Prever livro revisões - experiência aspeto do Office] (https://gallery.cortanaintelligence.com/Experiment/Predict-Book-Reviews-Predictive-Experiment-1)

## <a name="step-1-clean-and-preprocess-text-dataset"></a>Passo 1: Limpar e pré-processamento conjunto de dados de texto

Vamos começar a experiência dividindo as pontuações de revisão em grupos de categorias mínimo e máximo precisar o problema como classificação de classe de duas. Utilizamos [Editar metadados] (https://msdn.microsoft.com/library/azure/dn905986.aspx) e módulos [grupo categorias Values] (https://msdn.microsoft.com/library/azure/dn906014.aspx).

![Criar etiqueta](./media/machine-learning-text-analytics-module-tutorial/create-label.png)

Em seguida, podemos limpe o texto utilizando o módulo [pré-processamento texto] (https://msdn.microsoft.com/library/azure/mt762915.aspx). A limpeza reduz o ruído no conjunto de dados, ajudá-lo a localizar as funcionalidades mais importantes e melhora a exatidão do modelo de final. Vamos remover stopwords - palavras comuns, tais como "a" ou "a" - e números, carateres especiais, carateres duplicados, endereços de e-mail e URLs. Recomendamos também converter o texto para minúsculas, lemmatize as palavras e detetar limites de uma frase, em seguida, são indicados por "|||" símbolo no texto previamente transformado.

![Pré-processamento texto](./media/machine-learning-text-analytics-module-tutorial/preprocess-text.png)

O que acontece se pretende utilizar uma lista personalizada de stopwords? Pode passar-o na como entrada opcional. Também pode utilizar c# sintaxe normal expressão personalizada para substituir subcadeias e remover palavras por parte automática no separador Base: substantivos, verbos ou adjetivos.

Depois do pré-processamento estiver concluída, podemos dividir os dados em comboio e teste conjuntos.

## <a name="step-2-extract-numeric-feature-vectors-from-pre-processed-text"></a>Passo 2: Extrair vectores funcionalidade numérico de texto previamente transformado

Para criar um modelo de dados de texto, normalmente, tem de converter o texto de formato livre vectores funcionalidade numéricos. Neste exemplo, vamos utilizar [extrair N g funcionalidades do texto] módulo (https://msdn.microsoft.com/library/azure/mt762916.aspx) para transformar os dados de texto como formato. Este módulo utiliza uma coluna de palavras delimitado por espaços em branco e fórmula calcula um dicionário de palavras ou N-g de palavras, que aparecem no seu conjunto de dados. Em seguida, conta contagens quantas vezes cada do word ou N-g, é apresentado em cada registo e cria vectores funcionalidade das. Neste tutorial, vamos definido N g tamanho para 2, para que os nossos vectores funcionalidade incluam palavras simples e combinações de duas palavras subsequentes.

![Extrair N g](./media/machine-learning-text-analytics-module-tutorial/extract-ngrams.png)

Vamos aplicar TF * FIL (termos frequência inverso documento frequência) ponderação N g conta. Esta abordagem adiciona espessura de palavras que aparecem frequentemente num único registo mas são raros ao longo de todo o conjunto de dados. Outras opções de incluem binário, TF e graph peso.

Essas funcionalidades de texto tem muitas vezes dimensão alta. Por exemplo, se o corpo contiver 100.000 palavras exclusivas, o espaço de funcionalidade teria 100.000 dimensões ou mais se forem utilizados N g. O módulo extrair funcionalidades N g dá-lhe um conjunto de opções para reduzir a dimensão. Pode escolher excluir a palavras que estão curto ou longo, ou demasiado antigas ou demasiado frequente para que o valor de previsão significativo. Neste tutorial, vamos excluir g N que aparece em menos de 5 registos ou no superior a 80% de registos.

Além disso, pode utilizar a seleção de funcionalidades para selecionar apenas as funcionalidades que são mais correlacionadas com o destino de previsão. Utilizamos seleção de funcionalidades chi-quadrada para selecionar 1000 funcionalidades. Pode ver o vocabulário de palavras selecionadas ou N g ao clicar na saída à direita do módulo extrair N-g.

Como utilizar as funcionalidades de N g extrair uma abordagem alternativa, pode utilizar o Hashing funcionalidade módulo. Tenha em atenção que esse [funcionalidade Hashing] (https://msdn.microsoft.com/library/azure/dn906018.aspx) não tenha capacidades de seleção da funcionalidade de compilação ou TF * FIL peso.

## <a name="step-3-train-classification-or-regression-model"></a>Passo 3: Dar formação modelo de classificação ou regressão

Agora o texto de logaritmos para as colunas de funcionalidade numéricos. O conjunto de dados ainda contém colunas de cadeia de fases anterior, por isso utilizamos selecionar colunas no conjunto de dados para exclui-los.

Em seguida, utilizamos [duas classe regressão logística] (https://msdn.microsoft.com/library/azure/dn905994.aspx) para prever nosso destino: Rever alta ou baixa pontuação. Neste momento, o problema de análise de texto de logaritmos um problema de classificação normal. Pode utilizar as ferramentas disponíveis no Azure máquina formação para melhorar o modelo. Por exemplo, pode experimentar diferentes classificadores para saber como precisos resultados derem ou utilizar hyperparameter otimização para melhorar a exatidão.

![Comboio e pontuação](./media/machine-learning-text-analytics-module-tutorial/scoring-text.png)

## <a name="step-4-score-and-validate-the-model"></a>Passo 4: Pontuação e validar o modelo

Como pretende validar o modelo de formação? Estamos a pontuação-lo contra o conjunto de dados de teste e avaliar a precisão. No entanto, o modelo de aprendeu vocabulário de N g e os respetivos espessuras a partir do conjunto de dados de formação. Por conseguinte, podemos deve utilizar esse vocabulário e essas espessuras quando extrair funcionalidades a partir de dados de teste, por oposição à criação de vocabulário uma nova. Por conseguinte, vamos adicionar funcionalidades de N g extrair módulo ao ramo pontuação da experiência, ligue o vocabulário de saída do ramo de formação e defina o modo de vocabulário como só de leitura. Recomendamos também desativar a filtragem de N g por frequência definindo o mínimo como 1 instância e máximo para 100% e desativar a seleção de funcionalidade.

Depois da colunas da funcionalidade numérico de logaritmos a coluna de texto nos dados de teste, podemos excluir as colunas de cadeia de fases anterior como ramo de formação. Em seguida, utilizamos módulo de modelo de pontuação para efetuar previsões e módulo de modelo avaliar para avaliar a precisão.

## <a name="step-5-deploy-the-model-to-production"></a>Passo 5: Implementar o modelo de produção

O modelo é quase pronto a ser implementada produção. Quando implementado como serviço web, leva-o até cadeia de texto de formato livre como entrada e devolver uma previsão "sendo" ou "não". Utiliza o vocabulário N g conhecido para transformar o texto a funcionalidades e o modelo de regressão logística formação para criar uma previsão dessas funcionalidades. 

Para configurar a experiência de aspeto do Office, podemos pela primeira vez guardar o vocabulário N g como conjunto de dados e o modelo de regressão logística formação a partir do ramo formação da experiência. Em seguida, vamos guardar a experiência utilizar "Guardar como" para criar um gráfico de experiência para experiência aspeto do Office. Vamos remover o módulo de dados dividida e o ramo de formação de experiência. Recomendamos, em seguida, ligar o vocabulário N g e o modelo guardada anteriormente a funcionalidades de N g extrair e módulos de modelo de pontuação, respetivamente. Recomendamos também remover o módulo de modelo avaliar.

Estamos inserir selecionar colunas no módulo do conjunto de dados antes de módulo pré-processamento texto para remover a coluna etiqueta e a opção "Coluna de pontuação acrescentar para conjunto de dados" no módulo pontuação anular a seleção. Desta forma, o serviço web não pedir a etiqueta que está a tentar prever e faz não eco a entrada de funcionalidades em resposta.

![Experiência de aspeto do Office](./media/machine-learning-text-analytics-module-tutorial/predictive-text.png)

Agora que temos uma experiência que pode ser publicada como um serviço web e chamada através da execução do pedido de resposta ou lote APIs.

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre módulos de análise de texto a partir do [documentação MSDN] (https://msdn.microsoft.com/library/azure/dn905886.aspx).
