<properties
   pageTitle="Resolver problemas de Linux VM implementação-RM | Microsoft Azure"
   description="Resolução de problemas de implementação do Gestor de recursos quando cria uma nova máquina de virtual Linux no Azure"
   services="virtual-machines-linux, azure-resource-manager"
   documentationCenter=""
   authors="JiangChen79"
   manager="felixwu"
   editor=""
   tags="top-support-issue, azure-resource-manager"/>

<tags
  ms.service="virtual-machines-linux"
  ms.workload="na"
  ms.tgt_pltfrm="vm-linux"
  ms.devlang="na"
  ms.topic="article"
  ms.date="09/09/2016"
  ms.author="cjiang"/>

# <a name="troubleshoot-resource-manager-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>Resolução de problemas de implementação do Gestor de recursos com a criação de uma nova máquina de virtual Linux no Azure

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Os registos de auditoria recolher

Para começar a resolução de problemas, recolha registos de auditoria para identificar o erro associado com o problema. As ligações seguintes contêm informações detalhadas sobre o processo para seguir.

[Resolver problemas de implementações do grupo de recursos com o Portal do Azure](../resource-manager-troubleshoot-deployments-portal.md)

[Operações de auditoria com o Gestor de recursos](../resource-group-audit.md)

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[AZURE.INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y:** Se o SO está Linux generalizada e -é carregado e/ou capturado com a definição de GRG, em seguida, não haverá erros. Da mesma forma, se for o SO Linux especializado e -é carregado e/ou capturado com a definição de especializadas, em seguida, não haverá erros.

**Carregar erros de início:**

**N<sup>1</sup>:** Se o SO está Linux generalizada e é carregá-lo como especializado, irá obter um erro de limite de tempo aprovisionamento porque a VM está retida na fase de aprovisionamento.

**N<sup>2</sup>:** Se o sistema operativo é Linux especializado e ser carregado como generalized, obterá um erro de falha de aprovisionamento porque a nova VM está em execução com o nome do computador, nome de utilizador e palavra-passe original.

**Resolução:**

Para resolver estes erros ambas as, carregue o VHD original, disponível no local, com a mesma definição como que para o sistema operativo (generalized/especializado). Para carregar como generalized, se lembrar de executar - deprovision pela primeira vez.

**Capture erros:**

**N<sup>3</sup>:** Se o SO for Linux generalizada e a mesma é capturada como especializado, irá obter um erro de limite de tempo aprovisionamento porque a VM original não é utilizável tal como está marcado como generalized.

**N<sup>4</sup>:** Se o sistema operativo é Linux especializado e é capturado como generalized, obterá um erro de falha de aprovisionamento porque a nova VM está em execução com o nome do computador, nome de utilizador e palavra-passe original. Além disso, o original VM não está utilizável porque está marcado como especializado.

**Resolução:**

Para resolver estes erros ambas as, elimine a imagem atual a partir do portal e [volte a capturá-lo a partir de VHDs atuais](virtual-machines-linux-capture-image.md) com a mesma definição como que para o sistema operativo (generalized/especializado).

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Problema: Personalizada / Galeria / imagem marketplace; Falha de alocação
Este erro surge nas situações, quando o novo pedido VM é afixado a um cluster de que não consegue suportar o tamanho da memória virtual solicitado ou que não tem espaço livre disponível para acomodar o pedido.

**Causar 1:** Cluster não consegue suportar o tamanho da memória virtual pedido.

**Resolução 1:**

- Repetir o pedido utilizando um tamanho VM mais pequeno.
- Se não é possível alterar o tamanho da VM pedida:
  - Pare de todos os VMs no conjunto de disponibilidade.
  Clique em **grupos de recursos** > *seu grupo de recursos* > **recursos** > *Configurar a sua disponibilidade* > **máquinas virtuais** > *máquina virtual* > **Parar**.
  - Depois de todos os as VMs parar, crie a nova VM no tamanho pretendido.
  - Iniciar a nova VM em primeiro lugar e, em seguida, selecione cada uma das VMs deixou de e clique em **Iniciar**.

**Causar 2:** Cluster não tiver recursos gratuitos.

**Solução 2:**

- Repetir o pedido tarde.
- Se a nova VM pode fazer parte de um conjunto diferente de disponibilidade
  - Crie uma nova VM uma disponibilidade diferentes definir (na mesma região).
  - Adicione a nova VM à mesma rede virtual.

## <a name="next-steps"></a>Próximos passos
Se encontrar problemas quando inicia uma VM Linux parado ou redimensionar um VM Linux existente no Azure, consulte o artigo [problemas de implementação do Gestor de recursos a resolver problemas com que seja reiniciado ou redimensionar uma máquina de Virtual Linux existente no Azure](virtual-machines-linux-restart-resize-error-troubleshooting.md).
