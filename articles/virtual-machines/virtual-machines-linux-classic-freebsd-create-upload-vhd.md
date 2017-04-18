<properties
   pageTitle="Criar e carregar uma imagem FreeBSD VM | Microsoft Azure"
   description="Saiba como criar e carregar um disco rígido virtual (VHD) que contém o sistema operativo FreeBSD para criar uma máquina virtual Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="KylieLiang"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/29/2016"
   ms.author="kyliel"/>

# <a name="create-and-upload-a-freebsd-vhd-to-azure"></a>Criar e carregar um VHD FreeBSD para Azure

Este artigo mostra-lhe como criar e carregar um disco rígido virtual (VHD) que contém o sistema operativo FreeBSD. Depois de carregá-la, pode utilizá-lo como a sua própria imagem para criar uma máquina de virtual (VM) no Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


## <a name="prerequisites"></a>Pré-requisitos
Este artigo assume que tem os seguintes itens:

- **Azure uma subscrição**– se não tiver uma conta, pode criar outra em alguns minutos. Se tiver uma subscrição do MSDN, consulte o artigo [crédito Azure mensal para subscritores do Visual Studio.](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Caso contrário, saiba como [criar uma conta de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).  

- **Ferramentas de azure PowerShell**– o Azure PowerShell módulo tem de estar instalado e configurado para utilizar a sua subscrição do Azure. Para transferir o módulo, consulte o artigo [Azure transferências](https://azure.microsoft.com/downloads/). Um tutorial que descreve como instalar e configurar o módulo estiver disponível aqui. Utilize o cmdlet [Azure transferências](https://azure.microsoft.com/downloads/) para carregar o VHD.

- **Sistema operativo de FreeBSD instalado num ficheiro. vhd**– um suportadas FreeBSD sistema operativo tem de estar instalado para um disco rígido virtual. Existem várias ferramentas para criar ficheiros. vhd. Por exemplo, pode utilizar uma solução de virtualização como Hyper-V para criar o ficheiro. vhd e instalar o sistema operativo. Para obter instruções sobre como instalar e utilizar Hyper-V, consulte o artigo [Instalar Hyper-V e criar uma máquina virtual](http://technet.microsoft.com/library/hh846766.aspx).

> [AZURE.NOTE] O formato mais recente do VHDX não é suportado no Azure. Pode converter o disco para o formato VHD utilizando o Gestor de Hyper-V ou o cmdlet [vhd converter](https://technet.microsoft.com/library/hh848454.aspx). Além disso, existe um [tutorial no MSDN sobre como utilizar FreeBSD com Hyper-V](http://blogs.msdn.com/b/kylie/archive/2014/12/25/running-freebsd-on-hyper-v.aspx).

Esta tarefa inclui os cinco passos seguintes.

## <a name="step-1-prepare-the-image-for-upload"></a>Passo 1: Preparar a imagem para carregar

Na máquina virtual onde instalou o sistema operativo de FreeBSD, conclua os seguintes procedimentos:

1. Active o DHCP.

        # echo 'ifconfig_hn0="SYNCDHCP"' >> /etc/rc.conf
        # service netif restart

2. Ative SSH.

    SSH está ativada por predefinição após a instalação a partir de disco. Caso a mesma não está ativada por algum motivo, ou se utilizar FreeBSD VHD diretamente, escreva o seguinte:

        # echo 'sshd_enable="YES"' >> /etc/rc.conf
        # ssh-keygen -t dsa -f /etc/ssh/ssh_host_dsa_key
        # ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key
        # service sshd restart

3. Configure uma consola série.

        # echo 'console="comconsole vidconsole"' >> /boot/loader.conf
        # echo 'comconsole_speed="115200"' >> /boot/loader.conf

4. Instale sudo.

    A conta de raiz é desativada no Azure. Isto significa que terá de utilizar sudo a partir de um utilizador privilegiado para executar comandos com privilégios elevados.

        # pkg install sudo
;
5. Pré-requisitos para agente Azure.

        # pkg install python27  
        # pkg install Py27-setuptools27   
        # ln -s /usr/local/bin/python2.7 /usr/bin/python   
        # pkg install git

6. Instale agente Azure.

    A versão mais recente do agente Azure sempre pode encontrar no [github](https://github.com/Azure/WALinuxAgent/releases). A versão 2.0.10 + oficialmente suporta FreeBSD 10 e 10.1 e a versão 2.1.4 suporta oficialmente FreeBSD 10.2 e versões posteriores.

        # git clone https://github.com/Azure/WALinuxAgent.git  
        # cd WALinuxAgent  
        # git tag  
        …
        WALinuxAgent-2.0.16
        …
        v2.1.4
        v2.1.4.rc0
        v2.1.4.rc1

    Para 2.0, vamos utilizar 2.0.16 como um exemplo:

        # git checkout WALinuxAgent-2.0.16
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  

    Para 2.1, vamos utilizar 2.1.4 como um exemplo:

        # git checkout v2.1.4
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  
        # ln -sf /usr/local/sbin/waagent2.0 /usr/sbin/waagent2.0

    >[AZURE.IMPORTANT] Depois de instalar agente do Azure, é boa ideia verificar se está a ser executado:

        # waagent -version
        WALinuxAgent-2.1.4 running on freebsd 10.3
        Python: 2.7.11
        # service –e | grep waagent
        /etc/rc.d/waagent
        # cat /var/log/waagent.log

7. Deprovision o sistema.

    Deprovision o sistema limpá-lo e torná-lo adequado para aprovisionamento novamente. O seguinte comando também elimina a última conta de utilizador aprovisionada e os dados associados:

        # echo "y" |  /usr/local/sbin/waagent -deprovision+user  
        # echo  'waagent_enable="YES"' >> /etc/rc.conf

    Agora pode encerrar a VM.

## <a name="step-2-create-a-storage-account-in-azure"></a>Passo 2: Criar uma conta de armazenamento no Azure ##

É necessária uma conta de armazenamento no Azure para carregar um ficheiro. vhd para que possam ser utilizado para criar uma máquina virtual. Pode utilizar o portal clássico Azure para criar uma conta de armazenamento.

1. Inicie sessão no [portal clássica Azure](https://manage.windowsazure.com).

2. Na barra de comando, selecione **Novo**.

3. Selecione **Serviços de dados** > **armazenamento** > **criação rápida**.

    ![Criar rapidamente uma conta de armazenamento](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/Storage-quick-create.png)

4. Preencha os campos da seguinte forma:

    - No campo **URL** , escreva um nome de subdomínio para utilizar o URL de conta de armazenamento. Pode conter a entrada de 3-24 números e letras em minúsculas. Este nome torna-se o nome do anfitrião do URL que é utilizado para endereçar armazenamento de Blobs do Azure, armazenamento de Azure filas ou recursos de armazenamento de tabela do Azure para a subscrição.

    - No menu pendente **Grupo localização/afinidade** , escolha a **localização ou grupo de afinidade** para a conta de armazenamento. Um grupo de afinidade permite-lhe coloque as suas serviços em nuvem e armazenamento no Centro de dados do mesmo.

    - No campo de **replicação** , decida se pretende utilizar a replicação **Geo redundantes** para a conta de armazenamento. Replicação geo está ativada por predefinição. Esta opção replica os seus dados numa localização secundário, sem custos para si, para que o seu armazenamento Falha ao longo do até essa localização se ocorre uma falha principal na localização principal. A localização secundária é atribuída automaticamente e não pode ser alterada. Se precisar de mais controlo sobre a localização do seu armazenamento baseado na nuvem devido a requisitos legais ou organizacional política, pode desativar geo replicação. No entanto, tenha em atenção de que se ativar posteriormente geo replicação, será cobrado uma taxa de transferência de dados únicos para criar uma réplica os seus dados existentes para a localização secundária. Serviços de armazenamento sem replicação geo são oferecidos por um desconto. Obter mais detalhes sobre como gerir a replicação geo de contas de armazenamento podem ser encontradas aqui: [criar, gerir, ou eliminar uma conta de armazenamento](../storage-create-storage-account/#replication-options).

    ![Introduza os detalhes de conta de armazenamento](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/Storage-create-account.png)


5. Selecione **criar a conta de armazenamento**. A conta é apresentada em **armazenamento**.

    ![Conta de armazenamento criada com êxito](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/Storagenewaccount.png)

6. Em seguida, crie um contentor para os seus ficheiros. vhd carregados. Selecione o nome da conta de armazenamento e, em seguida, selecione **contentores**.

    ![Detalhe de conta de armazenamento](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/storageaccount_detail.png)

7. Selecione **criar um contentor**.

    ![Detalhe de conta de armazenamento](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/storageaccount_container.png)

8. No campo **nome** , escreva um nome para o contentor. Em seguida, no menu pendente **acesso** , selecione o tipo de política de acesso que pretende.

    ![Nome do contentor](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/storageaccount_containervalues.png)

    > [AZURE.NOTE] Por predefinição, o contentor for privado e só pode ser acedido pelo proprietário da conta. Para permitir que o público acesso de leitura para blobs no contentor, mas não para as propriedades do contentor e metadados, utilize a opção de **BLOBs público** . Para permitir o acesso de leitura completa público para o contentor e blobs, utilize a opção de **Contentor público** .

## <a name="step-3-prepare-the-connection-to-azure"></a>Passo 3: Preparar a ligação do Azure

Antes de que possa carregar um ficheiro. vhd, é necessário estabelecer uma ligação segura entre o computador e a sua subscrição do Azure. Pode utilizar o método de Azure Active Directory (Azure AD) ou o método de certificados para o fazer.

### <a name="use-the-azure-ad-method-to-upload-a-vhd-file"></a>Utilizar o método de Azure AD para carregar um ficheiro. vhd

1. Abra a consola do Azure PowerShell.

2. Escreva o seguinte comando:  
    `Add-AzureAccount`

    Este comando abre uma janela de início de sessão no local onde pode iniciar sessão com a sua conta escolar ou profissional.

    ![Janela do PowerShell](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/add_azureaccount.png)

3. Azure autentica e guarda as informações de credenciais. Em seguida,-fecha a janela.

### <a name="use-the-certificate-method-to-upload-a-vhd-file"></a>Utilizar o método de certificados para carregar um ficheiro. vhd

1. Abra a consola do Azure PowerShell.

2. Tipo:  `Get-AzurePublishSettingsFile`.

3. Numa janela do browser é aberto e pede-lhe para transferir um ficheiro de .publishsettings. Este ficheiro contém informações e um certificado para a sua subscrição Azure.

    ![Página de transferência do browser](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/Browser_download_GetPublishSettingsFile.png)

3. Guarde o ficheiro .publishsettings.

4. Tipo:  `Import-AzurePublishSettingsFile <PathToFile>`, onde `<PathToFile>` é o caminho completo para o ficheiro .publishsettings.

   Para mais informações, consulte o artigo [começar a trabalhar com os cmdlets do Azure](http://msdn.microsoft.com/library/windowsazure/jj554332.aspx).

   Para mais informações sobre como instalar e configurar o PowerShell, consulte o artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

## <a name="step-4-upload-the-vhd-file"></a>Passo 4: Carregue o ficheiro. vhd

Quando carregar o ficheiro. vhd, pode colocá-lo em qualquer lugar no seu armazenamento Blob do. Seguem-se alguns termos que irá utilizar quando carregar o ficheiro:
-  **BlobStorageURL** é o URL para a conta de armazenamento que criou no passo 2.
-  **YourImagesFolder** é o contentor dentro de armazenamento de BLOBs onde pretende armazenar as imagens.
- **VHDName** é a etiqueta que aparece no portal do Azure clássico para identificar o disco rígido virtual.
- **PathToVHDFile** é o nome do ficheiro. vhd e o caminho completo.


A partir da janela do Azure PowerShell que utilizou no passo anterior, escreva:

        Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>

## <a name="step-5-create-a-vm-with-the-uploaded-vhd-file"></a>Passo 5: Criar uma VM com o ficheiro. vhd carregados
Depois de carregar o ficheiro. vhd, pode adicioná-la como uma imagem para a lista de imagens personalizadas que estão associadas a sua subscrição e criar uma máquina virtual com esta imagem personalizada.

1. A partir da janela do Azure PowerShell que utilizou no passo anterior, escreva:

        Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>

    > [AZURE.NOTE]Utilize Linux como o tipo de sistema operativo. A versão atual do Azure PowerShell aceita apenas "Linux" ou "Windows" como um parâmetro.

2. Depois de concluir os passos anteriores, a nova imagem é listada quando escolher no separador **imagens** no portal do Azure clássico.  

    ![Escolher uma imagem](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/addfreebsdimage.png)

3. Crie uma máquina virtual a partir da galeria. Esta imagem nova está agora disponível nas **Minhas imagens**.
4. Selecione a nova imagem. Em seguida, percorra os avisos para configurar um nome de anfitrião, palavra-passe, SSH chave e assim sucessivamente.

    ![Imagem personalizada](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/createfreebsdimageinazure.png)

4. Depois de concluir o processo de aprovisionamento, verá a VM FreeBSD em execução no Azure.

    ![Imagem de FreeBSD no azure](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/freebsdimageinazure.png)
