<properties
    pageTitle="Criar e ligar a uma base de dados do MySQL no Azure"
    description="Saiba como utilizar o portal do Azure para criar uma base de dados do MySQL e, em seguida, ligar à mesma a partir de uma aplicação web do PHP no Azure."
    documentationCenter="php"
    services="app-service\web"
    authors="cephalin"
    manager="wpickett"
    editor=""
    tags="mysql"/>

<tags
    ms.service="multiple"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm;cephalin"/>

# <a name="create-and-connect-to-a-mysql-database-in-azure"></a>Criar e ligar a uma base de dados do MySQL no Azure

Este tutorial mostra-lhe como criar uma base de dados do MySQL no [portal do Azure](https://portal.azure.com) (fornecedor é [ClearDB](http://www.cleardb.com/)) e como ligar à mesma a partir de uma aplicação de web PHP em execução no [Azure aplicação de serviço](./app-service/app-service-value-prop-what-is.md). 

> [AZURE.NOTE] Também pode criar uma base de dados do MySQL como parte de um [modelo de aplicação Marketplace](./app-service-web/app-service-web-create-web-app-from-marketplace.md).

## <a name="create-a-mysql-database-in-azure-portal"></a>Criar uma base de dados do MySQL no portal do Azure

Para criar uma base de dados do MySQL no portal do Azure, faça o seguinte:

1. Inicie a sessão [portal do Azure](https://portal.azure.com).

2. No menu à esquerda, clique em **Novo** > **dados + armazenamento** > **Base de dados do MySQL**.

    ![Criar uma base de dados do MySQL no Azure - início](./media/store-php-create-mysql-database/create-db-1-start.png)

2. Na nova base de dados do MySQL [pá](azure-portal-overview.md), configure a nova base de dados do MySQL da seguinte forma (*pá*: uma página do portal que é apresentada horizontalmente):

    - **Nome da base de dados**: escreva um nome de forma exclusiva
    - **Subscrição**: selecione a subscrição para utilizar
    - **Tipo de base de dados**: selecione **partilhado** para camadas gratuitas ou em baixo custo ou **Dedicated** obter recursos dedicados. 
    - **Grupo de recursos**: adicionar a base de dados do MySQL a um [grupo de recursos](../azure-resource-manager/resource-group-overview.md) de existente ou colocá-la num novo. Recursos no mesmo grupo facilmente podem ser geridos em conjunto.
    - **Localização**: selecione uma localização perto. Ao adicionar a um grupo de recursos existente, está bloqueado para a localização do grupo de recursos.
    - **Camadas de preços**: clique em **Camadas preços**, em seguida, selecione uma opção comparar (camada de**mercúrio** é gratuita) e, em seguida, clique em **Selecionar**. 
    - **Termos legal**: clique em **Termos Legal**, reveja os detalhes de compra e clique em **comprar**.
    - **Afixar ao dashboard**: selecione se pretender que a aceder aos mesmos diretamente a partir do dashboard. Isto é especialmente útil se não estiver familiarizado com ainda navegação no portal.
    
    A captura de ecrã seguinte é apenas um exemplo de como pode configurar a sua base de dados do MySQL.  
    ![Criar uma base de dados do MySQL no Azure - configurar](./media/store-php-create-mysql-database/create-db-2-configure.png)

3. Quando tiver terminado a configurar, clique em **Criar**.

    ![Criar uma base de dados do MySQL no Azure - criar](./media/store-php-create-mysql-database/create-db-3-create.png)

    Irá ver enviarem uma notificação de pop-up que sabe que iniciou implementação.

    ![Criar uma base de dados do MySQL no Azure - em curso](./media/store-php-create-mysql-database/create-db-4-started-status.png)

    Irá obter outro pop-up assim que a implementação com êxito. O portal também será aberto o pá de base de dados do MySQL automaticamente.

<a name="connect"></a>
## <a name="connect-to-your-mysql-database"></a>Ligar a sua base de dados do MySQL

Para ver as informações de ligação para a nova base de dados do MySQL, clicar em **Propriedades** no pá da sua aplicação web.
    
![Criar uma base de dados do MySQL no Azure - pá de base de dados do MySQL](./media/store-php-create-mysql-database/create-db-5-finished-db-blade.png)

Agora pode utilizar essas informações de ligação em qualquer aplicação web. Um exemplo que mostra como utilizar as informações de ligação a partir de uma aplicação PHP simple está disponível [aqui](https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-mysql).

## <a name="connect-a-laravel-web-app-from-the-php-get-started-tutorial"></a>Ligar uma aplicação web do Laravel (a partir do PHP get introdução tutorial)

Suponha que apenas tiver terminado a tutoriais [criar, configurar e implementar uma aplicação web do PHP para Azure](./app-service-web/app-service-web-php-get-started.md) e tiver uma aplicação de web [Laravel](https://www.laravel.com/) em execução no Azure. Pode adicionar facilmente as capacidades de base de dados para a sua aplicação Laravel. Basta seguir os passos abaixo:

>[AZURE.NOTE] Os seguintes passos partem do pressuposto de que tiver concluído a tutoriais [criar, configurar e implementar uma aplicação web do PHP para Azure](./app-service-web/app-service-web-php-get-started.md).

1. Configure a aplicação Laravel no seu ambiente de desenvolvimento local para apontar para a base de dados do MySQL. Para executar esta tarefa, abra `.env` da sua aplicação Laravel diretório de raiz e configurar as opções de base de dados do MySQL.

        DB_CONNECTION=mysql
        DB_HOST=<HOSTNAME_from_properties_blade>
        DB_PORT=<PORT_from_properties_blade>
        DB_DATABASE=<see_note_below>
        DB_USERNAME=<USERNAME_from_properties_blade>
        DB_PASSWORD=<PASSWORD_from_properties_blade>

    >[AZURE.NOTE] Na pá **Propriedades** , o nome da base de dados MySQL pode ou não pode ser aquele apresentado no campo **Nome da base de dados** . É aconselhável verificar o parâmetro de base de dados no campo **Cadeia de ligação** . 
    >
    >![Criar uma base de dados do MySQL no Azure - em curso](./media/store-php-create-mysql-database/connect-db-1-database-name.png)

2. A forma mais rápida para confirmar que tem acesso MySQL agora é utilizar [andaimes de autenticação do Laravel predefinido](https://laravel.com/docs/5.2/authentication#authentication-quickstart). No terminal a linha de comandos, execute os seguintes comandos a partir do diretório de raiz da sua aplicação Laravel:

         php artisan migrate
         php artisan make:auth

    O primeiro comando cria as tabelas no Azure com base em migrações predefinidas na `database/migrations` diretório e o segundo comando microcápsulas as vistas básicas e rotas para o registo de utilizador e de autenticação.

3. Execute o servidor de desenvolvimento agora:

        php artisan serve

4. No browser, navegue para http://localhost:8000 e registar um novo utilizador, tal como apresentado:

    ![Ligar a dados do MySQL no Azure - registe-se de utilizador](./media/store-php-create-mysql-database/connect-db-2-development-server.png)

    Siga o pedido de IU concluído o registo. Assim que registo for concluída, vai ser iniciou sessão.
    
    ![Ligar a dados do MySQL no Azure - registe-se de utilizador](./media/store-php-create-mysql-database/connect-db-3-registered-user.png)

    Agora está a desenvolver a sua aplicação contra a base de dados do MySQL no Azure.

5. Agora, só precisa de criar uma réplica da sua `.env` definições para a sua aplicação Azure web. Execute os seguintes comandos do Azure clip:

        azure site appsetting add DB_CONNECTION=mysql
        azure site appsetting add DB_HOST=<HOSTNAME_from_properties_blade>
        azure site appsetting add DB_PORT=<PORT_from_properties_blade>
        azure site appsetting add DB_DATABASE=<Database_param_from_CONNECTION_INFO_from_properties_blade>
        azure site appsetting add DB_USERNAME=<USERNAME_from_properties_blade>
        azure site appsetting add DB_PASSWORD=<PASSWORD_from_properties_blade>

    Saiba como isto funciona em [configurar o Azure web app](./app-service-web/app-service-web-php-get-started.md#configure).

6. Em seguida, consolide e notificações push Azure alterações locais efectuadas anteriormente durante a execução `php artisan make:auth`.

        git add .
        git commit -m "scaffold auth views and routes"
        git push azure master

7. Navegue para a aplicação Azure web.

        azure site browse

8. Inicie sessão com as credenciais do utilizador que criou anteriormente.

    ![Ligar à base de dados do MySQL no Azure - navegue para o Azure web app](./media/store-php-create-mysql-database/connect-db-4-browse-azure-webapp.png)

    Depois de iniciar sessão, deverá ver o ecrã de início de sessão pós amigável.
    
    ![Ligar a dados do MySQL no Azure - tem sessão iniciado](./media/store-php-create-mysql-database/connect-db-5-logged-in.png)

    Parabéns, a aplicação web do PHP no Azure está agora a aceder a dados a partir da sua base de dados do MySQL. 

## <a name="next-steps"></a>Próximos passos

Para mais informações, consulte o [Centro de programadores do PHP](/develop/php/).
