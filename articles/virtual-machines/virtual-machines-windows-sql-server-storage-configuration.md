<properties
    pageTitle="Configuração de armazenamento para SQL Server VMs | Microsoft Azure"
    description="Este tópico descreve como Azure configura o armazenamento para SQL Server VMs durante o aprovisionamento (modelo de implementação do Gestor de recursos). Também explica como pode configurar o armazenamento do seu servidor VMs existente do SQL."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="ninarn"
    manager="jhubbard"    
    tags="azure-resource-manager"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/04/2016"
    ms.author="ninarn" />

# <a name="storage-configuration-for-sql-server-vms"></a>Configuração de armazenamento para SQL Server VMs

Quando configura uma imagem de máquina virtual do SQL Server no Azure, o Portal de ajuda para automatizar a configuração de armazenamento. Isto inclui anexar armazenamento para a VM, tornar essa armazenamento acessíveis para o SQL Server e configurá-la para otimizar para os seus requisitos de desempenho específicos.

Este tópico explica como Azure configura armazenamento para sua VMs de servidor SQL durante o aprovisionamento e para VMs existentes. Esta configuração é baseada as [melhores práticas de desempenho](virtual-machines-windows-sql-performance.md) para VMs Azure com o SQL Server.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementação clássica.

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar as definições de configuração de armazenamento automatizado, o seu máquina virtual requer as seguintes características:

- Aprovisionados com uma [imagem da galeria do SQL Server](virtual-machines-windows-sql-server-iaas-overview.md#option-1-deploy-a-sql-vm-per-minute-licensing).
- Utiliza o [modelo de implementação do Gestor de recursos](../resource-manager-deployment-model.md).
- Utiliza o [armazenamento de Premium](../storage/storage-premium-storage.md).

## <a name="new-vms"></a>Novos VMs
As secções seguintes descrevem como configurar o armazenamento para novos máquinas de virtuais do SQL Server.

### <a name="azure-portal"></a>Portal do Azure
Quando aprovisiona o uma VM Azure utilizando uma imagem da galeria do SQL Server, pode escolher configurar automaticamente o armazenamento para a nova VM. Especifique o tamanho do armazenamento, limites de desempenho e o tipo de carga de trabalho. A seguinte captura de ecrã mostra o pá de configuração de armazenamento utilizado durante a SQL VM aprovisionamento.

![Configuração do SQL Server VM armazenamento durante o aprovisionamento](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

Azure com base nas suas escolhas, efetua as seguintes tarefas de configuração de armazenamento depois de criar a VM:

- Cria e anexa discos de dados de armazenamento premium à máquina virtual.
- Configura os discos de dados para serem acessíveis para o SQL Server.
- Configura os discos de dados para um agrupamento de armazenamento com base nos tamanho e desempenho (IOPS e débito) requisitos especificados.
- Associa o agrupamento de armazenamento de uma nova unidade na máquina virtual.
- Optimiza esta nova unidade com base no tipo de carga de trabalho especificado (armazenamento de dados, transaccionais processamento ou geral).

Para obter mais detalhes sobre como o Azure configura as definições de armazenamento, consulte a [secção de configuração de armazenamento](#storage-configuration). Para completo obter instruções sobre como criar uma VM do SQL Server no Portal do Azure, consulte [o tutorial de aprovisionamento](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="resource-manage-templates"></a>Modelos de gerir de recursos
Se utilizar os seguintes modelos de Gestor de recursos, dois discos de dados de premium estão anexados por predefinição, com sem configuração do agrupamento de armazenamento. No entanto, pode personalizar estes modelos para alterar o número de discos de dados de premium estão anexados à máquina virtual.

- [Criar VM com cópia de segurança automatizada](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
- [Criar VM com a aplicação de patches automatizado](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
- [Criar VM com a integração de AKV](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

## <a name="existing-vms"></a>VMs existentes
Para existente SQL Server VMs, pode modificar algumas definições de armazenamento no portal do Azure. Selecione a VM, vá para a área de definições e, em seguida, selecione configuração do SQL Server. Pá a configuração do SQL Server mostra a utilização do armazenamento atual da sua VM. Todas as unidades existentes no seu VM são apresentadas neste gráfico. Para cada unidade, o espaço de armazenamento apresenta nas quatro secções:

- Dados do SQL
- Registo de SQL
- Outros (armazenamento de que não sejam SQL)
- Disponível

![Configurar o armazenamento para existente SQL Server VM](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

Para configurar o armazenamento para adicionar uma nova unidade ou expandir uma unidade existente, clique na ligação editar acima do gráfico.

As opções de configuração que vê varia dependendo se utilizou esta funcionalidade antes. Ao utilizar pela primeira vez, pode especificar os requisitos de armazenamento para uma unidade de novo. Se tiver utilizado anteriormente esta funcionalidade para criar uma unidade, pode optar por alargar o armazenamento de nessa unidade.

### <a name="use-for-the-first-time"></a>Utilizar pela primeira vez
Se for a primeira vez que utilizar esta funcionalidade, pode especificar os limites de tamanho e desempenho de armazenamento para uma nova unidade. Esta experiência é semelhante a que verá na hora de aprovisionamento. A diferença principal é que não são permitidos para especificar o tipo de carga de trabalho. Esta restrição impede a perturbar qualquer configurações do SQL Server existentes na máquina virtual.

![Configurar os controlos de deslize do SQL Server armazenamento](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-usage-sliders.png)

Azure cria uma nova unidade com base no seu especificações. Neste cenário, Azure executa as seguintes tarefas de configuração de armazenamento:

- Cria e anexa discos de dados de armazenamento premium à máquina virtual.
- Configura os discos de dados para serem acessíveis para o SQL Server.
- Configura os discos de dados para um agrupamento de armazenamento com base nos tamanho e desempenho (IOPS e débito) requisitos especificados.
- Associa o agrupamento de armazenamento de uma nova unidade na máquina virtual.

Para obter mais detalhes sobre como o Azure configura as definições de armazenamento, consulte a [secção de configuração de armazenamento](#storage-configuration).

### <a name="add-a-new-drive"></a>Adicionar uma nova unidade
Se já tiver configurado o armazenamento no seu VM de servidor de SQL, expandir armazenamento mostra duas novas opções. É a primeira opção Adicionar uma nova unidade, pode aumentar o nível de desempenho da sua VM.

![Adicionar uma nova unidade para uma VM de SQL](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-add-new-drive.png)

No entanto, depois de adicionar a unidade, tem de executar alguns configuração extra manual para alcançar o aumento do desempenho.

### <a name="extend-the-drive"></a>Expandir a unidade
É a outra opção de armazenamento de expandir expandir a unidade existente. Esta opção aumenta o armazenamento disponível para a sua unidade, mas não-aumentar o desempenho. Com os agrupamentos de armazenamento, não é possível alterar o número de colunas após o agrupamento de armazenamento é criado. O número de colunas determina o número de escritas paralelos, que pode ser divididos discos dados. Por conseguinte, quaisquer dados adicionados discos não é possível aumentar o desempenho. Pode apenas fornecem mais armazenamento para os dados que está a ser escritos. Esta limitação também significa que, quando expande a unidade, o número de colunas determina o número mínimo de discos de dados que pode adicionar. Por isso, se criar um agrupamento de armazenamento com discos quatro dados, o número de colunas também é quatro. Qualquer altura que alargar o armazenamento, tem de adicionar discos pelo menos quatro dados.

![Expandir uma unidade para um VM de SQL](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-a-drive.png)

## <a name="storage-configuration"></a>Configuração de armazenamento
Esta secção fornece uma referência para as alterações de configuração de armazenamento Azure executa automaticamente durante VM SQL de aprovisionamento ou de configuração no Portal do Azure.

- Se tiver selecionado TBs menos de dois de armazenamento para sua VM, Azure não cria um agrupamento de armazenamento.
- Se tiver selecionado, pelo menos, duas TBs de armazenamento para sua VM, Azure configura um agrupamento de armazenamento. A secção seguinte deste tópico fornece os detalhes da configuração do agrupamento de armazenamento.
- Configuração de armazenamento automático sempre utiliza discos de [armazenamento de premium](../storage/storage-premium-storage.md) P30 de dados. Consequentemente, não existe um mapeamento de 1:1 entre o seu número de Terabytes selecionado e o número de discos de dados ligados a sua VM.

Para informações sobre os preços, consulte a página de [preços de armazenamento](https://azure.microsoft.com/pricing/details/storage) no separador **Armazenamento de disco** .

### <a name="creation-of-the-storage-pool"></a>Criação de agrupamento de armazenamento
Azure utiliza as seguintes definições para criar o agrupamento de armazenamento no SQL Server VMs.

| Definição | Valor |
|-----|-----|
| Tamanho da faixa  | 256 KB (armazenamento de dados); 64 KB (transacionais) |
| Tamanhos de discos | 1 TB |
| Cache | Leitura |
| Tamanho da atribuição | Tamanho da unidade de 64 KB NTFS atribuição |
| Inicialização ficheiro instantâneas | Ativado |
| Bloquear páginas na memória | Ativado |
| Recuperação | Recuperação simples (sem RDP) |
| Número de colunas | Número de dados de discos<sup>1</sup> |
| Localização de TempDB | Armazenado nos dados discos<sup>2</sup> |

<sup>1</sup> quando o agrupamento de armazenamento estiver criado, não é possível alterar o número de colunas no conjunto de armazenamento.

<sup>2</sup> esta definição aplica-se apenas para a primeira unidade que cria utilizando a funcionalidade de configuração de armazenamento.

## <a name="workload-optimization-settings"></a>Definições de otimização de carga de trabalho
A tabela seguinte descreve as opções de tipo de carga de trabalho de três disponíveis e os respetivos correspondentes otimizações:

| Tipo de carga de trabalho | Descrição | Otimizações |
|-----|-----|-----|
| **Geral** | Predefinição que suporte a maioria das cargas de trabalho | Nenhum |
| **Processamento transaccional** | Optimiza o armazenamento de base de dados tradicional OLTP das cargas de trabalho | Sinalizador de rastreio 1117<br/>Sinalizador de rastreio 1118 |
| **Armazenamento de dados** | Optimiza o armazenamento de elaboração de relatórios e analíticos das cargas de trabalho | Sinalizador de rastreio 610<br/>Sinalizador de rastreio 1117 |

>[AZURE.NOTE] Só pode especificar o tipo de carga de trabalho quando aprovisionar uma máquina de virtual SQL ao selecioná-lo no passo de configuração de armazenamento.

## <a name="next-steps"></a>Próximos passos
Para obter outros tópicos relacionados com a execução do SQL Server no Azure VMs, consulte o artigo [SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).
