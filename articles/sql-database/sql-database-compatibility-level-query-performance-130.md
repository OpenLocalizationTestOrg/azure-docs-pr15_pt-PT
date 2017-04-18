<properties
    pageTitle="Nível de compatibilidade, como avaliar | Microsoft Azure"
    description="Passos e ferramentas para determinar qual o nível de compatibilidade é mais adequado para a base de dados na base de dados do SQL Azure ou Microsoft SQL Server"
    services="sql-database"
    documentationCenter=""
    authors="alainlissoir"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.devlang="NA"
    ms.tgt_pltfrm="NA"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="alainl"/>


# <a name="improved-query-performance-with-compatibility-level-130-in-azure-sql-database"></a>Melhorado desempenho da consulta com compatibilidade 130 de nível de base de dados do SQL Azure


Base de dados SQL Azure está a ser executado transparente centenas de milhares de bases de dados no número de níveis diferentes de compatibilidade, preservação e garantir a retrocompatibilidade, para a versão correspondente do Microsoft SQL Server para todos os seus clientes!

Por conseguinte, nada impede que os clientes que alteram quaisquer bases de dados existentes para o nível de compatibilidade mais recente a partir do beneficiam o novo otimizador de consulta e as funcionalidades de processador de consulta. Como um lembrete do histórico, o alinhamento de versões SQL para níveis de compatibilidade predefinidos são os seguintes:

- 100: no SQL Server 2008 e do Azure SQL da base de dados V11.
- 110: no SQL Server 2012 e Azure SQL base de dados V11.
- 120: no SQL Server 2014 e Azure SQL base de dados V12.
- 130: no SQL Server 2016 e Azure SQL base de dados V12.


> [AZURE.IMPORTANT] Iniciar no **2016 seg. texto Junho**, base de dados SQL Azure, o nível de compatibilidade predefinido será 130 em vez de 120 para bases de dados **criado recentemente** .
> 
> Bases de dados criadas antes de seg. texto Junho de 2016 serão *não* será afetado e irá manter o respetivo nível de compatibilidade atual (100, 110 ou 120). Bases de dados que migrar a partir da versão de base de dados do Azure SQL que V11 para V12 não terão o respetivo nível de compatibilidade alteradas quer.


Neste artigo, vamos explorar os benefícios de nível de compatibilidade 130 e saber como tirar partido esses benefícios. Vamos endereço os efeitos de lado possíveis sobre o desempenho da consulta para as aplicações de SQL existentes.


## <a name="about-compatibility-level-130"></a>Sobre a compatibilidade nivelar 130


Em primeiro lugar, se pretender saber o nível de compatibilidade atual da base de dados, execute a seguinte instrução de Transact-SQL.


```
SELECT compatibility_level
    FROM sys.databases
    WHERE name = '<YOUR DATABASE_NAME>’;
```


Antes desta alteração ao nível 130 acontece para **recentemente** criado bases de dados, vamos reveja que esta alteração é tudo sobre através de alguns exemplos de consulta muito básica e, consulte o artigo como qualquer pessoa pode beneficiar do mesmo.

Processamento de consultas em bases de dados relacionais pode ser muito complexo e pode levar a muitas ciência do computador e matemática para compreender as opções de estrutura inerentes e comportamentos. Neste documento, o conteúdo foi intencionalmente simplificado para garantir que qualquer pessoa com algumas informações técnicas mínima compreender o impacto da alteração de nível de compatibilidade e determinar como este pode ser vantajoso aplicações.

Vamos fazer com que um breve olhar sobre o que o nível de compatibilidade 130 coloca na tabela.  Pode encontrar mais detalhes ao [Alterar o nível de compatibilidade do base de dados (Transact-SQL)](https://msdn.microsoft.com/library/bb510680.aspx), mas aqui está um breve resumo:

- A operação de inserção de uma declaração inserir selecione pode ser multithreaded ou pode ter um plano paralelo, enquanto antes desta operação foi por tópicos único.
- Memória otimizado tabelas e consultas de variáveis de tabela agora podem ter planos paralelos, enquanto antes desta operação também foi por tópicos único.
- Estatísticas de memória otimizado tabela podem agora ser convertidas e são atualizadas automaticamente. Consulte o artigo [Novidades do motor de base de dados: na memória OLTP](https://msdn.microsoft.com/library/bb510411.aspx#InMemory) para obter mais detalhes.
- Modo de lote v/s o modo de linha altera com índices do arquivo de coluna
  - Ordena numa tabela com um índice de coluna arquivo está no modo de lote.
  - Agregados de sistema baseado em janelas agora funcionam no modo de lote como declarações de TSQL tempo de desfasamento/oportunidade potencial.
  - Trabalhar com consultas no arquivo de coluna as tabelas com várias cláusulas distintas em modo Batch.
  - Consultas executar em DOP = 1 ou com um plano série também executar no modo de lote.
- Por último, melhorias de cardinalidade estimativa são realmente a chegar com o nível de compatibilidade 120, mas para aqueles que está a executar a um nível de compatibilidade inferior (ou seja, 100, ou 110), o comando Mover para compatibilidade nível 130 moldados também estes melhoramentos e estes também podem beneficiar o desempenho da consulta das suas aplicações.


## <a name="practicing-compatibility-level-130"></a>Nível de compatibilidade 130 a praticar


Primeira vamos obter algumas tabelas, índices e dados aleatórios criados para praticar algumas destas funcionalidades novas. Os exemplos de script TSQL podem ser executados em SQL Server 2016 ou em base de dados do SQL Azure. No entanto, quando criar uma base de dados do Azure SQL, certifique-se de que escolhe no mínimo uma base de dados P2 porque precisa, pelo menos, algumas núcleos para permitir o multithreading e, consequentemente, beneficiem das vantagens destas funcionalidades.


```
-- Create a Premium P2 Database in Azure SQL Database

CREATE DATABASE MyTestDB
    (EDITION=’Premium’, SERVICE_OBJECTIVE=’P2′);
GO

-- Create 2 tables with a column store index on
-- the second one (only available on Premium databases)

CREATE TABLE T_source
    (Color varchar(10), c1 bigint, c2 bigint);

CREATE TABLE T_target
    (c1 bigint, c2 bigint);

CREATE CLUSTERED COLUMNSTORE INDEX CCI ON T_target;
GO

-- Insert few rows.

INSERT T_source VALUES
    (‘Blue’, RAND() * 100000, RAND() * 100000),
    (‘Yellow’, RAND() * 100000, RAND() * 100000),
    (‘Red’, RAND() * 100000, RAND() * 100000),
    (‘Green’, RAND() * 100000, RAND() * 100000),
    (‘Black’, RAND() * 100000, RAND() * 100000);

GO 200

INSERT T_source SELECT * FROM T_source;

GO 10
```


Agora, vamos dar uma olhadela a algumas das funcionalidades de processamento de consulta com o nível de compatibilidade 130 a chegar.


## <a name="parallel-insert"></a>Inserir Parallel


Executar as declarações de TSQL abaixo executa a operação de inserir em nível de compatibilidade 120 e 130, que respetivamente executa a operação de inserir num único modelo por tópicos (120) e num modelo de multithreaded (130).


```
-- Parallel INSERT … SELECT … in heap or CCI
-- is available under 130 only

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 120;
GO 

-- The INSERT part is in serial

INSERT t_target WITH (tablock)
    SELECT C1, COUNT(C2) * 10 * RAND()
        FROM T_source
        GROUP BY C1
    OPTION (RECOMPILE);

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130
GO

-- The INSERT part is in parallel

INSERT t_target WITH (tablock)
    SELECT C1, COUNT(C2) * 10 * RAND()
        FROM T_source
        GROUP BY C1
    OPTION (RECOMPILE);

SET STATISTICS XML OFF;
```


Solicitando o valor real o plano de consulta na sua representação gráfica ou o respectivo conteúdo XML, pode determinar quais estimativa de cardinalidade função está em reprodução. Consultar o planos lado a lado no figura 1, pode claramente Vemos que a execução de coluna loja inserir vai a partir de revisão em série no 120 para paralelo no 130. Além disso, tenha em atenção que a alteração do ícone de iteração no plano de 130 com duas setas paralelas, que ilustram o facto desse agora a execução de iteração é na verdade, paralela. Se tiver grandes inserir operações para concluir, paralela execução, ligada para o número de core que tem à sua disposição para a base de dados, será um melhor desempenho; até um vezes 100 mais rápida de cálculo consoante a forma sua situação!


*Figura 1: Inserir operação alterações da série até paralelo com compatibilidade nivelar 130.*


![Figura 1](./media/sql-database-compatibility-level-query-performance-130/figure-1.jpg)


## <a name="serial-batch-mode"></a>Modo série Batch


Mover para o nível de compatibilidade 130 quando está a processar linhas de dados permite da mesma forma, processamento de modo batch. Em primeiro lugar, operações de modo batch só estão disponíveis quando tem um índice de arquivo de coluna no local. Em segundo lugar, um lote de normalmente representa ~ 900 linhas e utiliza uma lógica de código otimizada para CPU multicore, superior débito da memória e diretamente tira partido dos dados comprimidos da loja do coluna sempre que possível. Nestas condições, SQL Server 2016 pode processar ~ 900 linhas em simultâneo, em vez de 1 linha no momento, e consequentemente, o custo total geral da operação de é agora partilhado por todo o lote, reduzindo global de custos por linha. Este valor partilhada de operações combinado com a compressão de arquivo de coluna que mostra basicamente reduz a latência envolvidas na operação de modo lote SELECT. Pode encontrar mais detalhes sobre o arquivo de coluna e lotes de modo a [Columnstore índices guia](https://msdn.microsoft.com/library/gg492088.aspx).


```
-- Serial batch mode execution

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 120;
GO

-- The scan and aggregate are in row mode

SELECT C1, COUNT (C2)
    FROM T_target
    GROUP BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO 

– The scan and aggregate are in batch mode,
-- and force MAXDOP to 1 to show that batch mode
-- also now works in serial mode.

SELECT C1, COUNT(C2)
    FROM T_target
    GROUP BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

SET STATISTICS XML OFF;
```


Visível abaixo, como por observar a consulta planos lado a lado na figura 2, podemos ver que o modo de processamento foi alterado com o nível de compatibilidade e, consequentemente, ao executar completamente as consultas em ambos os nível de compatibilidade, é possível ver que a maior parte das vezes processamento é gasto no modo de linha (86%) em comparação comparado o modo de batch (14%), onde tenham sido processados 2 lotes. Aumentar o conjunto de dados, o benefício aumenta.


*Figura 2: SELECIONE alterações à operação em série para o modo de lote com o nível de compatibilidade 130.*


![Figura 2](./media/sql-database-compatibility-level-query-performance-130/figure-2.jpg)


## <a name="batch-mode-on-sort-execution"></a>Modo de lote execução de ordenação


Semelhante ao que precede, aplicado a uma operação de ordenação, mas a transição a partir do modo de linha (nível de compatibilidade 120) para o modo de lote (nível de compatibilidade 130) melhora o desempenho da operação de ordenação para os motivos do mesmo.


```
-- Batch mode on sort execution

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 120;
GO

-- The scan and aggregate are in row mode

SELECT C1, COUNT(C2)
    FROM T_target
    GROUP BY C1
    ORDER BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

-- The scan and aggregate are in batch mode,
-- and force MAXDOP to 1 to show that batch mode
-- also now works in serial mode.

SELECT C1, COUNT(C2)
    FROM T_target
    GROUP BY C1
    ORDER BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

SET STATISTICS XML OFF;
```


Visível lado a lado no figura 3, podemos ver que a operação de ordenação no modo de linha representa 81% do custo, enquanto o modo de batch representa apenas 19% o custo (respetivamente 81% e 56% no ordenar propriamente dito).


*Figura 3: Operação de ordenação alterações da linha para o modo de lote com o nível de compatibilidade 130.*


![Figura 3](./media/sql-database-compatibility-level-query-performance-130/figure-3.png)


Obviamente, estas amostras só contenham dezenas de milhares de linhas, que é nada quando a opção de dados disponíveis na maioria dos servidores do SQL estes dias. Apenas project estes contra milhões de linhas em vez disso, e, este pode traduzir em vários minutos de execução evitados diariamente pendentes da natureza da sua carga de trabalho.


## <a name="cardinality-estimation-ce-improvements"></a>Melhorias do cardinalidade estimativa (CE)


Introduzidos com o SQL Server 2014, quaisquer bases de dados em execução de um nível de compatibilidade 120 ou acima serão recorrer a estimativa de cardinalidade nova funcionalidade. Basicamente, a estimativa de cardinalidade é a lógica utilizada para determinar como do SQL server será executar uma consulta com base no seu custo estimado. A estimativa é calculada utilizando a introdução de estatísticas associadas a objetos envolvidos nessa consulta. Praticamente, na linha um alto nível, as funções de cardinalidade estimativa são estimativas de contagem de linha juntamente com informações sobre a distribuição dos valores, contagens distintos, e contagens duplicadas constam as tabelas e objetos referenciados na consulta. Introdução estas estimativas mal, pode levar para desnecessária do disco e/s devido concede memória insuficiente (ou seja, TempDB derrames) ou para uma seleção de execução de um plano série sobre a execução de um plano paralelo para atribuir um nome algumas. Conclusão, estimativas incorretas podem levar a uma degradação do desempenho global da execução da consulta. No outro lado, estimativas melhor, estimativas mais exatas, leva a melhor execuções de consulta!

Tal como mencionado antes, otimizações de consulta e estimativas são uma questão complexa, mas se quiser saber mais sobre os planos de consulta e estimador de cardinalidade, pode referir-se para o documento na [Otimizar o planos de consulta com SQL Server 2014 cardinalidade estimador](https://msdn.microsoft.com/library/dn673537.aspx) para um vôo picado mais aprofundado.


## <a name="which-cardinality-estimation-do-you-currently-use"></a>Estimativa quais cardinalidade atualmente utiliza?


Para determinar em que estão a executar o suas consultas estimativa de cardinalidade, vamos apenas utilize os exemplos de consulta abaixo. Tenha em atenção que será executada neste primeiro exemplo em nível de compatibilidade 110, conferir a utilização das funções de cardinalidade estimativa antigas.


```
-- Old CE

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 110;
GO

SET STATISTICS XML ON;

SELECT [c1]
    FROM [dbo].[T_target]
    WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


Assim que a execução estiver concluída, clique na ligação XML e observe as propriedades de iteração a primeira, conforme apresentado abaixo. Tenha em atenção o nome da propriedade denominado CardinalityEstimationModelVersion definida no 70. Não significa que o nível de compatibilidade de base de dados está definido para a versão do SQL Server 7.0 (é definida no 110 como visíveis nas demonstrações TSQL acima), mas o valor 70 simplesmente representa a funcionalidade de cardinalidade estimativa legada disponível desde o SQL Server 7.0, tinha sem revisões principais até 2014 do SQL Server (o que é fornecido com um nível de compatibilidade de 120).


*Figura 4: O CardinalityEstimationModelVersion está definido para 70 ao utilizar um nível de compatibilidade de 110 ou abaixo.*


![Figura 4](./media/sql-database-compatibility-level-query-performance-130/figure-4.png)


Em alternativa, pode alterar o nível de compatibilidade para 130 e desativar a utilização da função cardinalidade estimativa nova através de LEGACY_CARDINALITY_ESTIMATION definido para ON com [ALTER configuração de CONFINADOS base de dados](https://msdn.microsoft.com/library/mt629158.aspx). Isto vai ser exatamente o mesmo que utilizar 110 a partir de um estimativa cardinalidade função ponto de vista, ao utilizar a nível de compatibilidade de processamento de consultas mais recente. Ao fazê-lo, pode beneficiar das funcionalidades chegar com o nível de compatibilidade (ou seja, o modo lote) mais recente de processamento nova consulta, mas ainda depender a funcionalidade de cardinalidade estimativa antiga se for necessário.


```
-- Old CE

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = ON;
GO

SET STATISTICS XML ON;

SELECT [c1]
    FROM [dbo].[T_target]
    WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


Basta passar para o nível de compatibilidade 120 ou 130 permite a nova funcionalidade de cardinalidade estimativa. Neste caso, a predefinição CardinalityEstimationModelVersion será definido, consoante adequado para 120 ou 130 como visíveis em baixo.


```
-- New CE

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = OFF;
GO

SET STATISTICS XML ON;

SELECT [c1]
    FROM [dbo].[T_target]
    WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


*Figura 5: O CardinalityEstimationModelVersion está definido para 130 ao utilizar um nível de compatibilidade de 130.*


![Figura 5](./media/sql-database-compatibility-level-query-performance-130/figure-5.jpg)


## <a name="witnessing-the-cardinality-estimation-differences"></a>Tenham assistido as diferenças de cardinalidade estimativa


Agora, vamos executar um pouco mais complexo que envolvam uma INNER JOIN com uma cláusula WHERE com algumas predicados de consulta e Vejamos a estimativa de contagem de linha a partir da função de cardinalidade estimativa antiga pela primeira vez.


```
-- Old CE row estimate with INNER JOIN and WHERE clause

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = ON;
GO

SET STATISTICS XML ON;

SELECT T.[c2]
    FROM
                   [dbo].[T_source] S
        INNER JOIN [dbo].[T_target] T  ON T.c1=S.c1
    WHERE
        S.[Color] = ‘Red’  AND
        S.[c2] > 2000  AND
        T.[c2] > 2000
    OPTION (RECOMPILE);
GO

SET STATISTICS XML OFF;
```


Executar esta consulta de forma eficaz devolve 200,704 linhas, enquanto a estimativa de linha com a funcionalidade de cardinalidade estimativa antiga afirmações 194,284 linhas. Obviamente, tal como disse antes, estes linha contagem de resultados será também depende com que frequência executou amostras anteriores, que preenche as tabelas de exemplo repetidamente em cada execução. Obviamente, predicados na sua consulta também terá uma influência sobre a estimativa real para além da forma de tabela, o conteúdo, dados e como se estes dados realmente ligam entre si.


*Figura 6: A estimativa de contagem de linha é 194,284 ou 6.000 linhas desativar a partir das 200,704 linhas esperadas.*


![Figura 6](./media/sql-database-compatibility-level-query-performance-130/figure-6.jpg)


Da mesma forma, vamos agora executar a consulta mesmo com a nova funcionalidade de cardinalidade estimativa.


```
-- New CE row estimate with INNER JOIN and WHERE clause

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = OFF;
GO

SET STATISTICS XML ON;

SELECT T.[c2]
    FROM
                   [dbo].[T_source] S
        INNER JOIN [dbo].[T_target] T  ON T.c1=S.c1
    WHERE
        S.[Color] = ‘Red’  AND
        S.[c2] > 2000  AND
        T.[c2] > 2000
    OPTION (RECOMPILE);
GO

SET STATISTICS XML OFF;
```


Consultar a abaixo, agora Vemos que a estimativa de linha é 202,877, ou muito mais perto e superiores a estimativa de cardinalidade antigo.

*Figura 7: A estimativa de contagem de linha é agora 202,877, em vez de 194,284.*


![Figura 7](./media/sql-database-compatibility-level-query-performance-130/figure-7.jpg)


Na realidade, o conjunto de resultados é 200,704 linhas (mas -depende do número de vezes que executar as consultas das amostras anterior, mas ainda mais importante, porque o TSQL utiliza a declaração de rand (), os valores reais devolvidos podem variar do uma executar para o próximo). Por conseguinte, neste exemplo em particular, a estimativa de cardinalidade novo é que uma tarefa na estimar o número de linhas, uma vez que é muito mais próximo 200,704, que 194,284 202,877 de melhor! Apelido, se mudar a cláusula WHERE predicados para igualdade como (em vez de ">" por exemplo), este pode efetuar as estimativas entre antigo e nova função cardinalidade ainda mais diferente, dependendo do que corresponde ao pode obter.

Obviamente, neste caso, a ser ~ 6000 linhas desativar partir da contagem real não representar muitos dados em algumas situações. Agora, transpor esta opção para milhões de linhas em várias tabelas e consultas mais complexas e, por vezes a estimativa pode ser desativada milhões de linhas e, por conseguinte, o risco de recolha de segurança o plano de execução mal ou pedir concede memória insuficiente levando a TempDB derrames e, por isso, mais e/s, são muito mais alto.

Se tiver a oportunidade, exercícios práticos esta comparação com o seu mais típica consultas e conjuntos de dados e ver para si próprio por quanto algumas das novas e antigas estimativas são afetadas, enquanto algumas podem apenas tornar-se mais desativar a partir da realidade ou algumas outras pessoas apenas simplesmente mais perto para a linha real contagens de palavras realmente devolvido nos conjuntos de resultados. -Depende da forma das suas consultas, as características de base de dados do Azure SQL, da natureza e o tamanho dos seus conjuntos de dados e as estatísticas disponíveis sobre os mesmos. Se que acabou de criar a instância de base de dados do Azure SQL, tem o otimizador de consulta criar os seus dados de conhecimento de raiz em vez de reutilizar estatísticas constituídas a anterior consulta é executada. Por isso, as estimativas são muito contextual e quase específicos para cada situação servidor e a aplicação. É um aspecto importante a ter em conta!


## <a name="some-considerations-to-take-into-account"></a>Considerações a ter em conta


Apesar da maioria das cargas de trabalho seriam beneficiem das vantagens do nível de compatibilidade 130, antes de aprovar o nível de compatibilidade para o seu ambiente de produção, que mostra basicamente tem 3 opções:

1. Deslocar-se para o nível de compatibilidade 130 e, consulte o artigo como efetuar coisas. No caso que existam alguns regressões, apenas basta definir nível de compatibilidade para o seu nível original ou em manter 130 e apenas inversa a estimativa de cardinalidade novamente para o modo de legado (tal como é explicado acima, isto individualmente conseguiu resolver o problema).
2. Testar cuidadosamente as aplicações existentes em caso de carga produção semelhantes, ajustar o gráfico e validar o desempenho antes de entrar produção. Em caso de problemas, mesmo que o acima, pode sempre voltar para o nível de compatibilidade original, ou simplesmente inverter a estimativa de cardinalidade novamente para o modo de legado.
3. Como uma opção final e a forma mais recente de endereço estas questões, é tirar partido da loja de consulta. Que é a opção recomendada hoje! Para assistir a análise das suas consultas em compatibilidade nivelar 120 ou abaixo versus 130, não é possível Aconselhamos suficiente para utilizar o arquivo de consulta. Arquivo de consulta está disponível com a versão mais recente do V12 de base de dados do SQL Azure e foi concebida para ajudá-lo com a resolução de problemas de desempenho de consulta. Pense na loja de consulta como um gravador de dados de voo para a base de dados recolher e apresentar informações detalhadas de históricas sobre todas as consultas. Isto simplifica significativamente forensics desempenho reduzindo o tempo para diagnosticar e resolver problemas. Pode encontrar mais informações no [consulta loja: um gravador de dados de voo para a base de dados](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database/).


AT alto nível, se já tiver um conjunto de bases de dados a executar o nível de compatibilidade 120 ou abaixo e plano mover alguns dos mesmos para 130 ou porque a sua carga de trabalho automaticamente aprovisionar novas bases de dados que serão mais cedo ser definido por predefinição para 130, considere as seguintes:

- Antes de mudar para o novo nível de compatibilidade de produção, ativar o arquivo de consulta. Pode referir-se para [alterar o modo de compatibilidade de base de dados e utilizar o arquivo de consulta](https://msdn.microsoft.com/library/bb895281.aspx) para obter mais informações.
- Em seguida, teste críticas todas as cargas de trabalho utilizando representante de dados e consultas de um ambiente de produção gosto e comparar o desempenho ocorreu ao e como denunciado ao arquivo de consulta. Se tiver alguns regressões, pode identificar as consultas incompatíveis com o arquivo de consulta e utilizar o plano Forçar opção a partir do arquivo de consulta (também conhecidos por plano afixação). Neste caso, que definitivamente manter-se com o nível de compatibilidade 130 e utilizar o plano de consulta antigo como sugerido pelo arquivo de consulta.
- Se pretender que tirar partido das novas funcionalidades e capacidades da base de dados do SQL Azure (que está a ser executado o SQL Server 2016), mas é sensíveis a alterações importadas pelo nível de compatibilidade 130, como um último recurso, pode optar por forçar o nível de compatibilidade de volta para o nível que se adequa às sua carga de trabalho utilizando uma instrução ALTER DATABASE. Mas em primeiro lugar, esteja ciente de que o plano de arquivo de consulta afixação opção é a melhor opção porque não utilizar 130 é basicamente manter-se ao nível a funcionalidade de uma versão mais antiga do SQL Server.
- Se tiver multi-inquilino aplicações que abrangem várias bases de dados, poderá ser necessário atualizar a lógica aprovisionamento das bases de dados para se certificar de um nível de compatibilidade consistentes em todas as bases de dados; os antigos e aprovisionados recentemente. O desempenho da aplicação carga de trabalho pode ser sensíveis a maiúsculas e ao facto de algumas bases de dados são executadas em níveis diferentes de compatibilidade e, por conseguinte, consistência nível de compatibilidade em quaisquer bases de dados pode ser necessária para fornecer a mesma experiência aos seus clientes toda a área de. Note que não é uma exigem, depende realmente como a aplicação é afectada pelo nível de compatibilidade.
- Por último, relativamente à estimativa de cardinalidade e, tal como alterar o nível de compatibilidade, antes de prosseguir de produção, recomenda-se para testar a sua carga de trabalho de produção em novas condições para determinar se a aplicação benefícios das melhorias de cardinalidade estimativa.


## <a name="conclusion"></a>Conclusão


Utilizar a base de dados do SQL Azure para beneficiar dos todos os melhoramentos do SQL Server 2016 claramente pode melhorar a sua execuções de consulta. Tal como-é! Obviamente, como qualquer nova funcionalidade, deve ser feita uma avaliação adequada para determinar as condições exatas em qual sua carga de trabalho de base de dados funciona melhor. A experiência mostra a maior parte dos carga de trabalho são esperar que pelo menos executada de forma transparente em nível de compatibilidade 130, enquanto tirar partido da nova consulta processamento de funções e novo cardinalidade estimativa. Que, modo real, disse sempre existem algumas exceções e efetuar conclusão inicial, diligência é uma avaliação importante para determinar quanto pode beneficiar estes melhoramentos. E novamente, o arquivo de consulta pode ser de uma excelente ajudar a efetuar este trabalho!

Como evolução deste SQL Azure, que pode esperar um nível de compatibilidade 140 no futuro. Quando está adequada a hora, podemos será iniciado falar sobre o que este nível de compatibilidade futura 140 moldados, tal como brevemente debatido aqui o nível de compatibilidade 130 é colocá hoje.

Por agora, vamos não se esqueça, começando de 2016 de Junho, base de dados do SQL Azure irá alterar o nível de compatibilidade predefinido de 120 para 130 para bases de dados recentemente criados. Tenha em atenção!


## <a name="references"></a>Referências


- [Novidades do motor de base de dados](https://msdn.microsoft.com/library/bb510411.aspx#InMemory)

- [Blogue: Arquivo de consulta: um gravador de dados de voo para a base de dados, por Borko Novakovic, de 2016 8 de Junho](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database/)

- [Nível de compatibilidade de base de dados ALTER (Transact-SQL)](https://msdn.microsoft.com/library/bb510680.aspx)

- [ALTERAR A CONFIGURAÇÃO DE ÂMBITO DE BASE DE DADOS](https://msdn.microsoft.com/library/mt629158.aspx)

- [Nível de compatibilidade 130 para V12 de base de dados do Azure SQL](https://azure.microsoft.com/updates/compatibility-level-130-for-azure-sql-database-v12/)

- [Otimizar a sua consulta planos com o SQL Server estimador de cardinalidade de 2014](https://msdn.microsoft.com/library/dn673537.aspx)

- [Guia de índices Columnstore](https://msdn.microsoft.com/library/gg492088.aspx)

- [Blogue: Melhorada desempenho da consulta com o nível de compatibilidade 130 na base de dados do Azure SQL, por Alain Lissoir, de 2016 6 de Maio](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/05/06/improved-query-performance-with-compatibility-level-130-in-azure-sql-database/)



<!--
Improved Query Performance with Compatibility Level 130 in Azure SQL Database

May 6, 2016 by Alain Lissoir (AlainL), on GitHub 'alainlissoir'.

https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/05/06/improved-query-performance-with-compatibility-level-130-in-azure-sql-database/

..... Now, above.
....................
..... Soon, below?

CAPS / MSDN ideally, but instead on ACom:
.. # Assess effects of latest compatibility level on query performance, how to

sql-database-compatibility-level-query-performance-130.md

genemi = MightyPen , 2016-05-20  Friday  17:00pm
-->
