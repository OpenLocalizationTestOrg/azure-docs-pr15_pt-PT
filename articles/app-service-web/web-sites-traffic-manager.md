<properties
    pageTitle="Controlar o tráfego na aplicação Azure web com o Gestor de tráfego Azure"
    description="Este artigo fornece informações de resumo para o Gestor de tráfego Azure que diz respeito ao Azure web apps."
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    writer="cephalin"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/25/2016"
    ms.author="cephalin"/>

# <a name="controlling-azure-web-app-traffic-with-azure-traffic-manager"></a>Controlar o tráfego na aplicação Azure web com o Gestor de tráfego Azure

> [AZURE.NOTE] Este artigo fornece informações de resumo para o Gestor de tráfego do Microsoft Azure que diz respeito ao Azure aplicação de serviço Web Apps. Podem encontrar mais informações sobre Azure tráfego próprio gestor visitando as ligações no final deste artigo.

## <a name="introduction"></a>Introdução
Pode utilizar o Gestor de tráfego de Azure para controlar como os pedidos de clientes web são distribuídos às aplicações web na aplicação de serviço de Azure. Quando pontos finais de aplicação web são adicionados a um perfil do Gestor de tráfego Azure, Gestor de tráfego Azure mantém controlar do estado das suas aplicações web (em execução, parado ou eliminadas), para que-pode decidir qual desses pontos finais deve receber tráfego.

## <a name="load-balancing-methods"></a>Métodos de balanceamento de carga
Gestor de tráfego Azure utiliza três métodos de balanceamento de carga diferentes. Estas são descritas na seguinte lista como pertencem ao Azure web apps.

* **Activação pós-falha**: Se tiver web app clones em diferentes regiões, pode utilizar este método para configurar uma aplicação web para todo o tráfego de cliente web do serviço e configurar outro web app numa região diferente para que o tráfego de serviço, caso a aplicação web primeira torna-se indisponível.

* **Arredondar Robin**: Se tiver web app clones em diferentes regiões, pode utilizar este método para distribuir uniforme tráfego entre as aplicações de web regiões diferentes.

* **Desempenho**: método de desempenho de distribui o tráfego com base na data e hora mais curta ida e volta a clientes. O método de desempenho pode ser utilizado para aplicações web dentro de uma região mesmo ou em diferentes regiões.

##<a name="web-apps-and-traffic-manager-profiles"></a>Web Apps e perfis do Gestor de tráfego
Para configurar o controlo de tráfego da aplicação web, crie um perfil no Azure tráfego Gestor de que utiliza um dos três carrega métodos balanceamento descritos anteriormente e, em seguida, adicione os pontos finais (neste caso, aplicações web) para o qual pretende controlar o tráfego para o perfil. O estado de aplicação web (em execução, parado ou eliminado) é regularmente comunicado ao perfil para que o Gestor de tráfego Azure pode direcionar tráfego em conformidade.

Quando utilizar o Gestor de tráfego Azure com Azure, tenha em atenção os seguintes pontos:

* Para web apenas implementações da aplicação dentro da mesma região, Web Apps fornece já activação e funcionalidade round robin consideração modo de aplicação web.

* Para implementações na mesma região que utilizam Web Apps em conjunto com outro serviço na nuvem Azure, pode combinar os dois tipos de pontos finais para permitir híbrido cenários.

* Só pode especificar um extremo de aplicação web por região de um perfil. Quando seleciona uma aplicação web como um ponto final para uma região, as restantes aplicações de web nesse região tornam-se indisponível para seleção para esse perfil.

* Os pontos finais da aplicação web que especificou num perfil do Gestor de tráfego Azure aparecerão na secção de **Nomes de domínio** na página configurar para a aplicação web no perfil, mas não serão configuráveis aí.

* Depois de adicionar uma aplicação web a um perfil, o **URL do Site** no Dashboard da página de portal do web app irá apresentar o URL de domínio personalizado do web app se configurou um. Caso contrário, apresentará o URL de perfil do Gestor de tráfego (por exemplo, `contoso.trafficmgr.com`). Tanto o direta nome de domínio do web app e o URL do Gestor de tráfego estarão visível na página de configurar a aplicação web na secção de **Nomes de domínio** .

* Os nomes de domínio personalizado irão funcionar como esperado, mas para além de adicioná-los às suas aplicações web, também tem de configurar o mapa DNS para apontar para o URL do Gestor de tráfego. Para obter informações sobre como configurar um domínio personalizado para uma aplicação Azure web, consulte o artigo [configurar um nome de domínio personalizado para um web site do Azure](web-sites-custom-domain-name.md).

* Só pode adicionar as aplicações de web que estão no modo padrão para um perfil do Gestor de tráfego Azure.

## <a name="next-steps"></a>Próximos passos

Para obter uma conceptual e técnica descrição geral do Gestor de tráfego do Azure, consulte o artigo [Descrição geral do Gestor de tráfego](../traffic-manager/traffic-manager-overview.md).

Para obter mais informações sobre como utilizar o Gestor de tráfego Web Apps, consulte que o blogue novas publicações [Utilizando o Gestor de tráfego Azure com o Azure Web Sites](http://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx) e [Azure tráfego gestor agora pode integrar com o Azure Web Sites](https://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/).
