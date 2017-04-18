<properties
    pageTitle="Se ligar à base de dados SQL utilizando PHP no Windows | Microsoft Azure"
    description="Apresenta um programa PHP de exemplo que se liga à base de dados do SQL Azure a partir de um cliente do Windows e fornece ligações para os componentes de software necessário necessários pelo cliente."
    services="sql-database"
    documentationCenter=""
    authors="meet-bhagdev"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="drivers"
    ms.tgt_pltfrm="na"
    ms.devlang="php"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="meetb"/>


# <a name="connect-to-sql-database-by-using-php-on-windows"></a>Se ligar à base de dados SQL utilizando PHP no Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)] 


Este tópico ilustra como se pode ligar à base de dados do SQL Azure uma aplicação de cliente escrita PHP que é executada no Windows.

## <a name="step-1--configure-development-environment"></a>Passo 1: Configurar o ambiente de desenvolvimento

[Configurar o ambiente de desenvolvimento para o desenvolvimento de PHP](https://msdn.microsoft.com/library/mt720663.aspx)

## <a name="step-2-create-a-sql-database"></a>Passo 2: Criar uma base de dados SQL

Ver a [página Introdução](sql-database-get-started.md) para saber como criar uma base de dados de exemplo.  É importante que segue o guia para criar um **modelo de base de dados AdventureWorks**. Os exemplos mostrados abaixo só funcionam com o **esquema de AdventureWorks**.


## <a name="step-3-get-connection-details"></a>Passo 3: Obter detalhes de ligação

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]


## <a name="step-4-run-sample-code"></a>Passo 4: Executar o código de exemplo

* [Prova de conceito estabelecer ligação ao utilizar PHP de SQL](https://msdn.microsoft.com/library/mt720665.aspx)
* [Ligar resiliently ao SQL com PHP](https://msdn.microsoft.com/library/mt720667.aspx)


## <a name="next-steps"></a>Próximos passos

* Rever a [Descrição geral do desenvolvimento de base de dados SQL](sql-database-develop-overview.md)
* Obter mais informações sobre o [Microsoft PHP controlador para SQL Server](https://msdn.microsoft.com/library/dn865013.aspx)
* Para mais informações sobre a instalação PHP e a utilização, consulte o artigo [Aceder ao servidor de bases de dados SQL com PHP](http://social.technet.microsoft.com/wiki/contents/articles/1258.accessing-sql-server-databases-from-php.aspx).

## <a name="additional-resources"></a>Recursos adicionais 

* [Padrões de design para aplicações do inquilino com várias SaaS com base de dados Azure SQL](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Explorar todas as [capacidades da base de dados SQL](https://azure.microsoft.com/services/sql-database/)
