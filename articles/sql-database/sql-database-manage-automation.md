<properties
    pageTitle="Gerir bases de dados do SQL Azure utilizando a automatização Azure | Microsoft Azure"
    description="Saiba mais sobre como o serviço de automatização do Azure pode ser utilizado para gerir bases de dados do Azure SQL na escala."
    services="sql-database, automation"
    documentationCenter=""
    authors="jodoglevy"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/26/2016"
    ms.author="jolevy"/>



#<a name="managing-azure-sql-databases-using-azure-automation"></a>Gerir bases de dados do SQL Azure utilizando a automatização do Azure

Este guia vai introduzi-lo para o serviço de automatização do Azure e como pode ser utilizado para simplificar a gestão das suas bases de dados do Azure SQL.


## <a name="what-is-azure-automation"></a>O que é o Azure automatização?

[Azure automatização](https://azure.microsoft.com/services/automation/) é um serviço Azure para simplificar a gestão de nuvem através de automatização do processo. Utilizar o Azure automatização, tarefas de execução longa, manuais, passíveis de erro e frequentemente repetidas podem ser automatizadas para aumentar a fiabilidade, eficiência e a hora a valor para a sua organização.

Automatização Azure fornece um motor de execução do fluxo de trabalho altamente fiáveis e altamente disponível que se adapta para corresponder às suas necessidades, à medida que aumenta a sua organização. No Azure automatização, processos podem ser pontapés manualmente, pelos sistemas de 3 terceiros ou em intervalos agendados para que as tarefas deverá acontecer exatamente quando necessário.

Baixar o overhead operacional e libertar IT / DevOps docentes concentrar-se no trabalho que adiciona empresas valor ao mover as tarefas de gestão de na nuvem para ser executada automaticamente por Azure automatização.


## <a name="how-can-azure-automation-help-manage-azure-sql-databases"></a>Como Azure automatização ajudam a gerir bases de dados Azure SQL?

Base de dados SQL Azure podem ser gerido na automatização Azure utilizando os [cmdlets do PowerShell de base de dados do SQL Azure](https://msdn.microsoft.com/library/dn546723.aspx) que estão disponíveis nas [Ferramentas de Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx). Automatização Azure tem os cmdlets do PowerShell de base de dados do SQL Azure estes fora da caixa de, para que pode efetuar todas as tarefas de gestão de SQL DB dentro do serviço. Também pode emparelhar estes cmdlets do Azure automatização com os cmdlets para outros serviços do Azure, para automatizar tarefas complexas através de serviços do Azure e 3º sistemas de terceiros.

Automatização Azure também tem a capacidade de comunicar com os servidores SQL diretamente, através da emissão comandos SQL através do PowerShell.

A [Galeria de livro execuções do Azure automatização](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) contém uma variedade de runbooks de Comunidade e equipa de produto para começar a utilizar a automatização de gestão de bases de dados do SQL Azure, outros serviços do Azure e sistemas de festa 3º. Galeria runbooks incluem:

 * [Executar consultas SQL relativamente a uma base de dados do SQL Server](https://gallery.technet.microsoft.com/scriptcenter/How-to-use-a-SQL-Command-be77f9d2)
 * [Dimensionar verticalmente (para cima ou para baixo) uma base de dados do SQL Azure com base numa agenda](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-e957354f)
 * [Truncar uma tabela SQL se a sua base de dados abordagens o tamanho máximo](https://gallery.technet.microsoft.com/scriptcenter/Azure-Automation-Your-SQL-30f8736b)
 * [Indexar tabelas numa base de dados SQL Azure se altamente fragmentados](https://gallery.technet.microsoft.com/scriptcenter/Indexes-tables-in-an-Azure-73a2a8ea)

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu os princípios básicos de automatização do Azure e como pode ser utilizado para gerir bases de dados Azure SQL, siga estas ligações para mais informações acerca de automatização do Azure.

- [Descrição geral de automatização Azure](../automation/automation-intro.md)
- [Meu livro de execuções primeiro](../automation/automation-first-runbook-graphical.md)
- [Mapa de formação de automatização Azure](https://azure.microsoft.com/documentation/learning-paths/automation/)
- [Azure automatização: O agente SQL na nuvem](https://azure.microsoft.com/blog/2014/06/26/azure-automation-your-sql-agent-in-the-cloud/) 
 
