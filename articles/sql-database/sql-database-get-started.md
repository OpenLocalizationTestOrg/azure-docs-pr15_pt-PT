<properties
    pageTitle="Tutorial de base de dados SQL: criar uma base de dados do SQL | Microsoft Azure"
    description="Saiba como configurar um servidor de base de dados SQL lógico, regra de firewall do servidor, base de dados SQL e dados de exemplo. Além disso, saiba como ligar com ferramentas de cliente, configure os utilizadores e configurar uma regra de firewall de base de dados."
    keywords="tutorial de base de dados do SQL, criar uma base de dados sql"
    services="sql-database"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/07/2016"
    ms.author="carlrab"/>


# <a name="sql-database-tutorial-create-a-sql-database-in-minutes-by-using-the-azure-portal"></a>Tutorial de base de dados SQL: criar uma base de dados do SQL em minutos utilizando o portal do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)

Neste tutorial, aprende a utilizar o portal do Azure para:

- Crie uma base de dados do Azure SQL com dados de exemplo.
- Crie uma regra da firewall ao nível do servidor para um único endereço IP ou para um intervalo de endereços IP.

Pode executar as seguintes tarefas mesmo ao utilizar [c#](sql-database-get-started-csharp.md) ou [PowerShell](sql-database-get-started-powershell.md).

[AZURE.INCLUDE [Login](../../includes/azure-getting-started-portal-login.md)]

<a name="create-logical-server-bk"></a>

## <a name="create-your-first-azure-sql-database"></a>Criar a sua primeira base de dados do Azure SQL 

1. Se atualmente não estiver ligado, ligue para o [portal do Azure](http://portal.azure.com).
2. Clique em **Novo**, clique em **dados + armazenamento**e, em seguida, localize a **Base de dados SQL**.

    ![Nova base de dados do sql 1](./media/sql-database-get-started/sql-database-new-database-1.png)

3. Clique em **Base de dados SQL** para abrir o pá de base de dados SQL. O conteúdo deste pá varia consoante o número das suas subscrições e os objetos existentes (como servidores existentes).

    ![Nova base de dados do sql 2](./media/sql-database-get-started/sql-database-new-database-2.png)

4. Na caixa de texto **nome da base de dados** , forneça um nome para a sua primeira base de dados - tal como "my-base de dados". Uma marca de verificação verde indica que forneça um nome válido.

    ![Nova base de dados do sql 3](./media/sql-database-get-started/sql-database-new-database-3.png)

5. Se tiver múltiplas subscrições, selecione uma subscrição.
6. Em **grupo de recursos**, clique em **Criar novo** e forneça um nome para o primeiro grupo de recursos - tal como "my--grupo de recursos". Uma marca de verificação verde indica que forneça um nome válido.

    ![Nova base de dados do sql 4](./media/sql-database-get-started/sql-database-new-database-4.png)

7. Em **Selecione origem**, clique em **amostra** e, em seguida, em **Selecione o exemplo** , clique em **AdventureWorksLT [V12]**.

    ![Nova base de dados do sql 5](./media/sql-database-get-started/sql-database-new-database-5.png)

8. Em **servidor**, clique em **Configurar obrigatório definições**.

    ![Nova base de dados do sql 6](./media/sql-database-get-started/sql-database-new-database-6.png)

9. No pá Server, clique em **criar um novo servidor**. É criada uma base de dados do Azure SQL dentro de um objeto de servidor, que pode ser um novo servidor ou um servidor existente.

    ![Nova base de dados do sql 7](./media/sql-database-get-started/sql-database-new-database-7.png)

10. Reveja o pá **novo servidor** para compreender as informações que necessárias para fornecer o seu servidor de novo.

    ![Nova base de dados do sql 8](./media/sql-database-get-started/sql-database-new-database-8.png)

11. Na caixa de texto **nome de servidor** , forneça um nome para o primeiro servidor - tal como "my-novo--objecto de servidor". Uma marca de verificação verde indica que forneça um nome válido.

    ![Nova base de dados do sql 9](./media/sql-database-get-started/sql-database-new-database-9.png)
 
12. Em **Iniciar sessão de administrador do servidor**, forneça um nome de utilizador para o início de sessão do administrador para este servidor - tal como "meu administrador de conta". Este login é conhecido como o início de sessão principal do servidor. Uma marca de verificação verde indica que forneça um nome válido.

    ![Nova base de dados do sql 10](./media/sql-database-get-started/sql-database-new-database-10.png)

13. Em **palavra-passe** e **Confirmar palavra-passe**, fornecer uma palavra-passe para o servidor de conta de início de sessão principal - tais como "p@ssw0rd1". Uma marca de verificação verde indica que forneça uma palavra-passe válida.

    ![Nova base de dados do sql 11](./media/sql-database-get-started/sql-database-new-database-11.png)
 
14. Em **localização**, selecione um centro de dados adequado à sua localização - tal como "Austrália Leste".

    ![Nova base de dados do sql 12](./media/sql-database-get-started/sql-database-new-database-12.png)

15. Em * * servidor criar V12 (atualização mais recente), repare que apenas tem a opção para criar uma versão atual do Azure SQL server.

    ![Nova base de dados do sql 13](./media/sql-database-get-started/sql-database-new-database-13.png)

16. Repare que, por predefinição, a caixa de verificação **Permitir azure serviços para aceder ao servidor** está selecionada e não pode ser alterada aqui. Esta é uma opção avançada. Pode alterar esta definição nas definições do servidor firewall para este objecto de servidor, apesar de para a maioria dos cenários isto não é necessário.

    ![Nova base de dados do sql 14](./media/sql-database-get-started/sql-database-new-database-14.png)

17. No novo pá de servidor, reveja as suas seleções e, em seguida, clique em **Selecionar** para selecionar este servidor nova para a nova base de dados.

    ![Nova base de dados do sql 15](./media/sql-database-get-started/sql-database-new-database-15.png)

18. No pá base de dados SQL, em **preços camada**, clique em **S2 padrão** e, em seguida, clique em **base** para escolher a camada comparar menos dispendiosa para a sua primeira base de dados. Pode sempre alterar a camada preços mais tarde.

    ![Nova base de dados do sql 16](./media/sql-database-get-started/sql-database-new-database-16.png)

19. No pá base de dados SQL, reveja as suas seleções e, em seguida, clique em **Criar** para criar o primeiro servidor e a base de dados. Os valores que forneceu são validados e começa a implementação.

    ![Nova base de dados do sql 17](./media/sql-database-get-started/sql-database-new-database-17.png)

20. Na barra de ferramentas portal, clique nos itens de **notificações** para verificar o estado da sua implementação.

    ![Nova base de dados do sql 18](./media/sql-database-get-started/sql-database-new-database-18.png)

>[AZURE.IMPORTANT]Quando concluir a implementação, o novo Azure SQL server e base de dados são criados no Azure. Não conseguir ligar à sua novo servidor ou base de dados utilizando ferramentas do SQL Server até que cria uma regra de firewall do servidor para abrir o firewall da base de dados SQL para ligações a partir de fora do Azure.

[AZURE.INCLUDE [Create server firewall rule](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="next-steps"></a>Próximos passos
Agora que que concluiu este tutorial de base de dados SQL e criado uma base de dados com alguns dados de exemplo, está pronto para explorar utilizando as suas ferramentas favoritas.

- Se estiver familiarizado com Transact-SQL e o SQL Server Management Studio (SSMS), saiba como [ligar e consulta numa base de dados SQL com SSMS](sql-database-connect-query-ssms.md).

- Se conhecer o Excel, saiba como [ligar a uma base de dados SQL Azure com o Excel](sql-database-connect-excel.md).

- Se estiver pronto para começar a codificação, escolha o idioma de programação em [bibliotecas de ligações para a base de dados SQL e o SQL Server](sql-database-libraries.md).

- Se pretender mover as bases de dados do SQL Server no local para Azure, consulte o artigo [migrar uma base de dados para a base de dados SQL](sql-database-cloud-migrate.md) para obter mais informações.

- Se quiser carregar alguns dados para uma nova tabela a partir de um ficheiro CSV utilizando a ferramenta de linha de comandos BCP, consulte o artigo [carregar os dados numa base de dados do SQL a partir de um ficheiro CSV com BCP](sql-database-load-from-csv-with-bcp.md).

- Se pretender comece a explorar segurança da base de dados do Azure SQL, consulte o artigo [Introdução à segurança](sql-database-get-started-security.md)


## <a name="additional-resources"></a>Recursos adicionais

[O que é a base de dados SQL?](sql-database-technical-overview.md)
