<properties
   pageTitle="Limitações de geral de base de dados do Azure SQL e diretrizes"
   description="Esta página descreve algumas limitações gerais para a base de dados do SQL Azure, bem como as áreas de interoperabilidade e suporte."
   services="sql-database"
   documentationCenter="na"
   authors="CarlRabeler"
   manager="jhubbard"
   editor="monicar" />
<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/06/2016"
   ms.author="carlrab" />

# <a name="azure-sql-database-general-limitations-and-guidelines"></a>Limitações de geral de base de dados do Azure SQL e diretrizes

Este tópico fornece limitações gerais e diretrizes para base de dados do SQL Azure. Para uma compreensão completa das quotas, gestão de recursos e suporte, consulte o artigo os [recursos adicionais](#additional-guidelines) no final deste tópico.

## <a name="connectivity-and-authentication"></a>Conectividade e de autenticação

  - Autenticação do Windows não é suportada. Consulte o artigo [Gerir bases de dados e inícios de sessão numa base de dados do Azure SQL](sql-database-manage-logins.md). No entanto, a autenticação do Azure Active Directory é suportada com algumas limitações. Consulte o artigo [ligar a base de dados SQL com a autenticação do Azure Active Directory](sql-database-aad-authentication.md).

  - Base de dados do Microsoft Azure SQL suporta os dados de tabela da cadeia (TDS) protocolo cliente versão 7.3 ou posterior.

  - Apenas as ligações em TCP/IP permitidas.

  - Não é suportado o browser do SQL Server do SQL Server 2008 porque base de dados do Microsoft Azure SQL não possui portas dinâmicas apenas porta 1433.

## <a name="sql-server-agentjobs"></a>Agente de/trabalhos de SQL Server

Base de dados do Microsoft Azure SQL não suporta agente do SQL Server, no entanto, pode utilizar tarefas flexível para executar tarefas ao longo de um a muitas bases de dados. Para mais informações acerca das tarefas flexível, consulte o artigo [tarefas flexível](sql-database-elastic-jobs-overview.md).

## <a name="sql-server-collation-support"></a>Suporte de agrupamento do SQL Server

O agrupamento de base de dados predefinido utilizado pelo base de dados do Microsoft Azure SQL é **SQL_LATIN1_GENERAL_CP1_CI_AS**, onde **LATIN1_GENERAL** for o inglês (Estados Unidos), **CP1** é o código de página 1252, **CI** é maiúsculas e minúsculas e **como** é sensível de destaques. Não é possível alterar o agrupamento para V12 bases de dados. Para mais informações sobre como configurar o agrupamento, consulte o artigo [Agrupar (Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx).

## <a name="naming-requirements"></a>Requisitos de nomenclatura

Determinadas nomes de utilizador não são permitidas por motivos de segurança. Não é possível utilizar os nomes dos seguintes procedimentos:

 - **Admin**
 - **administrador**
 - **convidado**
 - **raiz**
 - **SA**

Nomes para todos os novos objectos devem cumprir as regras do SQL Server para identificadores. Para mais informações, consulte o artigo [identificadores](https://msdn.microsoft.com/library/ms175874.aspx).

Para além disso, os nomes de utilizador de início de sessão e não podem conter a \ caráter (autenticação do Windows não é suportada).

## <a name="additional-guidelines"></a>Diretrizes adicionais

- Para além das limitações gerais descritas neste artigo, base de dados SQL tem as quotas de recursos específicos e limitações com base na sua **camada de serviços**. Para obter uma descrição geral das camadas de serviço, consulte o artigo [camadas de serviços de base de dados SQL](sql-database-service-tiers.md).

- Para outros limites de base de dados SQL, consulte o artigo [Limites de recursos de base de dados de SQL Azure](sql-database-resource-limits.md).

- Para segurança diretrizes relacionadas, consulte [diretrizes de segurança da base de dados do Azure SQL e limitações](sql-database-security-guidelines.md).

- Outra área relacionada rodeia a compatibilidade que tem de base de dados do SQL Azure com as versões no local do SQL Server, como o SQL Server 2014 e o SQL Server 2016. A versão mais recente do V12 da base de dados do SQL Azure fez muitos melhoramentos nesta área. Para obter mais detalhes, consulte o artigo [o que é novo no V12 de base de dados do SQL](sql-database-v12-whats-new.md).

- Para obter informações sobre a disponibilidade de controladores e suporte para a base de dados SQL, consulte o artigo [Bibliotecas de ligações para a base de dados SQL e o SQL Server](sql-database-libraries.md).
