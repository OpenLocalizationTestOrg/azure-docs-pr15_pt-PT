<properties 
    pageTitle="Converter WordPress Multisite no Azure de aplicação de serviço" 
    description="Saiba como tirar uma aplicação web do WordPress existente criada através da galeria no Azure e convertê-la para WordPress Multisite" 
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



# <a name="convert-wordpress-to-multisite-in-azure-app-service"></a>Converter WordPress Multisite no Azure de aplicação de serviço

## <a name="overview"></a>Descrição geral

*Por [Carlos Lobaugh][ben-lobaugh], [A Microsoft abrir tecnologias Inc.][ms-open-tech]*

Neste tutorial vai aprender tirar uma aplicação web do WordPress existente criada através da galeria no Azure e convertê-la uma instalação de WordPress Multisite. Para além disso, vai aprender atribuir um domínio personalizado para cada um dos subsites dentro da sua instalação.

É considerada que tem uma instalação existente do WordPress. Se não o fizer, siga os passos fornecidos no [artigo criar um web site da WordPress a partir da galeria no Azure][website-from-gallery].

Converter uma WordPress existente site único instalar para Multisite geralmente é bastante simple e muitos dos passos iniciais provêm diretamente a [Criar uma rede] [ wordpress-codex-create-a-network] página na [WordPress Codex](http://codex.wordpress.org).

Vamos começar.

## <a name="allow-multisite"></a>Permitir Multisite

Tem primeiro de ativar Multisite através de `wp-config.php` ficheiro com o **WP\_permitir\_MULTISITE** constante. Existem dois métodos para editar os seus ficheiros de aplicação web: o primeiro é através de FTP e o segundo através de Git. Se não estiver familiarizado com como configurar um dos seguintes métodos, consulte os seguintes tutoriais:

* [PHP web site com MySQL e FTP][website-w-mysql-and-ftp-ftp-setup]

* [PHP web site com MySQL e Git][website-w-mysql-and-git-git-setup]

Abrir o `wp-config.php` ficheiro com o editor da sua escolha e adicione o seguinte acima a `/* That's all, stop editing! Happy blogging. */` linha.

    /* Multisite */

    define( 'WP_ALLOW_MULTISITE', true );

Certifique-se de que guardar o ficheiro e carregue-o novamente para o servidor!

## <a name="network-setup"></a>Configuração de rede

Inicie sessão para a área de *wp administrador* da sua web app e o deverá conseguir ver um novo item no menu **Ferramentas** denominado **Configuração de rede**. Clique em **Configuração da rede** e preencha os detalhes da sua rede.

![Ecrã de configuração de rede][wordpress-network-setup]

Neste tutorial utiliza o esquema do site de *sub-diretórios* uma vez que deverá sempre funcionar e podemos irá configurar domínios personalizados para cada subsite mais tarde no tutorial. No entanto, deve ser possível para instalar um subdomínio se mapear um domínio através do [Portal do Azure](https://portal.azure.com) e configuração de universais DNS corretamente a configuração.

Para mais informações sobre subdomínio vs configurações subdirectório ver os [tipos de rede realizado] [ wordpress-codex-types-of-networks] artigo sobre o WordPress Codex.

## <a name="enable-the-network"></a>Activar a rede

A rede está agora configurada na base de dados, mas existe um passo restante para ativar a funcionalidade de rede. Finalizar o `wp-config.php` definições e certifique-se `web.config` corretamente encaminha cada site.


Depois de clicar no botão **instalar** na página de *Configuração de rede* , WordPress irá tentar atualizar o `wp-config.php` e `web.config` ficheiros. No entanto, deve verificar sempre os ficheiros para garantir que as atualizações foram efetuada com êxito. Caso contrário, este ecrã apresentará com as atualizações necessárias. Editar e guardar os ficheiros.


Após efetuar estas atualizações que terá de terminar a sessão e iniciar sessão de volta para o dashboard de administração wp.

Agora deverá haver um menu adicional sobre o administrador da barra **Os Meus Sites**de com nome. Este menu permite-lhe controlar a sua nova rede através do dashboard de **Administrador de rede** .

## <a name="adding-custom-domains"></a>Adicionar domínios personalizados

O [Mapeamento de WordPress um domínio] [ wordpress-plugin-wordpress-mu-domain-mapping] Plug-in faz com que a opção para adicionar domínios personalizados para qualquer site na sua rede. Para o plug-in funcionar corretamente, é necessário fazer algumas adicional o programa de configuração no Portal do e também na sua entidade.

## <a name="enable-domain-mapping-to-the-web-app"></a>Activar o mapeamento de domínio para o web app

O modo de plano de [Serviço de aplicação](http://go.microsoft.com/fwlink/?LinkId=529714) **Free** não suporta a adição domínios personalizados às aplicações Web. Irá precisar de alternar para modo **partilhado** ou **padrão** . Para fazer isto:

* Inicie sessão no Portal do Azure e localize a aplicação web. 
* Clique no separador **Dimensionar** nas **Definições**.
* Em **Geral**, selecione *PARTILHADO* ou *padrão*
* Clique em **Guardar**

Poderá receber uma mensagem que lhe pede para confirmar a alteração e confirme a que sua aplicação web pode agora assumir um custo, consoante a utilização e a outros configuração que definiu.

Bastam alguns segundos para processar as novas definições, por isso, agora é uma boa altura para começar a configurar o seu domínio.

## <a name="verify-your-domain"></a>Confirmar o seu domínio

Antes de Azure Web Apps permite-lhe mapear um domínio para o site, tem primeiro de Verifique se tem a autorização para mapear o domínio. Para fazê-lo, tem de adicionar um novo registo CNAME para a sua entrada DNS.

* Iniciar sessão no Gestor de DNS do seu domínio
* Criar um novo CNAME *awverify*
* Aponte *awverify* para *awverify. YOUR_DOMAIN.azurewebsites.NET*

Poderá demorar algum tempo para que as alterações DNS iniciar o efeito completo, por isso, se os passos seguintes não funcionam imediatamente, aceda tornar uma chávena de café, em seguida, voltar atrás e tente novamente.

## <a name="add-the-domain-to-the-web-app"></a>Adicionar o domínio para o web app

Regressar à sua aplicação web através do Portal do Azure, clique em **Definições**e, em seguida, clique em **domínios personalizados e SSL**.

Quando as *definições de SSL* são apresentadas, irá ver os campos onde irá introduzir todos os domínios que pretende atribuir a sua aplicação web. Se um domínio não estiver listado aqui, não irá estar disponível para mapeamento dentro WordPress, independentemente de como o DNS do domínio for configuração.

![Gerir a caixa de diálogo domínios personalizados][wordpress-manage-domains]

Depois de escrever o seu domínio na caixa de texto, Azure verificará o registo CNAME que criou anteriormente. Se o DNS não tem totalmente propagados, irá mostrar um indicador vermelho. Se tiver sido efetuada com êxito, verá uma marca de verificação verde. 

Tome nota do endereço IP listado na parte inferior da caixa de diálogo. Terá de esta opção para configurar o registo para o seu domínio.

## <a name="setup-the-domain-a-record"></a>Configurar o domínio de um registo

Se os outros passos foram bem sucedidos, agora pode atribuir o domínio para a sua aplicação Azure web através de um registo DNS. 

É importante anotar aqui que aplicações Azure web aceitam CNAME e um registos, no entanto, *tem* de utilizar um registo para activar o mapeamento de domínio inicial maiúscula. Um CNAME não pode ser reencaminhado para outro CNAME, que é o Azure criado com YOUR_DOMAIN.azurewebsites.net.

Utilizando o endereço IP do passo anterior, regresse ao seu Gestor de DNS e o registo para apontar para esse IP de configuração.


## <a name="install-and-setup-the-plugin"></a>Instalar e configurar o plug-in

WordPress Multisite atualmente não tem um método incorporado para mapear domínios personalizados. No entanto, existe um plug-in denominado [WordPress um domínio mapeamento] [ wordpress-plugin-wordpress-mu-domain-mapping] a funcionalidade que adiciona por si. Inicie sessão para a parte do administrador de rede do seu site e instalar o plug-in **WordPress um domínio mapeamento** .

Depois de instalar e ativar o plug-in, visite **Definições** > **Mapeamento de domínio** para configurar o plug-in. Na caixa de texto primeira, *Endereço IP do servidor*, introduza o endereço de IP que utilizou para configurar o registo do domínio. Defina as *Opções de domínio* que pretende (as predefinições são geralmente finas) e clique em **Guardar**.

## <a name="map-the-domain"></a>Mapa do domínio

Visite o **Dashboard** para o site que pretende mapear o domínio. Clique em **Ferramentas** > **Mapeamento de domínio** e escreva o novo domínio na caixa de texto e clique em **Adicionar**.

Por predefinição, o novo domínio vai ser reprogramado para o domínio de site gerada automaticamente. Se pretender que todo o tráfego enviado para o novo domínio, selecione a caixa de *domínio principal para este blogue* antes de guardar. Pode adicionar um número de domínios ilimitado a um site, mas só pode ser principal.

## <a name="do-it-again"></a>Fazê-lo novamente

Azure Web Apps permitem-lhe adicionar um número ilimitado de domínios para uma aplicação web. Para adicionar outro domínio terá de executar as secções de **verificar o domínio** e **configurar o domínio de um registo** para cada domínio.  

>[AZURE.NOTE] Se pretender começar a aplicação de serviço de Azure antes de inscrever-se para uma conta do Azure, aceda ao [Tentar aplicação de serviço](http://go.microsoft.com/fwlink/?LinkId=523751), onde imediatamente pode criar uma aplicação web do starter curto na aplicação de serviço. Sem cartões de crédito necessários; sem compromissos.

## <a name="whats-changed"></a>O que é alterado
* Para um guia para a alteração de Web sites para a aplicação de serviço Consulte o artigo: [aplicação de serviço de Azure e respectivo impacto na existente dos serviços do Azure](http://go.microsoft.com/fwlink/?LinkId=529714)

[ben-lobaugh]: http://ben.lobaugh.net
[ms-open-tech]: http://msopentech.com
[website-from-gallery]: https://www.windowsazure.com/develop/php/tutorials/website-from-gallery/
[wordpress-codex-create-a-network]: http://codex.wordpress.org/Create_A_Network
[website-w-mysql-and-ftp-ftp-setup]: https://www.windowsazure.com/develop/php/tutorials/website-w-mysql-and-ftp/#header-0
[website-w-mysql-and-git-git-setup]: https://www.windowsazure.com/develop/php/tutorials/website-w-mysql-and-git/#header-1
[wordpress-network-setup]: ./media/web-sites-php-convert-wordpress-multisite/wordpress-network-setup.png
[wordpress-codex-types-of-networks]: http://codex.wordpress.org/Before_You_Create_A_Network#Types_of_multisite_network
[wordpress-plugin-wordpress-mu-domain-mapping]: http://wordpress.org/extend/plugins/wordpress-mu-domain-mapping/

[wordpress-manage-domains]: ./media/web-sites-php-convert-wordpress-multisite/wordpress-manage-domains.png

 
