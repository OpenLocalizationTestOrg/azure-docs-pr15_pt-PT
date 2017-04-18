### <a name="prerequisites"></a>Pré-requisitos
- Uma conta Azure; Pode criar uma [conta gratuita](https://azure.microsoft.com/free)
- Uma [Base de dados do SQL Azure](../articles/sql-database/sql-database-get-started.md) com as suas informações de ligação, incluindo o nome do servidor, o nome de base de dados e o nome de utilizador/palavra-passe. Esta informação está incluída na cadeia de ligação de base de dados SQL:
  
    Servidor = tcp:*yoursqlservername*. database.windows.net,1433;Initial catálogo =*yourqldbname*; Informações de segurança de persistirem = FALSO; ID de utilizador = {your_username}; Palavra-passe = {your_password}; MultipleActiveResultSets = FALSO; Encriptar = verdadeiro; TrustServerCertificate = FALSO; Tempo limite da ligação = 30;

    Leia mais sobre [Bases de dados do SQL Azure](https://azure.microsoft.com/services/sql-database).

> [AZURE.NOTE] Quando cria uma base de dados do SQL Azure, também pode criar as bases de dados de exemplo incluídos com o SQL. 



Antes de utilizar a base de dados do SQL Azure numa aplicação lógica, ligar-se a sua base de dados do SQL. Pode fazê-lo facilmente dentro da sua aplicação de lógica no portal do Azure.  

Ligar a sua base de dados do SQL Azure através dos seguintes passos:  

1. Crie uma aplicação de lógica. No estruturador de lógica aplicações, adicione um accionador e, em seguida, adicione uma ação. Selecione **Mostrar Microsoft gerido APIs** na lista pendente e, em seguida, introduza "sql" na caixa de pesquisa. Selecione uma das ações:  

    ![Passo da criação de ligação do SQL Azure](./media/connectors-create-api-sqlazure/sql-actions.png)

2. Se ainda não tiver criado quaisquer ligações de base de dados SQL, lhe for pedido para os detalhes de ligação:  

    ![Passo da criação de ligação do SQL Azure](./media/connectors-create-api-sqlazure/connection-details.png) 

3. Introduza os detalhes de base de dados SQL. Propriedades com um asterisco são necessárias.

    | Propriedade | Detalhes |
|---|---|
| Ligar através do Gateway | Deixe desmarcada. Isto é utilizado quando se liga a um SQL Server no local. |
| Nome da ligação * | Introduza um nome para a ligação. | 
| Nome do SQL Server * | Introduza o nome do servidor; qual é algo parecido com *servername.database.windows.net*. O nome do servidor é apresentado nas propriedades da base de dados SQL no portal do Azure e também é apresentado na cadeia de ligação. | 
| Nome de base de dados do SQL * | Introduza o nome que lhe atribuiu a base de dados do SQL. Isto está na lista as propriedades da base de dados SQL na cadeia de ligação: catálogo inicial =*yoursqldbname*. | 
| Nome de utilizador * | Introduza o nome de utilizador que criou quando a base de dados do SQL foi criado. Isto é listado nas propriedades da base de dados SQL no portal do Azure. | 
| Palavra-passe * | Introduza a palavra-passe que criou quando a base de dados do SQL foi criado. | 

    Estas credenciais são utilizadas para autorizar a sua aplicação de lógica para ligar e aceder aos dados do SQL. Depois de concluído, os detalhes do seu ligação têm um aspeto semelhantes ao seguinte:  

    ![Passo da criação de ligação do SQL Azure](./media/connectors-create-api-sqlazure/sample-connection.png) 

4. Selecione **Criar**. 

5. Repare que a ligação foi criada. Agora, continue com os outros passos na sua aplicação de lógica: 

    ![Passo da criação de ligação do SQL Azure](./media/connectors-create-api-sqlazure/table.png)