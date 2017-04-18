<properties 
    pageTitle="Configurar uma Firewall de aplicação Web (WAF) para o ambiente de aplicação de serviço" 
    description="Saiba como configurar uma firewall de aplicação web à frente do seu ambiente de serviço de aplicação." 
    services="app-service\web" 
    documentationCenter="" 
    authors="naziml" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/17/2016" 
    ms.author="naziml"/>    

# <a name="configuring-a-web-application-firewall-waf-for-app-service-environment"></a>Configurar uma Firewall de aplicação Web (WAF) para o ambiente de aplicação de serviço

## <a name="overview"></a>Descrição geral ##
Web firewalls aplicação como o [Barracuda WAF para Azure](https://www.barracuda.com/programs/azure) que está disponível na [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/barracudanetworks/waf-byol/) ajuda-o a segura que aplicações web através da inspeção de tráfego da web para bloquear injecções de SQL, Scripting publicação em vários sites, software maligno carregamentos e aplicação DDoS e outros ataques de entrada. -Lo também inspeciona as respostas a partir dos servidores de back-end web para prevenção de perda de dados (DLP). Combinado com o isolamento adicionais dimensionamento e fornecida pela aplicação serviço ambientes, isto fornece um ambiente ideal para anfitrião empresas web crítico aplicações que precisam da suportar maliciosos pedidos e o tráfego de grande volume.

+[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="setup"></a>Programa de configuração ##
Para este documento que podemos irá configurar o nosso ambiente de serviço de aplicação atrás de vários carga com balanceamento de instâncias do Barracuda WAF para que apenas o tráfego do WAF pode contactar o ambiente do serviço de aplicação e não estar acessível a partir da DMZ. Recomendamos também terá o Gestor de tráfego Azure à frente do nossas instâncias Barracuda WAF para balanceamento de carga ao longo de centros de dados Azure e regiões. Um diagrama de alto nível da configuração seria como o que é apresentado abaixo.

![Arquitetura][Architecture] 

> Nota: Com a introdução de [ILB suporte para o ambiente de serviço de aplicação](app-service-environment-with-internal-load-balancer.md), pode configurar o Auxiliar para ser inacessível a partir de DMZ e apenas estar disponível para a rede privada. 

## <a name="configuring-your-app-service-environment"></a>Configurar o seu ambiente de aplicação de serviço ##
Para configurar um ambiente do serviço de aplicação, consulte a [nossa documentação](app-service-web-how-to-create-an-app-service-environment.md) sobre o assunto. Assim que tiver um ambiente do serviço de aplicação criado, pode criar [Aplicações Web](app-service-web-overview.md), [Aplicações de API](../app-service-api/app-service-api-apps-why-best-platform.md) e [Aplicações Mobile](../app-service-mobile/app-service-mobile-value-prop.md) este ambiente que serão todos protegidos por trás WAF podemos configurar na secção seguinte.

## <a name="configuring-your-barracuda-waf-cloud-service"></a>Configurar o seu serviço de nuvem WAF Barracuda ##
Barracuda tem um [artigo detalhado](https://campus.barracuda.com/product/webapplicationfirewall/article/WAF/DeployWAFInAzure) sobre como implementar o respetivo WAF numa máquina virtual no Azure. Mas porque pretende redundância e não apresentar um ponto de falha único, que pretende implementar pelo menos 2 WAF instância VMs no serviço na nuvem mesmo quando seguir estas instruções.

### <a name="adding-endpoints-to-cloud-service"></a>Adicionar os pontos finais para o serviço em nuvem ###
Assim que tiver 2 ou mais ocorrências de WAF VM no seu serviço de nuvem pode utilizar o [Azure portal](https://portal.azure.com/) para adicionar HTTP e HTTPS os pontos finais são utilizados pela aplicação, conforme mostrado na imagem abaixo.

![Configurar o ponto final][ConfigureEndpoint]

Se as suas aplicações utiliza outros pontos finais, certifique-se para adicioná-los a esta lista também. 

### <a name="configuring-barracuda-waf-through-its-management-portal"></a>Configurar Barracuda WAF através do seu Portal de gestão ###
Barracuda WAF utiliza TCP porta 8000 para a configuração através do seu portal de gestão. Uma vez que temos várias instâncias do VMs WAF terá de repetir estes passos para cada instância VM. 


> Nota: Depois de ter com a configuração de WAF, remova o ponto final TCP/8000 todos os seus VMs WAF para manter o seu WAF seguro.

Adicione o ponto final de gestão, conforme mostrado na imagem abaixo para configurar o seu WAF Barracuda.

![Adicionar o ponto final de gestão][AddManagementEndpoint]
 
Utilize um browser para navegar para o ponto final de gestão no seu serviço de nuvem. Se o seu serviço de nuvem for chamado test.cloudapp.net, teria de acesso este ponto final, procurando para http://test.cloudapp.net:8000. Deverá visualizar uma página de início de sessão como abaixo, que pode iniciar sessão com as credenciais que especificou na fase de configuração de WAF VM.

![Página de gestão de início de sessão][ManagementLoginPage]

Uma vez login deverá ver um dashboard como aquela na imagem abaixo que irá apresentar estatísticas básicas sobre a proteção de WAF.

![Dashboard de gestão][ManagementDashboard]

Clicar no separador serviços irá permitem-lhe configurar o seu WAF para os serviços que está a proteger. Para obter mais detalhes sobre como configurar o seu WAF Barracuda pode consultar a [documentação](https://techlib.barracuda.com/waf/getstarted1). No exemplo abaixo de uma aplicação Web do Azure servir o tráfego no HTTP e HTTPS foi configurada.

![Gestão de adicionar serviços][ManagementAddServices]

> Nota: Dependendo da forma como as suas aplicações estão configuradas e as funcionalidades que estão a ser utilizadas no seu ambiente de serviço de aplicação, terá de reencaminhar o tráfego para o TCP portas que não seja 80 e 443, por exemplo, se tiver o programa de configuração do IP SSL para uma aplicação Web. Para uma lista de portas de rede utilizadas nos ambientes de serviço de aplicação, consulte a secção de portas de rede de [documentação de tráfego de entrada do controlo](app-service-app-service-environment-control-inbound-traffic.md) .

## <a name="configuring-microsoft-azure-traffic-manager-optional"></a>Configurar o Gestor de tráfego de Microsoft Azure (opcional) ##
Se a sua aplicação está disponível no várias regiões, e, em seguida, pretende carregar equilibrá-los por trás do [Gestor de tráfego Azure](../traffic-manager/traffic-manager-overview.md). Para fazê-lo pode adicionar um ponto final no [portal clássica Azure](https://manage.azure.com) utilizando o nome de serviço na nuvem para sua WAF no perfil do Gestor de tráfego, conforme mostrado na imagem abaixo. 

![Ponto final do Gestor de tráfego][TrafficManagerEndpoint]

Se a sua aplicação requer autenticação, certifique-se de que tem alguns recursos que não necessitam de qualquer autenticação para o Gestor de tráfego para executar o ping para a disponibilidade da sua aplicação. Pode configurar o URL na secção Configurar no [portal clássica Azure](https://manage.azure.com) conforme apresentado abaixo.

![Configurar o Gestor de tráfego][ConfigureTrafficManager]

Para reencaminhar a utilização do Gestor de tráfego ping a partir do seu WAF à aplicação, é necessário para a configuração traduções de Web site no seu WAF Barracuda para reencaminhar o tráfego para a sua aplicação, conforme mostrado no exemplo abaixo.

![Web site traduções][WebsiteTranslations]

## <a name="securing-traffic-to-app-service-environment-using-network-security-groups-nsg"></a>Proteger o tráfego para o ambiente de aplicação de serviço utilizar grupos de segurança de rede (NSG)##
Siga a [documentação de tráfego de entrada de controlo](app-service-app-service-environment-control-inbound-traffic.md) para obter detalhes sobre como restringir o tráfego para o seu ambiente de serviço de aplicação a partir do WAF apenas utilizando o endereço VIP do serviço na nuvem. Eis um exemplo de comando do Powershell para realizar esta tarefa para a porta TCP 80.


    Get-AzureNetworkSecurityGroup -Name "RestrictWestUSAppAccess" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP Barracuda" -Type Inbound -Priority 201 -Action Allow -SourceAddressPrefix '191.0.0.1'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP

Substitua o SourceAddressPrefix o endereço Virtual IP (VIP) do serviço em nuvem seu WAF.

> Nota: O VIP do serviço na nuvem irá alterar ao eliminar e voltar a criar o serviço de nuvem. Certifique-se atualizar o endereço IP, no grupo de recursos de rede, uma vez, pode fazê-lo. 
 
<!-- IMAGES -->
[Architecture]: ./media/app-service-app-service-environment-web-application-firewall/Architecture.png
[ConfigureEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureEndpoint.png
[AddManagementEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/AddManagementEndpoint.png
[ManagementAddServices]: ./media/app-service-app-service-environment-web-application-firewall/ManagementAddServices.png
[ManagementDashboard]: ./media/app-service-app-service-environment-web-application-firewall/ManagementDashboard.png
[ManagementLoginPage]: ./media/app-service-app-service-environment-web-application-firewall/ManagementLoginPage.png
[TrafficManagerEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/TrafficManagerEndpoint.png
[ConfigureTrafficManager]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureTrafficManager.png
[WebsiteTranslations]: ./media/app-service-app-service-environment-web-application-firewall/WebsiteTranslations.png
