<properties
   pageTitle="Monitorizar um cluster de serviço de contentor Azure com Datadog | Microsoft Azure"
   description="Monitorize um cluster de serviço de contentor Azure com Datadog. Utilize web CC/SO da IU para implementar os agentes Datadog ao seu cluster."
   services="container-service"
   documentationCenter=""
   authors="rbitia"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Contentores, Cc/sistema operativo, Docker enxame, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure"   
   ms.date="07/28/2016"
   ms.author="t-ribhat"/>

# <a name="monitor-an-azure-container-service-cluster-with-datadog"></a>Monitorizar um cluster de serviço de contentor Azure com Datadog

Neste artigo podemos irá implementar Datadog agentes para todos os nós de agente no seu cluster de serviço do Azure contentor. Terá uma conta com Datadog para esta configuração. 

## <a name="prerequisites"></a>Pré-requisitos 

[Implementar](container-service-deployment.md) e [Ligar](container-service-connect.md) um cluster configurado pelo serviço de contentor Azure. Explorar o [Marathon IU](container-service-mesos-marathon-ui.md). Aceda a [http://datadoghq.com](http://datadoghq.com) para configurar uma conta de Datadog. 

## <a name="datadog"></a>Datadog 

Datadog é um serviço de monitorização que reúne monitorização de dados a partir do seu contentores dentro do seu cluster de serviço do Azure contentor. Datadog tem um Dashboard de integração de Docker onde pode ver métricas específicas dentro os contentores. Métricas reunidas do seu contentores estão organizadas por CPU, memória e de rede e/s. Datadog divide métricas em contentores e imagens. Um exemplo de o aspeto de IU de utilização da CPU está abaixo.

![IU Datadog](./media/container-service-monitoring/datadog4.png)

## <a name="configure-a-datadog-deployment-with-marathon"></a>Configurar uma implementação Datadog com Marathon

Estes passos irão mostrar-lhe como configurar e implementar Datadog aplicações para o seu cluster com Marathon. 

Aceder ao seu IU CC/SO através do [http://localhost:80 /](http://localhost:80/). Uma vez na IU do Centro de dados/SO navegue para o "universo" que se encontra no canto inferior esquerdo e, em seguida, procure "Datadog" e clique em "Instalar".

![Pacote de Datadog dentro do Businessobjects CC/SO](./media/container-service-monitoring/datadog1.png)

Agora para concluir a configuração terá uma conta de Datadog ou uma conta de avaliação gratuita. Assim que estiver com sessão iniciada aspeto do Web site Datadog para a esquerda e aceda a integrações ->, em seguida, de API. 

![Chave Datadog API](./media/container-service-monitoring/datadog2.png)

Em seguida, introduza a chave de API para a configuração de Datadog dentro do Businessobjects CC/SO. 

![Configuração de Datadog do universo de CC/SO](./media/container-service-monitoring/datadog3.png) 

Na configuração acima instâncias estão definidas para 10000000-lo sempre que um novo nó é adicionado ao cluster Datadog automaticamente irá implementar um agente para esse nó. Esta é uma solução provisória. Assim que tiver instalado o pacote deve navegar de volta para o Web site Datadog e localizar "Dashboards." A partir desse local verá personalizadas e integração de Dashboards. Dashboard de integração de Docker terá todas as métricas de contentor que necessita para o seu cluster de monitorização. 
