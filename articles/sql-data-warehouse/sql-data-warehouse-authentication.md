<properties
   pageTitle="Autenticação para armazém de dados do Azure SQL | Microsoft Azure"
   description="Azure autenticação para armazém de dados do SQL Azure Active Directory (AAD) e o SQL Server."
   services="sql-data-warehouse"
   documentationCenter=""
   authors="byham"
   manager="barbkess"
   editor=""
   tags=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/24/2016"
   ms.author="rickbyh;barbkess;sonyama"/>

# <a name="authentication-to-azure-sql-data-warehouse"></a>Autenticação para armazém de dados do Azure SQL

> [AZURE.SELECTOR]
- [Descrição geral de segurança](sql-data-warehouse-overview-manage-security.md)
- [Autenticação](sql-data-warehouse-authentication.md)
- [Encriptação (Portal)](sql-data-warehouse-encryption-tde.md)
- [Encriptação (T SQL)](sql-data-warehouse-encryption-tde-tsql.md)

Para ligar ao armazenamento de dados SQL, tem de passar as credenciais de segurança para fins de autenticação. Relativamente a estabelecer uma ligação, algumas definições de ligação estão configuradas como parte de estabelecer a sua sessão de consulta.  

Para mais informações sobre segurança e sobre como ativar as ligações para o seu armazém de dados, consulte o artigo [seguro uma base de dados no armazém de dados do SQL][].

## <a name="sql-authentication"></a>Autenticação do SQL
Para ligar ao armazenamento de dados SQL, tem de fornecer as seguintes informações:

- Nomedoservidor completamente qualificado
- Especificar a autenticação de SQL
- Nome de utilizador
- Palavra-passe
- (Opcional) da base de dados predefinido

Por predefinição a sua ligação liga-se para a base de dados *principal* e não a base de dados do utilizador. Para ligar à sua base de dados de utilizador, pode escolher executar uma das duas coisas:

- Especifique a base de dados predefinido quando registar o seu servidor com o Explorador do SQL Server objeto no SSDT, SSMS, ou na sua cadeia de ligação de aplicação. Por exemplo, inclua o parâmetro InitialCatalog necessário para uma ligação ODBC.
- Realce a base de dados do utilizador antes de criar uma sessão em SSDT.

> [AZURE.NOTE] A declaração de Transact-SQL **Minhabasededados utilização;** não é suportada para alterar a base de dados de uma ligação. Para obter orientações estabelecer ligação ao armazém de dados SQL com SSDT, consulte o artigo de [consulta com o Visual Studio][] .

## <a name="azure-active-directory-aad-authentication"></a>Autenticação do Azure Active Directory (AAD)

[Azure Active Directory] [ What is Azure Active Directory] autenticação é um mecanismo de estabelecer ligação ao armazém de dados do Microsoft Azure SQL utilizando identidades no Azure Active Directory (Azure AD). Com a autenticação do Azure Active Directory, pode gerir centralmente as identidades dos utilizadores de base de dados e outros serviços da Microsoft numa localização central. Gestão central de ID proporciona um só local para gerir os utilizadores do armazém de dados do SQL e simplifica a gestão de permissões. 

### <a name="benefits"></a>Benefícios

Benefícios do Azure Active Directory incluem:

- Fornece uma alternativa a autenticação do SQL Server.
- Ajuda a impedir que o multiplicação de identidades de utilizador por servidores de base de dados.
- Permite a rotação de palavra-passe num único local
- Gerir permissões de base de dados utilizando externos (AAD) grupos.
- Elimina armazenar palavras-passe ao ativar integrada autenticação do Windows e outras formas de autenticação suportados pelo Azure Active Directory.
- Utilizadores de base de dados de utilizações contidas para autenticar identidades ao nível da base de dados.
- Suporta autenticação baseada em token para estabelecer ligação ao armazém de dados SQL de aplicações.
- Suporta a autenticação Multifator através de autenticação Universal do Active Directory para SQL Server Management Studio. Para obter uma descrição de autenticação Multifator, consulte o artigo [SSMS suporte para o Azure AD MFA com base de dados SQL e armazém de dados SQL](../sql-database/sql-database-ssms-mfa-authentication.md).

> [AZURE.NOTE] Azure Active Directory é ainda relativamente novo e tem algumas limitações. Para se certificar de que o Azure Active Directory é uma boa opção para o seu ambiente, consulte o artigo [Azure AD funcionalidades e limitações][], especificamente as considerações adicionais.

### <a name="configuration-steps"></a>Passos de configuração

Siga estes passos para configurar a autenticação do Azure Active Directory.

1. Criar e preencher um Azure Active Directory
2. Opcional: Associar ou alterar o active directory que esteja atualmente associado com a sua subscrição do Azure
3. Crie um administrador do Azure Active Directory do armazém de dados do SQL Azure.
4. Configure os computadores de cliente
5. Criar utilizadores de base de dados contidas na base de dados mapeado para identidades do Azure AD
6. Ligar para o seu armazém de dados com identidades do Azure AD

Atualmente, os utilizadores do Azure Active Directory não são apresentados no Explorador do objeto SSDT. Como solução, veja os utilizadores na [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).
  
### <a name="find-the-details"></a>Localizar os detalhes
- Conclua os passos detalhados. Os passos detalhados para configurar e utilizar a autenticação do Azure Active Directory são praticamente idênticos para base de dados do SQL Azure e armazém de dados do SQL Azure. Siga os passos detalhados no tópico [para ligar à base de dados SQL ou SQL dados armazém ao utilizar o Azure autenticação do Active Directory](../sql-database/sql-database-aad-authentication.md).
- Criar funções de base de dados personalizados e adicionar utilizadores às funções. Em seguida, conceda permissões granulares às funções. Para mais informações, consulte o artigo [Introdução ao permissões do motor de base de dados](https://msdn.microsoft.com/library/mt667986.aspx).

## <a name="next-steps"></a>Próximos passos

Para começar a consultar o seu armazém de dados com Visual Studio e outras aplicações, consulte o artigo [consulta com o Visual Studio][].

<!-- Article references -->
[Proteger uma base de dados no armazém de dados SQL]: ./sql-data-warehouse-overview-manage-security.md
[Consulta com o Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[What is Azure Active Directory]: ../active-directory/active-directory-whatis.md
[Azure AD funcionalidades e limitações]: ../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations
