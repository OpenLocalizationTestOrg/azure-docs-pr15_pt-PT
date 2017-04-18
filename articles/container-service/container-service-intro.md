<properties
   pageTitle="Introdução de serviço do contentor Azure | Microsoft Azure"
   description="O serviço de contentor Azure fornece uma forma para simplificar a criação, configuração e gestão de um cluster de máquinas virtuais que são pré-configuradas automaticamente para executar as aplicações em contentores."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, contentores, Micro-services, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/13/2016"
   ms.author="rogardle"/>

# <a name="azure-container-service-introduction"></a>Introdução de serviço de contentor Azure

O serviço de contentor Azure torna mais simples que pode criar, configurar e gerir um cluster de máquinas virtuais que são pré-configuradas automaticamente para executar as aplicações em contentores. Utiliza uma configuração otimizada das ferramentas populares de agendamento e orchestration de abrir origem. Permite-lhe utilizar os seus conhecimentos existentes ou desenhar relativamente a um grande e em crescimento corpo de especialidade da Comunidade, para implementar e gerir aplicações baseadas em contentor no Microsoft Azure.


![O serviço de contentor Azure fornece um meio para gerir aplicações em contentores em vários anfitriões no Azure.](./media/acs-intro/acs-cluster.png)


O serviço de contentor Azure tira partido do formato de contentor Docker para se certificar de que os contentores de aplicação estão totalmente portátil. Também suporta a escolha de Marathon e Cc/SO ou Docker Swarm para que pode dimensionar estas aplicações para milhares de contentores ou até mesmo dezenas de milhares.

Ao utilizar o serviço de contentor Azure, pode tirar partido das funcionalidades empresarial do Azure, mantendo portabilidade aplicação – incluindo portabilidade nas camadas orchestration.

<a name="using-azure-container-service"></a>Utilizar o serviço de contentor Azure
-----------------------------

Nosso objetivo com o serviço de contentor Azure consiste em fornecer um contentor de ambiente de alojamento utilizando ferramentas de origem de abrir e tecnologias populares entre os nossos clientes hoje. Para o efeito, podemos expor os pontos finais da API padrão para sua orchestrator que selecionou (CC/SO ou Docker Swarm). Ao utilizar estes pontos finais, pode tirar partido qualquer software que seja compatível com a falar para esses pontos finais. Por exemplo, no caso do ponto final Docker Swarm, poderá optar por utilizar a interface de comandos Docker (CLI). Para CC sistema operativo, poderá optar por utilizar o clip de DCOS.

<a name="creating-a-docker-cluster-by-using-azure-container-service"></a>Criar um cluster de Docker utilizando o serviço de contentor Azure
-------------------------------------------------------

Para começar a utilizar o serviço de contentor Azure, implementar um cluster de serviço de contentor Azure através do portal do (Procurar 'Azure contentor serviço'), utilizando um modelo de Gestor de recursos do Azure ([Docker Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm) ou [Cc/SO](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)) ou com o [clip](/documentation/articles/xplat-cli-install/). Os modelos de guia de introdução fornecido podem ser modificados para incluir a configuração do Azure adicional ou avançada. Para mais informações sobre como implementar um cluster de serviço de contentor Azure, consulte o artigo [Implementar um cluster de serviço do Azure contentor](container-service-deployment.md).

<a name="deploying-an-application"></a>Implementar uma aplicação
------------------------

O serviço de contentor Azure fornece uma escolha de Docker Swarm ou em Cc/SO para orchestration. Como implementar a aplicação depende da sua escolha de orchestrator.

### <a name="using-dcos"></a>Utilizar o Centro de dados/SO

Cc/SO é um sistema operativo distribuído baseado no kernel de sistemas distribuídos do Apache Mesos. Apache Mesos é alojado no Apache Software Foundation e listas de alguns da [maiores nomes no IT](http://mesos.apache.org/documentation/latest/powered-by-mesos/) como utilizadores e contribuintes.

![Serviço de contentor Azure configurado para enxame a mostrar agentes e modelos globais.](media/acs-intro/dcos.png)

Cc/SO e Apache Mesos incluir um conjunto de funcionalidades impressionantes:

-   Escalabilidade provas dadas

-   Replicadas tolerância a falhas slaves utilizando Apache ZooKeeper e modelo global

-   Suporte de formatados como Docker membros em contentores

-   Nativo isolamento entre tarefas com Linux contentores

-   Multiresource agendamento (memória, CPU, disco e portas)

-   Java, Python e C++ APIs para desenvolver aplicações paralelas novas

-   Um IU da web para ver o estado de cluster

Por predefinição, a executar o serviço de contentor Azure CC/SO inclui a plataforma de orchestration Marathon para agendamento das cargas de trabalho. No entanto, incluído com a implementação de CC/SO de ACS é universo mesosfera dos serviços que podem ser adicionados no seu serviço, estes incluem motores, Hadoop, Cassandra e muito mais.

![Cc/SO universo num serviço de contentor Azure](media/dcos/universe.png)

#### <a name="using-marathon"></a>Utilizar Marathon

Marathon é um cluster wide inicialização e o sistema de controlo para os serviços no cgroups – ou, no caso do serviço de contentor Azure, contentores formatados como Docker. Marathon fornece uma IU da web a partir do qual pode implementar suas aplicações. Pode aceder-lhe um URL que tem um aspeto semelhante `http://DNS_PREFIX.REGION.cloudapp.azure.com` onde DNS\_PREFIXO e região são ambos definida no momento da implementação. Obviamente, também pode fornecer o seu nome de DNS. Para obter mais informações sobre como executar um contentor através da web Marathon IU, consulte [Gestão de contentor através da IU da web](container-service-mesos-marathon-ui.md).

![Lista de aplicações Marathon](media/dcos/marathon-applications-list.png)

Também pode utilizar a API REST para comunicar com Marathon. Existem várias bibliotecas do cliente que estão disponíveis para cada ferramenta. Abrangem uma variedade de idiomas – e, claro, pode utilizar o protocolo HTTP em qualquer idioma. Além disso, muitas ferramentas DevOps populares fornecem suporte para Marathon. Isto dá flexibilidade máxima para a sua equipa de operações quando está a trabalhar com um cluster de serviço do Azure contentor. Para mais informações sobre como executar um contentor ao utilizar a API do resto Marathon, consulte [Gestão de contentor com a API REST](container-service-mesos-marathon-rest.md).

### <a name="using-docker-swarm"></a>Utilizar Docker enxame

Docker enxame fornece clusters nativo para Docker. Uma vez que Docker Swarm serve a API Docker padrão, qualquer outra ferramenta que já comunica com um daemon Docker pode utilizar enxame para transparente Dimensionar para vários anfitriões no Azure o serviço de contentor.

![Serviço de contentor Azure configurado para utilizar o Centro de dados/SO – de jumpbox, agentes e modelos globais.](media/acs-intro/acs-swarm2.png)

Ferramentas suportadas para gerir os contentores num cluster enxame incluem, mas não estão limitadas ao livro, o seguinte procedimento:

-   Dokku

-   Compõem docker clip e Docker

-   Krane

-   Jenkins

<a name="videos"></a>Vídeos
------

Introdução ao Azure contentor Service (101):  

> [AZURE.VIDEO azure-container-service-101]

Criar aplicações com o serviço de contentor Azure (compilação 2016)

> [AZURE.VIDEO build-2016-building-applications-using-the-azure-container-service]
