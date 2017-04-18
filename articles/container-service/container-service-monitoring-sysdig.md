<properties
   pageTitle="Monitorizar um cluster de serviço de contentor Azure com Sysdig | Microsoft Azure"
   description="Monitorize um cluster de serviço de contentor Azure com Sysdig."
   services="container-service"
   documentationCenter=""
   authors="rbitia"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Contentores, Cc sistema operativo, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/08/2016"
   ms.author="t-ribhat"/>

# <a name="monitor-an-azure-container-service-cluster-with-sysdig"></a>Monitorizar um cluster de serviço de contentor Azure com Sysdig

Neste artigo, iremos irá implementar Sysdig agentes para todos os nós de agente no seu cluster de serviço do Azure contentor. Precisa de uma conta com Sysdig para esta configuração. 

## <a name="prerequisites"></a>Pré-requisitos 

[Implementar](container-service-deployment.md) e [Ligar](container-service-connect.md) um cluster configurado pelo serviço de contentor Azure. Explorar o [Marathon IU](container-service-mesos-marathon-ui.md). Aceda a [http://app.sysdigcloud.com](http://app.sysdigcloud.com) para configurar uma conta de nuvem Sysdig. 

## <a name="sysdig"></a>Sysdig

Sysdig é um serviço de monitorização que permite-lhe monitorizar os contentores de seu cluster. Sysdig chama-se para o ajudar com a resolução de problemas, mas também tem as básicas métricas de monitorização para CPU, redes, memória e e/s. Sysdig torna mais fácil ver os contentores estão a trabalhar o hardest ou essencialmente a utilizar o máximo de memória e CPU. Esta vista é na secção "Descrição geral", que se encontra atualmente no beta. 

![IU Sysdig](./media/container-service-monitoring-sysdig/sysdig6.png) 

## <a name="configure-a-sysdig-deployment-with-marathon"></a>Configurar uma implementação Sysdig com Marathon

Estes passos irão mostrar-lhe como configurar e implementar Sysdig aplicações para o seu cluster com Marathon. 

Aceder ao seu IU CC/SO através do [http://localhost:80 /](http://localhost:80/) uma vez na IU do Centro de dados/SO navegue para o "universo", que se encontra no canto inferior esquerdo e, em seguida, procure "Sysdig."

![Sysdig no Centro de dados/SO universo](./media/container-service-monitoring-sysdig/sysdig1.png)

Agora para concluir a configuração tem uma conta de nuvem Sysdig ou uma conta de avaliação gratuita. Assim que iniciou sessão Web site Sysdig na nuvem, clique no seu nome de utilizador e, na página deverá ver o seu "tecla de acesso". 

![Chave Sysdig API](./media/container-service-monitoring-sysdig/sysdig2.png) 

Em seguida, introduza a chave de acesso para a configuração de Sysdig dentro do Businessobjects CC/SO. 

![Configuração de Sysdig do universo de CC/SO](./media/container-service-monitoring-sysdig/sysdig3.png)

Conjunto de agora as instâncias para 10000000 por isso, sempre que um novo nó é adicionado ao cluster Sysdig serão automaticamente implementar um agente para esse nó novo. Esta é uma solução provisória para se certificar de que irá implementar Sysdig para todos os novos agentes dentro do cluster. 

![Configuração do Sysdig no Centro de dados/SO universo-instâncias](./media/container-service-monitoring-sysdig/sysdig4.png)

Assim que tiver instalado o pacote navegue para a IU Sysdig e poderá explorar as métricas de utilização de diferentes para os contentores seu cluster. 