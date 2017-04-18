<properties
   pageTitle="Criar anfitriões Docker no Azure com Docker máquina | Microsoft Azure"
   description="Descreve a utilização da máquina Docker para criar anfitriões docker no Azure."
   services="azure-container-service"
   documentationCenter="na"
   authors="mlearned"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="06/08/2016"
   ms.author="mlearned" />

# <a name="create-docker-hosts-in-azure-with-docker-machine"></a>Criar Docker anfitriões no Azure com máquina de Docker

Execução [Docker](https://www.docker.com/) contentores requer um anfitrião VM a executar o daemon docker.
Este tópico descreve como utilizar o comando de [máquina docker](https://docs.docker.com/machine/) para criar novo VMs Linux, configurados com o daemon Docker, a ser executada em Azure. 

**Nota:** 
- *Este artigo depende da versão de máquina docker 0.7.0 ou superior*
- *Windows contentores serão suportadas através de máquina docker no futuro próximo*

## <a name="create-vms-with-docker-machine"></a>Criar VMs com Docker máquina

Criar docker anfitrião VMs no Azure com o `docker-machine create` comando utilizando o `azure` controlador. 

O controlador Azure terá o seu ID de subscrição. Pode utilizar o [Clip do Azure](xplat-cli-install.md) ou o [Azure Portal](https://portal.azure.com) para obter a sua subscrição do Azure. 

**Utilizar o Portal do Azure**
- Selecione subscrições a partir da página de navegação à esquerda e copiar para o id da subscrição.

**Utilizar o clip Azure**
- Tipo de ```azure account list``` e copie o id da subscrição.

Tipo de `docker-machine create --driver azure` para ver as opções e os respetivos valores predefinidos.
Também pode ver a [documentação do controlador de Azure Docker](https://docs.docker.com/machine/drivers/azure/) para obter mais informações. 

O exemplo seguinte depende dos valores predefinidos, mas opcionalmente, aberto porta 80 no VM para acesso à internet. 

```
docker-machine create -d azure --azure-subscription-id <Your AZURE_SUBSCRIPTION_ID> --azure-open-port 80 mydockerhost
```

## <a name="choose-a-docker-host-with-docker-machine"></a>Escolha um docker anfitrião com máquina de docker
Assim que tiver uma entrada na máquina docker do seu anfitrião, pode definir o anfitrião predefinido quando comandos docker a ser executado.
##<a name="using-powershell"></a>Utilizar o PowerShell

```powershell
docker-machine env MyDockerHost | Invoke-Expression 
```

##<a name="using-bash"></a>Utilizar festa

```bash
eval $(docker-machine env MyDockerHost)
```

Agora, pode executar comandos docker contra o anfitrião especificado

```
docker ps
docker info
```

## <a name="run-a-container"></a>Executar um contentor

Com um anfitrião configurado, pode agora executar um servidor web simples para verificar se o seu sistema anfitrião foi configurado corretamente.
Aqui vamos utilizar uma imagem padrão nginx, especifique o que deverá escutar portas 80 e que, se for reiniciado anfitrião do VM, o contentor irá reiniciar também (`--restart=always`). 

```bash
docker run -d -p 80:80 --restart=always nginx
```

O resultado deverá ter um aspeto semelhante ao seguinte:

```
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
83f52fbfa5f8: Pull complete
fa664caa1402: Pull complete
Digest: sha256:12127e07a75bda1022fbd4ea231f5527a1899aad4679e3940482db3b57383b1d
Status: Downloaded newer image for nginx:latest
25942c35d86fe43c688d0c03ad478f14cc9c16913b0e1c2971cb32eb4d0ab721
```

## <a name="test-the-container"></a>Testar o contentor

Examinar a execução contentores utilizando `docker ps`:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                         NAMES
d5b78f27b335        nginx               "nginx -g 'daemon off"   5 minutes ago       Up 5 minutes        0.0.0.0:80->80/tcp, 443/tcp   goofy_mahavira
```

E verifique o contentor em execução, escreva `docker-machine ip <VM name>` para localizar o endereço IP para introduzir no browser:

```
PS C:\> docker-machine ip MyDockerHost
191.237.46.90
```

![Contentor de ngnix em execução](./media/vs-azure-tools-docker-machine-azure-config/nginxsuccess.png)

##<a name="summary"></a>Resumo
Com máquina de docker facilmente pode aprovisionar o anfitriões docker no Azure para sua validações de anfitrião docker individuais.
Para produção hospedagem de contentores, consulte o artigo o [Serviço de contentor do Azure](http://aka.ms/AzureContainerService)

Para desenvolver aplicações de Core .NET com o Visual Studio, consulte o artigo [Docker ferramentas para o Visual Studio](http://aka.ms/DockerToolsForVS)
