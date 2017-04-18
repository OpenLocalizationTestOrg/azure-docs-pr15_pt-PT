<properties
   pageTitle="Directrizes de segurança da base de dados Azure SQL e limitações | Microsoft Azure"
   description="Saiba mais sobre as diretrizes de base de dados do Microsoft Azure SQL e limitações relacionadas com a segurança."
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
   ms.date="10/18/2016"
   ms.author="rickbyh"/>

# <a name="azure-sql-database-security-guidelines-and-limitations"></a>Diretrizes de segurança de base de dados SQL Azure e limitações

Este tópico descreve as diretrizes de base de dados do Microsoft Azure SQL e limitações relacionadas com a segurança. Considere os seguintes pontos ao gerir a segurança das suas bases de dados do SQL Azure.

## <a name="access-to-the-virtual-master-database"></a>Acesso à base de dados principal virtual

Normalmente, apenas os administradores precisam de aceder à base de dados principal. Manutenção de rotina acesso para cada base de dados de utilizador deve ser através de utilizadores que não seja administrador contidas base de dados criados cada base de dados. Quando utiliza os utilizadores de base de dados contidas, não necessita de criar inícios de sessão da base de dados principal. Para mais informações, consulte [Contidas da base de dados utilizadores - efetuar a base de dados portátil](https://msdn.microsoft.com/library/ff929188.aspx).


## <a name="firewall"></a>Firewall

Firewall do SQL Server, é delimitado para todo o servidor de SQL Azure normalmente, é configurado através do portal e só deverá admitir os endereços IP utilizados pelos administradores. Para criar uma regra de firewall confinados de base de dados que se abre o necessárias intervalo de endereços IP para cada base de dados, ligar a uma base de dados de utilizador e, em seguida, utilizar [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) instrução Transact-SQL.

O serviço de base de dados do Azure SQL só está disponível através da porta TCP 1433. Para aceder a uma base de dados SQL do seu computador, certifique-se de que a firewall do computador cliente permite o envio de TCP de comunicação, na porta TCP 1433. Se não for necessário para outras aplicações, bloquear as ligações de entrada na porta TCP 1433. 

Como parte do processo de ligação, ligações a partir do Azure máquinas virtuais são redireccionadas para um endereço IP diferente e uma porta, exclusiva para cada função de trabalho. O número da porta está no intervalo de 11000 para 11999. Para mais informações sobre as portas TCP, consulte [portas para além das 1433 para ADO.NET 4,5 e V12 de base de dados do SQL](sql-database-develop-direct-route-ports-adonet-v12.md).


## <a name="authentication"></a>Autenticação

Utilizar a autenticação do Active Directory (segurança integrada) sempre que possível. Para obter informações sobre como configurar a autenticação AD, consulte o artigo [ligar ao SQL da base de dados ao utilizar o Azure autenticação do Active Directory](sql-database-aad-authentication.md)e, [ao selecionar um modo de autenticação](https://msdn.microsoft.com/library/ms144284.aspx) no SQL Server Books Online. 

Utilize a base de dados contidas utilizadores para melhorar escalabilidade. Para mais informações, consulte o artigo [Contidas da base de dados utilizadores - efetuar a base de dados portátil](https://msdn.microsoft.com/library/ff929188.aspx), [CREATE USER (Transact-SQL)](https://technet.microsoft.com/library/ms173463.aspx)e [Bases de dados contidos](https://technet.microsoft.com/library/ff929071.aspx).

O motor de base de dados fecha ligações que permanecem idle durante mais de 30 minutos. A ligação deve início de sessão novamente antes de poder ser utilizada.

Continuamente ligações activas a base de dados SQL exigem reauthorization (executado pelo motor de base de dados) pelo menos a cada 10 horas. O motor de base de dados tenta reauthorization utilizando a palavra-passe originalmente submetida e não é necessária nenhuma entrada do utilizador. Por razões de desempenho, quando uma palavra-passe for reposta numa base de dados do SQL, a ligação não é possível novamente autenticado, mesmo se a ligação foi reposta devido ao agrupamento de ligações. Este é diferente do comportamento no local SQL Server. Se a palavra-passe foi alterada desde a ligação foi autorizada inicialmente, a ligação deve ser terminada e uma nova ligação efetuada utilizando a nova palavra-passe. Um utilizador com permissão de ligação de base de dados eliminar explicitamente pode terminar uma ligação à base de dados SQL utilizando o comando [Eliminar](https://msdn.microsoft.com/library/ms173730.aspx) .

## <a name="logins-and-users"></a>Inícios de sessão e de utilizadores

Quando a gerir os inícios de sessão e os utilizadores na base de dados SQL, existem restrições.


- Tem de estar ligado à base de dados **principal** ao executar o ``CREATE/ALTER/DROP DATABASE`` declarações. -Não pode ser alterado ou largado o utilizador de base de dados na base de dados principal correspondente para o início de sessão principal ao nível do servidor. 
- US inglês são o idioma predefinido do login principal ao nível do servidor.
- Apenas os administradores (principal ao nível do servidor de início de sessão ou administrador do Azure AD) e os membros da função **Gestor de BD** de base de dados na base de dados **principal** de tem permissão para executar o ``CREATE DATABASE`` e ``DROP DATABASE`` declarações.
- Tem de estar ligado à base de dados principal ao executar o ``CREATE/ALTER/DROP LOGIN`` declarações. No entanto utilizar os inícios de sessão não é aconselhada. Utilize os utilizadores de base de dados contidas.
- Para ligar a uma base de dados de utilizador, tem de fornecer o nome da base de dados na cadeia de ligação.
- Apenas o início de sessão principal ao nível do servidor e os membros da função **loginmanager** de base de dados na base de dados **principal** de tem permissão para executar o ``CREATE LOGIN``, ``ALTER LOGIN``, e ``DROP LOGIN`` declarações.
- Ao executar o ``CREATE/ALTER/DROP LOGIN`` e ``CREATE/ALTER/DROP DATABASE`` demonstrações numa aplicação ADO.NET, utilizando os comandos parametrizados não é permitida. Para mais informações, consulte o artigo [comandos e parâmetros](https://msdn.microsoft.com/library/ms254953.aspx).
- Ao executar o ``CREATE/ALTER/DROP DATABASE`` e ``CREATE/ALTER/DROP LOGIN`` instruções, cada uma das seguintes declarações têm de ser a instrução apenas um lote de Transact-SQL. Caso contrário, ocorre um erro. Por exemplo, a seguinte Transact-SQL verifica se existe a base de dados. Se existir, um ``DROP DATABASE`` declaração chama-se para remover a base de dados. Uma vez que o ``DROP DATABASE`` declaração não estiver a declaração apenas no lote de, executar a seguinte instrução Transact-SQL resulta num erro.

```
IF EXISTS (SELECT [name]
           FROM   [sys].[databases]
           WHERE  [name] = N'database_name')
     DROP DATABASE [database_name];
GO
```

- Ao executar o ``CREATE USER`` instrução com o ``FOR/FROM LOGIN`` opção, tem de ser a instrução apenas um lote de Transact-SQL.
- Ao executar o ``ALTER USER`` instrução com o ``WITH LOGIN`` opção, tem de ser a instrução apenas um lote de Transact-SQL.
- Para ``CREATE/ALTER/DROP`` necessita de um utilizador a ``ALTER ANY USER`` permissão da base de dados.
- Quando tenta o proprietário de uma função de base de dados adicionar ou remover a outro utilizador de base de dados de ou para essa função base de dados, pode ocorrer o seguinte erro: **utilizador ou função 'Nome' não existir na base de dados.** Este erro ocorre porque o utilizador não está visível para o proprietário. Para resolver este problema, conceder o proprietário da função a ``VIEW DEFINITION`` permissão no utilizador. 

Para mais informações sobre os inícios de sessão e de utilizadores, consulte o artigo [Gerir bases de dados e inícios de sessão de base de dados do SQL Azure](sql-database-manage-logins.md).


## <a name="see-also"></a>Consulte também

[Firewall de base de dados do Azure SQL](sql-database-firewall-configure.md)

[Como: Configurar definições da Firewall (base de dados do Azure SQL)](sql-database-configure-firewall-settings.md)

[Gerir bases de dados e inícios de sessão de base de dados do SQL Azure](sql-database-manage-logins.md)

[Centro de segurança do motor de base de dados do SQL Server e base de dados Azure SQL](https://msdn.microsoft.com/library/bb510589)
