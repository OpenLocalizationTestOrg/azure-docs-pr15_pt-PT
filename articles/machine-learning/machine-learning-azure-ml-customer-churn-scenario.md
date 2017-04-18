<properties
    pageTitle="Analisar o cliente pelo utilizando máquina aprendizagem | Microsoft Azure"
    description="Caso prático de desenvolvimento de um modelo de integrada para analisar e pontuação vasilha de cliente"
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
    ms.date="09/20/2016" 
    ms.author="jeannt"/>

# <a name="analyzing-customer-churn-by-using-azure-machine-learning"></a>Analisar o cliente pelo utilizando a formação de máquina do Azure

##<a name="overview"></a>Descrição geral
Este tópico apresenta uma implementação de referência de um projeto de análise de vasilha de cliente é criado utilizando o Azure máquina aprendizagem Studio. -Aborda modelos genéricos associados para holistically para resolver o problema de vasilha de cliente industrial. Recomendamos também medir a precisão dos modelos que foram criadas ao utilizar máquina aprendizagem e podemos avaliar instruções para o desenvolvimento ainda mais.  

### <a name="acknowledgements"></a>Confirmações

Esta experiência foi desenvolvida e testada Serge Berger, capital dados cientista na Microsoft e Roger Barga, anteriormente produto Manager para Microsoft Azure máquina Learning. A equipa de documentação Azure gratefully reconhece seus conhecimentos e obrigado-los para partilhar este documento técnico.

>[AZURE.NOTE] Os dados utilizados para esta experiência não estão disponíveis ao público. Para um exemplo sobre como criar um modelo de aprendizagem máquina para uma análise vasilha, consulte o artigo: [Telco churn de modelo](http://gallery.cortanaintelligence.com/Experiment/Telco-Customer-Churn-5) na [Galeria de informações da empresa Cortana](http://gallery.cortanaintelligence.com/)


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##<a name="the-problem-of-customer-churn"></a>O problema de vasilha de cliente
Empresas no mercado consumidor e em todos os sectores enterprise tem de fazer em relação aos vasilha. Por vezes vasilha é excessiva e influencie as decisões de política. A solução tradicional é de prever alta propensão churners endereço das suas necessidades através de um serviço de apoio, campanhas de marketing ou aplicando dispensas especiais. Estas abordagens podem variar a partir do setor indústria e até mesmo a partir de um cluster de consumidor específico para outro dentro de uma indústria (por exemplo, telecomunicações).

O factor comuns é que empresas necessitam minimizar estes esforços de retenção do cliente especial. Assim sendo, uma metodologia natural seria para todos os clientes com a probabilidade de vasilha de pontuação e o N superior aqueles de endereço. Principais clientes poderão ser aqueles lucros; Por exemplo, em cenários mais sofisticados, uma função lucro é utilizada durante a seleção de candidatos para dispensa especial. No entanto, as seguintes considerações são apenas uma parte da estratégia holística lidar com vasilha. Empresas também terá de efetuar para o risco de conta (e risco associado tolerância), o nível e o custo de intervenção e segmentação de cliente plausível.  

##<a name="industry-outlook-and-approaches"></a>Outlook do setor e abordagens
Sofisticadas processamento de vasilha é um sinal de uma indústria maduro. O exemplo clássico é a indústria de telecomunicações onde subscritores são conhecidos por frequentemente mudar de um fornecedor para outro. Este vasilha voluntária é uma questão de prime. Além disso, fornecedores tem acumuladas conhecimentos significativo sobre *churn controladores*, que são os fatores que os clientes de unidade para mudar.

Por exemplo, escolha auscultador ou dispositivo é um controlador famoso de vasilha na empresa número de telemóvel. Como resultado, uma política de popular é subsidiem preço de um auscultador para subscritores do novos e carregar um preço completo aos clientes existentes para uma atualização. Historicamente, esta política tem por um instrutor para clientes salto a partir de um fornecedor para outro para obter um novo desconto, por sua vez, a tem de fornecedores de refinar a suas estratégias lhe for pedido.

Grande volatilidade em ofertas do auscultador é um fator que muito rapidamente invalida modelos de vasilha que são baseados em modelos de auscultador atual. Para além disso, telemóveis não estão apenas os dispositivos de telecomunicações; também são declarações moda (considere iPhone) e estas predictors sociais são fora do âmbito regular telecomunicações dos conjuntos de dados.

O resultado atual líquido de modelação é que não é possível elaborar uma política de som simplesmente eliminando conhecidos motivos para vasilha. Na verdade, uma estratégia de modelação contínuo, incluindo os modelos de clássicos quantifique as variáveis categorias (tais como árvores de decisão), é **obrigatório**.

Utilizar grandes conjuntos de dados nos seus clientes, as organizações estão a executar a análise de dados grande (em particular, deteção vasilha com base em dados grandes) como uma abordagem eficaz para o problema. Pode encontrar mais informações sobre a abordagem de grande dados para o problema da vasilha nas recomendações sobre a secção ETL.  

##<a name="methodology-to-model-customer-churn"></a>Metodologia para vasilha de cliente de modelo
Um processo de resolução de problemas comuns para resolver vasilha de cliente é descrito no ilustrações 1-3:  

1.  Um modelo de risco permite-lhe a considerar como ações afetam probabilidade e risco.
2.  Um modelo de intervenção permite-lhe a ter em consideração como o nível de intervenção pode afetar a probabilidade de vasilha e na quantidade de cliente o valor de duração (CLV).
3.  Esta análise é utilizado pela para uma análise de qualitativa é escalou para uma campanha de marketing de pro-activos destinos segmentos de clientes para entregar a oferta ideal.  

![][1]

Esta abordagem reencaminhar apelativa é a melhor forma de trate vasilha, mas este vem com complexidade: temos desenvolver um modelo de múltiplos archetype e dependências de rastreio entre os modelos. Pode ser encapsulated a interação entre modelos, conforme mostrado no diagrama seguinte:  

![][2]

*Figura 4: Unified archetype múltiplos modelo*  

Interação entre os modelos de é chave se estamos a uma abordagem holística para retenção de cliente. Cada modelo necessariamente diminui ao longo do tempo; Por conseguinte, a arquitetura é um ciclo implícito (semelhante a archetype definido pela norma de extração de dados CRISP DM, [***3***]).  

Ciclo de geral de decisão de risco de marketing segmentação/decomposição ainda é uma estrutura de GRG, é aplicável ao muitos problemas de empresas. Análise de vasilha é simplesmente um representante forte deste grupo de problemas porque tem todas as características de um problema de empresarial complexo não permite uma solução de aspeto do Office simplificada. Os sociais aspetos da abordagem moderna para churn, sobretudo, não são realçados na abordagem, mas os aspectos sociais são encapsulated no archetype modelação, como seriam no qualquer modelo.  

Adição interessante aqui é a análise de dados grande. Empresas de revenda e de telecomunicações hoje recolhem exaustivos dados relativos aos seus clientes e podemos facilmente pode prever que a necessidade de conectividade de múltiplos modelo irão tornar-se de uma tendência comuns, tendo em conta tendências emergentes como a Internet das coisas e dispositivos ubíquos, que permitem empresas para empregam soluções inteligentes às várias camadas.  

 
##<a name="implementing-the-modeling-archetype-in-machine-learning-studio"></a>Implementar a archetype de modelação no máquina aprendizagem Studio
Tendo em conta o problema tal descrito, o que é a melhor forma de implementar uma modelação integrada e pontuação abordagem? Nesta secção, podemos vai demonstrar como podemos Feito isto usando Azure máquina aprendizagem Studio.  

A abordagem de múltiplas modelo é a terão de ao estruturar um archetype global para vasilha. Mesmo a pontuação (aspeto do Office) parte da abordagem deve ser múltiplos modelo.  

O diagrama seguinte mostra o protótipo que criámos, que utiliza quatro algoritmos pontuação na máquina Studio de aprendizagem para prever vasilha. O motivo para utilizar uma abordagem de múltiplas modelo não é apenas criar um classificador conjunto para aumentar a precisão, mas também para proteger contra montagem excessiva e para melhorar a funcionalidade à seleção.  

![][3]

*Figura 5: Protótipo de um vasilha abordagem de modelação*  

As secções seguintes fornecem mais detalhes sobre o protótipo pontuação modelo que são implementadas através da utilização máquina aprendizagem Studio.  

###<a name="data-selection-and-preparation"></a>Preparação e de selecção de dados
Os dados utilizados para criar os modelos e os clientes de pontuação foi obtidos a partir de uma solução vertical CRM, com os dados foram encobertos para proteger a privacidade de cliente. Os dados contém informações sobre 8000 subscrições nos Estados Unidos e combina a origens de três: aprovisionamento dados (subscrição metadados), os dados da actividade (utilização do sistema) e dados de suporte ao cliente. Os dados não inclui qualquer negócio relacionado com informações sobre os clientes; Por exemplo, não inclui as pontuações de metadados ou crédito fidelidade.  

Para simplificar, ETL e processos de limpeza de dados são fora do âmbito porque podemos partem do princípio de que preparação de dados tem já foi feitos noutro local.   

Seleção de funcionalidades para modelação é baseada significância prévia pontuação de um conjunto de predictors, incluídas no processo de que utiliza o módulo floresta aleatório. Para a implementação no máquina aprendizagem Studio, podemos calculada a média, mediana e intervalos de representante de funcionalidades. Por exemplo, adicionámos agregados para os dados qualitativos, tal como valores mínimo e máximo de atividade de utilizador.    

Recomendamos também capturado temporais informações para o mais recente seis meses. Vamos analisar dados de um ano e Estabelecemos que mesmo se havia tendências estatisticamente significativas, o efeito no vasilha é bastante diminuído seis meses depois.  

O ponto mais importante é que todo o processo, incluindo ETL, seleção de funcionalidades e de modelação foi implementado na máquina aprendizagem Studio, utilizar origens de dados no Microsoft Azure.   

Diagramas seguintes ilustram os dados que foram utilizados.  

![][4]

*Figura 6: Excerto da origem de dados (encoberto)*  

![][5]


*Figura 7: Funcionalidades extraídas de origem de dados*
 
> Note que estes dados forem privados e, consequentemente, não podem ser partilhados o modelo e os dados.
> No entanto, para um modelo semelhante a utilizar dados publicamente disponíveis, consulte o artigo este exemplo experimentar na [Galeria de informações da empresa Cortana](http://gallery.cortanaintelligence.com/): [Telco de cliente](http://gallery.cortanaintelligence.com/Experiment/31c19425ee874f628c847f7e2d93e383).
> 
> Para saber mais sobre como pode implementar um modelo de análise de vasilha utilizando o conjunto de aplicações do Cortana informações da empresa, recomendamos que também [Este vídeo](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html) de Gestor de programa sénior vacina Hyong Tok. 
> 

###<a name="algorithms-used-in-the-prototype"></a>Algoritmos utilizados no protótipo

Utilizámos seguintes quatro máquina formação dos algoritmos para construir o protótipo (sem personalização):  

1.  Logística regressão (LR)
2.  Árvore de decisões aumentado (BT)
3.  Perceptron médio (Turma)
4.  Suporte de máquina de vetor (SVM)  


O diagrama seguinte ilustra uma parte da superfície de estrutura da experiência, indique a sequência na qual foram criados os modelos de:  

![][6]  


*Figura 8: Criação de modelos no máquina aprendizagem Studio*  

###<a name="scoring-methods"></a>Métodos de pontuação
Os modelos de quatro podemos classificados ao utilizar um conjunto de dados de formação com nome.  

Recomendamos também apresentados o conjunto de dados pontuação a um modelo de comparável criado ao utilizar a edição de ambiente de trabalho do SA Enterprise consequência 12. Vamos medido a precisão do modelo SA e todos os modelos de máquina aprendizagem Studio quatro.  

##<a name="results"></a>Resultados
Nesta secção, podemos apresentar os nossos resultados sobre a precisão dos modelos, com base em pontuação o conjunto de dados.  

###<a name="accuracy-and-precision-of-scoring"></a>Precisão de pontuação
Geralmente, a aplicação em Azure máquina aprender é atrás SA precisão cerca de 10 a 15% (área em curva ou AUC).  

No entanto, a métrica mais importante no vasilha é a taxa de erros de classificação: ou seja, de churners o início N como previsto pelo classificador, qual deles fazia realmente **não** vasilha e ainda recebida tratamento especial? O diagrama seguinte compara esta taxa de erros de classificação de todos os modelos:  

![][7]


*Figura 9: Área de protótipo Passau em curva*

###<a name="using-auc-to-compare-results"></a>Utilizar AUC para comparar os resultados
Área em curva (AUC) é uma métrica que representa uma medida global da *separabilidade* entre as distribuições de resultados para populações positivos e negativos. É semelhante ao grafo do tradicional Recetor operador característica (ROC), mas uma diferença importante é que a métrica do AUC não requer que escolher um valor limite. Em vez disso, resume os resultados ao longo de **todas as** escolhas possíveis. Em contrapartida, o gráfico ROC tradicional mostra a taxa positiva no eixo vertical e a taxa falsa positiva no eixo horizontal e o limiar de classificação varia.   

AUC é geralmente utilizada como uma medida de património para algoritmos diferentes (ou diferentes sistemas) porque permite que os modelos de ser comparado com os respetivos valores AUC. Esta é uma abordagem mais popular no ramos como meteorologia e biosciences. Assim sendo, AUC representa uma ferramenta popular para avaliar o desempenho classificador.  

###<a name="comparing-misclassification-rates"></a>Comparar as taxas de classificação
Vamos comparado com as taxas de classificação do conjunto de dados em questão, utilizando os dados CRM de aproximadamente 8000 subscrições.  

-   A taxa de erros de classificação SA foi 10 15%.
-   A taxa de erros de classificação de máquina aprendizagem Studio foi 15-20% para os churners início 200 300.  

Na indústria telecomunicações, é importante endereçar apenas os clientes que têm o mais alto risco churn através da oferta de um serviço de apoio ou a outro tratamento especial. Neste caso, a aplicação de máquina aprendizagem Studio atinge resultados vem o modelo de SA.  

Do mesmo modo, precisão é mais importante do que precisão porque é principalmente estiver interessados em corretamente classificar churners potenciais.  

O diagrama seguinte a partir da Wikipédia ilustra a relação num gráfico cheios de vida, fácil de compreender:  

![][8]

*Figura 10: Variação entre a precisão e precisão*

###<a name="accuracy-and-precision-results-for-boosted-decision-tree-model"></a>Precisão resultados para modelo de árvore de decisões aumentado  

O gráfico seguinte apresenta os resultados não processados de pontuação utilizando o protótipo máquina formação para o modelo de árvore de decisão aumentado, o que acontece ser mais exatas entre os modelos de quatro:  

![][9]

*Figura 11: Aumentado decisão árvore modelo características*

##<a name="performance-comparison"></a>Comparação de desempenho
Vamos comparados com a velocidade com que dados foram classificados utilizando os modelos de máquina aprendizagem Studio e um modelo comparável criados com a edição de ambiente de trabalho do SA Enterprise consequência 12.1.  

A tabela seguinte resume o desempenho dos algoritmos:  

*Tabela 1. Desempenho geral (precisão) dos algoritmos*

| LR|BT|TURMA|SVM|
|---|---|---|---|
|Modelo de média|O modelo de melhor|Apresentem um desempenho inferior|Modelo de média|

Os modelos de alojado no máquina aprendizagem Studio SA outperformed por 15 25% de velocidade de execução, mas precisão foram em grande medida valor_nominal.  

##<a name="discussion-and-recommendations"></a>Debate e recomendações
Na indústria telecomunicações, várias práticas entretanto apareceram para analisar vasilha, incluindo:  

-   Deriva métricas de quatro categorias fundamentais:
    -   **Entidade (por exemplo, uma subscrição)**. Aprovisionar informações básicas sobre a subscrição e/ou cliente que é o assunto da vasilha.
    -   **Atividade**. Obter todas as informações de utilização possíveis que está relacionada com a entidade, por exemplo, o número de inícios de sessão.
    -   **Suporte ao cliente**. Recolher informações a partir de registos de suporte ao cliente para indicar se a subscrição teve problemas ou interações com o suporte ao cliente.
    -   **Dados competitivos e profissionais**. Obter as informações possíveis acerca do cliente (por exemplo, pode ser indisponíveis ou disco rígido controlar).
-   Utilize a importância a seleção da funcionalidade de unidade. Isto implica que o modelo de árvore de decisões aumentado está sempre uma abordagem compromisso.  

A utilização destes quatro categorias cria a ilusão que uma abordagem simples *determinista* , baseada em índices de formadas no fatores razoáveis por categoria, deve ser suficiente para identificar os clientes de risco de vasilha. Infelizmente, apesar de parece plausível este conceito, é uma compreensão falsa. O motivo é que vasilha é um efeito temporal e os fatores que contribuem para churn são normalmente nos Estados breves. O que oportunidades potenciais um cliente a ter em consideração sair hoje poderão ser diferentes amanhã, e -certamente serão diferentes seis meses a partir de agora. Por conseguinte, um *probabilística* o modelo é uma necessidade.  

Esta observação importante com frequência é negligenciada na empresa, que prefere geralmente uma abordagem de business intelligence orientados para análise, principalmente porque é uma mais fácil vender e admissões automatização simples.  

No entanto, a promessa de análise de gestão personalizada utilizando máquina de formação Studio é que as quatro categorias de informação, classificados por divisão ou departamento, tornar-se uma origem de útil para conhecer máquina os vasilha.  

Outra capacidade apelativo chegar no Azure máquina formação é a capacidade de adicionar um módulo personalizado para o repositório de módulos predefinidos que já se encontram disponíveis. Esta capacidade, essencialmente, cria uma oportunidade para selecionar bibliotecas e criar modelos para mercados verticais. É um factor de diferenciação importante da Azure máquina aprendizagem no mercado local.  

Vamos Espero continuar a este tópico no futuro, especialmente relacionados com a análise de dados grande.
  
##<a name="conclusion"></a>Conclusão
Este papel descreve uma abordagem cuidada para lidar com o problema comum de cliente vasilha ao utilizar um quadro genérico. Vamos considerado um protótipo de modelos de pontuação e implementada-lo através da utilização Azure máquina aprendizagem. Por fim, vamos avaliada a precisão e o desempenho da solução protótipo exata relativamente à algoritmos comparáveis na SA.  

**Para obter mais informações:**  

Deste artigo ajudá-lo? Dê-nos seus comentários. Dê-numa escala de 1 (má) para 5 (excelente), como seria classificar deste documento e por que motivo deu-esta classificação? Por exemplo:  

-   É lhe classificação-alto devido a ter bons exemplos, capturas de ecrã excelente, desmarque a escrever ou qualquer outra razão?
-   É lhe classificação-baixa devido a originar má exemplos, capturas de ecrã nítidos ou escrita pouco claras?  

Este feedback irá ajudá-na melhorar a qualidade da técnicos com regulamentações que disponibilizamos.   

[Enviar comentários](mailto:sqlfback@microsoft.com).
 
##<a name="references"></a>Referências
Análise de aspeto do Office [1] : para além da previsões, McKnight Ocidental, gestão de informação, Julho/Agosto de 2011, p.18-20.  

Artigo da Wikipédia de [2] : [precisão](http://en.wikipedia.org/wiki/Accuracy_and_precision)

[3] [CRISP-DM 1.0: Guia de extração de dados passo a passo](http://www.the-modeling-agency.com/crisp-dm.pdf)   

[4] [dados grande Marketing: realizam forma mais eficaz os seus clientes e unidade valor](http://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn)

[5] [Telco churn de modelo](http://gallery.cortanaintelligence.com/Experiment/Telco-Customer-Churn-5) na [Galeria de informações da empresa Cortana](http://gallery.cortanaintelligence.com/) 
 
##<a name="appendix"></a>Anexo

![][10]

*Figura 12: Instantâneo de uma apresentação no vasilha protótipo*


[1]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-1.png
[2]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-2.png
[3]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-3.png
[4]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-4.png
[5]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-5.png
[6]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-6.png
[7]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-7.png
[8]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-8.png
[9]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-9.png
[10]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-10.png
