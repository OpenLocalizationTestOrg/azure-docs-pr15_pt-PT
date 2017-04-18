<properties
   pageTitle="Implementar a matriz Virtual StorSimple 1 - preparação do Portal"
   description="Tutorial primeiro para implementar StorSimple matriz virtual envolve preparar o portal"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/24/2016"
   ms.author="alkohli"/>

# <a name="deploy-storsimple-virtual-array---prepare-the-portal"></a>Implementar StorSimple Virtual matriz - preparar o portal

![](./media/storsimple-ova-deploy1-portal-prep/getstarted4.png)

## <a name="overview"></a>Descrição geral

Este artigo aplica-se a versão do Microsoft Azure StorSimple Virtual matriz (também conhecido como o dispositivo de virtual StorSimple no local ou dispositivo virtual StorSimple) em execução Março de 2016 disponibilidade geral (das versões DG). Este é o primeiro artigo na série de tutoriais de implementação necessárias para implementar completamente sua matriz virtual como um servidor de ficheiros ou num servidor de iSCSI. Este artigo descreve a preparação necessária para criar e configurar o seu serviço de Gestor de StorSimple antes de aprovisionamento de uma matriz virtual. Este artigo também se liga saída de uma lista de verificação de configuração de implementação, bem como a configuração de pré-requisitos.

Terá privilégios de administrador para concluir o processo de instalação e configuração. Recomendamos que reveja a lista de verificação de configuração de implementação antes de começar. Preparação portal irá demorar menos de 10 minutos.

As informações publicadas neste artigo aplica-se para a implementação de matrizes Virtual StorSimple no portal clássico Azure, bem como a nuvem do Microsoft Azure administração pública.

### <a name="get-started"></a>Introdução

O fluxo de trabalho de implementação consiste em preparar o portal, aprovisionamento de uma matriz virtual no seu ambiente virtualizado e concluir a configuração. Para começar a com a implementação de matriz Virtual StorSimple como um servidor de ficheiros ou num servidor de iSCSI, terá de referir-se para os seguintes recursos de tabelas (artigos e vídeos).

#### <a name="deployment-articles"></a>Artigos de implementação

Consulte os artigos seguintes na sequência prescrita para implementar a sua matriz de Virtual StorSimple.

| **#** | **Neste passo**                          | **Vai fazer isto...**                                                         | **Utilize estes documentos.**|
|------|-------------------------------------------|--------------------------------------------------------------------------------|------------------------|
|1.   | **Configurar o portal clássico do Azure**       | Criar e configurar o seu serviço de Gestor de StorSimple antes de um dispositivo virtual StorSimple de aprovisionamento.  |[Preparar o portal](storsimple-ova-deploy1-portal-prep.md)|
|2.   | **Aprovisionar a matriz Virtual**           | Para Hyper-V, aprovisionar e ligar a um dispositivo virtual StorSimple num sistema anfitrião a executar o Hyper-V no Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2. <br></br> <br></br> Para VMware, aprovisionar e ligar a um StorSimple no local virtual dispositivo num sistema anfitrião a executar o VMware ESXi 5.5 e acima.<br></br>| [Aprovisionar uma matriz virtual em Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) <br></br> <br></br> [Aprovisionar uma matriz no VMware virtual](storsimple-ova-deploy2-provision-vmware.md)|
|3.    | **Configurar a matriz Virtual**              | Para o seu servidor de ficheiros, executar a configuração inicial, registe-se o servidor de ficheiros StorSimple e concluir a configuração de dispositivo. Em seguida, pode aprovisionar o partilhas SMB. <br></br> <br></br> Para o seu servidor de iSCSI, executar a configuração inicial, registe-se do seu servidor de iSCSI StorSimple e concluir a configuração de dispositivo. Em seguida, pode aprovisionar o iSCSI volumes.| [Configurar o matriz virtual como servidor de ficheiros](storsimple-ova-deploy3-fs-setup.md)<br></br> <br></br>[Configurar a matriz virtual como servidor iSCSI](storsimple-ova-deploy3-iscsi-setup.md)|

#### <a name="deployment-videos"></a>Vídeos de implementação

| **Para efetuar este passo...** |  **Veja este vídeo.**|
|----------------|-------------|
| Instruções passo a passo para começar a utilizar com a matriz Virtual StorSimple. | [Começar a trabalhar com a matriz Virtual StorSimple](https://azure.microsoft.com/documentation/videos/get-started-with-the-storsimple-virtual-array/)|
| Instruções passo a passo para Aprovisionar uma matriz Virtual StorSimple em Hyper-V.|[Criar uma matriz Virtual StorSimple](https://azure.microsoft.com/documentation/videos/create-a-storsimple-virtual-array/) |
|Instruções passo a passo para configurar e registar uma matriz Virtual StorSimple|[Configurar uma matriz Virtual StorSimple](https://azure.microsoft.com/documentation/videos/configure-a-storsimple-virtual-array/)|
|Instruções passo a passo para criar partilhas, Agregar partilhas e restaurar dados de uma matriz de Virtual StorSimple configurado como um servidor de ficheiros|[Utilizar a matriz Virtual StorSimple](https://azure.microsoft.com/documentation/videos/use-the-storsimple-virtual-array/)|
|Instruções passo a passo para recuperação activação e falhas de uma matriz Virtual StorSimple|[Recuperação de falhas StorSimple matriz Virtual](https://azure.microsoft.com/documentation/videos/storsimple-virtual-array-disaster-recovery/)

Agora pode começar a configurar o portal do Azure clássico.

## <a name="configuration-checklist"></a>Lista de verificação de configuração

A lista de verificação de configuração descreve as informações que necessita de reunir antes de configurar o software no seu dispositivo StorSimple. Preparar estas informações antecedência ajuda a simplificar o processo de implementação do dispositivo StorSimple no seu ambiente. Dependendo se o dispositivo virtual StorSimple será implementado como um servidor de ficheiros ou num servidor de iSCSI, terá uma das seguintes listas de verificação.

-   Transfira [StorSimple matriz Virtual ficheiro Server Configuration Checklist](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf).

-   Transferir o [StorSimple Virtual matriz iSCSI Server Configuration Checklist](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf).

## <a name="prerequisites"></a>Pré-requisitos

Aqui irá encontrar os pré-requisitos configuração para o seu serviço de Gestor de StorSimple, dispositivo virtual StorSimple e a rede do Centro de dados.

### <a name="for-the-storsimple-manager-service"></a>Para o serviço do Gestor de StorSimple

Antes de começar, certifique-se de que:

-   Tem a sua conta Microsoft com credenciais de acesso.

-   Tem a sua conta de armazenamento do Microsoft Azure com credenciais de acesso.

-   A sua subscrição do Microsoft Azure deve ser ativada para o serviço do Gestor de StorSimple.

### <a name="for-the-storsimple-virtual-device"></a>Para o dispositivo virtual StorSimple

Antes de implementar um dispositivo virtual, certifique-se de que:

-   Tiver acesso a um sistema anfitrião a executar o Hyper-V no Windows Server 2008 R2 ou posterior ou VMware (ESXi 5,5 ou posterior) que pode ser utilizados para uma disposição um dispositivo.

-   É possível dedicar os seguintes recursos para aprovisionar o seu dispositivo virtual sistema anfitrião:

    -   Um mínimo de 4 núcleos.

    -   Pelo menos, 8 GB de RAM.

    -   Uma interface de rede.

    -   Um disco virtual 500 GB para os dados de sistema.

### <a name="for-the-datacenter-network"></a>Para a rede do Centro de dados

Antes de começar, certifique-se de que:

-   A rede no seu centro de dados está configurada de acordo com os requisitos de rede para o seu dispositivo StorSimple. Para obter mais informações, consulte os [Requisitos de sistema do StorSimple Virtual matriz](storsimple-ova-system-requirements.md).

-   O dispositivo virtual StorSimple tem uma largura de banda da Internet de Mbps dedicada 5 (ou mais) disponível sempre. Esta largura de banda não deve ser partilhada com quaisquer outras aplicações.

## <a name="step-by-step-preparation"></a>Preparação do passo a passo

Utilize as seguintes instruções passo a passo para preparar o seu portal para o serviço do Gestor de StorSimple.

## <a name="step-1-create-a-new-service"></a>Passo 1: Criar um novo serviço

Uma única ocorrência do serviço do Gestor de StorSimple pode gerir vários dispositivos StorSimple 1200. Execute os passos seguintes para criar uma nova instância do serviço do Gestor de StorSimple. Se tiver um serviço de Gestor de StorSimple existente para gerir os dispositivos de 1200, ignorar este passo e aceda a [passo2 para: obter a chave de registo do serviço](#step-2-get-the-service-registration-key).

[AZURE.INCLUDE [storsimple-ova-create-new-service](../../includes/storsimple-ova-create-new-service.md)]

> [AZURE.IMPORTANT]
>
> Se não ativar a criação automática de uma conta de armazenamento com o seu serviço, terá de criar, pelo menos, uma conta de armazenamento depois de ter criado um serviço com êxito.
>

> - Se não tiver criado uma conta de armazenamento automaticamente, aceda a [Configurar uma nova conta de armazenamento do serviço](#optional-step-configure-a-new-storage-account-for-the-service) para obter instruções detalhadas.
>

> - Se ativou a criação automática de uma conta de armazenamento, aceda a [passo 2: obter a chave de registo do serviço](#step-2-get-the-service-registration-key).


## <a name="step-2-get-the-service-registration-key"></a>Passo 2: Obter a chave de registo do serviço


Depois do serviço de Gestor de StorSimple está a trabalhar, terá de obter a chave de registo do serviço. Esta chave é utilizada para registar e ligar o seu dispositivo de StorSimple com o serviço.

Execute os seguintes passos no [portal clássica Azure](https://manage.windowsazure.com/).


[AZURE.INCLUDE [storsimple-ova-get-service-registration-key](../../includes/storsimple-ova-get-service-registration-key.md)]

> [AZURE.NOTE]
>
> A chave de registo do serviço é utilizada para registar todos os dispositivos de Gestor de StorSimple que tem de registar com o seu serviço de Gestor de StorSimple.

## <a name="step-3-download-the-virtual-device-image"></a>Passo 3: Transferir a imagem do dispositivo virtual

Depois de ter a chave de registo do serviço, terá de transferir a imagem de dispositivo virtual apropriado para aprovisionar um dispositivo virtual no seu sistema anfitrião. As imagens de dispositivo virtual são específico de sistema operativo e podem ser transferidas a partir da página de início rápido no portal do Azure clássica.

> [AZURE.IMPORTANT] O software a executar a matriz Virtual StorSimple apenas pode ser utilizado em conjunto com o serviço do Gestor de Storsimple.


Execute os seguintes passos no [portal clássica Azure](https://manage.windowsazure.com/).

#### <a name="to-get-the-virtual-device-image"></a>Para obter a imagem de dispositivo virtual

1.  Na página do **Gestor de StorSimple serviço** , clique no serviço que criou. Isto vai levá-lo para a página de **Início rápido** . (Pode clicar no ícone de guia de introdução do ![](./media/storsimple-ova-deploy1-portal-prep/image8.png) para aceder à página de **Início rápido** em qualquer altura.)

1.  Clique na ligação correspondente para a imagem que pretende transferir a partir do Microsoft Download Center. Os ficheiros de imagem são aproximadamente 4.8 GB.

    -   VHDX para Hyper-V no Windows Server 2012 e versões posteriores

    -   VHD para Hyper-V no Windows Server 2008 R2 e versões posteriores

    -   VMDK para VMWare ESXi 5.5 e versões posteriores

2.  Transfira e deszipe o ficheiro para uma unidade local, efetuar uma nota de onde está localizado o ficheiro deszipado.

![ícone de vídeo](./media/storsimple-ova-deploy1-portal-prep/video_icon.png) **vídeo disponível**

Veja o vídeo para obter instruções passo a passo para começar a trabalhar com a matriz Virtual StorSimple.

> [AZURE.VIDEO get-started-with-the-storsimple-virtual-array]



## <a name="optional-step-configure-a-new-storage-account-for-the-service"></a>Passo opcional: configurar uma nova conta de armazenamento para o serviço

Este passo é opcional que precisa de ser executado apenas se não ativar a criação automática de uma conta de armazenamento com o seu serviço.

Se necessitar de criar uma conta de armazenamento Azure numa região diferente, consulte o artigo [como criar uma conta de armazenamento](storage-create-storage-account.md#create-a-storage-account) para obter instruções passo a passo.

Execute os seguintes passos no [portal clássica Azure](https://manage.windowsazure.com/) na página do Gestor de StorSimple serviço para adicionar uma conta de armazenamento do Microsoft Azure existente.

#### <a name="to-add-a-storage-account"></a>Para adicionar uma conta de armazenamento

1.  O serviço de Gestor de StorSimple página de destino, selecione o seu serviço e faça duplo clique. Isto vai levá-lo para a página de **Início rápido** . Selecione a página de **Configurar** .

2.  Clique em **Add/edit armazenamento conta**. Na caixa de diálogo **Conta de armazenamento Add/Edit** , faça o seguinte:

    1.  Clique em **Adicionar novo**.

    1.  Forneça um nome para a sua conta de armazenamento.

    1.  Fornece a primária **Tecla de acesso** para a sua conta de armazenamento do Microsoft Azure.

    1.  Selecione **Enable SSL modo** para criar um canal seguro para comunicação de rede entre o seu dispositivo e na nuvem. Desmarque a caixa de verificação **Ativar SSL modo** apenas se estiver a funcionar dentro de uma nuvem privada.

    1.  Clique no ícone de verificação ![](./media/storsimple-ova-deploy1-portal-prep/image7.png). Será notificado depois da conta de armazenamento é criada com êxito.

        ![](./media/storsimple-ova-deploy1-portal-prep/image11.png)

1.  A conta de armazenamento recentemente criado será apresentada na página **Configurar** em **contas de armazenamento**. Clique em **Guardar** para guardar a conta de armazenamento recentemente criado. Clique em **OK** quando lhe for pedido para confirmar.


## <a name="next-step"></a>Passo seguinte

O passo seguinte é uma máquina virtual para o seu dispositivo virtual StorSimple de aprovisionamento. Dependendo do seu sistema operativo anfitrião, consulte as instruções detalhadas em:

-   [Aprovisionar uma matriz Virtual StorSimple no Hyper-V](storsimple-ova-deploy2-provision-hyperv.md)

-   [Aprovisionar uma matriz Virtual StorSimple no VMware](storsimple-ova-deploy2-provision-vmware.md)
