<properties
    pageTitle="Configure um nome de domínio personalizado para uma aplicação web no Azure aplicação de serviço de que utiliza o Gestor de tráfego de balanceamento de carga em."
    description="Utilizar um nome de domínio personalizado para um uma aplicação web no serviço de aplicação do Azure que inclui o Gestor de tráfego de balanceamento de carga em."
    services="app-service\web"
    documentationCenter=""
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="robmcm"/>

# <a name="configuring-a-custom-domain-name-for-a-web-app-in-azure-app-service-using-traffic-manager"></a>Configurar um nome de domínio personalizado para uma aplicação web no serviço de aplicação do Azure utilizando o Gestor de tráfego

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[AZURE.INCLUDE [intro](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

Este artigo fornece instruções genéricas para utilizar um nome de domínio personalizado com o serviço de aplicação do Azure que utilizam o Gestor de tráfego de balanceamento de carga em.

[AZURE.INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>
## <a name="understanding-dns-records"></a>Noções sobre registos DNS

[AZURE.INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>
## <a name="configure-your-web-apps-for-standard-mode"></a>Configurar as suas aplicações web do modo padrão

[AZURE.INCLUDE [modes](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>
## <a name="add-a-dns-record-for-your-custom-domain"></a>Adicionar um registo DNS do seu domínio personalizado

> [AZURE.NOTE] Se tiver adquirido domínio através do Azure aplicação de serviço Web Apps, em seguida, ignore a seguir passos e referir-se para o passo final do artigo [Comprar domínio para Web Apps](custom-dns-web-site-buydomains-web-app.md) .

Para associar o seu domínio personalizado uma aplicação web na aplicação de serviço de Azure, tem de adicionar uma nova entrada na tabela DNS do seu domínio personalizado utilizando ferramentas fornecidas pela entidade de registo de domínio que comprou o seu nome de domínio a partir de. Utilize os passos seguintes para localizar e utilizar as ferramentas DNS.

1. Inicie sessão na sua conta na sua entidade e procure uma página para gerir os registos DNS. Procure ligações ou áreas do site denominada como **Nome de domínio**, **DNS**ou de **Gestão de servidor de nomes**. Muitas vezes uma ligação a esta página pode ser encontrada ser visualizar as informações da conta e, em seguida, está à procura de uma ligação como **My domains**.

1. Depois de ter encontrado a página de gestão do seu nome de domínio, procure uma ligação que lhe permite editar os registos DNS. Isto pode estar listado como um **ficheiro de zona**, **Registos de DNS**, ou como uma ligação de configuração **Avançadas** .

    * A página provavelmente terá alguns registos já criados, tal como uma entrada a associação '**@**'ou'\*' com uma página de 'estacionamento domínio'. Também pode conter os registos do subdomínios comuns, como **www**.
    * A página será refere **registos CNAME**ou fornecer uma seta de lista pendente para selecionar um tipo de registo. -Lo também poderá mencionar outros registos tal como um e os **registos** **MX**. Em alguns casos, serão chamados por outros nomes como um **Registo de Alias**registos CNAME.
    * A página também terá campos que lhe permitem **mapa** a partir de um **nome do anfitrião** ou o **nome de domínio** para o outro nome de domínio.

1. Enquanto os detalhes da entidade de registo de cada variam, em geral mapear *a partir do* seu nome de domínio personalizado (como **contoso.com**,) *para* o nome de domínio do Gestor de tráfego (**contoso.trafficmanager.net**) que é utilizado para a sua aplicação web.

    > [AZURE.NOTE] Em alternativa, se um registo já se encontra na utilização e precisa de preemptively vincular as suas aplicações para o mesmo, pode criar um registo CNAME adicional. Por exemplo, para vincular preemptively **www.contoso.com** para a sua aplicação web, crie um registo CNAME de **awverify.www** para **contoso.trafficmanager.net**. Em seguida, pode adicionar "www.contoso.com" para a sua aplicação Web sem alterar o registo CNAME de "www". Para obter mais informações, consulte o artigo [criar registos DNS para uma aplicação web num domínio personalizado][CREATEDNS].

1. Assim que tiver terminado de adicionar ou modificar os seus registos DNS na sua entidade de registo, guarde as alterações.

<a name="enabledomain"></a>
## <a name="enable-traffic-manager"></a>Activar o Gestor de tráfego

[AZURE.INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

## <a name="next-steps"></a>Próximos passos

Para mais informações, consulte o [Centro de programadores do Node.js](/develop/nodejs/).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[CREATEDNS]: ../dns/dns-web-sites-custom-domain.md
