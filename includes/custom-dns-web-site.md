#<a name="configuring-a-custom-domain-name-for-an-azure-website"></a>Configurar um nome de domínio personalizado para um Web site Azure

Quando cria um Web site, Azure fornece um subdomínio amigável no domínio azurewebsites.net para que os seus utilizadores podem aceder ao seu Web site utilizando um URL, como http://&lt;meu site >. azurewebsites.net. No entanto, se configurar os seus Web sites para partilhado ou modo padrão, pode mapear o seu Web site para o seu próprio nome de domínio.

Opcionalmente, pode utilizar o Gestor de tráfego Azure para carregar o tráfego de entrada saldo ao seu Web site. Para mais informações sobre como Gestor de tráfego funciona com sites públicos, consulte o artigo [Controlar o tráfego de Web Sites do Azure com o Gestor de tráfego Azure][trafficmanager].

> [AZURE.NOTE] Os procedimentos nesta tarefa se aplicam a sites públicos do Azure; para serviços em nuvem, consulte o artigo <a href="/develop/net/common-tasks/custom-dns/">configurar um nome de domínio personalizado no Azure</a>.

> [AZURE.NOTE] Os passos nesta tarefa requerem que configurar os seus Web sites para partilhado ou o modo padrão, que podem ser alteradas quanto são faturada para a sua subscrição. Ver <a href="/pricing/details/web-sites/">Detalhes de preços de Web sites</a> para obter mais informações.

Neste artigo:

-   [Noções sobre registos CNAME e A](#understanding-records)
-   [Configurar os web sites para o modo partilhado ou padrão](#bkmk_configsharedmode)
-   [Adicionar os seus web sites ao Gestor de tráfego](#trafficmanager)
-   [Adicionar um CNAME do seu domínio personalizado](#bkmk_configurecname)
-   [Adicionar um registo a para o seu domínio personalizado](#bkmk_configurearecord)

<h2><a name="understanding-records"></a>Compreender os registos CNAME e A</h2>

CNAME (alias registos ou) e um registos permitem-lhe associar um nome de domínio um Web site, no entanto, que cada têm um funcionamento diferente.

###<a name="cname-or-alias-record"></a>Registo CNAME ou Alias

Um registo CNAME de mapas do domínio *específico* , tal como **contoso.com** ou **www.contoso.com**, para um nome de domínio canónico. Neste caso, o nome de domínio canónico é o quer o ** &lt;myapp >. azurewebsites.net** nome de domínio do seu Web site Azure ou os ** &lt;myapp >. trafficmgr.com** nome de domínio do seu perfil do Gestor de tráfego. Depois de criada, o CNAME cria um alias para o ** &lt;myapp >. azurewebsites.net** ou ** &lt;myapp >. trafficmgr.com** nome de domínio. A entrada CNAME vai ser resolvido para o endereço IP do seu ** &lt;myapp >. azurewebsites.net** ou ** &lt;myapp >. trafficmgr.com** nome de domínio automaticamente, para que se alterar o endereço IP do Web site, não possui que efetuar qualquer ação.

> [AZURE.NOTE] Algumas entidades de registo de domínio apenas permitem-lhe mapear subdomínios ao utilizar um registo CNAME, tais como www.contoso.com e não nomes de raiz, como contoso.com. Para mais informações sobre registos CNAME, consulte a documentação fornecida pela sua entidade de registo, <a href="http://en.wikipedia.org/wiki/CNAME_record">a entrada da Wikipédia no registo CNAME</a>ou o documento de <a href="http://tools.ietf.org/html/rfc1035">Nomes de domínio IETF - implementação e especificação</a> .

###<a name="a-record"></a>Um registo

Um registo a mapas tal como um domínio, tal como **contoso.com** ou **www.contoso.com**, *ou domínio universais* ** \*. contoso.com**, para um endereço IP. No caso de um site do Azure, o IP virtual do serviço ou IP específico do endereço que o utilizador compradas do seu Web site. Para que o benefício principal de um registo através de um registo CNAME seja que pode ter uma entrada que utiliza um carácter universal, tais como * **. contoso.com**, que seria processar tais como os pedidos para vários subdomínios * *mail.contoso.com**, * *login.contoso.com**, ou * *www.contso.com**.

> [AZURE.NOTE] Uma vez que um registo é mapeado para um endereço IP estático, que não consegue resolver automaticamente as alterações ao endereço IP do seu Web site. Um endereço IP para utilização com um registos é fornecido quando configurar as definições de nome de domínio personalizado para o seu Web site; No entanto, pode alterar este valor se eliminar e recrie o seu Web site ou alterar o modo de Web site para trás para libertar.

> [AZURE.NOTE] Registos de não podem ser utilizados com o Gestor de tráfego de balanceamento de carga. Para obter mais informações, consulte o artigo [Controlar o tráfego de Web Sites do Azure com o Gestor de tráfego Azure][trafficmanager].

<a name="bkmk_configsharedmode"></a><h2>Configurar os seus Web sites para o modo partilhado ou padrão</h2>

Definir um nome de domínio personalizado num Web site só está disponível para os modos de padrão para sites públicos do Azure e partilhado. Antes de mudar de um Web site do modo de Web site gratuito para partilhado ou modo de Web site padrão, tem primeiro de remover despesas maiúsculas no local para a sua subscrição do Web site. Para mais informações sobre preços de modo de partilhado e padrão, consulte o artigo [Detalhes do preços][PricingDetails].

1. No seu browser, abra o [Portal de gestão][portal].
2. No separador de **Web sites** , clique no nome do seu site.

    ![][standardmode1]

3. Clique no separador **escala** .

    ![][standardmode2]


4. Na secção **Geral** , defina o modo de Web site ao clicar em **PARTILHADO**.

    ![][standardmode3]

    > [AZURE.NOTE] Se estiver a utilizar o Gestor de tráfego com este Web site, tem de utilizar selecione modo padrão em vez de partilhado.

5. Clique em **Guardar**.
6. Quando lhe for pedido sobre o aumento do custo para modo partilhado (ou para o modo padrão se optar por padrão), clique em **Sim** se concordar.

    <!--![][standardmode4]-->

    **Nota**<br />
   Se receber um erro de "Configurar escala para o Web site 'nome do Web site' falhou", pode utilizar o botão Detalhes para obter mais informações.

<a name="trafficmanager"></a><h2>(Opcional) Adicionar os seus Web sites ao Gestor de tráfego</h2>

Se pretender utilizar o seu Web site com o Gestor de tráfego, execute os passos seguintes.

1. Se ainda não tiver um perfil do Gestor de tráfego, utilize as informações no [artigo criar um perfil do Gestor de tráfego utilizando a criação rápida] [ createprofile] para criar uma. Nota a **. trafficmgr.com** associado com o seu perfil do Gestor de tráfego de nome de domínio. Será utilizada num passo posterior.

2. Utilize as informações em [Adicionar ou eliminar pontos finais] [ addendpoint] para adicionar o seu Web site como um ponto final no seu perfil do Gestor de tráfego.

    > [AZURE.NOTE] Se não estiver listado o seu Web site ao adicionar um ponto final, certifique-se de que está configurado para o modo padrão. Tem de utilizar o modo padrão do seu Web site para poder trabalhar com o tráfego Manager.

3. Inicie sessão no site do seu DNS entidade de registo e ir para a página para gerir DNS. Procure ligações ou áreas do site denominada como **Nome de domínio**, **DNS**ou de **Gestão de servidor de nomes**.

4. Agora pode encontre onde pode selecionar ou introduza registos CNAME. Poderá ter que selecionar o tipo de registo a partir de uma lista pendente para baixo ou aceder a uma página de definições avançadas. Deverá procure as palavras **CNAME**, **Alias**ou **subdomínios**.

5. O alias do domínio ou subdomínio também tem de fornecer para o CNAME. Por exemplo, **www** se pretender criar um alias para **www.customdomain.com**.

5. Também tem de fornecer um nome de anfitrião que é o nome de domínio canónico para este alias CNAME. Este é o **. trafficmgr.com** nome do seu Web site.

Por exemplo, o seguinte registo CNAME reencaminha todo o tráfego do **www.contoso.com** para **contoso.trafficmgr.com**, o nome de domínio de um Web site:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Nome de alias/anfitrião/subdomínio</strong></td>
<td><strong>Domínio canónico</strong></td>
</tr>
<tr>
<td>www</td>
<td>contoso.trafficmgr.com</td>
</tr>
</table>

Visitante do **www.contoso.com** nunca verão o anfitrião verdadeiro (contoso.azurewebsite.net), para que o processo de reencaminhamento de chamadas é invisível para o utilizador final.

> [AZURE.NOTE] Se estiver a utilizar o Gestor de tráfego com um Web site, não terá de seguir os passos nas secções seguintes, '**Adicionar um CNAME do seu domínio personalizado**' e '**Adicionar um registo a para o seu domínio personalizado**'. O registo CNAME criado nos passos anteriores encaminhar tráfego de entrada para o tráfego Gestor de designs, em seguida, encaminha o tráfego para a endpoint(s) de Web site.

<a name="bkmk_configurecname"></a><h2>Adicionar um CNAME do seu domínio personalizado</h2>

Para criar um registo CNAME, tem de adicionar uma nova entrada na tabela DNS do seu domínio personalizado utilizando ferramentas fornecidas pela sua entidade de registo. Cada entidade de registo tem um método semelhante, mas ligeiramente diferente para especificar um registo CNAME, mas os conceitos são os mesmos.

1. Utilize um dos seguintes métodos para localizar o **. azurewebsite.net** nome de domínio atribuído ao seu Web site.

    * Inicie sessão no [Portal de gestão do Azure][portal], selecione o seu site, selecione **Dashboard**e, em seguida, localize a entrada de **URL do Site** na secção **vista rápida** .

    * Instalar e configurar o [Powershell do Azure](/manage/install-and-configure-windows-powershell/)e, em seguida, utilize o seguinte comando:

            get-azurewebsite yoursitename | select hostnames

    * Instalar e configurar a [Interface de linha de comandos do Azure](/manage/install-and-configure-cli/)e, em seguida, utilize o seguinte comando:

            azure site domain list yoursitename

    Guardar esta **. azurewebsite.net** atribuir um nome, à medida que será utilizado nos passos seguintes.

3. Inicie sessão no site do seu DNS entidade de registo e ir para a página para gerir DNS. Procure ligações ou áreas do site denominada como **Nome de domínio**, **DNS**ou de **Gestão de servidor de nomes**.

4. Agora pode encontre onde pode selecionar ou introduza registos CNAME. Poderá ter que selecionar o tipo de registo a partir de uma lista pendente para baixo ou aceder a uma página de definições avançadas. Deverá procure as palavras **CNAME**, **Alias**ou **subdomínios**.

5. O alias do domínio ou subdomínio também tem de fornecer para o CNAME. Por exemplo, **www** se pretender criar um alias para **www.customdomain.com**. Se pretender criar um alias para o domínio de raiz, poderá estar listado como o '**@**' símbolo nas ferramentas de DNS da sua entidade de registo.

5. Também tem de fornecer um nome de anfitrião que é o nome de domínio canónico para este alias CNAME. Este é o **. azurewebsite.net** nome do seu Web site.

Por exemplo, o seguinte registo CNAME reencaminha todo o tráfego do **www.contoso.com** para **contoso.azurewebsite.net**, o nome de domínio de um Web site:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Nome de alias/anfitrião/subdomínio</strong></td>
<td><strong>Domínio canónico</strong></td>
</tr>
<tr>
<td>www</td>
<td>contoso.azurewebsite.NET</td>
</tr>
</table>

Visitante do **www.contoso.com** nunca verão o anfitrião verdadeiro (contoso.azurewebsite.net), para que o processo de reencaminhamento de chamadas é invisível para o utilizador final.

> [AZURE.NOTE] O exemplo acima só se aplica ao tráfego no subdomínio do __www__ . Uma vez que não é possível utilizar carateres universais com registos CNAME, terá de criar um CNAME para cada domínio/subdomínio. Se pretende direcionar tráfego de subdomínios, tais como *. contoso.com, para o seu endereço de azurewebsite.net, pode configurar uma entrada de __URL redirecionar__ ou __Reencaminhar URL__ nas suas definições de DNS ou crie um registo.

> [AZURE.NOTE] Pode demorar algum tempo para o seu CNAME a serem propagadas através do sistema DNS. Não é possível definir o CNAME para o Web site até o CNAME tem propagadas. Pode utilizar um serviço como o <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> para verificar se o CNAME está disponível.

###<a name="add-the-domain-name-to-your-website"></a>Adicionar o nome de domínio ao Web site

Depois do registo CNAME para nome de domínio tem propagadas, tem de o associar ao seu Web site. Pode adicionar o nome de domínio personalizado definido pelo registo CNAME ao seu Web site utilizando um da linha de comandos de Azure Interface (Azure CLI) ou utilizando o Portal de gestão do Azure.

**Para adicionar um nome de domínio utilizando as ferramentas de linha de comandos**

Instalar e configurar a [Interface de comandos do Azure](/manage/install-and-configure-cli/)e, em seguida, utilize o seguinte comando:

    azure site domain add customdomain yoursitename

Por exemplo, o seguinte procedimento irá adicionar um nome de domínio personalizado do **www.contoso.com** para o Web site **contoso.azurewebsite.net** :

    azure site domain add www.contoso.com contoso

Pode confirmar que foi adicionado o nome de domínio personalizado para o Web site utilizando o seguinte comando:

    azure site domain list yoursitename

A lista devolvida por este comando deve conter o nome de domínio personalizado, bem como a predefinição **. azurewebsite.net** entrada.

**Para adicionar um nome de domínio utilizando o Portal de gestão do Azure**

1. No seu browser, abra o [Portal de gestão do Azure][portal].

2. No separador de **Web sites** , clique no nome do seu site, selecione **Dashboard**e, em seguida, selecione **Gerir domínios** a partir da parte inferior da página.

    ![][setcname2]

6. Na caixa de texto de **Nomes de domínio** , escreva o nome do domínio que configurou.

    ![][setcname3]

6. Clique na marca de verificação para aceitar o nome de domínio.

Assim que tiver concluído a configuração, o nome de domínio personalizado será listado na secção de **nomes de domínio** da página **Configurar** do seu Web site.

<a name="bkmk_configurearecord"></a><h2>Adicionar um registo a para o seu domínio personalizado</h2>

Para criar um registo, primeiro tem de localizar o endereço IP do seu Web site. Em seguida, adicione uma entrada na tabela DNS do seu domínio personalizado utilizando as ferramentas fornecidas pela sua entidade de registo. Cada entidade de registo tem um método semelhante, mas ligeiramente diferente para especificar um registo, mas os conceitos são os mesmos. Para além de criar um registo, também terá de criar um registo CNAME que utiliza o Azure para verificar o registo.

1. No seu browser, abra o [Portal de gestão do Azure][portal].

2. No separador de **Web sites** , clique no nome do seu site, selecione **Dashboard**e, em seguida, selecione **Gerir domínios** a partir da parte inferior do ecrã.

    ![][setcname2]

5. Na caixa de diálogo **Gerir os domínios personalizados** , localize **O endereço de IP para utilizar ao configurar um registos**. Copie o endereço IP. Isto vai ser utilizado quando criar o registo.

5. Na caixa de diálogo **Gerir os domínios personalizados** , tenha em atenção awverify nome de domínio no final do texto na parte superior da caixa de diálogo. Deve ser **awverify.mysite.azurewebsites.net** onde o **meu site** é o nome do seu Web site. Copie esta situação, tal como é o nome de domínio utilizado quando criar a verificação do registo CNAME.

6. Inicie sessão no site do seu DNS entidade de registo e ir para a página para gerir DNS. Procure ligações ou áreas do site denominada como **Nome de domínio**, **DNS**ou de **Gestão de servidor de nomes**.

6. Localize onde pode selecionar ou introduza registos CNAME e a. Poderá ter que selecionar o tipo de registo a partir de uma lista pendente para baixo ou aceder a uma página de definições avançadas.

7. Execute os seguintes passos para criar o registo:

    1. Selecione ou introduza o domínio ou o subdomínio que irá utilizar o registo. Por exemplo, selecione **www** se pretender criar um alias para **www.customdomain.com**. Se pretende criar uma entrada de caracteres universais para todos os subdomínios, introduza '__*__'. Isto vai abranger todos os subdomínios como **mail.customdomain.com**, **login.customdomain.com**e **www.customdomain.com**.

        Se pretender criar um registo a para o domínio de raiz, poderá estar listado como o '**@**' símbolo nas ferramentas de DNS da sua entidade de registo.

    2. Introduza o endereço IP do seu serviço de nuvem no campo fornecido. Associa a entrada de domínio utilizada no registo de com o endereço IP da sua implementação do serviço de nuvem.

        Por exemplo, o seguinte procedimento que um registo reencaminha todo o tráfego de **contoso.com** **137.135.70.239**, o endereço IP do nossa aplicação implementado:

        <table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
        <tr>
        <td><strong>Nome de anfitrião/subdomínio</strong></td>
        <td><strong>Endereço IP</strong></td>
        </tr>
        <tr>
        <td>@</td>
        <td>137.135.70.239</td>
        </tr>
        </table>

        Este exemplo demonstra a criação de um registo a para o domínio de raiz. Se pretender criar uma entrada de caracteres universais para todos os subdomínios de folha de rosto, irá introduzir '__*__' como o subdomínio.

7. Em seguida, crie um registo CNAME que tem um alias de **awverify**e um domínio canónico de **awverify.mysite.azurewebsites.net** que obteve anterior.

    > [AZURE.NOTE] Enquanto um alias de awverify poderão funcionar para algumas entidades de registo, as outras pessoas podem exigir o nome de domínio alias cheio de awverify.www.customdomainname.com ou awverify.customdomainname.com.

    Por exemplo, o seguinte procedimento irá criar um registo CNAME Azure pode utilizar para verificar a configuração de registo de respostas.

    <table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
    <tr>
    <td><strong>Nome de alias/anfitrião/subdomínio</strong></td>
    <td><strong>Domínio canónico</strong></td>
    </tr>
    <tr>
    <td>awverify</td>
    <td>awverify.contoso.azurewebsites.NET</td>
    </tr>
    </table>

> [AZURE.NOTE] Pode demorar algum tempo para o awverify CNAME a serem propagadas através do sistema DNS. Não é possível definir o nome de domínio personalizado definido pelo registo para o Web site até a awverify CNAME tem propagadas. Pode utilizar um serviço como o <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> para verificar se o CNAME está disponível.

###<a name="add-the-domain-name-to-your-website"></a>Adicionar o nome de domínio ao Web site

Depois do registo CNAME de **awverify** para nome de domínio tem propagadas, em seguida, pode associar o domínio personalizado definido pelo registo com o seu site. Pode adicionar o nome de domínio personalizado definido pelo registo ao seu Web site utilizando um do clip do Azure ou utilizando o Portal de gestão do Azure.

**Para adicionar um nome de domínio utilizando a Interface da linha de comandos do Azure (Azure CLI)**

Instalar e configurar o [Clip do Azure](/manage/install-and-configure-cli/)e, em seguida, utilize o seguinte comando:

    azure site domain add customdomain yoursitename

Por exemplo, o seguinte procedimento irá adicionar um nome de domínio personalizado do **contoso.com** para o Web site **contoso.azurewebsite.net** :

    azure site domain add contoso.com contoso

Pode confirmar que foi adicionado o nome de domínio personalizado para o Web site utilizando o seguinte comando:

    azure site domain list yoursitename

A lista devolvida por este comando deve conter o nome de domínio personalizado, bem como a predefinição **. azurewebsite.net** entrada.

**Para adicionar um nome de domínio utilizando o Portal de gestão do Azure**

1. No seu browser, abra o [Portal de gestão do Azure][portal].

2. No separador de **Web sites** , clique no nome do seu site, selecione **Dashboard**e, em seguida, selecione **Gerir domínios** a partir da parte inferior da página.

    ![][setcname2]

6. Na caixa de texto de **Nomes de domínio** , escreva o nome do domínio que configurou.

    ![][setcname3]

6. Clique na marca de verificação para aceitar o nome de domínio.

Assim que tiver concluído a configuração, o nome de domínio personalizado será listado na secção de **nomes de domínio** da página **Configurar** do seu Web site.

> [AZURE.NOTE] Depois de ter adicionado o nome de domínio personalizado definido pelo registo ao seu Web site, pode remover o registo CNAME de awverify utilizando as ferramentas de fornecida pela sua entidade de registo. No entanto, se pretender adicionar A outra registo no futuro, terá de recriar o awverify registo antes de poder associar o novo nome de domínio definido por novo um registo com o Web site.

## <a name="next-steps"></a>Próximos passos

-   [Como gerir web sites](/manage/services/web-sites/how-to-manage-websites/)

-   [Configurar um certificado SSL para Web Sites](/develop/net/common-tasks/enable-ssl-web-site/)


<!-- Bookmarks -->

[Configure your web sites for shared mode]: #bkmk_configsharedmode
[Configure the CNAME on your domain registrar]: #bkmk_configurecname
[Configure a CNAME verification record on your domain registrar]: #bkmk_configurecname
[Configure an A record for the domain name]:#bkmk_configurearecord
[Set the domain name in management portal]: #bkmk_setcname

<!-- Links -->

[PricingDetails]: /pricing/details/
[portal]: http://manage.windowsazure.com
[digweb]: http://www.digwebinterface.com/
[cloudservicedns]: ../articles/custom-dns.md
[trafficmanager]: ../articles/app-service-web/web-sites-traffic-manager.md
[addendpoint]: ../articles/traffic-manager/traffic-manager-endpoints.md
[createprofile]: ../articles/traffic-manager/traffic-manager-manage-profiles.md

<!-- images -->

[setcname1]: ../media/dncmntask-cname-5.png


<!-- images -->
[standardmode1]: ./media/custom-dns-web-site/dncmntask-cname-1.png
[standardmode2]: ./media/custom-dns-web-site/dncmntask-cname-2.png
[standardmode3]: ./media/custom-dns-web-site/dncmntask-cname-3.png
[standardmode4]: ./media/custom-dns-web-site/dncmntask-cname-4.png


[setcname2]: ./media/custom-dns-web-site/dncmntask-cname-6.png
[setcname3]: ./media/custom-dns-web-site/dncmntask-cname-7.png
