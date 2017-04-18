<properties 
    pageTitle="O que é o processo de ciências de dados de equipa?  | Microsoft Azure" 
    description="O processo de ciências de dados de equipa é um método sistemático para criar aplicações inteligentes que tirar partido de análise avançadas." 
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


# <a name="what-is-the-team-data-science-process-tdsp"></a>O que é o processo de ciências de dados de equipa (TDSP)?

O [Processo de ciências de dados de equipa (TDSP)](data-science-process-overview.md) fornece uma abordagem sistemática à criação de aplicações inteligentes que permite que as equipas de cientistas de dados para colaborar eficazmente sobre o ciclo de vida completo das atividades necessário para desativar estas aplicações nos produtos. O TDSP destaca uma sequência de passos que fornecer **orientações** sobre como definir o problema, configure as ferramentas e ambiente for necessário, analisar dados relevantes, criar e avaliar os modelos de aspeto do Office e, em seguida, implementar os modelos de aplicações empresariais. 

Eis os passos no **Processo de ciências de dados de equipa**:  

![Fluxo de trabalho REMATE](./media/machine-learning-data-science-the-cortana-analytics-process/CAP-workflow.png)

O processo é **iterativo**: a compreensão de novos e existentes ou refinamentos no modelo de evolução deste e requer a reformulação cumpridos anteriormente na sequência de passos. Desenvolvimento organizacional existente e processos de planeamento do projecto são **facilmente adaptados** para trabalhar com a sequência definidos pelo TDSP dos passos. 

Os passos no processo são num diagrama ligados no [caminho de formação TDSP](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) e descritos abaixo.  

## <a name="preparation-steps"></a>Passos de preparação 

## <a name="p1-plan-the-analytics-project"></a>P1. Planear o projeto de análise 

Inicie um projeto de análise ao definir o seu objectivos empresariais e problemas. Elas são especificadas em termos de **requisitos de negócio**. É um objectivo central deste passo identificar as variáveis de negócio cruciais (previsão de vendas ou a probabilidade de ser uma ordem fraudulenta, por exemplo) a análise tem de prever de cumprir estes requisitos. Planeamento adicionais, em seguida, é normalmente essencial para desenvolver a compreensão das **origens de dados** necessários para o endereço objetivos do projeto a partir de uma perspetiva analítica. Não é antigas, por exemplo, para encontrar o que precisa de sistemas existentes recolher e registar adicionais tipos de dados para resolver o problema e alcançar os objetivos do projeto. Para obter orientações, consulte o artigo [Planear o seu ambiente para o processo de ciências de dados de equipa](machine-learning-data-science-plan-your-environment.md) e [cenários de análise avançadas no Azure máquina aprendizagem](machine-learning-data-science-plan-sample-scenarios.md).  

## <a name="p2-setup-analytics-environment"></a>P2. Ambiente de análise de configuração 

Um ambiente analytics para o processo de ciências de dados de equipa envolve vários componentes: 

- **áreas de trabalho de dados** onde os dados estão testados para análise e modelação de, 
- uma **transformação infraestrutura** para pré-processamento, explorar e modelação de dados
- uma **infraestrutura de runtime** operationalize os modelos de analytical e executar as aplicações de cliente inteligente que consumir os modelos.  

A infraestrutura de análise que tem de estar configuração frequentemente faz parte de um ambiente em que está separado do sistemas operacional core. Mas -normalmente tira partido de dados a partir de vários sistemas dentro da empresa e do origens externas para a empresa. A infraestrutura de análise pode ser puramente baseado na nuvem, ou um programa de configuração no local ou híbrida dos dois. Para opções, consulte o artigo [Configurar ambientes de ciências de dados para utilização no processo de ciências de dados de equipa](machine-learning-data-science-environment-setup.md).

## <a name="analytics-steps"></a>Análise passos:  

## <a name="1-ingest-data-into-the-analytical-environment"></a>1. ingerir esta última dados para o ambiente analítico 

O primeiro passo é trazer os dados relevantes de várias origens, quer a partir de dentro ou fora da empresa, para um ambientes analítico onde os dados podem ser processados. O **formato** dos dados na origem de pode ser diferentes do formato necessário pelo destino. Por isso, algumas transformação de dados também poderá ter de ser efetuados pelas ferramentas de ingestão. Para opções, consulte o artigo [carregar dados em ambientes de armazenamento para análise](machine-learning-data-science-ingest-data.md)

Para além de ingestão inicial de dados, muitas aplicações inteligentes são necessárias para atualizar os dados regularmente como parte de um processo de aprendizagem em curso. Isto pode ser feito ao configurar um **pipeline de dados** ou o fluxo de trabalho. Isto faz parte da iterativo parte do processo que inclui a reformulação repetida e voltar a avaliar os modelos de analytical utilizados pela aplicação inteligente implementar a solução. Por exemplo, consulte o artigo [mover os dados de um servidor SQL no local para SQL Azure com Azure fábrica de dados](machine-learning-data-science-move-sql-azure-adf.md).


## <a name="2-explore-and-pre-process-data"></a>2. a explorar e previamente processar dados 

O passo seguinte é obter uma mais aprofundada compreensão dos dados por investigar respetivo **Estatísticas de resumo** , relações e ao utilizar técnicas essa **visualização**. É também onde são processados os problemas de **qualidade dos dados** e a integridade, tais como valores em falta, erros de correspondência de tipo de dados e dados inconsistentes, as relações. Pré-processamento transformações são utilizadas para limpar os dados não processados antes de continuar a análise e modelação de pode realizar. Para obter uma descrição, consulte o artigo [formação de tarefas para preparar dados para máquina melhorado](machine-learning-data-science-prepare-data.md).


## <a name="3-develop-features"></a>3. desenvolver funcionalidades 

Cientistas de dados, em colaboração com especialistas de domínio, tem de identificar as funcionalidades que capturar as propriedades importantes do conjunto de dados e que melhor podem ser utilizados para prever as variáveis de negócio cruciais identificadas durante o planeamento. Estas novas funcionalidades podem ser derivadas dos dados existentes ou precisem de dados adicionais para ser recolhidos. Este processo é conhecido como **engenharia funcionalidade** e é um destes passos principais na criação de um sistema de análise de aspeto do Office eficaz. Este passo requer uma combinação de especialidade domínio creative e informações obtidas a partir do passo de informações detalhadas de dados. Para obter orientações, consulte o artigo [funcionalidades de engenharia no processo de ciências de dados de equipa](machine-learning-data-science-create-features.md).


## <a name="4-create-predictive-models"></a>4. criar modelos de aspeto do Office 

Cientistas dados construir modelos analytical para prever as variáveis chaves identificadas por aos requisitos de negócio definidos no planeamento passo através de dados que foi limpo e featurized. Sistemas de aprendizagem máquina suportam vários **Modelação algoritmos** que são aplicáveis a uma grande variedade de casos. Para obter orientações, consulte o artigo [como escolher algoritmos para equipa Azure máquina aprendizagem](machine-learning-algorithm-choice.md).

Cientistas dados tem de escolher o modelo mais adequado para a sua tarefa de previsão e não é invulgar que resultados a partir de vários modelos necessitam de ser combinados para obter os melhores resultados. Para modelação de dados de entrada está normalmente divididos aleatoriamente em três partes:

- um conjunto de dados de formação 
- um conjunto de dados de validação 
- um conjunto de dados de teste 

Os modelos foram criados utilizando o **conjunto de dados de formação**. A combinação ideal de modelos (com parâmetros optimizados) está selecionada executando os modelos e os erros de previsão para o **conjunto de dados de validação**de medição. Por fim **testar o conjunto de dados** é utilizada para avaliar o desempenho do modelo que selecionou no dados independentes que não foram utilizados para formar ou validar o modelo.  Para obter os procedimentos, consulte o artigo [como avalie o desempenho do modelo no Azure máquina aprendizagem](machine-learning-evaluate-model-performance.md).


## <a name="5-deploy-and-consume-models"></a>5. implementar e consuma modelos 

Assim que temos um conjunto de modelos que executam bem, pode ser **operacionalizada** para outras aplicações consumir. Dependendo dos requisitos de negócio, previsões são efetuadas em **tempo real** ou numa base **lote** . Para ser operacionalizada, os modelos de tem de ser apresentadas com uma **API interface aberta** que é consumida facilmente a partir de várias aplicações essas Web site online, as folhas de cálculo, dashboards ou linha de aplicações empresariais e back-end. Consulte o artigo [Implementar um serviço web de aprendizagem do Azure máquina](machine-learning-publish-a-machine-learning-web-service.md).

## <a name="summary-and-next-steps"></a>Resumo e os passos seguintes

O [Processo de ciências de dados de equipa](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) é considerado uma sequência de passos sujeita a iteração esse **fornecer orientações** sobre as tarefas necessárias para utilizar análise avançadas para criar um aplicações inteligente. Cada passo também fornece detalhes sobre como utilizar vários tecnologias da Microsoft para concluir as tarefas descritas. 

Enquanto TDSP não exigir tipos específicos de **documentação** artefactos, é aconselhável os resultados de exploração de dados, modelação e avaliação de documentos e guardar o código pertinente para que possa a análise sujeita a iteração quando for necessário. Também permite reutilização do trabalho analytics quando trabalha com outras aplicações que envolvam dados semelhantes e tarefas de previsão.

Também são fornecidos a tutoriais completa ponto a ponto que demonstram a todos os passos do processo de **cenários específicos** . Consulte o artigo, por exemplo:

- [O processo de ciências de dados de equipa em ação: utilizar o SQL Server](machine-learning-data-science-process-sql-walkthrough.md)
- [o processo de ciências de dados de equipa em ação: utilizar clusters de HDInsight Hadoop](machine-learning-data-science-process-hive-walkthrough.md).
- [Utilizar motores no Azure HD.mdnsight de ciências de dados](machine-learning-data-science-spark-overview.md)
- [Dimensionáveis ciência de dados no Azure dados Lake: um tutorial de ponto a ponto](machine-learning-data-science-process-data-lake-walkthrough.md)

 
