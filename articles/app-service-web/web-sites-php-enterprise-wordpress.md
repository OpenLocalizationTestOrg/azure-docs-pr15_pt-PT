<properties
    pageTitle="WordPress de classe empresarial Azure de aplicação de serviço | Microsoft Azure"
    description="Aprenda a alojar um site de WordPress classe empresarial num serviço de aplicação do Azure"
    services="app-service\web"
    documentationCenter=""
    authors="sunbuild"
    manager="yochayk"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.devlang="php"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="web"
    ms.date="10/24/2016"
    ms.author="sumuth"/>

# <a name="enterprise-class-wordpress-on-azure-app-service"></a>WordPress de classe empresarial Azure de aplicação de serviço

Aplicação de serviço de Azure fornece um ambiente dimensionável, seguro e fáceis de utilizar para missão crítico, grande escala [WordPress] [ wordpress] sites. Sites de classe empresarial tal como o [Office] executa o Microsoft propriamente dito[ officeblog] e [Bing] [ bingblog] blogues. Este documento mostra-lhe como pode utilizar as aplicações do Azure aplicação de serviço Web para estabelecer e manter uma classe empresarial, WordPress site baseado na nuvem que pode processar um grande volume de visitantes.

## <a name="architecture-and-planning"></a>Arquitetura e planeamento

Uma instalação de WordPress básica possui requisitos apenas dois.

* **Base de dados do MySQL** - disponíveis através do [ClearDB no Azure Marketplace][cdbnstore], ou pode gerir o seu próprio instalação MySQL no Azure máquinas virtuais utilizando o [Windows] [ mysqlwindows] ou [Linux][mysqllinux].

  > [AZURE.NOTE] ClearDB fornece várias configurações do MySQL, com características de desempenho diferente para cada configuração. Consulte o artigo da [Loja do Azure] [ cdbnstore] para obter informações sobre ofertas fornecido através da loja Azure ou diretamente conforme visto no [Web site de ClearDB](http://www.cleardb.com/pricing.view).

* **PHP 5.2.4 ou maior** -aplicação de serviço de Azure atualmente fornecem [versões PHP 5.4, 5.5 e 5.6][phpwebsite].

    > [AZURE.NOTE] Recomendamos que execute sempre a versão mais recente do PHP para se certificar de que tem as correções de segurança mais recentes.

### <a name="basic-deployment"></a>Implementação básica

Utilizar apenas os requisitos básicos, pode criar uma solução básica dentro de uma região Azure.

![uma aplicação web do Azure e base de dados do MySQL alojado numa região Azure único][basic-diagram]

Enquanto Isto permite-lhe para escala de saída da aplicação através da criação de várias instâncias de aplicações Web do site, tudo o que está alojado numa região geográfica específica os centros de dados. Visitantes de fora desta região poderão ver tempos de resposta lenta quando utilizar o site e, se os centros de dados nesta região descer, pelo que é que a aplicação.


### <a name="multi-region-deployment"></a>Implementação da região com várias

Utilizar o Azure [Gestor de tráfego][trafficmanager], é possível dimensionar o seu site WordPress através de vários regiões geográficas enquanto fornecer apenas um URL para os visitantes. Todos os visitantes do está disponível através do Gestor de tráfego e, em seguida, são encaminhados para uma região com base na configuração de balanceamento de carga.

![uma aplicação Azure web, alojada no várias regiões, utilizando o router CDBR elevada disponibilidade para encaminhar para MySQL ao longo de regiões][multi-region-diagram]

Dentro de cada uma das regiões, o site WordPress seria ainda ser dimensionado em várias instâncias de aplicações Web, mas este dimensionamento é região específico; regiões de tráfego alta podem ser dimensionados diferente tráfego baixo aqueles.

Replicação e encaminhamento a várias bases de dados do MySQL pode ser feita através de do ClearDB [CDBR alta disponibilidade Router] [ cleardbscale] (apresentada à esquerda) ou [MySQL Cluster CGE][cge].

### <a name="multi-region-deployment-with-media-storage-and-caching"></a>Implementação da região com várias com o armazenamento de multimédia e de colocação em cache
Se o site aceita carregamentos ou ficheiros de multimédia de anfitrião, utilize o armazenamento de Blobs do Azure. Se precisar de colocação em cache, considere [Redis cache][rediscache], [Memcache nuvem](http://azure.microsoft.com/gallery/store/garantiadata/memcached/), [MemCachier](http://azure.microsoft.com/gallery/store/memcachier/memcachier/)ou um das ofertas de colocação em cache na [Loja do Azure](http://azure.microsoft.com/gallery/store/).

![uma aplicação Azure web, alojada no várias regiões, com o router CDBR elevada disponibilidade para MySQL, com gerido Cache, armazenamento de BLOBs e CDN][performance-diagram]

Armazenamento de BLOBs é geo-distribuídos regiões por predefinição, para não ter de se preocupar replicação de ficheiros em todos os sites. Também pode ativar o Azure [Conteúdo distribuição rede (CDN)] [ cdn] para armazenamento de BLOBs quais distribui ficheiros para o fim nós mais perto para os seus visitantes.

### <a name="planning"></a>Planeamento

#### <a name="additional-requirements"></a>Requisitos adicionais

Para fazer isto... | Utilize esta opção...
------------------------|-----------
**Carregar ou armazenar ficheiros de grandes dimensões** | [Plug-in para utilizar o armazenamento de Blobs do WordPress][storageplugin]
**Enviar mensagens de e-mail** | [SendGrid] [ storesendgrid] e o [Plug-in do WordPress para utilizar SendGrid][sendgridplugin]
**Nomes de domínio personalizado** | [Configurar um nome de domínio personalizado no serviço de aplicação do Azure][customdomain]
**HTTPS** | [Ativar HTTPS para uma aplicação web na aplicação de serviço do Azure][httpscustomdomain]
**Pré-produção validação** | [Configurar o teste ambientes para web apps no serviço de aplicação do Azure][staging] <p>Mudar de uma aplicação web a partir de teste para produção também move-se a configuração de WordPress. Deverá Certifique-se de que todas as definições são actualizadas para os requisitos para a sua aplicação de produção antes de mudar de aplicação do faseada para produção.</p>
**Monitorização e resolução de problemas** | [Ativar o registo de diagnóstico para aplicações web na aplicação de serviço de Azure] [ log] e [Monitor Web Apps no serviço de aplicação do Azure][monitor]
**Implementar o seu site** | [Implementar uma aplicação web na aplicação de serviço do Azure][deploy]

#### <a name="availability-and-disaster-recovery"></a>Recuperação de disponibilidade e falhas

Para fazer isto... | Utilize esta opção...
------------------------|-----------
**Sites de saldo carga** ou **geo-distribuir sites** | [Encaminhar tráfego com o Gestor de tráfego Azure][trafficmanager]
**Criar cópias de segurança e restauro** | [Cópia de segurança de uma aplicação web na aplicação de serviço de Azure] [ backup] e [Restaurar uma aplicação web na aplicação de serviço do Azure][restore]

#### <a name="performance"></a>Desempenho

Desempenho na nuvem é obtido principalmente através de colocação em cache e escala-out; No entanto a memória, largura de banda e outros atributos do Web Apps alojamento devem ser considerados.

Para fazer isto... | Utilize esta opção...
------------------------|-----------
**Noções sobre as capacidades de instância de aplicação de serviço** | [Detalhes do preços, incluindo as capacidades de camadas de aplicação de serviço][websitepricing]
**Recursos de cache** | [Redis cache][rediscache], [Memcache nuvem](/gallery/store/garantiadata/memcached/), [MemCachier](/gallery/store/memcachier/memcachier/)ou um das ofertas de colocação em cache na [Loja do Azure](/gallery/store/)
**Dimensionar a aplicação** | [Dimensionar uma aplicação web na aplicação de serviço de Azure] [ websitescale] [ClearDB alta disponibilidade encaminhamento]e[cleardbscale]. Se optar por alojar e gerir o seu próprio instalação MySQL, deverá tomar em consideração [MySQL Cluster CGE] [ cge] para escala de saída

#### <a name="migration"></a>Migração

Existem dois métodos para migrar um site de WordPress existente para o serviço de aplicação do Azure.

* ** [WordPress exportar] [ export] ** -Isto exporta o conteúdo do seu blogue, que, em seguida, pode ser importado para um novo site de WordPress no serviço de aplicação do Azure utilizando o [Plug-in do WordPress importador][import].

    > [AZURE.NOTE] Enquanto este processo permite-lhe migrar o conteúdo, não migrar quaisquer plug-ins, temas ou outras personalizações. Estes tem de estar instalados novamente manualmente.

* **Migração manual** - [agregar o seu site] [ wordpressbackup] e [base de dados][wordpressdbbackup], em seguida, manualmente restaurá-la para uma aplicação web na aplicação de serviço de Azure e associada de base de dados do MySQL para migrar altamente personalizadas de sites e evitar tédio de instalar manualmente o plug-ins, temas e outras personalizações.

## <a name="step-by-step-instructions"></a>Instruções passo a passo

### <a name="create-a-wordpress-site"></a>Criar um site de WordPress

1. Utilizar o [Azure Marketplace] [ cdbnstore] para criar uma base de dados do MySQL do tamanho que identificou na secção [arquitetura e planeamento](#planning) , na ou nas regiões que irá alojar o seu site.

2. Siga os passos no [artigo criar uma aplicação web do WordPress na aplicação de serviço de Azure] [ createwordpress] para criar uma aplicação web do WordPress. Ao criar a aplicação web, selecione **utilizar uma base de dados do MySQL existente** e selecione a base de dados criada no passo 1.

Se estiver a migrar um site de WordPress existente, consulte o artigo [migrar um site de WordPress existente para Azure](#Migrate-an-existing-WordPress-site-to-Azure) depois de criar uma nova aplicação web.

### <a name="migrate-an-existing-wordpress-site-to-azure"></a>Migrar um site de WordPress existente para o Azure

Tal como mencionado na secção [arquitetura e planeamento](#planning) , existem duas formas de migrar um site de WordPress.

* **exportar e importar** - dos sites sem o grau de personalização, ou se apenas quiser mover o conteúdo.

* **cópia de segurança e restauro** - para sites com muitas personalização onde pretende mover tudo.

Utilize uma das seguintes secções para migrar o seu site.

#### <a name="the-export-and-import-method"></a>O método de exportação e importação

1. Utilizar [WordPress exportar] [ export] para exportar o seu site existente.

2. Crie uma aplicação web utilizando os passos na secção [criar um site de WordPress](#Create-a-new-WordPress-site) .

3. Inicie sessão no seu site WordPress na Web Apps e clique em **Plug-ins** -> **Adicionar novo**. Procurar e instalar o plug-in **WordPress importador** .

4. Após ter sido instalado o plug-in importador, clique em **Ferramentas** -> **Importar**e, em seguida, selecione **WordPress** para utilizar o plug-in do WordPress importador.

5. Na página **Importar WordPress** , clique em **Escolher ficheiro**. Procure o ficheiro WXR exportado a partir do seu site WordPress existente e, em seguida, escolha **carregar ficheiro e importar**.

6. Clique em **Submeter**. É-lhe pedido que a importação foi efetuada com êxito.

8. Assim que concluiu todos estes passos, reinicie o seu site a partir do seu pá de aplicação web no [portal do Azure][mgmtportal].

Após importar o site, poderá ter de efetuar os seguintes passos para activar definições não contidas no ficheiro de importação.

Se estivesse a utilizar isto... | Faça o seguinte …
------------------ | ----------
**Hiperligações permanentes** | A partir do dashboard de WordPress do novo site, clique em **Definições** -> **hiperligações permanentes** e, em seguida, actualizar a estrutura de hiperligações permanentes
**ligações de imagem/multimédia** | Para atualizar as ligações para a nova localização, utilize o [Plug-in do veludo azuis atualizar URLs][velvet], uma ferramenta de pesquisa e substituir, ou manualmente na base de dados
**Temas** | Aceda a **aparência** -> **tema** e atualizar o tema do site, conforme necessário
**Menus** | Se o seu tema suportar menus, ligações para a home page ainda poderão ter o sub-diretório de antigo incorporado-los. Aceda a **aparência** -> **Menus** e atualizá-las

#### <a name="the-backup-and-restore-method"></a>O método de cópia de segurança e restauro

1. Criar uma cópia de segurança seu WordPress existente do site utilizando as informações no [WordPress cópias de segurança][wordpressbackup].

2. Criar uma cópia de segurança a base de dados existente utilizando as informações no [cópias de segurança da base de dados][wordpressdbbackup].

3. Criar uma base de dados e restaurar a cópia de segurança.

    1. Comprar uma nova base de dados a partir do [Azure Marketplace][cdbnstore], ou uma base de dados do MySQL num [Windows] de configuração[ mysqlwindows] ou [Linux] [ mysqllinux] VM.

    2. Utilizar um cliente do MySQL como [MySQL estar][workbench], ligar-se para a nova base de dados e importar a sua base de dados WordPress.

    3. Actualize a base de dados para alterar as entradas de domínio para o seu novo domínio, Azure aplicação de serviço. Por exemplo, mywordpress.azurewebsites.net. Utilizar a [Procurar e substituir para WordPress bases de dados de Script] [ searchandreplace] para com segurança alterar todas as instâncias.

4. Criar uma aplicação web no portal do Azure e publicar a cópia de segurança WordPress.

    1. Criar uma aplicação web no [portal do Azure] [ mgmtportal] com uma base de dados utilizando o **Novo** -> **Web + Mobile** -> **Azure Marketplace** -> **Web Apps** -> **Web app + SQL** (ou **aplicação Web + MySQL**) -> **Criar**. Configure as definições necessárias para criar uma aplicação web vazia.

    2. Na cópia de segurança WordPress, localize o ficheiro **wp config.php** e abra-o num editor. Substitua as seguintes entradas com as informações para a nova base de dados do MySQL.

        * **DB_NAME** - o nome de utilizador da base de dados

        * **DB_USER** - o nome de utilizador utilizado para aceder a base de dados

        * **DB_PASSWORD** - a palavra-passe de utilizador

        Depois de alterar estas entradas, guarde e feche o ficheiro **wp config.php** .

    3. Utilizar a [Implementar uma aplicação web na aplicação de serviço de Azure] [ deploy] informações para ativar o método de implementação que pretende utilizar e, em seguida, implementar a cópia de segurança WordPress para a sua aplicação web na aplicação de serviço de Azure.

5. Depois de ter sido implementado o site WordPress, deve ser possível aceder à nova site (como um aplicação de serviço web app) utilizando o *. azurewebsite.net URL para o site.

### <a name="configure-your-site"></a>Configurar o seu site

Depois do site WordPress foi criado ou migrado, utilize as seguintes informações para melhorar o desempenho ou ativar funcionalidades adicionais.

Para fazer isto... | Utilize esta opção...
------------- | -----------
**Definir o modo de plano de serviço de aplicação, tamanho e ativar dimensionamento** | [Dimensionar uma aplicação web no serviço de aplicação do Azure][websitescale]
**Ativar as ligações de base de dados persistente** | Por predefinição, WordPress não utilizar ligações de base de dados persistente, que podem fazer com que a sua ligação à base de dados ficam limitada após várias ligações. Para permitir ligações psom, instale o (ligações psom adaptador Plug-in) [https://wordpress.org/plugins/persistent-database-connection-updater/installation/]. 
**Melhorar o desempenho** | <ul><li><p><a href="http://ppe.blogs.msdn.com/b/windowsazure/archive/2013/11/18/disabling-arr-s-instance-affinity-in-windows-azure-web-sites.aspx">Desativar o cookie ARR</a> - pode melhorar o desempenho ao ser executada WordPress numa várias instâncias de Web Apps</p></li><li><p>Ative colocação em cache. <a href="http://msdn.microsoft.com/library/azure/dn690470.aspx">Redis cache</a> (pré-visualização) pode ser utilizada com o <a href="https://wordpress.org/plugins/redis-object-cache/">Redis Plug-in WordPress de cache de objecto</a>, ou utilize um das ofertas de colocação em cache a partir da <a href="/gallery/store/">Loja do Azure</a></p></li><li><p><a href="http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/">Como fazer WordPress mais rápido com Wincache</a> - Wincache está ativada por predefinição para Web Apps</p></li><li><p><a href="../web-sites-scale/">Escala de uma aplicação web na aplicação de serviço de Azure</a> e a utilização <a href="http://www.cleardb.com/developers/cdbr/introduction">ClearDB alta disponibilidade encaminhamento</a> ou <a href="http://www.mysql.com/products/cluster/">MySQL Cluster CGE</a></p></li></ul>
**Utilizar blobs para armazenamento** | <ol><li><p><a href="../storage-create-storage-account/">Criar uma conta de armazenamento do Windows Azure</a></p></li><li><p>Saiba como <a href="../cdn-how-to-use/">utilizar a rede de distribuição conteúdo (CDN)</a> geo-distribuir dados armazenados num blobs.</p></li><li><p>Instalar e configurar o <a href="https://wordpress.org/plugins/windows-azure-storage/">Armazenamento do Windows Azure para plug-in de WordPress</a>.</p><p>Para o programa de configuração detalhado e informações de configuração para o plug-in, consulte o <a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">Manual do utilizador</a>.</p> </li></ol>
**Ativar o correio eletrónico** | Ative <a href="https://azure.microsoft.com/en-us/marketplace/partners/sendgrid/sendgrid-azure/">SendGrid</a> utilizar o arquivo de Azure. Instale o <a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified">Plug-in do SendGrid</a> para WordPress.
**Configurar um nome de domínio personalizado** | [Configurar um nome de domínio personalizado no serviço de aplicação do Azure][customdomain]
**Ativar HTTPS para um nome de domínio personalizado** | [Ativar HTTPS para uma aplicação web na aplicação de serviço do Azure][httpscustomdomain]
**Carregar o saldo ou geo-distribuir o seu site** | [Encaminhar o tráfego com o Gestor de tráfego Azure][trafficmanager]. Se estiver a utilizar um domínio personalizado, consulte o artigo [configurar um nome de domínio personalizado no serviço de aplicação do Azure] [ customdomain] para obter informações sobre como utilizar o Gestor de tráfego com nomes de domínio personalizado
**Ativar as cópias de segurança automatizadas** | [Agregar uma aplicação web na aplicação de serviço do Azure][backup]
**Ativar o registo de diagnóstico** | [Ativar o registo de diagnóstico para web apps no serviço de aplicação do Azure][log]

## <a name="next-steps"></a>Próximos passos

* [Otimização WordPress](http://codex.wordpress.org/WordPress_Optimization)

* [Converter WordPress Multisite no Azure de aplicação de serviço](web-sites-php-convert-wordpress-multisite.md)

* [ClearDB atualizar Assistente do Azure](http://www.cleardb.com/store/azure/upgrade)

* [Alojamento WordPress numa subpasta da sua aplicação web na aplicação de serviço do Azure](http://blogs.msdn.com/b/webapps/archive/2013/02/13/hosting-wordpress-in-a-subfolder-of-your-windows-azure-web-site.aspx)

* [Passo a passo: Criar um site de WordPress utilizando o Azure](http://blogs.technet.com/b/blainbar/archive/2013/08/07/article-create-a-wordpress-site-using-windows-azure-read-on.aspx)

* [Alojar o seu blogue WordPress existente no Azure](http://blogs.msdn.com/b/msgulfcommunity/archive/2013/08/26/migrating-a-self-hosted-wordpress-blog-to-windows-azure.aspx)

* [Ativar as hiperligações permanentes bonito no WordPress](http://www.iis.net/learn/extensions/url-rewrite-module/enabling-pretty-permalinks-in-wordpress)

* [Como migrar e executar o blogue do seu WordPress na aplicação de serviço do Azure](http://www.kefalidis.me/2012/06/how-to-migrate-and-run-your-wordpress-blog-on-windows-azure-websites/)

* [Como executar gratuitamente WordPress na aplicação de serviço do Azure](http://architects.dzone.com/articles/how-run-wordpress-azure)

* [WordPress no Azure em 2 minutos ou menos](http://www.sitepoint.com/wordpress-windows-azure-2-minutes-less/)

* [Mover um blogue WordPress para Azure - parte 1: criar um blogue WordPress no Azure](http://www.davebost.com/2013/07/10/moving-a-wordpress-blog-to-windows-azure-part-1)

* [Mover um blogue WordPress para Azure - parte 2: Transferir o seu conteúdo](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-transferring-your-content)

* [Mover um blogue WordPress para Azure - parte 3: configurar o seu domínio personalizado](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-3-setting-up-your-custom-domain)

* [Mover um blogue WordPress para Azure - parte 4: ache hiperligações permanentes e o URL reescrever regras](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-4-pretty-permalinks-and-url-rewrite-rules)

* [Mover um blogue WordPress para Azure - parte 5: mover a partir de uma subpasta na raiz](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-5-moving-from-a-subfolder-to-the-root)

* [Como configurar uma aplicação web do WordPress na sua conta do Azure](http://www.itexperience.net/2014/01/20/how-to-set-up-a-wordpress-website-in-your-windows-azure-account/)

* [Propping o WordPress no Azure](http://www.johnpapa.net/wordpress-on-azure/)

* [Sugestões para WordPress no Azure](http://www.johnpapa.net/azurecleardbmysql/)

>[AZURE.NOTE] Se pretender começar a aplicação de serviço de Azure antes de inscrever-se para uma conta do Azure, aceda ao [Tentar aplicação de serviço](http://go.microsoft.com/fwlink/?LinkId=523751), onde imediatamente pode criar uma aplicação web do starter curto na aplicação de serviço. Sem cartões de crédito necessários; sem compromissos.

## <a name="whats-changed"></a>O que é alterado
* Para um guia para a alteração de Web sites para a aplicação de serviço Consulte o artigo: [aplicação de serviço de Azure e respectivo impacto na existente dos serviços do Azure](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- URL List -->

[performance-diagram]: ./media/web-sites-php-enterprise-wordpress/performance-diagram.png
[basic-diagram]: ./media/web-sites-php-enterprise-wordpress/basic-diagram.png
[multi-region-diagram]: ./media/web-sites-php-enterprise-wordpress/multi-region-diagram.png
[wordpress]: http://www.microsoft.com/web/wordpress
[officeblog]: http://blogs.office.com/
[bingblog]: http://blogs.bing.com/
[cdbnstore]: http://www.cleardb.com/store/azure
[storageplugin]: https://wordpress.org/plugins/windows-azure-storage/
[sendgridplugin]: http://wordpress.org/plugins/sendgrid-email-delivery-simplified/
[phpwebsite]: web-sites-php-configure.md
[customdomain]: web-sites-custom-domain-name.md
[trafficmanager]: ../traffic-manager/traffic-manager-overview.md
[backup]: web-sites-backup.md
[restore]: web-sites-restore.md
[rediscache]: https://azure.microsoft.com/documentation/services/redis-cache/
[managedcache]: http://msdn.microsoft.com/library/azure/dn386122.aspx
[websitescale]: web-sites-scale.md
[managedcachescale]: http://msdn.microsoft.com/library/azure/dn386113.aspx
[cleardbscale]: http://www.cleardb.com/developers/cdbr/introduction
[staging]: web-sites-staged-publishing.md
[monitor]: web-sites-monitor.md
[log]: web-sites-enable-diagnostic-log.md
[httpscustomdomain]: web-sites-configure-ssl-certificate.md
[mysqlwindows]: ../virtual-machines/virtual-machines-windows-classic-mysql-2008r2.md
[mysqllinux]: ../virtual-machines/virtual-machines-linux-classic-mysql-on-opensuse.md
[cge]: http://www.mysql.com/products/cluster/
[websitepricing]: /pricing/details/app-service/
[export]: http://en.support.wordpress.com/export/
[import]: http://wordpress.org/plugins/wordpress-importer/
[wordpressbackup]: http://wordpress.org/plugins/wordpress-importer/
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[createwordpress]: web-sites-php-web-site-gallery.md
[velvet]: https://wordpress.org/plugins/velvet-blues-update-urls/
[mgmtportal]: https://portal.azure.com/
[wordpressbackup]: http://codex.wordpress.org/WordPress_Backups
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[workbench]: http://www.mysql.com/products/workbench/
[searchandreplace]: http://interconnectit.com/124/search-and-replace-for-wordpress-databases/
[deploy]: web-sites-deploy.md
[posh]: ../powershell-install-configure.md
[Azure CLI]: ../xplat-cli-install.md
[storesendgrid]: https://azure.microsoft.com/marketplace/partners/sendgrid/sendgrid-azure/
[cdn]: ../cdn/cdn-overview.md
