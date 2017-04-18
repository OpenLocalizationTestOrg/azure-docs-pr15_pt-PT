<properties
    pageTitle="Capturar uma VM Linux para utilizar como um modelo | Microsoft Azure"
    description="Saiba como capturar e generalizar uma imagem de uma baseado em Linux Azure máquina virtual (VM) criada com o modelo de implementação do Azure o Gestor de recursos."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="iainfou"/>


# <a name="capture-a-linux-virtual-machine-running-on-azure"></a>Capturar uma máquina de virtual Linux em execução no Azure

Siga os passos neste artigo para generalizar e capturar Azure Linux máquina virtual (VM) no modelo de implementação de Gestor de recursos. Quando generalizar a VM, remover informações pessoais da conta e preparar a VM para ser utilizado como uma imagem. Em seguida, capturar uma imagem de GRG virtual no disco rígido (VHD) para o sistema operativo, VHDs para discos dados anexados e um [modelo de Gestor de recursos](../azure-resource-manager/resource-group-overview.md) para implementações do novas VM. 

Para criar VMs utilizando a imagem, configurar recursos de rede para cada novo VM e utilizar o modelo (um ficheiro JavaScript Object Notation ou JSON,) para implementá-lo das imagens VHD capturadas. Desta forma, pode criar uma réplica uma VM com a sua configuração de software atual, semelhante à forma como utilizar imagens no Azure Marketplace.

>[AZURE.TIP]Se pretender criar uma cópia da sua VM Linux existente com o seu estado especializado para cópia de segurança ou depurar, consulte o artigo [criar uma cópia de uma máquina de virtual Linux em execução no Azure](virtual-machines-linux-copy-vm.md). E se pretende carregar um VHD Linux a partir de um VM no local, consulte o artigo [carregar e criar uma VM Linux da imagem do disco personalizada](virtual-machines-linux-upload-vhd.md).  

## <a name="before-you-begin"></a>Antes de começar

Certifique-se de que cumpre os pré-requisitos seguintes:

* **Azure VM criados no modelo de implementação de Gestor de recursos** - se não tiver criado uma VM Linux, pode utilizar o [portal](virtual-machines-linux-quick-create-portal.md), o [Clip do Azure](virtual-machines-linux-quick-create-cli.md)ou os [modelos de Gestor de recursos](virtual-machines-linux-cli-deploy-templates.md). 

    Configure a VM conforme necessário. Por exemplo, [Adicionar dados discos](virtual-machines-linux-add-disk.md), aplicar atualizações e instale aplicações. 
* **Clip do azure** - instalar o [Clip do Azure](../xplat-cli-install.md) num computador local.

## <a name="step-1-remove-the-azure-linux-agent"></a>Passo 1: Remover o agente Azure Linux

Em primeiro lugar, execute o comando **waagent** com o parâmetro **deprovision** na VM Linux. Este comando elimina ficheiros e dados para tornar a VM está preparado para generalizar. Para obter mais detalhes, consulte o [Manual do utilizador do agente de Linux Azure](virtual-machines-linux-agent-user-guide.md).

1. Ligar a sua VM Linux utilizando um cliente de SSH.

2. Na janela do SSH, escreva o seguinte comando:

    ```
    sudo waagent -deprovision+user
    ```
    >[AZURE.NOTE] Execute apenas este comando numa VM que pretende capturar como uma imagem. Não garante que a imagem está desmarcada todas as informações confidenciais ou adequado para redistribuição.

3. Escreva **y** para continuar. Pode adicionar a **-Forçar** parâmetro para evitar este passo de confirmação.

4. Após completa o comando, escreva **Sair**. Este passo fecha o cliente SSH.

    
## <a name="step-2-capture-the-vm"></a>Passo 2: Capturar a VM

Utilize o clip do Azure para generalizar e capturar a VM. Nos exemplos seguintes, substitua os nomes dos parâmetros de exemplo com os seus próprios valores. Os nomes dos parâmetros de exemplo incluem **myResourceGroup**, **myVnet**e **myVM**.

5. A partir do seu computador local, abra o clip do Azure e o [início de sessão à sua subscrição do Azure](../xplat-cli-connect.md). 

6. Certifique-se de que está no modo de Gestor de recursos.

    ```
    azure config mode arm
    ```

7. Encerre a VM que já de descontinuada utilizando o seguinte comando:

    ```
    azure vm deallocate -g MyResourceGroup -n myVM
    ```

8. Generalize VM com o seguinte comando:

    ```
    azure vm generalize -g MyResourceGroup -n myVM
    ```

9. Agora execute o comando **azure vm capturar** , que a VM para capturar. No seguinte exemplo, os imagem VHDs são capturados com nomes começados por **MyVHDNamePrefix**e a opção **-t** Especifica um caminho para o modelo **MyTemplate.json**. 

    ```
    azure vm capture MyResourceGroup MyResourceGroup MyVHDNamePrefix -t MyTemplate.json
    ```

    >[AZURE.IMPORTANT]Os ficheiros de VHD imagem são criados por predefinição na mesma conta de armazenamento a VM original utilizada. Utilize a *mesma conta de armazenamento* para armazenar os VHDs para qualquer novos VMs cria a partir da imagem. 

6. Para encontrar a localização de uma imagem capturada, abra o modelo JSON num editor de texto. Na **storageProfile**, localize o **uri** da **imagem** localizados no contentor de **sistema** . Por exemplo, o URI da imagem do disco sistema operativo é semelhante a`https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Passo 3: Criar uma VM a partir da imagem capturada
Utilize agora a imagem com um modelo para criar uma VM Linux. Estes passos mostram-lhe como utilizar o clip do Azure e o modelo de ficheiro JSON capturado para criar a VM numa nova rede virtual.

### <a name="create-network-resources"></a>Criar recursos de rede

Para utilizar o modelo, primeiro tem de configurar um rede virtual e NIC para a nova VM. Recomendamos que criar um grupo de recursos para estes recursos na localização onde a sua imagem VM está armazenada. Executar comandos semelhante aos nomes dos seguintes procedimentos, substituindo para os recursos e uma localização apropriada Azure ("centralus" nestes comandos):

    azure group create MyResourceGroup1 -l "centralus"

    azure network vnet create MyResourceGroup1 myVnet -l "centralus"

    azure network vnet subnet create MyResourceGroup1 myVnet mySubnet

    azure network public-ip create MyResourceGroup1 myIP -l "centralus"

    azure network nic create MyResourceGroup1 myNIC -k mySubnet -m myVnet -p myIP -l "centralus"

### <a name="get-the-id-of-the-nic"></a>Obter o Id da NIC

Para implementar uma VM da imagem utilizando JSON que guardou durante a captura, terá do Id do NIC. Obtê-lo executando o seguinte comando:

    azure network nic show MyResourceGroup1 myNIC

O **Id** na saída é semelhante a`/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic`



### <a name="create-a-vm"></a>Criar uma VM
Agora, execute o seguinte comando para criar o seu VM a partir de imagem capturada VM. Utilize o parâmetro **-f** para especificar o caminho para o ficheiro JSON modelo que guardou.

    azure group deployment create MyResourceGroup1 MyDeployment -f MyTemplate.json

Nos resultados do comando, lhe for pedido para fornecer um novo nome VM, o nome de utilizador de administração e palavra-passe e o Id da imagem que criou anteriormente.

    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    vmName: myNewVM
    adminUserName: myAdminuser
    adminPassword: ********
    networkInterfaceId: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resource Groups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic

O exemplo seguinte mostra o que vê para uma implementação com êxito:

    + A inicialização configurações do modelo e parâmetros
    + Criar uma informações de implementação: criado xxxxxxx de implementação do modelo
    + Aguardar implementação concluir os dados: DeploymentName: MyDeployment dados: ResourceGroupName: MyResourceGroup1 dados: ProvisioningState: foi concluída com êxito dados: carimbo: xxxxxxx dados: modo: dados utilizarão: valor nome do tipo


    data:    ------------------  ------------  -------------------------------------

    dados: vmName cadeia myNewVM


    dados: vmSize Standard_D1 cadeia


    dados: adminUserName cadeia myAdminuser


    dados: adminPassword SecureString não definido


    dados: networkInterfaceId informações de /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic cadeia: implementação de grupo criar comando OK

### <a name="verify-the-deployment"></a>Verificar a implementação

Agora SSH à máquina virtual que criou para verificar a implementação e começar a utilizar a nova VM. Para ligar via SSH, localize o endereço IP do VM que criou, executando o seguinte comando:

    azure network public-ip show MyResourceGroup1 myIP

O endereço IP público estiver listado nos resultados do comando. Por predefinição ligar para a VM Linux por SSH no Porta 22.

## <a name="create-additional-vms"></a>Criar VMs adicionais
Utilize a imagem capturada e o modelo para implementar VMs adicionais utilizando os passos descritos na secção anterior. Outras opções para criar VMs da imagem de incluem através de um modelo de guia de introdução ou a executar o comando **Criar azure vm** .

### <a name="use-the-captured-template"></a>Utilizar o modelo capturado

Para utilizar a imagem capturada e o modelo, siga estes passos (detalhados na secção anterior):

* Certifique-se de que a sua imagem VM está na mesma conta de armazenamento que aloja a VM VHD.
* Copie o ficheiro de JSON modelo e especificar um nome exclusivo para o disco de sistema operativo da nova VM VHD (ou VHDs). Por exemplo, na **storageProfile**, em **vhd**, na **uri**, especifique um nome exclusivo para **osDisk** VHD, semelhante ao`https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/MyNewVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`
* Crie uma NIC na mesma ou outra rede virtual.
* Utilizar o ficheiro JSON modelo modificado, crie uma implementação no grupo de recursos, na qual pode configura a rede virtual.

### <a name="use-a-quickstart-template"></a>Utilizar um modelo de guia de introdução

Se pretender que a rede configurar automaticamente quando cria uma VM da imagem, pode especificar os recursos num modelo. Por exemplo, consulte o artigo o [modelo de vm de 101 da imagem utilizador](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) a partir do GitHub. Este modelo cria uma VM a partir de uma imagem personalizada e de rede virtual necessárias, endereço IP público e recursos NIC. Para obter instruções sobre utilizar o modelo no portal do Azure, consulte o artigo [como criar uma máquina de virtual a partir de uma imagem personalizada utilizando um modelo de Gestor de recursos](http://codeisahighway.com/how-to-create-a-virtual-machine-from-a-custom-image-using-an-arm-template/).

### <a name="use-the-azure-vm-create-command"></a>Utilizar a vm azure criar comando

Normalmente, é mais fácil utilizar um modelo de Gestor de recursos para criar uma VM a partir da imagem. No entanto, pode criar a VM _imperatively_ utilizando o comando **Criar azure vm** com o **-Q** (**– imagem urn**) parâmetro. Se utilizar este método, também ser efetuada com o parâmetro **-d** (**vhd – SO-disco**) para especificar a localização do ficheiro. vhd SO para a nova VM. Este ficheiro tem de ser no contentor vhds da conta de armazenamento a localização onde armazenou o ficheiro de VHD de imagem. O comando copia o VHD para a nova VM automaticamente para o contentor **vhds** .

Antes de executar o **azure vm criar** com a imagem, conclua os passos seguintes:

1.  Criar um grupo de recursos ou identificar um grupo de recursos existente para a implementação.

2.  Crie um recurso de endereço IP público e um recurso de imagem para a nova VM. Para obter os passos criar uma rede virtual, endereço IP público e NIC utilizando o clip, consulte o artigo neste artigo. (**azure vm criar** também pode criar uma imagem, mas é necessário transmitir parâmetros adicionais para uma rede virtual e sub-rede.)


Em seguida, execute um comando que transmite URIs para o novo ficheiro SO VHD e a imagem existente. Neste exemplo, um tamanho de Standard_A1 VM é criada na região Leste dos EUA.

    azure vm create MyResourceGroup1 myNewVM eastus Linux -d "https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/MyNewVHDNamePrefix.vhd" -Q "https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd" -z Standard_A1 -u myAdminname -p myPassword -f myNIC

Para obter opções adicionais de comando, execute `azure help vm create`.

## <a name="next-steps"></a>Próximos passos

Para gerir o seu VMs com o clip, consulte as tarefas no [Implementar e gerir máquinas virtuais através da utilização de modelos de Gestor de recursos do Azure e o clip do Azure](virtual-machines-linux-cli-deploy-templates.md).
