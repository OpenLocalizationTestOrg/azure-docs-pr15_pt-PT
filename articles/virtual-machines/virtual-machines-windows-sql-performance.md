<properties
    pageTitle="Práticas recomendadas do desempenho para SQL Server | Microsoft Azure"
    description="Fornece os procedimentos recomendados para otimizar o desempenho do SQL Server no Microsoft Azure VMs."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/07/2016"
    ms.author="jroth" />

# <a name="performance-best-practices-for-sql-server-in-azure-virtual-machines"></a>Práticas recomendadas do desempenho para SQL Server em máquinas virtuais do Azure

## <a name="overview"></a>Descrição geral

Este tópico fornece melhores práticas para otimizar o desempenho do SQL Server no Microsoft Azure Virtual Machine. Enquanto executa o SQL Server em máquinas virtuais do Azure, recomendamos que continuar a utilizar o mesmo optimização opções que são aplicáveis para o SQL Server no ambiente de servidor no local do desempenho da base de dados. No entanto, o desempenho da base de dados relacional numa nuvem pública depende vários fatores como o tamanho de uma máquina virtual e a configuração dos discos dados.

Quando criar imagens do SQL Server, [considere aprovisionamento seu VMs no portal do Azure](virtual-machines-windows-portal-sql-server-provision.md). SQL Server VMs aprovisionado no Portal do Gestor de recursos com implementar todas as seguintes melhores práticas, incluindo a configuração de armazenamento.

Este artigo é com foco nos sobre como obter o *melhor* desempenho para SQL Server no Azure VMs. Se a sua carga de trabalho for menos exigem, não poderá implicar cada optimização indicada abaixo. Considere as necessidades de desempenho e padrões de carga de trabalho à medida que avalia estas recomendações.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="quick-check-list"></a>Lista de verificação rápida

Segue-se uma lista de verificação rápida para um desempenho ideal do SQL Server em máquinas virtuais do Azure:

|Área|Otimizações|
|---|---|
|[Tamanho da memória virtual](#vm-size-guidance)|[DS3](virtual-machines-windows-sizes.md#standard-tier-ds-series) ou superior para SQL Enterprise edition.<br/><br/>[DS2](virtual-machines-windows-sizes.md#standard-tier-ds-series) ou superior para as edições do SQL padrão e da Web.|
|[Armazenamento](#storage-guidance)|Utilize o [armazenamento de Premium](../storage/storage-premium-storage.md). Armazenamento padrão só é recomendado para Dev Center/teste.<br/><br/>Manter a [conta de armazenamento](../storage/storage-create-storage-account.md) e o SQL Server VM na mesma região.<br/><br/>A desativação do Azure [armazenamento geo redundante](../storage/storage-redundancy.md) (geo replicação) na conta de armazenamento.|
|[Discos](#disks-guidance)|Utilize um mínimo de 2 [P30 discos](../storage/storage-premium-storage.md#scalability-and-performance-targets-when-using-premium-storage) (1, ficheiros de registo; 1 para ficheiros de dados e TempDB).<br/><br/>Evite utilizar o sistema operativo ou discos temporários para o armazenamento de base de dados ou o registo.<br/><br/>Ativar leia colocação em cache em discos alojamento ficheiros de dados e TempDB.<br/><br/>Não ativar colocação em cache em discos o ficheiro de registo de alojamento.<br/><br/>Importante: Pare o serviço do SQL Server ao alterar as definições de cache para um disco Azure VM.<br/><br/>Faixas vários dados Azure discos para obter aumento do débito es.<br/><br/>Formatar com tamanhos de alocação documentado.|
|[E/S](#io-guidance)|Active a compressão de página de base de dados.<br/><br/>Ative inicialização ficheiro instantânea para ficheiros de dados.<br/><br/>Limitar ou desativar a duplicação da base de dados.<br/><br/>Desative autoshrink da base de dados.<br/><br/>Mova todas as bases de dados para discos de dados, incluindo bases de dados do sistema.<br/><br/>Deslocar-se do SQL Server erro registo e rastreio directórios do ficheiro para discos de dados.<br/><br/>Configure localizações do ficheiro de cópia de segurança e base de dados predefinido.<br/><br/>Ative páginas bloqueadas.<br/><br/>Aplica correções de desempenho do SQL Server.|
|[Funcionalidade específico](#feature-specific-guidance)|Criar cópias de segurança diretamente ao armazenamento blob.|

Para mais informações sobre *como* e *por que motivo* para tornar estas otimizações, reveja os detalhes e os passos fornecidos nas secções seguintes.

## <a name="vm-size-guidance"></a>Orientação de tamanho VM

Para as aplicações sensíveis a maiúsculas e desempenho, é recomendável que utilize os seguintes [máquinas virtuais tamanhos](virtual-machines-windows-sizes.md):

- **SQL Server Enterprise Edition**: DS3 ou superior

- **SQL Server Standard e Web edições**: DS2 ou superior


## <a name="storage-guidance"></a>Orientação de armazenamento

Suporte de VMs DS-série (juntamente com DSv2 série e série s) [Armazenamento Premium](../storage/storage-premium-storage.md). Armazenamento de Premium é recomendado para todas as cargas de trabalho de produção.

> [AZURE.WARNING] Armazenamento padrão tem largura de banda e latências variados e só é recomendado para Dev Center/testar cargas de trabalho. Cargas de trabalho de produção devem utilizar armazenamento Premium.

Além disso, recomendamos que criar a sua conta de armazenamento Azure no Centro de dados do mesmo como máquinas virtuais do SQL Server para reduzir atrasos de transferência. Ao criar uma conta de armazenamento, desactive a replicação de geo tal como os não é garante a ordem de escrita consistentes em vários discos. Em vez disso, considere configurar uma tecnologia de recuperação do SQL Server falhas entre centros de dados Azure duas. Para mais informações, consulte o artigo [elevada disponibilidade e recuperação de falhas para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="disks-guidance"></a>Orientação de discos

Existem três tipos de disco principal numa VM Azure:

- **Disco do SO**: Quando cria uma máquina de Virtual Azure, a plataforma irá anexar pelo menos um disco (assinalado como a unidade **C** ) para a VM para o disco de sistema operativo. Este disco está um VHD armazenado como BLOBs uma página no armazenamento.
- **Disco temporário**: máquinas virtuais do Azure contêm outro disco denominado o disco temporário (assinalada como o **D**: unidade). Este é um disco no nó que pode ser utilizado para a área de rascunho.
- **Discos de dados**: também pode anexar discos adicionais para o seu computador virtual como discos de dados e, estes serão armazenados nas armazenamento como blobs de página.

As seguintes secções descrevem as recomendações para utilizar estes discos diferentes.

### <a name="operating-system-disk"></a>Disco do sistema operativo

Um disco do sistema operativo é um VHD que pode de arranque e montagem como uma versão a execução de um sistema operativo e é assinalada como unidade **C** .

Colocação em cache política no disco do sistema operativo predefinido é de **Leitura/escrita**. Para as aplicações sensíveis a maiúsculas e desempenho, recomendamos que utilize discos de dados em vez do disco do sistema operativo. Consulte a secção em dados discos abaixo.

### <a name="temporary-disk"></a>Disco temporário

A unidade de armazenamento temporário assinalada como o **D**: unidade, não é mantida ao armazenamento de Blobs do Azure. Não armazenar os ficheiros de base de dados de utilizador ou ficheiros de registo do utilizador da transação no **D**: unidade.

Para D série, Dv2 série e VMs série G, a unidade de temporária estes VMs é baseado em SSD. Se a sua carga de trabalho faz utilização frequente do TempDB (por exemplo, para objetos temporários ou complexas associações), armazenar TempDB na unidade **D** poderá resultar em débito de TempDB superior e inferior TempDB latência.

Para VMs que suportam o armazenamento de prémio (DS série, DSv2 série e série s), recomendamos que armazenar TempDB e/ou extensões de conjunto de memória intermédia num disco que suporta o armazenamento de Premium com leitura colocação em cache ativado. Existe uma exceção para esta recomendação; Se a utilização do TempDB consome muitos escrita, é possível alcançar um melhor desempenho, guardando-TempDB na unidade **D** local, que também é baseado em SSD nestes tamanhos de máquina.

### <a name="data-disks"></a>Discos de dados

- **Utilizar dados discos para dados e ficheiros de registo**: no mínimo, utilize 2 Premium de armazenamento de [discos P30](../storage/storage-premium-storage.md#scalability-and-performance-targets-when-using-premium-storage) onde um disco contém os ficheiros de registo e a outra a ficheiros de dados e TempDB.

- **Repartição do disco**: para obter mais débito, pode adicionar dados adicionais discos e utilizar repartição do disco. Para determinar o número de discos de dados, é necessário analisar o número de IOPS disponíveis para os seus discos de dados e de registo. Para essas informações, consulte o artigo as tabelas IOPS por tamanho do disco e [tamanho da memória virtual](virtual-machines-windows-sizes.md) o seguinte artigo: [Utilizar o armazenamento de Premium para discos](../storage/storage-premium-storage.md). Utilize o seguinte estas diretrizes:

    - Para o Windows 8/Windows Server 2012 ou posterior, utilize [Espaços de armazenamento](https://technet.microsoft.com/library/hh831739.aspx). Defina o tamanho da faixa para 64 KB das cargas de trabalho OLTP e KB 256 para cargas de trabalho de armazenamento de dados para evitar impacto no desempenho devido ao alinhamento partição. Além disso, defina o número de colunas = número de discos físicos. Para configurar um espaço de armazenamento com mais do que 8 discos tem de utilizar o PowerShell (não IU Gestor de servidor) para definir o número de colunas para fazer corresponder o número de discos explicitamente. Para obter mais informações sobre como configurar [Os espaços de armazenamento](https://technet.microsoft.com/library/hh831739.aspx), consulte [Cmdlets de espaços de armazenamento no Windows PowerShell](https://technet.microsoft.com/library/jj851254.aspx)

    - Para o Windows 2008 R2 ou anterior, pode utilizar discos dinâmicos (volumes SO riscas) e o tamanho da faixa é sempre 64 KB. Tenha em atenção que esta opção é preterida a partir do Windows 8/Windows Server 2012. Para obter informações, consulte a declaração de suporte no [serviço de disco virtuais é na transição para API de gestão de armazenamento do Windows](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx).

    - Se a sua carga de trabalho não está registo intenso e não é necessário IOPs dedicados, pode configurar único agrupamento de armazenamento. Caso contrário, crie dois conjuntos de armazenamento, uma para os ficheiros de registo e outro agrupamento de armazenamento para o ficheiro de dados (s) e TempDB. Determine o número de discos associados a cada agrupamento de armazenamento com base nas suas expectativas de carregamento. Tenha em atenção que diferentes tamanhos VM permitir que um número diferente de discos dados anexados. Para mais informações, consulte o artigo [tamanhos para máquinas virtuais](virtual-machines-windows-sizes.md).

    - Se não estiver a utilizar o armazenamento de prémio (Dev Center/testar cenários), a recomendação é adicionar o número máximo de discos de dados suportados pelo seu [tamanho da memória virtual](virtual-machines-windows-sizes.md) e utilizar repartição do disco.

- **Política de colocação em cache**: discos de dados para o armazenamento de Premium, ativar colocação em cache leitura nos discos dados alojamento apenas TempDB e ficheiros de dados. Se não estiver a utilizar o armazenamento de Premium, não ativar qualquer colocação em cache em qualquer discos de dados. Para obter instruções sobre como configurar a cache de disco, consulte os seguintes tópicos: [Conjunto AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) e [AzureDataDisk conjunto](https://msdn.microsoft.com/library/azure/jj152851.aspx).

    >[AZURE.WARNING] Pare o serviço do SQL Server ao alterar a definição de cache de discos Azure VM para evitar a possibilidade de danos da base de dados.

- **Tamanho da unidade de atribuição NTFS**: quando a formatação do disco de dados, é recomendável que utilize um tamanho de unidade de atribuição de 64 KB para dados e ficheiros de registo, bem como TempDB.

- **Melhores práticas de disco gestão**: Quando remover um disco de dados ou alterar o tipo de cache, pare o serviço do SQL Server durante a alteração. Quando as definições de colocação em cache são alteradas no disco SO, Azure deixa a VM, altera o tipo de cache e reinicia o VM. Quando as definições da cache de um disco de dados forem alteradas, a VM não está parada, mas o disco de dados está desligado da VM durante a alteração e, em seguida, verificaçoes.

    >[AZURE.WARNING] Falha para parar o serviço do SQL Server durante estas operações pode causar danos de base de dados.

## <a name="io-guidance"></a>Orientação e/s

- Obter os melhores resultados com armazenamento Premium são realizados quando parallelize a aplicação e os pedidos. Armazenamento de Premium foi concebido para obter cenários onde a profundidade de fila IO é maior do que 1, para que irá ver pouca ou nenhuma ganhos de desempenho para pedidos de série threaded único (mesmo se estiverem armazenamento intenso). Por exemplo, isto pode afetar os resultados por tópicos único teste das ferramentas de análise de desempenho, tal como SQLIO.

- Considere utilizar [compressão de página de base de dados](https://msdn.microsoft.com/library/cc280449.aspx) , tal como o pode ajudar a melhorar o desempenho das cargas de trabalho intensivos e/s. No entanto, a compressão de dados pode aumentar o consumo de CPU no servidor de base de dados.

- Considere ativar inicialização instantâneas ficheiro reduzir o tempo necessário para a atribuição de inicial do ficheiro. Para tirar partido da inicialização ficheiro instantâneas, conceder a conta de serviço do SQL Server (MSSQLSERVER) com SE_MANAGE_VOLUME_NAME e adicioná-lo para a política de segurança de **Efetuar tarefas de manutenção de Volume** . Se estiver a utilizar uma imagem de plataforma do SQL Server Azure, a conta predefinida do serviço (NT Service\MSSQLSERVER) não é adicionada à política de segurança de **Efetuar tarefas de manutenção de Volume** . Por outras palavras, inicialização ficheiro instantâneas não está activada numa imagem de plataforma do SQL Server Azure. Depois de adicionar a conta de serviço do SQL Server para a política de segurança de **Efetuar tarefas de manutenção de Volume** , reinicie o serviço do SQL Server. Podem existir considerações de segurança para utilizar esta funcionalidade. Para mais informações, consulte o artigo [Inicialização de ficheiro de base de dados](https://msdn.microsoft.com/library/ms175935.aspx).

- **duplicação** é considerado como meramente uma contingência para inesperados crescimento. Não gerir o seu crescimento dos dados e inicie sessão no dia a dia com duplicação. Se for utilizada duplicação, previamente aumentar o ficheiro com o parâmetro de tamanho.

- Certifique-se de **autoshrink** está desativado para evitar sobrecarga desnecessária que negativa pode afetar o desempenho.

- Mova todas as bases de dados para discos de dados, incluindo bases de dados do sistema. Para mais informações, consulte o artigo [Mover bases de dados do sistema](https://msdn.microsoft.com/library/ms345408.aspx).

- Deslocar-se do SQL Server erro registo e rastreio directórios do ficheiro para discos de dados. Isto pode ser feito no Gestor de configuração do SQL Server ao clicar a instância do SQL Server e selecionar propriedades. As definições de ficheiro de registo e rastreio erro podem ser alteradas no separador **Parâmetros de arranque** . O diretório de falha de sistema é especificado no separador **Avançadas** . A seguinte captura de ecrã mostra onde procurar o parâmetro de arranque do registo de erros.

    ![Captura de ecrã do SQL ErrorLog](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

- Configure localizações do ficheiro de cópia de segurança e base de dados predefinido. Utilize as recomendações neste tópico e efetue as alterações na janela servidor de propriedades. Para obter instruções, consulte o artigo [Ver ou alterar as localizações predefinido para dados e ficheiros de registo (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). A captura de ecrã seguinte demonstra onde efetuar estas alterações.

    ![Ficheiros de registo de dados do SQL e cópia de segurança](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)

- Ative páginas bloqueadas reduzir IO e outras atividades de paginação. Para mais informações, consulte o artigo [Ativar as páginas de bloqueio na opção de memória (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

- Se estiver a executar o SQL Server 2012, instale o Service Pack 1 cumulativa atualização 10. Esta atualização contém a fix para obter um desempenho fraca em i / quando executar selecione instrução de tabela temporária no SQL Server 2012. Para obter informações, consulte este [artigo da base de dados de conhecimento](http://support.microsoft.com/kb/2958012).

- Considere comprimir os ficheiros de dados ao transferir/fora do Azure.

## <a name="feature-specific-guidance"></a>Orientações específicas de funcionalidade

Algumas implementações poderão alcançar vantagens de desempenho adicionais utilizar técnicas de configuração mais avançadas. A lista seguinte destaca algumas funcionalidades do SQL Server que podem ajudá-lo para alcançar um melhor desempenho:

- **Cópia de segurança ao armazenamento Azure**: quando efetuar cópias de segurança para SQL Server em máquinas virtuais do Azure a executar o, pode utilizar [Cópia de segurança do SQL Server para URL](https://msdn.microsoft.com/library/dn435916.aspx). Esta funcionalidade está disponível ao iniciar com o SQL Server 2012 SP1 CU2 e recomendados para cópia de segurança para os discos dados anexados. Quando lhe cópia de segurança/restauro para a Azure armazenamento, siga as recomendações fornecidas na [Cópia de segurança do SQL Server para URL melhores práticas e resolução de problemas e restaurar a partir de cópias de segurança armazenados no armazenamento Azure](https://msdn.microsoft.com/library/jj919149.aspx). Também pode automatizar estas cópias de segurança com [Cópia de segurança automatizado para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-classic-sql-automated-backup.md).

    Antes de SQL Server 2012, pode utilizar a [Cópia de segurança do SQL Server para a ferramenta de Azure](https://www.microsoft.com/download/details.aspx?id=40740). Esta ferramenta pode ajudar a aumentar o débito cópia de segurança utilizando vários destinos faixa de cópia de segurança.

- **Ficheiros de dados do SQL Server no Azure**: esta funcionalidade nova, [Ficheiros de dados do SQL Server no Azure](https://msdn.microsoft.com/library/dn385720.aspx), está disponível ao iniciar com o SQL Server 2014. Com o SQL Server com ficheiros de dados no Azure demonstra características de desempenho comparáveis como utilizando dados Azure discos.

## <a name="next-steps"></a>Próximos passos

Se estiver interessado em explorar o SQL Server e armazenamento de Premium mais detalhados, consulte o artigo [Utilizar Azure Premium armazenamento com o SQL Server em máquinas virtuais](virtual-machines-windows-classic-sql-server-premium-storage.md).

Para melhores práticas de segurança, consulte o artigo [Considerações de segurança para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-security.md).

Rever os outros tópicos de máquina de Virtual do SQL Server no [SQL Server na descrição geral de máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).
