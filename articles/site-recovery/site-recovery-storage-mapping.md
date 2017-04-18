<properties
    pageTitle="Mapear o armazenamento no Azure recuperação de Site para a replicação de máquina virtual Hyper-V entre centros de dados no local | Microsoft Azure"
    description="Prepare o mapeamento de armazenamento para a replicação de máquina virtual Hyper-V entre dois centros de dados no local com a recuperação de Site do Azure."
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
    ms.date="07/06/2016"
    ms.author="raynew"/>


# <a name="prepare-storage-mapping-for-hyper-v-virtual-machine-replication-between-two-on-premises-datacenters-with-azure-site-recovery"></a>Preparar o mapeamento de armazenamento para a replicação de máquina virtual Hyper-V entre dois centros de dados no local com a recuperação de Site do Azure


Azure recuperação de sites contribui para a sua estratégia de recuperação (BCDR) empresas continuidade e falhas por orquestrar replicação, activação pós-falha e recuperação de máquinas virtuais e servidores físicos. Este artigo descreve o mapeamento de armazenamento, que ajuda a certificar ideal utilize de armazenamento quando estiver a utilizar recuperação de sites para criar uma réplica máquinas virtuais de Hyper-V entre centros de dados VMM de duas no local.

Registe quaisquer comentários ou perguntas na parte inferior deste artigo ou no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Descrição geral

Mapeamento de armazenamento só é relevante quando estiver a replicação máquinas virtuais Hyper-V que estão localizadas no nuvens VMM a partir de um centro de dados principal para um centro de dados secundário utilizando a replicação de réplica do Hyper-V ou SAN, da seguinte forma:


- **No local para a replicação no local com réplica Hyper-V)** — Ter configurado o armazenamento mapeamento por mapeamento classificações de armazenamento numa origem e destino servidores VMM para fazer o seguinte:

    - **Identificar o armazenamento de destino para máquinas virtuais de réplica**— máquinas virtuais vai ser replicadas para um alvo de armazenamento (SMB partilhar ou cluster volumes partilhados (CSVs)) que escolher.
    - **Posicionamento de máquina virtual réplica**— é utilizado o mapeamento de armazenamento para colocar optimizada máquinas virtuais de réplica em servidores de anfitriões Hyper-V. Máquinas virtuais de réplica serão colocadas em anfitriões que podem aceder a classificação de armazenamento mapeados.
    - **Nenhum mapeamento de armazenamento**— se não configurar mapeamento de armazenamento, máquinas virtuais vai ser replicadas para a localização de armazenamento predefinido especificada no servidor anfitrião Hyper-V associado a máquina de virtual réplica.

- **No local para a replicação no local com SAN**— configurar armazenamento mapeamento por mapeamento agrupamentos de matrizes de armazenamento numa origem e VMM servidores de destino.
    - **Agrupamento de especificar**— Especifica que agrupamento de armazenamento secundário receba replicação dados a partir do conjunto de principal.
    - **Agrupamentos de armazenamento de destino identificar**— garante que LUNs num grupo de replicação de origem são replicados ao destino mapeados agrupamento de armazenamento da sua escolha.

## <a name="set-up-storage-classifications-for-hyper-v-replication"></a>Configurar classificações de armazenamento para a replicação Hyper-V

Quando estiver a utilizar o Hyper-V réplica para criar uma réplica com a recuperação de Site, mapear entre classificações de armazenamento em servidores VMM de origem e destino, ou num único servidor VMM se dois sites são geridas pelo servidor VMM mesmo. Tenha em atenção que:

- Quando mapeamento está corretamente configurado e a replicação está ativada, irá ser replicado virtual no disco rígido uma máquina virtual na localização primária ao armazenamento na localização de destino mapeadas.
- Classificações de armazenamento tem de estar disponíveis para os grupos de anfitriões localizados na nuvens de origem e destino.
- Classificações de não é necessário ter o mesmo tipo de armazenamento. Por exemplo, pode mapear uma classificação de origem que contém partilhas SMB para uma classificação de destino que contém CSVs.
- Leia mais em [como criar classificações de armazenamento no VMM](https://technet.microsoft.com/library/gg610685.aspx).

## <a name="example"></a>Exemplo

Se classificações estão corretamente configuradas no VMM Quando seleciona a origem e destino servidor VMM durante o mapeamento de armazenamento, as classificações de origem e destino serão apresentadas. Eis um exemplo de partilhas de ficheiros de armazenamento e classificações de uma organização com duas localizações em Nova Iorque e Chicago.

**Localização** | **Servidor VMM** | **Partilha de ficheiros (origem)** | **Classificação (origem)** | **Mapeados para** | **Partilha de ficheiros (destino)**
---|---|--- |---|---|---
Nova Iorque | VMM_Source| SourceShare1 | OURO | GOLD_TARGET | TargetShare1
 |  | SourceShare2 | PRATA | SILVER_TARGET | TargetShare2
 | | SourceShare3 | BRONZE | BRONZE_TARGET | TargetShare3
Chicago | VMM_Target |  | GOLD_TARGET | Não mapeados |
| | | SILVER_TARGET | Não mapeados |
 | | | BRONZE_TARGET | Não mapeados

Seria configurar estas no separador **Servidor de armazenamento** na página de **recursos** do portal de recuperação de sites.

![Configurar mapeamento de armazenamento](./media/site-recovery-storage-mapping/storage-mapping1.png)

Com este exemplo:
- Quando um uma máquina de virtual réplica é criada para qualquer máquina virtual sobre o armazenamento de ouro (SourceShare1), vai ser replicada para um armazenamento GOLD_TARGET (TargetShare1).
- Quando é criada uma máquina de virtual réplica para qualquer máquina virtual armazenamento prata (SourceShare2), vai ser replicadas para um armazenamento SILVER_TARGET (TargetShare2) e assim sucessivamente.

As partilhas de ficheiros real e os respetivos classificações atribuídas no VMM aparecem a captura de ecrã seguinte.

![Classificações de armazenamento no VMM](./media/site-recovery-storage-mapping/storage-mapping2.png)

## <a name="multiple-storage-locations"></a>Várias localizações de armazenamento

Se a classificação de destino é atribuída a várias partilhas SMB ou CSVs, a localização de armazenamento ideal será automaticamente seleccionada quando a máquina virtual está protegida. Se nenhum armazenamento de destino adequado estiver disponível com a classificação especificada, a localização de armazenamento predefinido especificada no anfitrião do Hyper-V é utilizada para colocar os discos rígido virtuais réplica.

A tabela seguinte mostra como classificação de armazenamento e volumes de cluster partilhado são configurados no nosso exemplo.

**Localização** | **Classificação** | **Associadas de armazenamento**
---|---|---
Nova Iorque | OURO | <p>C:\ClusterStorage\SourceVolume1</p><p>\\FileServer\SourceShare1</p>
 | PRATA | <p>C:\ClusterStorage\SourceVolume2</p><p>\\FileServer\SourceShare2</p>
Chicago | GOLD_TARGET | <p>C:\ClusterStorage\TargetVolume1</p><p>\\FileServer\TargetShare1</p>
 | SILVER_TARGET| <p>C:\ClusterStorage\TargetVolume2</p><p>\\FileServer\TargetShare2</p>

A tabela seguinte resume o comportamento quando ativa proteção para máquinas virtuais (VM1 - VM5) neste ambiente de exemplo.

**Máquina virtual** | **Armazenamento de origem** | **Classificação de origem** | **Armazenamento de destino mapeados**
---|---|---|---
VM1 | C:\ClusterStorage\SourceVolume1 | OURO | <p>C:\ClusterStorage\SourceVolume1</p><p>\\\FileServer\SourceShare1</p><p>Ambas as GOLD_TARGET</p>
VM2 | \\FileServer\SourceShare1 | OURO | <p>C:\ClusterStorage\SourceVolume1</p><p>\\FileServer\SourceShare1</p> <p>Ambas as GOLD_TARGET</p>
VM3 | C:\ClusterStorage\SourceVolume2 | PRATA | <p>C:\ClusterStorage\SourceVolume2</p><p>\FileServer\SourceShare2</p>
VM4 | \FileServer\SourceShare2 | PRATA |<p>C:\ClusterStorage\SourceVolume2</p><p>\\FileServer\SourceShare2</p><p>Ambas as SILVER_TARGET</p>
VM5 | C:\ClusterStorage\SourceVolume3 | N/D | Nenhum mapeamento, para que serve a localização de armazenamento predefinida do anfitrião do Hyper-V

## <a name="next-steps"></a>Próximos passos

Agora que tem uma melhor compreensão das mapeamento de armazenamento, [preparar-se implementar o Azure recuperação de sites](site-recovery-best-practices.md).
