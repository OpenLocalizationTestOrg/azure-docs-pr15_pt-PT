<properties
    pageTitle="Tutorial: Web app com uma base de dados do inquilino com várias utilizando entidade quadro e de segurança de nível de linha"
    description="Obter informações sobre como desenvolver uma aplicação web do ASP.NET MVC 5 com um inquilino com várias base de dados SQL backent, utilizar estrutura de entidades e segurança de nível de linha."
  metaKeywords="azure asp.net mvc entity framework multi tenant row level security rls sql database"
    services="app-service\web"
    documentationCenter=".net"
    manager="jeffreyg"
  authors="tmullaney"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="04/25/2016"
    ms.author="thmullan"/>

# <a name="tutorial-web-app-with-a-multi-tenant-database-using-entity-framework-and-row-level-security"></a>Tutorial: Web app com uma base de dados do inquilino com várias utilizando entidade quadro e de segurança de nível de linha

Este tutorial mostra como criar uma aplicação web do inquilino com várias com um modelo de arrendamento "[base de dados partilhada, esquema partilhada](https://msdn.microsoft.com/library/aa479086.aspx)" e utilizar estrutura de entidades e [Segurança de nível de linha](https://msdn.microsoft.com/library/dn765131.aspx). Neste modelo, uma base de dados única contém dados para muitos inquilinos e cada linha de cada tabela está associada a um "inquilino ID." Linha nível de segurança (RLS), uma nova funcionalidade para a base de dados SQL Azure, é utilizado para impedir que os inquilinos acedam uns dos outros dados. Isto requer apenas uma única, pequena alteração para a aplicação. Centralizando a lógica de acesso de inquilino dentro da base de dados, RLS simplifica o código da aplicação e reduz o risco de perda de dados acidentais entre inquilinos.

Vamos começar com a aplicação Contact Manager simples a partir de [criar uma aplicação do ASP.NET MVP com auth e DB do SQL e implementar a aplicação de serviço de Azure](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md). Direita agora, a aplicação permite que todos os utilizadores (inquilinos) para ver todos os contactos:

![Aplicação do Gestor de contacto antes de ativar RLS](./media/web-sites-dotnet-entity-framework-row-level-security/ContactManagerApp-Before.png)

Com apenas alguns pequenas alterações, vamos adicionar suporte para multitenancy, para que os utilizadores possam ver apenas os contactos que pertencem aos mesmos.

## <a name="step-1-add-an-interceptor-class-in-the-application-to-set-the-sessioncontext"></a>Passo 1: Adicionar uma classe Interceptor na aplicação para definir o SESSION_CONTEXT

Existe uma alteração de aplicação que precisamos de fazer. Uma vez que todos os utilizadores da aplicação ligarem à base de dados utilizando a mesma cadeia de ligação (ou seja, mesmo início de sessão SQL), atualmente existe nenhuma forma para uma política de RLS saber qual o utilizador deve filtrem. Esta abordagem é muito frequente nas aplicações web porque permite-eficiente de agrupamento de ligações, mas significa que precisamos de outra forma para identificar o utilizador aplicação actual dentro da base de dados. A solução é tem a aplicação de configurar um par de valor de chave para o ID do utilizador actual no [SESSION_CONTEXT](https://msdn.microsoft.com/library/mt590806) imediatamente depois de abrir uma ligação, antes de executar qualquer consultas. SESSION_CONTEXT é um arquivo de valor de chave confinados sessão e a nossa política RLS irá utilizar o ID de utilizador armazenado no mesmo para identificar o utilizador actual.

Vamos adicionar um [interceptor](https://msdn.microsoft.com/data/dn469464.aspx) (em particular, um [DbConnectionInterceptor](https://msdn.microsoft.com/library/system.data.entity.infrastructure.interception.idbconnectioninterceptor)), uma nova funcionalidade no âmbito (EF) 6, para configurar automaticamente o ID do utilizador actual na SESSION_CONTEXT através da execução de uma instrução de T SQL sempre EF abre uma ligação entidade.

1.  Abra o projeto ContactManager no Visual Studio.
2.  Botão direito do rato na pasta Modelos no Explorador de solução e selecione Adicionar > escolares.
3.  Atribua um nome da nova classe "SessionContextInterceptor.cs" e clique em Adicionar.
4.  Substitua os conteúdos de SessionContextInterceptor.cs o código seguinte.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Data.Common;
using System.Data.Entity;
using System.Data.Entity.Infrastructure.Interception;
using Microsoft.AspNet.Identity;

namespace ContactManager.Models
{
    public class SessionContextInterceptor : IDbConnectionInterceptor
    {
        public void Opened(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
            // Set SESSION_CONTEXT to current UserId whenever EF opens a connection
            try
            {
                var userId = System.Web.HttpContext.Current.User.Identity.GetUserId();
                if (userId != null)
                {
                    DbCommand cmd = connection.CreateCommand();
                    cmd.CommandText = "EXEC sp_set_session_context @key=N'UserId', @value=@UserId";
                    DbParameter param = cmd.CreateParameter();
                    param.ParameterName = "@UserId";
                    param.Value = userId;
                    cmd.Parameters.Add(param);
                    cmd.ExecuteNonQuery();
                }
            }
            catch (System.NullReferenceException)
            {
                // If no user is logged in, leave SESSION_CONTEXT null (all rows will be filtered)
            }
        }
        
        public void Opening(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void BeganTransaction(DbConnection connection, BeginTransactionInterceptionContext interceptionContext)
        {
        }

        public void BeginningTransaction(DbConnection connection, BeginTransactionInterceptionContext interceptionContext)
        {
        }

        public void Closed(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void Closing(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void ConnectionStringGetting(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void ConnectionStringGot(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void ConnectionStringSet(DbConnection connection, DbConnectionPropertyInterceptionContext<string> interceptionContext)
        {
        }

        public void ConnectionStringSetting(DbConnection connection, DbConnectionPropertyInterceptionContext<string> interceptionContext)
        {
        }

        public void ConnectionTimeoutGetting(DbConnection connection, DbConnectionInterceptionContext<int> interceptionContext)
        {
        }

        public void ConnectionTimeoutGot(DbConnection connection, DbConnectionInterceptionContext<int> interceptionContext)
        {
        }

        public void DataSourceGetting(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void DataSourceGot(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void DatabaseGetting(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void DatabaseGot(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void Disposed(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void Disposing(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void EnlistedTransaction(DbConnection connection, EnlistTransactionInterceptionContext interceptionContext)
        {
        }

        public void EnlistingTransaction(DbConnection connection, EnlistTransactionInterceptionContext interceptionContext)
        {
        }

        public void ServerVersionGetting(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void ServerVersionGot(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void StateGetting(DbConnection connection, DbConnectionInterceptionContext<System.Data.ConnectionState> interceptionContext)
        {
        }

        public void StateGot(DbConnection connection, DbConnectionInterceptionContext<System.Data.ConnectionState> interceptionContext)
        {
        }
    }

    public class SessionContextConfiguration : DbConfiguration
    {
        public SessionContextConfiguration()
        {
            AddInterceptor(new SessionContextInterceptor());
        }
    }
}
```

Que é a mudança de única aplicação necessária. Ir para a frente e criar e publicar a aplicação.

## <a name="step-2-add-a-userid-column-to-the-database-schema"></a>Passo 2: Adicionar uma coluna de ID de utilizador para o esquema da base de dados

Em seguida, precisamos de adicionar uma coluna de ID de utilizador para a tabela de contactos para associar cada linha um utilizador (inquilino). Vamos irá alterar o esquema diretamente na base de dados, para que não temos que incluir este campo no nosso modelo de dados EF.

Ligar à base de dados diretamente, através do SQL Server Management Studio ou Visual Studio e, em seguida, execute o seguinte T SQL:

```
ALTER TABLE Contacts ADD UserId nvarchar(128)
    DEFAULT CAST(SESSION_CONTEXT(N'UserId') AS nvarchar(128))
```

Esta ação adiciona uma coluna de ID de utilizador para a tabela de contactos. Utilizamos o tipo de dados nvarchar(128) para que correspondam aos IDs de utilizador das armazenados na tabela AspNetUsers e, criamos uma restrição predefinida que irá configurar automaticamente o ID de utilizador para as linhas inseridas recentemente para ser o ID de utilizador actualmente armazenada na SESSION_CONTEXT.

Agora a tabela seguinte aspeto:

![Tabela SSMS contactos](./media/web-sites-dotnet-entity-framework-row-level-security/SSMS-Contacts.png)

Quando são criados novos contactos, eles vai ser atribuídos automaticamente o ID de utilizador correta. Para fins de demonstração, no entanto, vamos atribuir algumas estes contactos existentes a um utilizador existente.

Se criou alguns utilizadores a aplicação já (por exemplo, utilizando o local, Google ou Facebook contas), irá vê-los na tabela AspNetUsers. Na captura de ecrã abaixo, há até ao momento apenas um utilizador.

![Tabela de SSMS AspNetUsers](./media/web-sites-dotnet-entity-framework-row-level-security/SSMS-AspNetUsers.png)

Copiar o Id de user1@contoso.com, e colá-la a instrução de SQL de T abaixo. Execute esta declaração de três dos contactos associar este ID de utilizador.

```
UPDATE Contacts SET UserId = '19bc9b0d-28dd-4510-bd5e-d6b6d445f511'
WHERE ContactId IN (1, 2, 5)
```

## <a name="step-3-create-a-row-level-security-policy-in-the-database"></a>Passo 3: Criar uma política de segurança de nível de linha de base de dados

O último passo é criar uma política de segurança que utiliza o ID de utilizador no SESSION_CONTEXT para filtrar os resultados devolvidos pela consultas automaticamente.

Enquanto ainda estiver ligado à base de dados, execute o seguinte T SQL:

```
CREATE SCHEMA Security
go

CREATE FUNCTION Security.userAccessPredicate(@UserId nvarchar(128))
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS accessResult
    WHERE @UserId = CAST(SESSION_CONTEXT(N'UserId') AS nvarchar(128))
go

CREATE SECURITY POLICY Security.userSecurityPolicy
    ADD FILTER PREDICATE Security.userAccessPredicate(UserId) ON dbo.Contacts,
    ADD BLOCK PREDICATE Security.userAccessPredicate(UserId) ON dbo.Contacts
go

```

Este código faz três coisas. Em primeiro lugar, cria um novo esquema como prática recomendada para centralizando e limitação de acesso para os objetos RLS. Em seguida, que cria uma função predicada que irá devolver "1" quando o ID de utilizador de uma linha corresponde o ID de utilizador no SESSION_CONTEXT. Por fim, que cria uma política de segurança que adiciona esta função como um filtro e um bloco de predicado na tabela contactos. O predicado filtro faz com que consultas devolver apenas as linhas que pertencem ao utilizador atual e o predicado bloco age como salvaguarda para impedir que a aplicação nunca acidentalmente inserção de uma linha para o utilizador errado.

Agora, execute a aplicação e inicie sessão no como user1@contoso.com. Este utilizador agora vê apenas os contactos podemos atribuídos para este ID de utilizador anterior:

![Aplicação do Gestor de contacto antes de ativar RLS](./media/web-sites-dotnet-entity-framework-row-level-security/ContactManagerApp-After.png)

Para validar isto ainda mais, tente registar um novo utilizador. Verão sem contactos, porque nenhum tiver sido atribuído às mesmas. Se que criar um novo contacto, será atribuído aos mesmos e apenas conseguirão vê-la.

## <a name="next-steps"></a>Próximos passos

Já está! A aplicação de web Contact Manager simple foi convertida para um inquilino com várias um onde cada utilizador tem a sua própria lista de contactos. Ao utilizar segurança de nível de linha, podemos tenha evitada a complexidade da impor inquilino lógica de acesso no nosso código da aplicação. Este transparência permite que a aplicação focar o problema de empresas real à mão e também reduz o risco de fugas acidentalmente dados tal como a aplicação do código base cresce.

Neste tutorial tem apenas riscado a superfície do que é possível fazer com o RLS. Por exemplo, é possível ter mais sofisticada ou lógica de acesso granular e do possíveis armazenar mais do que apenas o ID do utilizador actual na SESSION_CONTEXT. Também é possível [integrar RLS com as bibliotecas de cliente de ferramentas da base de dados flexível](../sql-database/sql-database-elastic-tools-multi-tenant-row-level-security.md) suportar inquilinos com várias shards de uma camada de dados de escala de saída.

Para além destas possibilidades, estamos a trabalhar também para tornar RLS melhor ainda. Se tiver dúvidas, ideias ou coisas que gostaria de ver, consulte diga-nos saber nos comentários. Agradecemos-lhe os seus comentários!
