<properties
   pageTitle="Início rápido do solução de base de dados Azure SQL | Microsoft Azure"
   description="Saiba mais sobre soluções de base de dados do Azure SQL"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-quickstart"
   ms.date="09/06/2016"
   ms.author="carlrab"/>

# <a name="explore-azure-sql-database-solution-quick-starts"></a>Explorar início rápido do solução de base de dados Azure SQL

Este artigo contém uma descrição geral do Azure SQL da base de dados solução rápida é iniciado. Estes inicia rápida estão localizado no repositório de amostras GitHub SQL Server e demonstram a utilização da base de dados do SQL numa solução completa com base em cenários reais. Para a tutoriais passo a passo simples que demonstram a utilização de uma determinada funcionalidade de base de dados SQL, consulte o artigo [tutoriais Explore o base de dados do Azure SQL](sql-database-explore-tutorials.md).

## <a name="try-the-wingtiptickets-demo-and-hands-on-lab"></a>Experimente o laboratório prático e WingTipTickets demonstração

A demonstração de [WingTipTickets de base de dados do SQL Azure](https://github.com/microsoft/wingtiptickets) e laboratório prático demonstram uma base de dados do SQL Azure e a aplicação de exemplo com base no Azure pesquisa que é utilizada para pedidos de conjunto de vender.


## <a name="collect-and-monitor-resource-usage-data-across-multiple-pools"></a>Recolher e monitorizar os dados de utilização de recursos através de vários conjuntos de dados

[Solução guia de introdução: telemetria agrupamento flexível através do PowerShell](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools) fornece uma solução para recolher e monitorizar a utilização de recursos base de dados SQL através de múltiplos agrupamentos de uma subscrição. Quando tiver uma subscrição de um grande número de bases de dados, é complexo para monitorizar a cada agrupamento flexível separadamente.

Para resolver este problema, pode combinar os cmdlets do PowerShell de base de dados do SQL e consultas de T SQL para recolher dados de utilização de recursos a partir de vários conjuntos de dados e suas bases de dados. Isto ajuda a monitorizar e analisar utilização de recursos de forma mais eficiente.

Este guia fornece um conjunto de PowerShell scripts e consultas de T SQL juntamente com a documentação sobre o que faz a solução e como implementá-lo.

## <a name="get-started-with-elastic-database-in-an-saas-scenario"></a>Introdução ao flexível da base de dados num cenário de SaaS

 [Solução guia de introdução: dashboard personalizado do conjunto de dados flexível para SaaS](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools-custom-dashboard) fornece uma solução para um cenário de Software-como-a-solução (SaaS) que tira partido da funcionalidade flexível da base de dados da base de dados do SQL para fornecer rentável e dimensionáveis da base de dados back-end para uma aplicação do SaaS.

Nesta solução, irá guiá-lo através da execução de uma aplicação web. Esta aplicação web permite-lhe visualizar a carga que é criada uma base de dados flexível por um gerador de carga que utiliza um dashboard personalizado que o portal do Azure os suplementos.

Neste guia fornece uma carga gerador e monitorização do web app, juntamente com a documentação sobre o que faz a aplicação e como utilizá-la.

## <a name="create-an-azure-sql-database-by-using-code-first-development-and-the-entity-framework"></a>Criar uma base de dados do Azure SQL utilizando desenvolvimento código primeira e a arquitetura de entidade

O vídeo e amostra no [Código primeiro para uma nova base de dados](https://msdn.microsoft.com/data/jj193542.aspx) , fornece uma introdução à programação código primeiro que destina-se uma nova base de dados. Este cenário destina-se uma base de dados que não existe, mas que vão ser criado pelo código primeiro. Em alternativa, o cenário cria uma base de dados vazia ao qual código primeiro adiciona novas tabelas.

Código pela primeira vez permite-lhe definir o seu modelo, utilizando por classes c# ou Visual Basic .NET. Pode executar a configuração adicional opcional utilizando atributos no seu aulas e propriedades ou ao utilizar uma API fluent.

## <a name="integrate-elastic-database-tools-into-an-entity-framework-application"></a>Integrar ferramentas da base de dados flexível uma aplicação de entidade Framework

O exemplo de [biblioteca de base de dados flexível do cliente com entidade Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) mostra as alterações que precisa de efetuar para uma aplicação de entidade Framework para integrá-la com as [Ferramentas de base de dados flexível](sql-database-elastic-scale-get-started.md). O foco estiver no [shard mapear gestão](sql-database-elastic-scale-shard-map-management.md) e [Encaminhamento dependentes de dados](sql-database-elastic-scale-data-dependent-routing.md) com a entidade Framework código primeira abordagem a compor.

O [Código primeiro para uma nova amostra de base de dados para EF](http://msdn.microsoft.com/data/jj193542.aspx) serve nosso exemplo em execução ao longo deste exemplo. O código de exemplo que acompanha este documento faz parte de um conjunto de ferramentas da base de dados flexível de amostras nos exemplos de código do Visual Studio.

## <a name="integrate-elastic-database-tools-with-row-level-security"></a>Integrar ferramentas da base de dados flexível com segurança de nível de linha

[Aplicações multi-inquilino com ferramentas de base de dados flexível e segurança de nível de linha](sql-database-elastic-tools-multi-tenant-row-level-security.md) mostra as alterações que precisa de efetuar a uma aplicação de entidade Framework para integrar [Ferramentas da base de dados flexível](sql-database-elastic-scale-get-started.md) com [segurança de nível de linha](https://msdn.microsoft.com/library/dn765131). Este exemplo ilustra como utilizar estas tecnologias em conjunto para criar uma aplicação com uma camada de dados altamente dimensionáveis que suporte shards multi-inquilino.

Pode fazê-lo utilizando o ADO.NET SqlClient ou entidade Framework. Este exemplo Expande a [biblioteca do cliente da base de dados flexível com entidade Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) ao adicionar suporte para bases de dados de shard multi-inquilino.
Cria uma aplicação de consola simples para criar blogues e mensagens, com quatro inquilinos e duas shard multi-inquilino as bases de dados.

## <a name="create-online-surveys-with-the-tailspin-surveys-application"></a>Criar inquéritos onlinehttps com a aplicação de brinquedos inquéritos

Esta [aplicação de exemplo brinquedos inquéritos](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md) é uma multitenant, aplicação web, denominada inquéritos, que permite aos utilizadores criar inquéritos onlinehttps. O exemplo endereços algumas preocupações chave sobre como gerir identidades de utilizador numa aplicação multi-inquilino, incluindo a inscrição, autenticação, autorização e funções de aplicação.

## <a name="learn-about-the-latest-security-features-of-sql-database-with-the-contoso-clinic-demo-application"></a>Saiba mais sobre as funcionalidades mais recentes de segurança da base de dados SQL com a aplicação de demonstração de acção Contoso

Esta [aplicação Contoso meio demonstração](https://github.com/Microsoft/azure-sql-security-sample) demonstra as funcionalidades mais recentes de segurança da base de dados SQL.

## <a name="next-steps"></a>Próximos passos

[Explorar a tutoriais de base de dados do Azure SQL](sql-database-explore-tutorials.md)
