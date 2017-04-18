<properties
    pageTitle="Diretrizes de soluções de armazenamento | Microsoft Azure"
    description="Saiba mais sobre as diretrizes de estrutura e implementação chaves para implementar soluções de armazenamento nos serviços de infraestrutura Azure."
    documentationCenter=""
    services="virtual-machines-windows"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="storage-infrastructure-guidelines"></a>Diretrizes de infraestrutura de armazenamento

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)] 

Este artigo foca-se em compreender as suas necessidades de armazenamento e considerações de estrutura para alcançar desempenho ideal máquina virtual (VM).


## <a name="implementation-guidelines-for-storage"></a>Diretrizes de implementação de armazenamento

Decisões:

- Precisa de utilizar o armazenamento de padrão ou Premium para sua carga de trabalho?
- Necessita de repartição do disco para criar discos superiores a 1023 GB?
- Necessita de repartição do disco para alcançar um desempenho ideal e/s para sua carga de trabalho?
- Qual o conjunto de contas de armazenamento de que precisa de alojar o seu carga de trabalho ou infraestrutura?

Tarefas:

- Reveja os pedidos e/s das aplicações do que está a implementar e planear o número adequado e o tipo de contas de armazenamento.
- Crie o conjunto de contas de armazenamento utilizando Convenção de nomenclatura. Pode utilizar o Azure PowerShell ou o portal.


## <a name="storage"></a>Armazenamento

Armazenamento Azure é uma peça chave implementar e gerir máquinas virtuais (VMs) e aplicações. Armazenamento Azure fornece serviços para armazenar dados do ficheiro, dados não estruturados e mensagens de e também é peça infraestrutura do VMs de suporte.

Existem dois tipos de contas de armazenamento disponível para o suporte VMs:

- Armazenamento padrão contas lhe dá acesso ao armazenamento blob do (utilizado para armazenar discos Azure VM), armazenamento de tabela, armazenamento de fila de espera e armazenamento de ficheiros.
- Contas de [armazenamento de Premium](../storage/storage-premium-storage.md) entregar suporte do disco de alto desempenho, baixa latência para e/s intensivos das cargas de trabalho, tais como SQL Servers num AlwaysOn cluster. Armazenamento de Premium atualmente suporta apenas a discos Azure VM.

Azure cria VMs com um disco do sistema operativo, um disco temporário e de zero ou mais discos de dados opcionais. O disco do sistema operativo e os discos de dados são blobs do Azure página, enquanto que o disco temporário está armazenado localmente no nó onde reside na máquina. Tomar cuidado quando conceber aplicações para utilizar apenas este disco temporário para os dados que não sejam persistente, tal como a VM pode ser migrada entre anfitriões durante um evento de manutenção. Quaisquer dados armazenados no disco temporário seria perdidos.

Durabilidade e elevada disponibilidade é fornecido pelo ambiente de armazenamento do Windows Azure subjacente para se certificar de que os seus dados permanecem protegidos contra falhas de manutenção ou hardware não planeadas. Como estruturar o seu ambiente de armazenamento do Windows Azure, pode optar por criar uma réplica armazenamento VM:

- localmente dentro de um determinado centro de dados do Azure
- ao longo do Azure centros de dados dentro de uma determinada região
- ao longo do Azure centros de dados através de regiões diferentes

Pode obter [mais informações sobre as opções de replicação para elevada disponibilidade](../storage/storage-introduction.md#replication-for-durability-and-high-availability).

Sistema operativo discos e dados têm um tamanho máximo de 1023 gigabytes (GB). O tamanho máximo de um blob é 1024 GB e que tem de conter os metadados (rodapé) do ficheiro VHD (um GB é 1024<sup>3</sup> bytes). Pode utilizar espaços de armazenamento no Windows Server 2012 para ultrapassem este limite ao agrupamento em conjunto de discos de dados para apresentar volumes lógicos superiores a 1023GB para sua VM.

Existem alguns limites escalabilidade ao estruturar as implementações de armazenamento do Windows Azure - consulte o artigo [limites de subscrição e o serviço do Microsoft Azure, quotas e restrições](azure-subscription-service-limits.md#storage-limits) para obter mais detalhes. Consulte também [destinos de desempenho e escalabilidade do armazenamento Azure](../storage/storage-scalability-targets.md).

Armazenamento de aplicação, pode armazenar dados de objeto não estruturados, como documentos, imagens, cópias de segurança, dados de configuração, registos de início, etc. utilizar o armazenamento de Blobs. Em vez da sua aplicação ao escrever um disco virtual ligado à VM, pode escrever a aplicação diretamente ao armazenamento de Blobs do Azure. Armazenamento de BLOBs também fornece a opção de [armazenamento quente e fria camadas](../storage/storage-blob-storage-tiers.md) dependendo do seu necessidades de disponibilidade e as restrições de custo.


## <a name="striped-disks"></a>Discos às riscas
Para além de permitindo-lhe criar discos com mais 1023 GB, em muitos casos, utilizar repartição para discos dados melhora o desempenho ao permitir vários blobs para trás o armazenamento de um único volume. Com repartição, a e/s necessário para escrever e ler os dados a partir de um disco lógico única avança em paralelo.

Azure impõe limites o número de discos de dados e a quantidade de largura de banda disponível, consoante o tamanho da memória virtual. Para obter detalhes, consulte o artigo [tamanhos para máquinas virtuais](virtual-machines-windows-sizes.md).

Se estiver a utilizar repartição do disco para discos Azure dados, considere as seguintes diretrizes:

- Discos dados devem estar sempre o tamanho máximo (1023 GB).
- Anexe discos dados máxima permitidos para o tamanho da memória virtual.
- Utilize espaços de armazenamento.
- Evitar utilizar disco dados Azure opções de cache (colocação em cache política = nenhum).

Para mais informações, consulte o artigo [espaços de armazenamento - estruturar para obter um desempenho](http://social.technet.microsoft.com/wiki/contents/articles/15200.storage-spaces-designing-for-performance.aspx).


## <a name="multiple-storage-accounts"></a>Várias contas de armazenamento

Quando a estruturar o seu ambiente de armazenamento do Windows Azure, pode utilizar várias contas de armazenamento como o número de VMs implementar aumenta. Isto ajuda a distribuir saída a e/s pela infraestrutura de armazenamento do Windows Azure subjacente para manter um desempenho ideal para o seu VMs e aplicações. Como estruturar as aplicações que estão a implementar, considere os requisitos e/s que cada VM tem e saldo saída esses VMs em contas de armazenamento do Windows Azure. Experimente evitar agrupar todos os a alta e/s exigem VMs na apenas um ou dois contas de armazenamento.

Para obter mais informações sobre as funcionalidades e/s das diferentes opções de armazenamento do Windows Azure e algumas recomendar larguras, consulte [destinos de desempenho e escalabilidade do armazenamento Azure](../storage/storage-scalability-targets.md).


## <a name="next-steps"></a>Próximos passos

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)] 