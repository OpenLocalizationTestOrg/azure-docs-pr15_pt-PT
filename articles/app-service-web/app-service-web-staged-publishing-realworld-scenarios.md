<properties
  pageTitle="Utilizar DevOps ambientes de forma eficaz para a sua aplicação web"
  description="Saiba como utilizar faixas de implementação para configurar e gerir vários ambientes de desenvolvimento para a sua aplicação"
  services="app-service\web"
  documentationCenter=""
  authors="sunbuild"
  manager="yochayk"
  editor=""/>

<tags
  ms.service="app-service"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="web"
  ms.date="10/24/2016"
  ms.author="sumuth"/>

# <a name="use-devops-environments-effectively-for-your-web-apps"></a>Utilizar DevOps ambientes de forma eficaz para as suas aplicações web

Este artigo mostra-lhe configurar e gerir implementações de aplicação web para múltiplas versões da sua aplicação como o desenvolvimento, de transição, as perguntas e respostas e de produção. Pode ser considerado cada versão da sua aplicação como o ambiente de desenvolvimento para necessidade específica de dentro o processo de implementação. Por exemplo, ambiente de perguntas e respostas pode ser utilizada pela equipa de programadores para testar a qualidade da aplicação antes de notificações push as alterações à produção.
Configurar o vários ambientes de desenvolvimento pode ser um desafio que precisar para controlar, gerir os recursos (cluster, aplicação web, base de dados, cache, etc.) e implementar código em ambientes.

## <a name="setting-up-a-non-production-environment-stagedevqa"></a>Configurar um ambiente do não produção (fase, Dev Center, as perguntas e respostas)
Assim que tiver uma aplicação de produção web cópia e em execução, o próximo passo é criar um ambiente não sejam de produção. Para que possa utilizar faixas de implementação, certifique-se de que estiver a executar no modo de plano de serviço de aplicação **Premium** ou **padrão** . Faixas de implementação são aplicações web ao vivo realmente com os seus próprios nomes de anfitriões. Elementos de conteúdos e configuração da aplicação Web podem ser trocados entre duas faixas de implementação, incluindo a ranhura de produção. Implementar a aplicação para uma ranhura implementação tem os seguintes benefícios:

1. Pode validar alterações da aplicação web de uma transição ranhura de implementação antes de trocá-lo com a ranhura de produção.
2. Implementar uma aplicação web para uma ranhura em primeiro lugar e trocá-lo para produção assegura que todas as instâncias da ranhura são aquece antes de a ser convertido em produção. Isto elimina o tempo de inatividade quando implementar a aplicação web. O redirecionamento de tráfego está totalmente integrado e não existem pedidos desaparecem devido a operações de trocar. Este fluxo de trabalho todo pode ser automatizado através da configuração [Automática trocar](web-sites-staged-publishing.md#configure-auto-swap-for-your-web-app) quando não é necessária uma pré-trocar validação.
3. Depois de um trocar, ranhura com a aplicação web anteriormente faseada tem agora o anterior produção web app. Se as alterações trocadas na ranhura produção não como esperado, pode efetuar o mesmo trocar imediatamente para obter a "última conhecidos boa aplicação web do" novamente.

Para configurar uma transição ranhura de implementação, consulte o artigo [Configurar ambientes para web apps no Azure aplicação de serviço de teste](web-sites-staged-publishing.md). Cada ambiente deve incluir o próprio conjunto de recursos, por exemplo se web de aplicação utiliza uma base de dados, em seguida, produção e teste do web app que devem estar a utilizar bases de dados diferentes. Adicione recursos de ambiente de desenvolvimento transição como base de dados, armazenamento ou cache para definir o seu ambiente de desenvolvimento transição.

## <a name="examples-of-using-multiple-development-environments"></a>Exemplos de utilização de vários ambientes de desenvolvimento

Qualquer projecto deve seguir uma gestão de código de origem com, pelo menos, dois ambientes, num ambiente de desenvolvimento e produção mas quando utilizar sistemas de gestão de conteúdo, quadros de aplicação, etc. podemos poderão ocorrer problemas onde a aplicação não suportam este cenário terminar a caixa. Esta situação for verdadeira para alguns dos quadros populares abordados abaixo. Muitas questões ao sítio ideias quando trabalha com um CMS quadros tal como

1. Como reduzir a dividi-la em diferentes ambientes
2. Quais os ficheiros que posso alterar e impossível impacto framework atualizações de versões
3. Como gerir as configurações por ambiente
4. Como gerir módulos/plug-ins versão atualizações, atualizações de versões principais framework

Existem diversas formas para configurar o ambiente de vários para o seu projeto e os exemplos abaixo são um único essa método as respetivas aplicações.

### <a name="wordpress"></a>WordPress
Esta secção vai aprender a configurar um fluxo de trabalho de implementação com faixas para WordPress. WordPress como a maior parte dos CMS soluções não suporta a trabalhar com vários ambientes de desenvolvimento terminar a caixa. Aplicação de serviço Web Apps tem algumas funcionalidades que tornam mais fácil armazenar as definições de configuração de fora do seu código.

Antes de criar uma ranhura transição, configure o código da aplicação para suportar vários ambientes. Para suportar os ambientes de múltiplos WordPress necessite de editar `wp-config.php` na sua aplicação web do desenvolvimento local, adicione o seguinte código no início do ficheiro. Isto vai permite que a aplicação escolher a configuração correta com base no ambiente de selecionado.

```
// Support multiple environments
// set the config file based on current environment
if (strpos($_SERVER['HTTP_HOST'],'localhost') !== false) {
// local development
 $config_file = 'config/wp-config.local.php';
}
elseif ((strpos(getenv('WP_ENV'),'stage') !== false) || (strpos(getenv('WP_ENV'),'prod' )!== false ))
//single file for all azure development environments
 $config_file = 'config/wp-config.azure.php';
}
$path = dirname(__FILE__). '/';
if (file_exists($path. $config_file)) {
// include the config file if it exists, otherwise WP is going to fail
require_once $path. $config_file;
```

Criar uma pasta de raiz da aplicação web denominado `config` e adicionar um ficheiro duas ficheiros: `wp-config.azure.php` e `wp-config.local.php` representando o seu ambiente do azure e local respetivamente.

Copiar o seguinte procedimento no `wp-config.local.php` :

```
<?php
// MySQL settings
/** The name of the database for WordPress */

define('DB_NAME', 'yourdatabasename');

/** MySQL database username */
define('DB_USER', 'yourdbuser');

/** MySQL database password */
define('DB_PASSWORD', 'yourpassword');

/** MySQL hostname */
define('DB_HOST', 'localhost');
/**
 * For developers: WordPress debugging mode.
 * * Change this to true to enable the display of notices during development.
 * It is strongly recommended that plugin and theme developers use WP_DEBUG
 * in their development environments.
 */
define('WP_DEBUG', true);

//Security key settings
define('AUTH_KEY', 'put your unique phrase here');
define('SECURE_AUTH_KEY','put your unique phrase here');
define('LOGGED_IN_KEY','put your unique phrase here');
define('NONCE_KEY', 'put your unique phrase here');
define('AUTH_SALT', 'put your unique phrase here');
define('SECURE_AUTH_SALT', 'put your unique phrase here');
define('LOGGED_IN_SALT', 'put your unique phrase here');
define('NONCE_SALT', 'put your unique phrase here');

/**
 * WordPress Database Table prefix.
 *
 * You can have multiple installations in one database if you give each a unique
 * prefix. Only numbers, letters, and underscores please!
 */
$table_prefix = 'wp_';
```

Definir as teclas de segurança acima pode ajudar a impedir a aplicação web a ser atacado, pelo que os valores exclusivos. Se precisar de gerar a cadeia de chaves de segurança mencionados acima, pode ir para o gerador automático para criar novos teclas/valores utilizando este [link] (https://api.wordpress.org/secret-key/1.1/salt)

Copie o código seguinte no `wp-config.azure.php`:


``` <?php
    // MySQL settings
    /** The name of the database for WordPress */
    
    define('DB_NAME', getenv('DB_NAME'));
    
    /** MySQL database username */
    define('DB_USER', getenv('DB_USER'));
    
    /** MySQL database password */
    define('DB_PASSWORD', getenv('DB_PASSWORD'));
    
    /** MySQL hostname */
    define('DB_HOST', getenv('DB_HOST'));
    
    /**
    * For developers: WordPress debugging mode.
    *
    * Change this to true to enable the display of notices during development.
    * It is strongly recommended that plugin and theme developers use WP_DEBUG
    * in their development environments.
    * Turn on debug logging to investigate issues without displaying to end user. For WP_DEBUG_LOG to
    * do anything, WP_DEBUG must be enabled (true). WP_DEBUG_DISPLAY should be used in conjunction
    * with WP_DEBUG_LOG so that errors are not displayed on the page */
    
    */
    define('WP_DEBUG', getenv('WP_DEBUG'));
    define('WP_DEBUG_LOG', getenv('TURN_ON_DEBUG_LOG'));
    define('WP_DEBUG_DISPLAY',false);
    
    //Security key settings
    /** If you need to generate the string for security keys mentioned above, you can go the automatic generator to create new keys/values: https://api.wordpress.org/secret-key/1.1/salt **/
    define('AUTH_KEY',getenv('DB_AUTH_KEY'));
    define('SECURE_AUTH_KEY', getenv('DB_SECURE_AUTH_KEY'));
    define('LOGGED_IN_KEY', getenv('DB_LOGGED_IN_KEY'));
    define('NONCE_KEY', getenv('DB_NONCE_KEY'));
    define('AUTH_SALT', getenv('DB_AUTH_SALT'));
    define('SECURE_AUTH_SALT', getenv('DB_SECURE_AUTH_SALT'));
    define('LOGGED_IN_SALT',  getenv('DB_LOGGED_IN_SALT'));
    define('NONCE_SALT',  getenv('DB_NONCE_SALT'));
    
    /**
    * WordPress Database Table prefix.
    *
    * You can have multiple installations in one database if you give each a unique
    * prefix. Only numbers, letters, and underscores please!
    */
    $table_prefix = getenv('DB_PREFIX');
```

#### <a name="use-relative-paths"></a>Utilize os caminhos relativos
É um critério último configurar a aplicação de WordPress para utilizar os caminhos relativos. WordPress armazena informações de URL na base de dados. Isto torna mover conteúdo de um ambiente para outro mais difícil que precisar para atualizar a base de dados sempre que mover a partir do local para fase ou fase ambientes de produção. Para reduzir o risco de problemas que pode dever-se com a implementação de uma base de dados sempre que implementar a partir de um ambiente para outro, utilize o [Plug-in de ligações de raiz relativa](https://wordpress.org/plugins/root-relative-urls/) que pode ser instalado utilizando WordPress dashboard de administrador ou transferi-lo manualmente a partir de [aqui](https://downloads.wordpress.org/plugin/root-relative-urls.zip).


Adicione as seguintes entradas ao seu `wp-config.php` ficheiro antes do `That's all, stop editing!` comentário:

```

  define('WP_HOME', 'http://'. filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
    define('WP_SITEURL', 'http://'. filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
    define('WP_CONTENT_URL', '/wp-content');
    define('DOMAIN_CURRENT_SITE', filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
```

Ativar o plug-in através de `Plugins` menu no dashboard de administrador WordPress. Guarde as suas definições de hiperligação permanente para a aplicação de WordPress.

#### <a name="the-final-wp-configphp-file"></a>Finais `wp-config.php` ficheiro
Atualizações WordPress Core não serão afeta a sua `wp-config.php`, `wp-config.azure.php` e `wp-config.local.php` ficheiros. No final deste como `wp-config.php` ficheiro terá o seguinte aspeto

```
<?php
/**
 * The base configurations of the WordPress.
 *
 * This file has the following configurations: MySQL settings, Table Prefix,
 * Secret Keys, and ABSPATH. You can find more information by visiting
 *
 * Codex page. You can get the MySQL settings from your web host.
 *
 * This file is used by the wp-config.php creation script during the
 * installation. You don't have to use the web web app, you can just copy this file
 * to "wp-config.php" and fill in the values.
 *
 * @package WordPress
 */

// Support multiple environments
// set the config file based on current environment
if (strpos($_SERVER['HTTP_HOST'],'localhost') !== false) { // local development
  $config_file = 'config/wp-config.local.php';
}
elseif ((strpos(getenv('WP_ENV'),'stage') !== false) ||(strpos(getenv('WP_ENV'),'prod' )!== false )){
  $config_file = 'config/wp-config.azure.php';
}


$path = dirname(__FILE__). '/';
if (file_exists($path. $config_file)) {
  // include the config file if it exists, otherwise WP is going to fail
  require_once $path. $config_file;
}

/** Database Charset to use in creating database tables. */
define('DB_CHARSET', 'utf8');

/** The Database Collate type. Don't change this if in doubt. */
define('DB_COLLATE', '');


/* That's all, stop editing! Happy blogging. */

define('WP_HOME', 'http://'. $_SERVER['HTTP_HOST']);
define('WP_SITEURL', 'http://'. $_SERVER['HTTP_HOST']);
define('WP_CONTENT_URL', '/wp-content');
define('DOMAIN_CURRENT_SITE', $_SERVER['HTTP_HOST']);

/** Absolute path to the WordPress directory. */
if ( !defined('ABSPATH') )
    define('ABSPATH', dirname(__FILE__). '/');

/** Sets up WordPress vars and included files. */
require_once(ABSPATH. 'wp-settings.php');
```

#### <a name="set-up-a-staging-environment"></a>Configurar um ambiente de teste
Partindo do princípio que já tem uma aplicação de web WordPress em execução no Azure Web, inicie sessão [portal de pré-visualização de gestão do Azure](http://portal.azure.com) e vá para a aplicação web do WordPress. Se aplicações não pode criar um partir de mercado. Para saber mais, [clique aqui](web-sites-php-web-site-gallery.md).
Clique em definições das -> implementação faixas -> Adicionar para criar uma ranhura implementação com a fase de nome. Uma ranhura implementação é outra aplicação web partilha os mesmos recursos que a aplicação web primária criado anteriormente.

![Criar ranhura de implementação de fase](./media/app-service-web-staged-publishing-realworld-scenarios/1setupstage.png)

Adicionar outra base de dados MySQL, apresentamos `wordpress-stage-db` ao seu grupo de recursos `wordpressapp-group`.

 ![Adicionar dados do MySQL ao grupo de recursos](./media/app-service-web-staged-publishing-realworld-scenarios/2addmysql.png)

Atualizar as cadeias de ligação para a sua ranhura de implementação de fase apontar para a base de dados recentemente criado, `wordpress-stage-db`. Nota que o seu produção web app, `wordpressprodapp` e transição web app `wordpressprodapp-stage` têm de apontar para bases de dados diferentes.

#### <a name="configure-environment-specific-app-settings"></a>Configurar as definições da aplicação de ambiente específicas
Os programadores podem armazenar pares de cadeia de valor de chave no Azure como parte das informações de configuração associadas a uma aplicação web denominada as definições da aplicação. O tempo de execução, aplicação de serviço Web Apps automaticamente obtém estes valores por si e torna-as na aplicação web da execução de código disponíveis. A partir de um título perspetiva que é um bonito lado beneficiar desde informações confidenciais, tais como cadeias de ligação de base de dados com palavras-passe nunca aparecem como texto simples num ficheiro como `wp-config.php`.

Este processo definido como se segue é útil quando executar que inclua alterações de ficheiro e alterações de base de dados para a aplicação de WordPress:
- Actualização da versão WordPress
- Adicionar novos ou editar ou atualizar o plug-ins
- Adicionar novos ou editar ou atualizar temas

Configure definições de aplicação para:

- informações de base de dados
- Ativar/desativar o registo de WordPress
- Definições de segurança WordPress

![Definições da aplicação para o Wordpress web app](./media/app-service-web-staged-publishing-realworld-scenarios/3configure.png)

Certifique-se de que adicionou as seguintes definições de aplicação para a sua aplicação e fase ranhura web de produção. Note que a produção web appe transição web app utilizam bases de dados diferentes.
Desmarque a caixa de verificação **Ranhura definição** para todos os parâmetros de definições exceto WP_ENV. Isto irá trocar a configuração para a aplicação web, juntamente com o conteúdo do ficheiro e a base de dados. Se a **Definição de ranhura** estiver **marcado**, as definições da aplicação do web app e configuração da cadeia de ligação não se move em ambientes quando efetuar uma operação de TROCAR e, consequentemente, se estiverem presentes as alterações de base de dados Isto irá não interromper a aplicação web do produção.

Implemente a aplicação de web do ambiente de desenvolvimento local fase web app e base de dados utilizando WebMatrix ou ferramenta (s) da sua preferência como FTP, Git ou PhpMyAdmin.

![Diálogo de matriz publicar Web para WordPress web app](./media/app-service-web-staged-publishing-realworld-scenarios/4wmpublish.png)

Procure e testar a sua aplicação web do transição. Tendo em conta um cenário que o tema da aplicação web tem de ser atualizados, eis o teste web app.

![Procurar antes de trocar faixas de teste do web app](./media/app-service-web-staged-publishing-realworld-scenarios/5wpstage.png)


 Se todas as agrado, clique no botão **trocar** na sua aplicação web transição para mover o seu conteúdo para o ambiente de produção. Neste caso trocar do web app e a base de dados em ambientes durante a operação cada **trocar** .

![Trocar alterações de pré-visualização para WordPress](./media/app-service-web-staged-publishing-realworld-scenarios/6swaps1.png)

 > [AZURE.NOTE]
 >Se tiver um cenário onde precisa de apenas push ficheiros (sem actualizações de base de dados), em seguida, **Verificar** a **Definição de ranhura** para todos os a base de dados relacionados com *as definições da aplicação* e *definições de ligação de cadeias* no pá de definição de aplicação web no interior do portal de pré-visualização Azure antes de efetuar a TROCAR. Neste DB_NAME maiúsculas/minúsculas, DB_HOST, DB_PASSWORD, DB_USER, definição de cadeia de ligação predefinida deve não aparecer na alterações de pré-visualização quando efetuar uma **trocar**. AT desta vez, quando estiver a concluir a operação **trocar** a aplicação web do WordPress terá as atualizações ficheiros **apenas**.

Antes de efetuar uma TROCAR, eis o produção WordPress web app ![produção no browser antes de trocar faixas](./media/app-service-web-staged-publishing-realworld-scenarios/7bfswap.png)

Após a operação de TROCAR o tema foi atualizado na sua aplicação web de produção.

![Produção no browser depois de trocar faixas](./media/app-service-web-staged-publishing-realworld-scenarios/8afswap.png)

Numa situação quando precisar da **recuperar**, pode ir para definições de produção web app e clique no botão **trocar** para trocar web app e base de dados a partir de produção para ranhura transição. Um dado importante que se deve lembrar é que se alterações de base de dados forem incluídas com uma operação de **trocar** a qualquer momento, em seguida, da próxima vez que implementar novamente para a sua aplicação web transição que precisa para implementar a base de dados muda para a base de dados atual para a sua aplicação web transição que pode ser a base de dados de produção anterior ou a fase.

#### <a name="summary"></a>Resumo
Generalizar o processo de qualquer aplicação com uma base de dados

1. Instalar a aplicação no seu ambiente do local
2. Incluir ambiente específicas de configuração (local e Azure Web App)
3. Configurar o seu ambientes de na aplicação de serviço Web Apps – transição, produção
4. Se tiver uma aplicação de produção já em execução no Azure, sincronize o seu conteúdo de produção (ficheiros/código + da base de dados) para ambiente de teste e local.
5. Desenvolver a sua aplicação no seu ambiente do local
6. Coloque a aplicação web do produção em manutenção ou modo de bloqueada e base de dados de sincronização conteúdo a partir de produção ambientes de transição e Dev Center
7. Implementar para ambiente de teste e teste
8. Implementar para ambiente de produção
9. Repita os passos 4 a 6

### <a name="umbraco"></a>Umbraco
Nesta secção vai aprender como a CMS Umbraco utiliza um módulo personalizado para implementar a partir do ambiente de DevOps vários. Este exemplo fornece-lhe uma abordagem diferente para gerir vários ambientes de programação.

[Umbraco CMS](http://umbraco.com/) é uma das popular.NET CMS soluções utilizadas pelo muitos programadores que fornece [Courier2](http://umbraco.com/products/more-add-ons/courier-2) módulo para implementar a partir do desenvolvimento transição para ambientes de produção. Pode facilmente criar um ambiente de desenvolvimento local para uma aplicação web do Umbraco CMS utilizando o Visual Studio ou WebMatrix.

1. Crie uma aplicação web do Umbraco com Visual Studio, [clique aqui](https://our.umbraco.org/documentation/Installation/install-umbraco-with-nuget).
2. Para criar uma aplicação web do Umbraco com WebMatrix, [clique aqui](http://umbraco.com/help-and-support/video-tutorials/getting-started/working-with-webmatrix).

Lembre-se remover o `install` pasta em sua aplicação e nunca carregue-o para a fase ou de produção aplicações web. Para este tutorial, irá ser utilizar WebMatrix

#### <a name="set-up-a-staging-environment"></a>Configurar o ambiente de teste
- Crie uma ranhura de implementação, tal como mencionado acima Umbraco CMS web App, partindo do princípio de que já tem uma aplicação web do Umbraco CMS para cima e a ser executado. Se não pode criar um partir de mercado.

- Atualize a cadeia de ligação para a sua ranhura de implementação de fase apontar para a base de dados recentemente criado, **umbraco-fase-db**. O produção web app (umbraositecms-1) e transição web app (umbracositecms-1-fase) **devem** apontar para bases de dados diferentes.

![Atualizar a cadeia de ligação para a web app com a nova base de dados de transição de transição](./media/app-service-web-staged-publishing-realworld-scenarios/9umbconnstr.png)

- Clique em **definições de obter publicar** a implementação ranhura **fase**. Isto irá transferir um ficheiro de definições de publicar que armazenam todas as informações necessárias por ou matriz de Web do Visual Studio para publicar a aplicação a partir do desenvolvimento local web app para o Azure web app.

 ![Obter publicar a definição da aplicação web transição](./media/app-service-web-staged-publishing-realworld-scenarios/10getpsetting.png)

- Abra a aplicação web do desenvolvimento local no **WebMatrix** ou **Visual Studio**. Neste tutorial estou a utilizar o Web matriz e primeiro precisa de importar o ficheiro de definições de publicar para a sua aplicação web transição

![Importar definições de publicar para Umbraco utilizando Web matriz](./media/app-service-web-staged-publishing-realworld-scenarios/11import.png)

- Rever alterações na caixa de diálogo e implemente a sua aplicação local web para a sua aplicação Azure web, *1-fase de umbracositecms*. Quando implementar ficheiros diretamente a aplicação web do transição irão omitir quaisquer ficheiros existentes na `~/app_data/TEMP/` pasta, tal como estes gerar quando o palco web app é o primeiro iniciado. Também deve omitir o `~/app_data/umbraco.config` ficheiro como esta, também será voltar a ser gerado.

![Rever alterações de publicar matriz web](./media/app-service-web-staged-publishing-realworld-scenarios/12umbpublish.png)

- Depois de publicar com êxito a aplicação de local web Umbraco a transição do web app, navegue para a aplicação web do transição e executar alguns testes de regra de saída quaisquer problemas.

#### <a name="set-up-courier2-deployment-module"></a>Configurar o módulo de implementação de Courier2
Com o módulo [Courier2](http://umbraco.com/products/more-add-ons/courier-2) pode emissão conteúdo, folhas de estilo, módulos de desenvolvimento e muito mais com um simples com o botão direito de uma aplicação web transição para produção web app para uma mais implementações gratuito de sem problemas e reduzir o risco da força a aplicação web do produção quando implementar uma atualização.
Comprar uma licença para Courier2 para o domínio `*.azurewebsites.net` e o seu domínio personalizado (apresentamos http://abc.com) assim que tiver comprado a licença, coloque a licença transferida (. Ficheiro LIC) na `bin` pasta.

![Largue o ficheiro de licença em pasta bin](./media/app-service-web-staged-publishing-realworld-scenarios/13droplic.png)

Transferir o pacote de Courier2 partir [aqui](https://our.umbraco.org/projects/umbraco-pro/umbraco-courier-2/). Inicie sessão na sua aplicação web do fase, diga http://umbracocms-site-stage.azurewebsites.net/umbraco e clique no Menu de **Programador** e selecione **pacotes**. Clique em **instalar o** pacote local

![Programa de instalação do pacote de Umbraco](./media/app-service-web-staged-publishing-realworld-scenarios/14umbpkg.png)

Carregar o pacote de courier2 utilizando o programa de instalação.

![Carregar o pacote do módulo courier](./media/app-service-web-staged-publishing-realworld-scenarios/15umbloadpkg.png)

Para configurar o seu tem de atualizar o ficheiro de courier.config em pasta de **configuração** da sua aplicação web.

```xml
<!-- Repository connection settings -->
 <!-- For each site, a custom repository must be configured, so Courier knows how to connect and authenticate-->
 <repositories>
    <!-- If a custom Umbraco Membership provider is used, specify login & password + set the passwordEncoding to clear: -->
    <repository name="production web app" alias="stage" type="CourierWebserviceRepositoryProvider" visible="true">
      <url>http://umbracositecms-1.azurewebsites.net</url>
      <user>0</user>
      <!--<login>user@email.com</login> -->
      <!-- <password>user_password</password>-->
      <!-- <passwordEncoding>Clear</passwordEncoding>-->
      </repository>
 </repositories>
 ```

Em `<repositories>`, introduza as informações de utilizador e o URL da site de produção. Se estiver a utilizar o fornecedor de membros Umbraco predefinido, em seguida, adicione o ID do utilizador de administração no <user> secção. Se estiver a utilizar um fornecedor de associação Umbraco personalizado, utilize `<login>`,`<password>` Courier2 módulo saber como ligar para o site de produção. Para obter mais detalhes, reveja a [documentação](http://umbraco.com/help-and-support/customer-area/courier-2-support-and-download/developer-documentation) para módulo Courier.

Da mesma forma, instalar o módulo Courier no seu site de produção e configure-o ponto de fase web App no seu ficheiro courier.config respetivos conforme mostrado aqui

```xml
 <!-- Repository connection settings -->
 <!-- For each site, a custom repository must be configured, so Courier knows how to connect and authenticate-->
 <repositories>
    <!-- If a custom Umbraco Membership provider is used, specify login & password + set the passwordEncoding to clear: -->
    <repository name="Stage web app" alias="stage" type="CourierWebserviceRepositoryProvider" visible="true">
      <url>http://umbracositecms-1-stage.azurewebsites.net</url>
      <user>0</user>
      </repository>
 </repositories>
```

Clique no separador de Courier2 no dashboard da aplicação web Umbraco CMS e selecione localizações. Deverá ver o nome do repositório tal como mencionado no `courier.config`. Fazê-lo no seu produção e teste aplicações web.

![Repositório de aplicação de web de destino de vista](./media/app-service-web-staged-publishing-realworld-scenarios/16courierloc.png)

Permite agora aos implementar algum conteúdo de site de transição para o site de produção. Ir para o conteúdo e selecione uma página existente ou criar uma nova página. Posso irá seleccionar uma página existente do meu web app onde o título da página é alterado para **Introdução – novo** e agora clique em **Guardar e publicar**.

![Alterar o título da página e publicar](./media/app-service-web-staged-publishing-realworld-scenarios/17changepg.png)

Agora, selecione a página modificada e, em seguida, *clique direito do rato* para ver todas as opções. Clique em **Courier** para ver a caixa de diálogo de implementação. Clique no **Implementar** para iniciar a implementação

![Diálogo de implementação do módulo Courier](./media/app-service-web-staged-publishing-realworld-scenarios/18dialog1.png)

Rever as alterações e clique em continuar.

![Courier módulo implementação diálogo rever alterações](./media/app-service-web-staged-publishing-realworld-scenarios/19dialog2.png)

Registo de implementação mostra se a implementação foi efetuada com êxito.

 ![Ver registos de implementação do módulo Courier](./media/app-service-web-staged-publishing-realworld-scenarios/20successdlg.png)

Procure a sua aplicação web de produção para ver se as alterações serão refletidas.

 ![Navegue até produção web app](./media/app-service-web-staged-publishing-realworld-scenarios/21umbpg.png)

Para saber mais sobre como utilizar a Courier, reveja a documentação.

#### <a name="how-to-upgrade-umbraco-cms-version"></a>Como atualizar a versão de Umbraco CMS

Courier não irá ajudar a implementar com atualizar a partir de uma versão do Umbraco CMS para outra. Quando atualizar a versão de Umbraco CMS, tem de procurar incompatibilidades com o seu módulos personalizados ou módulos de terceiros e as bibliotecas Umbraco Core. Como prática recomendada

1. SEMPRE cópia de segurança a web app e a base de dados antes de efetuar uma atualização. No Azure Web App, pode configurar cópias de segurança automáticas para os seus Web sites utilizando a cópia de segurança de funcionalidades e restaurar o seu site se for necessário utilizar restaurar funcionalidade. Para obter mais detalhes, consulte o artigo [como efectuar uma cópia da sua aplicação web](web-sites-backup.md) e [como restaurar a sua aplicação web](web-sites-restore.md).

2. Verifique se os pacotes de terceiros que está a utilizar são compatíveis com a versão que está a atualizar para. O pacote de página de transferência, reveja a compatibilidade de projeto com a versão de Umbraco CMS.

Para obter mais detalhes sobre como atualizar a sua aplicação web localmente, siga as orientações como mencionadas [aqui](https://our.umbraco.org/documentation/getting-started/set up/upgrading/general).

Assim que o seu site de desenvolvimento local é atualizado, publica as alterações de teste do web app. Teste a sua aplicação e se todos os agrado, utilize o botão de **trocar** **trocar** seu site de transição para o produção web app. Quando executar a operação **trocar** , pode ver as alterações que serão afectadas na configuração da sua aplicação web. Com esta operação **trocar** , podemos são trocar as aplicações de web e bases de dados. Isto significa que, depois da TROCAR a aplicação web de produção passará a apontar para umbraco-fase-db base de dados e a transição do web app irá apontar para umbraco-ordem de produção-db base de dados.

![Trocar pré-visualização para implementar Umbraco CMS](./media/app-service-web-staged-publishing-realworld-scenarios/22umbswap.png)

A vantagem de trocar a web app e a base de dados:
1. Dá-lhe a capacidade para recuperar uma versão anterior da sua aplicação web com outra **trocar** se existirem problemas de aplicação.
2. Uma atualização terá implementar bases de dados a partir do web app para produção web app e base de dados de transição e ficheiros. Existem muitas coisas que podem correr mal quando implementar ficheiros e base de dados. Ao utilizar a funcionalidade de **trocar** de faixas, podemos reduzir o tempo de inatividade durante uma atualização e reduzir o risco de falhas que podem ocorrer quando implementar alterações.
3. Fornece-lhe a capacidade de fazer **A / B testes** utilizando a funcionalidade de [testes de produção](https://azure.microsoft.com/documentation/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/)

Este exemplo mostra-lhe a flexibilidade da plataforma do qual pode criar personalizados módulos semelhantes ao módulo Umbraco Courier para gerir a implementação em ambientes.

## <a name="references"></a>Referências
[Desenvolvimento de agile software com o serviço de aplicação do Azure](app-service-agile-software-development.md)

[Configurar o teste ambientes para web apps no serviço de aplicação do Azure](web-sites-staged-publishing.md)

[Como bloquear o acesso à faixas de implementação de que não sejam produção web](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)
