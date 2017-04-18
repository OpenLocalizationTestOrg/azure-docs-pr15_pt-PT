<properties
    pageTitle="Ligar o Excel a base de dados SQL | Microsoft Azure"
    description="Saiba como ligar o Microsoft Excel a base de dados do Azure SQL na nuvem. Importar dados para o Excel para obter informações detalhadas dados e relatórios."
    services="sql-database"
    keywords="ligar para o sql do excel, importar dados para o excel"
    documentationCenter=""
    authors="joseidz"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/05/2016"
    ms.author="joseidz"/>


# <a name="sql-database-tutorial-connect-excel-to-an-azure-sql-database-and-create-a-report"></a>Tutorial de base de dados SQL: ligar o Excel para uma base de dados do Azure SQL e criar um relatório

> [AZURE.SELECTOR]
- [Visual Studio](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Saiba como ligar o Excel para uma base de dados do SQL na nuvem para que possa importar dados e criar tabelas e gráficos com base nos valores da base de dados. Neste tutorial, que irá configurar a ligação entre o Excel e uma tabela de base de dados, guarde o ficheiro que armazena os dados e as informações de ligação para o Excel e, em seguida, criar um gráfico dinâmico a partir dos valores de base de dados.

Terá de uma base de dados SQL Azure antes de começar. Se não tiver uma, consulte o artigo [criar a primeira base de dados do SQL](sql-database-get-started.md) para obter uma base de dados com dados de exemplo para cima e a ser executada em alguns minutos. Neste artigo, irá importar dados de exemplo para o Excel a partir desse artigo, mas pode seguir passos semelhantes nos seus dados.

Também terá uma cópia do Excel. Este artigo utiliza o [Microsoft Excel 2016](https://products.office.com/en-US/).

## <a name="connect-excel-to-a-sql-database-and-create-an-odc-file"></a>Ligar o Excel a uma base de dados do SQL e criar um ficheiro odc

1.  Para ligar o Excel a base de dados SQL, abra o Excel e, em seguida, criar um novo livro ou abra um livro do Excel existente.

2.  Na barra de menus na parte superior da página clique em **dados**, clique em **De outras origens**e, em seguida, clique em **Do SQL Server**.

    ![Seleccionar origem de dados: ligar o Excel a base de dados SQL.](./media/sql-database-connect-excel/excel_data_source.png)

    É aberto o Assistente de ligação de dados.

3.  Na caixa de diálogo **ligar ao servidor de base de dados** , escreva o **nome do servidor** de base de dados SQL que pretende ligar no formulário de <*nomedoservidor*>**. database.windows.net**. Por exemplo, **adworkserver.database.windows.net**.

4.  Em **credenciais de início de sessão**, clique em **utilizar os seguintes nome de utilizador e palavra-passe**, escreva o **Nome de utilizador** e a **palavra-passe** configurar para o servidor de base de dados SQL quando criou, ou seja e, em seguida, clique em **seguinte**.

    ![Escreva as credenciais de utilizador início de sessão e nome de servidor](./media/sql-database-connect-excel/connect-to-server.png)

    > [AZURE.TIP] Dependendo do seu ambiente de rede, não poderá ligar ou poderá perder a ligação se o servidor de base de dados SQL não permitir o tráfego a partir do seu endereço IP do cliente. Aceda ao [Azure portal](https://portal.azure.com/), clique em servidores de SQL, clique no servidor, clique em firewall em definições e adicionar o seu endereço IP do cliente. Veja [como configurar definições da firewall](sql-database-configure-firewall-settings.md) para obter detalhes.

5. Na caixa de diálogo **Seleccionar base de dados e tabela** , selecione a base de dados que pretende trabalhar com a partir da lista e, em seguida, clique nas tabelas ou vistas que pretende trabalhar com (optamos por **vGetAllCategories**) e, em seguida, clique em **seguinte**.

    ![Selecione uma base de dados e tabela.](./media/sql-database-connect-excel/select-database-and-table.png)

    Abre a caixa de diálogo **Guardar ficheiro de ligação de dados e termine** , onde fornecer informações sobre o ficheiro de ligação (*. odc) do Office da base de dados que o Excel utiliza. Pode deixar as predefinições ou personalizar as suas seleções.

6. Pode deixar as predefinições, mas tenha em atenção o **Nome do ficheiro** em particular. Uma **Descrição**, um **Nome amigável**e **Pesquisar palavras-chave** ajudá-lo e outros utilizadores não se esqueça de o que estiver a estabelecer ligação ao e encontre a ligação. Clique em **sempre tentativa de utilizar este ficheiro para atualizar os dados** se pretender que as informações de ligação armazenadas no ficheiro odc para que possa ser quando ligar para o mesmo e, em seguida, clique em **Concluir**.

    ![Guardar um ficheiro odc](./media/sql-database-connect-excel/save-odc-file.png)

    É apresentada a caixa de diálogo **Importar dados** .

## <a name="import-the-data-into-excel-and-create-a-pivot-chart"></a>Importar os dados para o Excel e crie um gráfico dinâmico
Agora que já foram estabelecidas a ligação e criado o ficheiro com as informações de ligação de dados e, está a ler para importar os dados.

1. Na caixa de diálogo **Importar dados** , clique na opção que pretende para apresentar os seus dados na folha de cálculo e, em seguida, clique em **OK**. Optamos por **gráfico dinâmico**. Também pode optar por criar uma **nova folha de cálculo** ou para **adicionar estes dados ao modelo de dados**. Para mais informações sobre modelos de dados, consulte o artigo [criar um modelo de dados no Excel](https://support.office.com/article/Create-a-Data-Model-in-Excel-87E7A54C-87DC-488E-9410-5C75DBCB0F7B). Clique em **Propriedades** para explorar informações sobre o ficheiro odc que criou no passo anterior e para escolher opções de atualização dos dados.

    ![Escolher o formato de dados no Excel](./media/sql-database-connect-excel/import-data.png)

    Folha de cálculo tem agora uma tabela dinâmica vazia e o gráfico.

8. Em **Campos de tabela dinâmica**, selecione todas as caixas de verificação para os campos que pretende ver.

    ![Configure o relatório de base de dados.](./media/sql-database-connect-excel/power-pivot-results.png)

> [AZURE.TIP]Se pretender estabelecer ligação a outras folhas de cálculo e livros do Excel para a base de dados, clique em **dados**, clique em **ligações**, clique em **Adicionar**, selecione a ligação que criou a partir da lista e, em seguida, clique em **Abrir**.
> ![Abrir uma ligação a partir de outro livro](./media/sql-database-connect-excel/open-from-another-workbook.png)

## <a name="next-steps"></a>Próximos passos

- Saiba como [ligar à base de dados SQL com SQL Server Management Studio](sql-database-connect-query-ssms.md) para consultar avançadas e análise.
- Saiba mais sobre os benefícios da [flexível conjuntos de dados](sql-database-elastic-pool.md).
- Saiba como [criar uma aplicação web que liga à base de dados do SQL no back-end](../app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md).
