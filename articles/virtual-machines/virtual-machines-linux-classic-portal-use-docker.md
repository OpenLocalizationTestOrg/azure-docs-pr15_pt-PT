<properties
    pageTitle="Utilizar o Docker VM extensão para Linux | Microsoft Azure"
    description="Descreve Docker e as extensões de máquinas virtuais do Azure e como criar máquinas virtuais do Azure que são anfitriões docker utilizando o clip do Azure num modelo de implementação clássica."
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
    ms.date="05/27/2016"
    ms.author="rasquill"/>


# <a name="using-the-docker-vm-extension-with-the-azure-classic-portal"></a>Utilizar a extensão de VM Docker com o portal clássico do Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


[Docker](https://www.docker.com/) é uma das abordagens virtualização mais populares que utiliza [Linux contentores](http://en.wikipedia.org/wiki/LXC) em vez de máquinas virtuais como uma forma de isolamento de dados e de informática em recursos partilhados. Pode utilizar a extensão de Docker VM gerida pelo [Azure Linux agente] para criar uma VM Docker que aloja qualquer número de membros em contentores para as suas aplicações no Azure.

> [AZURE.NOTE] Este tópico descreve como criar uma VM Docker a partir do Azure portal clássico. Para ver como criar uma VM Docker na linha de comandos, consulte o artigo [como utilizar a extensão de VM Docker a partir da linha de comandos Interface o Azure (Azure CLI)]. Para ver um debate de alto nível de contentores e os respetivos vantagens, consulte o artigo [Docker alto nível quadro](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).

## <a name="create-a-new-vm-from-the-image-gallery"></a>Criar uma nova VM a partir da Galeria de imagem
O primeiro passo requer um VM Azure a partir de uma imagem de Linux que suporta a extensão de VM Docker, utilizando uma imagem de Ubuntu 14.04 LTS a partir da Galeria de imagem como uma imagem de servidor de exemplo e o ambiente de trabalho do Ubuntu 14.04 como um cliente. No portal do, clique em **+ Novo** no canto inferior esquerdo para criar uma nova instância VM e selecione uma imagem de Ubuntu 14.04 LTS as seleções disponíveis ou a partir da Galeria de imagem completa, conforme apresentado abaixo.

> [AZURE.NOTE] Atualmente, só Ubuntu 14.04 LTS imagens mais recentes do que de Julho de 2014 suportam a extensão de VM Docker.

![Criar uma nova imagem Ubuntu](./media/virtual-machines-linux-classic-portal-use-docker/ChooseUbuntu.png)

## <a name="create-docker-certificates"></a>Criar certificados Docker

Depois de ter sido criada a VM, certifique-se de que Docker está instalado no seu computador cliente. (Para obter detalhes, consulte [as instruções de instalação Docker](https://docs.docker.com/installation/#installation).)

Criar os ficheiros de certificado e a chave de comunicação Docker de acordo com [a executar o Docker com https] e colocá-los na **`~/.docker`** directório no computador cliente.

> [AZURE.NOTE] A extensão de VM Docker no portal do atualmente credenciais que estão codificada base64.

Na linha de comandos, utilize **`base64`** ou outra ferramenta de codificação favorita para criar tópicos codificado em base64. Este procedimento com um conjunto de ficheiros de certificado e chave simple poderá ter um aspeto semelhante a esta:

```
 ~/.docker$ ls
 ca-key.pem  ca.pem  cert.pem  key.pem  server-cert.pem  server-key.pem
 ~/.docker$ base64 ca.pem > ca64.pem
 ~/.docker$ base64 server-cert.pem > server-cert64.pem
 ~/.docker$ base64 server-key.pem > server-key64.pem
 ~/.docker$ ls
 ca64.pem    ca.pem    key.pem            server-cert.pem   server-key.pem
 ca-key.pem  cert.pem  server-cert64.pem  server-key64.pem
```

## <a name="add-the-docker-vm-extension"></a>Adicionar a extensão VM Docker
Para adicionar a extensão de VM Docker, localize a instância VM que criou e desloque para baixo até **extensões** e clique na mesma para ativar as extensões de VM, conforme apresentado abaixo.
> [AZURE.NOTE] Esta funcionalidade é suportada no portal do pré-visualizar apenas: https://portal.azure.com/

![](./media/virtual-machines-linux-classic-portal-use-docker/ClickExtensions.png)
### <a name="add-an-extension"></a>Adicionar uma extensão
Clique no sinal **+ Adicionar** para apresentar as extensões de VM possíveis, pode adicionar a este VM.

![](./media/virtual-machines-linux-classic-portal-use-docker/ClickAdd.png)
### <a name="select-the-docker-vm-extension"></a>Selecione a extensão VM Docker
Selecione a extensão de VM Docker, que reúne a descrição do Docker e ligações importantes, e, em seguida, clique em **Criar** na parte inferior para começar o processo de instalação.

![](./media/virtual-machines-linux-classic-portal-use-docker/ChooseDockerExtension.png)

![](./media/virtual-machines-linux-classic-portal-use-docker/CreateButtonFocus.png)
### <a name="add-your-certificate-and-key-files"></a>Adicione o certificado e ficheiros de chave:

Nos campos do formulário, introduza as versões codificado em base64 do seu certificado de AC, o certificado de servidor e a chave do servidor, conforme mostrado no seguinte gráfico.

![](./media/virtual-machines-linux-classic-portal-use-docker/AddExtensionFormFilled.png)

> [AZURE.NOTE] Tenha em atenção que (como a imagem anterior) a 2376 estiver preenchida por predefinição. Pode introduzir qualquer ponto final de aqui, mas o próximo passo será-se para a abrir o ponto final correspondente. Se alterar a predefinição, certifique-se abrir o ponto final correspondente no próximo passo.

## <a name="add-the-docker-communication-endpoint"></a>Adicionar o ponto final de comunicação de Docker
Ao visualizar o grupo de recursos que criou, selecione o grupo de segurança de rede associados a VM e clique em **Regras de entrada de segurança** para visualizar as regras, conforme mostrado aqui.

![](./media/virtual-machines-linux-classic-portal-use-docker/AddingEndpoint.png)

Clique em **+ Adicionar** para adicionar outra regra e, no caso predefinido, introduza um nome para o ponto final (neste exemplo, **Docker**) e 2376 'destino porta intervalo'. Defina o valor protocolo mostrando **TCP**e clique em **OK** para criar a regra.

![](./media/virtual-machines-linux-classic-portal-use-docker/AddEndpointFormFilledOut.png)


## <a name="test-your-docker-client-and-azure-docker-host"></a>Testar o seu cliente Docker e Azure Docker anfitrião
Localize e copie o nome do domínio da sua VM e, na linha de comandos do seu computador cliente, tipo `docker --tls -H tcp://` *dockerextension* `.cloudapp.net:2376 info` (onde *dockerextension* é substituída pelo subdomínio a VM).

O resultado deverá ser semelhante ao seguinte:

```
$ docker --tls -H tcp://dockerextension.cloudapp.net:2376 info
Containers: 0
Images: 0
Storage Driver: devicemapper
 Pool Name: docker-8:1-131214-pool
 Pool Blocksize: 65.54 kB
 Data file: /var/lib/docker/devicemapper/devicemapper/data
 Metadata file: /var/lib/docker/devicemapper/devicemapper/metadata
 Data Space Used: 305.7 MB
 Data Space Total: 107.4 GB
 Metadata Space Used: 729.1 kB
 Metadata Space Total: 2.147 GB
 Library Version: 1.02.82-git (2013-10-04)
Execution Driver: native-0.2
Kernel Version: 3.13.0-36-generic
WARNING: No swap limit support
```

Depois de concluir os passos acima, tem agora um anfitrião Docker totalmente funcional em execução numa VM Azure, configurado para estar ligado à remotamente a partir de outros clientes.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximos passos

Está pronto para ir para o [Guia de utilizador Docker] e utilizar o seu VM Docker. Se pretender automatizar a criação de Docker de anfitriões no Azure VMs através da interface de linha de comandos, veja [como utilizar a extensão de VM Docker a partir da linha de comandos Interface o Azure (Azure CLI)]

<!--Anchors-->
[Create a new VM from the Image Gallery]: #createvm
[Create Docker Certificates]: #dockercerts
[Add the Docker VM Extension]: #adddockerextension
[Test Docker Client and Azure Docker Host]: #testclientandserver
[Next steps]: #next-steps

<!--Image references-->
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[Como utilizar a extensão de VM Docker a partir da linha de comandos Interface o Azure (Azure CLI)]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/
[Agente de Linux Azure]: virtual-machines-linux-agent-user-guide.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md

[Executar Docker com https]: http://docs.docker.com/articles/https/
[Guia de utilizador docker]: https://docs.docker.com/userguide/
