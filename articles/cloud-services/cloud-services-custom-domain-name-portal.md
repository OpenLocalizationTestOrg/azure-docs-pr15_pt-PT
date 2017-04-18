<properties
    pageTitle="Configurar um nome de domínio personalizado no serviços em nuvem | Microsoft Azure"
    description="Saiba como expor o aplicação Azure ou os dados na Internet num domínio personalizado ao configurar as definições de DNS.  Estes exemplos utilizam o portal do Azure."
    services="cloud-services"
    documentationCenter=".net"
    authors="Thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="adegeo"/>

# <a name="configuring-a-custom-domain-name-for-an-azure-cloud-service"></a>Configurar um nome de domínio personalizado para um serviço em nuvem Azure

> [AZURE.SELECTOR]
- [Portal do Azure](cloud-services-custom-domain-name-portal.md)
- [Azure portal clássico](cloud-services-custom-domain-name.md)

Quando cria um serviço na nuvem, Azure atribui-lhe para um subdomínio do **cloudapp.net**. Por exemplo, se o seu serviço de nuvem se chamar "contoso", os utilizadores poderão aceder a sua aplicação num URL, como http://contoso.cloudapp.net. Azure também atribui um endereço IP virtual.

No entanto, também pode expor a aplicação no seu próprio nome de domínio, como **contoso.com**. Este artigo explica como reservar ou configurar um nome de domínio personalizado para funções de web do serviço na nuvem.

Fazer, já undestand que registos CNAME e A são? [Saltar anteriores a explicações](#add-a-cname-record-for-your-custom-domain).

> [AZURE.NOTE]
> Os procedimentos nesta tarefa aplicam-se aos serviços em nuvem Azure. Para os serviços de aplicação, consulte o artigo [Esta](../app-service-web/web-sites-custom-domain-name.md). Para contas de armazenamento, consulte o artigo [Esta](../storage/storage-custom-domain-name.md).

<p/>

> [AZURE.TIP]
> Começar a utilizar mais rápido - utilizar a nova Azure [instruções guiadas](http://support.microsoft.com/kb/2990804)!  Torna associando um nome de domínio personalizado e comunicações proteger (SSL) com serviços em nuvem Azure ou sites públicos do Azure num instante.

## <a name="understand-cname-and-a-records"></a>Compreender os registos CNAME e A

CNAME (alias registos ou) e um registos permitem-lhe associar um nome de domínio com um servidor específico (ou neste caso, do serviço) no entanto estes têm um funcionamento diferente. Também existem algumas considerações específicas ao utilizar um registos com serviços em nuvem Azure que deverá tomar em consideração antes de decidir qual deseja utilizar.

### <a name="cname-or-alias-record"></a>Registo CNAME ou Alias

Um registo CNAME de mapas do domínio *específico* , tal como **contoso.com** ou **www.contoso.com**, para um nome de domínio canónico. Neste caso, o nome de domínio canónico é o **.cloudapp [myapp] .net** nome de domínio do seu Azure alojado aplicação. Depois de criada, o CNAME cria um alias para o **.cloudapp [myapp] .net**. A entrada CNAME vai ser resolvido para o endereço IP do seu **.cloudapp [myapp] .net** automaticamente, do serviço para que se alterar o endereço IP do serviço em nuvem, não possui que efetuar qualquer ação.

> [AZURE.NOTE]
> Algumas entidades apenas permitem-lhe mapear subdomínios ao utilizar um registo CNAME, tais como www.contoso.com e não nomes de raiz, como contoso.com. Para mais informações sobre registos CNAME, consulte a documentação fornecida pela sua entidade de registo, [a entrada da Wikipédia no registo CNAME](http://en.wikipedia.org/wiki/CNAME_record)ou o documento de [Nomes de domínio IETF - implementação e especificação](http://tools.ietf.org/html/rfc1035) .

### <a name="a-record"></a>Um registo

*Um registo a* mapas tal como um domínio, tal como **contoso.com** ou **www.contoso.com**, *ou domínio universais* ** \*. contoso.com**, para um endereço IP. No caso de uma Azure serviço na nuvem, o IP virtual do serviço. Para que o benefício principal de um registo através de um registo CNAME seja que pode ter uma entrada que utiliza um carácter universal, tais como \* **. contoso.com**, que seria tratar pedidos de vários subdomínios como **mail.contoso.com**, **login.contoso.com**ou **www.contso.com**.

> [AZURE.NOTE]
> Uma vez que um registo é mapeado para um endereço IP estático, que não consegue resolver automaticamente alterações para o endereço IP do seu serviço de nuvem. Os endereços IP utilizados pelo seu serviço de nuvem que são atribuído a primeira vez que implementar uma ranhura vazia (produção ou transição.) Se eliminar a implementação para a ranhura, o endereço IP é disponibilizado pelo Azure e qualquer implementações futuras a ranhura podem ser dada um novo endereço IP.
>
> Convenientemente, o endereço IP de uma ranhura implementação determinado (produção ou transição) é mantido quando trocar entre a transição e implementações de produção ou efetuar uma atualização no local de uma implementação existente. Para obter mais informações sobre como efectuar estas ações, consulte [como gerir serviços em nuvem](cloud-services-how-to-manage.md).


## <a name="add-a-cname-record-for-your-custom-domain"></a>Adicionar um registo CNAME para o seu domínio personalizado

Para criar um registo CNAME, tem de adicionar uma nova entrada na tabela DNS do seu domínio personalizado utilizando as ferramentas fornecidas pela sua entidade de registo. Cada entidade de registo tem um método semelhante, mas ligeiramente diferente para especificar um registo CNAME, mas os conceitos são os mesmos.

1. Utilize um dos seguintes métodos para localizar o **. cloudapp.net** atribuído ao seu serviço de nuvem de nome de domínio.

    * Inicie sessão no [portal do Azure], selecione o serviço de nuvem, observe a secção **Essentials** e, em seguida, localize a entrada de **URL do Site** .

        ![secção de vista rápida a mostrar o URL do site][csurl]
            
        **OU**
  
    * Instalar e configurar o [Powershell do Azure](../powershell-install-configure.md)e, em seguida, utilize o seguinte comando:

        ```powershell
        Get-AzureDeployment -ServiceName yourservicename | Select Url
        ```
    
    Guarde o nome de domínio utilizado no URL devolvido por qualquer método indicado, tal como será necessário ao criar um registo CNAME.

1.  Inicie sessão no site do seu DNS entidade de registo e vá para a página para gerir DNS. Procure ligações ou áreas do site denominada como **Nome de domínio**, **DNS**ou de **Gestão de servidor de nomes**.

2.  Agora pode encontre onde pode selecionar ou introduza do CNAME. Poderá ter que selecionar o tipo de registo a partir de uma lista pendente para baixo ou aceder a uma página de definições avançadas. Deverá procure as palavras **CNAME**, **Alias**ou **subdomínios**.

3.  Também tem de fornecer o alias do domínio ou subdomínio para o CNAME, como **www** se pretender criar um alias para **www.customdomain.com**. Se pretender criar um alias para o domínio de raiz, poderá estar listado como o '**@**' símbolo nas ferramentas de DNS da sua entidade de registo.

4. Em seguida, tem de fornecer um nome de anfitrião canónico, que é o domínio de **cloudapp.net** da sua aplicação neste caso.

Por exemplo, o seguinte registo CNAME reencaminha todo o tráfego do **www.contoso.com** para **contoso.cloudapp.net**, o nome de domínio personalizado da sua aplicação implementada:

| Nome de alias/anfitrião/subdomínio | Domínio canónico     |
| ------------------------- | -------------------- |
| www                       | contoso.cloudapp.NET |

> [AZURE.NOTE]
Visitante do **www.contoso.com** nunca verão o anfitrião verdadeiro (contoso.cloudapp.net), para que o processo de reencaminhamento de chamadas é invisível para o utilizador final.

> O exemplo acima só se aplica ao tráfego no subdomínio do **www** . Uma vez que não é possível utilizar carateres universais com registos CNAME, terá de criar um CNAME para cada domínio/subdomínio. Se pretende direcionar tráfego de subdomínios, tais como *. contoso.com, para o seu endereço de cloudapp.net, pode configurar um * *URL redirecionar* * ou * *Reencaminhar URL** entrada nas suas definições de DNS, ou criar um registo.


## <a name="add-an-a-record-for-your-custom-domain"></a>Adicionar um registo a para o seu domínio personalizado

Para criar um registo, primeiro tem de localizar o endereço IP virtual do seu serviço de nuvem. Em seguida, adicione uma nova entrada na tabela DNS do seu domínio personalizado utilizando as ferramentas fornecidas pela sua entidade de registo. Cada entidade de registo tem um método semelhante, mas ligeiramente diferente para especificar um registo, mas os conceitos são os mesmos.

1. Utilize um dos seguintes métodos para obter o endereço IP do seu serviço de nuvem.

    * Início de sessão no [portal do Azure], selecione o seu serviço de nuvem, observe a secção **Essentials** e, em seguida, localize a entrada de **endereços IP público** .

        ![secção de vista rápida a mostrar o VIP][vip]

        **OU**

    * Instalar e configurar o [Powershell do Azure](../powershell-install-configure.md)e, em seguida, utilize o seguinte comando:

        ```powershell
        get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip
        ```
    
    Guarde o endereço IP, como será necessário ao criar um registo.

1.  Inicie sessão no site do seu DNS entidade de registo e vá para a página para gerir DNS. Procure ligações ou áreas do site denominada como **Nome de domínio**, **DNS**ou de **Gestão de servidor de nomes**.

2.  Agora pode encontre onde pode selecionar ou introduza um registo. Poderá ter que selecionar o tipo de registo a partir de uma lista pendente para baixo ou aceder a uma página de definições avançadas.

3. Selecione ou introduza o domínio ou o subdomínio que irá utilizar este registo. Por exemplo, selecione **www** se pretender criar um alias para **www.customdomain.com**. Se pretende criar uma entrada de caracteres universais para todos os subdomínios, introduza '__*__'. Isto vai abranger todos os subdomínios como **mail.customdomain.com**, **login.customdomain.com**e **www.customdomain.com**.

    Se pretender criar um registo a para o domínio de raiz, poderá estar listado como o '**@**' símbolo nas ferramentas de DNS da sua entidade de registo.

4. Introduza o endereço IP do seu serviço de nuvem no campo fornecido. Associa a entrada de domínio utilizada no registo de com o endereço IP da sua implementação do serviço de nuvem.

Por exemplo, o seguinte procedimento que um registo reencaminha todo o tráfego de **contoso.com** **137.135.70.239**, o endereço IP da sua aplicação implementado:

| Nome de anfitrião/subdomínio | Endereço IP     |
| ------------------- | -------------- |
| @                   | 137.135.70.239 |


Este exemplo demonstra a criação de um registo a para o domínio de raiz. Se pretender criar uma entrada de caracteres universais para todos os subdomínios de folha de rosto, irá introduzir '__*__' como o subdomínio.

>[AZURE.WARNING]
>Endereços IP no Azure são dinâmicos por predefinição. Irá provavelmente pretende utilizar um [endereço IP reservado](../virtual-network/virtual-networks-reserved-public-ip.md) para se certificar de que o seu endereço IP não é alterada.

## <a name="next-steps"></a>Próximos passos

* [Como gerir serviços em nuvem](cloud-services-how-to-manage.md)
* [Como mapear CDN conteúdo para um domínio personalizado](../cdn/cdn-map-content-to-custom-domain.md)
* [Configuração geral do seu serviço de nuvem](cloud-services-how-to-configure-portal.md).
* Saiba como [Implementar um serviço na nuvem](cloud-services-how-to-create-deploy-portal.md).
* Configure [os certificados ssl](cloud-services-configure-ssl-certificate-portal.md).

[Expose Your Application on a Custom Domain]: #access-app
[Add a CNAME Record for Your Custom Domain]: #add-cname
[Expose Your Data on a Custom Domain]: #access-data
[VIP swaps]: cloud-services-how-to-manage-portal.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production
[Create a CNAME record that associates the subdomain with the storage account]: #create-cname
[Portal do Azure]: https://portal.azure.com
[vip]: ./media/cloud-services-custom-domain-name-portal/csvip.png
[csurl]: ./media/cloud-services-custom-domain-name-portal/csurl.png
 