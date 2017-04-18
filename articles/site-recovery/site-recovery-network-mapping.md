<properties
    pageTitle="Preparar o mapeamento de rede para protecção de máquina virtual Hyper-V com VMM no Azure Site recuperação | Microsoft Azure"
    description="Configure o mapeamento de rede para Hyper-V máquina virtual replicação a partir de um centro de dados no local para Azure ou para um site secundário."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="10/05/2016"
    ms.author="raynew"/>


# <a name="prepare-network-mapping-for-hyper-v-virtual-machine-protection-with-vmm-in-azure-site-recovery"></a>Preparar o mapeamento de rede para protecção de máquina virtual Hyper-V com VMM no Azure recuperação de sites

Azure recuperação de sites contribui para a sua estratégia de recuperação (BCDR) empresas continuidade e falhas por orquestrar replicação, activação pós-falha e recuperação de máquinas virtuais e servidores físicos.

Este artigo descreve o mapeamento de rede, que ajuda a configurar optimizada definições de rede, quando estiver a utilizar recuperação de sites para criar uma réplica máquinas virtuais de Hyper-V localizada no nuvens VMM entre as duas centros de dados no local ou entre um centro de dados no local e o Azure. Tenha em atenção que, se estiver a replicação Hyper-V VMs sem uma nuvem VMM ou replicação VMware VMs ou servidores físicos, este artigo não é relevante.

Registe quaisquer comentários ou perguntas na parte inferior deste artigo ou no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="overview"></a>Descrição geral

Mapeamento de rede é utilizado quando Azure recuperação de Site seja implementada criar uma réplica máquinas virtuais de Hyper-V para Azure ou para um centro de dados secundário, utilizando a replicação Hyper-V réplica ou SAN.

- **Replicação Hyper-V máquinas virtuais nuvens VMM entre dois locais centros de dados**— rede mapas de mapeamento entre redes VM num servidor VMM origem e as redes VM num servidor VMM destino de fazer o seguinte:

    - **Máquinas virtuais de ligar após activação pós-falha**— garante que máquinas virtuais serão ligadas ao redes adequados após activação pós-falha. A máquina de virtual réplica irá estar ligada à rede de destino que está mapeada para a rede de origem.
    - **Máquinas virtuais de réplica de local nos servidores do anfitrião**— ideal colocar máquinas virtuais de réplica em servidores de anfitriões Hyper-V. Máquinas virtuais de réplica serão colocadas em anfitriões que podem aceder às redes VM mapeadas.
    - **Nenhum mapeamento de rede**— se não configurar mapeamento de rede, replicadas máquinas virtuais não estiver ligadas a quaisquer redes VM após activação pós-falha.

- **Replicação Hyper-V máquinas virtuais uma VMM no local na nuvem para Azure**— rede mapas de mapeamento entre redes VM no servidor VMM de origem e destino Azure redes de fazer o seguinte:
    - **Ligar máquinas virtuais após activação pós-falha**— todos os computadores que activação pós-falha na mesma rede pode ligar umas às outras, independentemente que plano de recuperação estiverem na.
    - **Gateway de rede**— se um gateway de rede está configurado no destino rede Azure, máquinas virtuais pode ligar a outras máquinas virtuais no local.
    - **Nenhum mapeamento de rede**— se não configurar mapeamento de rede, apenas máquinas virtuais esse activação pós-falha no plano de recuperação mesmo será possível ligar umas às outras após falhas sobre a Azure.


## <a name="network-mapping-example"></a>Exemplo de mapeamento de rede

Mapeamento de rede pode ser configurado entre redes VM em dois servidores VMM ou num servidor VMM única se dois sites são geridas pelo mesmo servidor. Quando mapeamento está corretamente configurado e a replicação está ativada, uma máquina virtual na localização primária será ligada a uma rede e respectiva réplica na localização de destino irá estar ligada à sua rede mapeada.

Se redes tiverem sido configurados corretamente nas VMM, ao selecionar uma rede VM destino durante o mapeamento de rede, as nuvens de origem VMM que utilizam a rede VM origem serão apresentadas, juntamente com as redes VM destino disponíveis nas nuvens de destino que são usadas para protecção.

Eis um exemplo para ilustrar este mecanismo. Vamos uma organização com duas localizações em Nova Iorque e Chicago.

**Localização** | **Servidor VMM** | **Redes VM** | **Mapeados para**
---|---|---|---
Nova Iorque | VMM-Nova Iorque| VMNetwork1-Nova Iorque | Mapeados para VMNetwork1 Chicago
 |  | VMNetwork2-Nova Iorque | Não mapeados
Chicago | VMM Chicago| VMNetwork1 Chicago | Mapeados para VMNetwork1-Nova Iorque
 | | VMNetwork2 Chicago | Não mapeados

Com este exemplo:

- Quando é criada uma máquina de virtual réplica para qualquer máquina virtual que está ligada à VMNetwork1-Nova Iorque, será ligado ao VMNetwork1 Chicago.
- Quando é criada uma máquina de virtual réplica para VMNetwork2-Nova Iorque ou VMNetwork2 Chicago, não vai estar ligado a qualquer rede.

Eis como nuvens VMM estão configurados no nossa organização de exemplo e as redes lógicas associadas as nuvens.

### <a name="cloud-protection-settings"></a>Definições de proteção da nuvem

**Nuvem protegida** | **Proteger na nuvem** | **Rede lógica (Nova Iorque)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>DISP</p><p></p> | <p>LogicalNetwork1-Nova Iorque</p><p>LogicalNetwork1 Chicago</p>
SilverCloud2 | <p>DISP</p><p></p> | <p>LogicalNetwork1-Nova Iorque</p><p>LogicalNetwork1 Chicago</p>

### <a name="logical-and-vm-network-settings"></a>Lógico e VM definições de rede

**Localização** | **Rede lógica** | **Rede VM associada**
---|---|---
Nova Iorque | LogicalNetwork1-Nova Iorque | VMNetwork1-Nova Iorque
Chicago | LogicalNetwork1 Chicago | VMNetwork1 Chicago
 | LogicalNetwork2Chicago | VMNetwork2 Chicago

### <a name="target-networks"></a>Redes de destino

Com base nestas definições, ao clicar em rede VM de destino, a tabela seguinte mostra as opções que irão estar disponíveis.

**Selecione** | **Nuvem protegida** | **Proteger na nuvem** | **Rede de destino disponíveis**
---|---|---|---
VMNetwork1 Chicago | SilverCloud1 | SilverCloud2 | Disponível
 | GoldCloud1 | GoldCloud2 | Disponível
VMNetwork2 Chicago | SilverCloud1 | SilverCloud2 | Não disponível
 | GoldCloud1 | GoldCloud2 | Disponível



## <a name="multiple-subnets"></a>Várias sub-redes

Se a rede de destino tem várias sub-redes e uma desses sub-redes tem o mesmo nome como sub-rede no qual está localizada a máquina virtual da origem, em seguida, a máquina de virtual réplica será ligada nessa sub-rede de destino depois activação pós-falha. Se não existirem sem sub-rede destino com um nome correspondente, a máquina virtual será ligada para a primeira sub-rede na rede.


### <a name="failback"></a>Reposição de recurso

Para ver o que acontece no caso de reposição de recurso (replicação inversa), imaginemos que VMNetwork1-Nova Iorque é mapeado para VMNetwork1-Chicago, com as seguintes definições.


**Máquina virtual** | **Ligado à rede VM**
---|---
VM1 | Rede VMNetwork1
VM2 (réplica da VM1) | VMNetwork1 Chicago

Com estas definições, vamos rever o que acontece no apenas alguns cenários possíveis.

**Cenário** | **Resultado**
---|---
Sem alterações nas propriedades de rede de VM 2 após activação pós-falha. | VM 1 mantém-se ligado à rede de origem.
Propriedades de rede de VM 2 são alteradas depois de activação pós-falha e está desligado. | VM-1 é desligado.
Propriedades de rede de VM 2 são alteradas depois de activação pós-falha e está ligado à VMNetwork2 Chicago. | Se não está mapeado VMNetwork2 Chicago, será desligada VM-1.
Mapeamento de rede de VMNetwork1 Chicago é alterado. | Será ligado à rede agora mapeados para VMNetwork1 Chicago VM-1.


## <a name="next-steps"></a>Próximos passos

Agora que tem uma melhor compreensão das mapeamento de rede, [começar a trabalhar com a implementação de recuperação de sites](site-recovery-best-practices.md).
