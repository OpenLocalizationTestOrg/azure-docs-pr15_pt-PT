
<!--
includes/sql-database-create-new-database-portal.md

Latest Freshness check:  2016-04-11 , carlrab.

As of circa 2016-04-11, the following topics might include this include:
articles/sql-database/sql-database-get-started-tutorial.md

-->
## <a name="create-a-new-azure-sql-database"></a>Criar uma nova base de dados do Azure SQL

Utilize os seguintes passos no portal do Azure para criar uma nova base de dados do Azure SQL num novo ou existente base de dados do Azure SQL lógicos servidor.

1. Se atualmente não estiver ligado, ligue para o [portal do Azure](http://portal.azure.com).
2. Clique em **Novo**, escreva a **Base de dados SQL**e, em seguida, clique em **Base de dados do SQL (a nova base de dados)**.

     ![Nova base de dados](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-1.png)

3. Clique em **Base de dados do SQL (a nova base de dados)**.

     ![Nova base de dados](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-2.png)

4. Clique em **Criar** para criar uma nova base de dados no serviço de base de dados SQL.

     ![Nova base de dados](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-3.png)

5. Forneça os valores para as seguintes propriedades do servidor:

 - Nome da base de dados
 - Subscrição: Isto aplica-se apenas se tiver múltiplas subscrições.
 - Grupo de recursos: se de que está a dar os primeiros passos, utilize o grupo de recursos do servidor lógico.
 - Selecione origem: pode escolher uma base de dados em branco, dados de exemplo ou uma cópia de segurança da base de dados Azure. Para migrar uma base de dados do SQL Server no local ou carregar os dados utilizando a ferramenta de linha de comandos BCP, consulte as ligações no final deste artigo.
 - : Um novo ou existente lógico de servidor.
 - Início de sessão do servidor admin
 - Palavra-passe
 - Comparar camadas: se de que está a dar os primeiros passos, utilize o valor predefinido S0.
 - Agrupamento: Isto aplica-se apenas se foi escolhido uma base de dados em branco.

        ![New database](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-4.png)

6.  Clique em **Criar**. Na área de notificação, pode ver que iniciou implementação.

     ![Nova base de dados](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-5.png)

7. Aguarde para implementação concluir antes de prosseguir para o passo seguinte.

     ![Nova base de dados](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-6.png)
