<properties
   pageTitle="Resolução de problemas armazém de dados do Azure SQL | Microsoft Azure"
   description="Resolução de problemas armazém de dados do Azure SQL."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/30/2016"
   ms.author="sonyama;barbkess"/>

# <a name="troubleshooting-azure-sql-data-warehouse"></a>Resolução de problemas armazém de dados do Azure SQL

Este tópico indica algumas questões de resolução de problemas mais comuns que podemos ouvir a partir dos nossos clientes.

## <a name="connecting"></a>Ligar-se

| Problema                              | Resolução                                      |
| :----------------------------------| :---------------------------------------------- |
| Ocorreu uma falha do início de sessão para o utilizador 'NT AUTHORITY\ANONYMOUS LOGON'. (Microsoft SQL Server, erro: 18456) | Este erro ocorre quando um utilizador AAD tenta ligar à base de dados principal, mas não tem um utilizador no modelo global.  Para corrigir este problema quer especificar o armazenamento de dados SQL que pretende ligar no momento da ligação ou adicionar o utilizador a base de dados principal.  Consulte o artigo [Descrição geral de segurança][] artigo para obter mais detalhes.|
|O servidor principal "Nomedoutilizadorpessoal" não é possível aceder à base de dados "principal" no contexto atual de segurança. Não consegue abrir a base de dados do utilizador predefinida. Falha ao iniciar sessão. Ocorreu uma falha do início de sessão para o utilizador 'Nomedoutilizadorpessoal'. (Microsoft SQL Server, erro: 916) | Este erro ocorre quando um utilizador AAD tenta ligar à base de dados principal, mas não tem um utilizador no modelo global.  Para corrigir este problema quer especificar o armazenamento de dados SQL que pretende ligar no momento da ligação ou adicionar o utilizador a base de dados principal.  Consulte o artigo [Descrição geral de segurança][] artigo para obter mais detalhes.|
| Erro CTAIP                        | Este erro pode ocorrer quando for criado um início de sessão no SQL server base de dados principal, mas não na base de dados SQL armazém de dados.  Se se deparar com este erro, veja o artigo [Descrição geral de segurança][] .  Este artigo explica como criar criar um início de sessão e o utilizador no modelo global e, em seguida, como criar um utilizador da base de dados do SQL armazém de dados.|
| Bloqueado pela Firewall                |Bases de dados SQL Azure estão protegidos por firewalls nível de servidor e base de dados para garantir que apenas conhecidos endereços IP ter acesso a uma base de dados. As firewalls são seguras por predefinição, o que significa que explicitamente tem de ativar e o endereço IP ou o intervalo de endereços antes de poder ligar.  Para configurar a firewall do access, siga os passos no [acesso ao configurar servidor firewall para o seu IP de cliente][] na [aprovisionar instruções][].|
| Não consegue ligar com a ferramenta ou controlador | Armazém de dados SQL recomenda a utilização do [SSMS][], [SSDT para Visual Studio 2015][]ou [sqlcmd][] para consultar os seus dados. Para obter mais detalhes sobre controladores e estabelecer ligação ao armazém de dados SQL, consulte os artigos de [controladores para armazém de dados do SQL Azure][] e [ligar ao armazenamento de dados do SQL Azure][] .|


## <a name="tools"></a>Ferramentas

| Problema                              | Resolução                                      |
| :----------------------------------| :---------------------------------------------- |
| Explorador de objeto do Visual Studio falta utilizadores AAD | Este é um problema conhecido.  Como solução, veja os utilizadores na [sys.database_principals][].  Consulte o artigo [autenticação para armazém de dados do SQL Azure][] para saber mais sobre como utilizar o Azure Active Directory com armazém de dados SQL.|

## <a name="performance"></a>Desempenho

|  Problema                             | Resolução                                      |
| :----------------------------------| :---------------------------------------------- |
| Resolução de problemas de desempenho da consulta  | Se estiver a tentar resolver problemas de uma consulta específica, comece com [aprendendo a monitorizar as suas consultas][].|
| Desempenho da consulta fraca e planos é frequentemente um resultado de estatísticas em falta   | A causa mais comuns de baixa desempenho é falta de estatísticas sobre as suas tabelas.  Ver [as estatísticas de tabela Maintaining] [ Statistics] para obter detalhes sobre como criar estatísticas e por que razão são críticos para o seu desempenho.|
| Baixa simultaneidade / na fila de consultas   | Noções sobre a [Gestão de carga de trabalho][] é importante para compreender como saldo a alocação de memória com simultaneidade.|
| Como implementar o melhores práticas    | Coloque o melhor para começar a aprender formas para melhorar o desempenho de consulta são artigo [práticas recomendadas do armazém de dados SQL][] .|
| Como melhorar o desempenho com o dimensionamento  | Por vezes, a solução para melhorar o desempenho é adicionar simplesmente o que mais calcular power para as suas consultas por [dimensionamento seu armazém de dados SQL][].|
| Desempenho da consulta fraca estatístico como resultado da qualidade de índice remissivo baixa | Consultas de algumas vezes podem abrandamento devido a [columnstore originar má qualidade de índice remissivo][].  Consulte este artigo para obter mais informações e como [Reconstruir índices para melhorar a qualidade de segmento][].|

## <a name="system-management"></a>Gestão de sistema

|  Problema                             | Resolução                                      |
| :----------------------------------| :---------------------------------------------- |
| Msg 40847: Não conseguiu executar a operação porque o servidor seria exceda a quota de base de dados da transação unidade permitida do 45000. | Reduza a [DWU][] da base de dados que está a tentar criar ou [Pedir um aumento de quota][].|
| Utilização do espaço a investigar    | Consulte o artigo [tamanhos de tabela][] para compreender a utilização de espaço do sistema.|
| Ajudar com a gestão de tabelas          | Consulte o artigo [Descrição geral de tabela] [ Overview] artigo para obter ajuda com a gestão suas tabelas.  Este artigo também inclui ligações para tópicos mais detalhados, como [tipos de dados de tabela][Data types], [distribuir a uma tabela][Distribute], [indexação de uma tabela][Index], [uma tabela de criação de partições][Partition], [Maintaining estatísticas da tabela] [ Statistics] e [tabelas temporárias][Temporary].|

## <a name="polybase"></a>Polybase

|  Problema                             | Resolução                                      |
| :----------------------------------| :---------------------------------------------- |
| Erro UTF-8                        |  Atualmente PolyBase suporta apenas ao carregar os ficheiros de dados que tenham sido codificado UTF-8.  Consulte o artigo [trabalhar em torno o requisito de PolyBase UTF-8][] para obter orientações sobre como contornar esta limitação.|
| Carregamento falha devido às linhas de grandes dimensões   | Atualmente o suporte de grandes dimensões linha não está disponível para Polybase.  Isto significa que, se a tabela contiver varchar (Max), tipo ou varbinary (Max), tabelas externas não podem ser utilizadas para carregar os seus dados.  Cargas para as linhas de grandes dimensões é atualmente suportados apenas através de Azure fábrica de dados (com BCP), a análise da cadeia de Azure, SSIS, BCP ou a classe de .NET SQLBulkCopy. Suporte de PolyBase para as linhas de grandes dimensões será adicionado um lançamento futuro.|
| está a falhar BCP carga da tabela com o tipo de dados de máximo | Existe um problema conhecido que exige que sejam colocados varchar (Max), tipo ou varbinary (Max) no final da tabela em alguns cenários.  Tente mover as colunas de máximo para o fim da tabela.|

## <a name="differences-from-sql-database"></a>Diferenças de base de dados SQL

|  Problema                             | Resolução                                      |
| :----------------------------------| :---------------------------------------------- |
| Funcionalidades de base de dados SQL não suportadas  | Consulte o artigo [funcionalidades de tabela não suportadas][].|
| Tipos de dados de base de dados SQL não suportados  | Consulte o artigo [tipos de dados não suportadas][].|
| ELIMINAR e limitações de actualização      | Consulte o artigo [soluções de ATUALIZAÇÃO][], [Eliminar soluções][] e [Utilizar CTAS para contornar sintaxe ATUALIZAR e eliminar não suportada][].  |
| Declaração de impressão em série não é suportada   | Consulte o artigo [Intercalar soluções][].|
| Limitações de procedimento armazenado       | Consulte [limitações de procedimento armazenado][] para compreender algumas das limitações dos procedimentos armazenados.|
| UDFs não suportam as instruções SELECT | Esta é uma limitação actual do nossos UDFs.  Consulte o artigo [Criar função][] para a sintaxe da que suportamos.   |

## <a name="next-steps"></a>Próximos passos

Se estiver foram não é possível encontrar uma solução para o seu problema acima, eis alguns outros recursos que pode experimentar.

- [Blogues]
- [Pedidos de funcionalidade]
- [Vídeos]
- [Blogues da equipa de gato]
- [Criar bilhetes de suporte]
- [Fórum MSDN]
- [Fórum de área de excesso de pilha]
- [Twitter]

<!--Image references-->

<!--Article references-->
[Descrição geral de segurança]: ./sql-data-warehouse-overview-manage-security.md
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx
[SSDT para Visual Studio 2015]: ./sql-data-warehouse-install-visual-studio.md
[Controladores para armazém de dados do Azure SQL]: ./sql-data-warehouse-connection-strings.md
[Ligar ao armazenamento de dados do Azure SQL]: ./sql-data-warehouse-connect-overview.md
[Criar bilhetes de suporte]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Dimensionamento seu armazém de dados SQL]: ./sql-data-warehouse-manage-compute-overview.md
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[pedir um aumento de quota]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change 
[Aprender a monitorizar as suas consultas]: ./sql-data-warehouse-manage-monitor.md
[Instruções de aprovisionamento]: ./sql-data-warehouse-get-started-provision.md
[Configurar o acesso ao servidor da firewall para o seu IP de cliente]: ./sql-data-warehouse-get-started-provision.md#create-a-new-azure-sql-server-level-firewall
[Práticas recomendadas do armazém de dados SQL]: ./sql-data-warehouse-best-practices.md
[Tamanhos de tabela]: ./sql-data-warehouse-tables-overview.md#table-size-queries
[Funcionalidades de tabela não suportadas]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Tipos de dados não suportadas]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Columnstore originar má qualidade de índice remissivo]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Reconstruir índices para melhorar a qualidade do segmento]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Gestão de carga de trabalho]: ./sql-data-warehouse-develop-concurrency.md
[Utilizar CTAS para contornar sintaxe ATUALIZAR e eliminar não suportada]: ./sql-data-warehouse-develop-ctas.md#using-ctas-to-work-around-unsupported-features
[Soluções de actualização]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[ELIMINAR soluções]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[Soluções de impressão em série]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[Limitações de procedimento armazenado]: ./sql-data-warehouse-develop-stored-procedures.md#limitations
[Autenticação para armazém de dados do Azure SQL]: ./sql-data-warehouse-authentication.md
[Trabalhar em torno o requisito de PolyBase UTF-8]: ./sql-data-warehouse-load-polybase-guide.md#working-around-the-polybase-utf-8-requirement

<!--MSDN references-->
[sys.database_principals]: https://msdn.microsoft.com/library/ms187328.aspx
[CRIAR FUNÇÃO]: https://msdn.microsoft.com/library/mt203952.aspx
[SQLCMD]: https://azure.microsoft.com/en-us/documentation/articles/sql-data-warehouse-get-started-connect-sqlcmd/

<!--Other Web references-->
[Blogues]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogues da equipa de gato]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Pedidos de funcionalidade]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum MSDN]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse
[Fórum de área de excesso de pilha]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vídeos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse

