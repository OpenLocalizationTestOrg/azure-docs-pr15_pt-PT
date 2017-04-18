<properties
    pageTitle="Descrição geral: ferramentas de gestão para a base de dados SQL | Microsoft Azure"
    description="Compara as ferramentas e opções para gerir a base de dados do SQL Azure"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="sstein"/>

# <a name="overview-management-tools-for-sql-database"></a>Descrição geral: ferramentas de gestão para a base de dados SQL

Este tópico explora e compara ferramentas e opções para gerir bases de dados Azure SQL, pelo que pode escolher a ferramenta certa para a tarefa, o seu negócio e. Selecionar a ferramenta certa depende bases de dados quantos gerir, a tarefa e com que frequência é executada numa tarefa.

## <a name="azure-portal"></a>Portal do Azure

O [Azure portal](https://portal.azure.com) é uma aplicação baseada na web onde pode criar, atualizar e eliminar bases de dados e os servidores de lógicos-las e monitorizar a atividade de base de dados. Esta ferramenta está excelente se estiver a começar com o Azure, gestão de algumas bases de dados, ou precisa de fazer algo rapidamente.

Para mais informações sobre como utilizar o portal, consulte o artigo [Gerir bases de dados do SQL através do portal Azure](sql-database-manage-portal.md).

## <a name="sql-server-management-studio-and-sql-server-data-tools-in-visual-studio"></a>SQL Server Management Studio e as ferramentas de dados do SQL Server no Visual Studio

SQL Server Management Studio (SSMS) e o SQL Server dados Tools (SSDT) são ferramentas de cliente que são executados no seu computador para gerir e desenvolver a sua base de dados na nuvem. Se for um programador de aplicação familiarizado com o Visual Studio ou outros ambientes de desenvolvimento integrado (IDEs), [Experimente utilizar SSDT no Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Muitos administradores de base de dados estiver familiarizados com SSMS, que podem ser utilizados com bases de dados Azure SQL. [Transferir a versão mais recente do SSMS](https://msdn.microsoft.com/library/mt238290) e utilizar sempre a versão mais recente ao trabalhar com a base de dados do SQL Azure. Para mais informações sobre como gerir as bases de dados do SQL Azure com SSMS, consulte o artigo [Gerir bases de dados do SQL utilizando SSMS](sql-database-manage-azure-ssms.md).

> [AZURE.IMPORTANT] Utilize sempre a versão mais recente do [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290) e [Ferramentas de dados do SQL Server](https://msdn.microsoft.com/library/mt204009.aspx) para permanecer sincronizados com atualizações para o Microsoft Azure e base de dados SQL.


## <a name="powershell"></a>PowerShell

Pode utilizar o PowerShell para gerir bases de dados e agrupamentos de base de dados flexível e para automatizar implementações do recurso Azure. A Microsoft recomenda esta ferramenta para gerir um grande número de bases de dados e automatizar implementação e alterações de recursos num ambiente de produção.

Para obter mais informações, consulte o artigo [Gerir a base de dados SQL com PowerShell](sql-database-manage-powershell.md)

## <a name="elastic-database-tools"></a>Ferramentas de base de dados flexível
Utilize as ferramentas de base de dados flexível para executar ações tais como 

* Executar um script T-SQL contra um conjunto de bases de dados utilizando uma [tarefa flexível](sql-database-elastic-jobs-overview.md)
* Mover bases de dados do inquilino com várias modelo a um modelo de inquilino único com a [ferramenta de intercalação de divisão](sql-database-elastic-scale-overview-split-and-merge.md)
* Gerir bases de dados de um modelo de inquilino única ou um modelo de inquilino com várias utilizando a [biblioteca de escala flexível do cliente](sql-database-elastic-database-client-library.md).
 

## <a name="additional-resources"></a>Recursos adicionais

- [Gestor de recursos do Azure](https://azure.microsoft.com/features/resource-manager/)
- [Automatização Azure](https://azure.microsoft.com/documentation/services/automation/)
- [Programador do Azure](https://azure.microsoft.com/documentation/services/scheduler/)