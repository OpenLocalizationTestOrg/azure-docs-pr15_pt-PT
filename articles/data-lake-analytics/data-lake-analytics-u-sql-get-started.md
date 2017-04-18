<properties
   pageTitle="Desenvolver scripts de U SQL utilizando ferramentas de Lake de dados para o Visual Studio | Azure"
   description="Saiba como instalar ferramentas de Lake de dados para Visual Studio, como desenvolver e scripts de teste U-SQL. "
   services="data-lake-analytics"
   documentationCenter=""
   authors="edmacauley"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="tutorial-get-started-with-azure-data-lake-analytics-u-sql-language"></a>Tutorial: Introdução ao idioma do Azure dados Lake Analytics U-SQL

U SQL está num idioma que une os benefícios da SQL com a potência modelar do seu próprio código para processar todos os dados em qualquer escala. Capacidade de consulta distribuída dimensionáveis U-SQL permite-lhe eficazmente analisar os dados na loja e através de stores relacionais como base de dados do SQL Azure.  Permite-lhe para o processo de dados não estruturados aplicando esquema no ler, inserir lógica personalizada e de UDF e incluem expansão para ativar o controlo ajustar detalhado sobre como executar na escala. Para saber mais sobre a filosofia estrutura atrás U SQL, consulte este [Visual Studio mensagem no blogue](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

Existem algumas diferenças a partir do ANSI SQL ou T-SQL. Por exemplo, o respetivo palavras-chave como SELECT tem de ser em maiúsculas.

-Se o idioma de sistema e expressão tipo dentro cláusulas select, onde predicados etc estão no c#.
Isto significa que os tipos de dados são os tipos de c# e c# nulo semântica de utilizar os tipos de dados e as operações de comparação dentro de um predicado siga c# sintaxe (por exemplo, um = = "foo").  Isto significa também, que os valores são objetos de .NET completos, permitindo-lhe utilizar facilmente qualquer método para trabalhar com no objeto (ex "f the the your". Split(' ')).

Para mais informações, consulte o artigo [Referência de U-SQL](http://go.microsoft.com/fwlink/p/?LinkId=691348).

###<a name="prerequisites"></a>Pré-requisitos

Tem de concluir [Tutorial: desenvolver scripts de U SQL utilizando ferramentas de Lake de dados para o Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).

Tutorial, executou uma tarefa de análise de Lake de dados com o seguinte script U SQL:

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO "/output/SearchLog-first-u-sql.csv"
    USING Outputters.Csv();

Este script não tem qualquer passos de transformação. Lê a partir de ficheiro de origem denominado **SearchLog.tsv**, schematizes-lo e exporta o conjunto de linhas novamente num ficheiro denominado **SearchLog primeiro-u sql.csv**.

Repare que o ponto de interrogação ao lado de tipo de dados do campo Duração. Isto significa que o campo Duração dever nulo.

Alguns conceitos e palavras-chave que se encontram no script:

- **Variáveis de conjunto de linhas**: cada expressão da consulta que gera um conjunto de linhas pode ser atribuído a uma variável. U SQL segue o T-SQL variável nomenclatura padrão, por exemplo, **@searchlog** no script.
    Tenha em atenção que a atribuição não forçar execução. Nomes a expressão meramente e fornece-lhe a capacidade de câmara expressões mais complexas.
- **EXTRAIR** dá-lhe a capacidade de definir um esquema de leitura. Esquema de é especificado por um nome de coluna e c# par de type nome por coluna. Utiliza uma chamados **Extractor**, por exemplo, **Extractors.Tsv()** para extrair tsv ficheiros. Pode desenvolver purgadores personalizados.
- **Saída** leva-o até um conjunto de linhas e serializes-lo. O Outputters.Csv() de saída de um ficheiro separado por vírgulas para a localização especificada. Também pode desenvolver Outputters personalizados.
- Repare que os duas caminhos são caminhos relativos. Também pode utilizar caminhos absolutos.  Por exemplo

        adl://<ADLStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv

    Tem de utilizar o caminho absoluto para aceder aos ficheiros nas contas ligadas armazenamento.  A sintaxe para os ficheiros guardados na conta de armazenamento do Windows Azure associada é:

        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE] Azure Blob contentor com blobs públicos ou permissões de acesso de contentores público atualmente não são suportadas.

## <a name="use-scalar-variables"></a>Utilizar variáveis escalares

Também pode utilizar variáveis escalares para simplificar a manutenção de script. Também pode ser escrito o script U SQL anterior como o seguinte procedimento:

    DECLARE @in  string = "/Samples/Data/SearchLog.tsv";
    DECLARE @out string = "/output/SearchLog-scalar-variables.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM @in
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO @out
        USING Outputters.Csv();

## <a name="transform-rowsets"></a>Transformar conjuntos de linhas

Utilize a **SELECIONAR** para transformar conjuntos de linhas:

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-rowsets.csv"
        USING Outputters.Csv();

A cláusula WHERE utiliza [c# expressão booleana](https://msdn.microsoft.com/library/6a71f45d.aspx). Pode utilizar a linguagem da expressão c# fazer o seu próprio expressões e funções. Ainda pode executar uma filtragem mais complexa combinando-los com conjunções lógicas (e) e disjunctions (ORs).

O script seguinte utiliza o método de DateTime.Parse() e conjunção.

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    @rs1 =
        SELECT Start, Region, Duration
        FROM @rs1
        WHERE Start >= DateTime.Parse("2012/02/16") AND Start <= DateTime.Parse("2012/02/17");

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-datatime.csv"
        USING Outputters.Csv();

Repare que a segunda consulta está a funcionar com o resultado do primeiro conjunto de linhas, por conseguinte, o resultado é uma composição dos dois filtros. Também pode reutilizar um nome de variável e os nomes estão limitadas ao lexically.

## <a name="aggregate-rowsets"></a>Agregados conjuntos de linhas

U SQL fornece-lhe com familiares **ORDER BY**, **Agrupar por** e agregações.

A seguinte consulta localiza a duração total por região e exporta, em seguida, na parte superior 5 durações por ordem.

U SQL conjuntos de linhas não preservar a respetiva ordem para a consulta seguinte. Assim sendo, para encomendar um resultado, terá de adicionar ORDER BY a declaração de saída, conforme apresentado abaixo:

    DECLARE @outpref string = "/output/Searchlog-aggregation";
    DECLARE @out1    string = @outpref+"_agg.csv";
    DECLARE @out2    string = @outpref+"_top5agg.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region;

    @res =
    SELECT *
    FROM @rs1
    ORDER BY TotalDuration DESC
    FETCH 5 ROWS;

    OUTPUT @rs1
        TO @out1
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();
    OUTPUT @res
        TO @out2
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

Cláusula ORDER BY U SQL tem de ser combinado com a cláusula obtenção numa expressão de SELECIONAR.

Cláusula U SQL tendo pode ser utilizada para restringir a saída a grupos que satisfaçam a condição HAVING:

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/Searchlog-having.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

## <a name="join-data"></a>Aderir a dados

U SQL fornece operadores comuns de associação como associação interna, associação externa esquerda/direita/inteiro, JOIN SEMITRANSPARENTE, para participar, não só em tabelas, mas qualquer conjuntos de linhas (mesmo aqueles produzidos a partir de ficheiros).

O seguinte script associa searchlog com um registo de impressão anúncio e dá-nos anúncios para a cadeia de consulta para uma determinada data.

    @adlog =
        EXTRACT UserId int,
                Ad string,
                Clicked int
        FROM "/Samples/Data/AdsLog.tsv"
        USING Extractors.Tsv();

    @join =
        SELECT a.Ad, s.Query, s.Start AS Date
        FROM @adlog AS a JOIN <insert your DB name>.dbo.SearchLog1 AS s
                        ON a.UserId == s.UserId
        WHERE a.Clicked == 1;

    OUTPUT @join   
        TO "/output/Searchlog-join.csv"
        USING Outputters.Csv();


U SQL suporta apenas a sintaxe de associação compatível com ANSI: predicado Rowset1 associação Rowset2 Sucessivamente. A sintaxe antiga do Rowset1 a partir de, predicado Rowset2 onde não é suportada.
O predicado numa associação tem de ser uma associação de igualdade e sem expressão. Se pretender utilizar uma expressão, adicione-a cláusula select de um anterior conjunto de linhas. Se quiser fazer uma comparação diferente, pode movê-lo para a cláusula WHERE.


## <a name="create-databases-table-valued-functions-views-and-tables"></a>Criar bases de dados, funções de valor de tabela, vistas e tabelas

U SQL permite-lhe utilizar os dados no contexto de uma base de dados e o esquema. Por isso, não tem de sempre ler ou escrever aos ficheiros.

Todos os scripts U-SQL é executado com uma base de dados predefinida (mestre) e o esquema predefinido (DBO) como o contexto predefinido. Pode criar a sua própria base de dados e/ou esquema. Para alterar o contexto, utilize a declaração de **utilizar** para alterar o contexto.


### <a name="create-a-table-valued-function-tvf"></a>Criar uma função de valor de tabela (TVF)

Em script de U SQL anterior, repetidos o utilizar EXTRAIR de leitura a partir do mesmo ficheiro de origem. A função de valor de tabela U-SQL permite-lhe incorporar os dados para utilização futura.   

O script seguinte cria uma TVF designado *Searchlog()* na base de dados predefinida e o esquema:

    DROP FUNCTION IF EXISTS Searchlog;

    CREATE FUNCTION Searchlog()
    RETURNS @searchlog TABLE
    (
                UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
    )
    AS BEGIN
    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();
    RETURN;
    END;

O seguinte script mostra-lhe como utilizar TVF definida no script anterior:

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM Searchlog() AS S
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/SerachLog-use-tvf.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

### <a name="create-views"></a>Criar vistas

Se tiver apenas uma expressão de consulta que pretende abstract e não pretende que seja parametrizá-la, pode criar uma vista em vez de uma função de valor de tabela.

O script seguinte cria uma vista denominada *SearchlogView* na base de dados predefinida e esquema:

    DROP VIEW IF EXISTS SearchlogView;

    CREATE VIEW SearchlogView AS  
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

O script seguinte demonstra a utilizar a vista definida:

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM SearchlogView
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/Searchlog-use-view.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

### <a name="create-tables"></a>Criar tabelas

Semelhante à tabela de base de dados relacionais, U SQL permite-lhe criar uma tabela com um esquema predefinido ou criar uma tabela e inferir o esquema da consulta que preenche a tabela (também conhecido como criar tabela como SELECIONE ou CTAS).

O seguinte script de criar uma base de dados e duas tabelas:

    DROP DATABASE IF EXISTS SearchLogDb;
    CREATE DATABASE SeachLogDb
    USE DATABASE SearchLogDb;

    DROP TABLE IF EXISTS SearchLog1;
    DROP TABLE IF EXISTS SearchLog2;

    CREATE TABLE SearchLog1 (
                UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string,

                INDEX sl_idx CLUSTERED (UserId ASC)
                    PARTITIONED BY HASH (UserId)
    );

    INSERT INTO SearchLog1 SELECT * FROM master.dbo.Searchlog() AS s;

    CREATE TABLE SearchLog2(
        INDEX sl_idx CLUSTERED (UserId ASC)
                PARTITIONED BY HASH (UserId)
    ) AS SELECT * FROM master.dbo.Searchlog() AS S; // You can use EXTRACT or SELECT here


### <a name="query-tables"></a>Tabelas de consulta

Pode consultar as tabelas (criadas no script anterior) da mesma forma como consulta sobre os ficheiros de dados. Em vez de criar um conjunto de linhas utilizando EXTRAIR, pode agora pode apenas referir-se para o nome da tabela.

O script de transformação que utilizou anteriormente é modificado a leitura das tabelas:

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM SearchLogDb.dbo.SearchLog2
    GROUP BY Region;

    @res =
        SELECT *
        FROM @rs1
        ORDER BY TotalDuration DESC
        FETCH 5 ROWS;

    OUTPUT @res
        TO "/output/Searchlog-query-table.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

Tenha em atenção que atualmente não pode executar um seleccione numa tabela no mesmo script como o script onde criar nessa tabela.


##<a name="conclusion"></a>Conclusão

O que está incluído no tutorial é apenas uma pequena parte de U-SQL. Devido ao âmbito neste tutorial,-não abrange tudo, tais como:

- Utilizar CROSS aplicam-se para descompactar partes de cadeias, matrizes e mapas para linhas.
- Trabalhar com partições conjuntos de dados (conjuntos de ficheiros e tabelas com partições).
- Desenvolva operadores de definidas pelo utilizador, como purgadores, outputters, processadores, directórios definidos pelo utilizador no c#.
- Utilize funções de sistema baseado em janelas U-SQL.
- Gerir o código U SQL com vistas, funções de valor de tabela e procedimentos armazenados.
- Execute código personalizado arbitrário no seu nós de processamento.
- Ligar a bases de dados do SQL Azure e consultas federate ao longo de-los e os dados do U SQL e Azure dados Lake.

## <a name="see-also"></a>Consulte também

- [Descrição geral da análise Lake de dados do Microsoft Azure](data-lake-analytics-overview.md)
- [Desenvolver scripts de U SQL utilizando ferramentas de Lake de dados para o Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Utilizar funções de janela de U SQL para trabalhos de análise de Lake de dados do Azure](data-lake-analytics-use-window-functions.md)
- [Monitorizar e resolver problemas de tarefas do Azure dados Lake Analytics através do Portal do Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

## <a name="let-us-know-what-you-think"></a>Saber diga-na sua opinião

- [Submeter um pedido de funcionalidade](http://aka.ms/adlafeedback)
- [Obter ajuda nos fóruns do](http://aka.ms/adlaforums)
- [Fornecer comentários sobre U SQL](http://aka.ms/usqldiscuss)
