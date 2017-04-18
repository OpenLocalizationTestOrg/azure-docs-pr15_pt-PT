<properties
    pageTitle="Se ligar à base de dados SQL utilizando Java com JDBC no Windows | Microsoft Azure"
    description="Apresenta um exemplo de código Java que pode utilizar para ligar à base de dados do SQL Azure. O exemplo utiliza JDBC e é executado no computador de cliente do Windows."
    services="sql-database"
    documentationCenter=""
    authors="LuisBosquez"
    manager="jhubbard"
    editor="genemi"/>


<tags
    ms.service="sql-database"
    ms.workload="drivers"
    ms.tgt_pltfrm="na"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="lbosq"/>


# <a name="connect-to-sql-database-by-using-java-with-jdbc-on-windows"></a>Se ligar à base de dados SQL utilizando Java com JDBC no Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)] 


Este tópico apresenta uma amostra de código Java que pode utilizar para ligar à base de dados do SQL Azure. O exemplo Java baseia-se no Java Development Kit (JDK) versão 1.8. O exemplo liga a uma base de dados do SQL Azure utilizando o controlador JDBC.

## <a name="step-1--configure-development-environment"></a>Passo 1: Configurar o ambiente de desenvolvimento

[Configurar o ambiente de desenvolvimento para o desenvolvimento de Java](https://msdn.microsoft.com/library/mt720658.aspx)

## <a name="step-2-create-a-sql-database"></a>Passo 2: Criar uma base de dados SQL

Ver a [página Introdução](sql-database-get-started.md) para saber como criar uma base de dados.  

## <a name="step-3-get-connection-string"></a>Passo 3: Obter a cadeia de ligação

[AZURE.INCLUDE [sql-database-include-connection-string-jdbc-20-portalshots](../../includes/sql-database-include-connection-string-jdbc-20-portalshots.md)]

> [AZURE.NOTE] Se estiver a utilizar o controlador de JTDS JDBC, em seguida, é necessário adicionar "ssl = exigir" para o URL da ligação de cadeia e tem de definir a opção seguinte para o JVM "-Djsse.enableCBCProtection=false". Esta opção JVM desativa uma correção para uma vulnerabilidade de segurança, por isso, certifique-se de que compreende qual o risco está envolvido antes de configurar esta opção.

## <a name="step-4-run-sample-code"></a>Passo 4: Executar o código de exemplo

* [Prova de conceito estabelecer ligação ao SQL utilizando Java](https://msdn.microsoft.com/library/mt720656.aspx)

## <a name="next-steps"></a>Próximos passos

* Visite o [Centro de programadores do Java](/develop/java/).
* Rever a [Descrição geral do desenvolvimento de base de dados SQL](sql-database-develop-overview.md)
* Obter mais informações sobre o [Microsoft JDBC controlador para SQL Server](https://msdn.microsoft.com/library/mt484311.aspx)

## <a name="additional-resources"></a>Recursos adicionais 

* [Padrões de design para aplicações do inquilino com várias SaaS com base de dados Azure SQL](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Explorar todas as [capacidades da base de dados SQL](https://azure.microsoft.com/services/sql-database/)
