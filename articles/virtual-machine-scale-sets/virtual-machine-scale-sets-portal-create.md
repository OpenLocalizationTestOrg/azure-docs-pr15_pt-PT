<properties
    pageTitle="Criar um conjunto de escala de Máquina Virtual através do portal Azure | Microsoft Azure"
    description="Implemente conjuntos de escala através do portal Azure."
    keywords="conjuntos de escala de máquina virtual" 
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gatneil"
    manager="madhana"
    editor="tysonn"
    tags="azure-resource-manager" />

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/15/2016"
    ms.author="gatneil"/>

# <a name="create-a-virtual-machine-scale-set-using-the-azure-portal"></a>Criar um conjunto de escala de Máquina Virtual através do portal Azure

Este tutorial mostra-lhe como é fácil criar um conjunto de escala de Máquina Virtual em apenas alguns minutos, utilizando o portal do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="choose-the-vm-image-from-the-marketplace"></a>Selecione a imagem VM a partir de mercado

A partir do portal, pode facilmente implementar uma escala de configurar com CentOS, CoreOS, Debian, Suse abrir, Red chapéu Enterprise Linux, SUSE Linux Enterprise Server, Ubuntu servidor ou imagens do Windows Server.

Em primeiro lugar, navegue para o [Azure portal](https://portal.azure.com) num browser. Clique em `New`, procure `scale set`e, em seguida, selecione o `Virtual machine scale set` entrada:

![ScaleSetPortalOverview](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalOverview.PNG)

## <a name="create-the-linux-virtual-machine"></a>Criar a máquina de virtual Linux

Agora pode utilizar as predefinições e criar rapidamente a máquina virtual.

* No `Basics` pá, introduza um nome para o conjunto de escala. Este nome torna-se a base do FQDN do Balanceador de carga à frente do conjunto de escala, por isso, certifique-se de que o nome é exclusivo ao longo das Azure.

* Selecione o seu SO pretendido escreva, introduza o seu nome de utilizador pretendida e selecione o tipo de autenticação que prefere. Se optar por uma palavra-passe, tem de ser, pelo menos, 12 caracteres longo e reunir três terminar os seguintes requisitos de complexidade quatro: caráter um minúsculas, um maiúsculas caráter, um número e um caráter especial. Obtenha mais informações sobre [os requisitos de nome de utilizador e palavra-passe](../virtual-machines/virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm). Se optar por `SSH public key`, ser-se de que apenas colar na sua chave pública, não a chave privada:

![ScaleSetPortalBasics](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalBasics.PNG)

* Introduza o seu nome de grupo do recurso pretendido e a localização e, em seguida, clique em `OK`.

* No `Virtual machine scale set service settings` pá: introduza a etiqueta de nome de domínio pretendida (a base do FQDN do Balanceador de carga à frente do conjunto de escala). Esta etiqueta tem de ser exclusiva ao longo das Azure.

* Selecione a sua imagem de disco do sistema operativo pretendido, contar instância e o tamanho de máquina.

* Activar ou desactivar autoscale e configurar se activado:

![ScaleSetPortalService](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalService.PNG)

* No `Summary` pá, quando validação estiver concluída, clique em `OK`.

* Por fim, na `Purchase` pá, clique em `Purchase` para iniciar a escala defina implementação.

## <a name="connect-to-a-vm-in-the-scale-set"></a>Ligar a uma VM no conjunto de escala

Assim que é implementado seu conjunto de escala, navegue para a `Inbound NAT Rules` separador do Balanceador de carga para o conjunto de escala:

![ScaleSetPortalNatRules](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalNatRules.PNG)

Pode ligar para cada VM na escala do conjunto utilizando estas regras NAT. Por exemplo, para um conjunto de escala do Windows, se existir uma regra NAT no porta recebida 50000, pode ligar para essa máquina através do RDP no `<load-balancer-ip-address>:50000`. Para um conjunto de escala Linux, teria de ligar utilizando o comando `ssh -p 50000 <username>@<load-balancer-ip-address>`.

## <a name="next-steps"></a>Próximos passos

Para a documentação sobre como implementar conjuntos de escala a partir do clip, consulte [a documentação](./virtual-machine-scale-sets-cli-quick-create.md).

Para a documentação sobre como implementar conjuntos de escala a partir do PowerShell, consulte [a documentação](./virtual-machine-scale-sets-windows-create.md).

Para obter documentação sobre como implementar conjuntos de escala do Visual Studio, consulte [a documentação](./virtual-machine-scale-sets-vs-create.md).

Para obter documentação geral, consulte o artigo [conjuntos de página de descrição geral de documentação para escala](./virtual-machine-scale-sets-overview.md).

Para obter informações gerais, consulte o artigo a [página de destino principal para conjuntos de escala](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

