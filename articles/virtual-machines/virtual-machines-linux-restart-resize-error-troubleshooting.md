<properties
   pageTitle="VM reiniciar ou redimensionar problemas | Microsoft Azure"
   description="Resolver problemas de implementação do Gestor de recursos com reiniciar ou redimensionar uma máquina de Virtual Linux existente no Azure"
   services="virtual-machines-linux, azure-resource-manager"
   documentationCenter=""
   authors="Deland-Han"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>

<tags
   ms.service="virtual-machines-linux"
   ms.topic="support-article"
   ms.tgt_pltfrm="vm-linux"
   ms.devlang="na"
   ms.workload="required"
   ms.date="09/09/2016"
   ms.author="delhan"/>

# <a name="troubleshoot-resource-manager-deployment-issues-with-restarting-or-resizing-an-existing-linux-virtual-machine-in-azure"></a>Resolver problemas de implementação do Gestor de recursos com reiniciar ou redimensionar uma máquina de Virtual Linux existente no Azure

Quando tentar iniciar uma Máquina Virtual do Azure parado (VM) ou redimensionar um VM Azure existente, o erro comum que encontrar é uma falha de alocação. Este erro resultados quando o cluster ou a região não tem recursos disponíveis ou quando não consegue suportar o tamanho da memória virtual pedido.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Os registos de auditoria recolher

Para começar a resolução de problemas, recolha registos de auditoria para identificar o erro associado com o problema. As ligações seguintes contêm informações detalhadas sobre o processo de:

[Resolução de problemas de implementações do grupo de recursos com o Portal do Azure](../resource-manager-troubleshoot-deployments-portal.md)

[Operações de auditoria com o Gestor de recursos](../resource-group-audit.md)

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problema: Erro ao iniciar uma VM parada

Tente iniciar uma VM parada mas recebe uma falha de alocação.

### <a name="cause"></a>Causa

O pedido para iniciar a VM parada tem de ser tentada na cluster original que aloja o serviço de nuvem. No entanto, o cluster não ter espaço livre disponível para cumprir o pedido.

### <a name="resolution"></a>Resolução

*   Parar de todos os VMs no conjunto de disponibilidade e, em seguida, reinicie cada VM.

  1. Clique em **grupos de recursos** > _seu grupo de recursos_ > **recursos** > _Configurar a sua disponibilidade_ > **máquinas virtuais** > _máquina virtual_ > **Parar**.

  2. Depois de todos os as VMs parar, selecione cada uma das VMs deixou e clique em Iniciar.

*   Repetir o pedido de reiniciar tarde.

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problema: Erro ao redimensionamento uma VM existente

Experimente redimensionar uma existente VM mas recebe uma falha de alocação.

### <a name="cause"></a>Causa

O pedido para redimensionar a VM tem de ser tentada na cluster original que aloja o serviço de nuvem. No entanto, o cluster não suporta o tamanho da memória virtual pedido.

### <a name="resolution"></a>Resolução

* Repetir o pedido utilizando um tamanho VM mais pequeno.

* Se não é possível alterar o tamanho da VM pedida:

  1. Pare de todos os VMs no conjunto de disponibilidade.

    * Clique em **grupos de recursos** > _seu grupo de recursos_ > **recursos** > _Configurar a sua disponibilidade_ > **máquinas virtuais** > _máquina virtual_ > **Parar**.

  2. Depois de todos os as VMs parar, redimensione a VM pretendida para um tamanho de maior.
  3. Selecione a VM redimensionada e clique em **Iniciar**e, em seguida, iniciar cada uma das VMs deixou de.

## <a name="next-steps"></a>Próximos passos

Se encontrar problemas quando cria uma nova VM de Linux no Azure, consulte o artigo [resolução de problemas de implementação com a criação de uma nova máquina de virtual Linux no Azure](../virtual-machines/virtual-machines-linux-troubleshoot-deployment-new-vm.md).
