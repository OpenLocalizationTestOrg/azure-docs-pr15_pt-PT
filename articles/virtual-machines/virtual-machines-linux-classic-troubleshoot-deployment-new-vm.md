<properties
   pageTitle="Resolver problemas de Linux VM implementação-clássica | Microsoft Azure"
   description="Resolução de problemas de implementação clássica quando cria uma nova máquina de virtual Linux no Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="JiangChen79"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>

<tags
  ms.service="virtual-machines-linux"
  ms.workload="na"
  ms.tgt_pltfrm="vm-linux"
  ms.devlang="na"
  ms.topic="article"
  ms.date="09/06/2016"
  ms.author="cjiang"/>

# <a name="troubleshoot-classic-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>Resolução de problemas de implementação clássica com a criação de uma nova máquina de virtual Linux no Azure

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-selectors](../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-selectors-include.md)]

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Os registos de auditoria recolher

Para começar a resolução de problemas, recolha registos de auditoria para identificar o erro associado com o problema.

No portal do Azure, clique em **Procurar** > **máquinas virtuais** > *máquina virtual Windows* > **Definições** > **registos de auditoria**.

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[AZURE.INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y:** Se o SO está Linux generalizada e -é carregado e/ou capturado com a definição de GRG, em seguida, não haverá erros. Da mesma forma, se for o SO Linux especializado e -é carregado e/ou capturado com a definição de especializadas, em seguida, não haverá erros.

**Carregar erros de início:**

**N<sup>1</sup>:** Se o SO está Linux generalizada e é carregá-lo como especializado, irá obter um erro de limite de tempo aprovisionamento porque a VM está retida na fase de aprovisionamento.

**N<sup>2</sup>:** Se o sistema operativo é Linux especializado e ser carregado como generalized, obterá um erro de falha de aprovisionamento porque a nova VM está em execução com o nome do computador, nome de utilizador e palavra-passe original.

**Resolução:**

Para resolver estes erros ambas as, carregue o VHD original, disponível no local, com a mesma definição como que para o sistema operativo (generalized/especializado). Para carregar como generalized, se lembrar de executar - deprovision pela primeira vez. Para mais informações, consulte [criar e carregar um disco rígido Virtual que contém o sistema operativo Linux](virtual-machines-linux-classic-create-upload-vhd.md) .

**Capture erros:**

**N<sup>3</sup>:** Se o SO for Linux generalizada e a mesma é capturada como especializado, irá obter um erro de limite de tempo aprovisionamento porque a VM original não é utilizável tal como está marcado como generalized.

**N<sup>4</sup>:** Se o sistema operativo é Linux especializado e é capturado como generalized, obterá um erro de falha de aprovisionamento porque a nova VM está em execução com o nome do computador, nome de utilizador e palavra-passe original. Além disso, o original VM não está utilizável porque está marcado como especializado.

**Resolução:**

Para resolver estes erros ambas as, elimine a imagem atual a partir do portal e [volte a capturá-lo a partir de VHDs atuais](virtual-machines-linux-classic-capture-image.md) com a mesma definição como que para o sistema operativo (generalized/especializado).

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Problema: Personalizada / Galeria / imagem marketplace; Falha de alocação
Este erro surge nas situações, quando o novo pedido VM é enviado para um cluster de que não tem espaço livre disponível para acomodar o pedido, ou que não consegue suportar o tamanho da memória virtual solicitado. Não é possível misturar a série diferente de VMs no serviço de nuvem do mesmo. Por isso, se pretende criar uma nova VM de um tamanho diferente o que pode suportar o seu serviço de nuvem, o pedido de cluster irá falhar.

Consoante as restrições do serviço na nuvem que utilizar para criar a nova VM, poderá encontrar um erro causado por uma das duas situações.

**Causar 1:** O serviço de nuvem é afixado a um cluster específico ou está ligada a um grupo de afinidade e, por conseguinte, afixado para um cluster específico por predefinição. Por isso, novo recurso de cluster pedidos nesse grupo afinidade são tentou no mesmo cluster onde estão alojados os recursos existentes. No entanto, mesmo cluster pode ou não suporta o tamanho da memória virtual pedido ou tem espaço suficiente disponível, que resulta num erro de alocação. Esta situação for verdadeira se os novos recursos são criados através de um serviço de nuvem novo ou através de um serviço de nuvem existente.

**Resolução 1:**

- Criar um novo serviço de nuvem e associá-la numa região ou uma rede virtual com base em região.
- Crie uma nova VM no novo serviço em nuvem.
  Se obtiver um erro ao tentar criar um novo serviço de nuvem, volte a tentar tarde ou alterar a região para o serviço de nuvem.

> [AZURE.IMPORTANT] Se estava a tentar criar uma nova VM num serviço na nuvem existente, mas não foi e tinha que criar um novo serviço de nuvem para o novo VM, pode optar por consolidar todos os seus VMs no serviço de nuvem do mesmo. Para fazê-lo, eliminar VMs no serviço de nuvem existente e volte a capturar partir seus discos no novo serviço em nuvem. No entanto, é importante lembrar-se de que o novo serviço de nuvem terá um novo nome e VIP, pelo que necessita de actualizar estas para todas as dependências atualmente utilizam estas informações para o serviço de nuvem existente.

**Causar 2:** O serviço de nuvem está associado uma rede virtual que está ligada a um grupo de afinidade, para que é afixada para um cluster específico por predefinição. Todos os recursos de cluster de novos pedidos de nesse grupo afinidade, portanto, são tentou no mesmo cluster onde estão alojados os recursos existentes. No entanto, mesmo cluster pode ou não suporta o tamanho da memória virtual pedido ou tem espaço suficiente disponível, que resulta num erro de alocação. Esta situação for verdadeira se os novos recursos são criados através de um serviço de nuvem novo ou através de um serviço de nuvem existente.

**Solução 2:**

- Crie uma nova rede virtual regional.
- Crie a nova VM a nova rede virtual.
- [Ligar a rede virtual existente](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/) para a nova rede virtual. Obtenha mais informações sobre [as redes virtuais regionais](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/). Em alternativa, pode [migrar a sua rede virtual afinidade grupo são baseados a uma rede virtual regional](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/)e, em seguida, criar a nova VM.

## <a name="next-steps"></a>Próximos passos
Se encontrar problemas quando inicia uma VM Linux parado ou redimensionar um VM Linux existente no Azure, consulte o artigo [resolução de problemas de implementação clássica com reiniciar ou redimensionar uma máquina de Virtual Linux existente no Azure](virtual-machines-linux-classic-restart-resize-error-troubleshooting.md).
