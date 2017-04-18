<properties 
    pageTitle="Utilizar regressão Linear em aprender máquina | Microsoft Azure" 
    description="Uma comparação de modelos de regressão linear no Excel e no Azure máquina aprendizagem Studio" 
    metaKeywords="" 
    services="machine-learning" 
    documentationCenter="" 
    authors="garyericson" 
    manager="jhubbard" 
    editor="cgronlun"  />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/09/2016" 
    ms.author="kbaroni;garye" />

# <a name="using-linear-regression-in-azure-machine-learning"></a>Utilizar regressão linear no Azure máquina aprendizagem

> *Kate Baroni* e *Boatman Carlos* são arquitectura de solução da empresa no dados informações Centro de excelência da Microsoft. Neste artigo, eles descrevem sua experiência migrar um conjunto de aplicações de análise de regressão existente para uma solução baseada na nuvem, utilizando o Azure máquina aprendizagem.  
 
&nbsp; 
  
[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]  
 
## <a name="goal"></a>Objectivo

Os nossos iniciado um projeto no dois objectivos deve ter em conta:  

1. Utilizar a análise de aspeto do Office para melhorar a exatidão do projecções de receitas mensal nossa organização  
2. Utilizar o Azure ML para confirmar, otimizar, aumentar a velocidade e a escala dos nossos resultados.  

Como muitas empresas, o nossa organização vai através de uma processo de previsão de receitas mensal. Foi as tarefas de nossa equipa pequenas de analistas de negócio com a utilização de máquina de aprendizagem para suportar o processo e melhorar a exatidão de previsão.  A equipa gasto vários meses recolher dados de várias origens e executar os atributos de dados através de análise estatística identificar atributos de chave relevantes para a previsão de vendas de serviços.  Foi os passos seguintes começar a modelos de estatísticas de regressão protótipos nos dados no Excel.  Dentro de alguns semanas tivemos um modelo de regressão do Excel que foi outperforming o campo atual e finanças previsão processos. Tornou-se o resultado de previsão do plano base.  


Em seguida, podemos demorou o passo seguinte para mover os nossos analytics aspeto do Office sobre a Azure ML para saber como Azure ML poderia melhorar sobre o desempenho do aspeto do Office.


## <a name="achieving-predictive-performance-parity"></a>Movimentar paridade desempenho aspeto do Office

Foi nossa primeira prioridade alcançar paridade entre os modelos de regressão Azure ML e no Excel.  Tendo em conta os mesmos dados exatos e a divisão da mesma para a formação e testar dados queremos alcançar paridade desempenho aspeto do Office entre o Excel e Azure ML.   Inicialmente podemos Ocorreu uma falha. Modelo do Excel outperformed o modelo de Azure ML.   A falha foi devido a falta de noções básicas sobre a definição de ferramenta de base em Azure ML. Após uma sincronização com a equipa de produto do Azure ML, vamos adquirida com uma melhor compreensão da base definição necessário para o nossos conjuntos de dados e atingidos paridade entre os dois modelos.  

### <a name="create-regression-model-in-excel"></a>Criar o modelo de regressão no Excel
Os nossos regressão Excel utilizado o modelo de regressão linear padrão que se encontram no Excel Analysis ToolPak. 

Estamos calculado *vermelha % absoluta erro* e utilizado como medida de desempenho para o modelo.  Demorou 3 meses para chegar a um modelo de trabalhar com o Excel.  Vamos inseriu muita formação para a experiência de Azure ML que estava finalmente úteis no Noções sobre os requisitos.

### <a name="create-comparable-experiment-in-azure-machine-learning"></a>Criar experiência comparável no Azure máquina aprendizagem  
Vamos seguido estes passos para criar o nossa experiência no Azure ML:  

1.  Carregar o conjunto de dados como um ficheiro csv para Azure ML (ficheiro muito pequeno)
2.  Criada uma nova experiência e utilizada [Selecionar colunas no conjunto de dados] [ select-columns] módulo para selecionar as mesmas funcionalidades de dados utilizadas no Excel   
3.  Utilizar os [Dados divididos] [ split] módulo (com o modo de *Expressão relativa* ) dividir os dados em conjuntos de comboio mesmo exata como tivesse sido concluído no Excel  
4.  Experimented com [Regressão Linear] [ linear-regression] documentados módulo (apenas opções predefinidas) e, em comparação com os resultados ao nosso modelo de regressão do Excel

### <a name="review-initial-results"></a>Rever resultados iniciais
Na primeira, o modelo do Excel outperformed claramente o modelo de Azure ML:  

|   |Excel|Azure ML|
|---|:---:|:---:|
|Desempenho|   |  |
|<ul style="list-style-type: none;"><li>Ajustado R quadrado</li></ul>| 0.96 |N/D|
|<ul style="list-style-type: none;"><li>Coeficiente de <br />Imagem</li></ul>|N/D|   0.78<br />(precisão baixa)|
|Erro absoluto médio |  $9. 5M|  $ 19.4 M|
|Erro absoluto (%) médio|   6.03%|  12.2%

Quando o podemos executou o nosso processo e os resultados por programadores e cientistas de dados da equipa do Azure ML, rapidamente fornecido algumas sugestões úteis.  

* Quando utiliza a [Regressão Linear] [ linear-regression] módulo Azure ml, dois métodos são fornecidos:
    *  Online com gradação de cores descida: Poderão ser mais adequados para problemas de grande escala
    *  Quadrados mínimos normais: Este é o método que maioria das pessoas pensar quando estes ouvir regressão linear. Para conjuntos de dados pequenas quadrados mínimos normais pode ser uma escolha mais ideal.
*  Considere optimização o parâmetro L2 regularização espessura para melhorar o desempenho. Está definido para 0,001 por predefinição e para os nossos pequenas conjunto de dados, podemos configurá-lo a 0,005 para melhorar o desempenho.    

### <a name="mystery-solved"></a>Mistério resolvido!
Quando é aplicada as recomendações, podemos atingir o desempenho do plano base mesmo em ML Azure como com o Excel:   

|| Excel|Azure ML (inicial do nome)|Azure ML c / o ajuste dos quadrados|
|---|:---:|:---:|:---:|
|Valor com nome  |Valores reais (numérico)|mesmo|mesmo|
|Aprendiz  |Excel -> dados -> a análise de regressão|Regressão linear.|Regressão linear|
|Opções de aprendiz|N/D|Predefinições|Ajuste dos quadrados normal<br />L2 = 0,005|
|Conjunto de dados|26 linhas, 3 funcionalidades, 1 etiqueta.   Todos os numérico.|mesmo|mesmo|
|Dividir: comboio|Formação do Excel nas 18 pela primeira vez linhas, testadas nas linhas última 8.|mesmo|mesmo|
|Dividir: teste|Fórmula de regressão aplicada à última 8 linhas do Excel|mesmo|mesmo|
|**Desempenho**||||
|Ajustado R quadrado|0.96|N/D||
|Coeficiente de imagem|N/D|0.78|0.952049|
|Erro absoluto médio |$9. 5M|$ 19.4 M|$9. 5M|
|Erro absoluto (%) médio|<span style="background-color: 00FF00;">6.03%</span>|12.2%|<span style="background-color: 00FF00;">6.03%</span>|

Além disso, os coeficientes de Excel comparados com bem para a espessura de funcionalidade no modelo de formação Azure:

||Coeficientes do Excel|Funcionalidade Azure espessuras|
|---|:---:|:---:|
|Intercetar/tendência|19470209.88|19328500|
|Funcionalidade respostas|0.832653063|0.834156|
|Funcionalidade B|11071967.08|11007300|
|Funcionalidade C|25383318.09|25140800|

## <a name="next-steps"></a>Próximos passos

Queremos consumir Azure ML web service no Excel.  Os nossos analistas de negócio dependem do Excel e é necessária uma forma de chamar o serviço web do Azure ML com uma linha de dados do Excel e mantê-la devolver o valor previsto para o Excel.   

Também queremos otimizar o nosso modelo, utilizando as opções e algoritmos disponíveis no Azure ML.

### <a name="integration-with-excel"></a>Integração com o Excel
A nossa solução era operationalize nosso modelo de regressão Azure ML através da criação de um serviço web a partir do modelo de formação.  Dentro de alguns minutos, o serviço web foi criado e podemos chame-diretamente a partir do Excel para devolver um valor de receitas previsto.    

A secção do *Dashboard de serviços Web* inclui um livro do Excel transferível.  O livro inclui os serviço API e o esquema informações da web incorporadas pré-formatados.   Quando clicar em *Transferir o livro do Excel*, esta é aberta e pode guardá-lo para o seu computador local.    

![][1]
 
Com o livro aberto, copie os parâmetros de predefinidos para a secção de parâmetro azul, conforme apresentado abaixo.  Assim que os parâmetros são introduzidos, Excel chamadas ao serviço web AzureML e o previsto classificados etiquetas serão apresentada na secção os valores previstos verde.  O livro irão continuar a criar previsões para parâmetros com base no seu modelo de formação para todos os itens de linha introduzidas em parâmetros.   Para mais informações sobre como utilizar esta funcionalidade, consulte o artigo [consumir um serviço de Web de aprendizagem do Azure máquina a partir do Excel](machine-learning-consuming-from-excel.md). 

![][2]
 
### <a name="optimization-and-further-experiments"></a>Otimização e ainda mais experiências
Agora que tivemos um plano base com o nosso modelo do Excel, é movido para a frente para otimizar o modelo de regressão Linear nosso Azure ML.  Utilizámos o módulo de [seleção de funcionalidades do filtro de baseado] [ filter-based-feature-selection] para melhorar o nossa selecção de dados iniciais de elementos e -contribuiu-nos alcançar uma melhoria de desempenho de 4.6% vermelha erro absolutas.   Para projetos futuros utilizamos esta funcionalidade que poderia guardar-nos semanas no iteração em atributos de dados para localizar o conjunto de funcionalidades para utilizar para modelização à direita.  

Seguinte planeamos incluir algoritmos adicionais, como [Bayesian] [ bayesian-linear-regression] ou [Aumentado árvores de decisão] [ boosted-decision-tree-regression] no nossa experiência para comparar o desempenho.    

Se quiser experimentar regressão, um bom conjunto de dados para experimentar é o energia eficiência regressão exemplo conjunto de dados, que tem muitas atributos numéricas. O conjunto de dados é fornecido como parte de conjuntos de dados de exemplo no ML Studio.  Pode utilizar uma variedade de aprendizagem módulos para prever aquecimento carga ou carregar refrigeração.  O gráfico abaixo é que uma comparação de desempenho de regressão diferentes aprende contra a eficiência energética conjunto de dados as previsões para a variável de destino Cooling carga: 

|Modelo|Erro absoluto médio|Erro ao quadrado de média raiz|Erro absoluto relativo|Erro ao quadrado de relativas|Coeficiente de imagem
|---|---|---|---|---|---
|Árvore de decisões aumentado|0.930113|1.4239|0.106647|0.021662|0.978338
|Regressão linear (descida gradação)|2.035693|2.98006|0.233414|0.094881|0.905119
|Rede nervoso regressão|1.548195|2.114617|0.177517|0.047774|0.952226
|Regressão linear (normais quadrados mínimos)|1.428273|1.984461|0.163767|0.042074|0.957926  

## <a name="key-takeaways"></a>Chaves Takeaways 

Vamos muitas aprendeu por de regressão do Excel em execução e Azure máquina aprendizagem experiências em paralelo. Criar o modelo do plano base no Excel e compará-lo a modelos de utilizar o Azure ML [Regressão Linear] [ linear-regression] contribuiu saiba-nos Azure ML e podemos descobertas oportunidades para melhorar o desempenho de seleção e o modelo de dados.         

Encontrámos também é aconselhável utilizar [seleção de funcionalidades do filtro de baseado] [ filter-based-feature-selection] para acelerar projetos futuros previsão.  Aplicando a seleção de funcionalidades para os seus dados, pode criar um modelo de melhorada no Azure ML com um melhor desempenho geral. 

A capacidade de transferir o aspeto do Office analítico previsão a partir do Azure ML para o Excel sistémica permite que um aumento significativo na capacidade de com êxito fornecer resultados a um público de utilizador empresas abrangentes.     


## <a name="resources"></a>Recursos
São apresentados alguns recursos de ajuda para que trabalhar com regressão:  

* Regressão no Excel.  Se nunca tiver experimentado regressão no Excel, este tutorial torna mais fácil: [http://www.excel-easy.com/examples/regression.html](http://www.excel-easy.com/examples/regression.html)
* Vs de regressão previsão.  Tyler Chessman escrever um artigo de blogue que explica como tempo série previsão no Excel, que contém a descrição de um bom principiante de regressão linear. [http://sqlmag.com/SQL-Server-Analysis-Services/Understanding-Time-Series-forecasting-Concepts](http://sqlmag.com/sql-server-analysis-services/understanding-time-series-forecasting-concepts)  
*   Regressão Linear de ajuste dos quadrados normais: Falhas, problemas e falhas.  Para uma introdução e debate de regressão: [http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/](http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/ )

[1]: ./media/machine-learning-linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png
[2]: ./media/machine-learning-linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png


<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
 
