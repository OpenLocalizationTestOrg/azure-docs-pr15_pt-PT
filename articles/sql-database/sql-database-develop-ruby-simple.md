<properties
    pageTitle="Se ligar à base de dados SQL utilizando Rubi | Microsoft Azure"
    description="Dar um exemplo de código Rubi que pode executar para ligar à base de dados do SQL Azure."
    services="sql-database"
    documentationCenter=""
    authors="ajlam"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="drivers"
    ms.tgt_pltfrm="na"
    ms.devlang="ruby"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="andrela"/>


# <a name="connect-to-sql-database-by-using-ruby"></a>Se ligar à base de dados SQL utilizando Rubi 

[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)] 

Este tópico mostra como ligar e uma base de dados do SQL Azure utilizando Rubi da consulta. Pode executar este exemplo a partir do Windows, Ubuntu Linux ou Mac plataformas.

## <a name="step-1-configure-development-environment"></a>Passo 1: Configurar o ambiente de desenvolvimento

[Pré-requisitos para utilizar o controlador de Rubi TinyTDS para SQL Server](https://msdn.microsoft.com/library/mt711041.aspx)

## <a name="step-2-create-a-sql-database"></a>Passo 2: Criar uma base de dados SQL

Ver a [página Introdução](sql-database-get-started.md) para saber como criar uma base de dados de exemplo.  É importante que segue o guia para criar um **modelo de base de dados AdventureWorks**. Os exemplos mostrados abaixo só funcionam com o **esquema de AdventureWorks**.

## <a name="step-3-get-connection-details"></a>Passo 3: Obter detalhes de ligação

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## <a name="step-4-run-sample-code"></a>Passo 4: Executar o código de exemplo

[Prova de conceito estabelecer ligação ao utilizar Rubi SQL](http://msdn.microsoft.com/library/mt715797.aspx)

## <a name="next-steps"></a>Próximos passos

* Rever a [Descrição geral do desenvolvimento de base de dados SQL](sql-database-develop-overview.md)
* Obter mais informações sobre o [Microsoft Rubi controlador para SQL Server](https://msdn.microsoft.com/library/mt691981.aspx)

## <a name="additional-resources"></a>Recursos adicionais 

* [Padrões de design para aplicações do inquilino com várias SaaS com base de dados Azure SQL](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Explorar todas as [capacidades da base de dados SQL](https://azure.microsoft.com/services/sql-database/)
