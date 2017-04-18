<properties
   pageTitle="VM reiniciar ou redimensionar problemas | Microsoft Azure"
   description="Implementação clássica resolver problemas relacionados com reiniciar ou redimensionar uma máquina de Virtual Linux existente no Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="Deland-Han"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>

<tags
   ms.service="virtual-machines-linux"
   ms.topic="support-article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="required"
   ms.date="09/20/2016"
   ms.devlang="na"
   ms.author="delhan"/>

# <a name="troubleshoot-classic-deployment-issues-with-restarting-or-resizing-an-existing-linux-virtual-machine-in-azure"></a>Implementação clássica resolver problemas relacionados com reiniciar ou redimensionar uma máquina de Virtual Linux existente no Azure

> [AZURE.SELECTOR]
- [Clássico](../articles/virtual-machines/virtual-machines-linux-classic-restart-resize-error-troubleshooting.md)
- [Gestor de recursos](../articles/virtual-machines/virtual-machines-linux-restart-resize-error-troubleshooting.md)

Quando tentar iniciar uma Máquina Virtual do Azure parado (VM) ou redimensionar um VM Azure existente, o erro comum que encontrar é uma falha de alocação. Este erro resultados quando o cluster ou a região não tem recursos disponíveis ou quando não consegue suportar o tamanho da memória virtual pedido.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Os registos de auditoria recolher

Para começar a resolução de problemas, recolha registos de auditoria para identificar o erro associado com o problema.

No portal do Azure, clique em **Procurar** > **máquinas virtuais** > _máquina virtual Linux_ > **Definições** > **registos de auditoria**.

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problema: Erro ao iniciar uma VM parada

Tente iniciar uma VM parada mas recebe uma falha de alocação.

### <a name="cause"></a>Causa

O pedido para iniciar a VM parada tem de ser tentada na cluster original que aloja o serviço de nuvem. No entanto, o cluster não ter espaço livre disponível para cumprir o pedido.

### <a name="resolution"></a>Resolução

* Criar um novo serviço de nuvem e associá-la ou uma região ou uma rede virtual com base em região, mas não é um grupo de afinidade.

* Elimine a VM parada.

* Recrie a VM no novo serviço em nuvem ao utilizar os discos.

* Comece a VM recriada.

Se obtiver um erro ao tentar criar um novo serviço de nuvem, volte a tentar tarde ou alterar a região para o serviço de nuvem.

> [AZURE.IMPORTANT] O novo serviço de nuvem terá um novo nome e VIP, por isso tem de alterar essas informações para todas as dependências utilizar essas informações para o serviço de nuvem existente.

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problema: Erro ao redimensionamento uma VM existente

Experimente redimensionar uma existente VM mas recebe uma falha de alocação.

### <a name="cause"></a>Causa

O pedido para redimensionar a VM tem de ser tentada na cluster original que aloja o serviço de nuvem. No entanto, o cluster não suporta o tamanho da memória virtual pedido.

### <a name="resolution"></a>Resolução

Reduzir o tamanho da memória virtual pedido e tente novamente o pedido de redimensionamento.

* Clique em **Procurar todos os** > **máquinas virtuais (clássico)** > _máquina virtual_ > **Definições** > **tamanho**. Para obter passos detalhados, consulte o artigo [redimensionar a máquina virtual](https://msdn.microsoft.com/library/dn168976.aspx).

Se não for possível reduzir o tamanho da memória virtual, siga estes passos:

  * Crie um novo serviço de nuvem, garantindo que não é ligada a um grupo de afinidade e não associada a uma rede virtual que está associada a um grupo de afinidade.

  * Crie uma nova, maiores dimensões VM no mesmo.

Também pode consolidar os todos os seus VMs no serviço de nuvem do mesmo. Se o serviço de nuvem existente está associado uma rede virtual com base em região, pode ligar o novo serviço de nuvem à rede virtual existente.

Se o serviço de nuvem existente não está associado uma rede virtual com base em região, em seguida, tem de eliminar VMs no serviço de nuvem existente e recrie-as no novo serviço em nuvem dos seus discos. No entanto, é importante lembrar-se de que o novo serviço de nuvem terá um novo nome e VIP, pelo que necessita de actualizar estas para todas as dependências atualmente utilizam estas informações para o serviço de nuvem existente.

## <a name="next-steps"></a>Próximos passos

Se encontrar problemas quando cria uma nova VM de Linux no Azure, consulte o artigo [resolução de problemas de implementação com a criação de uma nova máquina de virtual Linux no Azure](../virtual-machines/virtual-machines-linux-troubleshoot-deployment-new-vm.md).
