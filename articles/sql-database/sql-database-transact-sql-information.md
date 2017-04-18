<properties
   pageTitle="Não suportado no Azure SQL da base de dados T-SQL | Microsoft Azure"
   description="Declarações de Transact-SQL que são menos de totalmente suportadas na base de dados do SQL Azure"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="08/30/2016"
   ms.author="rick.byham@microsoft.com"/>

# <a name="azure-sql-database-transact-sql-differences"></a>Diferenças de base de dados de SQL Transact-SQL Azure


A maioria das funcionalidades Transact-SQL que dependem das aplicações é suportada no Microsoft SQL Server e base de dados do SQL Azure. Segue-se uma lista parcial das funcionalidades suportadas para aplicações:

- Tipos de dados.
- Operadores.
- Funções de cadeia, a média aritmética lógica e cursor.

No entanto, base de dados do SQL Azure foi concebido para identificar funcionalidades a partir de qualquer dependência de base de dados **principal** . Como consequência muitas actividades de nível do servidor são inapropriadas para base de dados SQL e não são suportadas. Funcionalidades que são preteridas no SQL Server não são suportadas geralmente na base de dados SQL.

> [AZURE.NOTE]
> Este tópico aborda as funcionalidades que estão disponíveis com a base de dados do SQL quando atualizado para a versão atual; V12 de base de dados do SQL. Para mais informações sobre V12, consulte o artigo [do SQL da base de dados V12 o que novo](sql-database-v12-whats-new.md).

As secções seguintes apresentam funcionalidades que são parcialmente suportadas e as funcionalidades que não são totalmente suportadas.


## <a name="features-partially-supported-in-sql-database-v12"></a>Funcionalidades parcialmente suportadas no V12 de base de dados SQL

V12 de base de dados do SQL suporta mas não em todos os argumentos que existam nas correspondente demonstrações SQL Server 2016 Transact-SQL. Por exemplo, a instrução CREATE PROCEDURE está disponível no entanto, todas as opções do procedimento criar não estão disponíveis. Consulte os tópicos de sintaxe ligadas para obter detalhes sobre as áreas de cada instrução suportadas.

- Bases de dados: [Criar](https://msdn.microsoft.com/library/dn268335.aspx )/[ALTER](https://msdn.microsoft.com/library/ms174269.aspx)
- DMVs geral, estão disponíveis para funcionalidades que estão disponíveis.
- Funções: [Criar](https://msdn.microsoft.com/library/ms186755.aspx)/[ALTER função](https://msdn.microsoft.com/library/ms186967.aspx)
- [ELIMINAR](https://msdn.microsoft.com/library/ms173730.aspx) 
- Inícios de sessão: [Criar](https://msdn.microsoft.com/library/ms189751.aspx)/[ALTER início de sessão](https://msdn.microsoft.com/library/ms189828.aspx)
- Procedimentos armazenados: [Criar](https://msdn.microsoft.com/library/ms187926.aspx)/[ALTER PROCEDURE](https://msdn.microsoft.com/library/ms189762.aspx)
- Tabelas: [Criar](https://msdn.microsoft.com/library/dn305849.aspx)/[ALTER](https://msdn.microsoft.com/library/ms190273.aspx)
- Tipos de (personalizado): [Criar o tipo](https://msdn.microsoft.com/library/ms175007.aspx)
- Utilizadores: [Criar](https://msdn.microsoft.com/library/ms173463.aspx)/[ALTER utilizador](https://msdn.microsoft.com/library/ms176060.aspx)
- Vistas: [Criar](https://msdn.microsoft.com/library/ms187956.aspx)/[alterar vista](https://msdn.microsoft.com/library/ms173846.aspx)

## <a name="features-not-supported-in-sql-database"></a>Funcionalidades não suportadas na base de dados SQL

- Agrupamento de objetos do sistema
- Ligação relacionados com: declarações de ponto final, ORIGINAL_DB_NAME. Base de dados SQL não suporta a autenticação do Windows, mas o suporta a autenticação do Azure Active Directory semelhante. Alguns tipos de autenticação requerem a versão mais recente do SSMS. Para mais informações, consulte o artigo [ligar a base de dados SQL ou SQL dados armazém ao utilizar o Azure autenticação do Active Directory](sql-database-aad-authentication.md).
- Cruzada consultas de base de dados com três ou quatro nomes de uma parte. (Consultas de base de dados em várias só de leitura são suportadas utilizando [a consulta de base de dados flexível](sql-database-elastic-query-overview.md).)
- Interligação de que é o proprietário da base de dados cruzada, definição fidedigna
- Recolha
- Diagramas de base de dados
- Correio de base de dados
- DATABASEPROPERTY (DATABASEPROPERTYEX como alternativa, utilize)
- Os inícios de sessão como executar
- Encriptação: gestão de chave extensible
- Eventos: eventos, as notificações de eventos, as notificações de consulta
- Funcionalidades relacionadas com o posicionamento de ficheiro de base de dados, tamanho e ficheiros de base de dados que são automaticamente geridos pelo Microsoft Azure.
- Funcionalidades que se relacionam com elevada disponibilidade, que é gerida através da sua conta do Microsoft Azure: fazer cópia de segurança, restaurar, AlwaysOn, espelhando as bases de dados, registo de envio, modos de recuperação. Para obter mais informações, consulte o artigo cópia de segurança de base de dados do SQL Azure e restaurar.
- Funcionalidades que dependem o leitor de registo ser executada numa base de dados SQL: replicação de notificações Push, alterar dados capturar.
- Funcionalidades que dependem o SQL Server Agent ou base de dados MSDB: tarefas, alertas, operadores, com base em política de gestão, correio da base de dados, servidores de gestão central.
- FILESTREAM
- Funções: fn_get_sql fn_virtualfilestats, fn_virtualservernodes
- Tabelas temporárias globais
- Definições do servidor relacionadas com o hardware: memória, threads de trabalho, afinidade da CPU, sinalizadores de rastreio, etc. Utilize níveis de serviço.
- HAS_DBACCESS
- ELIMINAR A TAREFA DE ESTATÍSTICA
- Servidores ligados, OPENQUERY, OPENROWSET, OPENDATASOURCE, inserir em volume e nomes de quatro partes
- Servidores de mestre/destino
- .NET framework [integração do CLR com o SQL Server](http://msdn.microsoft.com/library/ms254963.aspx)
- Governador de recursos
- Pesquisa semântica
- Credenciais de servidor. Base de dados de utilização limitadas ao credenciais em vez disso.
- Itens de nível Server: servidor funções, IS_SRVROLEMEMBER, sys.login_token. Permissões ao nível do servidor não estão disponíveis, embora algumas são substituídas pelos permissões ao nível da base de dados. Algumas DMVs ao nível do servidor não estão disponíveis, embora algumas são substituídas pelos DMVs de nível de base de dados.
- Sem servidor express: localdb, instâncias do utilizador
- Corretor de serviço
- DEFINIR REMOTE_PROC_TRANSACTIONS
- ENCERRAMENTO
- sp_addmessage
- Opções de sp_configure e RECONFIGURE. Algumas opções estão disponíveis utilizando [ALTER configuração em CONFINADOS da base de dados](https://msdn.microsoft.com/library/mt629158.aspx).
- sp_helpuser
- sp_migrate_user_to_contained
- Auditoria do SQL Server. Utilize a base de dados SQL auditoria em vez disso.
- Gerador de perfis do SQL Server
- Rastreio do SQL Server
- Rastrear sinalizadores. Alguns itens de sinalizador rastreio foram movidos para modos de compatibilidade.
- Depuração Transact-SQL
- Accionadores: Confinados servidor ou accionadores de início de sessão
- Declaração de utilização: para alterar o contexto da base de dados para uma base de dados diferente, terá de fazer uma nova ligação para a nova base de dados.


## <a name="full-transact-sql-reference"></a>Referência de Transact-SQL completa

Para mais informações sobre gramática, a utilização e exemplos Transact-SQL, consulte o artigo [Referência Transact-SQL (motor de base de dados)](https://msdn.microsoft.com/library/bb510741.aspx) no SQL Server Books Online. 

### <a name="about-the-applies-to-tags"></a>Sobre as marcas de "Aplica-se ao"

A referência de Transact-SQL inclui tópicos relacionados com versões do SQL Server 2008 para o apresentar. Por baixo do título de tópico aí é um ícone da barra, listar as quatro plataformas do SQL Server e indicar que a aplicação. Por exemplo, grupos de disponibilidade foram introduzidos no SQL Server 2012. O tópico [Criar grupo AVAILABILTY](https://msdn.microsoft.com/library/ff878399.aspx) indica que aplica-se para a declaração de * * do SQL Server (começando 2012). A declaração de não se aplica ao SQL Server 2008, SQL Server 2008 R2, base de dados do SQL Azure, armazém de dados do SQL Azure ou Parallel Data Warehouse.

Em alguns casos, o assunto geral de um tópico pode ser utilizado num produto, mas existem secundárias diferenças entre os produtos. As diferenças estão indicadas na pontos intermédios no tópico conforme adequado.

