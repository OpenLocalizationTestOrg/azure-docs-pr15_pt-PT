<properties
    pageTitle="Utilizar a extensão VM Docker para Linux no Azure"
    description="Descreve Docker e as extensões de máquinas virtuais do Azure e mostra como criar através de programação máquinas virtuais no Azure são docker anfitriões da linha de comandos utilizando o clip do Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="squillace"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="08/29/2016"
    ms.author="rasquill"/>

# <a name="using-the-docker-vm-extension-from-the-azure-command-line-interface-azure-cli"></a>Utilizando a extensão VM Docker do Azure da linha de comandos a Interface (Azure CLI)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]



Este tópico descreve como criar uma VM com a extensão de VM Docker a partir do modo de gestão (asm) serviço no Azure clip qualquer plataforma. [Docker](https://www.docker.com/) é uma das abordagens virtualização mais populares que utiliza [Linux contentores](http://en.wikipedia.org/wiki/LXC) em vez de máquinas virtuais como uma forma de isolamento de dados e de informática em recursos partilhados. Pode utilizar a extensão Docker VM e o [Azure Linux agente](virtual-machines-linux-agent-user-guide.md) para criar uma VM Docker que aloja qualquer número de membros em contentores para as suas aplicações no Azure. Para ver um debate de alto nível de contentores e os respetivos vantagens, consulte o artigo [Docker alto nível quadro](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).


##<a name="how-to-use-the-docker-vm-extension-with-azure"></a>Como utilizar a extensão de VM Docker com Azure
Para utilizar a extensão Docker VM com Azure, tem de instalar uma versão da [Interface de linha de comandos Azure](https://github.com/Azure/azure-sdk-tools-xplat) (Azure CLI) mais elevados que 0.8.6 (como este texto foi escrito a versão atual não for 0.10.0). Pode instalar o clip do Azure Mac, Linux e Windows.


O processo de concluída para utilizar Docker no Azure é simple:

+ Instalar o clip do Azure e as respectivas dependências no computador a partir do qual pretende controlar Azure (no Windows, esta será uma distribuição Linux em execução como uma máquina virtual)
+ Utilize os comandos do Azure clip Docker para criar um anfitrião VM Docker no Azure
+ Utilize os comandos de Docker locais para gerir os contentores Docker na sua VM Docker no Azure.


### <a name="install-the-azure-command-line-interface-azure-cli"></a>Instalar o Azure Interface de comandos (Azure CLI)

Para instalar e configurar o clip do Azure, consulte o artigo [sobre como instalar a Interface de comandos do Azure](../xplat-cli-install.md). Para confirmar a instalação, escreva `azure` na linha de comandos e após um breve momento deverá ver o ClipArt Azure clip ASCII, que lista os comandos básicos disponíveis para si. Se a instalação trabalhou corretamente, deverá conseguir escrever `azure help vm` e veja o que é um dos comandos listados "docker".

> [AZURE.NOTE] Docker tem ferramentas para o Windows, [Docker máquina](https://docs.docker.com/installation/windows/), que também pode utilizar para automatizar a criação de um cliente docker que pode utilizar para trabalhar com o Azure VMs como anfitriões docker.

### <a name="connect-the-azure-cli-to-to-your-azure-account"></a>Ligar o clip Azure para a sua conta Azure
Antes de poder utilizar o clip do Azure tem de associar as suas credenciais de conta Azure o clip do Azure na sua plataforma. A secção [como ligar à sua subscrição do Azure](../xplat-cli-connect.md) explica como transferir e importar o seu ficheiro **.publishsettings** ou associar o seu clip Azure com um id organizacional.

> [AZURE.NOTE] Existem algumas diferenças em comportamento ao utilizar um ou outros métodos de autenticação, para que não se esqueça de ler o documento acima para compreender a funcionalidade diferente.

### <a name="install-docker-and-use-the-docker-vm-extension-for-azure"></a>Instalar Docker e utilizar a extensão de VM Docker para Azure
Siga as [instruções de instalação Docker](https://docs.docker.com/installation/#installation) para instalar Docker localmente no seu computador.

Para utilizar Docker com uma máquina de Virtual do Azure, a imagem de Linux utilizada para a VM tem de ter o [Azure Linux VM Agent](virtual-machines-linux-agent-user-guide.md) instalado. Atualmente, existem apenas dois tipos de imagens que fornecem esta informação:

+ Uma imagem de Ubuntu a partir da Galeria de imagem do Azure ou

+ Uma imagem de Linux personalizada que criou com o Azure Linux VM Agent instalado e configurado. Consulte o artigo [Azure Linux VM agente](virtual-machines-linux-agent-user-guide.md) para mais informações sobre como construir uma VM Linux personalizado com o agente de VM Azure.

### <a name="using-the-azure-image-gallery"></a>Utilizar a Galeria de imagem do Azure

A partir de uma sessão Terminal ou festa, utilize o seguinte comando do Azure clip para localizar a imagem de Ubuntu mais recente na Galeria de VM para utilizar ao escrever

`azure vm image list | grep Ubuntu-14_04`

e selecione um dos nomes de imagem, tais como `b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB`e utilize o seguinte comando para criar uma nova VM utilizar essa imagem.

```
azure vm docker create -e 22 -l "West US" <vm-cloudservice name> "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB" <username> <password>
```

em que:

+ * &lt;vm cloudservice nome&gt; * é o nome da VM que irão tornar-se o computador do anfitrião de contentor Docker no Azure

+  * &lt;nome de utilizador&gt; * é o nome de utilizador do utilizador da VM raiz predefinido

+ * &lt;palavra-passe&gt; * é a palavra-passe da conta de *nome de utilizador* que cumpre as normas de complexidade do Azure

> [AZURE.NOTE] Atualmente, uma palavra-passe tem de ter, pelo menos, 8 caracteres, contêm um minúsculas e um maiúsculas caráter, um número e um caráter especial tal como um dos seguintes carateres: `!@#$%^&+=`. Não, o período no fim da frase anterior não é um caráter especial.

Se o comando foi efetuada com êxito, deverá ver algo semelhante ao seguinte, consoante as opções que utilizou e precisos argumentos:

![](./media/virtual-machines-linux-classic-cli-use-docker/dockercreateresults.png)

> [AZURE.NOTE] Criar uma máquina virtual pode demorar alguns minutos, mas após ter sido aprovisionada (o valor de estado é `ReadyRole`) o daemon Docker (o serviço de Docker) é iniciado e pode ligar-se para o anfitrião do contentor Docker.

Para testar a VM Docker que criou no Azure, escreva

`docker --tls -H tcp://<vm-name-you-used>.cloudapp.net:2376 info`

onde * &lt;vm-nome--utilizou&gt; * é o nome da máquina virtual que utilizou em sua chamada para `azure vm docker create`. Deverá ver algo semelhante ao seguinte, que indica que o seu VM de anfitrião Docker é para cima e em execução no Azure e aguardar os seus comandos. 

Agora que pode experimentar para ligar através do seu cliente de docker para obter informações (em alguns configurações de cliente Docker, tal como que no Mac, poderá ter de utilizar `sudo`):

    sudo docker --tls -H tcp://testsshasm.cloudapp.net:2376 info
    Password:
    Containers: 0
    Images: 0
    Storage Driver: devicemapper
    Pool Name: docker-8:1-131781-pool
    Pool Blocksize: 65.54 kB
    Backing Filesystem: extfs
    Data file: /dev/loop0
    Metadata file: /dev/loop1
    Data Space Used: 1.821 GB
    Data Space Total: 107.4 GB
    Data Space Available: 28 GB
    Metadata Space Used: 1.479 MB
    Metadata Space Total: 2.147 GB
    Metadata Space Available: 2.146 GB
    Udev Sync Supported: true
    Deferred Removal Enabled: false
    Data loop file: /var/lib/docker/devicemapper/devicemapper/data
    Metadata loop file: /var/lib/docker/devicemapper/devicemapper/metadata
    Library Version: 1.02.77 (2012-10-15)
    Execution Driver: native-0.2
    Logging Driver: json-file
    Kernel Version: 3.19.0-28-generic
    Operating System: Ubuntu 14.04.3 LTS
    CPUs: 1
    Total Memory: 1.637 GiB
    Name: testsshasm
    WARNING: No swap limit support

Apenas para ter a certeza de que é útil todos os, pode examinar a VM para a extensão de Docker:

    azure vm extension get testsshasm
    info: Executing command vm extension get
    + Getting virtual machines
    data: Publisher Extension name ReferenceName Version State
    data: -------------------- --------------- ------------------------- ------- ------
    data: Microsoft.Azure.E... DockerExtension DockerExtension 1.* Enable
    info: vm extension get command OK

### <a name="docker-host-vm-authentication"></a>Docker anfitrião VM autenticação

Para além de criar Docker VM, o `azure vm docker create` comando também cria automaticamente os certificados necessários para permitir que o computador do cliente Docker ligar ao anfitrião do contentor Azure utilizando HTTPS e os certificados são armazenados no máquinas o cliente e o anfitrião, conforme adequado. Em tentativas subsequentes, os certificados existentes são reutilizados e partilhados com o anfitrião o novo.

Por predefinição, os certificados são colocados na `~/.docker`, e Docker será configurada para executar no porta **2376**. Se pretender utilizar uma porta diferente ou do diretório, em seguida, pode utilizar uma das seguintes `azure vm docker create` opções de linha de comandos para configurar o contentor Docker VM a utilizar uma porta diferente ou certificados diferentes para ligar a clientes do anfitrião:

```
-dp, --docker-port [port]              Port to use for docker [2376]
-dc, --docker-cert-dir [dir]           Directory containing docker certs [.docker/]
```

O daemon Docker no anfitrião do está configurado para escutar e autenticar ligações de cliente na porta especificada utilizando os certificados gerados pela `azure vm docker create` comando. O computador cliente tem de ter estes certificados para aceder ao anfitrião do Docker.

> [AZURE.NOTE] Um anfitrião utilizarão executar sem estes certificados serão vulnerável a qualquer pessoa que podem ser utilizados para ligar ao computador. Antes de modificar a configuração predefinida, certifique-se de que compreender os riscos para o seu computadores e aplicações.

## <a name="next-steps"></a>Próximos passos

* Está pronto para ir para o [Guia de utilizador Docker] e utilizar o seu VM Docker. Para criar uma VM com capacidade de Docker no novo portal, veja [como utilizar a extensão de VM Docker com o Portal].

* A extensão do Azure Docker VM também suporta a compor Docker, que utiliza um ficheiro YAML declarativa para terem uma aplicação do programador-modeled qualquer ambiente e gerar uma implementação consistente. Consulte o artigo [Introdução às Docker e compor definir e executar uma aplicação de contentor com várias numa máquina virtual Azure].  

<!--Anchors-->
[Subheading 1]: #subheading-1
[Subheading 2]: #subheading-2
[Subheading 3]: #subheading-3
[Next steps]: #next-steps

[How to use the Docker VM Extension with Azure]: #How-to-use-the-Docker-VM-Extension-with-Azure
[Virtual Machine Extensions for Linux and Windows]: #Virtual-Machine-Extensions-For-Linux-and-Windows
[Container and Container Management Resources for Azure]: #Container-and-Container-Management-Resources-for-Azure



<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: ../web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md
[Como utilizar a extensão de VM Docker com o Portal]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/

[Guia de utilizador docker]: https://docs.docker.com/userguide/
 
[Introdução às Docker e compor definir e executar uma aplicação do contentor com várias numa máquina virtual Azure]:virtual-machines-linux-docker-compose-quickstart.md