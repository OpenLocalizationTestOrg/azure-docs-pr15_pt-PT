<properties
   pageTitle="Apresentar o catálogo de dados do Azure a análise de Lake U-SQL | Azure"
   description="Apresentar o catálogo de dados do Azure a análise de Lake U-SQL"
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

# <a name="use-u-sql-catalog"></a>Utilizar o catálogo U SQL

O catálogo de U SQL é utilizado para estruturar o código e dados para que podem ser partilhados por scripts U-SQL. O catálogo permite que o mais alto desempenho possível com dados no Azure dados Lake.

Cada conta Azure dados Lake Analytics tem um catálogo de U SQL associada. Não consegue eliminar o catálogo de U-SQL. Atualmente catálogos U SQL não pode ser partilhados entre contas de arquivo de Lake de dados.

Cada catálogo U SQL contém uma base de dados denominada **modelo global**. A base de dados do modelo global não podem ser eliminada.  Cada catálogo U SQL pode conter mais bases de dados adicionais.

Base de dados U SQL contém:

- Conjuntos de – partilhar código .NET entre scripts U SQL.
- Funções de valores de tabela – partilhar o código U SQL entre scripts U SQL.
- Tabelas – partilhar dados entre scripts U SQL.
- Esquemas - partilhar esquemas de tabela entre scripts U SQL.

## <a name="manage-catalogs"></a>Gerir catálogos
Cada conta Azure dados Lake Analytics tem uma conta do Azure dados Lake arquivo predefinida associada. Esta conta de arquivo de Lake dados é referida como conta predefinida dados Lake loja. Catálogo de U SQL é armazenado na conta de dados Lake arquivo predefinida na pasta /catalog. Não elimine os ficheiros na pasta /catalog.

### <a name="use-azure-portal"></a>Utilizar o Azure portal

Consulte o artigo [Gerir a análise de dados Lake através do portal](data-lake-analytics-manage-use-portal.md#view-u-sql-catalog)


### <a name="use-data-lake-tools-for-visual-studio"></a>Utilize ferramentas de Lake de dados para o Visual Studio.

Pode utilizar ferramentas de Lake de dados para o Visual Studio para gerir o catálogo.  Para mais informações sobre as ferramentas, consulte o artigo [Utilizar ferramentas de Lake de dados para o Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).

**Para gerir o catálogo**

1. Abrir o Visual Studio e ligar ao azure. Para obter as instruções, consulte o artigo [ligar ao Azure](data-lake-analytics-data-lake-tools-get-started.md#connect-to-azure).
1. Abra o **Explorador de servidor** , prima **CTRL + ALT + S**.
2. A partir do **Explorador do servidor**, expanda **Azure**, expanda **A análise de dados Lake**, expandir a sua conta de dados Lake Analytics, expandir **bases de dados**e, em seguida, expanda **principal**.



    - Para adicionar uma nova base de dados, **base de dados**com o botão direito e, em seguida, clique em **Criar a base de dados**.
    - Para adicionar uma nova assemblagem, **montagens**de contexto e, em seguida, clique em **Registe-se assemblagem**.
    - Para adicionar um novo esquema, com o botão direito **esquemas**e, em seguida, clique em "Criar esquema * *.
    - Para adicionar uma nova tabela, com o botão direito **tabelas**e, em seguida, clique em "" Criar tabela * *.
    - Para adicionar uma nova função de valor de tabela, consulte o artigo [operadores para tarefas Lake a análise de dados definidos pelo utilizador de desenvolver U-SQL](data-lake-analytics-u-sql-develop-user-defined-operators.md).


![Procure os catálogos de U SQL Visual Studio](./media/data-lake-analytics-use-u-sql-catalog/data-lake-analytics-browse-catalogs.png)


## <a name="see-also"></a>Consulte também

- Introdução
    - [Começar a trabalhar com dados Lake Analytics através do portal Azure](data-lake-analytics-get-started-portal.md)
    - [Começar a trabalhar com dados Lake Analytics através do PowerShell do Azure](data-lake-analytics-get-started-powershell.md)
    - [Introdução ao Azure .NET SDK a utilizar a análise de dados Lake](data-lake-analytics-get-started-net-sdk.md)
    - [Desenvolver scripts de U SQL utilizando ferramentas de Lake de dados para o Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
    - [Introdução ao idioma do Azure dados Lake Analytics U-SQL](data-lake-analytics-u-sql-get-started.md)

- U SQL e desenvolvimento
    - [Introdução ao idioma do Azure dados Lake Analytics U-SQL](data-lake-analytics-u-sql-get-started.md)
    - [Utilize funções de janela U-SQL para tarefas do Azure dados Lake Analytics](data-lake-analytics-use-window-functions.md)
    - [Desenvolver operadores de definidas pelo utilizador U SQL para trabalhos de dados Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md)

- Gestão
    - [Gerir Azure dados Lake Analytics através do portal Azure](data-lake-analytics-manage-use-portal.md)
    - [Gerir Azure dados Lake Analytics através do PowerShell do Azure](data-lake-analytics-manage-use-powershell.md)
    - [Monitorizar e resolver problemas de tarefas do Azure dados Lake Analytics através do portal Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

- Tutorial do ponto a ponto
    - [Utilizar a tutoriais interativos Azure dados Lake Analytics](data-lake-analytics-use-interactive-tutorials.md)
    - [Analisar os registos de Web site utilizando a análise do Azure dados Lake](data-lake-analytics-analyze-weblogs.md)
