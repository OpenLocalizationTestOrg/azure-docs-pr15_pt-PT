<properties
   pageTitle="Utilizar a extensão do Azure Docker VM | Microsoft Azure"
   description="Saiba como utilizar a extensão Docker VM para rapidamente e segura implementar um ambiente do Docker no Azure utilizar modelos de Gestor de recursos."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="10/25/2016"
   ms.author="iainfou"/>

# <a name="create-a-docker-environment-in-azure-using-the-docker-vm-extension"></a>Criar um ambiente do Docker no Azure utilizando a extensão de Docker VM
Docker é uma plataforma imagens que lhe permite trabalhar rapidamente com contentores Linux (e também Windows) e gestão de contentor populares. No Azure, existem várias formas, pode implementar Docker de acordo com as suas necessidades. Este artigo foca-se em utilizando a extensão Docker VM e modelos de Gestor de recursos do Azure. 

Para mais informações sobre os diferentes métodos de implementação, incluindo a utilização de máquina de Docker e dos serviços do contentor Azure, consulte os artigos seguintes:

- Rapidamente protótipo uma aplicação, pode criar um único anfitrião Docker utilizar [Docker máquina](./virtual-machines-linux-docker-machine.md).
- Para maiores e mais estáveis ambientes, pode utilizar a extensão do Azure Docker VM, também suporta a [Compor Docker](https://docs.docker.com/compose/overview/) para gerar implementações contentor consistentes. Este detalhes de artigo utilizando a extensão do Azure Docker VM.
- Para criar ambientes pronto para produção, dimensionáveis que fornecem ferramentas adicionais de agendamento e gestão, pode implementar um [Docker Swarm cluster no Azure contentor Services](../container-service/container-service-deployment.md).


## <a name="azure-docker-vm-extension-overview"></a>Descrição geral de extensão do Azure Docker VM
A extensão do Azure Docker VM instala e configura o Docker daemon, Docker cliente e Docker compor no seu Linux máquina virtual (VM). Ao utilizar a extensão do Azure Docker VM, ter mais controlo e funcionalidades que simplesmente utilizar Docker máquina ou criar o anfitrião Docker si mesmo. Estas funcionalidades adicionais, tal como [Docker compor](https://docs.docker.com/compose/overview/), tornam a extensão do Azure Docker VM adequada para mais robusta programador ou ambientes de produção.

Modelos de Gestor de recursos Azure definem toda a estrutura do seu ambiente. Modelos permitem-lhe criar e configurar recursos, tais como o anfitrião do Docker VMs, armazenamento, controlos de acesso baseado em funções RBCA () e diagnóstico. Pode reutilizar estes modelos para criar implementações adicionais de uma forma consistente. Para mais informações sobre o Gestor de recursos do Azure e modelos, consulte o artigo [Descrição geral do Gestor de recursos](../azure-resource-manager/resource-group-overview.md). 


## <a name="deploy-a-template-with-the-azure-docker-vm-extension"></a>Implementar um modelo com a extensão do Azure Docker VM
Vamos utilizar um modelo de guia de introdução existente para criar um VM Ubuntu que utiliza a extensão do Azure Docker VM para instalar e configurar o anfitrião do Docker. Pode ver o modelo aqui: [Simple implementação de uma VM Ubuntu com Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). 

Precisa de [Mais recente Azure clip](../xplat-cli-install.md) instalados e tiver sessão iniciada na utilizando o modo de Gestor de recursos da seguinte forma:

```
azure config mode arm
```

Implemente o modelo utilizando o clip do Azure, especificando o modelo URI. O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` na `WestUS` localização. Utilize o seu próprio nome de grupo de recursos e a localização da seguinte forma:

```
azure group create --name myResourceGroup --location "West US" \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Atender os pedidos para atribuir um nome a sua conta de armazenamento, fornecer um nome de utilizador e palavra-passe e forneça um nome DNS. O resultado é semelhante ao exemplo seguinte:

```
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Updating resource group myResourceGroup
info:    Updated resource group myResourceGroup
info:    Supply values for the following parameters
newStorageAccountName: mystorageaccount
adminUsername: ops
adminPassword: P@ssword!
dnsNameForPublicIP: mypublicip
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK

```

Clip do Azure regressa à linha de comandos depois de apenas alguns segundos, mas o seu anfitrião de Docker é ainda estiver a ser criados e configurados pela extensão do Azure Docker VM. Bastam alguns minutos para a implementação concluir. Pode ver os detalhes sobre como a utilizar o estado Docker anfitrião a `azure vm show` comando.

O exemplo seguinte verifica o estado da VM denominada `myDockerVM` (o nome predefinido a partir de modelo - não alterar este nome) no grupo de recursos com o nome `myResourceGroup`. Introduza o nome do grupo de recursos que criou no passo anterior:

```bash
azure vm show -g myResourceGroup -n myDockerVM
```

O resultado do `azure vm show` comando é semelhante ao exemplo seguinte:

```
info:    Executing command vm show
+ Looking up the VM "myDockerVM"
+ Looking up the NIC "myVMNicD"
+ Looking up the public ip "myPublicIPD"
data:    Id                              :/subscriptions/guid/resourceGroups/myresourcegroup/providers/Microsoft.Compute/virtualMachines/MyDockerVM
data:    ProvisioningState               :Succeeded
data:    Name                            :MyDockerVM
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
[...]
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-D3-95
data:          Provisioning State        :Succeeded
data:          Name                      :myVMNicD
data:          Location                  :westus
data:            Public IP address       :13.91.107.235
data:            FQDN                    :mypublicip.westus.cloudapp.azure.com]
data:
data:    Diagnostics Instance View:
info:    vm show command OK
```

Na parte superior do resultado, verá o `ProvisioningState` da VM. Quando esta ação apresentará `Succeeded`, já terminou a implementação e pode SSH para a VM.

Perto do final do resultado, `FQDN` apresenta o nome de domínio completamente qualificado do seu anfitrião de Docker. Este FQDN é o que utiliza para SSH ao seu anfitrião de Docker nos passos restantes.


## <a name="deploy-your-first-nginx-container"></a>Implementar o seu primeiro contentor de nginx
Assim que a implementação de terminar, SSH para o seu anfitrião de Docker novo a partir do seu computador local. Introduza o seu próprio nome de utilizador e o FQDN da seguinte forma:

```bash
ssh ops@mypublicip.westus.cloudapp.azure.com
```

Assim que tiver sessão iniciada anfitrião do Docker, vamos executar um contentor nginx:

```
sudo docker run -d -p 80:80 nginx
```

O resultado é semelhante ao exemplo seguinte, tal como a imagem nginx é transferida e um contentor iniciado:

```
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
a48df1751a97: Pull complete
8ddc2d7beb91: Pull complete
Digest: sha256:2ca2638e55319b7bc0c7d028209ea69b1368e95b01383e66dfe7e4f43780926d
Status: Downloaded newer image for nginx:latest
b6ed109fb743a762ff21a4606dd38d3e5d35aff43fa7f12e8d4ed1d920b0cd74
```

Verificar o estado dos contentores em execução no seu anfitrião de Docker da seguinte forma:

```
sudo docker ps
```

O resultado é semelhante ao exemplo seguinte, que mostra que o contentor nginx está em execução e portas TCP 80 e 443 e a ser reencaminhado:

```
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

Para ver o contentor em ação, abra o num web browser e introduza o nome FQDN do seu anfitrião de Docker:

![Contentor de ngnix em execução](./media/virtual-machines-linux-dockerextension/nginxrunning.png)


## <a name="azure-docker-vm-extension-template-reference"></a>Referência do modelo de extensão de Docker VM Azure
O exemplo anterior utiliza um modelo de guia de introdução existente. Também pode implementar a extensão do Azure Docker VM com os seus próprios modelos de Gestor de recursos. Para fazê-lo, adicione o seguinte para os modelos de Gestor de recursos, que define o `vmName` da sua VM corretamente:

```
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'), '/DockerExtension'))]",
  "apiVersion": "2015-05-01-preview",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "DockerExtension",
    "typeHandlerVersion": "1.1",
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

Pode encontrar instruções passo a passo mais detalhada sobre como utilizar modelos de Gestor de recursos ao ler o artigo [Descrição geral do Gestor de recursos do Azure](../azure-resource-manager/resource-group-overview.md).


## <a name="next-steps"></a>Próximos passos
Poderá pretender [configurar o daemon Docker porta TCP](https://docs.docker.com/engine/reference/commandline/dockerd/#/bind-docker-to-another-hostport-or-a-unix-socket), compreender [segurança Docker](https://docs.docker.com/engine/security/security/)ou implementar contentores utilizando [Docker compor](https://docs.docker.com/compose/overview/). Para mais informações sobre a extensão do Azure Docker VM propriamente dito, consulte o [GitHub projeto](https://github.com/Azure/azure-docker-extension/).

Leia mais informações sobre as opções de implementação Docker adicionais no Azure:

- [Utilizar o Docker Machine com o controlador do Azure](./virtual-machines-linux-docker-machine.md)  
- [Introdução às Docker e compor definir e executar uma aplicação de contentor com várias numa máquina virtual Azure](virtual-machines-linux-docker-compose-quickstart.md).
- [Implementar um cluster de serviço de contentor do Azure](../container-service/container-service-deployment.md)
