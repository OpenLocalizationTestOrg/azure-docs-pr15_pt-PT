<properties
   pageTitle="Carregar o saldo contentores num cluster o serviço de contentor Azure | Microsoft Azure"
   description="Balanceamento de carga através de vários contentores num cluster Azure o serviço de contentor."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Contentores, Micro-services, Cc/sistema operativo, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/11/2016"
   ms.author="rogardle"/>

# <a name="load-balance-containers-in-an-azure-container-service-cluster"></a>Carregar o saldo contentores num cluster o serviço de contentor Azure

Neste artigo, vamos explorar como criar um balanceador de carga interno num um centro de dados/SO gerido Marathon LB a utilizar o serviço de contentor Azure. Isto permite-lhe dimensionar as suas aplicações horizontalmente. -Lo também permite-lhe tirar partido do agente público e privado clusters ao colocar o seu balanceadores de carga no cluster público e os contentores de aplicação no cluster privado.

## <a name="prerequisites"></a>Pré-requisitos

[Implementar uma instância do Azure contentor serviço](container-service-deployment.md) com o tipo de orchestrator CC/SO e [Certifique-se de que o cliente pode ligar ao seu cluster](container-service-connect.md). 

## <a name="load-balancing"></a>Balanceamento de carga

Existem duas camadas de balanceamento de carga no cluster o serviço de contentor que podemos irá criar: 

  1. Azure Balanceador de carga fornece pontos de entrada pública (aqueles que os utilizadores finais irá visitas). Esta é fornecida automaticamente pelo serviço de contentor Azure e é, por predefinição, configurada para expor portas 80, 443 e 8080.
  2. O Balanceador de carga Marathon (marathon lb) encaminha os pedidos de entrada para instâncias de contentor que essas pedidos de serviço. Como podemos dimensionar os contentores fornecer o nosso serviço web, marathon lb dinamicamente adapta-se. Este Balanceador de carga não for fornecido por predefinição no seu serviço de contentor, mas é muito fácil de instalar.

## <a name="marathon-load-balancer"></a>Balanceador de carga Marathon

Balanceador de carga Marathon dinamicamente reconfiguração com base nos contentores que tenha a implementado. Também é flexível para a perda de um contentor ou um agente - se tal acontecer, Apache Mesos simplesmente irá reiniciar o contentor noutro local e marathon lb irá adaptar.

Para instalar o Balanceador de carga Marathon pode utilizar qualquer um dos CC/SO web IU ou da linha de comandos.

### <a name="install-marathon-lb-using-dcos-web-ui"></a>Instalar Marathon LB utilizando o Centro de dados/SO Web IU

  1. Clique em 'Universo'
  2. Procurar 'Marathon LB'
  3. Clique em «Instalar»

![Instalar o marathon-lb através da Interface de Web do Centro de dados/SO](./media/dcos/marathon-lb-install.png)

### <a name="install-marathon-lb-using-the-dcos-cli"></a>Instalar Marathon-LB utilizando o clip de CC/SO

Depois de instalar o clip de CC/SO e garantindo que pode ligar ao seu cluster, execute o seguinte comando a partir do computador cliente:

```bash
dcos package install marathon-lb
```

Este comando instala automaticamente o Balanceador de carga no cluster agentes públicos.

## <a name="deploy-a-load-balanced-web-application"></a>Implementar uma carga de balanceamento de aplicação Web

Agora que temos o pacote de marathon lb, podemos pode implementar um contentor de aplicação que gostaríamos de balanceamento de carga. Neste exemplo estamos irá implementar um servidor web simples utilizando a seguinte configuração:

```json
{
  "id": "web",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "yeasy/simple-web",
      "network": "BRIDGE",
      "portMappings": [
        { "hostPort": 0, "containerPort": 80, "servicePort": 10000 }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "labels":{
    "HAPROXY_GROUP":"external",
    "HAPROXY_0_VHOST":"YOUR FQDN",
    "HAPROXY_0_MODE":"http"
  }
}

```

  * Definir o valor de `HAProxy_0_VHOST` para o FQDN do Balanceador de carga para sua agentes. Este é o formulário `<acsName>agents.<region>.cloudapp.azure.com`. Por exemplo, se criar um cluster de serviço de contentor com nome `myacs` na região `West US`, o FQDN seria `myacsagents.westus.cloudapp.azure.com`. Também pode encontrar esta procurando pelo balanceador de carga com "agente" no nome do quando está à procura através dos recursos no grupo de recursos que criou para o serviço de contentor no [portal do Azure](https://portal.azure.com).
  * Definir o servicePort a uma porta > = 10.000. Identifica o serviço que está a ser executado neste contentor – marathon lb utiliza esta para identificar os serviços deve equilibrar entre.
  * Definir o `HAPROXY_GROUP` etiqueta para "externos".
  * Definir `hostPort` 0. Isto significa que Marathon arbitrariamente atribuirá uma porta disponível.
  * Definir `instances` para o número de instâncias que pretende criar. Pode sempre Dimensionar estes cima e para baixo mais tarde.

Vale noing que por predefinição que marathon irá implementar ao cluster privado, isto significa que a implementação acima apenas será acessível através do seu Balanceador de carga, que é normalmente o comportamento que podemos pretendem.

### <a name="deploy-using-the-dcos-web-ui"></a>Implementar utilizando a IU da Web de CC/SO

  1. Visite a página de Marathon em http://localhost/marathon (depois de configurar o seu [SSH túnel](container-service-connect.md) e clique em`Create Appliction`
  2. No `New Application` diálogo clique `JSON Mode` no canto superior direito
  3. Cole a JSON acima no editor
  4. Clique em`Create Appliction`

### <a name="deploy-using-the-dcos-cli"></a>Implementar a utilizar o clip de CC/SO

Para implementar esta aplicação com o clip de CC/SO copie simplesmente o JSON acima para um ficheiro denominado `hello-web.json`e executar:

```bash
dcos marathon app add hello-web.json
```

## <a name="azure-load-balancer"></a>Balanceador de carga Azure

Por predefinição, Balanceador de carga Azure expõe portas 80, 8080 e 443. Se estiver a utilizar um destes três portas (tal como o podemos no exemplo acima), em seguida, não há nada que tem de fazer. Deverá conseguir acertar FQDN do seu Balanceador de carga de agente – e sempre que atualizar, irá clicar em um dos seus servidores de três web um modo round robin. No entanto, se utilizar uma porta diferente, tem de adicionar uma regra de round robin e uma sonda no balanceador de carga da porta que utilizou. Pode fazê-lo a partir do [Azure clip](../xplat-cli-azure-resource-manager.md), com os comandos `azure network lb rule create` e `azure network lb probe create`. Pode também fazê-lo através do Portal Azure.


## <a name="additional-scenarios"></a>Cenários adicionais

Pode ter um cenário que utilizar domínios diferentes para expor os diferentes serviços. Por exemplo:

mydomain1.com -> Azure LB:80 -> marathon-lb:10001 -> mycontainer1:33292  
mydomain2.com -> Azure LB:80 -> marathon-lb:10002 -> mycontainer2:22321

Para realizar esta, consulte o artigo [anfitriões virtuais](https://mesosphere.com/blog/2015/12/04/dcos-marathon-lb/), que fornece uma maneira de associar domínios para caminhos marathon-lb específico.

Em alternativa, que expor portas diferentes e mapeá-los para o serviço atrás marathon lb correto. Por exemplo:

Azure lb:80 -> marathon-lb:10001 -> mycontainer:233423  
Azure lb:8080 -> marathon-lb:1002 -> mycontainer2:33432


## <a name="next-steps"></a>Próximos passos

Consulte a documentação de CC/SO para ativado mais [marathon lb](https://dcos.io/docs/1.7/usage/service-discovery/marathon-lb/).
