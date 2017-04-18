<properties 
   pageTitle="Aceleração de funcionamento em rede para uma máquina de virtual - Portal | Microsoft Azure"
   description="Saiba como configurar acelerada de rede para uma máquina virtual Azure utilizando o Portal do Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/26/2016"
   ms.author="jdial" />

# <a name="accelerated-networking-for-a-virtual-machine"></a>Redes acelerada para uma máquina virtual

> [AZURE.SELECTOR]
- [Portal do Azure](virtual-network-accelerated-networking-portal.md)
- [PowerShell](virtual-network-accelerated-networking-powershell.md)

Redes acelerada permite única raiz e/s virtualização (SR IOV) para uma máquina de virtual (VM), significativamente a melhorar o desempenho de rede. Este caminho de alto desempenho ignora o anfitrião do datapath reduzir a latência, interferências de duração dos e a utilização da CPU para utilização com as mais excessiva rede das cargas de trabalho no tipos VM suportados. Este artigo explica como utilizar o Portal do Azure para configurar acelerada à rede no modelo de implementação de Gestor de recursos do Azure. Também pode criar uma VM com acelerada de funcionamento em rede através do Azure PowerShell. Para saber como, clique na caixa de PowerShell na parte superior deste artigo.

A imagem seguinte mostra a comunicação entre as duas máquinas virtuais (VM) com e sem acelerada de rede:

![Comparação](./media/virtual-network-accelerated-networking-portal/image1.png)

Sem acelerada funcionamento em rede, todo o tráfego de rede e terminar a VM tem percorrer o anfitrião e o parâmetro virtual. O parâmetro virtual fornece todas as imposição de política, tal como grupos de segurança de rede, listas de controlo de acesso, isolamento e outros serviços de rede foi virtualizado para o tráfego de rede. Para saber mais, leia o artigo [Hyper-V rede Virtualização e mudar Virtual](https://technet.microsoft.com/library/jj945275.aspx) .

Com acelerada de rede, o tráfego de rede chega ao cartão de rede (NIC) e, em seguida, é encaminhado para a VM. Todas as políticas de rede que o parâmetro virtual aplica-se sem acelerada de rede são descarregadas e aplicadas em hardware. Aplicar a política de hardware permite NIC reencaminhe o tráfego de rede diretamente para a VM, ignore o anfitrião e o parâmetro virtual, enquanto mantém todos os a política-aplicada no anfitrião do.

Os benefícios da acelerada de rede só se aplicam a VM que está ativado no. Para obter melhores resultados, é ideal ativar esta funcionalidade no VMs, pelo menos, duas ligado para a mesma VNet. Quando comunicar ao longo do VNets ou conexão no local, esta funcionalidade tem um impacto mínimo para latência geral.

[AZURE.INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

##<a name="benefits"></a>Benefícios

- **Inferior latência / superiores pacotes por segundo (pps):** Remover o parâmetro virtual da datapath remove o tempo gastam pacotes no anfitrião do para processamento de política e aumenta o número de pacotes que podem ser processadas dentro da VM.
- **Reduzido interferências de duração dos:** Processamento de parâmetro virtual depende da quantidade de política de que precisa de ser aplicada e a carga de trabalho da CPU que está a fazer o processamento. Descarregar a imposição de política do hardware remove esse variabilidade por entregar pacotes diretamente para a VM, removendo o anfitrião para comunicação VM e todas as interrupções de software e parâmetros de contexto.
- **a utilização da CPU diminuiu:** Ignorar o parâmetro virtual no anfitrião do orienta para menos a utilização da CPU para processamento de tráfego de rede.

## <a name="limitations"></a>Limitações

Existem as seguintes limitações ao utilizar esta funcionalidade:
 
- **Criação de interface de rede:** Só pode ser ativado acelerado funcionamento em rede para uma nova interface de rede.  Não pode ser ativado numa interface de rede existente.
- **Criação VM:** Só pode ser anexada uma interface de rede com a rede acelerada ativada para uma VM quando a VM é criada. Não pode ser anexada a interface de rede para um VM existente.
- **Regiões:** Oferecida no apenas as regiões oeste Central-nos e oeste Europe Azure. O conjunto de regiões irá expandir no futuro.
- **Sistema operativo de suportados:** Microsoft Windows Server 2012 R2 e Windows Server 2016 pré-visualização técnica 5. Suporte de Linux e Windows Server 2012 será adicionado mais rapidamente.
- **Tamanho da memória virtual:** Standard_D15_v2 e Standard_DS15_v2 são suportados apenas tamanhos de instância VM. Para mais informações, consulte o artigo de [tamanhos de VM do Windows](../virtual-machines/virtual-machines-windows-sizes.md) . O conjunto de tamanhos de instância VM suportados irá expandir no futuro.

Alterações para estas limitações vai ser anunciadas através da página de [Rede Virtual do Azure atualizações](https://azure.microsoft.com/updates/accelerated-networking-in-preview) .

## <a name="create-a-windows-vm-with-accelerated-networking"></a>Criar um VM do Windows com a rede acelerada

1. Registe-se para a pré-visualização ao enviar uma mensagem de e-mail para [Acelerada subscrições de rede](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) com o seu ID da subscrição e utilização pretendida. Não conclua os passos restantes até depois de receber um e-mail a notificar lhe tenha sido aceites para a pré-visualização.
2. Inicie sessão no Portal do Azure em http://portal.azure.com.
3. Crie uma VM completando os passos no artigo [criar uma VM Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md) selecionar as seguintes opções:
    - Selecione um sistema operativo listado na secção limitações deste artigo.
    - Selecione uma localização (região) listada na secção limitações deste artigo.
    - Selecione um tamanho da memória virtual listado na secção limitações deste artigo. Se um dos tamanhos suportados não estiver listado, clique em **Ver tudo** no pá a **Escolher um tamanho** para selecionar um tamanho de uma lista expandida.
    - Na pá **Definições** , clique em *ativado* para **Accelerated sociais**, tal como apresentado na seguinte imagem:

        ![Definições](./media/virtual-network-accelerated-networking-portal/image3.png)

    >[AZURE.NOTE] A opção acelerada de rede só serão visível se tiver:
    >
    >- Aceite para a pré-visualização
    >- Selecionada sistema operativo suportado, a localização e tamanhos VM mencionados na secção limitações deste artigo.

5. Quando a VM estiver criada, transfira o [controlador acelerada de rede](https://gallery.technet.microsoft.com/Azure-Accelerated-471b5d84), ligar-se e iniciar sessão na VM e executar o instalador do controlador dentro da VM.
6. Botão direito do rato no botão do Windows e clique em **Gestor de dispositivos**. Verifique se o **Mellanox ConnectX-3 Virtual função Ethernet adaptador** aparece na opção de **rede** quando expandido, conforme apresentado na seguinte imagem:

    ![Gestor de dispositivos](./media/virtual-network-accelerated-networking-portal/image2.png)