<properties
    pageTitle="Capture uma imagem de uma VM Linux | Microsoft Azure"
    description="Saiba como capturar uma imagem de uma baseado em Linux Azure máquina virtual (VM) criada com o modelo de implementação clássica."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/31/2016"
    ms.author="iainfou"/>


# <a name="how-to-capture-a-classic-linux-virtual-machine-as-an-image"></a>Como capturar uma máquina de virtual Linux clássica como uma imagem

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Obter informações sobre como [executar estes passos utilizando o modelo de Gestor de recursos](virtual-machines-linux-capture-image.md).

Este artigo mostra-lhe como capturar uma máquina virtual Azure clássica Linux a ser executado como uma imagem para criar outras máquinas virtuais. Esta imagem inclui o disco de sistema operativo e discos de dados ligados à máquina virtual. -Não inclui configuração de rede, pelo que necessita configurar que quando cria as outras máquinas virtuais da imagem.

Azure armazena a imagem em **imagens**, juntamente com quaisquer imagens que carregou. Para mais informações sobre as imagens, consulte o artigo [Sobre como imagens de Máquina Virtual no Azure] [].

## <a name="before-you-begin"></a>Antes de começar

Estes passos partem do pressuposto de que já criou uma máquina virtual Azure utilizando o modelo de implementação clássica e configurado o sistema operativo, incluindo quaisquer discos de dados a anexar. Se precisar de criar uma VM, leia [como criar uma máquina de Virtual Linux] [].


## <a name="capture-the-virtual-machine"></a>Capturar a máquina virtual

1. [Ligar à máquina virtual](virtual-machines-linux-mac-create-ssh-keys.md) através de um cliente SSH da sua escolha.

2. Na janela do SSH, escreva o seguinte comando. O resultado do `waagent` podem variar ligeiramente consoante a versão deste utilitário:

    `sudo waagent -deprovision+user`

    Este comando tenta limpar do sistema e torná-la adequado para reprovisioning. Esta operação executa as seguintes tarefas:

    - Remove as teclas de anfitrião SSH (se existir Provisioning.RegenerateSshHostKeyPair y no ficheiro de configuração)
    - Limpa a configuração do servidor de nomes no /etc/resolv.conf
    - Remove o `root` palavra-passe de utilizador a partir do/etc/sombra (se existir Provisioning.DeleteRootPassword «y» no ficheiro de configuração)
    - Remove em cache DHCP concessões de cliente
    - Nome do anfitrião repõe para localhost.localdomain
    - Elimina a última utilizador aprovisionada conta (obtida de /var/lib/waagent) **e os dados associados**.

    >[AZURE.NOTE] Deprovisioning elimina ficheiros e dados para "generalizar" a imagem. Execute apenas este comando numa máquina virtual que pretende capturar como um novo modelo de imagem. Não garante que a imagem está desmarcada todas as informações confidenciais ou é adequada para a redistribuição de terceiros.


3. Escreva **y** para continuar. Pode adicionar a `-force` parâmetro para evitar este passo de confirmação.

4. Escreva **Sair** para fechar o cliente SSH.

    >[AZURE.NOTE] Os restantes passos partem do pressuposto de que já tiver [instalado o clip do Azure](../xplat-cli-install.md) no computador cliente. Também podem ser feitos os seguintes passos no [portal clássica Azure] [].

5. A partir do computador cliente, abra o clip do Azure e inicie sessão à sua subscrição do Azure. Para obter detalhes, leia [ligar a uma subscrição do Azure a partir do clip o Azure](../xplat-cli-connect.md).

6. Certifique-se de que está no modo de gestão de serviços:

    `azure config mode asm`

7. Encerre a máquina virtual que já será descontinuada nos passos anteriores com:

    `azure vm shutdown <your-virtual-machine-name>`

    >[AZURE.NOTE] Pode saber todas as máquinas virtuais criadas na sua subscrição utilizando`azure vm list`

8. Quando a máquina virtual está parada, capture a imagem com o comando:

    `azure vm capture -t <your-virtual-machine-name> <new-image-name>`

    Escreva o nome de imagem que pretende em vez de _novo nome imagem_. Este comando cria uma imagem do sistema operativo GRG. O `-t` subcomando elimina a máquina virtual original.

9.  A nova imagem está agora disponível na lista de imagens que podem ser utilizados para configurar qualquer máquinas virtuais novas. É possível visualizá-lo com o comando:

    `azure vm image list`

    No [portal clássica Azure] [], aparece na lista de **imagens** .

    ![Captura de ecrã com êxito](./media/virtual-machines-linux-classic-capture-image/VMCapturedImageAvailable.png)


## <a name="next-steps"></a>Próximos passos
A imagem está pronta para ser utilizado para criar máquinas virtuais. Pode utilizar o comando Azure clip `azure vm create` e fornecer o nome da imagem que criou. Consulte o artigo [utilizar o clip do Azure com o modelo de implementação clássico](../virtual-machines-command-line-tools.md) para obter detalhes sobre o comando. Em alternativa, utilize o [Azure portal clássico] [] para criar uma máquina virtual personalizada utilizando o método **a partir da Galeria** e selecionar a imagem que criou. Veja [como criar uma máquina de Virtual personalizada] [] para obter mais detalhes.

**Consulte também:** [Guia de utilizador do agente de Linux Azure](virtual-machines-linux-agent-user-guide.md)

[Azure portal clássico]: http://manage.windowsazure.com
[Acerca de imagens de Máquina Virtual no Azure]: virtual-machines-linux-classic-about-images.md
[Como criar uma Máquina Virtual personalizado]: virtual-machines-linux-classic-create-custom.md
[How to Attach a Data Disk to a Virtual Machine]: virtual-machines-windows-classic-attach-disk.md
[Como criar uma máquina de Virtual Linux]: virtual-machines-linux-classic-create-custom.md
