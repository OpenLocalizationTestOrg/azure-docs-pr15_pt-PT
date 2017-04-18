<properties
    pageTitle="Como efetuar tarefas de administração, por exemplo, repor palavras-passe de administrador | Microsoft Azure"
    description="Descreve como executar tarefas administrativas comuns numa base de dados SQL. Por exemplo, a reposição de palavra-passe de administrador, conceder e a remover o acesso."
    services="sql-database"
    documentationCenter=""
    authors="v-shysun"
    manager="felixwu"
    editor=""
    keywords="Repor palavra-passe de administrador"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="v-shysun"/>

# <a name="how-to-perform-common-administrative-tasks-such-as-resetting-admin-password-in-azure-sql-database"></a>Como efetuar tarefas administrativas comuns, tais como repor palavra-passe de administrador na base de dados do SQL Azure
Utilize este tópico para os passos rápidos para conceder e remover o acesso a uma base de dados do Azure SQL. Para informações mais abrangentes, consulte:

- [Gerir bases de dados e inícios de sessão de base de dados do SQL Azure](sql-database-manage-logins.md)
- [Proteger a sua base de dados SQL](sql-database-security.md)
- [Centro de segurança do motor de base de dados do SQL Server e base de dados Azure SQL](https://msdn.microsoft.com/library/bb510589)


[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="to-reset-admin-password-for-a-logical-server"></a>Para repor a palavra-passe de administrador para um servidor de lógico

- No [Portal do Azure](https://portal.azure.com) clique em **Servidores de SQL**, selecione o servidor a partir da lista e, em seguida, clique em **Repor palavra-passe**.

## <a name="to-help-make-sure-only-authorized-ip-addresses-are-allowed-to-access-the-server"></a>Para ajudar a tornar certificar-se apenas IP autorizado endereços são permitidos para aceder ao servidor
- Consulte o artigo [como: Configurar definições da firewall na base de dados SQL](sql-database-configure-firewall-settings.md).

## <a name="to-create-contained-database-users-in-the-user-database"></a>Para criar utilizadores de base de dados contidas na base de dados de utilizador
- Utilize a instrução [CREATE USER](https://msdn.microsoft.com/library/ms173463.aspx) e ver [Contidas da base de dados utilizadores - efetuar a base de dados portátil](https://msdn.microsoft.com/library/ff929188.aspx).

## <a name="to-authenticate-contained-database-users-by-using-your-azure-active-directory"></a>Para autenticar utilizadores de base de dados contidas utilizando o Azure Active Directory
- Consulte o artigo [ligar-se a base de dados SQL utilizando a autenticação do Azure Active Directory](sql-database-aad-authentication.md).

## <a name="to-create-additional-logins-for-high-privileged-users-in-the-virtual-master-database"></a>Para criar os inícios de sessão adicionais para utilizadores privilegiados alta na base de dados principal virtual
- Utilize a instrução de [Criar o início de sessão](https://msdn.microsoft.com/library/ms189751.aspx) e consulte a secção Gerir inícios de sessão gerir bases de [dados e inícios de sessão de base de dados do SQL Azure](sql-database-manage-logins.md) para obter mais detalhes.
