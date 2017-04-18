Sistema de nomes de domínio (DNS) é utilizado para localizar coisas na internet. Por exemplo, quando introduz um endereço no seu browser, ou clique numa ligação numa página web, utiliza DNS para traduzir o domínio para um endereço IP. O endereço IP é ordenar como uma morada, mas não é muito humano amigável. Por exemplo, é muito mais fácil de lembrar um nome de DNS como **contoso.com** , que é não se esqueça de um endereço IP como 192.168.1.88 ou 2001:0:4137:1f67:24a2:3888:9cce:fea3.

No sistema DNS é baseado *os registos*. Registos associam um específico *nome*, como **contoso.com**, um endereço IP ou outro nome DNS. Quando uma aplicação, tal como um browser, procura um nome no DNS, o registo e utiliza o aponte para que o endereço. Se o valor aponta para um endereço IP, browser irá utilizar esse valor. Se apontar para outro nome DNS, a aplicação tem de fazer resolução novamente. Finalmente, resolução do nome de todos os terminará num endereço IP.

Quando cria um Web site Azure, um nome de DNS é atribuído automaticamente para o site. Este nome assume a forma de ** &lt;yoursitename&gt;. azurewebsites.net**. Quando adiciona o seu Web site como um ponto final Azure tráfego Gestor, o seu Web site, em seguida, está acessível através de ** &lt;yourtrafficmanagerprofile&gt;. trafficmanager.net** domínio.

> [AZURE.NOTE] Quando o seu Web site está configurado como um ponto final de tráfego Gestor, irá utilizar o **. trafficmanager.net** endereço quando criar registos DNS.

> Só pode utilizar registos CNAME com o Gestor de tráfego

Também existem vários tipos de registos, cada uma com os seus próprios funções e limitações, mas para Web sites configurados como pontos finais de tráfego Gestor, só queremos acerca de um; Registos *CNAME* .

###<a name="cname-or-alias-record"></a>Registo CNAME ou Alias

Um registo CNAME mapas um nome DNS *específico* , tal como **mail.contoso.com** ou **www.contoso.com**, para outro nome de domínio (canónico). No caso de Web sites Azure utilizando o Gestor de tráfego, o nome de domínio canónico é o ** &lt;myapp >. trafficmanager.net** nome de domínio do seu perfil do Gestor de tráfego. Depois de criada, o CNAME cria um alias para o ** &lt;myapp >. trafficmanager.net** nome de domínio. A entrada CNAME vai ser resolvido para o endereço IP do seu ** &lt;myapp >. trafficmanager.net** nome de domínio automaticamente, para que se alterar o endereço IP do Web site, não possui que efetuar qualquer ação.

Depois de tráfego chega ao Gestor de tráfego, em seguida, encaminha o tráfego para o seu site utilizando o método que está configurada para balanceamento de carga. Este é completamente transparente para que os visitantes do seu Web site. Só verão o nome de domínio personalizado no respetivo browser.

> [AZURE.NOTE] Algumas entidades apenas permitem-lhe mapear subdomínios ao utilizar um registo CNAME, tais como **www.contoso.com**e não nomes de raiz, como **contoso.com**. Para mais informações sobre registos CNAME, consulte a documentação fornecida pela sua entidade de registo, <a href="http://en.wikipedia.org/wiki/CNAME_record">a entrada da Wikipédia no registo CNAME</a>ou o documento de <a href="http://tools.ietf.org/html/rfc1035">Nomes de domínio IETF - implementação e especificação</a> .
