<properties
   pageTitle="O que é uma lista de controlo de acesso de rede (ACL)?"
   description="Saiba mais sobre ACL"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="what-is-an-endpoint-access-control-list-acls"></a>O que é um ponto final (ACL) na lista de controlo de acesso?

Um ponto final a lista de controlo de acesso (ACL) é um melhoramento de segurança disponível para a implementação do Azure. Uma ACL fornece a capacidade de seletivamente permitir ou negar tráfego para um ponto final de máquina virtual. Esta funcionalidade de filtragem de pacote fornece uma camada adicional de segurança. Pode especificar ACL de rede para os pontos finais apenas. Não é possível especificar uma ACL para uma rede virtual ou uma sub-rede específica contidas na rede virtual.

> [AZURE.IMPORTANT] Recomenda-se para utilizar grupos de segurança de rede (NSGs) em vez de ACL sempre que possível. Para saber mais sobre NSGs, consulte o artigo [o que é um grupo de segurança da rede?](virtual-networks-nsg.md).

ACL podem ser configuradas utilizando o PowerShell ou o Portal de gestão. Para configurar uma rede ACL utilizando o PowerShell, consulte o artigo [Gerir o acesso listas de controlo (ACL) para os pontos finais ao utilizar o PowerShell](virtual-networks-acl-powershell.md). Para configurar uma rede ACL utilizando o Portal de gestão, consulte o artigo [como definir o pontos finais para uma Máquina Virtual](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md).

Utilizar ACL de rede, pode fazer o seguinte:

- Seletivamente permitir ou negar tráfego recebido com base no intervalo de endereços IPv4 para um ponto final a entrada de máquina virtual de sub-rede remota.

- Endereços IP da lista de suporte

- Criar múltiplas regras por ponto final de máquina virtual

- Especificar até 50 regras ACL por ponto final de máquina virtual

- Utilizar a regra corrigida para garantir que o conjunto de regras correto são aplicados num ponto final da determinado máquina virtual (mais baixo para o maior)

- Especifique uma ACL para uma endereço IPv4 de sub-rede remota específica.

## <a name="how-acls-work"></a>Como funcionam as ACL

Uma ACL é um objeto que contém uma lista de regras. Quando criar uma ACL e aplicá-lo a um ponto final de máquina virtual, filtragem de pacotes ocorre no nó anfitrião da sua VM. Isto significa que o tráfego de endereços IP remotos é filtrado pelo nó anfitrião para regras de ACL correspondência em vez de no seu VM. Isto impede que a VM gastar os ciclos da CPU preciosas em filtragem de pacotes.

Quando é criada uma máquina virtual, uma ACL predefinida é colocada no local para bloquear todo o tráfego recebido. No entanto, se um ponto final é criado para (porta 3389), em seguida, a predefinição é modificado ACL para permitir que todo o tráfego de entrada para esse ponto final. O tráfego de entrada a partir de qualquer sub-rede remota, em seguida, é permitido para esse ponto final e sem aprovisionamento de firewall é necessário. Todas as outras portas estão bloqueadas para o tráfego de entrada, a menos que os pontos finais são criados para essas portas. O tráfego de saída é permitido por predefinição.

**Tabela de ACL predefinido de exemplo**

| **Regra #** | **Sub-rede remota** | **Ponto final** | **Permitir/Negar** |
|--------|---------------|----------|-------------|
| 100    | 0.0.0.0/0     | 3389     | Permitir      |

## <a name="permit-and-deny"></a>Permitir e negar

Pode seletivamente permitir ou negar o tráfego de rede para um ponto final a entrada de máquina virtual ao criar regras que especificam "Permitir" ou "Negar". É importante ter em atenção que por predefinição, quando é criado um ponto final, todo o tráfego é permitido para o ponto final. Por que razão, é importante compreender como criar regras de autorização/negar e colocá-los a ordem de precedência adequada se pretender que o controlo granular sobre o tráfego de rede que escolher para permitir que para alcançar o ponto final de máquina virtual.

Pontos a considerar:

1. **Sem ACL –** Por predefinição quando é criado um ponto final, podemos permitir, todos os para o ponto final.

1. **Permitir-** Quando adiciona um ou mais "permitam" intervalos, são negar todos os outros intervalos por predefinição. Apenas os pacotes do intervalo IP permitido poderão comunicar com o ponto final de máquina virtual.

1. **Negar-** Quando adiciona um ou mais "Negar" intervalos, são permitindo todos os outros intervalos de tráfego por predefinição.

1. **Combinação de permitir e negar-** Pode utilizar uma combinação de "Permitir" e "Negar" quando pretender excepção saída de um intervalo IP específico para lhe ser permitido ou negado.

## <a name="rules-and-rule-precedence"></a>Regras e a precedência de regras

ACL de rede podem ser configuradas no pontos finais de máquina virtual específica. Por exemplo, pode especificar uma rede ACL para um ponto final RDP criado numa máquina virtual endereços de IP para determinados para que bloqueios para baixo do access. A tabela abaixo mostra uma forma para conceder acesso ao público virtual IPs (VIP) de um determinado intervalo para permitir o acesso para RDP. Todos os outros IPs remoto são negadas. Vamos seguem uma ordem de regra *mais baixos terá precedência* .

### <a name="multiple-rules"></a>Múltiplas regras

No exemplo abaixo, se pretender permitir o acesso o ponto final RDP apenas de duas públicos IPv4 intervalos de endereços (65.0.0.0/8 e 159.0.0.0/8), é possível alcançar isto ao especificar duas regras de *Permitir* . Neste caso, uma vez que RDP é criado por predefinição para uma máquina virtual, poderá pretender bloquear o acesso à porta RDP com base numa sub-rede remota. O exemplo abaixo apresenta uma forma para conceder acesso ao público virtual IPs (VIP) de um determinado intervalo para permitir o acesso para RDP. Todos os outros IPs remoto estão negado. Isto funciona porque ACL de rede pode ser configurado para um ponto final de máquina virtual específica e acesso negado por predefinição.

**Exemplo – múltiplas regras**

| **Regra #** | **Sub-rede remota** | **Ponto final** | **Permitir/Negar** |
|--------|---------------|----------|-------------|
| 100    | 65.0.0.0/8    | 3389     | Permitir      |
| 200    | 159.0.0.0/8   | 3389     | Permitir      |

### <a name="rule-order"></a>Ordem de regra

Uma vez que podem ser especificadas múltiplas regras para um ponto final, tem de ter uma forma de organizar regras para determinar qual a regra tem precedência. A ordem de regra especifica a precedência. Rede ACL seguem uma ordem de regra *mais baixos terá precedência* . No exemplo abaixo, o ponto final na porta 80 seletivamente é concedido acesso a apenas determinados intervalos de endereços IP. Para configurar isto, temos uma regra de negação (regra \# 100) para os endereços no espaço 175.1.0.1/24. Uma segunda regra, em seguida, é especificada com a precedência de 200 que permita o acesso a todos os outros endereços em 175.0.0.0/8.

**Exemplo – a precedência de regras**

| **Regra #** | **Sub-rede remota** | **Ponto final** | **Permitir/Negar** |
|--------|---------------|----------|-------------|
| 100    | 175.1.0.1/24  | 80       | Negar        |
| 200    | 175.0.0.0/8   | 80       | Permitir      |

## <a name="network-acls-and-load-balanced-sets"></a>ACL de rede e carregar conjuntos desequilibrados

ACL de rede podem ser especificadas num ponto final (LB definido) do conjunto de balanceamento de carga. Se não for especificada uma ACL de um conjunto de LB, a ACL de rede é aplicada a todas as máquinas virtuais no conjunto LB. Por exemplo, se um conjunto de LB é criado com "Porta 80" e o conjunto de LB contém 3 VMs, a ACL de rede criado no ponto final "A porta 80" de um que VM aplicará automaticamente para as outros VMs.

![ACL de rede e carregar conjuntos desequilibrados](./media/virtual-networks-acl/IC674733.png)

## <a name="next-steps"></a>Próximos passos

[Como gerir listas de controlo de acesso (ACL) para os pontos finais ao utilizar o PowerShell](virtual-networks-acl-powershell.md)
