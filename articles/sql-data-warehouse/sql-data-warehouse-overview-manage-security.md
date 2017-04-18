<properties
   pageTitle="Proteger uma base de dados no armazém de dados do SQL | Microsoft Azure"
   description="Sugestões para proteger uma base de dados no armazém de dados do SQL Azure para desenvolver soluções."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/24/2016"
   ms.author="rortloff;barbkess;sonyama"/>

# <a name="secure-a-database-in-sql-data-warehouse"></a>Proteger uma base de dados no armazém de dados SQL

> [AZURE.SELECTOR]
- [Descrição geral de segurança](sql-data-warehouse-overview-manage-security.md)
- [Autenticação](sql-data-warehouse-authentication.md)
- [Encriptação (Portal)](sql-data-warehouse-encryption-tde.md)
- [Encriptação (T SQL)](sql-data-warehouse-encryption-tde-tsql.md)

Este artigo explica os princípios básicos para proteger a sua base de dados do armazém de dados do SQL Azure. Em particular, este artigo irá começar a com recursos de limitação de acesso, proteger dados e monitorizar atividades numa base de dados.

## <a name="connection-security"></a>Segurança de ligação

Segurança de ligação que se refere a como restringir e ligações seguras a sua base de dados utilizando regras de firewall e encriptação de ligação.

Regras de firewall são utilizadas pelo servidor e a base de dados para rejeitar tentativas de ligação proveniente de endereços IP que não foram explicitamente whitelisted. Para permitir ligações a partir da aplicação ou o endereço IP público do computador do cliente, primeiro tem de criar uma regra de nível do servidor firewall utilizando o Portal do Azure, REST API ou PowerShell. Como prática recomendada, deve restringir os intervalos de endereços IP permitidos através da firewall do seu servidor quanto possíveis.  Para aceder ao armazém de dados do SQL Azure a partir do seu computador local, certifique-se de que a firewall do seu computador local e de rede permite o envio comunicação na porta TCP 1433.  Para mais informações, consulte o artigo [firewall de base de dados do SQL Azure][], [sp_set_firewall_rule][]e [sp_set_database_firewall_rule][].

Ligações para o seu armazém de dados SQL são encriptadas por predefinição.  Modificar as definições de ligação para desativar a encriptação são ignoradas.

## <a name="authentication"></a>Autenticação

Autenticação refere-se como prova a sua identidade ao ligar à base de dados. Armazém de dados SQL atualmente suporta a autenticação do SQL Server com um nome de utilizador e palavra-passe, bem como Azure Active Directory. 

Quando criou o servidor lógico para a base de dados, que especificou um início de sessão "server admin" com um nome de utilizador e palavra-passe. Utilizar estas credenciais, é possível autenticar a qualquer base de dados nesse servidor como o proprietário da base de dados ou "dbo" através de autenticação do SQL Server.

No entanto, como prática recomendada, utilizadores da sua organização devem utilizar uma conta diferente para autenticar. Desta forma pode limitar as permissões atribuídas à aplicação e reduzir os riscos da atividade malicioso caso o código da aplicação não esteja vulnerável a um ataque de introdução de SQL. 

Para criar um utilizador autenticado do SQL Server, ligar à base de dados **principal** no servidor com o início de sessão do administrador do servidor e crie um novo início de sessão do servidor.  Para além disso, é aconselhável criar um utilizador da base de dados principal para os utilizadores do armazém de dados do SQL Azure. Criar um utilizador no modelo global permite que um utilizador para iniciar sessão utilizando ferramentas como SSMS sem especificar um nome de base de dados.  Também permite-lhes utilizar o Explorador de objeto para ver todas as bases de dados no SQL server.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Em seguida, ligar a sua **base de dados do SQL armazém de dados** com o início de sessão do administrador do servidor e criar um utilizador de base de dados com base no início de sessão do servidor que acabou de criar.

```sql
-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Se um utilizador vai ser efetuar operações adicionais, tais como criar inícios de sessão ou crie novas bases de dados, também será necessário a serem atribuídas à `Loginmanager` e `dbmanager` funções numa base de dados principal. Para mais informações sobre estas funções adicionais e autenticar a uma base de dados do SQL, consulte o artigo [Gerir bases de dados e inícios de sessão de base de dados do SQL Azure][].  Para obter mais detalhes sobre Azure AD para armazém de dados SQL, consulte o artigo [ligar ao SQL dados armazém ao utilizar o Azure autenticação do Active Directory][].


## <a name="authorization"></a>Autorização

Autorização refere-se para o que pode fazer dentro de uma base de dados do armazém de dados do SQL Azure e, isto é controlado pelo associações a sua conta de utilizador uma função e permissões. Como prática recomendada, deverá conceder aos utilizadores os privilégios, pelo menos, necessários. Armazém de dados SQL Azure facilita a isto gerir com funções no T-SQL:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

A conta de administrador do servidor que está a ligar é um membro do proprietário, que possui autoridade de fazer nada dentro da base de dados. Guarde esta conta para implementar actualizações de esquema e outras operações de gestão. Utilize a conta "ApplicationUser" com permissões mais limitadas para ligar a partir da sua aplicação para a base de dados com o mínimo de privilégios necessário a sua aplicação.

Existem formas de limitar ainda mais o um utilizador pode fazer com a base de dados do Azure SQL:

- Granular [permissões][] permitem-lhe controlar quais as operações que pode fazer no colunas individuais, tabelas, vistas, procedimentos e outros objetos na base de dados. Utilize permissões granulares para ter maior controlo e conceder as permissões mínimas necessárias. O sistema de permissão granular está um pouco complicado e irão necessitar algumas prático utilizar de forma eficaz.
- [Funções de base de dados][] que não seja db_datareader e db_datawriter podem ser utilizados para criar mais eficazes contas de utilizador de aplicações ou menos poderosas contas de gestão. As funções de base de dados incorporado fixo fornecem uma forma fácil de conceder permissões, mas podem resultar em conceder permissões mais do que são necessários.
- [Procedimentos armazenados][] podem ser utilizados para limitar as ações que podem ser retiradas da base de dados.

Gerir bases de dados e os servidores de lógicos a partir do Portal de clássica Azure ou utilizar a API do Gestor de recursos do Azure é controlada pelo atribuições de funções da sua conta de utilizador do portal. Para obter mais informações sobre este tópico, consulte o [controlo de acesso baseado em funções no Portal do Azure][].

## <a name="encryption"></a>Encriptação

Azure SQL dados armazém transparente dados encriptação (TDE) ajuda a proteger contra a ameaça de atividade maliciosa executando encriptação em tempo real e desencriptar dos dados na restantes.  Quando encriptar a base de dados, associadas cópias de segurança e ficheiros de registo da transação são encriptados sem necessidade de quaisquer alterações às suas aplicações. TDE encripta o armazenamento dos toda uma base de dados utilizando uma chave simétrica denominada a chave de encriptação da base de dados. Base de dados SQL a chave de encriptação da base de dados está protegida por um certificado de servidor incorporados. O certificado de servidor incorporados é exclusivo para cada servidor de base de dados SQL. Microsoft roda automaticamente estes certificados pelo menos a cada cerca de 90 dias. O algoritmo de encriptação utilizado pelo armazém de dados SQL é AES 256. Para obter uma descrição geral de TDE, consulte o artigo [Transparente encriptação de dados][].

Pode encriptar a base de dados utilizando o [Portal do Azure] [ Encryption with Portal] ou [T-SQL][Encryption with TSQL].

## <a name="next-steps"></a>Próximos passos

Para detalhes e exemplos sobre como ligar para o seu armazém de dados SQL com protocolos diferentes, consulte o artigo [ligar ao armazenamento de dados SQL][].

<!--Image references-->

<!--Article references-->
[Ligar ao armazenamento de dados SQL]: ./sql-data-warehouse-connect-overview.md
[Encryption with Portal]: ./sql-data-warehouse-encryption-tde.md
[Encryption with TSQL]: ./sql-data-warehouse-encryption-tde-tsql.md
[Estabelecer ligação ao armazém de dados SQL utilizando a autenticação do Azure Active Directory]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[Firewall de base de dados SQL Azure]: https://msdn.microsoft.com/library/ee621782.aspx
[sp_set_firewall_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp_set_database_firewall_rule]: https://msdn.microsoft.com/library/dn270010.aspx
[Funções de base de dados]: https://msdn.microsoft.com/library/ms189121.aspx
[Gerir bases de dados e inícios de sessão de base de dados do SQL Azure]: https://msdn.microsoft.com/library/ee336235.aspx
[Permissões]: https://msdn.microsoft.com/library/ms191291.aspx
[Procedimentos armazenados]: https://msdn.microsoft.com/library/ms190782.aspx
[Encriptação de dados transparente]: https://msdn.microsoft.com/library/bb934049.aspx
[Azure portal]: https://portal.azure.com/

<!--Other Web references-->
[Controlo de acesso baseado em funções no Portal do Azure]: https://azure.microsoft.com/documentation/articles/role-based-access-control-configure
