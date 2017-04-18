<properties
   pageTitle="Analisar os registos de Web site utilizando a análise do Azure dados Lake | Azure"
   description="Saiba como analisar os registos de Web site utilizando a análise de dados Lake. "
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

# <a name="tutorial-analyze-website-logs-using-azure-data-lake-analytics"></a>Tutorial: Analisar registos de Web site utilizando a análise do Azure dados Lake

Saiba como analisar registos de Web site utilizando a análise de dados Lake, sobretudo no descobrir que referências tiveram erros quando que tentaram visite o Web site.

>[AZURE.NOTE] Se apenas quiser ver a aplicação a trabalhar, guarda tempo até a [tutoriais interativos utilização Azure dados Lake Analytics](data-lake-analytics-use-interactive-tutorials.md). Neste tutorial é baseado no mesmo cenário e o mesmo código. É o objetivo deste tutorial dar-lhe os programadores a experiência de criação e execução de uma aplicação de análise de Lake dados a partir do fim para fim.

## <a name="prerequisites"></a>Pré-requisitos:

- O **visual Studio 2015, Visual Studio 2013 atualizar 4, ou Visual Studio 2012http com Visual C++ instalado**.
- **Microsoft Azure SDK para .NET versão 2.5 ou acima**.  Instalá-lo a utilizar o [instalador do plataforma Web](http://www.microsoft.com/web/downloads/platform.aspx).
- **[Ferramentas de dados Lake para Visual Studio](http://aka.ms/adltoolsvs)**.

    Quando estiver instalada dados Lake ferramentas para o Visual Studio, verá um menu **Dados Lake** no Visual Studio:

    ![Menu de U SQL Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-menu.png)

- **Dados de conhecimento básicos de Lake a análise de dados e as ferramentas de Lake de dados para Visual Studio**. Para começar a utilizar, consulte o artigo:

    - [Introdução ao Azure dados Lake Analytics através do Portal do Azure](data-lake-analytics-get-started-portal.md).
    - [Script de desenvolver U-SQL utilizando ferramentas de dados Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).

- **Uma conta de análise de Lake de dados.**  Consulte o artigo [criar uma conta Azure dados Lake Analytics](data-lake-analytics-get-started-portal.md#create_adl_analytics_account).

    As ferramentas de Lake dados não suporta a criação de contas de análise de Lake de dados.  Por isso, tem de criá-lo utilizando o Portal do Azure, Azure PowerShell, .NET SDK ou clip de Azure.
- **Carregue os dados de exemplo para a conta de análise de Lake de dados.** Consulte o artigo [SearchLog.tsv carregar para a conta de armazenamento de Lake dados predefinida](data-lake-analytics-get-started-portal.md#update-data-to-the-default-adl-storage-account).

    Para executar uma tarefa de dados Lake Analytics, terá de alguns dados. Apesar das ferramentas de Lake dados suportar o carregamento dos dados, que irá utilizar o portal para carregar os dados de exemplo para facilitar este tutorial seguir.

## <a name="connect-to-azure"></a>Ligar ao Azure

Antes de poder criar e testar qualquer scripts U SQL, primeiro tem de ligar ao Azure.

**Para ligar a dados Lake Analytics**

1. Abrir o Visual Studio.
2. No menu de **Lake de dados** , clique em **Opções e definições**.
4. Clique em **Iniciar sessão**ou **Alterar utilizador** se alguém tem sessão iniciada no e siga as instruções.
5. Clique em **OK** para fechar a caixa de diálogo Opções e definições.

**Para procurar as suas contas de dados Lake Analytics**

1. A partir do Visual Studio, abra o **Explorador de servidor** , prima **CTRL + ALT + S**.
2. A partir do **Explorador do servidor**, expanda **Azure**e, em seguida, expanda **Lake a análise de dados**. Deve ver uma lista das suas contas de dados Lake Analytics se existirem qualquer. Não é possível criar contas de análise de Lake dados a partir do studio. Para criar uma conta, consulte o artigo [Introdução ao Azure dados Lake Analytics através do Portal do Azure](data-lake-analytics-get-started-portal.md) ou [Introdução ao Azure dados Lake Analytics através do Azure PowerShell](data-lake-analytics-get-started-powershell.md).

## <a name="develop-u-sql-application"></a>Desenvolver a aplicação de U SQL

Uma aplicação de U SQL é principalmente um script U-SQL. Para saber mais sobre U SQL, consulte o artigo [Introdução ao U-SQL](data-lake-analytics-u-sql-get-started.md).

Pode adicionar os operadores definidos pelo utilizador de adição para a aplicação.  Para mais informações, consulte o artigo [operadores para tarefas Lake a análise de dados definidos pelo utilizador de desenvolver U-SQL](data-lake-analytics-u-sql-develop-user-defined-operators.md).

**Para criar e submeter uma tarefa de dados Lake Analytics**

1. No menu **ficheiro** , clique em **Novo**e, em seguida, clique em **projeto**.
2. Selecione o tipo de projecto U-SQL.

    ![novo projeto do Visual Studio de U SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)

3. Clique em **OK**. Visual studio cria uma solução com um ficheiro de Script.usql.
4. Introduza o seguinte script para o ficheiro de Script.usql:

        // Create a database for easy reuse, so you don't need to read from a file every time.
        CREATE DATABASE IF NOT EXISTS SampleDBTutorials;

        // Create a Table valued function. TVF ensures that your jobs fetch data from the weblog file with the correct schema.
        DROP FUNCTION IF EXISTS SampleDBTutorials.dbo.WeblogsView;
        CREATE FUNCTION SampleDBTutorials.dbo.WeblogsView()
        RETURNS @result TABLE
        (
            s_date DateTime,
            s_time string,
            s_sitename string,
            cs_method string,
            cs_uristem string,
            cs_uriquery string,
            s_port int,
            cs_username string,
            c_ip string,
            cs_useragent string,
            cs_cookie string,
            cs_referer string,
            cs_host string,
            sc_status int,
            sc_substatus int,
            sc_win32status int,
            sc_bytes int,
            cs_bytes int,
            s_timetaken int
        )
        AS
        BEGIN

            @result = EXTRACT
                s_date DateTime,
                s_time string,
                s_sitename string,
                cs_method string,
                cs_uristem string,
                cs_uriquery string,
                s_port int,
                cs_username string,
                c_ip string,
                cs_useragent string,
                cs_cookie string,
                cs_referer string,
                cs_host string,
                sc_status int,
                sc_substatus int,
                sc_win32status int,
                sc_bytes int,
                cs_bytes int,
                s_timetaken int
            FROM @"/Samples/Data/WebLog.log"
            USING Extractors.Text(delimiter:' ');
            RETURN;
        END;

        // Create a table for storing referrers and status
        DROP TABLE IF EXISTS SampleDBTutorials.dbo.ReferrersPerDay;
        @weblog = SampleDBTutorials.dbo.WeblogsView();
        CREATE TABLE SampleDBTutorials.dbo.ReferrersPerDay
        (
            INDEX idx1
            CLUSTERED(Year ASC)
            PARTITIONED BY HASH(Year)
        ) AS

        SELECT s_date.Year AS Year,
            s_date.Month AS Month,
            s_date.Day AS Day,
            cs_referer,
            sc_status,
            COUNT(DISTINCT c_ip) AS cnt
        FROM @weblog
        GROUP BY s_date,
                cs_referer,
                sc_status;

    Para compreender o U-SQL, consulte o artigo [Introdução ao idioma dados Lake Analytics U-SQL](data-lake-analytics-u-sql-get-started.md).    

5. Adicionar um novo script de U SQL ao seu projeto e introduza o seguinte:

        // Query the referrers that ran into errors
        @content =
            SELECT *
            FROM SampleDBTutorials.dbo.ReferrersPerDay
            WHERE sc_status >=400 AND sc_status < 500;

        OUTPUT @content
        TO @"/Samples/Outputs/UnsuccessfulResponses.log"
        USING Outputters.Tsv();

6. Mude novamente para o primeiro script U SQL e junto ao botão **Submeter** , especifique a sua conta de análise.
7. **Solução Explorer**, clique com o botão direito do rato em **Script.usql**e, em seguida, clique em **Criar Script**. Verificar os resultados no painel de saída.
8. **Solução Explorer**, clique com o botão direito do rato em **Script.usql**e, em seguida, clique em **Submeter Script**.
9. Verifique se que a **Conta de análise** é aquele em que pretende executar a tarefa e, em seguida, clique em **Submeter**. Resultados de submissão e ligação de tarefa estão disponíveis nas ferramentas de Lake de dados para a janela do Visual Studio resulta quando a apresentação seja concluída.
10. Aguarde até que a tarefa é concluída com êxito.  Se a tarefa falhar, provavelmente está em falta o ficheiro de origem.  Consulte a secção pré-requisito deste tutorial. Para informações adicionais de resolução de problemas, consulte o artigo [Monitor e resolver problemas de tarefas do Azure dados Lake Analytics](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md).

    Quando a tarefa estiver concluída, deverá ver o ecrã seguinte:

    ![a análise de dados lake analisar blogues Web registos de Web site](./media/data-lake-analytics-analyze-weblogs/data-lake-analytics-analyze-weblogs-job-completed.png)

11. Agora, repita os passos 7 a 10 para **Script1.usql**.

>[AZURE.NOTE]Não consegue ler ou escrever a uma tabela do U SQL que foi criada ou modificada no mesmo script.  Razão pela qual utilize dois scripts para este exemplo.

**Para ver o resultado da tarefa**

1. A partir do **Explorador do servidor**, expanda **Azure**, expanda **Lake a análise de dados**, expandir a sua conta de dados Lake Analytics, expandir **Contas de armazenamento**, a conta de armazenamento de Lake dados predefinida com o botão direito e, em seguida, clique em **Explorador**.
2.  Faça duplo clique em **amostras** para abrir a pasta e, em seguida, faça duplo clique em **saídas**.
3.  Faça duplo clique **UnsuccessfulResponsees.log**.
4.  Pode também clicar duas vezes o ficheiro de exportação dentro a vista de gráfico da tarefa para poder navegue diretamente para o resultado.

## <a name="see-also"></a>Consulte também

Para começar a trabalhar com dados Lake análises com ferramentas diferentes, consulte o artigo:

- [Começar a trabalhar com dados Lake Analytics através do Portal do Azure](data-lake-analytics-get-started-portal.md)
- [Começar a trabalhar com dados Lake Analytics através do PowerShell do Azure](data-lake-analytics-get-started-powershell.md)
- [Introdução ao .NET SDK a utilizar a análise de dados Lake](data-lake-analytics-get-started-net-sdk.md)

Para ver mais tópicos de desenvolvimento:

- [Desenvolver scripts de U SQL utilizando ferramentas de Lake de dados para o Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Introdução ao idioma do Azure dados Lake Analytics U-SQL](data-lake-analytics-u-sql-get-started.md)
- [Desenvolver operadores de definidas pelo utilizador U SQL para trabalhos de dados Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md)
