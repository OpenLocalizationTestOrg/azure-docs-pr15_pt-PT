<properties
    pageTitle="Criar anfitriões Docker no Azure com Docker máquina | Microsoft Azure"
    description="Descreve a utilização da máquina Docker para criar anfitriões docker no Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="squillace"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="07/22/2016"
    ms.author="rasquill"/>

# <a name="use-docker-machine-with-the-azure-driver"></a>Utilizar o Docker Machine com o controlador do Azure

[Docker](https://www.docker.com/) é uma das abordagens virtualização mais populares que utiliza Linux contentores em vez de máquinas virtuais como uma forma de isolar os dados da aplicação e informática em recursos partilhados. Este tópico descreve quando e como utilizar o [Docker Machine](https://docs.docker.com/machine/) (o `docker-machine` comando) para criar novo Linux VMs no Azure activado como um anfitrião docker para os contentores Linux.


## <a name="create-vms-with-docker-machine"></a>Criar VMs com Docker máquina

Criar docker anfitrião VMs no Azure com o `docker-machine create` comando utilizando o `azure` argumento de controlador para a opção controlador (`-d`) e quaisquer outros argumentos. 

O exemplo seguinte depende dos valores predefinidos, mas, aberto na VM à internet para teste com um contentor nginx, torna a porta 80 `ops` o utilizador de início de sessão para SSH e chamadas para a nova VM `machine`. 

Tipo de `docker-machine create --driver azure` para ver as opções e os respetivos valores predefinidos; Também pode ler a [documentação do controlador de Azure Docker](https://docs.docker.com/machine/drivers/azure/). (Tenha em atenção que se tiver de autenticação de dois fatores ativada, irá ser-lhe para autenticar utilizando o segundo factor.)

```bash
docker-machine create -d azure \
  --azure-ssh-user ops \
  --azure-subscription-id <Your AZURE_SUBSCRIPTION_ID> \
  --azure-open-port 80 \
  machine
```

O resultado deve ter um aspeto semelhante a esta, consoante se tiver configurada na sua conta de autenticação de dois fatores.

```
Creating CA: /Users/user/.docker/machine/certs/ca.pem
Creating client certificate: /Users/user/.docker/machine/certs/cert.pem
Running pre-create checks...
(machine) Microsoft Azure: To sign in, use a web browser to open the page https://aka.ms/devicelogin. Enter the code <code> to authenticate.
(machine) Completed machine pre-create checks.
Creating machine...
(machine) Querying existing resource group.  name="machine"
(machine) Creating resource group.  name="machine" location="eastus"
(machine) Configuring availability set.  name="docker-machine"
(machine) Configuring network security group.  name="machine-firewall" location="eastus"
(machine) Querying if virtual network already exists.  name="docker-machine-vnet" location="eastus"
(machine) Configuring subnet.  name="docker-machine" vnet="docker-machine-vnet" cidr="192.168.0.0/16"
(machine) Creating public IP address.  name="machine-ip" static=false
(machine) Creating network interface.  name="machine-nic"
(machine) Creating storage account.  name="vhdsolksdjalkjlmgyg6" location="eastus"
(machine) Creating virtual machine.  name="machine" location="eastus" size="Standard_A2" username="ops" osImage="canonical:UbuntuServer:15.10:latest"
Waiting for machine to be running, this may take a few minutes...
Detecting operating system of created instance...
Waiting for SSH to be available...
Detecting the provisioner...
Provisioning with ubuntu(systemd)...
Installing Docker...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...
Checking connection to Docker...
Docker is up and running!
To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: docker-machine env machine
```

## <a name="configure-your-docker-shell"></a>Configurar a shell de docker

Agora, escreva `docker-machine env <VM name>` para ver o que precisa de fazer para configurar a shell de. 

```bash
docker-machine env machine
```

Imprime que as informações de ambiente, que tenha este aspeto. Tenha em atenção que o endereço IP foi atribuído, que vai precisar de testar a VM.

```
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://191.237.46.90:2376"
export DOCKER_CERT_PATH="/Users/rasquill/.docker/machine/machines/machine"
export DOCKER_MACHINE_NAME="machine"
# Run this command to configure your shell:
# eval $(docker-machine env machine)
```

Pode executar o comando de sugestão de configuração, ou pode configurar as variáveis de ambiente sozinho. 

## <a name="run-a-container"></a>Executar um contentor

Agora pode executar um servidor web simples para testar se todas as está a funcionar corretamente. Aqui vamos utilizar uma imagem padrão nginx, especifique o que deverá escutar portas 80 e que, se a VM reinicia o contentor deverá reiniciar também (`--restart=always`). 

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

E verifique o contentor em execução, escreva `docker-machine ip <VM name>` para localizar o endereço IP (caso se tenha esquecido da partir do `env` comando):

![Contentor de ngnix em execução](./media/virtual-machines-linux-docker-machine/nginxsuccess.png)

## <a name="next-steps"></a>Próximos passos

Se está interessado, pode experimentar o Azure [Docker VM extensão](virtual-machines-linux-dockerextension.md) para efetuar a mesma operação utilizando o clip do Azure ou os modelos de Gestor de recursos Azure. 

Para obter mais exemplos de trabalhar com Docker, consulte o artigo [trabalhar com Docker](https://github.com/Microsoft/HealthClinic.biz/wiki/Working-with-Docker) a partir de ligar a [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 [demonstração](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/). Para obter mais tutoriais a partir de demonstração HealthClinic.biz, consulte o artigo [Tutoriais de ferramentas de programador do Azure](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).

