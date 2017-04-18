<properties
   pageTitle="Gestão de contentor de serviço de contentor Azure através da API REST | Microsoft Azure"
   description="Implemente contentores a um cluster de Azure contentor serviço Mesos utilizando o Marathon REST API."
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

# <a name="container-management-through-the-rest-api"></a>Gestão de contentor através da API REST

Cc/SO fornece um ambiente para implementar e dimensionamento das cargas de trabalho agrupadas, enquanto abstracting o hardware subjacente. Na parte superior de CC sistema operativo, existe um quadro que gere o agendamento e cluster cargas de trabalho em execução.

Apesar de quadros estão disponíveis para muitas das cargas de trabalho mais utilizadas, este documento descreve como pode criar e dimensionar implementações contentor utilizando Marathon. Antes de trabalhar através destes exemplos, precisa de um cluster de CC/SO está configurado no Azure contentor de serviço. Também tem de ter conectividade remota a este cluster. Para mais informações sobre estes itens, consulte os artigos seguintes:

- [Implementar um cluster de serviço de contentor do Azure](container-service-deployment.md)
- [Ligar a um cluster de serviço de contentor do Azure](container-service-connect.md)

Depois de estar ligado ao cluster de serviço de contentor Azure, pode aceder a CC/SO e relacionados REST APIs através de porta http://localhost:local. Os exemplos neste documento partem do pressuposto de que são túnel na porta 80. Por exemplo, o ponto final Marathon esteja contactável `http://localhost/marathon/v2/`. Para mais informações sobre as várias APIs, consulte a documentação mesosfera [Marathon API](https://mesosphere.github.io/marathon/docs/rest-api.html) e [Chronos API](https://mesos.github.io/chronos/docs/api.html)e a documentação Apache [Mesos Scheduler API](http://mesos.apache.org/documentation/latest/scheduler-http-api/).

## <a name="gather-information-from-dcos-and-marathon"></a>Reunir informações a partir do Centro de dados/SO e Marathon

Antes de implementar contentores ao cluster CC/SO, reunir algumas informações sobre o cluster CC/SO, tais como os nomes e o estado atual de agentes CC/SO. Para fazê-lo, consultar o `master/slaves` ponto final da API do resto do Centro de dados/SO. Se tudo o que vai bem, irá ver uma lista de agentes do Centro de dados/SO e várias propriedades para cada um.

```bash
curl http://localhost/mesos/master/slaves
```

Agora, utilize o Marathon `/apps` ponto final para verificar a existência atual implementações de aplicações para o cluster CC/SO. Se este for um novo cluster, verá uma matriz vazia para as aplicações.

```
curl localhost/marathon/v2/apps

{"apps":[]}
```

## <a name="deploy-a-docker-formatted-container"></a>Implementar um contentor formatados como Docker

Implementar formatados como Docker contentores através de Marathon utilizando um ficheiro JSON que descreva a implementação pretendida. O exemplo seguinte irá implementar o contentor Nginx, porta encadernação 80 do agente CC/SO à porta 80 do contentor. Tenha em atenção que a propriedade de 'acceptedResourceRoles' é definida como 'slave_public'. Isto irá implementar o contentor para um agente do conjunto de escala agente para o público.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
    "acceptedResourceRoles": [
    "slave_public"
  ],
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Para implementar um contentor formatados como Docker, crie o seu próprio ficheiro JSON ou utilize o exemplo fornecido na [demonstração do serviço de contentor Azure](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json). Guardá-lo numa localização acessível. Em seguida, para implementar o contentor, execute o seguinte comando. Especifique o nome do ficheiro JSON.

```
curl -X POST http://localhost/marathon/v2/apps -d @marathon.json -H "Content-type: application/json"
```

O resultado será semelhante ao seguinte:

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

Agora, se consultar Marathon para as aplicações, esta nova aplicação irá mostrar no resultado.

```
curl localhost/marathon/v2/apps
```

## <a name="scale-your-containers"></a>Dimensionar os contentores

Também pode utilizar a API Marathon para dimensionar saída ou dimensionar nas implementações da aplicação. No exemplo anterior, implementado numa instância de uma aplicação. Vamos Dimensionar este out para três instâncias de uma aplicação. Para fazê-lo, criar um ficheiro JSON utilizando o seguinte texto JSON e armazená-lo numa localização acessível.

```json
{ "instances": 3 }
```

Execute o seguinte comando para dimensionar fora da aplicação.

>[AZURE.NOTE] O URI será http://localhost/marathon/v2/apps/ e, em seguida, o ID da aplicação para dimensionar. Se estiver a utilizar o exemplo Nginx fornecido aqui, o URI seria http://localhost/marathon/v2/apps/nginx.

```json
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Por fim, consulta o ponto final Marathon para aplicações. Verá que existem agora três dos contentores Nginx.

```
curl localhost/marathon/v2/apps
```

## <a name="use-powershell-for-this-exercise-marathon-rest-api-interaction-with-powershell"></a>Utilizar o PowerShell para este exercício: interação Marathon REST API com PowerShell

Pode efetuar estas ações mesmo através dos comandos do PowerShell num sistema Windows.

Para recolher informações sobre o Centro de dados/SO cluster, como nomes de agente e estado de agente, execute o seguinte comando.

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

Implementar formatados como Docker contentores através de Marathon utilizando um ficheiro JSON que descreva a implementação pretendida. O exemplo seguinte irá implementar o contentor Nginx, porta encadernação 80 do agente CC/SO à porta 80 do contentor.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Criar o seu próprio ficheiro JSON ou utilize o exemplo fornecido na [demonstração do serviço de contentor Azure](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json). Guardá-lo numa localização acessível. Em seguida, para implementar o contentor, execute o seguinte comando. Especifique o nome do ficheiro JSON.

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

Também pode utilizar a API Marathon para dimensionar saída ou dimensionar nas implementações da aplicação. No exemplo anterior, implementado numa instância de uma aplicação. Vamos Dimensionar este registo de saída para três instâncias de uma aplicação. Para fazê-lo, criar um ficheiro JSON utilizando o seguinte texto JSON e armazená-lo numa localização acessível.

```json
{ "instances": 3 }
```

Execute o seguinte comando para dimensionar fora da aplicação.

> [AZURE.NOTE] O URI será http://localhost/marathon/v2/apps/ e, em seguida, o ID da aplicação para dimensionar. Se estiver a utilizar o exemplo Nginx fornecido aqui, o URI seria http://localhost/marathon/v2/apps/nginx.

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## <a name="next-steps"></a>Próximos passos

- [Leia mais sobre os pontos finais Mesos HTTP]( http://mesos.apache.org/documentation/latest/endpoints/).
- [Leia mais sobre a API do resto Marathon]( https://mesosphere.github.io/marathon/docs/rest-api.html).
