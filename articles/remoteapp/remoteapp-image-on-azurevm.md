<properties
    pageTitle="Criar uma imagem de RemoteApp do Azure com base numa VM Azure | Microsoft Azure"
    description="Saiba como criar uma imagem para RemoteApp do Azure ao começar com uma máquina virtual Azure."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# <a name="create-a-azure-remoteapp-image-based-on-an-azure-virtual-machine"></a>Criar uma imagem de RemoteApp do Azure com base numa máquina virtual Azure

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Pode criar RemoteApp do Azure imagens (que mantenha as aplicações que partilha na sua coleção de) a partir de uma máquina virtual Azure. Também pode optar por utilizar uma imagem de máquina virtual que adicionámos na Galeria de imagem do Azure VM que cumpre todos os requisitos de imagem RemoteApp do Azure - pode utilizar essa imagem VM como ponto de partida para o seu próprio VM, se pretender. Procure a imagem de "Windows servidor remoto ambiente de trabalho anfitrião da sessão" na biblioteca.

Existem dois passos para criar a sua própria imagem com base numa VM Azure - criar a imagem e, em seguida, carregue-o partir da biblioteca de Azure VM para RemoteApp do Azure.

## <a name="create-a-custom-image-based-on-an-azure-vm"></a>Criar uma imagem personalizada com base numa VM Azure

Utilize estes passos para criar uma imagem com base numa VM Azure.

1. Crie uma máquina de virtual Azure. Pode utilizar o "Windows servidor remoto ambiente de trabalho anfitrião da sessão" ou "Windows servidor remoto ambiente de trabalho sessão anfitrião com o Microsoft Office 365 ProPlus" imagem a partir da Galeria de imagem do Azure máquina virtual. Esta imagem cumpre todos os RemoteApp do Azure modelo imagem requisitos.

    Para obter detalhes, consulte o artigo [criar uma VM a executar o Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md).

2. Ligar para a VM e instalar e configurar as aplicações que pretende partilhar através de RemoteApp. Certifique-se efetuar qualquer configurações adicionais do Windows, uma ferramenta necessária às suas aplicações.

    Para obter detalhes, consulte o artigo [como iniciar sessão numa máquina Virtual a executar o Windows Server](../virtual-machines/virtual-machines-windows-classic-connect-logon.md).

3. Se estiver a utilizar uma das imagens do anfitrião de sessões de ambiente de trabalho remoto do Windows Server, existe um script de validação incluído que irá garantir que a VM satisfaz a pré-reqs. RemoteApp Para executar o script, faça duplo clique **ValidateRemoteAppImage** no ambiente de trabalho. Certifique-se de que todos os erros comunicados pelo script estão corrigidos antes de prosseguir para o passo seguinte.

4. SYSPREP generalizar e capturar a imagem. Veja [como capturar uma máquina de Virtual do Windows para utilizar como um modelo](../virtual-machines/virtual-machines-windows-classic-capture-image.md) para obter instruções.



## <a name="import-the-image-into-the-azure-remoteapp-image-library"></a>Importar a imagem para a biblioteca de imagens RemoteApp do Azure

Utilize estes passos para importar a nova imagem para o Azure RemoteApp:

1. No separador **Imagens de modelos** :
    - Se tiver sem imagens existentes, clique em **carregar ou importar uma imagem do modelo**.
    - Se já tiver, pelo menos, uma imagem, clique em **+** para adicionar uma nova imagem.

2. Selecione **Importar uma imagem a partir do seu máquinas virtuais** biblioteca e, em seguida, clique em **seguinte**.

3. Na página seguinte, selecione a imagem personalizada a partir da lista e confirme que seguiu os passos indicados quando criou a sua imagem. Clique em **seguinte**.
4. Introduza um nome para a nova imagem RemoteApp e escolha a localização e, em seguida, clique em marca de verificação para iniciar o processo de importação.

> [AZURE.NOTE] Pode importar imagens a partir de qualquer localização Azure suportada pelo máquinas virtuais do Azure para qualquer localização Azure suportada pelo RemoteApp do Azure. A importação pode demorar até 25 minutos, dependendo das localizações.

Agora, está pronto para criar a nova coleção, quer uma colecção de [nuvem](remoteapp-create-cloud-deployment.md) ou [híbrido](remoteapp-create-hybrid-deployment.md), consoante as suas necessidades.
