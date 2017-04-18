<properties 
    pageTitle="Criar uma aplicação web do PHP MySQL na aplicação de serviço de Azure e implementar utilizando FTP" 
    description="Um tutorial demonstra como criar uma aplicação web PHP que armazena os dados na implementação de FTP MySQL e a utilização para Azure." 
    services="app-service\web" 
    documentationCenter="php" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="PHP" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>


#<a name="create-a-php-mysql-web-app-in-azure-app-service-and-deploy-using-ftp"></a>Criar uma aplicação web do PHP MySQL na aplicação de serviço de Azure e implementar utilizando FTP

Este tutorial mostra-lhe como criar uma aplicação web do PHP MySQL e como implementá-lo utilizando FTP. Neste tutorial assume que tem [PHP][install-php], [MySQL][install-mysql], um servidor web e um cliente de FTP instalado no seu computador. As instruções neste tutorial, podem ser seguidas em qualquer sistema operativo, incluindo o Windows, Mac e Linux. Após concluir este guia, terá de uma aplicação de web PHP/MySQL em execução no Azure.
 
Vai aprender:

* Como criar uma aplicação web e uma base de dados do MySQL utilizando o Portal do Azure. Porque PHP é activado nas aplicações Web, por predefinição, nada especial é necessária para executar o seu código PHP.
* Como publicar a sua aplicação ao Azure utilizando FTP.
 
Ao seguir este tutorial, irá criar um registo simples web app no PHP. A aplicação irá estar alojada numa aplicação Web. Uma captura de ecrã da aplicação concluída é abaixo:

![Web Site da Azure PHP][running-app]

>[AZURE.NOTE] Se pretender começar a aplicação de serviço de Azure antes de inscrever-se para uma conta, aceda ao [Tentar aplicação de serviço](http://go.microsoft.com/fwlink/?LinkId=523751), onde imediatamente pode criar uma aplicação web do starter curto na aplicação de serviço. Sem cartões de crédito obrigatório, sem compromissos. 


##<a name="create-a-web-app-and-set-up-ftp-publishing"></a>Criar uma aplicação web e configurar a publicação de FTP

Siga estes passos para criar uma aplicação web e uma base de dados do MySQL:

1. Inicie sessão no [Portal do Azure][management-portal].
2. Clique no ícone **+ Novo** no canto superior esquerdo do Portal do Azure.

    ![Criar novo Web Site do Azure][new-website]

3. Na pesquisa escreva **Web app + MySQL** e clique em **aplicação Web + MySQL**.

    ![Criar um novo Web Site do personalizado][custom-create]

4. Clique em **Criar**. Introduza um nome de serviço de aplicação exclusiva, um nome para o grupo de recursos e um novo plano de serviço válido.

    ![Grupo nome do conjunto de recursos][resource-group]


6. Introduza valores para a nova base de dados, incluindo a concordar com os termos legais.

    ![Criar nova base de dados do MySQL][new-mysql-db]
    
7. Quando a aplicação web foi criada, irá ver a nova pá de serviço de aplicação.


6. Clique em **Definições** > **credenciais de implementação**. 

    ![Definir credenciais de implementação][set-deployment-credentials]

7. Para ativar a publicação de FTP, tem de fornecer um nome de utilizador e palavra-passe. Guarde as credenciais e tome nota do nome de utilizador e palavra-passe que cria.

    ![Criar de credenciais de publicação][portal-ftp-username-password]

##<a name="build-and-test-your-app-locally"></a>Criar e testar a sua aplicação localmente

O pedido de registo é uma aplicação de PHP simple que lhe permite registe-se para um evento ao fornecer o seu nome e endereço de e-mail. Obter informações sobre requerentes de registo anterior são apresentadas numa tabela. Informações de registo são armazenadas numa base de dados do MySQL. A aplicação é constituído por dois ficheiros:

* **Index.php**: apresenta um formulário de registo e uma tabela que contém informações do inscrito.
* **createtable.php**: cria a tabela MySQL para a aplicação. Este ficheiro só será utilizado uma vez.

Criar e executar a aplicação de localmente, siga os passos abaixo. Tenha em atenção que estes passos partem do princípio de que tem PHP, MySQL, e um servidor web, configure o seu computador local e que o utilizador ativou a [extensão de DOP para MySQL][pdo-mysql].

1. Criar uma base de dados do MySQL denominado `registration`. Pode fazê-lo a partir da linha de comandos do MySQL com este comando:

        mysql> create database registration;

2. No diretório de raiz do seu servidor web, crie uma pasta denominada `registration` e criar dois ficheiros no mesmo - outro chamado `createtable.php` e outro chamado `index.php`.

3. Abrir o `createtable.php` ficheiro num editor de texto ou IDE e adicione o código abaixo. Este código será utilizado para criar o `registration_tbl` tabela na `registration` base de dados.

        <?php
        // DB connection info
        $host = "localhost";
        $user = "user name";
        $pwd = "password";
        $db = "registration";
        try{
            $conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
            $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
            $sql = "CREATE TABLE registration_tbl(
                        id INT NOT NULL AUTO_INCREMENT, 
                        PRIMARY KEY(id),
                        name VARCHAR(30),
                        email VARCHAR(30),
                        date DATE)";
            $conn->query($sql);
        }
        catch(Exception $e){
            die(print_r($e));
        }
        echo "<h3>Table created.</h3>";
        ?>

    > [AZURE.NOTE] 
    > Terá de atualizar os valores para <code>$user</code> e <code>$pwd</code> com o seu nome de utilizador do MySQL local e a palavra-passe.

4. Abra um browser e navegue para [http://localhost/registration/createtable.php][localhost-createtable]. Isto irá criar a `registration_tbl` tabela na base de dados.

5. Abra o ficheiro de **index.php** num editor de texto ou IDE e adicione o código HTML e CSS básico para a página (o código PHP será adicionado passos posteriores).

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

        ?>
        </body>
        </html>

6. Dentro das tags PHP, adicione o código PHP para ligar à base de dados.

        // DB connection info
        $host = "localhost";
        $user = "user name";
        $pwd = "password";
        $db = "registration";
        // Connect to database.
        try {
            $conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
            $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
        }
        catch(Exception $e){
            die(var_dump($e));
        }

    > [AZURE.NOTE]
    > Novamente, terá de atualizar os valores para <code>$user</code> e <code>$pwd</code> com o seu nome de utilizador do MySQL local e a palavra-passe.

7. Após o código de ligação de base de dados, adicione o código para inserir as informações de registo na base de dados.

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

8. Por fim, após o código acima, adicione o código de obtenção de dados da base de dados.

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

Agora pode navegar para [http://localhost/registration/index.php] [ localhost-index] para testar a aplicação.

##<a name="get-mysql-and-ftp-connection-information"></a>Obter informações de ligação do MySQL e FTP

Para ligar à base de dados MySQL que está a ser executado nas aplicações Web, do seu irá precisar das informações de ligação. Para obter informações sobre a ligação MySQL, siga estes passos:

1. A partir da aplicação de serviço pá de aplicação web, clique na ligação do grupo de recursos:

    ![Seleccionar o grupo de recursos][select-resourcegroup]

1. A partir do seu grupo de recursos, clique em da base de dados:

    ![Selecione a base de dados][select-database]

2. A base de dados de resumo, selecione **definições do** > **Propriedades**.

    ![Selecione propriedades][select-properties]
    
2. Tome nota dos valores dos `Database`, `Host`, `User Id`, e `Password`.

    ![Propriedades de nota][note-properties]

3. A aplicação web, clique na ligação **Transferir publicar perfil** no canto inferior direito da página:

    ![Transferir publicar perfil][download-publish-profile]

4. Abrir o `.publishsettings` ficheiro num editor de XML. 

3. Localizar o `<publishProfile >` elemento com `publishMethod="FTP"` que aspeto semelhante a este:

        <publishProfile publishMethod="FTP" publishUrl="ftp://[mysite].azurewebsites.net/site/wwwroot" ftpPassiveMode="True" userName="[username]" userPWD="[password]" destinationAppUrl="http://[name].antdf0.antares-test.windows-int.net" 
            ...
        </publishProfile>
    
Tome nota da `publishUrl`, `userName`, e `userPWD` atributos.

##<a name="publish-your-app"></a>Publicar a sua aplicação

Depois de ter testado localmente a sua aplicação, pode publicá-lo para a sua aplicação web utilizando o FTP. No entanto, primeiro tem de atualizar as informações de ligação de base de dados na aplicação. Utilizando as informações de ligação de base de dados tiver sido obtido anterior (na **obter MySQL e informações de ligação de FTP** secção), de atualizar as seguintes informações no **tanto** a `createdatabase.php` e `index.php` ficheiros com os valores adequados:

    // DB connection info
    $host = "value of Data Source";
    $user = "value of User Id";
    $pwd = "value of Password";
    $db = "value of Database";

Agora, está pronto para publicar a sua aplicação utilizando FTP.

1. Abra o seu cliente de FTP à escolha.

2. Introduza a *parte do nome de anfitrião* do `publishUrl` atributo indicadas acima no seu cliente de FTP.

3. Introduza o `userName` e `userPWD` atributos indicadas acima inalterada no seu cliente de FTP.

4. Estabelecer uma ligação.

Depois de ter ligado irá poderá carregar e transferir ficheiros conforme necessário. Certifique-se de que são ao carregar ficheiros para o diretório de raiz, que é `/site/wwwroot`.

Após carregar ambos `index.php` e `createtable.php`, navegue até à **http://[site name].azurewebsites.net/createtable.php** para criar a tabela MySQL para a aplicação, em seguida, navegue até à **http://[site name].azurewebsites.net/index.php** para começar a utilizar a aplicação.
 
## <a name="next-steps"></a>Próximos passos

Para mais informações, consulte o [Centro de programadores do PHP](/develop/php/).

[install-php]: http://www.php.net/manual/en/install.php
[install-mysql]: http://dev.mysql.com/doc/refman/5.6/en/installing.html
[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php
[localhost-createtable]: http://localhost/tasklist/createtable.php
[localhost-index]: http://localhost/tasklist/index.php
[running-app]: ./media/web-sites-php-mysql-deploy-use-ftp/running_app_2.png
[new-website]: ./media/web-sites-php-mysql-deploy-use-ftp/new_website2.png
[custom-create]: ./media/web-sites-php-mysql-deploy-use-ftp/create_web_mysql.png
[website-details]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/website_details.jpg
[new-mysql-db]: ./media/web-sites-php-mysql-deploy-use-ftp/create_db.png
[go-to-webapp]: ./media/web-sites-php-mysql-deploy-use-ftp/select_webapp.png
[set-deployment-credentials]: ./media/web-sites-php-mysql-deploy-use-ftp/set_credentials.png
[portal-ftp-username-password]: ./media/web-sites-php-mysql-deploy-use-ftp/save_credentials.png
[resource-group]: ./media/web-sites-php-mysql-deploy-use-ftp/set_group.png
[new-web-app]: ./media/web-sites-php-mysql-deploy-use-ftp/create_wa.png
[select-database]: ./media/web-sites-php-mysql-deploy-use-ftp/select_database.png
[select-resourcegroup]: ./media/web-sites-php-mysql-deploy-use-ftp/select_resourcegroup.png
[select-properties]: ./media/web-sites-php-mysql-deploy-use-ftp/select_properties.png
[note-properties]: ./media/web-sites-php-mysql-deploy-use-ftp/note-properties.png

[connection-string-info]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/connection_string_info.png
[management-portal]: https://portal.azure.com
[download-publish-profile]: ./media/web-sites-php-mysql-deploy-use-ftp/download_publish_profile_3.png
 
