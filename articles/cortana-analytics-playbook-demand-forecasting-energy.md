<properties
    pageTitle="Cortana Intelligence solução modelo Playbook para previsão de pedido de energia | Microsoft Azure"
    description="Um modelo de solução com Microsoft Cortana Intelligence que ajuda a previsão da procura para uma empresa de utilitário de energia."
    services="cortana-analytics"
    documentationCenter=""
    authors="ilanr9"
    manager="ilanr9"
    editor="yijichen"/>

<tags
    ms.service="cortana-analytics"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/24/2016"
    ms.author="ilanr9;yijichen;garye"/>

# <a name="cortana-intelligence-solution-template-playbook-for-demand-forecasting-of-energy"></a>Cortana Intelligence solução modelo Playbook para previsão de pedido de energia  

## <a name="executive-summary"></a>Resumo Executivo  

Nos últimos alguns anos, Internet coisas (IoT), fontes de energia alternativo e dados grandes tem Unidas para criar oportunidades vastas no domínio de utilitário e energia. Ao mesmo tempo, o utilitário e sector da energia inteira tem visto consumo atenuamento com os consumidores exigem melhores formas de controlar a sua utilização de energia. Por conseguinte, o utilitário e empresas grelha inteligente estão necessidade excelente inovar e renovar próprios. Além disso, muitos grelhas utilitário e power são tornar-se Desatualizadas e muito dispendioso manter e gerir. Durante o último ano, a equipa tem trabalhado num número de compromissos dentro do domínio de energia. Durante a estes compromissos, podemos ter encontrado muitos casos em que utilitários ou fabricantes independentes de software (fornecedores de Software independentes) ter sido está à procura para previsão para a procura de energia futuras. Estas previsões reproduzir uma função de importante no respetivo negócio atual e futuro e ficaram foundation para vários casos de utilização. Estas incluem-se ao power curto e a longo prazo carga previsão, negociação, balanceamento de carga, otimização de grelha etc. Dados grandes e métodos de análise avançadas (AA) como máquina aprendizagem (ML) são as chaves enablers para produzir correctos e fiáveis previsões.  

Neste playbook, vamos juntar empresas e diretrizes analytical necessárias para o desenvolvimento de efetuada com êxito e solução de previsão de implementação do pedido de energia. Estas diretrizes propostas podem ajudar utilitários, cientistas de dados e engenheiros de dados estabelecer soluções totalmente operacionalizadas, baseado na nuvem, previsão pedido. Para as empresas que apenas estão a ser iniciadas os respetivos dados grandes e viagem de análise avançadas, como uma solução pode representar a propagação inicial na sua estratégia de grelha inteligente a longo prazo.

>[AZURE.TIP] Para transferir um diagrama que fornece uma descrição geral arquitetura deste modelo, consulte o artigo [arquitetura de modelo de solução de informações da empresa Cortana para previsão de pedido de energia](cortana-analytics-architecture-demand-forecasting-energy.md).  

## <a name="overview"></a>Descrição geral  

Este documento abrange o negócio, dados e aspectos técnicos do utilizar análise de Cortana e, em determinada Azure máquina Learning AML () para a implementação e implementação de soluções de previsão de energia. O documento é constituído por três partes principais:  

1. Noções sobre de empresas  
2. Noções sobre de dados  
3. Implementação técnica

A peça de **Empresas compreensão** destaca o aspecto de empresas que é necessário compreender e ter em consideração antes de decisão de investimento. Explica como qualificar o problema de empresas à mão para garantir que a aprendizagem automática e de análise de aspeto do Office estão facto eficazes e aplicável. O documento ainda mais explica as noções básicas de aprendizagem automática e como são utilizadas para resolver problemas de previsão de energia. Descreve os pré-requisitos e critérios qualificação de um caso de utilização. Algumas exemplo utilize casos empresas maiúsculas e cenários também são fornecidos.

Os dados são o ingrediente principal para qualquer solução de aprendizagem automática. A peça de **Dados compreensão** deste documento abrange alguns aspetos importantes dos dados. Descreve o tipo de dados que são necessário para previsão de energia, requisitos de qualidade de dados e as origens de dados que existem normalmente. Recomendamos também explicam como os dados não processados são utilizados para preparar funcionalidades de dados que realmente conduzem a peça de modelação.

A terceira parte do documento abrange o aspecto de **Implementação técnica** de uma solução. Funcionalidade engenharia e modelação de estão no núcleo do processo de ciências de dados e, por conseguinte, estão a ser abordados no alguns detalhes. Cobrir o conceito de serviços web, que são um veículo importante para implementação na nuvem de soluções de análise de aspeto do Office. Recomendamos também destacar uma arquitetura de típica de uma solução operationalized do fim para fim.

Além disso, o documento inclui material de referência que pode utilizar para obter ainda mais Noções sobre do domínio e tecnologia.

É importante ter em atenção que não pretendemos cobrir neste documento o processo de ciências dados mais aprofundado, seus aspectos matemáticos e técnicos. Estes detalhes podem encontrar na [documentação do Azure ML](http://azure.microsoft.com/services/machine-learning/) e [blogues](http://blogs.microsoft.com/blog/tag/azure-machine-learning/).

### <a name="target-audience"></a>Público-alvo   
O público-alvo para este documento é empresas e pessoal técnico quem gostaria de obter dados de conhecimento e compreensão dos máquina formação baseado soluções e como estes estão a ser utilizados especificamente dentro do domínio previsão de energia.

Também podem beneficiar cientistas de dados a partir da leitura este documento para obter uma melhor compreensão do processo de alto nível unidades a implementação de uma solução de previsão de energia. Neste contexto-pode também ser utilizado para estabelecer um boa do plano base e o ponto de partida para obter mais informações detalhadas e avançadas material.

### <a name="industry-trends"></a>Tendências de indústria  
Nos últimos alguns anos, IoT, fontes de energia alternativo e dados grandes tem Unidas para criar oportunidades vastas no espaço utilitário e energia. Ao mesmo tempo, o utilitário e sectores energia inteira tem visto consumo atenuamento com os consumidores exigem melhores formas de controlar a sua utilização de energia.

Muitos utilitário e empresas de energia inteligente tem sido experiência de um da [grelha inteligente](https://en.wikipedia.org/wiki/Smart_grid) ao implementar um número de utilização casos que tornam utilizam dos dados gerados pela grelha. Muitos casos de utilização baseiam-se à volta das características inerentes de produção eléctrica: não é possível acumulada nem reserve armazenado como inventário. Sim, o que é produzido deve ser utilizado. Precisa de previsão consumo de energia simplesmente utilitários que pretende tornar-se mais eficientes porque que irá dá-los maior capacidade de **Saldo oferta e a procura**, evitando-se as perdas de energia, **reduzir emissão de gás estufa**e o custo de controlo.

Quando falar de custos, existe outro aspecto importante, que corresponde ao preço. Novas capacidades ao comércio power entre utilitários tem inseriu uma excelente necessidade de **previsão procura futura e futuro preço de eléctrica**. Isto pode ajudar a determinar os respetivos volumes de produção de empresas.

Quando utilizamos a palavra 'inteligente', podemos realmente a referência a uma grelha que pode obter informações sobre e, em seguida, faça previsões. -Pode antecipar sazonais alterações no consumo, bem como **prever sobrecarga temporário situações e ajustar automaticamente para a mesma**. Através da regulação remotamente consumo (com a ajuda destes inteligentes metros), poderá ser resolvidas situações sobrecarga localizados. **Ao primeiro as previsões e, em seguida, serve**, a grelha torna propriamente dito forma inteligente ao longo do tempo.

Para o resto deste documento iremos destacar uma família específica de casos de utilização que abrangem a previsão de futuro, curto e a procura de energia a longo prazo. Estamos trabalhado nas seguintes áreas para alguns meses e ter adquirida com alguns dados de conhecimento e competências que permitem-nos produzir resultados de classe do setor. Outros casos de utilização serão abrangidos também no documento no futuro próximo.

## <a name="business-understanding"></a>Noções sobre de empresas

### <a name="business-goals"></a>Objectivos de negócio
É o objetivo de **Demonstração de energia** demonstrar uma solução que pode ser implementada numa moldura de tempo muito curto de aprendizagem automática e a análise típica de aspeto do Office. Especificamente, o nosso foco atual é sobre ativar soluções de previsão de pedido de energia para que o valor de negócio possa rapidamente realizado e utilizado relativamente a. As informações neste playbook podem ajudar o cliente realizar os objetivos seguintes:
-   Hora abreviada ao valor de aprendizagem automática com base solução
-   Capacidade para expandir um piloto utilizar maiúsculas/minúsculas para outros casos de utilização ou para um âmbito mais amplo com base na sua necessidade de negócio
-   Obter dados de conhecimento do conjunto de informações da empresa Cortana produto rapidamente

Com estes objetivos deve ter em conta, esta playbook destina-se a fornecer o negócio e dados de conhecimento técnico de que o podem ajudar na alcançar estes objectivos.

### <a name="power-load-and-demand-forecasting"></a>Power carga e pedido de previsão
Dentro do sector de energia, podem existir várias formas no qual a procura de previsão pode ajudar a resolver problemas críticos da empresa. Na verdade, pedido previsão pode ser considerado foundation para muitos casos de utilização de core na indústria. Em geral, considere podemos dois tipos de previsões de pedido de energia: curto e longo prazo. Cada um deles poderá servir um objetivo diferente e utilizam uma abordagem diferente. A diferença entre os dois principal é o horizonte previsão, o que significa que o intervalo de tempo para o futuro para o qual podemos seria a previsão.

#### <a name="short-term-load-forecasting"></a>Curtas termos carga previsão
Dentro do contexto do pedido de energia, breve termos carregar previsão (STLF) é definida como a carga de agregado está prevista no futuro próximo em várias partes da grelha da (ou a grelha como um todo). Neste contexto, curto prazo é definido para ser horizonte temporal no intervalo de 1 hora a 24 horas. Em alguns casos, um horizonte de 48 horas também é possível. Por conseguinte, STLF é muito frequente num caso de utilização operacional da grelha. Eis alguns exemplos de STLF pelo esforço casos de utilização:
-   Oferta e a procura balanceamento
-   Suporte de negociação Power
-   Tornar de mercado (preço de energia definição)
-   Otimização operacional grelha
-   [Pedido de resposta](https://en.wikipedia.org/wiki/Demand_response)
-   Pedido de previsão de pico
-   Gestão da procura lado
-   Balanceamento de carga e prevenção de sobrecarga
-   A longo prazo carga previsão
-   Deteção de falhas e anomalia
-   Pico corte/nivelamento 

Modelo de STLF principalmente são baseados no passado próximo (último dia ou da semana) dados de consumo e a utilização prevista temperatura como um predictor importante. Obter temperatura precisa de previsão para a hora seguinte e para cima para 24 horas está a ficar menor um desafio dias agora. Estes modelos são menos sensíveis a sazonais padrões ou tendências de consumo a longo prazo.

Soluções SLTF também são muito provavelmente gerar grande volume de chamadas de previsão (pedidos de serviço) uma vez que estão a ser chamados numa base de hora a hora e em alguns casos mesmo com maior frequência. Também é muito frequente ver implantação onde cada subestação individual ou transformador é representado como um modelo de autónomo e, consequentemente, o volume de pedidos de previsão maiores do mesmo.

#### <a name="long-term-load-forecasting"></a>A longo prazo carga previsão
O objetivo de longo prazo carga previsão (LTLF) é a previsão da procura power com horizonte uma vez que se situa entre 1 semana de vários meses (e, em alguns casos de um número de anos). Este intervalo de cores horizonte predefinido é principalmente aplicável para planeamento e casos de utilização do investimento.

Para obter cenários a longo prazo, é importante ter dados de alta qualidade que abrange um intervalo de múltiplos anos (mínimos 3 anos). Estes modelos normalmente extrair padrões de sazonalidade de dados de histórico e utilize predicators externos tal como padrões meteorologia e clima.

É importante esclarecer que quanto maior for o horizonte previsão é, quanto menos precisos poderá estar a previsão. Por conseguinte, é importante produzir algumas intervalos de confiança juntamente com a previsão real que permitirá homem para factor a variação possível para os respetivos processo de planeamento.

Uma vez que o cenário consumo para LTLF principalmente está a planear, podemos pode esperar muito volumes de previsão mais baixos (em comparação com STLF). Vamos normalmente iria ver estas previsões incorporadas ferramentas de visualização, tal como o Excel ou obter e ser chamados manualmente pelo utilizador.

### <a name="short-term-vs-long-term-prediction"></a>Termos breve vs. longa termos previsão
A tabela seguinte compara STLF e LTLF em relação aos atributos mais importantes:

|Atributo|Carga de curto prazo de previsão|Longo prazo previsão de carregamento|
|---|---|---|
|Previsão de cores horizonte predefinido|A partir de 1 hora a 48 horas|A partir de 1 a 6 meses ou mais|
|Granularidade de dados|Preço por hora|Diária ou hora a hora|
|Casos de utilização típica|<ul><li>Procura/fornecimento balanceamento</li><li>Escolha a previsão de hora</li><li>Pedido de resposta</li></ul>|<ul><li>A longo prazo planeamento</li><li>Planeamento de recursos de grelha</li><li>Planeamento de recursos</li></ul>|
|Predictors típicas|<ul><li>Dia ou da semana</li><li>Hora do dia</li><li>Temperatura preço por hora</li></ul>|<ul><li>Mês do ano</li><li>Dia do mês</li><li>Temperatura de longo prazo e clima</li></ul>|
|Intervalo de dados de histórico|Património em dois ou três anos de dados|Património dos cinco a 10 anos de dados|
|Precisão típica|MAPE * 5% ou inferior|MAPE * de 25% ou inferior|
|Frequência de previsão|Produzidos cada hora ou a cada 24 horas|Produzidos assim mensal, trimestral ou anual|
\*[MAPE](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error) – erro de percentagem média média

Como podem ser vistos desta tabela, é muito importante distinguir entre a curto e a longo prazo previsão cenários, tal como estes representam as necessidades da empresa diferente e poderão ter diferentes implementação e padrões de consumo.

### <a name="example-use-case-1-esmart-systems--overload-optimization"></a>Caso de utilização de exemplo 1: eSmart sistemas – otimização de sobrecarga
Uma função importante de uma [grelha inteligente](https://en.wikipedia.org/wiki/Smart_grid) é dinamicamente constantemente optimize e ajustar para os padrões de consumo alteração. Consumo de energia pode ser afectado pelo alterações curto prazo que principalmente são causadas pelos variações de temperatura (*por exemplo*, mais potência é utilizado para condição de ar ou aquecimento). Ao mesmo tempo, consumo de energia também é influenciado pela tendências a longo prazo. Estes podem incluir efeitos sazonalidade, feriados nacionais, a longo prazo crescimento de consumo e mesmo económicas fatores como índice consumidor, preço Adamastor e PIB.

Neste caso de utilização, [eSmart](http://www.esmartsystems.com/) pretendia implementar uma solução baseada na nuvem que permite que as previsões propensão da situação sobrecarga no qualquer subestação determinada da grelha. Em particular, eSmart pretendia identificar subestações que provavelmente sobrecarga dentro da hora seguinte, para que uma ação de imediata pode tomar para evitar ou resolver esta situação.

Um precisas e efetuar a mesma rapidez com predição necessita de implementação dos três modelos de aspeto do Office:
-   Tempo modelo de termos que lhe permite previsão de consumo de energia no cada subestação durante a próxima alguns semanas ou meses
-   Modelo de curto prazo que lhe permite previsão da situação sobrecarga uma determinado subestação durante a hora seguinte
-   Modelo de temperatura que fornece a previsão de temperatura futura através de vários cenários

Objectivo de modelo de longo prazo é classificar subestações pelo respetivo propensão para Sobrecarga (dado a sua capacidade de transmissão de energia) durante a semana ou mês seguinte. Isto permite a criação de uma pequena lista das subestações que iria servir como uma entrada de dados para a previsão de curto prazo. Como temperatura é uma predictor importante para o modelo a longo prazo, existe uma necessidade de constantemente produzir previsões de temperatura com várias cenário e feed-los como entrada para o modelo a longo prazo. A previsão de curto prazo, em seguida, é chamada para prever qual subestação é provável que sobrecarga sobre a hora seguinte.

Os modelos de curto e longo prazo são implementados individualmente por cada subestação. Por conseguinte, a execução destes modelos prática necessita orchestration extenso. Para obter maior precisão de previsão a curto prazo, um modelo de mais granular é dedicado para cada hora do dia. Todos os estes modelos são executados a cada hora e de conclusão execução dentro de alguns minutos para permitir que o tempo suficiente responder e efetuar ações prevenção caso seja necessário. Desta coleção de modelos é atualizada utilizando periódicas readaptação os dados mais recentes.

Obter mais informações sobre este casos de utilização podem ser encontradas [aqui](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18945).

#### <a name="use-case-qualification-criteria--prerequisites"></a>Utilizar critérios de maiúsculas/minúsculas qualificação – pré-requisitos
A intensidade do principal da Cortana Intelligence é na sua capacidade para implementar e dimensionar soluções centrada de aprendizagem máquina eficientes. Destina-se para o suporte milhares de previsões que são executadas em simultâneo. Pode dimensionar automaticamente para satisfazer um padrão de consumo alteração. Assim, o foco de uma solução é no precisão e o desempenho utilizaria. Por exemplo, uma empresa utility está interessada em que produza a procura de energia precisão de previsão para a hora seguinte e para cada hora do dia. Por outro lado, estamos a menos interessados em resposta à pergunta de por que motivo o pedido é previsto para ser tal como é (o modelo propriamente dito irá encarregam-se de que).

Por conseguinte, é importante aperceba que nem todos os casos de utilização e problemas empresariais podem ser resolvidos de forma eficaz a utilizar formação de máquina.

Cortana Intelligence e aprendizagem de máquina podem ser altamente eficazes para resolver um problema de empresas determinado quando forem cumpridos os seguintes critérios:
-   É o problema de negócio no mão **aspeto do Office** na natureza. Um exemplo de maiúsculas/minúsculas de utilização de aspeto do Office é uma empresa de utilitário que gostaria de prever carga power uma determinado subestação durante a hora seguinte. Por outro lado, analisar e classificar controladores da procura histórica seria **descritivo** em natureza e, consequentemente, menos aplicável.
-   Existe um limpar **caminho de ação** a tomar assim que a previsão estiver disponível. Por exemplo, as previsões uma sobrecarga numa subestação durante a hora seguinte podem acionar uma ação pro-activos de reduzir carga de que está associada esse subestação e potencialmente impedindo o uma sobrecarga.
-   Caso de utilização representa um **típico tipo de problema** essas que, quando resolvido-pode Prepare o caminho para resolver outras semelhantes casos de utilização.
-   O cliente pode definir os **objetivos quantitativos e qualitativos** para demonstrar uma implementação com êxito solução. Por exemplo, um bom objetivo quantitativo para previsão de pedido de energia seria o limiar de precisão requerida (*por exemplo*, seta para cima para o erro de 5% é permitida) ou quando as previsões subestação sobrecarga, em seguida, a precisão (taxa; de positivos verdadeiros) e resgate (extensão da positivos verdadeiros) deve ser acima de um determinado limiar. Estes objetivos devem ser derivados de objectivos de negócio do cliente.
-   Existe um limpar **cenário de integração** com o fluxo de trabalho da empresa empresas. Por exemplo, a previsão de carga subestação pode ser integrada o Centro de controlo de grelha para permitir que as atividades de prevenção de sobrecarga.
-   O cliente tem pronto para utilizar os **dados com qualidade suficiente** para suportar o caso de utilização (consulte mais a secção seguinte, **Qualidade dos dados**, este playbook).
-   O cliente e mulheres abrange nuvem centrados em dados arquitetura ou **de formação baseado na nuvem máquina**, incluindo Azure ML e outros componentes do conjunto de informações da empresa Cortana.
-   O cliente está disposto a estabelecer **um fluxo de dados de fim para fim** que instalações a entrega de dados para a nuvem contínua e é dispostos a **operationalize** a solução.
-   O cliente está pronto a **dedicar recursos** quem vai ser ativamente interessada durante a implementação piloto inicial para que os dados de conhecimento e que é o proprietário da solução podem ser transferidos para o cliente após a conclusão com êxito.
-   O recurso de cliente deve ser um **dados qualificados profissionais**, preferência uma cientista de dados.

Qualificação de um caso de utilização com base em critérios acima referidos substancialmente pode melhorar as taxas de sucesso de um caso de utilização e estabelecer uma boa beachhead para a implementação de casos de utilização futuras.

### <a name="cloud-based-solutions"></a>Soluções baseadas na nuvem
Conjunto de informações da empresa Cortana no Azure é um ambiente integrado que reside na nuvem. A implementação de uma solução de análise avançadas num ambiente nuvem detém vantagens substanciais para empresas e ao mesmo tempo poderão significa alterar grande para empresas que utilizam ainda soluções de TI no local. Dentro do sector de energia, existe uma tendência limpar da migração gradual de operações para a nuvem. Esta tendência vai à mão em mão juntamente com o desenvolvimento da grelha inteligente como de outros fabricantes referidos acima, **Indústria tendências**. Como este playbook é com o foco numa solução baseada na nuvem no domínio de energia, é importante explicar as vantagens e outras considerações de implementação de uma solução baseada na nuvem.

Talvez a maior vantagem de uma solução baseada na nuvem é o custo. Como uma solução utiliza dos componentes implementado na nuvem, não há custos processo decorra ou custos de componente de vendas (custo dos bens vendidos) associados. Isto significa que não precisa de investir em hardware, software e manutenção IT e, consequentemente, não existe uma redução substancial risco de empresas.

Outra vantagem importante é a estrutura de custo repartição das soluções baseadas na nuvem. Os servidores de baseado na nuvem para computação ou armazenamento podem ser implementados e dimensionados numa base just-conforme for necessário. Isto representa a vantagem de eficiência de custo de uma solução baseada na nuvem.

Por fim, não é necessário para investir na manutenção IT ou desenvolvimento de infraestrutura futuras como tudo isto faz parte da oferta baseado na nuvem. Nessa medida, conjunto de informações da empresa Cortana inclui o melhor nos serviços de classe e mantém evolução de seu mapa de estradas. Novas funcionalidades, componentes e capacidades são constantemente a ser introduzidas e evoluem.

Para uma empresa que apenas está a iniciar o respetiva transição na nuvem, podemos são recomendar altamente para tirar uma abordagem gradual através da implementação de um mapa de estradas de migração na nuvem. Vamos acreditar que dos utilitários e empresas de no domínio de energia, casos de utilização que são outros fabricantes referidos neste playbook representam uma excelente oportunidade para tal exija soluções de análise de aspeto do Office na nuvem.

#### <a name="business-case-justification-considerations"></a>Considerações de negócios justificação maiúsculas e minúsculas
Em muitos casos, o cliente poderá estar interessado efetuar uma justificação empresas para um caso de utilização determinado em que uma solução baseada na nuvem e aprendizagem de máquina são componentes importantes. Ao contrário de uma solução no local, no caso de uma solução baseada na nuvem, o componente de custo processo decorra for mínimo e a maior parte dos elementos de custo está associadas a utilização real. Quando chegar implementar uma solução no conjunto de informações da empresa Cortana de previsão de energia, vários serviços podem ser integrados com uma única estrutura comuns do custo. Por exemplo, as bases de dados (*por exemplo*, SQL Azure) podem ser utilizadas para armazenar os dados não processados e, em seguida, para as previsões reais Azure ML é utilizada para os serviços de previsão do anfitrião. Neste exemplo, a estrutura de custo pode incluir armazenamento e componentes transacionais.

Por outro lado, um deve ter uma boa compreensão do valor de negócio de operativo um pedido de energia previsão (termo curto ou longo). Na verdade, é importante apercebeu se o valor de negócio de cada operação de previsão. Por exemplo, com exatidão previsão carga power a 24 horas seguinte pode impedir que overproduction ou pode ajudar a impedir overloads a grelha e isto pode ser quantificado em termos de poupanças financeiras diariamente.

Uma fórmula básica para calcular o benefício financeiro da procura previsão solução seria: ![solução de previsão de uma fórmula básica para calcular o benefício financeiro da procura](media/cortana-analytics-playbook-demand-forecasting-energy/financial-benefit-formula.png)

Uma vez que o conjunto de informações da empresa Cortana fornece um modelo de preços repartição, não é necessário para sempre um componente de custo fixo para esta fórmula. Esta fórmula pode ser calculada numa base diária, mensal ou anualmente.

Atual Cortana conjunto de informações da empresa e Azure ML preços planos podem ser encontradas [aqui](http://azure.microsoft.com/pricing/details/machine-learning/).

### <a name="solution-development-process"></a>Processo de desenvolvimento de soluções
Utilize o ciclo de desenvolvimento de um pedido de energia previsão solução normalmente envolve 4 fases, as quais oferecemos de tecnologias baseado na nuvem e serviços incluído no conjunto de informações da empresa Cortana.

Isto é ilustrado no diagrama seguinte:

![Ciclo de grelha inteligente](media/cortana-analytics-playbook-demand-forecasting-energy/smart-grid-cycle.png)

O parágrafo seguinte descreve este processo do 4 passo:

1.  **Recolha de dados** – qualquer solução de análise avançadas com base baseia-se nos dados (consulte o artigo **Compreender dados**). Especificamente, quando chegar a previsão e de análise de aspeto do Office, podemos depender fluxo em curso, dinâmico de dados. No caso de energia pedido previsão, estes dados podem origem diretamente a partir do metros inteligentes ou já é necessário agregados uma base de dados no local. Recomendamos também dependem de outras origens de dados como meteorologia e temperatura externas. Este fluxo de dados em curso deve ser orquestrado, agendado e armazenado. [Dados Azure fábrica](http://azure.microsoft.com/services/data-factory/) (ADF) é a nossa workhorse principal para realizar esta tarefa.
2.  **Modelação** – de energia correcta e fiável previsões, um tem desenvolver (comboio) e manter um modelo de excelente que torna a utilização de dados de histórico e extrai os padrões de significativos e aspeto do Office nos dados. A área de máquina Learning (ML) tenha sido em crescimento rapidamente com mais avançados algoritmos regularmente a ser desenvolvidos. Azure ML Studio fornece uma experiência de utilizador excelente que ajuda a utilizam dos algoritmos ML mais avançados dentro de um fluxo de trabalho concluída. Esse fluxo de trabalho é ilustrado no diagrama de fluxo de intuitivo e inclui o preparação de dados, extração de funcionalidade, modelação e avaliação de modelo. O utilizador pode utilizar em centenas de vários modelos que estão incluídos neste ambiente. No final nesta fase uma cientista dados terá um modelo de trabalho que está totalmente avaliadas e pronto para implementação.

    O diagrama seguinte é uma ilustração de um fluxo de trabalho normal:

    ![Modelação de fluxo de trabalho](media/cortana-analytics-playbook-demand-forecasting-energy/modeling-workflow.png)

3.  **Implementação** – com um modelo de trabalhar no lado, o passo seguinte é implementação. Aqui é convertido o modelo de um serviço web que expõe um API RESTful que pode ser em simultâneo chamado através da Internet a partir de vários clientes consumo. Azure ML fornece uma forma simple de implementação de um modelo de diretamente a partir do Studio ML Azure com um único clique de um botão. O processo de implementação inteira acontece em definições avançadas. Esta solução pode dimensionar automaticamente para cumprir o consumo necessário.

4.  **Consumo** – nesta fase, na realidade oferecemos utilizar do modelo de previsão para produzir previsões. O consumo pode ser orientado a partir de uma aplicação de utilizador (*por exemplo*, dashboard) ou diretamente a partir de um sistema operacional tal como a pedido/fornecimento balanceamento sistema ou uma solução de otimização de grelha. Podem ser orientados vários casos de utilização de um único modelo.

## <a name="data-understanding"></a>Noções sobre de dados
Após abrangendo as considerações de negócio (consulte o artigo **Compreender empresas**) de um pedido de energia previsão solução, podemos agora está prontos discutir a peça de dados. Qualquer solução analytics aspeto do Office depende fiável de dados. Para previsão de pedido de energia, podemos dependem de dados de histórico consumo com vários níveis de granularidade. Esses dados históricos são utilizados como matéria. -Lo será submetidos uma análise cuidada na qual o cientista dados irá identificar predictors (também conhecidas como funcionalidades) que podem ser colocados num modelo que acaba por ser irá gerar as previsões necessárias.

Os restantes nesta secção, podemos descrevem os vários passos e considerações para compreender os dados e como colocá-lo a um formulário utilizável.

### <a name="the-model-development-cycle"></a>O ciclo de desenvolvimento de modelo
Produzir bom previsão modelos requer alguma preparação cuidadosa e planeamento. Força para baixo o processo de modelação para vários passos e focar um passo de cada vez significativamente podem melhorar o resultado de todo o processo.

O diagrama seguinte ilustra como o processo de modelação pode ser dividido em vários passos:

![Ciclo de desenvolvimento de modelo](media/cortana-analytics-playbook-demand-forecasting-energy/model-development-cycle.png)

À medida que possa ver visto que o ciclo consiste seis passos:
-   Preparação de problema
-   Ingestão de dados e informações detalhadas de dados
-   Preparação de dados e engenharia de funcionalidade
-   Modelação
-   Avaliação de modelo
-   Desenvolvimento

Os restantes nesta secção podemos descrevem os passos individuais e itens a considerar em cada passo.

### <a name="problem-formulation"></a>Preparação de problema
Vamos poderá considerar a composição de problema como o passo mais crítico uma necessita de tomar antes de implementar qualquer solução analytics aspeto do Office. Aqui vamos seria transformar o problema de negócio e decomposição-lo para elementos específicos que podem ser resolvidos utilizando dados e técnicas de modelação. É aconselhável precisar o problema como um conjunto de perguntas que Gostaríamos para responder. Aqui estão algumas perguntas possíveis que podem ser aplicáveis no âmbito da previsão de pedido de energia:
-   Qual é a carga esperada uma subestação individual a hora ou o dia seguinte?
-   Em que altura do dia meu grelha experiência do pedido de pico?
-   Como provável que é o meu grelha para suportar a carga de pico esperado?
-   Quanto power deve gerar estação de energia durante a cada hora do dia?

Elaborar estas questões nos permite focar-se em obter os dados corretos e implementar uma solução que totalmente está alinhada com o problema de negócio em questão. Além disso, podemos, em seguida, pode definir algumas métricas chave que permitem-nos avaliar o desempenho do modelo. Por exemplo, como precisos a previsão deverá e o que é o intervalo de erro que ainda seria aceitável pela empresa?

### <a name="data-sources"></a>Origens de dados
Grelha de inteligente moderna recolhe dados a partir de várias peças e componentes da grelha. Estes dados representam vários aspetos da operação de e a utilização da grelha de energia. No âmbito da procura de energia de previsão, podemos estiverem a limitar o debate em origens de dados que reflita o consumo de procura real. Importante uma fonte de consumo de energia são metros inteligentes. Utilitários em todo o mundo rapidamente estão a implementar metros inteligentes para os respetivos consumidores. Metros inteligentes gravar o consumo de energia real e constantemente estes dados para a empresa utilitário de reencaminhamento. Dados são recolhidos e enviados novamente num intervalo fixo, que se situa entre cada 5 minutos para 1 hora. Metros inteligentes mais avançados podem ser programados remotamente para controlar e equilibrar o consumo real dentro de um agregado. Dados medidor inteligente são relativamente fiáveis e incluem um carimbo de data / hora. Que torna um ingrediente importante para pedido de previsão. Medidor dados podem ser agregados (somados) a vários níveis dentro da topologia de grelha: transformador, subestação, região, *etc*. Em seguida, podemos pode escolher o nível de agregação necessárias para criar um modelo de previsão para a mesma. Por exemplo, se a empresa utilitário gostaria de previsão de carga futura em cada uma das suas subestações de grelha, em seguida, dados dos medidores todos os podem ser agregados para cada subestação individual e utilizados como uma entrada para o modelo de previsão. Vamos referem-se para metros inteligentes como uma origem de dados interno.

Uma previsão de pedido de energia fiável também irá depender de outras origens de dados externos. Um factor importante que afeta consumo de energia é a meteorologia, ou com mais precisão a temperatura. Dados de histórico mostram forte correlação entre temperatura exterior e consumo de energia. Durante dias de Verão quente, fazer consumidores utilizar das suas ar condicionado e durante a alimentação Inverno sistemas de aquecimento. Uma origem fidedigna das temperaturas históricas na localização de grelha, por isso, é chave. Além disso, recomendamos também dependem previsão precisa de temperatura como um predictor de consumo de energia.

Também podem ajudar a outras origens de dados externos na criação de modelos de previsão de pedido de energia. Estes podem incluir a longo prazo variações climáticas, económicos índices (*por exemplo*, PIB) e outras pessoas. Neste documento podemos esta não incluirá estas outras origens de dados.

### <a name="data-structure"></a>Estrutura de dados
Após identificar as origens de dados necessários, gostaríamos de fazer para se certificar de que os dados não processados que tenham sido recolhidos incluem as funcionalidades de dados correctos. Para criar um modelo de previsão pedido fiável, precisamos de Certifique-se de que os dados recolhidos incluem elementos de dados que o podem ajudar a prever a procura futura. Aqui estão alguns requisitos básicos que dizem respeito à estrutura de dados (esquema) dos dados não processados.

Os dados não processados consiste em linhas e colunas. Cada medição é representada como uma única linha de dados. Cada linha de dados inclui múltiplas colunas (também conhecidas como funcionalidades ou campos).

1.  **Carimbo de data / hora** – o campo Data/hora representa a hora real quando a medição foi registada. -Deve cumprir-se um dos formatos de data/hora comuns. Partes de data e hora devem ser incluídos. Na maioria dos casos, não é necessário para o tempo para ser gravados até o segundo nível de granularidade. É importante especificar o fuso horário no qual os dados são registados.
2.  **Medidor de ID** - este campo identifica um indicador de apresentar ou o dispositivo de medida. É uma variável de categorias e pode ser uma combinação de dígitos e carateres.
3.  **Valor consumo** – este é o consumo real correspondente a uma determinada data/hora. O consumo pode ser medido em kWh (kilowatt-hour) ou qualquer outro preferido unidades. É importante ter em atenção que a unidade de medida deve manter-se consistentes em todas as medições dos dados. Em alguns casos, pode ser fornecido consumo superior 3 fases de energia. Nesse caso seria precisamos de recolher todas as fases consumo independentes.
4.  **Temperatura** – a temperatura normalmente são recolhidas a partir de uma fonte independente. No entanto, deve ser compatível com os dados de consumo. Deve incluir uma carimbo data/hora, tal como descrito acima que permitirá a ser sincronizada com os dados de consumo real. O valor de temperatura pode ser especificado em graus Celsius ou Fahrenheit, mas deve manter-se consistentes em todas as medições.
5.  **Localização –** O campo localização está normalmente associado com o local onde os dados de temperatura tem foram recolhidos. Pode ser representado como um número de código postal ou latitude/longitude (lat/longo).

As tabelas seguintes mostra exemplos de um bom consumo e temperatura formato de dados:

|**Data**|**Tempo**|**ID de indicador**|**Fase 1**|**Fase 2**|**Fase 3**|
|--------|--------|------------|-----------|-----------|-----------|
|1/7/2015|10:00:00|ABC1234     |7.0        |2.1        |5.3        |
|1/7/2015|10:00:01|ABC1234     |7.1        |2.2        |4.3        |
|1/7/2015|10:00:02|ABC1234     |6.0        |2.1        |4.0        |

|**Data**|**Tempo**|**Localização**|**Temperatura**|
|--------|--------|-------------|---------------|
|1/7/2015|10:00:00|11242        |24.4           |
|1/7/2015|10:00:01|11242        |24.4           |
|1/7/2015|10:00:02|11242        |24.5           |

Como podem ser vistos acima, neste exemplo inclui 3 diferentes valores para consumo associados com 3 fases de energia. Além disso, tenha em atenção que os campos de data e hora são separadas, no entanto estes podem também ser combinados numa única coluna. Neste caso, a coluna de localização é representada num formato de código postal de 5 dígitos e a temperatura num formato de graus Celsius.

### <a name="data-format"></a>Formato de dados
Conjunto de informações da empresa Cortana pode suportar os formatos de dados mais comuns, como CSV, TSV, JSON, *etc*. É importante que o formato de dados permanece consistente para o inteiro ciclo de vida do projeto.

### <a name="data-ingestion"></a>Ingestão de dados
Uma vez que a previsão de pedido de energia é previsto de constantemente e frequentemente, podemos tem de garantir que os dados não processados estão a fluir através de um processo de ingestão sólida e fiável dados. O processo de ingestão tem garante que os dados não processados estão disponíveis para o processo de previsão na altura requerida. Que implica que a frequência de ingestão dados deve ser maior do que a frequência de previsão.

Por exemplo: se os nossos pedido previsão solução iria gerar uma previsão nova na 8:00 AM diariamente, em seguida, precisamos para se certificar de que todos os dados que tenham sido recolhidos durante as últimas 24 horas tem sido totalmente penetração até que ponto e tem de par incluam de última hora de dados.

Para realizar esta tarefa, o conjunto de informações da empresa Cortana oferece várias formas para suportar um processo de ingestão fiável de dados. Isto será ainda mais abordado na secção de **implementação** deste documento.

### <a name="data-quality"></a>Qualidade dos dados
A origem de dados não processados que é necessária para efetuar a previsão de pedido de fiável e preciso tem de cumprir alguns critérios de qualidade de dados básico. Apesar de métodos de estatísticos avançados podem ser utilizados para o Adquirente na íntegra para alguns problema de qualidade de dados possível, precisamos ainda para se certificar de que estamos são passagem algumas limiar de qualidade de base de dados quando ingesting novos dados. Aqui estão algumas considerações relativas à qualidade de dados não processados:
-   **Valor em falta** – Isto refere-se para a situação quando específica medida não foram recolhida. O requisito de base aqui é que a taxa de valor em falta deve não ser superior a 10% para qualquer determinado período de tempo. No caso que um único valor não é apresentado-deve ser indicado utilizando um valor predefinido (por exemplo: '9999') e não "0" que pode ser uma medida válida.
-   **Precisão de medida** – o valor real de consumo ou temperatura devem ser registadas com exatidão. Medidas imprecisas vão produzir imprecisas previsões. Normalmente, o erro de medição deve ser inferior a 1% relativamente ao valor verdadeiro.
-   **Tempo de medida** – é necessário que o carimbo de data/hora real os dados recolhidos irão não afastar-se mais de 10 segundos em relação a hora da medição real verdadeiro.
-   A **sincronização** – quando várias origens de dados estão a ser utilizadas (*por exemplo*, consumo e temperatura) podemos tem de garantir que não existem são sem sincronização com a hora problemas entre elas. Isto significa que a diferença entre o carimbo de data/hora recolhida a partir de qualquer duas independentes origens de dados de tempo não deve exceder mais de 10 segundos.
-   A **latência** - como de outros fabricantes referidos acima, **Ingestão de dados**, estamos dependentes de um processo de fluxo de e ingestão fiável de dados. Para controlar que recomendamos tem de garantir que estamos a latência de dados de controlo. Isto é especificado como a diferença de tempo entre o tempo que foi redirecionada a medição real e a hora em que foi carregado para o armazenamento do conjunto de informações da empresa Cortana e está pronto para ser utilizado. Para a carga de curto prazo a previsão a latência total não deve ser maior do que 30 minutos. Para a carga de longo prazo a previsão a latência total não deve ser maior do que 1 dia.

### <a name="data-preparation-and-feature-engineering"></a>Preparação de dados e engenharia de funcionalidade
Assim que os dados não processados tem sido aspirado (consultar **Dados ingestão**) e foi armazenados em segurança, está pronto para ser processada. A fase de preparação de dados que mostra basicamente é tomar os dados não processados e converter (transformará, alterar a forma dos)-lo para um formulário para a fase de modelação. Que podem incluir operações simples, tais como utilizar a coluna de dados não processados tal como está com o valor real correspondente medido, valores padronizados, mais complexas operações, tais como o [tempo de atraso](https://en.wikipedia.org/wiki/Lag_operator)e as outras pessoas. As colunas de dados recentemente criado são referidas como funcionalidades de dados e o processo de gerar estes é referido como engenharia de funcionalidade. No final deste processo seria temos um novo conjunto de dados que tenha sido derivado dos dados não processados e podem ser utilizado para modelação. Além disso, a fase de preparação de dados tem de encarregam-se de valores em falta (consulte a **Qualidade dos dados**) e o Adquirente na íntegra para os mesmos. Em alguns casos, teria também precisamos de normalizar os dados para se certificar de que todos os valores são representados na mesma escala.

Nesta secção que listar podemos algumas das funcionalidades de dados comuns que estão incluídas na energia pedido modelos de previsão.

**Tempo pelo esforço funcionalidades:** Estas funcionalidades estão derivadas dos dados de data/hora. Estes são extraídos e convertidos em categorias funcionalidades, como:
-   Hora do dia – esta é a hora do dia que utiliza valores de 0 a 23
-   Dia da semana – este representa o dia da semana e utiliza valores de 1 (Domingo) a 7 (Sábado)
-   Dia do mês – este representa a data real e podem pegar em valores de 1 a 31
-   Mês do ano – este representa o mês e utiliza valores de 1 (Janeiro) e 12 (Dezembro)
-   Fim de semana – esta é uma funcionalidade de valor binário utiliza os valores de 0 para os dias da semana ou 1 para o fim de semana
-   Férias – esta é uma funcionalidade de valor binário utiliza os valores de 0 para um dia normal ou 1 para um feriado
-   Termos de Fourier – os termos de Fourier são espessuras que derivem do carimbo de hora e são utilizadas para capturar a sazonalidade (ciclos) dos dados. Uma vez que podem temos épocas vários nos nossos dados de precisamos múltiplos termos de Fourier. Por exemplo, valores de pedido poderão ter anuais, semanais e diários épocas/ciclos que irá resultar em termos de Fourier 3.

**Funcionalidades de medida independentes:** As funcionalidades independentes incluem todos os elementos de dados Gostaríamos para utilizar como predictors no nosso modelo. Aqui vamos excluir a funcionalidade dependente que iria precisamos de prever.
-   Funcionalidade de tempo de desfasamento – estas são tempo deslocado para a valores da procura real. Por exemplo, o tempo de desfasamento 1 funcionalidades sem soltar o valor de procura na hora anterior (partindo do princípio dados horários) relativamente ao carimbo de hora atual. Da mesma forma, vamos adicionar tempo de desfasamento 2, atraso 3, *etc*. A combinação real de funcionalidades de tempo de desfasamento que são utilizadas são determinadas durante a fase de modelação pela avaliação dos resultados de modelo.
-   A longo prazo tendência – esta funcionalidade representa o crescimento linear no pedido entre os anos.

**Funcionalidade dependente:** A funcionalidade dependente é a coluna de dados que Gostaríamos nosso modelo de prever. Com [controlada máquina de formação](https://en.wikipedia.org/wiki/Supervised_learning), precisamos de formar pela primeira vez o modelo com as funcionalidades dependentes (que é também conhecido como etiquetas). Isto permite que o modelo saber os padrões dos dados associados a funcionalidade dependente. No pedido de energia de previsão normalmente queremos prever a procura real e, consequentemente, podemos seria utilizá-la como a funcionalidade dependente.

**Processamento de valores em falta:** Durante a fase de preparação de dados, precisamos de determinar a melhor estratégia para gerir os valores em falta. Isto é principalmente feito utilizando os diversos estatística [métodos de imputação de dados](https://en.wikipedia.org/wiki/Imputation_(statistics)). No caso de energia pedido previsão, podemos normalmente imputar valores em falta através da utilização de média móvel a partir de pontos de dados disponíveis anteriores.

**Normalização de bases de dados:** Dados normalização é outro tipo de transformação que é utilizado para trazer todos os dados numéricos, como o pedido de previsão para uma escala de semelhante. Normalmente, isto ajuda a melhorar a exatidão de modelo e a precisão. Vamos faria normalmente fazê-lo dividindo o valor real pelo intervalo de dados.
Isto irá dimensionar o valor original para baixo para um intervalo mais pequeno, normalmente entre -1 e 1.

## <a name="modeling"></a>Modelação
A fase de modelação é onde a conversão dos dados num modelo ocorre o mais. No essencial este processo não existem são avançadas algoritmos que analisar dados de histórico (dados formação), extrair padrões e criar um modelo. Esse modelo pode ser mais tarde utilizado para prever em novos dados que não tem sido utilizados para criar o modelo.

Assim que temos um modelo de fiável trabalhar, vamos, em seguida, utilizá-lo para pontuação novos dados estruturados para incluir as funcionalidades necessárias (X). O processo de pontuação irá tornar utilizar do modelo persistente (objecto da fase de formação) e prever a variável de destino que é representada pelo Ŷ.

### <a name="demand-forecasting-modeling-techniques"></a>Pedido de previsão modelação técnicas
No caso de pedido de previsão oferecemos utilização dos dados de histórico são ordenados por hora. Vamos geralmente fazer referência a dados que inclui a dimensão de tempo como [séries de tempo](https://en.wikipedia.org/wiki/Time_series). É o objetivo no modelação de série de tempo localizar o tempo de tendências relacionadas, sazonalidade; automática-correlação (correlação ao longo do tempo) e elaborar aqueles num modelo.

Nos últimos anos algoritmos avançados foram desenvolvidos para acomodar previsão da série de tempo e para melhorar a exatidão de previsão. Vamos discutir brevemente alguns dos mesmos aqui.

> [AZURE.NOTE] Esta secção não se destina a ser utilizado como uma máquina de aprendizagem e descrição geral de previsão mas preferir como um breve inquérito de modelação técnicas que são utilizadas frequentemente para previsão pedido. Para obter mais informações e material educacional sobre previsão da série de tempo, recomendamos vivamente o livro online [previsão: princípios e exercícios práticos](https://www.otexts.org/book/fpp).

#### <a name="ma-moving-averagehttpswwwotextsorgfpp62"></a>[**MA (média)**](https://www.otexts.org/fpp/6/2)
Média é uma das técnicas analytical primeiros que foi utilizado para previsão da série de tempo e é ainda um dos mais frequentemente utilizado técnicas data de hoje. Também é foundation para mais avançadas técnicas de previsão. Com a média móvel podemos são previsão o ponto de dados seguinte ao calcular a média dos pontos mais recentes K, onde K indica a ordem de média móvel.

A técnica de média móvel tem o efeito de suavização a previsão e, consequentemente, poderão não processar grande bem volatilidade dos dados.

#### <a name="ets-exponential-smoothinghttpswwwotextsorgfpp75"></a>[**ETS (Suavização exponencial)**](https://www.otexts.org/fpp/7/5)
Suavização exponencial (ETS) é uma família de vários métodos que utilizem média ponderada recentes de pontos de dados para poder prever o ponto de dados seguinte. A ideia é atribuir espessuras mais elevadas para valores mais recentes e diminuir gradualmente deste peso para valores medidos mais antigos. Existem várias dos métodos diferentes com este família, processamento de sazonalidade algumas páginas incluem os dados tal como [Holt invernos sazonal método](https://www.otexts.org/fpp/7/5).

Alguns dos seguintes métodos também factor sazonalidade dos dados.

#### <a name="arima-auto-regression-integrated-moving-averagehttpswwwotextsorgfpp8"></a>[**ARIMA (automática regressão integrada mover média)**](https://www.otexts.org/fpp/8)
Automática regressão integrada mover média (ARIMA) é outra família dos métodos que é geralmente utilizada para previsão de séries de tempo. Combina praticamente métodos de regressão automática com média móvel. Métodos de regressão automática utilizam modelos de regressão tirando valores de série de tempo anterior para calcular no ponto da data seguinte. Métodos ARIMA também se aplicam diferenciação métodos que incluam calcular a diferença entre pontos de dados e utilizando essas em vez do valor medido original. Por fim, ARIMA também utiliza as técnicas de média móveis descritos acima. A combinação de todos os dos seguintes métodos de várias formas é o que constrói a família dos métodos ARIMA.

ETS e ARIMA são utilizados frequentemente hoje para previsão de pedido de energia e muitos outros problemas de previsão. Em muitos casos estes valores são combinados em conjunto para a prestação resultados precisos muito.

**Geral múltiplo regressão** Modelos de regressão poderão ser a abordagem de modelação mais importante dentro do domínio de aprendizagem automática e estatísticas. No contexto de séries de tempo utilizamos regressão para calcular os valores futuros (*por exemplo*, de procura). Regressão é uma combinação de linear dos predictors saiba e a espessura (também conhecida como coeficientes) desses predictors durante o processo de formação. O objetivo é produzir uma linha de regressão irá previsão nosso valor previsto. Métodos de regressão são adequados quando a variável de destino é numérica e, consequentemente, também se adequa previsão da série de tempo. Existe uma vasta gama de métodos de regressão, incluindo modelos de regressão muito simples como [Regressão Linear](https://en.wikipedia.org/wiki/Linear_regression) e aqueles mais avançadas, tais como árvores de decisão, [Florestas aleatório](https://en.wikipedia.org/wiki/Random_forest), [Redes nervoso](https://en.wikipedia.org/wiki/Artificial_neural_network)e aumentado árvores de decisão.

Construir a procura de energia previsão como um problema de regressão dá-nos muitas flexibilidade na selecção nossas funcionalidades de dados que podem ser combinadas a partir dos dados de série de tempo de procura real e externos fatores como temperatura. Obter mais informações sobre as funcionalidades seleccionadas são descritos com a funcionalidade de engenharia (consulte **preparação de dados e funcionalidade engenharia**) a secção deste playbook.

A partir do nossa experiência com pós-implementação e implementação do piloto de previsões de pedido de energia, ter encontrámos que os modelos de regressão avançadas que estão disponíveis no Azure ML opções têm tendência para produzir os melhores resultados e oferecemos utilizar dos mesmos.

## <a name="model-evaluation"></a>Avaliação de modelo
Avaliação de modelo tem uma função crítica dentro do **Ciclo de desenvolvimento do modelo**. Neste passo olharmos para validar o modelo e do respectivo desempenho com dados de vida real. Durante o passo de modelação utilizamos uma parte dos dados disponíveis para o modelo de formação. Durante a fase de avaliação, podemos tirar o resto dos dados para testar o modelo. Praticamente significa alimentação dos dados do novo modelo que tem sido reestruturados e que contém as mesmas funcionalidades que o conjunto de dados de formação. No entanto, durante o processo de validação, utilizamos o modelo de prever a variável de destino em vez de fornecer a variável de destino disponíveis. Muitas vezes a referência é feita este processo como modelo pontuação. Estamos seria, em seguida, utilize os valores de destino true e compará-los com aqueles previsto. O objetivo aqui é medir e minimizar o erro de previsão, que significa que a diferença entre as previsões e o valor verdadeiro. Quantificação a medição erro é a chave de uma vez que recomendamos gostaria de otimizar o modelo e validar se o erro é realmente decrescentes. Ajuste o modelo pode ser processada modificando os parâmetros de modelo que controlam o processo de aprendizagem, ou ao adicionar ou remover funcionalidades de dados (designado por [parâmetros varrer](https://channel9.msdn.com/Blogs/Windows-Azure/Data-Science-Series-Building-an-Optimal-Model-With-Parameter-Sweep)). Praticamente Isto significa que poderá precisamos de iteração entre engenharia a funcionalidade de modelação e do modelo de fases de avaliação várias vezes até que estamos a conseguir reduzir o erro ao nível necessário.

É importante que o erro de previsão nunca será zero como de ênfase nunca é um modelo que perfeitamente pode prever cada resultado. No entanto, existe um determinado magnitude de erro que for aceitável pela empresa. Durante o processo de validação, gostaríamos de saber a garantir que o erro de previsão nosso modelo é a nível ou melhor do nível de tolerância de negócio. Por conseguinte, é importante definir o nível do erro máximo no início do ciclo de durante a fase de **Composição de problema** .

### <a name="typical-evaluation-techniques"></a>Técnicas de avaliação típica
Existem várias formas no qual predição erro pode ser medido e quantificado. Nesta secção iremos destacar o debate sobre técnicas de avaliação relevantes para séries de tempo e específico para a procura de energia de previsão.

#### <a name="mapehttpsenwikipediaorgwikimeanabsolutepercentageerror"></a>[**MAPE**](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error)
MAPE designa vermelha erro de percentagem absoluta. Com MAPE podemos são computação a diferença entre cada prevista ponto e o valor real desse ponto. Vamos, em seguida, quantifique as o erro por ponto calculando a proporção de diferença em relação o valor real. No último passo podemos estes valores médios. A fórmula matemática utilizada para MAPE é o seguinte:

![Fórmula de MAPE](media/cortana-analytics-playbook-demand-forecasting-energy/mape-formula.png)
*sempre que um<sub>t</sub> é o valor real, F<sub>t</sub> é o valor previsto e n é o horizonte previsão.*

## <a name="deployment"></a>Implementação
Assim que estamos ter permanente para baixo a fase de modelação e validado o desempenho do modelo que estamos prontas a ser enviada para a fase de implementação. Neste contexto, implementação significa que permita ao cliente consumir o modelo ao executar reais previsões no mesmo em grande escala. O conceito de implementação é chave no Azure ML desde que o nosso objetivo principal é constantemente invocar previsões por oposição a obtenção apenas o conhecimentos aprofundados dos dados de. A fase de implementação é a parte onde podemos ativar o modelo de consumidas em grande escala.

Dentro do contexto de previsão de pedido de energia, nosso objectivo invocar previsões contínuas e periódicos, garantindo que que estão disponíveis para o modelo de dados frescos e que os dados previstos são enviados para o cliente consome.

### <a name="web-services-deployment"></a>Web serviços implementação
O bloco modular com versões principal no Azure ML é o serviço web. Esta é a forma mais eficaz para ativar o consumo de um modelo de aspeto do Office na nuvem. O serviço Web contém o modelo e molda-o com uma [RESTful](http://www.restapitutorial.com/) API (Application Programming Interface). API pode ser utilizada como parte de qualquer código de cliente, conforme ilustrado no diagrama abaixo.

![Vamos e o serviço de implementação consumo](media/cortana-analytics-playbook-demand-forecasting-energy/web-service-deployment-and-consumption.png)

Como podem ser vistos, o serviço web é implementado na nuvem Cortana conjunto de informações da empresa e pode ser chamado ao longo do respectivo ponto final de REST API exposta. Tipo de clientes através de vários domínios diferente pode chamar o serviço através da Web API em simultâneo. Também pode dimensionar o serviço web para suportar milhares de chamadas em simultâneo.

### <a name="a-typical-solution-architecture"></a>Uma arquitetura de solução típica
Quando implementar um pedido de energia previsão solução, estamos interessados em implementar uma solução de ponto a ponto que ultrapassa o serviço web de previsão e facilita o fluxo de dados completa. No momento que podemos invocar uma previsão nova, precisamos de Certifique-se de que o modelo é Moro com as funcionalidades de dados atualizados. Que implica que os dados não processados recentemente recolhidos são constantemente penetração, processados e transformados na funcionalidade necessária configurar no qual o modelo foi criado. Ao mesmo tempo, gostaríamos de fazer tornar os dados previstos disponíveis para o fim utilizado por outros clientes. Um exemplo dados ciclo fluxo (ou pipeline de dados) é ilustrado no diagrama abaixo:

![Procura de energia previsão de fluxo de dados de ponto a ponto](media/cortana-analytics-playbook-demand-forecasting-energy/energy-demand-forecase-end-data-flow.png)

Estes são os passos que executada como parte do ciclo de previsão de pedido de energia:
1.  Milhões de metros dados implementado constantemente estão a gerar dados consumo de energia em tempo real.
2.  Estes dados está a ser recolhidas e carregados num repositório de nuvem (*por exemplo*, BLOBs do Azure).
3.  Antes de serem processados, os dados não processados são agregados a um subestação ou nível regional, tal como foi definido pela empresa.
4.  O processamento de funcionalidade (consulte **preparação de dados e a funcionalidade de processamento**), em seguida, ocorre o mais e produz os dados que são necessários para o modelo de formação ou pontuação – os dados de conjunto de funcionalidade são armazenados numa base de dados (*por exemplo*, SQL Azure).
5.  O serviço novamente formação é chamado de formar novamente o modelo de previsão – que a versão actualizada do modelo é mantida para que possa ser utilizado pelo serviço web pontuação.
6.  O serviço web pontuação é chamado numa agenda que se adequa a frequência de previsão necessária.
7.  Os dados previstos são armazenados numa base de dados que pode ser acedido por um cliente consumo fim.
8.  O cliente de consumo obtém as previsões, aplica-se-lo novamente na grelha e consome-lo em conformidade com o caso de utilização necessários.

É importante ter em atenção que este ciclo inteiro está totalmente automatizado e executa uma agenda. O orchestration inteira deste ciclo dados puder ser processada utilizando ferramentas, como o [Azure fábrica de dados](http://azure.microsoft.com/services/data-factory/).

### <a name="end-to-end-deployment-architecture"></a>Arquitetura de implementação de ponto a ponto
Para poder praticamente implementar uma solução de previsão de pedido de energia no Cortana Intelligence, precisamos de garantir que os componentes necessários estabelecidos e corretamente configurados.

O diagrama seguinte ilustra uma arquitetura de informações da empresa de Cortana com base típica que implementa e orquestra ciclo de fluxo de dados que é descrito acima:

![Arquitetura de implementação de ponto a ponto](media/cortana-analytics-playbook-demand-forecasting-energy/architecture.png)

Para obter mais informações sobre cada um dos componentes e a arquitetura inteira fórum referir-se para o modelo de solução de energia.
