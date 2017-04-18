Sistema de nomes de domínio (DNS) é utilizado para localizar recursos na internet. Por exemplo, quando introduz um endereço de aplicação web no seu browser, ou clique numa ligação numa página web, utiliza DNS para traduzir o domínio para um endereço IP. O endereço IP é ordenar como uma morada, mas não é muito humano amigável. Por exemplo, é muito mais fácil de lembrar um nome de DNS como **contoso.com** , que é não se esqueça de um endereço IP como 192.168.1.88 ou 2001:0:4137:1f67:24a2:3888:9cce:fea3.

No sistema DNS é baseado *os registos*. Registos associam uma específico *nome*, como **contoso.com**, um endereço IP ou outro nome DNS. Quando uma aplicação, tal como um browser, procura um nome no DNS, o registo e utiliza o aponte para que o endereço. Se o valor aponta para um endereço IP, browser irá utilizar esse valor. Se apontar para outro nome DNS, a aplicação tem de fazer resolução novamente. Finalmente, resolução do nome de todos os terminará num endereço IP.

Quando cria uma aplicação web na aplicação de serviço, um nome de DNS é atribuído automaticamente para a aplicação web. Este nome assume a forma de ** &lt;yourwebappname&gt;. azurewebsites.net**. Não existem também é um endereço IP virtual disponíveis para utilização quando criar DNS registos, para que possa criar quer registos que apontem para a **. azurewebsites.net**, ou pode apontar para o endereço IP.

> [AZURE.NOTE] Irá alterar o endereço IP da sua aplicação web se eliminar e recrie a sua aplicação web ou alterar o modo de plano de serviço de aplicação para **livre** após ter sido definida para **básicas**, **partilhada**ou **padrão**.

Também existem vários tipos de registos, cada uma com os seus próprios funções e limitações, mas para web apps só Queremos saber dois, registos *A* e *CNAME* .

###<a name="address-record-a-record"></a>Endereço registo (um)

Um registo a mapas tal como um domínio, tal como **contoso.com** ou **www.contoso.com**, *ou domínio universais* ** \*. contoso.com**, para um endereço IP. No caso de uma aplicação web na aplicação de serviço, o IP virtual do serviço ou IP específico de endereços que o utilizador compradas para a sua aplicação web.

São as vantagens principais de um registo através de um registo CNAME:

* Pode mapear um domínio de raiz como **contoso.com** para um endereço IP; várias entidades de registo permitem apenas este utilizando um registos

* Pode ter uma entrada que utiliza um carácter universal, tais como ** \*. contoso.com**, que seria tratar pedidos de vários subdomínios como **mail.contoso.com**, **blogs.contoso.com**ou **www.contso.com**.

> [AZURE.NOTE] Uma vez que um registo é mapeado para um endereço IP estático, que não consegue resolver automaticamente as alterações ao endereço IP da sua aplicação web. Um endereço IP para utilização com um registos é fornecido quando configurar as definições de nome de domínio personalizado para a sua aplicação web; No entanto, pode alterar este valor se eliminar e recrie a sua aplicação web ou alterar o modo de plano de serviço de aplicação para trás para **livre**.

###<a name="alias-record-cname-record"></a>Alias registo (CNAME)

Um registo CNAME mapas um nome DNS *específico* , tal como **mail.contoso.com** ou **www.contoso.com**, para outro nome de domínio (canónico). No caso de aplicação de serviço Web Apps, o nome de domínio canónico é o ** &lt;yourwebappname >. azurewebsites.net** nome de domínio da sua aplicação web. Depois de criada, o CNAME cria um alias para o ** &lt;yourwebappname >. azurewebsites.net** nome de domínio. A entrada CNAME vai ser resolvido para o endereço IP do seu ** &lt;yourwebappname >. azurewebsites.net** nome de domínio automaticamente, para que se alterar o endereço IP do web app, não possui que efetuar qualquer ação.

> [AZURE.NOTE] Algumas entidades apenas permitem-lhe mapear subdomínios ao utilizar um registo CNAME, tais como **www.contoso.com**e não nomes de raiz, como **contoso.com**. Para mais informações sobre registos CNAME, consulte a documentação fornecida pela sua entidade de registo, <a href="http://en.wikipedia.org/wiki/CNAME_record">a entrada da Wikipédia no registo CNAME</a>ou o documento de <a href="http://tools.ietf.org/html/rfc1035">Nomes de domínio IETF - implementação e especificação</a> .

###<a name="web-app-dns-specifics"></a>Detalhes DNS do Web app

Utilizar um registo com aplicações Web requer que criar primeiro um dos seguintes registos TXT:

* **Para o domínio de raiz** - TXT DNS de um registo de **@** para ** &lt;yourwebappname&gt;. azurewebsites.net**.

* **Para um sub-domínio específico** - um nome de DNS de ** &lt;subdomínio >** para ** &lt;yourwebappname&gt;. azurewebsites.net**. Por exemplo, **blogues** se o registo que **blogs.contoso.com**.

* **Para o caráter universal sub-dodmains** - TXT DNS de um registo de * * * para ** &lt;yourwebappname&gt;. azurewebsites.net**.

Este registo TXT é utilizado para confirmar que é o proprietário do domínio que está a tentar utilizar. Este é para além de criar um registo a apontar para o endereço IP virtual da sua aplicação web.

Pode encontrar o endereço IP e **. azurewebsites.net** nomes para a sua aplicação web executando os seguintes passos:

1. No seu browser, abra o [Portal do Azure](https://portal.azure.com).

2. Na pá **Aplicações Web** , clique no nome da sua aplicação web e, em seguida, selecione **domínios personalizada** a partir da parte inferior da página.

    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3. No pá **domínios personalizados** , irá ver o endereço IP virtual. Guardar esta informação, tal como será utilizada quando criar registos DNS

    ![](./media/custom-dns-web-site/virtual-ip-address.png)

    > [AZURE.NOTE] Não é possível utilizar nomes de domínio personalizado com uma aplicação web do **Free** e tem de actualizar o plano de serviço de aplicação para **partilhado**, **básica**, **padrão**ou camada **Premium** . Para obter mais informações sobre o plano de serviço de aplicação preços camadas, incluindo como alterar a camada comparar da sua aplicação web, consulte o artigo [como dimensionar aplicações web](../articles/web-sites-scale.md).
