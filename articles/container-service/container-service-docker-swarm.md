<properties
   pageTitle="Gestão de contentor de serviço de contentor Azure com Docker Swarm | Microsoft Azure"
   description="Implementar contentores para um Swarm Docker num serviço de contentor do Azure"
   services="container-service"
   documentationCenter=""
   authors="neilpeterson"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, contentores, Micro-services, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/13/2016"
   ms.author="timlt"/>

# <a name="container-management-with-docker-swarm"></a>Gestão de contentor com Docker Swarm

Docker enxame fornece um ambiente para implementar das cargas de trabalho em contentores através de um conjunto de anfitriões Docker agrupado. Docker enxame utiliza a API Docker nativa. Fluxo de trabalho para gerir contentores num Docker Swarm é quase idêntico ao que seria num sistema anfitrião único contentor. Este artigo fornece exemplos simples de implementação das cargas de trabalho em contentores uma instância de serviço de contentor Azure do Docker Swarm. Para obter mais aprofundada no Docker Swarm, consulte o artigo [Docker Swarm no Docker.com](https://docs.docker.com/swarm/).

Pré-requisitos para os exercícios neste documento:

[Criar um cluster de enxame num serviço de contentor do Azure](container-service-deployment.md)

[Ligar-se com o cluster enxame num serviço de contentor do Azure](container-service-connect.md)

## <a name="deploy-a-new-container"></a>Implementar um novo contentor

Para criar um novo contentor no Swarm a Docker, utilize o `docker run` comando (garantindo que abriu um túnel SSH para os modelos globais de acordo com a pré-requisitos acima). Este exemplo cria um contentor a partir do `yeasy/simple-web` imagem:


```bash
user@ubuntu:~$ docker run -d -p 80:80 yeasy/simple-web

4298d397b9ab6f37e2d1978ef3c8c1537c938e98a8bf096ff00def2eab04bf72
```

Depois de ter sido criado o contentor, utilizar `docker ps` para devolver informações sobre o contentor. Repare que o agente de enxame que está a alojar o contentor está listado:


```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   31 seconds ago      Up 9 seconds        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

Agora pode aceder a aplicação que está a ser executado neste contentor através do nome DNS público do Balanceador de carga agente enxame. Pode encontrar estas informações no portal do Azure:  


![Visite real resultados](media/real-visit.jpg)  

Por predefinição, o Balanceador de carga tem portas 80, 443 e 8080 abrir. Se pretender ligar à porta outra terá de abrir essa porta no balanceador de carga Azure para o conjunto de agente.

## <a name="deploy-multiple-containers"></a>Implementar vários contentores

Como vários contentores são iniciados, através da execução 'docker executar' várias vezes, pode utilizar o `docker ps` estiver a executar o comando para ver que aloja os contentores. No exemplo abaixo, três contentores são distribuídas uniformemente ao longo dos três agentes de enxame:  


```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
11be062ff602        yeasy/simple-web    "/bin/sh -c 'python i"   11 seconds ago      Up 10 seconds       10.0.0.6:83->80/tcp   swarm-agent-34A73819-2/clever_banach
1ff421554c50        yeasy/simple-web    "/bin/sh -c 'python i"   49 seconds ago      Up 48 seconds       10.0.0.4:82->80/tcp   swarm-agent-34A73819-0/stupefied_ride
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   2 minutes ago       Up 2 minutes        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

## <a name="deploy-containers-by-using-docker-compose"></a>Implementar contentores utilizando Docker compor

Pode utilizar Docker compor para automatizar a implementação e configuração do contentores múltiplos. Para fazê-lo, certifique-se de que foi criado um túnel Shell seguro (SSH) e que tenha sido definida a variável DOCKER_HOST (consulte as pré-requisitos acima).

Crie um ficheiro de docker compose.yml no seu sistema local. Para executar esta tarefa, utilize este [exemplo](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/docker-compose.yml).

```bash
web:
  image: adtd/web:0.1
  ports:
    - "80:80"
  links:
    - rest:rest-demo-azure.marathon.mesos
rest:
  image: adtd/rest:0.1
  ports:
    - "8080:8080"

```

Executar `docker-compose up -d` para iniciar as implementações contentor:


```bash
user@ubuntu:~/compose$ docker-compose up -d
Pulling rest (adtd/rest:0.1)...
swarm-agent-3B7093B8-0: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-3: Pulling adtd/rest:0.1... : downloaded
Creating compose_rest_1
Pulling web (adtd/web:0.1)...
swarm-agent-3B7093B8-3: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-0: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/web:0.1... : downloaded
Creating compose_web_1
```

Por fim, a lista de executar o contentores será devolvida. Esta lista reflete os contentores que foram implementados utilizando Docker compor:


```bash
user@ubuntu:~/compose$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                     NAMES
caf185d221b7        adtd/web:0.1        "apache2-foreground"   2 minutes ago       Up About a minute   10.0.0.4:80->80/tcp       swarm-agent-3B7093B8-0/compose_web_1
040efc0ea937        adtd/rest:0.1       "catalina.sh run"      3 minutes ago       Up 2 minutes        10.0.0.4:8080->8080/tcp   swarm-agent-3B7093B8-0/compose_rest_1
```

Naturalmente, pode utilizar `docker-compose ps` examinar apenas os contentores definidos no seu `compose.yml` ficheiro.

## <a name="next-steps"></a>Próximos passos

[Saiba mais sobre Docker Swarm](https://docs.docker.com/swarm/)
