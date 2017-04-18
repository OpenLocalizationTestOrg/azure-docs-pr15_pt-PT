<properties
   pageTitle="Descrição geral de segurança de base de dados SQL"
   description="Saiba mais sobre segurança de base de dados do SQL Azure e o SQL Server, incluindo as diferenças entre a nuvem e do SQL Server no local quando chegar para autenticação, autorização, segurança de ligação, encriptação e conformidade."
   services="sql-database"
   documentationCenter=""
   authors="tmullaney"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="06/09/2016"
   ms.author="thmullan;jackr"/>


# <a name="securing-your-sql-database"></a>Proteger a sua base de dados SQL

## <a name="overview"></a>Descrição geral

Este artigo explica os princípios básicos para proteger a camada de dados de uma aplicação utilizando a base de dados do SQL Azure. Em particular, este artigo irá ajudar a começar com recursos de limitação de acesso, proteger os dados, e monitorização atividades numa base de dados criados no [artigo Introdução ao tutorial de base de dados SQL](sql-database-get-started.md). Para obter uma descrição completa das funcionalidades de segurança disponíveis em todas as versões do SQL, consulte o [Centro de segurança do motor de base de dados do SQL Server e base de dados do SQL Azure](https://msdn.microsoft.com/library/bb510589). Informações adicionais também estão disponíveis no [segurança e base de dados do SQL Azure documento técnico](https://download.microsoft.com/download/A/C/3/AC305059-2B3F-4B08-9952-34CDCA8115A9/Security_and_Azure_SQL_Database_White_paper.pdf) (PDF).

## <a name="connection-security"></a>Segurança de ligação

Segurança de ligação que se refere a como restringir e ligações seguras a sua base de dados utilizando regras de firewall e encriptação de ligação.

Regras de firewall são utilizadas pelo servidor e a base de dados para rejeitar tentativas de ligação proveniente de endereços IP que não foram explicitamente whitelisted. Para permitir que a aplicação ou o endereço IP público do computador do cliente para tentar ligar-se para uma nova base de dados, primeiro tem de criar uma regra de nível do servidor firewall utilizando o Portal clássica Azure, REST API ou PowerShell. Como prática recomendada, deve restringir os intervalos de endereços IP permitidos através da firewall do seu servidor quanto possíveis. Para mais informações, consulte o artigo [Firewall de base de dados do SQL Azure](https://msdn.microsoft.com/library/ee621782).

Todas as ligações de base de dados do SQL Azure requerem encriptação (SSL/TLS) em todas as horas enquanto dados são "em trânsito" e da base de dados. Na cadeia de ligação da sua aplicação, tem de especificar parâmetros para encriptar a ligação e *não* para confiar no certificado de servidor (isto é feito se copiar a cadeia de ligação terminar Portal clássica do Azure), caso contrário, a ligação será não verificar a identidade do servidor e serão sensíveis a ataques "Homem-em-de-meio". Para o controlador ADO.NET, por exemplo, estes cadeia parâmetros de ligação são **encriptar = True** e **TrustServerCertificate = False**. Para mais informações, consulte o artigo [encriptação de ligação do Azure SQL da base de dados e validação do certificado](https://msdn.microsoft.com/library/azure/ff394108#encryption).


## <a name="authentication"></a>Autenticação

Autenticação refere-se como prova a sua identidade ao ligar à base de dados. Base de dados SQL suporta dois tipos de autenticação:

 - **Autenticação do SQL**, que utiliza um nome de utilizador e palavra-passe
 - **Autenticação do azure Active Directory**, que utiliza identidades geridas pelo Azure Active Directory e é suportada para gerido e integradas domínios

Quando criou o servidor lógico para a base de dados, que especificou um início de sessão "server admin" com um nome de utilizador e palavra-passe. Utilizar estas credenciais, é possível autenticar quaisquer bases de dados nesse servidor como o proprietário da base de dados ou "dbo." Se pretende utilizar a autenticação do Azure Active Directory, terá de criar outro administrador do servidor denominado "Azure AD administrador," qual tem permissão para administrar Azure AD utilizadores e grupos. Este administrador também pode executar todas as operações que pode um administrador do servidor de normal. Consulte o artigo [ligar ao SQL da base de dados ao utilizar o Azure autenticação do Active Directory](sql-database-aad-authentication.md) para instruções sobre como criar um administrador do Azure AD para ativar a autenticação do Azure Active Directory.

Como prática recomendada a aplicação deve utilizar uma conta diferente para autenticar – desta forma, pode limitar as permissões atribuídas à aplicação e reduzir os riscos da atividade malicioso caso o código da aplicação não esteja vulnerável a um ataque de introdução de SQL. É a abordagem recomendada criar um [contidas utilizador de base de dados](https://msdn.microsoft.com/library/ff929188), que permite a sua aplicação autenticar diretamente para uma base de dados simples. Pode criar um utilizador de base de dados contidas que utiliza a autenticação de SQL executando o seguinte comando de T SQL enquanto estiver ligado a sua base de dados de utilizador como o início de sessão do administrador do servidor:

```
CREATE USER ApplicationUser WITH PASSWORD = 'strong_password'; -- SQL Authentication
```

Se tiver criado um administrador do Azure AD, pode criar um utilizador de base de dados contidas que utiliza a autenticação do Azure Active Directory executando o seguinte comando de T SQL enquanto estiver ligado a sua base de dados de utilizador como administrador do Azure AD:

```
CREATE USER [Azure_AD_principal_name | Azure_AD_group_display_name] FROM EXTERNAL PROVIDER; -- Azure Active Directory Authentication
```

Em ambos os casos, a cadeia de ligação da sua aplicação deverá especificar estas credenciais de utilizador, em vez do servidor administração início de sessão, para ligar à base de dados.

Para mais informações sobre autenticar a uma base de dados do SQL, consulte o artigo [Gerir bases de dados e inícios de sessão de base de dados do SQL Azure](sql-database-manage-logins.md).


## <a name="authorization"></a>Autorização
Autorização refere-se para o que pode fazer dentro de uma base de dados do SQL Azure e, isto é controlado pelo associações a sua conta de utilizador uma função e permissões. Como prática recomendada, deverá conceder aos utilizadores os privilégios, pelo menos, necessários. Base de dados SQL Azure facilita a isto gerir com funções no T-SQL:

```
ALTER ROLE db_datareader ADD MEMBER ApplicationUser; -- allows ApplicationUser to read data
ALTER ROLE db_datawriter ADD MEMBER ApplicationUser; -- allows ApplicationUser to write data
```

A conta de administrador do servidor que está a ligar é um membro do proprietário, que possui autoridade de fazer nada dentro da base de dados. Guarde esta conta para implementar actualizações de esquema e outras operações de gestão. Utilize a conta "ApplicationUser" com permissões mais limitadas para ligar a partir da sua aplicação para a base de dados com o mínimo de privilégios necessário a sua aplicação.

Existem formas de limitar ainda mais o um utilizador pode fazer com a base de dados do Azure SQL:

* [Funções de base de dados](https://msdn.microsoft.com/library/ms189121) que não seja db_datareader e db_datawriter podem ser utilizados para criar mais eficazes contas de utilizador de aplicações ou menos poderosas contas de gestão.
* Granular [permissões](https://msdn.microsoft.com/library/ms191291) permitem-lhe controlar quais as operações que pode fazer no colunas individuais, tabelas, vistas, procedimentos e outros objetos na base de dados.
* [Representação](https://msdn.microsoft.com/library/vstudio/bb669087) e [Iniciar sessão módulo](https://msdn.microsoft.com/library/bb669102) podem ser utilizados para segura elevar temporariamente as permissões.
* Pode ser utilizada a [Segurança de nível de linha de](https://msdn.microsoft.com/library/dn765131) limite de linhas de um utilizador pode aceder.
* [Dados Masking](sql-database-dynamic-data-masking-get-started.md) podem ser utilizados para limitar a exposição de dados confidenciais.
* [Procedimentos armazenados](https://msdn.microsoft.com/library/ms190782) podem ser utilizados para limitar as ações que podem ser retiradas da base de dados.

Gerir bases de dados e os servidores de lógicos a partir do Portal de clássica Azure ou utilizar a API do Gestor de recursos do Azure é controlada pelo atribuições de funções da sua conta de utilizador do portal. Para obter mais informações sobre este tópico, consulte o [controlo de acesso baseado em funções no Portal do Azure](../active-directory./role-based-access-control-configure.md).


## <a name="encryption"></a>Encriptação

Base de dados SQL Azure pode ajudar a proteger os seus dados ao encriptá os seus dados quando este se encontra "resto" ou armazenados em ficheiros de base de dados e cópias de segurança, utilizando [Transparente encriptação de dados](http://go.microsoft.com/fwlink/?LinkId=526242). Para encriptar a base de dados, ligar-se como um proprietário da base de dados e executar:

```
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

Para obter outras formas encriptar o seu segredos de dados, considere:

* [Encriptação de nível de células](https://msdn.microsoft.com/library/ms179331.aspx) para encriptar colunas específicas ou mesmo as células de dados com chaves de encriptação diferentes.
* Se necessita de um módulo de Hardware de segurança ou gestão central da hierarquia de chave de encriptação, considere utilizar [Azure chave cofre com o SQL Server numa VM Azure](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx).
* [Sempre encriptados](https://msdn.microsoft.com/library/mt163865.aspx) (na pré-visualização) torna encriptação transparente para aplicações e permite que os clientes encriptar dados sensíveis a maiúsculas e dentro de aplicações de cliente sem partilhar as chaves de encriptação com base de dados SQL.

## <a name="auditing"></a>Auditoria

Auditoria e controlo eventos de base de dados podem ajudar a manter a conformidade de regulamentação e identificar actividades suspeitas. Auditoria de base de dados SQL permite-lhe gravar eventos na base de dados para uma auditoria iniciar sessão na sua conta de armazenamento do Windows Azure. Auditoria de base de dados SQL também integra-se com o Microsoft Power BI para facilitar a desagregação relatórios e análises. Para mais informações, consulte o artigo [Introdução ao auditoria de base de dados SQL](sql-database-auditing-get-started.md).

Deteção de ameaça de base de dados do SQL fornece uma camada adicional de segurança na parte superior de auditoria. Permite-lhe responder a ameaças à medida que estes ocorrem fornecendo alertas de segurança sobre atividades discordantes. Para mais informações, consulte o artigo [Introdução ao deteção de ameaça de base de dados SQL](sql-database-threat-detection-get-started.md).  

## <a name="compliance"></a>Conformidade

Para além das acima funcionalidades de que o podem ajudar a sua aplicação cumprir os requisitos de conformidade de segurança de vários, base de dados do SQL Azure também participa auditorias normais e tenha sido certificada contra um número de padrões de conformidade. Para mais informações, consulte o artigo do [Centro de fidedignidade do Microsoft Azure](https://azure.microsoft.com/support/trust-center/), onde pode encontrar a lista mais recente de [certificações de conformidade de base de dados SQL](https://azure.microsoft.com/support/trust-center/services/).
