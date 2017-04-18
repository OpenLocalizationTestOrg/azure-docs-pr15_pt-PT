<properties
   pageTitle="Ligar a um cluster de serviço de contentor Azure | Microsoft Azure"
   description="Ligar a um cluster de serviço de contentor Azure utilizando um túnel SSH."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, contentores, Micro-services, Cc/SO, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/13/2016"
   ms.author="rogardle"/>


# <a name="connect-to-an-azure-container-service-cluster"></a>Ligar a um cluster de serviço de contentor do Azure

O Centro de dados/SO e clusters Docker Swarm que são implementados pelo serviço de contentor Azure expõem os pontos finais de descanso. No entanto, estes pontos finais não estão a abrir para o mundo exterior. Para gerir estes pontos finais, terá de criar um túnel Shell seguro (SSH). Depois de um SSH túnel foi estabelecida, pode executar comandos contra os pontos finais cluster e ver o cluster IU através de um browser no seu próprio sistema. Este documento orienta-criar um túnel SSH de Linux, OS X e Windows.

>[AZURE.NOTE] Pode criar uma sessão SSH com um sistema de gestão de cluster. No entanto, não recomendamos esta. Estamos a tratar diretamente um sistema de gestão expõe o risco para as alterações à configuração inadvertidas.   

## <a name="create-an-ssh-tunnel-on-linux-or-os-x"></a>Criar um túnel SSH Linux ou OS X

A primeira coisa que fazer quando cria um túnel SSH no Linux ou OS X é localizar o nome de modelos globais de balanceamento de carga DNS público. Para fazer isto, expanda o grupo de recursos para que cada recurso está a ser apresentado. Localize e selecione o endereço IP público da forma mestre. Isto vai abrir para cima uma pá que contenha informações sobre o endereço IP público, que inclui o nome de DNS. Guarde este nome para utilizar posteriormente. <br />


![Nome de DNS público](media/pubdns.png)

Agora, abra uma shell e execute o seguinte comando onde:

**Porta** é a porta do ponto final que pretende mostrar. Para enxame, este é 2375. Para CC sistema operativo, utilize porta 80.  
**Nome de utilizador** é o nome de utilizador que foi fornecido quando tiver implementado cluster.  
**DNSPREFIX** é o prefixo DNS que forneceu quando tiver implementado cluster.  
**Região** é a região em que se encontra o seu grupo de recursos.  
**PATH_TO_PRIVATE_KEY** [Opcional] é o caminho para a chave privada que corresponde à chave pública que forneceu quando criou o cluster de serviço de contentor. Utilize esta opção com -i sinalizar.

```bash
ssh -L PORT:localhost:PORT -f -N [USERNAME]@[DNSPREFIX]mgmt.[REGION].cloudapp.azure.com -p 2200
```
> A porta de ligação SSH é 2200 – não à porta padrão 22.

## <a name="dcos-tunnel"></a>Túnel CC/SO

Para abrir um túnel para os pontos finais CC/SO-relacionados com, pode execute um comando que é semelhante ao seguinte:

```bash
sudo ssh -L 80:localhost:80 -f -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Agora pode aceder os pontos finais CC/SO-relacionados com a:

- CC SISTEMA OPERATIVO:`http://localhost/`
- Marathon:`http://localhost/marathon`
- Mesos:`http://localhost/mesos`

Da mesma forma, pode chegar o resto APIs para cada aplicação através deste túnel.

## <a name="swarm-tunnel"></a>Enxame túnel

Para abrir um túnel para o ponto final enxame, pode execute um comando com um aspeto semelhante ao seguinte:

```bash
ssh -L 2375:localhost:2375 -f -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Agora pode definir a variável de ambiente de DOCKER_HOST da seguinte forma. Pode continuar a utilizar a interface de comandos Docker (CLI) como habitualmente.

```bash
export DOCKER_HOST=:2375
```

## <a name="create-an-ssh-tunnel-on-windows"></a>Criar um túnel SSH no Windows

Existem várias opções para criar SSH túneis no Windows. Neste documento serão descrevem como utilizar betumes para fazer isto.

Transfira betumes ao seu sistema do Windows e executar a aplicação.

Introduza um nome de anfitrião é composto pelo nome de utilizador de administração do cluster e o nome DNS público da primeira forma mestre no cluster. O **Nome do anfitrião** terá o seguinte aspeto: `adminuser@PublicDNS`. Introduza 2200 para a **porta**.

![Configuração para 1](media/putty1.png)

Selecione **SSH** e **autenticação**. Adicione o seu ficheiro de chave privada para autenticação.

![Configuração para 2](media/putty2.png)

Selecione **túneis** e configurar as seguintes opções reencaminhadas portas:
- **Porta de origem:** Sua preferência – utilização 80 para CC/SO ou 2375 para enxame.
- **Destino:** Utilize localhost:80 para CC/SO ou localhost:2375 para enxame.

O exemplo seguinte está configurado para o Centro de dados/SO, mas terá uma aspeto semelhante para Docker Swarm.

>[AZURE.NOTE] Portas 80 não devem ser utilizado quando cria este túnel.

![Configuração para 3](media/putty3.png)

Quando tiver terminado, guarde a configuração da ligação e ligar a sessão para. Ao ligar, pode ver a configuração da porta no registo de eventos para.

![Para o registo de eventos](media/putty4.png)

Quando tiver configurado o túnel para CC/SO, pode aceder o ponto final relacionado em:

- CC SISTEMA OPERATIVO:`http://localhost/`
- Marathon:`http://localhost/marathon`
- Mesos:`http://localhost/mesos`

Quando tiver configurado o túnel para Docker Swarm, pode aceder ao cluster de enxame através do clip de Docker. Primeiro terá de configurar uma variável de ambiente de Windows com o nome `DOCKER_HOST` com um valor de ` :2375`.

## <a name="next-steps"></a>Próximos passos

Implementar e gerir contentores com CC/SO ou enxame:

- [Trabalhar com o serviço de contentor Azure e Cc/SO](container-service-mesos-marathon-rest.md)
- [Trabalhar com o serviço de contentor Azure e Docker enxame](container-service-docker-swarm.md)
