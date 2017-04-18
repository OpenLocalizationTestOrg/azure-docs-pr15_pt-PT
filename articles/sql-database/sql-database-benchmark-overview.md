<properties
    pageTitle="Descrição geral de referência de base de dados SQL Azure"
    description="Este tópico descreve referência de base de dados do SQL Azure utilizados para medir o desempenho da base de dados do SQL Azure."
    services="sql-database"
    documentationCenter="na"
    authors="CarlRabeler"
    manager="jhubbard"
    editor="monicar" />


<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="06/21/2016"
    ms.author="carlrab" />

# <a name="azure-sql-database-benchmark-overview"></a>Descrição geral de referência de base de dados SQL Azure

## <a name="overview"></a>Descrição geral
Base de dados do Microsoft Azure SQL oferece três [camadas de serviços](sql-database-service-tiers.md) com vários níveis de desempenho. Cada nível de desempenho disponibiliza um conjunto crescente de recursos ou «potência», concebido para entregar débito superior de cada vez.

É importante conseguir quantifique as como potência crescente de cada nível de desempenho converte em desempenho bases de dados maior. Para efetuar este Microsoft tenha desenvolvido a referência de base de dados de SQL Azure (ASDB). O valor de referência exercícios uma mistura de operações básicas que se encontram no todos os OLTP das cargas de trabalho. Vamos medir o débito atingido para bases de dados a ser executada em cada nível de desempenho.

Os recursos e potência de cada nível de camadas e o desempenho do serviço são expressa em termos de [Unidades da transação da base de dados (DTUs)](sql-database-technical-overview.md#understand-dtus). DTUs fornece uma maneira para descrever a capacidade de um nível de desempenho com base numa medida mistura de CPU, memória, relativa e leitura e escrita taxas disponibilizadas pelos cada nível de desempenho. Duplicar a classificação de DTU de uma base de dados equivale a duplicar o poder de base de dados. O valor de referência permite-nos avaliar o impacto no desempenho de base de dados do power crescente disponibilizada pelos cada nível de desempenho ao exercício operações de base de dados reais, enquanto dimensionamento tamanho da base de dados, número de utilizadores e taxas da transação proporcionalmente os recursos fornecidos para a base de dados.

Por expressar o débito da camada serviço básico utilizando as transações por hora, a camada de serviço padrão, utilizando as transações por minuto e a camada de serviço Premium utilizando as transações por segundo, torna mais fácil relacionar rapidamente o desempenho potenciais de cada camada de serviço para os requisitos de uma aplicação.

## <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Resultados de testes de referência correlação reais da base de dados de desempenho
É importante compreender que ASDB, como todos os avaliações, é representativas e indicativo apenas. As taxas da transação atingidas com a aplicação de teste de referência não serão os mesmos como aqueles que poderá ser realizado com outras aplicações. O valor de referência inclui uma coleção da transação diferentes tipos de executar relativamente a um esquema que contém um intervalo de tabelas e tipos de dados. Enquanto o teste de referência exercícios as mesmas operações básicas que são comuns a todos os OLTP das cargas de trabalho, este não representar qualquer classe específica da base de dados ou da aplicação. O objetivo da referência é fornecer um guia razoável para o desempenho relativo de uma base de dados que pode ser esperado quando se escala para cima ou para baixo entre níveis de desempenho. Na realidade, bases de dados são de tamanhos diferentes e complexidade, encontrar diferentes combinações de cargas de trabalho em irão responder formas diferentes. Por exemplo, uma aplicação com muitos IO poderá visitas limiares IO mais cedo ou uma aplicação de CPU-a com um grau elevado poderá visitas limites de CPU mais cedo. Não há garantias que irão Dimensionar quaisquer bases de dados específico da mesma forma como o valor de referência em aumentar carga.

O valor de referência e a sua metodologia são descritos detalhadamente mais abaixo.

## <a name="benchmark-summary"></a>Resumo de teste de referência
ASDB meça o desempenho de uma mistura de operações de base de dados básicas que ocorram com mais frequência numa única transação da online processamento das cargas de trabalho (OLTP). Apesar do teste de referência destina-se com a nuvem computação em ideias, o esquema da base de dados, de população de dados e as transações foram concebidas para ser ficarem representativas dos elementos básicos mais frequentemente utilizados em das cargas de trabalho OLTP.

## <a name="schema"></a>Esquema
Esquema de destina-se de ter o suficiente variedade e complexidade para suportar uma vasta gama de operações. Execuções relativamente a uma base de dados composto por seis tabelas. As tabelas estão divididos em três categorias: tamanho fixo, dimensionamento e, em crescimento. Existem duas tabelas de tamanho fixo; três tabelas dimensionamento; e uma tabela de crescimento. Tamanho fixo tabelas têm um número constante de linhas. Dimensionamento tabelas têm de cardinalidade que é proporcional desempenho bases de dados, mas não altera durante o teste de referência. A tabela crescente é dimensionada como uma tabela de dimensionamento no carregamento inicial, mas, em seguida, as alterações de cardinalidade durante a execução o teste de referência, tal como linhas são inseridas e eliminadas.

O esquema inclui uma mistura de tipos de dados, incluindo o número inteiro, numérico, caráter e data/hora. O esquema inclui chaves primárias e secundárias, mas não quaisquer chaves externas – ou seja, não existem sem restrições de integridade referencial entre tabelas.

Um programa de geração dados gera os dados para a base de dados inicial. Dados numéricos e de número inteiro, são gerados com diversas estratégias. Em alguns casos, aleatoriamente valores são distribuídos ao longo de um intervalo. Em outros casos, um conjunto de valores é aleatoriamente permutado para se certificar de que é mantida uma distribuição específica. Campos de texto são gerados a partir de uma lista de palavras para produzir dados apelativa reais ponderada.

A base de dados é dimensionada com base em "fator de escala". Fator de escala (abreviado como IC) determina a cardinalidade do dimensionamento e em crescimento tabelas. Conforme descrito abaixo na secção utilizadores e Pacing, o tamanho da base de dados, número de utilizadores e o desempenho máximo todos Dimensionar proporcionalmente entre si.

## <a name="transactions"></a>Transações
A carga de trabalho é composta por tipos da transação nove, conforme apresentado na tabela abaixo. Cada transação foi concebida para realçar um determinado conjunto de caraterísticas de sistema na base de dados motor e sistema de hardware, com a opção Alto contraste das outras operações. Esta abordagem facilita a avaliar o impacto das diferentes componentes para o desempenho geral. Por exemplo, a operação "Leitura grossa" produz um número significativo de operações de leitura a partir do disco.

| Tipo da transação | Descrição |
|---|---|
| Ler Lite | SELECIONE; na memória; só de leitura |
| Média de leitura | SELECIONE; principalmente na memória; só de leitura |
| Grossa de leitura | SELECIONE; principalmente não na memória; só de leitura |
| Atualização Lite | ACTUALIZAR; na memória; leitura e escrita |
| Atualizar grossa | ACTUALIZAR; principalmente não na memória; leitura e escrita |
| Inserir Lite | INSERIR; na memória; leitura e escrita |
| Inserir grossa | INSERIR; principalmente não na memória; leitura e escrita |
| Eliminar | ELIMINAR; mistura de na memória e não na memória; leitura e escrita |
| Grossa CPU | SELECIONE; na memória; relativamente CPU sobrecarga; só de leitura |

## <a name="workload-mix"></a>Mistura de carga de trabalho
Transações são selecionadas aleatoriamente a partir de uma distribuição ponderada com a seguinte misturar geral. A mistura global tem um rácio de leitura/escrita cerca de 2:1.

| Tipo da transação | % de Mix |
|---|---|
| Ler Lite | 35 |
| Média de leitura | 20 |
| Grossa de leitura | 5 |
| Atualização Lite | 20 |
| Atualizar grossa | 3 |
| Inserir Lite | 3 |
| Inserir grossa | 2 |
| Eliminar | 2 |
| Grossa CPU | 10 |

## <a name="users-and-pacing"></a>Utilizadores e caminhar a passo
A carga de trabalho de referência é condicionada a partir de uma ferramenta que submete transações através de um conjunto de ligações para simular o comportamento de um número de utilizadores em simultâneo. Apesar de todas as ligações e transações estiverem máquina gerada, para simplificar podemos referem-se a estas ligações como "utilizadores". Apesar de cada utilizador funciona independentemente dos existentes dela todos os outros utilizadores, todos os utilizadores efetuar o mesmo ciclo de passos apresentado abaixo:

1. Estabelecer uma ligação de base de dados.
2. Repita até sinalizado para sair:
    - Selecione uma transação aleatoriamente (a partir de uma distribuição ponderada).
    - Efetuar a transação selecionada e medir o tempo de resposta.
    - Aguarde para um atraso pacing.
3. Feche a ligação de base de dados.
4. Sair.

O atraso pacing (no passo 2c) está seleccionado aleatoriamente, mas com uma distribuição que tem uma média da segunda 1.0. Portanto, cada utilizador em média, pode, gerar no máximo uma transação por segundo.

## <a name="scaling-rules"></a>Regras de dimensionamento
O número de utilizadores é determinado pelo tamanho da base de dados (em unidades de escala fator). Existe um utilizador para todas as unidades de escala fator cinco. O atraso pacing, devido a um utilizador pode gerar no máximo uma transação por segundo, em média.

Por exemplo, um escala-fator de 500 (IC = 500) base de dados terá 100 utilizadores e pode obter uma taxa máxima de 100 TP. Conduzir uma TP superior frequência necessita de mais utilizadores e uma base de dados maior.

A tabela abaixo mostra o número de utilizadores realmente sofrido para cada nível de camadas e o desempenho do serviço.

| Camadas de serviço (nível de desempenho) | Utilizadores | Tamanho da base de dados |
|---|---|---|
| Básicas | 5 | 720 MB |
| Padrão (S0) | 10 | 1 GB |
| Padrão (S1) | 20 | 2.1 GB |
| Padrão (S2) | 50 | 7.1 GB |
| Premium (P1) | 100 | 14 GB |
| Premium (P2) | 200 | 28 GB |
| Premium (P6/P3) | 800 | 114 GB |

## <a name="measurement-duration"></a>Duração de medida
Uma referência válida executar requer a duração de uma medida de estado estável pelo menos uma hora.

## <a name="metrics"></a>Métricas
As teclas métricas na referência são tempo débito e a resposta.

- Débito é medida do desempenho essenciais no teste de referência. Débito é comunicado em transações por unidade-de-time, contando todos os tipos da transação.
- Tempo de resposta é uma medida de previsibilidade de desempenho. A restrição de tempo de resposta varia de acordo com a classe de serviço, com as classes superiores do serviço está a ter um requisito de tempo de resposta mais restritivas, conforme apresentado abaixo.

| Categoria do serviço  | Débito medida | Requisito de hora de resposta |
|---|---|---|
| Premium | Transações por segundo | 95. º percentil na 0,5 segundos |
| Padrão | Transações por minuto | 90. º percentil em segundos 1.0 |
| Básicas | Transações por hora | 80. º percentil em segundos 2.0 |

## <a name="conclusion"></a>Conclusão
Referência de base de dados do SQL Azure meça o desempenho relativo da base de dados do SQL Azure a executar o intervalo de camadas de serviços disponíveis e níveis de desempenho. O valor de referência exercícios uma mistura de operações de base de dados básicas que ocorram com mais frequência numa única transação da online processamento das cargas de trabalho (OLTP). Por medir o desempenho real, o valor de referência fornece uma avaliação mais importantes do impacto no débito de alterar o nível de desempenho que é possível listando apenas os recursos fornecidos pela cada nível como velocidade, o tamanho da memória e IOPS CPU. No futuro, podemos irão continuar a evoluir o teste de referência para alargar o âmbito e expandir os dados fornecidos.

## <a name="resources"></a>Recursos
[Introdução à base de dados SQL](sql-database-technical-overview.md)

[Camadas de serviços e níveis de desempenho](sql-database-service-tiers.md)

[Orientação de desempenho para bases de dados simples](sql-database-performance-guidance.md)
