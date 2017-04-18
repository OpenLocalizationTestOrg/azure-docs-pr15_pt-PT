<properties
    pageTitle="Criar e utilizar um balanceador de carga interno com um ambiente do serviço de aplicação | Microsoft Azure"
    description="Criar e utilizar um Auxiliar com um ILB"
    services="app-service"
    documentationCenter=""
    authors="ccompy"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="ccompy"/>

# <a name="using-an-internal-load-balancer-with-an-app-service-environment"></a>Utilizar um balanceador de carga interno com um ambiente do serviço de aplicação #

A funcionalidade de aplicação serviço Environments(ASE) é uma opção de serviço Premium do serviço de aplicação do Azure que fornece uma capacidade de configuração avançada que não está disponível nos carimbos de inquilinos com várias.  A funcionalidade de Auxiliar essencialmente implementa o serviço de aplicação do Azure no seu Network(VNet) Virtual do Azure.  Para obter uma maior compreensão das capacidades de disponibilizadas pelos ambientes de serviço de aplicação ler [o que é um ambiente do serviço de aplicação] [ WhatisASE] documentação.  Se não souber as vantagens de funcionar numa VNet leia as [FAQ sobre a rede Virtual Azure][virtualnetwork].  


## <a name="overview"></a>Descrição geral ##


Pode ser implementada uma Auxiliar com um ponto final acessível de internet ou com um endereço IP no seu VNet.  Para definir o endereço IP para um endereço de VNet tem implementar o Auxiliar com um Balancer(ILB) carga interno.  Quando o Auxiliar está configurado com uma ILB fornecem:

- o próprio domínio ou subdomínio.  Para tornar mais fáceis, este documento assume subdomínio, mas pode configurá-la de qualquer forma.  
- o certificado utilizado para HTTPS
- Gestão de DNS do seu subdomínio.  


Em troca, pode fazer coisas como:

- aplicações de intranet de anfitrião, como a linha de aplicações empresariais, segura na nuvem que pode aceder através de um Site no Site ou ExpressRoute VPN
- aplicações de anfitrião na nuvem que não sejam encontram listadas servidores DNS públicos
- criar aplicações de back-end de internet, isolada quais as aplicações de front-end podem integrar em segurança


#### <a name="disabled-functionality"></a>Funcionalidade desativada ####

Existem algumas coisas que não consegue efetuar ao utilizar um Auxiliar ILB.  Esses aspetos incluem:

- utilizar IPSSL
- atribuir endereços IP às aplicações específicas
- comprar e utilizando um certificado com uma aplicação através do portal.  Obviamente ainda pode obter certificados diretamente com uma autoridade de certificação e utilizá-la com as suas aplicações, não apenas através do portal do Azure.


## <a name="creating-an-ilb-ase"></a>Criar um Auxiliar ILB ##

Criar um Auxiliar ILB não é muito diferente a partir de criar um Auxiliar normalmente.  Para um debate mais aprofundado sobre a criação de um Auxiliar ler [como criar um ambiente do serviço de aplicação][HowtoCreateASE].  O processo para criar um Auxiliar ILB é a mesma entre criar um VNet durante a criação de Auxiliar ou selecionar uma VNet pré-existentes.  Para criar um Auxiliar ILB: 

1.  No portal do Azure selecione **New-Web > + Mobile -> ambiente de serviço de aplicação**
2.  Selecione a sua subscrição
3.  Selecionar ou criar um grupo de recursos
4.  Selecione ou crie um VNet
5.  Criar uma sub-rede se selecionar uma VNet
6.  Selecione **Virtual/localização de rede-> configuração VNet** e configure o tipo de VIP para interno
7.  Fornecer o nome de subdomínio (esta será o subdomínio utilizado para as aplicações que criou neste Auxiliar)
8.  Selecione Ok e, em seguida, criar


![][1]


Dentro de pá rede Virtual existe uma opção de configuração de VNet.  Permite-selecione entre um VIP interno ou VIP externos.  A predefinição é externo.  Se tiver definido como externo seu Auxiliar irá utilizar um VIP acessível de internet.  Se selecionar interna, o Auxiliar será configurada com um ILB num endereço IP do seu VNet.  


Após selecionar interna, a capacidade de adicionar mais endereços IP para o Auxiliar é removida e em vez disso, será necessário fornecer o subdomínio da Auxiliar.  Num Auxiliar com um VIP externos o nome da Auxiliar é utilizado no subdomínio para aplicações criadas nesse Auxiliar.  
Se o seu Auxiliar era denominada ***contosotest*** e a aplicação em que era denominado de Auxiliar ***mytest*** , em seguida, o subdomínio seria do formato ***contosotest.p.azurewebsites.net*** e o URL para essa aplicação seria ***mytest.contosotest.p.azurewebsites.net***.  
Se definir o tipo de VIP para interna, o nome do seu Auxiliar não é utilizado no subdomínio para o Auxiliar.  Especifique o subdomínio explicitamente.  Se o subdomínio foi ***contoso.corp.net*** e que efetuou uma aplicação nesse Auxiliar denominada ***timereporting*** o URL para essa aplicação seria ***timereporting.contoso.corp.net***.


## <a name="apps-in-an-ilb-ase"></a>Aplicações num Auxiliar ILB ##

Criar uma aplicação num Auxiliar ILB é igual a criar uma aplicação num Auxiliar normalmente.  

1. No portal do Azure selecione **New-Web > + Mobile -> Web** ou **Mobile** ou **Aplicação API**
2. Introduza o nome da aplicação
2. Selecione a subscrição
3. Selecionar ou criar o grupo de recursos
4. Selecione ou crie Plan(ASP) de serviço de aplicação.  Se criar uma nova ASP, em seguida, selecione o Auxiliar como a localização e selecione o conjunto de trabalho que pretende o ASP ser criada em.  Quando cria o ASP selecionar o Auxiliar como a localização e o conjunto de trabalho.  Ao especificar o nome da aplicação irá ver que o subdomínio em seu nome da aplicação é substituído pelo subdomínio para o Auxiliar.   
5. Selecione criar.  Deverá selecione a caixa de verificação **Afixar ao dashboard** se pretender que a aplicação que aparecem no seu dashboard.  

![][2]


No nome da aplicação o nome do subdomínio é atualizado para refletir o subdomínio da sua Auxiliar.  


## <a name="post-ilb-ase-creation-validation"></a>Validação de criação de ILB Auxiliar de mensagem ##

Um Auxiliar ILB é ligeiramente diferente que a que não sejam - ILB Auxiliar.  Tal como já Note-se de que precisa para gerir os seus próprios DNS e também tem de fornecer o seu próprio certificado para ligações HTTPS.  


Depois de criar o seu Auxiliar será Repare que o subdomínio mostra o subdomínio que especificou e existe um novo item no menu de **definição** chamado **Certificado ILB**.  O Auxiliar é criado com um certificado autoassinado que facilita testar HTTPS.  O portal irá indicar que precisa para fornecer o seu próprio certificado para HTTPS mas esta é a unidade que tenha um certificado que vai com o seu subdomínio.  

![][3]


Se está apenas a experimentar coisas e não sabe como criar um certificado, pode utilizar a aplicação da consola do IIS MMC para criar um certificado de assinatura automático.  Quando estiver criado pode exportá-lo como um ficheiro. pfx e, em seguida, carregue-o na IU do certificado de ILB. Quando acede a um site protegido com um certificado autoassinado, o browser irá dar-lhe um aviso de que está a aceder ao site não é seguro devido a impossibilidade de validar o certificado.  Se pretender evitar que aviso tem um certificado corretamente com a sessão iniciado, que corresponde ao seu subdomínio e tem uma cadeia de fidedignidade é reconhecida pelo seu browser.

![][6]

Se pretender experimentar o fluxo com o seus próprio certificados e testar o HTTP e HTTPS acesso à sua Auxiliar:

1.  Aceda a Auxiliar IU após a criação de Auxiliar **Auxiliar -> Definições -> ILB certificados**
2.  Configurar o certificado ILB ao selecionar ficheiro pfx do certificado e forneça a palavra-passe.  Este passo assume algum tempo a processar e a mensagem que está em curso uma operação de dimensionamento será apresentada.
3.  Obter o endereço ILB para o seu Auxiliar (**Auxiliar -> propriedades -> endereço IP Virtual**)
4.  Criar uma aplicação web no Auxiliar após a criação 
5.  Criar uma VM se não tiver um nesse VNET (não está na mesma sub-rede como a quebra Auxiliar ou coisas)
6.  Configurar o DNS para o subdomínio.  Pode utilizar um caráter universal com o seu subdomínio no seu DNS ou se pretender efetuar algumas testes simples, edite o ficheiro de anfitriões no seu VM para definir o nome da aplicação web para o endereço VIP IP.  Se o Auxiliar tivesse o nome do subdomínio. ilbase.com e efetuadas a mytestapp de aplicação web para que-seria ser dirigida mytestapp.ilbase.com, em seguida, defina que no seu ficheiro de anfitriões.  (No Windows o ficheiro de anfitriões estiver no C:\Windows\System32\drivers\etc\)
7.  Utilizar um browser nesse VM e aceda ao http://mytestapp.ilbase.com (ou tudo o que é o nome da aplicação web com o subdomínio)
8.  Utilizar um browser desse VM e aceda ao https://mytestapp.ilbase.com que terá de aceitar a falta de segurança, se utilizar um certificado autoassinado.  


O endereço IP do seu ILB está na lista as propriedades de como o endereço IP Virtual

![][4]


## <a name="using-an-ilb-ase"></a>Utilizar um Auxiliar ILB ##

#### <a name="network-security-groups"></a>Grupos de segurança de rede ####

Um Auxiliar ILB permite isolamento de rede para as suas aplicações, como as aplicações não são acessíveis ou até mesmo conhecidos através da internet.  Este é excelente para alojamento de sites da intranet tal como a linha de aplicações empresariais.  Quando for necessário restringir o acesso mesmo ainda pode utilizar ainda Groups(NSGs) de segurança de rede para controlar o acesso ao nível de rede. 


Se pretender utilizar NSGs ainda mais restringir o acesso, em seguida, é necessário para se certificar de que não interromper a comunicação que necessita da Auxiliar para poder trabalhar com.  Apesar do acesso HTTP/HTTPS é de apenas através de ILB utilizado pela Auxiliar o Auxiliar ainda depende do recurso fora de VNet.  Para ver que aceder à rede ainda é necessária procure as informações no documento no [Controlar o tráfego de entrada para um ambiente do serviço de aplicação] [ ControlInbound] e o documento no [Detalhes de configuração de rede para a aplicação de serviço ambientes com ExpressRoute][ExpressRoute].  


Para configurar o seu NSGs tem de conhecer o endereço IP que é utilizado pelo Azure para gerir o seu Auxiliar.  Esse endereço IP também é o endereço IP de saída a partir do seu Auxiliar se torna pedidos através da internet.  Para localizar este IP endereço aceda a **Definições -> propriedades** e localize o **Endereço IP saída**.  

![][5]


#### <a name="general-ilb-ase-management"></a>Gestão de geral ILB Auxiliar ####

Gerir uma Auxiliar ILB em grande medida é igual a gerir um Auxiliar normalmente.  É necessário para dimensionar o seu agrupamentos de trabalho para alojar mais ocorrências ASP e dimensionar o seus servidores front-End para processar maiores quantidades de tráfego HTTP/HTTPS.  Para obter informações gerais sobre como gerir a configuração de um Auxiliar, ler o documento sobre [como configurar um ambiente do serviço de aplicação][ASEConfig].  


Os itens de gestão adicionais são gestão de certificado e gestão de DNS.  Tem de obter e carregar o certificado utilizado para HTTPS após a criação de ILB Auxiliar e substituí-lo antes que expire.  Uma vez que o Azure é o proprietário do domínio base pode fornecemos a certificados para ASEs com um VIP externos.  Uma vez que o subdomínio utilizado por um Auxiliar ILB pode ser qualquer coisa, tem de fornecer o seu próprio certificado para HTTPS. 


#### <a name="dns-configuration"></a>Configuração de DNS ####

Ao utilizar um VIP externos o DNS é gerido pelo Azure.  Qualquer aplicação criada no seu Auxiliar é adicionada automaticamente ao Azure DNS que é um público DNS.  Um Auxiliar ILB tem que gerir os seus próprios DNS.  Para um determinado subdomínio como contoso.corp.net necessárias para criar DNS A registos que apontem para o seu endereço ILB para:

    * 
    publicar *.SCM ftp 


## <a name="getting-started"></a>Introdução
Todos os artigos e como-para para o ambientes de serviço de aplicação estão disponíveis no [ficheiro Leia-me para a aplicação de serviço ambientes](../app-service/app-service-app-service-environments-readme.md).

Para iniciar a aplicação de serviço ambientes, consulte [Introdução à aplicação de serviço ambientes][WhatisASE]

Para obter mais informações sobre a plataforma de aplicação de serviço do Azure, consulte o artigo [Aplicação de serviço de Azure][AzureAppService].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!--Image references-->
[1]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createilbase.png
[2]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createapp.png
[3]: ./media/app-service-environment-with-internal-load-balancer/ilbase-newase.png
[4]: ./media/app-service-environment-with-internal-load-balancer/ilbase-vip.png
[5]: ./media/app-service-environment-with-internal-load-balancer/ilbase-externalvip.png
[6]: ./media/app-service-environment-with-internal-load-balancer/ilbase-ilbcertificate.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[ControlInbound]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[ExpressRoute]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/
[vnetnsgs]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[ASEConfig]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
