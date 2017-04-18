<properties
    pageTitle="Criar uma aplicação web do PHP MySQL na aplicação de serviço de Azure e implementar utilizando Git"
    description="Um tutorial demonstra como criar uma aplicação web PHP que armazena dados MySQL e utilizar a implementação de Git para Azure."
    services="app-service\web"
    documentationCenter="php"
    authors="rmcmurray"
    manager="wpickett"
    editor=""
    tags="mysql"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="create-a-php-mysql-web-app-in-azure-app-service-and-deploy-using-git"></a>Criar uma aplicação web do PHP MySQL na aplicação de serviço de Azure e implementar utilizando Git

Este tutorial mostra-lhe como criar uma aplicação web do PHP MySQL e como implementá-lo para a [Aplicação de serviço](http://go.microsoft.com/fwlink/?LinkId=529714) utilizando Git. Irá utilizar [PHP][install-php], a ferramenta de linha de comandos do MySQL (parte do [MySQL][install-mysql]) e [Git] [ install-git] instalado no seu computador. As instruções neste tutorial, podem ser seguidas em qualquer sistema operativo, incluindo o Windows, Mac e Linux. Após concluir este guia, terá de uma aplicação de web PHP/MySQL em execução no Azure.

Vai aprender:

* Como criar uma aplicação web e uma base de dados do MySQL utilizando o [Portal do Azure][management-portal]. Porque PHP é activado na [Aplicação de serviço Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) por predefinição, nada especial é necessária para executar o seu código PHP.
* Como publicar e voltar a publicar a aplicação para Azure utilizando Git.
* Como ativar a extensão de compositor automatizar compositor tarefas em cada `git push`.

Ao seguir este tutorial, irá criar um registo simples web app no PHP. A aplicação irá estar alojada nas aplicações Web. Uma captura de ecrã da aplicação concluída é abaixo:

![Web site da PHP Azure][running-app]

## <a name="set-up-the-development-environment"></a>Configurar o ambiente de desenvolvimento

Neste tutorial assume que tem [PHP][install-php], a ferramenta de linha de comandos do MySQL (parte do [MySQL][install-mysql]) e [Git] [ install-git] instalado no seu computador.

<a id="create-web-site-and-set-up-git"></a>
## <a name="create-a-web-app-and-set-up-git-publishing"></a>Criar uma aplicação web e configurar a publicação de Git

Siga estes passos para criar uma aplicação web e uma base de dados do MySQL:

1. Inicie sessão no [Portal do Azure][management-portal].
2. Clique no ícone de **Novo** .

3. Clique em **Ver todas as** junto **Marketplace**. 

4. Clique em **Web + Mobile** **Web app + MySQL**. Em seguida, clique em **Criar**.

4. Introduza um nome válido para o seu grupo de recursos.

    ![Grupo nome do conjunto de recursos][resource-group]

5. Introduza valores para a nova aplicação web.

    ![Criar a aplicação web][new-web-app]

6. Introduza valores para a nova base de dados, incluindo a concordar com os termos legais.

    ![Criar nova base de dados do MySQL][new-mysql-db]

7. Quando a aplicação web foi criada, irá ver a nova pá de aplicação web.

7. Nas **Definições** na **Implementação contínuo**, clique em clique no _Configurar obrigatório definições_.

    ![Configurar o Git publicação][setup-publishing]

8. Selecione o **Local Git repositório** para a origem.

    ![Configurar o Git repositório][setup-repository]


9. Para ativar a publicação de Git, tem de fornecer um nome de utilizador e palavra-passe. Tome nota do nome de utilizador e palavra-passe que cria. (Se configurou um repositório de Git antes, este passo será ignorado.)

    ![Criar de credenciais de publicação][credentials]


## <a name="get-remote-mysql-connection-information"></a>Obter informações de ligação remotas do MySQL

Para ligar à base de dados MySQL que está a ser executado nas aplicações Web, do seu irá precisar das informações de ligação. Para obter informações sobre a ligação MySQL, siga estes passos:

1. A partir do seu grupo de recursos, clique em da base de dados:

    ![Selecione a base de dados][select-database]

2. A base de dados **Definições**, selecione **Propriedades**.

    ![Selecione propriedades][select-properties]

2. Tome nota dos valores dos `Database`, `Host`, `User Id`, e `Password`.

    ![Propriedades de nota][note-properties]

## <a name="build-and-test-your-app-locally"></a>Criar e testar a sua aplicação localmente

Agora que criou uma aplicação web, pode desenvolver a sua aplicação localmente, então implementá-lo após o ensaio.

O pedido de registo é uma aplicação de PHP simple que lhe permite registe-se para um evento ao fornecer o seu nome e endereço de e-mail. Obter informações sobre requerentes de registo anterior são apresentadas numa tabela. Informações de registo são armazenadas numa base de dados do MySQL. A aplicação é constituído por um ficheiro (código de copiar/colar disponíveis abaixo):

* **Index.php**: apresenta um formulário de registo e uma tabela que contém informações do inscrito.

Criar e executar a aplicação localmente, siga os passos abaixo. Tenha em atenção que estes passos partem do princípio de que tem a ferramenta de linha de comandos de MySQL (parte do MySQL) configurar no seu computador local e PHP e ativou a [extensão de DOP para MySQL][pdo-mysql].

1. Ligar ao servidor do MySQL remoto, utilizando o valor para `Data Source`, `User Id`, `Password`, e `Database` que que recuperou anterior:

        mysql -h{Data Source] -u[User Id] -p[Password] -D[Database]

2. Linha de comandos do MySQL irão aparecer:

        mysql>

3. Cole o seguinte `CREATE TABLE` comando para criar o `registration_tbl` tabela na base de dados:

        CREATE TABLE registration_tbl(id INT NOT NULL AUTO_INCREMENT, PRIMARY KEY(id), name VARCHAR(30), email VARCHAR(30), date DATE);

4. Na raiz da sua pasta de aplicação local crie **index.php** ficheiro.

5. Abra o ficheiro de **index.php** num editor de texto ou IDE e adicione o código seguinte e concluir as alterações necessárias marcadas com `//TODO:` comentários.


        <html>
        <head>
        <Title>Registration Form</Title>
        <style type="text/css">
            body { background-color: #fff; border-top: solid 10px #000;
                color: #333; font-size: .85em; margin: 20; padding: 20;
                font-family: "Segoe UI", Verdana, Helvetica, Sans-Serif;
            }
            h1, h2, h3,{ color: #000; margin-bottom: 0; padding-bottom: 0; }
            h1 { font-size: 2em; }
            h2 { font-size: 1.75em; }
            h3 { font-size: 1.2em; }
            table { margin-top: 0.75em; }
            th { font-size: 1.2em; text-align: left; border: none; padding-left: 0; }
            td { padding: 0.25em 2em 0.25em 0em; border: 0 none; }
        </style>
        </head>
        <body>
        <h1>Register here!</h1>
        <p>Fill in your name and email address, then click <strong>Submit</strong> to register.</p>
        <form method="post" action="index.php" enctype="multipart/form-data" >
              Name  <input type="text" name="name" id="name"/></br>
              Email <input type="text" name="email" id="email"/></br>
              <input type="submit" name="submit" value="Submit" />
        </form>
        <?php
            // DB connection info
            //TODO: Update the values for $host, $user, $pwd, and $db
            //using the values you retrieved earlier from the Azure Portal.
            $host = "value of Data Source";
            $user = "value of User Id";
            $pwd = "value of Password";
            $db = "value of Database";
            // Connect to database.
            try {
                $conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
                $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
            }
            catch(Exception $e){
                die(var_dump($e));
            }
            // Insert registration info
            if(!empty($_POST)) {
            try {
                $name = $_POST['name'];
                $email = $_POST['email'];
                $date = date("Y-m-d");
                // Insert data
                $sql_insert = "INSERT INTO registration_tbl (name, email, date)
                           VALUES (?,?,?)";
                $stmt = $conn->prepare($sql_insert);
                $stmt->bindValue(1, $name);
                $stmt->bindValue(2, $email);
                $stmt->bindValue(3, $date);
                $stmt->execute();
            }
            catch(Exception $e) {
                die(var_dump($e));
            }
            echo "<h3>Your're registered!</h3>";
            }
            // Retrieve data
            $sql_select = "SELECT * FROM registration_tbl";
            $stmt = $conn->query($sql_select);
            $registrants = $stmt->fetchAll();
            if(count($registrants) > 0) {
                echo "<h2>People who are registered:</h2>";
                echo "<table>";
                echo "<tr><th>Name</th>";
                echo "<th>Email</th>";
                echo "<th>Date</th></tr>";
                foreach($registrants as $registrant) {
                    echo "<tr><td>".$registrant['name']."</td>";
                    echo "<td>".$registrant['email']."</td>";
                    echo "<td>".$registrant['date']."</td></tr>";
                }
                echo "</table>";
            } else {
                echo "<h3>No one is currently registered.</h3>";
            }
        ?>
        </body>
        </html>

4.  Num terminal aceda à sua pasta de aplicação e escreva o seguinte comando:

        php -S localhost:8000

Agora pode navegar para **http://localhost:8000 /** para testar a aplicação.


## <a name="publish-your-app"></a>Publicar a sua aplicação

Depois de ter testado localmente a sua aplicação, pode publicá-lo ao utilizar Git do Web Apps. Irá iniciar o repositório Git local e publicar a aplicação.

> [AZURE.NOTE]
> Estes são os mesmos passos apresentados no Portal do Azure no final de criar uma aplicação web e definir o Git publicação secção acima.

1. (Opcional)  Se tenha esquecido ou no local incorreto o URL da sua Git repostitory remoto, navegue para as propriedades da aplicação web no Portal do Azure.

1. Abrir GitBash (ou um terminal, se Git estiver na sua `PATH`), altere directórios para o diretório de raiz da sua aplicação e execute os seguintes comandos:

        git init
        git add .
        git commit -m "initial commit"
        git remote add azure [URL for remote repository]
        git push azure master

    Será pedido para a palavra-passe que criou anteriormente.

    ![Push inicial para Azure através do Git][git-initial-push]

2. Navegue para **http://[site name].azurewebsites.net/index.php** para começar a utilizar a aplicação (esta informação será armazenada no seu dashboard da conta):

    ![Web site da PHP Azure][running-app]

Depois de ter publicado sua aplicação, pode começar a fazer alterações ao mesmo e utilizar Git para publicá-las.

## <a name="publish-changes-to-your-app"></a>Publicar as alterações à sua aplicação

Para publicar as alterações à sua aplicação, siga estes passos:

1. Faça alterações para a sua aplicação localmente.
2. Abrir GitBash (ou um terminal it Git está no seu `PATH`), altere directórios para o diretório de raiz da sua aplicação e execute os seguintes comandos:

        git add .
        git commit -m "comment describing changes"
        git push azure master

    Será pedido para a palavra-passe que criou anteriormente.

    ![A instalação push alterações do site Azure através do Git][git-change-push]

3. Navegue para **http://[site name].azurewebsites.net/index.php** para ver a sua aplicação e quaisquer alterações que tenha efetuado:

    ![Web site da PHP Azure][running-app]

>[AZURE.NOTE] Se pretender começar a aplicação de serviço de Azure antes de inscrever-se para uma conta do Azure, aceda ao [Tentar aplicação de serviço](http://go.microsoft.com/fwlink/?LinkId=523751), onde imediatamente pode criar uma aplicação web do starter curto na aplicação de serviço. Sem cartões de crédito necessários; sem compromissos.

<a name="composer"></a>
## <a name="enable-composer-automation-with-the-composer-extension"></a>Ativar a automatização compositor com a extensão de compositor

Por predefinição, o processo de implementação git na aplicação de serviço não fazer nada com composer.json, se tiver um no projeto PHP. Pode ativar a composer.json processar durante `git push` ao ativar a extensão de compositor.

1. No seu PHP web pá da aplicação no [portal do Azure][management-portal], clique em **Ferramentas** > **extensões**.

    ![Definições de extensão compositor][composer-extension-settings]

2. Clique em **Adicionar**e, em seguida, clique em **Compositor**.

    ![Adicionar compositor extensão][composer-extension-add]
    
3. Clique em **OK** para aceitar os termos legais. Clique em **OK** novamente para adicionar a extensão.

    O pá **instalados extensões** agora irá mostrar a extensão de compositor.  
    ![Vista de extensão compositor][composer-extension-view]
    
4. Agora, executar `git add`, `git commit`, e `git push` como na secção anterior. Verá agora compositor estiver a instalar dependências definidas na composer.json.

    ![Compositor extensão sucesso][composer-extension-success]

## <a name="next-steps"></a>Próximos passos

Para mais informações, consulte o [Centro de programadores do PHP](/develop/php/).

<!-- URL List -->

[install-php]: http://www.php.net/manual/en/install.php
[install-SQLExpress]: http://www.microsoft.com/download/details.aspx?id=29062
[install-Drivers]: http://www.microsoft.com/download/details.aspx?id=20098
[install-git]: http://git-scm.com/
[install-mysql]: http://dev.mysql.com/downloads/mysql/
[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php
[management-portal]: https://portal.azure.com
[sql-database-editions]: http://msdn.microsoft.com/library/windowsazure/ee621788.aspx

<!-- IMG List -->

[running-app]: ./media/web-sites-php-mysql-deploy-use-git/running_app_2.png
[new-website]: ./media/web-sites-php-mysql-deploy-use-git/new_website2.png
[custom-create]: ./media/web-sites-php-mysql-deploy-use-git/create_web_mysql.png
[new-mysql-db]: ./media/web-sites-php-mysql-deploy-use-git/create_db.png
[go-to-webapp]: ./media/web-sites-php-mysql-deploy-use-git/select_webapp.png
[setup-git-publishing]: ./media/web-sites-php-mysql-deploy-use-git/setup_git_publishing.png
[credentials]: ./media/web-sites-php-mysql-deploy-use-git/save_credentials.png
[resource-group]: ./media/web-sites-php-mysql-deploy-use-git/set_group.png
[new-web-app]: ./media/web-sites-php-mysql-deploy-use-git/create_wa.png
[setup-publishing]: ./media/web-sites-php-mysql-deploy-use-git/setup_deploy.png
[setup-repository]: ./media/web-sites-php-mysql-deploy-use-git/select_local_git.png
[select-database]: ./media/web-sites-php-mysql-deploy-use-git/select_database.png
[select-properties]: ./media/web-sites-php-mysql-deploy-use-git/select_properties.png
[note-properties]: ./media/web-sites-php-mysql-deploy-use-git/note-properties.png

[git-instructions]: ./media/web-sites-php-mysql-deploy-use-git/git-instructions.png
[git-change-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-change-push.png
[git-initial-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-initial-push.png

[composer-extension-settings]: ./media/web-sites-php-mysql-deploy-use-git/composer-extension-settings.png
[composer-extension-add]: ./media/web-sites-php-mysql-deploy-use-git/composer-extension-add.png
[composer-extension-view]: ./media/web-sites-php-mysql-deploy-use-git/composer-extension-view.png
[composer-extension-success]: ./media/web-sites-php-mysql-deploy-use-git/composer-extension-success.png
