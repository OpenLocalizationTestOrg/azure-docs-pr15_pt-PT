<properties
    pageTitle="Migrar para o armazenamento do Azure Premium | Microsoft Azure"
    description="Migre máquinas virtuais existentes para o armazenamento do Windows Azure Premium. Armazenamento de Premium oferece suporte de alto desempenho, baixa latência disco para posso/O-com um grau elevado das cargas de trabalho em execução no máquinas virtuais do Azure."
    services="storage"
    documentationCenter="na"
    authors="aungoo-msft"
    manager="tadb"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/21/2016"
    ms.author="yuemlu"/>


# <a name="migrating-to-azure-premium-storage"></a>Migrar para o armazenamento do Azure Premium

## <a name="overview"></a>Descrição geral

Armazenamento de Premium Azure fornece suporte do disco de alto desempenho, baixa latência para máquinas virtuais executadas posso/the-com um grau elevado cargas de trabalho. Pode tirar partido da velocidade e o desempenho destes discos por migrar discos VM da sua aplicação para o armazenamento do Windows Azure Premium.

É o objetivo deste guia para o ajudar a novos utilizadores de armazenamento de Premium Azure melhor preparar-se para fazer uma transição suave a partir do seu sistema atual ao armazenamento de Premium. O guia de endereços três dos componentes chave este processo: 

  - [Planear a migração para o armazenamento de Premium](#plan-the-migration-to-premium-storage)
  - [Preparar e copiar discos rígido virtuais (VHDs) para o armazenamento de Premium](#prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage)
  - [Criar máquina de Virtual Azure utilizando o armazenamento de Premium](#create-azure-virtual-machine-using-premium-storage)

Pode migrar VMs a partir de outras plataformas ao armazenamento de Premium do Azure ou migrar existente Azure VMs a partir do armazenamento padrão para o armazenamento de Premium. Este guia abrange os passos para ambos os dois cenários. Siga os passos especificados na secção relevante dependendo do seu cenário.

>[AZURE.NOTE] Pode encontrar uma descrição geral da funcionalidade e preços de armazenamento de Premium no armazenamento Premium: [Armazenamento de alto desempenho para cargas de trabalho do Azure Máquina Virtual](storage-premium-storage.md). Recomendamos que migrar qualquer disco de máquina virtual exigir alta IOPS ao armazenamento de Premium do Azure para um melhor desempenho para a sua aplicação. Se o disco não requer IOPS alta, pode limitar os custos, mantendo-lo no armazenamento padrão, que armazena os dados do disco máquina virtual em unidades de disco rígido (HDDs) em vez de SSDs.

Concluir o processo de migração na sua totalidade precisem de ações adicionais antes e depois dos passos fornecidos neste guia. Alguns exemplos incluem configurar redes virtuais ou os pontos finais ou façam alterações de código na própria aplicação que precisem de algum tempo de inatividade na sua aplicação. Estas ações são exclusivas para cada aplicação e, devem concluir-as juntamente com os passos fornecidos neste guia para fazer a transição completa ao armazenamento de Premium tão simples quanto possível.


## <a name="plan-the-migration-to-premium-storage"></a>Planear a migração para o armazenamento de Premium

Esta secção assegura que está pronto para seguir os passos de migração neste artigo e ajuda-o a tomar a decisão melhor nos tipos de VM e disco.

### <a name="prerequisites"></a>Pré-requisitos
- Terá de uma subscrição do Azure. Se não tiver uma, pode criar uma subscrição de um mês [versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/) ou visite [Azure preços](https://azure.microsoft.com/pricing/) para obter mais opções.
- Para executar os cmdlets do PowerShell, terá do módulo do Microsoft Azure PowerShell. Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para as instruções de instalação e ponto de instalação.
- Quando planeia utilizar VMs Azure em execução no armazenamento Premium, tem de utilizar os VMs com capacidade de armazenamento de Premium. Pode utilizar discos padrão e armazenamento de Premium com VMs com capacidade de armazenamento de Premium. Discos de armazenamento Premium estarão disponíveis com mais tipos de VM no futuro. Para mais informações sobre todos os tipos de disco do Azure VM e tamanhos disponíveis, consulte o artigo [tamanhos para máquinas virtuais](../virtual-machines/virtual-machines-windows-sizes.md) e [tamanhos](../cloud-services/cloud-services-sizes-specs.md)para serviços em nuvem.

### <a name="considerations"></a>Considerações sobre

Um VM Azure suporta anexar vários discos Premium armazenamento para que as suas aplicações podem ter até 64 TB de armazenamento por VM. Com o armazenamento de Premium, as aplicações podem alcançar 80.000 IOPS (operações de entrada/saída por segundo) por VM e 2000 MB por segundo débito do disco por VM com latências extremamente baixos para operações de leitura. Tem opções numa variedade de VMs e discos. É nesta secção para ajudá-lo a encontrar uma opção que melhor se adequa às sua carga de trabalho.

#### <a name="vm-sizes"></a>Tamanhos VM
As especificações de tamanho do Azure VM estão listadas na [tamanhos para máquinas virtuais](../virtual-machines/virtual-machines-windows-sizes.md). Reveja as características de desempenho da máquinas virtuais que funcionam com o armazenamento de Premium e escolha o tamanho da memória virtual mais adequado que melhor se adequa às sua carga de trabalho. Certifique-se de que existe largura de banda suficiente disponíveis no seu VM para orientar o tráfego de disco.


#### <a name="disk-sizes"></a>Tamanhos de discos
Existem três tipos de discos que podem ser utilizados com a VM e cada tem IOPs específicos e débito limites. Tenha em consideração estes limites quando escolher o tipo de disco para sua VM com base nas necessidades da sua aplicação em termos de capacidade, desempenho e escalabilidade e pico for carregada.

|Tipo de armazenamento de disco Premium|P10|P 20|P30|
|:---:|:---:|:---:|:---:|
|Tamanho do disco|128 GB|512 GB|1024 GB (1 TB)|
|IOPS por disco|500|2300|5000|
|Débito por disco|100 MB por segundo|150 MB por segundo|200 MB por segundo|

Dependendo da sua carga de trabalho, determine se discos dados adicionais são necessários para o VM. Pode anexar vários discos dados persistentes para sua VM. Se for necessário, pode faixas por discos para aumentar a capacidade e o desempenho do volume. (Consulte o que é repartição do disco [aqui](storage-premium-storage-performance.md#disk-striping).) Se faixas discos de dados de armazenamento de Premium utilizando [Espaços de armazenamento][4], deverá configure-o com uma coluna para cada disco que é utilizado. Caso contrário, o desempenho global do volume às riscas pode ser inferior a esperado devido a distribuição desigual de tráfego entre discos. Para Linux VMs pode utilizar o utilitário *mdadm* para alcançar o mesmo. Consulte o artigo [Configurar o Software RAID no Linux](../virtual-machines/virtual-machines-linux-configure-raid.md) para obter detalhes.

#### <a name="storage-account-scalability-targets"></a>Destinos de escalabilidade de conta de armazenamento
Contas de armazenamento de Premium têm os seguintes objectivos escalabilidade além a [escalabilidade de armazenamento do Azure e metas de desempenho](storage-scalability-targets.md). Se aos requisitos da aplicação excederem os destinos escalabilidade de uma conta de armazenamento única, construir a sua aplicação para que utilize várias contas de armazenamento e partição os seus dados nessas contas de armazenamento.

|Capacidade total de conta|Largura de banda total de uma conta de armazenamento localmente redundantes|
|:--|:---|
|Capacidade de disco: 35TB<br />Instantâneo de capacidade: 10 TB|Gigabits até 50 por segundo para entrada + saída|

Para mais informações sobre especificações de armazenamento de Premium, consulte o artigo [escalabilidade e desempenho destinos quando utilizar o armazenamento de Premium](storage-premium-storage.md#scalability-and-performance-targets-when-using-premium-storage).

#### <a name="disk-caching-policy"></a>Política de colocação em cache do disco
Por predefinição, o disco colocação em cache política é *Só de leitura* para todos os dados de Premium discos e *Leitura e escrita* para o disco de sistema operativo Premium anexado a VM. Esta definição de configuração é recomendada para alcançar um desempenho ideal para IOs a aplicação. Para discos de dados de escrita grossa ou só de leitura (como ficheiros de registo do SQL Server), desative disco colocação em cache para que é possível alcançar um melhor desempenho da aplicação. As definições de cache de discos dados existentes podem ser atualizadas com [Azure Portal](https://portal.azure.com) ou o parâmetro *- HostCaching* do cmdlet *AzureDataDisk conjunto* .

#### <a name="location"></a>Localização
Selecione uma localização onde o armazenamento do Windows Azure Premium está disponível. Consulte o artigo [Dos serviços do Azure por região](https://azure.microsoft.com/regions/#services) para obter informações atualizadas em localizações disponíveis. VMs localizados na mesma região como a conta de armazenamento que armazena os discos para a VM irão dar-muito melhor desempenho que se estiverem em regiões em separado.

#### <a name="other-azure-vm-configuration-settings"></a>Outras definições de configuração do Azure VM
Ao criar uma VM Azure, lhe-á pedido para configurar algumas definições de VM. Lembre-se de que algumas definições são corrigidas para o tempo de vida da VM, enquanto pode modificar ou adicionar outras pessoas mais tarde. Reveja estas definições de configuração do Azure VM e certifique-se de que estes estão configurados corretamente para que correspondam aos requisitos de carga de trabalho.

### <a name="optimization"></a>Otimização

[Armazenamento do Windows azure Premium: Design de alto desempenho](storage-premium-storage-performance.md) fornece diretrizes para criar aplicações de alto desempenho utilizando o armazenamento do Azure Premium. Pode seguir as diretrizes combinadas com desempenho melhores práticas aplicáveis às tecnologias utilizadas pela sua aplicação.


## <a name="prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage"></a>Preparar e copiar discos rígido virtuais (VHDs) para o armazenamento de Premium

A secção seguinte fornece diretrizes para preparar VHDs a partir do seu VM e copiar VHDs para o armazenamento do Windows Azure.

- [Cenário 1: "Posso estou a migrar VMs Azure existentes para o armazenamento do Azure Premium."](#scenario1)
- [Cenário 2: "Posso estou a migrar VMs a partir do noutras plataformas ao armazenamento de Premium Azure."](#scenario2)

### <a name="prerequisites"></a>Pré-requisitos

Para preparar os VHDs migração, terá de:

- Uma subscrição do Azure, uma conta de armazenamento e um contentor nessa conta de armazenamento para o qual pode copiar o VHD. Tenha em atenção que a conta de armazenamento de destino pode ser uma conta padrão ou Premium armazenamento dependendo do seu requisito.
- Uma ferramenta generalizar VHD se planeia criar várias instâncias VM a partir dos mesmos. Por exemplo, sysprep para Windows ou Subf-sysprep para Ubuntu.
- Uma ferramenta para carregar o ficheiro VHD para a conta de armazenamento. Consulte o artigo [Transferir dados com o AzCopy da linha de comandos utilitário](storage-use-azcopy.md) ou utilize um [Explorador de armazenamento Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx). Este guia descreve copiar o seu VHD utilizando a ferramenta de AzCopy.

> [AZURE.NOTE] Se escolher opção Copiar síncrono com AzCopy, para um desempenho ideal, copie o VHD ao executar uma destas ferramentas a partir de um VM Azure que está na mesma região como a conta de armazenamento de destino. Se está a copiar um VHD a partir de um VM Azure numa região diferente, o desempenho pode ser mais lento.
>
> Para copiar uma grande quantidade de dados através de largura de banda limitada, considere [utilizar o serviço de Azure importar/exportar para o qual transferir dados ao armazenamento Blob](storage-import-export-service.md); Esta opção permite-lhe transferir os seus dados por unidades de disco rígido envio para um centro de dados do Azure. Pode utilizar o serviço do Azure importar/exportar para copiar dados para apenas uma conta de armazenamento padrão. Assim que estiverem os dados na sua conta de armazenamento padrão, pode utilizar a [API de BLOBs cópia](https://msdn.microsoft.com/library/azure/dd894037.aspx) ou AzCopy para transferir os dados à sua conta de armazenamento premium.
>
> Tenha em atenção que a Microsoft Azure suporta apenas tamanho fixo VHD ficheiros. Ficheiros VHDX ou VHDs dinâmicos não são suportados. Se tiver um VHD dinâmico, pode convertê-la para tamanho fixo, utilizando o cmdlet [VHD converter](http://technet.microsoft.com/library/hh848454.aspx) .

### <a name="scenario1"></a>Cenário 1: "Posso estou a migrar VMs Azure existentes para o armazenamento do Azure Premium."

Se estiver a migrar existente Azure VMs, parar a VM, preparar VHDs pelo tipo de VHD que pretende e, em seguida, copie o VHD com AzCopy ou PowerShell.

A VM tem de estar completamente para baixo para migrar um estado limpo. Haverá um tempo de inatividade até a migração ser concluída.

#### <a name="step-1-prepare-vhds-for-migration"></a>Passo 1. Preparar VHDs migração

Se estiver a migrar existente Azure VMs ao armazenamento de Premium, poderá ser seu VHD:

- Uma imagem GRG sistema operativo
- Um disco exclusivas do sistema operativo
- Um disco de dados

Abaixo iremos guiá através destes 3 cenários para preparar o seu VHD.

##### <a name="use-a-generalized-operating-system-vhd-to-create-multiple-vm-instances"></a>Utilizar um VHD GRG do sistema operativo para criar várias instâncias VM

Se está a carregar um VHD que será utilizado para criar várias instâncias de Azure VM genéricas, primeiro tem generalizar VHD utilizando um utilitário sysprep. Isto aplica-se para um VHD que está no local ou na nuvem. Sysprep remove todas as informações específicas do computador do VHD.

>[AZURE.IMPORTANT] Tire um instantâneo ou cópia de segurança a VM antes generalizá-lo. Em execução sysprep irá parar e retirar a instância VM. Siga os passos abaixo para sysprep um VHD de sistema operativo Windows. Tenha em atenção que a executar o comando Sysprep necessitam que encerrar a máquina virtual. Para mais informações sobre o Sysprep, consulte o artigo [Descrição geral de Sysprep](http://technet.microsoft.com/library/hh825209.aspx) ou [Referência técnica do Sysprep](http://technet.microsoft.com/library/cc766049.aspx).

1. Abra uma janela de linha de comandos como administrador.
2. Introduza o seguinte comando para abrir o Sysprep:

        %windir%\system32\sysprep\sysprep.exe

3. Na ferramenta de preparação de sistema, selecione sistema introduza Out of Box experiência (OOBE), selecione a caixa de verificação Generalize, selecione **encerramento**e, em seguida, clique em **OK**, conforme mostrado na imagem abaixo. Sysprep irá generalizar o sistema operativo e encerrar o sistema.

    ![][1]

Para um VM Ubuntu, utilize Subf sysprep para alcançar o mesmo. Consulte o artigo [Subf sysprep](http://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) para obter mais detalhes. Consulte também algumas das abrir origem [Linux servidor de aprovisionamento de software](http://www.cyberciti.biz/tips/server-provisioning-software.html) para outros sistemas operativos de Linux.

##### <a name="use-a-unique-operating-system-vhd-to-create-a-single-vm-instance"></a>Utilizar um VHD exclusivo do sistema operativo para criar uma única ocorrência VM

Se tiver uma aplicação em execução na VM solicitar os dados específicos do computador, não generalize VHD. Um VHD GRG não pode ser utilizado para criar uma instância do Azure VM exclusiva. Por exemplo, se tiver o controlador de domínio no seu VHD, executar sysprep irá torná-la ineficaz como um controlador de domínio. Reveja as aplicações em execução no seu VM e o impacto da execução do sysprep nos mesmos antes de generalizar VHD.

##### <a name="register-data-disk-vhd"></a>Registar dados disco VHD

Se tiver discos de dados no Azure a ser migrado, tem Certifique-se de que são encerrar os VMs que utilizam estes discos de dados.

Siga os passos descritos abaixo para copiar VHD ao armazenamento de Premium do Azure e registá-lo como um disco aprovisionada dados.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Passo 2. Criar o destino para sua VHD

Crie uma conta de armazenamento para manter o seu VHDs. Quando planear onde pretende guardar o seu VHDs, tenha em consideração os seguintes pontos:

- O destino da conta de armazenamento Premium.
- A localização da conta de armazenamento tem de ser igual ao armazenamento de Premium capazes Azure VMs irá criar na fase final. Pode copiar para uma nova conta de armazenamento, ou o plano para utilizar a mesma conta de armazenamento com base nas suas necessidades.
- Copiar e guarde a chave de conta de armazenamento da conta de armazenamento de destino para a próxima fase.

Para discos de dados, pode optar por manter alguns discos dados numa conta de armazenamento padrão (por exemplo, discos que tenham armazenamento arrefecedor), mas recomendamos vivamente que mover todos os dados de carga de trabalho de produção utilizar o armazenamento de premium.

#### <a name="copy-vhd-with-azcopy-or-powershell"></a>Passo 3. Copiar VHD com AzCopy ou PowerShell

Terá de localizar a sua chave de conta de contentor caminho e o armazenamento, para processar qualquer uma destas duas opções. Pode encontrar a chave de conta de caminho e o armazenamento do contentor no **Portal do Azure** > **armazenamento**. O URL do contentor serão como "https://myaccount.blob.core.windows.net/mycontainer/".

##### <a name="option-1-copy-a-vhd-with-azcopy-asynchronous-copy"></a>Opção 1: Copiar um VHD com AzCopy (cópia assíncrona)

Utilizar AzCopy, pode carregar facilmente o VHD através da Internet. Dependendo do tamanho dos VHDs, este poderá demorar tempo. Lembre-se verificar se os limites de penetração/saída da conta de armazenamento ao utilizar esta opção. Consulte o artigo [escalabilidade de armazenamento do Azure e desempenho destinos](storage-scalability-targets.md) para obter detalhes.

1. Transferir e instalar o AzCopy a partir daqui: [versão mais recente do AzCopy](http://aka.ms/downloadazcopy)
2. Abra o PowerShell do Azure e vá para a pasta onde o AzCopy está instalado.
3. Utilize o seguinte comando para copiar o ficheiro VHD a partir de "Origem" para "Destino".

        AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>

    Exemplo:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /Pattern:abc.vhd

    Eis as descrições dos parâmetros utilizados no comando AzCopy:

 - * */Origem: * &lt;origem&gt;:** * localização da pasta ou do URL de contentor de armazenamento que contém o VHD.
 - * */SourceKey: * &lt;chave da conta de origem&gt;:** * chave da conta de armazenamento da conta de armazenamento de origem.
 - * */Dest: * &lt;destino&gt;:** * URL do contentor de armazenamento para copiar o VHD para.
 - * */DestKey: * &lt;chave da conta de destino&gt;:** * chave da conta de armazenamento da conta de armazenamento de destino.
 - * */Padrão: * &lt;de nome de ficheiro&gt;:** * especificar o nome de ficheiro do VHD para copiar.

Para obter detalhes sobre como utilizar a ferramenta de AzCopy, consulte o artigo [Transferir dados com o utilitário AzCopy da linha de comandos](storage-use-azcopy.md).

##### <a name="option-2-copy-a-vhd-with-powershell-synchronized-copy"></a>Opção 2: Copiar um VHD com PowerShell (sincronizado cópia)

Também pode copiar o ficheiro VHD utilizando o cmdlet do PowerShell AzureStorageBlobCopy iniciar. Utilize o seguinte comando no Azure PowerShell para copiar VHD. Substitua os valores na <> valores correspondentes da sua conta de armazenamento de origem e destino. Para utilizar este comando, tem de ter um contentor denominado vhds na sua conta de armazenamento de destino. Se o contentor não existir, crie uma antes de executar o comando.

    $sourceBlobUri = <source-vhd-uri>

    $sourceContext = New-AzureStorageContext  –StorageAccountName <source-account> -StorageAccountKey <source-account-key>

    $destinationContext = New-AzureStorageContext  –StorageAccountName <dest-account> -StorageAccountKey <dest-account-key>

    Start-AzureStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer <dest-container> -DestBlob <dest-disk-name> -DestContext $destinationContext

Exemplo:

    C:\PS> $sourceBlobUri = "https://sourceaccount.blob.core.windows.net/vhds/myvhd.vhd"

    C:\PS> $sourceContext = New-AzureStorageContext  –StorageAccountName "sourceaccount" -StorageAccountKey "J4zUI9T5b8gvHohkiRg"

    C:\PS> $destinationContext = New-AzureStorageContext  –StorageAccountName "destaccount" -StorageAccountKey "XZTmqSGKUYFSh7zB5"

    C:\PS> Start-AzureStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer "vhds" -DestBlob "myvhd.vhd" -DestContext $destinationContext

### <a name="scenario2"></a>Cenário 2: "Posso estou a migrar VMs a partir do noutras plataformas ao armazenamento de Premium Azure."

Se estiver a migrar VHD a partir de que não sejam - Azure armazenamento em nuvem para Azure, primeiro tem de exportar o VHD para um diretório local. Têm o caminho completo de origem do directório local onde o VHD está armazenado útil e, em seguida, utilizar AzCopy para a carregar para o armazenamento do Windows Azure.

#### <a name="step-1-export-vhd-to-a-local-directory"></a>Passo 1. Exportar VHD para um diretório local

##### <a name="copy-a-vhd-from-aws"></a>Copiar um VHD de AWS

1. Se estiver a utilizar AWS, exporte a instância CE da Comissão2 para um VHD num balde Amazon S3. Siga os passos descritos na documentação Amazon para exportar Amazon CE da Comissão2 instâncias instalar a ferramenta de interface de comandos (CLI) CE da Amazon Comissão2 e executar o comando Criar-instância exportar tarefa para exportar a instância CE da Comissão2 para um ficheiro VHD. Certifique-se de que utilizar **VHD** para o disco #95; imagem & #95; Variável de formato ao executar o comando **criar a instância-exportar-tarefa** . O ficheiro exportado do VHD é guardado no balde Amazon S3 que designar durante o processo.

        aws ec2 create-instance-export-task --instance-id ID --target-environment TARGET_ENVIRONMENT \
        --export-to-s3-task DiskImageFormat=DISK_IMAGE_FORMAT,ContainerFormat=ova,S3Bucket=BUCKET,S3Prefix=PREFIX

2. Transfira o ficheiro VHD a partir de balde S3. Selecione o ficheiro VHD, em seguida, **ações** > **Transferir**.

    ![][3]

##### <a name="copy-a-vhd-from-other-non-azure-cloud"></a>Copie um VHD da nuvem outras não Azure

Se estiver a migrar VHD a partir de que não sejam - Azure armazenamento em nuvem para Azure, primeiro tem de exportar o VHD para um diretório local. Copie o caminho completo de origem do directório local onde se encontra armazenado VHD.

##### <a name="copy-a-vhd-from-on-premise"></a>Copie um VHD no local

Se estiver a migrar VHD a partir de um ambiente no local, terá do caminho de origem concluída a localização onde armazenou VHD. O caminho de origem dever-se uma partilha de localização ou ficheiro do servidor.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Passo 2. Criar o destino para sua VHD

Crie uma conta de armazenamento para manter o seu VHDs. Quando planear onde pretende guardar o seu VHDs, tenha em consideração os seguintes pontos:

- A conta de armazenamento de destino poderão ser armazenamento padrão ou premium, dependendo do seu requisito de aplicação.
- Região de conta de armazenamento tem de ser igual ao armazenamento de Premium capazes Azure VMs irá criar na fase final. Pode copiar para uma nova conta de armazenamento, ou o plano para utilizar a mesma conta de armazenamento com base nas suas necessidades.
- Copiar e guarde a chave de conta de armazenamento da conta de armazenamento de destino para a próxima fase.

Recomendamos vivamente que mover todos os dados de carga de trabalho de produção utilizar o armazenamento de premium.

#### <a name="step-3-upload-the-vhd-to-azure-storage"></a>Passo 3. Carregar o VHD ao armazenamento Azure

Agora que tem o VHD no diretório local, pode utilizar AzCopy ou AzurePowerShell para carregar o ficheiro. vhd para o armazenamento do Windows Azure. Ambas as opções são fornecidas aqui:

##### <a name="option-1-using-azure-powershell-add-azurevhd-to-upload-the-vhd-file"></a>Opção 1: Utilizar Azure PowerShell adicionar-AzureVhd para carregar o ficheiro. vhd

    Add-AzureVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo>

Um exemplo <Uri> poderá ser **_"https://storagesample.blob.core.windows.net/mycontainer/blob1.vhd"_**. Um exemplo <FileInfo> poderá ser **_"C:\path\to\upload.vhd"_**.

##### <a name="option-2-using-azcopy-to-upload-the-vhd-file"></a>Opção 2: Utilizar AzCopy para carregar o ficheiro. vhd

Utilizar AzCopy, pode carregar facilmente o VHD através da Internet. Dependendo do tamanho dos VHDs, este poderá demorar tempo. Lembre-se verificar se os limites de penetração/saída da conta de armazenamento ao utilizar esta opção. Consulte o artigo [escalabilidade de armazenamento do Azure e desempenho destinos](storage-scalability-targets.md) para obter detalhes.

1. Transferir e instalar o AzCopy a partir daqui: [versão mais recente do AzCopy](http://aka.ms/downloadazcopy)
2. Abra o PowerShell do Azure e vá para a pasta onde o AzCopy está instalado.
3. Utilize o seguinte comando para copiar o ficheiro VHD a partir de "Origem" para "Destino".

        AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>

    Exemplo:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /BlobType:page /Pattern:abc.vhd

    Eis as descrições dos parâmetros utilizados no comando AzCopy:

 - * */Origem: * &lt;origem&gt;:** * localização da pasta ou do URL de contentor de armazenamento que contém o VHD.
 - * */SourceKey: * &lt;chave da conta de origem&gt;:** * chave da conta de armazenamento da conta de armazenamento de origem.
 - * */Dest: * &lt;destino&gt;:** * URL do contentor de armazenamento para copiar o VHD para.
 - * */DestKey: * &lt;chave da conta de destino&gt;:** * chave da conta de armazenamento da conta de armazenamento de destino.
 - **/BlobType: página:** Especifica que o destino é um blob de página.
 - * */Padrão: * &lt;de nome de ficheiro&gt;:** * especificar o nome de ficheiro do VHD para copiar.

Para obter detalhes sobre como utilizar a ferramenta de AzCopy, consulte o artigo [Transferir dados com o utilitário AzCopy da linha de comandos](storage-use-azcopy.md).

##### <a name="other-options-for-uploading-a-vhd"></a>Outras opções para carregar um VHD

Também pode carregar um VHD à sua conta de armazenamento utilizando um dos seguintes meios:

- [Cópia de Azure armazenamento Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
- [Carregar Blobs do Explorer armazenamento Azure](https://azurestorageexplorer.codeplex.com/)
- [Referência do armazenamento importar/exportar serviço REST API](https://msdn.microsoft.com/library/dn529096.aspx)

>[AZURE.NOTE] Recomendamos que utilize o serviço de importação/exportação se estimada tempo a carregar tem mais de 7 dias. Pode utilizar [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) para estimar a hora de unidade de tamanho e a transferência de dados.
>
> Importar/exportar podem ser utilizado para copiar para uma conta de armazenamento padrão. Terá de copiar de armazenamento padrão para a conta de armazenamento de premium através de uma ferramenta como AzCopy.


## <a name="create-azure-virtual-machine-using-premium-storage"></a>Criar VMs Azure utilizando o armazenamento de Premium

Depois do VHD é carregado ou copiado para a conta de armazenamento pretendido, siga as instruções nesta secção para registar o VHD como uma imagem do sistema operativo ou disco SO dependendo do seu cenário e, em seguida, criar uma instância VM a partir dos mesmos. O disco de dados VHD pode ser anexado a VM depois de ter sido criada. É fornecido um script de migração de exemplo no final desta secção. Este script simple não corresponder a todos os cenários. Poderá ter de atualizar o script para corresponder com o seu cenário específico. Para ver se este script aplica-se ao seu cenário, consulte o artigo abaixo [Um exemplo de Script de migração](#a-sample-migration-script).

### <a name="checklist"></a>Lista de verificação

1.  Aguarde até que todos os discos VHD copiar está concluída.
2.  Certifique-se de que armazenamento do Premium está disponível na região que está a migrar.
3.  Decida que irá utilizar a nova série de VM. Deverá ser um com capacidade de armazenamento de Premium e o tamanho deve ser cálculo consoante a forma a disponibilidade na região e com base nas suas necessidades.
4.  Decida o tamanho da memória virtual exato que irá utilizar. Tamanho da memória virtual tem de estar grande o suficiente para suportar o número de discos de dados que tiver. Por exemplo Se tiver 4 discos de dados, a VM tem de ter núcleos 2 ou mais. Além disso, considere processamento power, memória e precisa de largura de banda de rede.
5.  Crie uma conta de armazenamento de Premium na região de destino. Esta é a conta que irá utilizar para a nova VM.
6.  Ter à mão, incluindo a lista de discos e correspondentes VHD blobs os detalhes VM actuais.

Prepare a sua aplicação de tempo de inatividade. Para executar uma migração limpa, tem de interromper o todo o processamento do sistema atual. Apenas, em seguida, pode acedê-lo à estado consistente que pode migrar para a nova plataforma. Duração de tempo de inatividade irá depende da quantidade de dados no discos a migrar.

>[AZURE.NOTE] Se estiver a criar uma VM de Gestor de recursos do Azure a partir de um disco VHD especializadas, consulte [Este modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) para implementar o Gestor de recursos VM utilizando o disco existente.

### <a name="register-your-vhd"></a>Registe-se a sua VHD

Para criar uma VM a partir do SO VHD ou anexar um disco de dados a uma nova VM, primeiro tem de registá-los. Siga os passos abaixo, dependendo do cenário seu VHD.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>GRG VHD sistema operativo para criar várias instâncias de Azure VM

Depois de imagem do sistema operativo GRG VHD é carregada para a conta de armazenamento, registe-se-la como uma **Imagem do Azure VM** para que possa criar um ou mais ocorrências VM a partir dos mesmos. Utilize os seguintes cmdlets do PowerShell para registar a sua VHD como uma imagem Azure VM SO. Fornece onde VHD foi copiado para o URL do contentor concluída.

    Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows

Copiar e guardar o nome desta nova imagem de VM Azure. No exemplo acima, é *OSImageName*.

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Exclusivo VHD sistema operativo para criar uma única ocorrência de Azure VM

Depois do VHD SO exclusivo ser carregado para a conta de armazenamento, registe-se-lo como um **Disco do Azure SO** para que possa criar uma instância VM a partir dos mesmos. Utilize estes cmdlets do PowerShell para registar a sua VHD como um disco de SO Azure. Fornece onde VHD foi copiado para o URL do contentor concluída.

    Add-AzureDisk -DiskName "OSDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "My OS Disk" -OS "Windows"

Copiar e guardar o nome deste disco de SO Azure novo. No exemplo acima, é *OSDisk*.

#### <a name="data-disk-vhd-to-be-attached-to-new-azure-vm-instances"></a>Dados VHD deve ser anexado à novas Azure VM ocorrências do disco

Depois de é carregado o disco de dados VHD à conta de armazenamento, registe-se-lo como um disco de dados do Azure para que pode ser anexado a sua nova série de DS, DSv2 série s série Azure VM uma instância de ou.

Utilize estes cmdlets do PowerShell para registar a sua VHD como um disco de dados do Azure. Fornece onde VHD foi copiado para o URL do contentor concluída.

    Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"

Copiar e guardar o nome deste disco de dados do Azure novo. No exemplo acima, é *DataDisk*.

### <a name="create-a-premium-storage-capable-vm"></a>Criar uma VM com capacidade de armazenamento de Premium

Uma vez a imagem do sistema operativo ou disco SO estão registados, criar uma nova série de DS, DSv2 série ou VM da série s. Utilizar a imagem do sistema operativo ou o nome do disco sistema operativo que registou. Selecione o tipo VM a camada de armazenamento de Premium. No exemplo abaixo, estamos a utilizar o tamanho da memória virtual *Standard_DS2* .

>[AZURE.NOTE] Atualize o tamanho do disco para se certificar de que corresponda ao seu capacidade e requisitos de desempenho e os tamanhos disponível no disco Azure.

Siga os cmdlets do PowerShell passo a passo abaixo para criar a nova VM. Em primeiro lugar, defina os parâmetros comuns:

    $serviceName = "yourVM"
    $location = "location-name" (e.g., West US)
    $vmSize ="Standard_DS2"
    $adminUser = "youradmin"
    $adminPassword = "yourpassword"
    $vmName ="yourVM"
    $vmSize = "Standard_DS2"

Primeiro, crie um serviço na nuvem na qual irá alojar o seu novos VMs.

    New-AzureService -ServiceName $serviceName -Location $location

Em seguida, dependendo do seu cenário, crie a instância do Azure VM a partir da imagem do sistema operativo ou o disco de sistema operativo que registou.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>GRG VHD sistema operativo para criar várias instâncias de Azure VM

Crie as um ou mais novas DS série Azure VM instâncias utilizando a **Imagem do sistema operativo Azure** que registou. Especifique este nome de imagem do sistema operativo na configuração VM ao criar uma nova VM conforme apresentado abaixo.

    $OSImage = Get-AzureVMImage –ImageName "OSImageName"

    $vm = New-AzureVMConfig -Name $vmName –InstanceSize $vmSize -ImageName $OSImage.ImageName

    Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm

    New-AzureVM -ServiceName $serviceName -VM $vm

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Exclusivo VHD sistema operativo para criar uma única ocorrência de Azure VM

Crie uma nova instância de Azure VM de série DS utilizando o **Azure SO disco** que registou. Especifique este nome SO disco na configuração VM quando criar a nova VM conforme apresentado abaixo.

    $OSDisk = Get-AzureDisk –DiskName "OSDisk"

    $vm = New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -DiskName $OSDisk.DiskName

    New-AzureVM -ServiceName $serviceName –VM $vm

Especificar informações outras Azure VM, tal como um serviço na nuvem, região, conta de armazenamento, conjunto de disponibilidade e política de colocação em cache. Tenha em atenção que a instância VM deve estar cocriação situada com o sistema operativo associado ou discos de dados, pelo que a conta de serviço, região e armazenamento na nuvem selecionado deve ser na mesma localização como os VHDs subjacentes nesses discos.

### <a name="attach-data-disk"></a>Anexar o disco de dados

Por fim, se ter registado dados disco VHDs, anexe-os para a nova Premium armazenamento capazes Azure VM.

Utilize a seguir cmdlet do PowerShell para anexar o disco de dados para a nova VM e especificar a política de colocação em cache. No exemplo abaixo, a política de colocação em cache está definida para *só de leitura*.

    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

    Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm

    Update-AzureVM  -VM $vm

>[AZURE.NOTE] Poderão existir passos adicionais necessários para suportar a aplicação que não é ser abrangido neste guia.

### <a name="checking-and-plan-backup"></a>Verificar e planear cópias de segurança

Assim que a nova VM estiver a trabalhar, aceder à mesma com o mesmo id de início de sessão e a palavra-passe é como a VM original e certifique-se de que tudo está a funcionar como esperado. Todas as definições, incluindo os volumes às riscas, seria presentes na nova VM.

O último passo é planear cópias de segurança e agenda de manutenção para a nova VM com base nas necessidades da aplicação.

### <a name="a-sample-migration-script"></a>Um script de migração de exemplo

Se tiver várias VMs para migrar, poderá ser útil automatização através de scripts de PowerShell. Segue-se um exemplo de script automatiza a migração de uma VM. Nota que abaixo script é apenas um exemplo e não existem alguns suposições feitas sobre os discos VM atuais. Poderá ter de atualizar o script para corresponder com o seu cenário específico.

Os pressupostos são:

- Está a criar clássica Azure VMs.
- Os seus discos SO de origem e discos de dados de origem favoritos na mesma conta de armazenamento e mesmo contentor. Se o sistema operativo discos e dados não estiverem no mesmo local, pode utilizar AzCopy ou Azure PowerShell para copiar VHDs sobre contas de armazenamento e contentores. Referir-se para o passo anterior: [Copiar VHD com AzCopy ou PowerShell](#copy-vhd-with-azcopy-or-powershell). Este script para cumprir o seu cenário de edição é outra opção, mas recomendamos que utilize AzCopy ou o PowerShell, uma vez que é mais fácil e rápido.

O script de automatização é fornecido abaixo. Substituir texto com as suas informações e atualize o script para corresponder com o seu cenário específico.

>[AZURE.NOTE] Utilizando o script existente não preserva configuração de rede da sua origem VM. É necessário re-config as definições de redes no seu VMs migrados.

    <#
    .Synopsis
    This script is provided as an EXAMPLE to show how to migrate a VM from a standard storage account to a premium storage account. You can customize it according to your specific requirements.

    .Description
    The script will copy the vhds (page blobs) of the source VM to the new storage account.
    And then it will create a new VM from these copied vhds based on the inputs that you specified for the new VM.
    You can modify the script to satisfy your specific requirement, but please be aware of the items specified
    in the Terms of Use section.

    .Terms of Use
    Copyright © 2015 Microsoft Corporation.  All rights reserved.

    THIS CODE AND ANY ASSOCIATED INFORMATION ARE PROVIDED “AS IS” WITHOUT WARRANTY OF ANY KIND,
    EITHER EXPRESSED OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE IMPLIED WARRANTIES OF MERCHANTABILITY
    AND/OR FITNESS FOR A PARTICULAR PURPOSE. THE ENTIRE RISK OF USE, INABILITY TO USE, OR
    RESULTS FROM THE USE OF THIS CODE REMAINS WITH THE USER.

    .Example (Save this script as Migrate-AzureVM.ps1)

    .\Migrate-AzureVM.ps1 -SourceServiceName CurrentServiceName -SourceVMName CurrentVMName –DestStorageAccount newpremiumstorageaccount -DestServiceName NewServiceName -DestVMName NewDSVMName -DestVMSize "Standard_DS2" –Location “Southeast Asia”

    .Link
    To find more information about how to set up Azure PowerShell, refer to the following links.
    http://azure.microsoft.com/documentation/articles/powershell-install-configure/
    http://azure.microsoft.com/documentation/articles/storage-powershell-guide-full/
    http://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/

    #>

    Param(
    # the cloud service name of the VM.
    [Parameter(Mandatory = $true)]
    [string] $SourceServiceName,

    # The VM name to copy.
    [Parameter(Mandatory = $true)]
    [String] $SourceVMName,

    # The destination storage account name.
    [Parameter(Mandatory = $true)]
    [String] $DestStorageAccount,

    # The destination cloud service name
    [Parameter(Mandatory = $true)]
    [String] $DestServiceName,

    # the destination vm name
    [Parameter(Mandatory = $true)]
    [String] $DestVMName,

    # the destination vm size
    [Parameter(Mandatory = $true)]
    [String] $DestVMSize,

    # the location of destination VM.
    [Parameter(Mandatory = $true)]
    [string] $Location,

    # whether or not to copy the os disk, the default is only copy data disks
    [Parameter(Mandatory = $false)]
    [Bool] $DataDiskOnly = $true,

    # how frequently to report the copy status in sceconds
    [Parameter(Mandatory = $false)]
    [Int32] $CopyStatusReportInterval = 15,

    # the name suffix to add to new created disks to avoid conflict with source disk names
    [Parameter(Mandatory = $false)]
    [String]$DiskNameSuffix = "-prem"

    ) #end param

    #######################################################################
    #  Verify Azure PowerShell module and version
    #######################################################################

    #import the Azure PowerShell module
    Write-Host "`n[WORKITEM] - Importing Azure PowerShell module" -ForegroundColor Yellow
    $azureModule = Import-Module Azure -PassThru

    if ($azureModule -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    else
    {
        #show module not found interaction and bail out
        Write-Host "[ERROR] - PowerShell module not found. Exiting." -ForegroundColor Red
        Exit
    }


    #Check the Azure PowerShell module version
    Write-Host "`n[WORKITEM] - Checking Azure PowerShell module verion" -ForegroundColor Yellow
    If ($azureModule.Version -ge (New-Object System.Version -ArgumentList "0.8.14"))
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    Else
    {
        Write-Host "[ERROR] - Azure PowerShell module must be version 0.8.14 or higher. Exiting." -ForegroundColor Red
        Exit
    }

    #Check if there is an azure subscription set up in PowerShell
    Write-Host "`n[WORKITEM] - Checking Azure Subscription" -ForegroundColor Yellow
    $currentSubs = Get-AzureSubscription -Current
    if ($currentSubs -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
        Write-Host "`tYour current azure subscription in PowerShell is $($currentSubs.SubscriptionName)." -ForegroundColor Green
    }
    else
    {
        Write-Host "[ERROR] - There is no valid Azure subscription found in PowerShell. Please refer to this article http://azure.microsoft.com/documentation/articles/powershell-install-configure/ to connect an Azure subscription. Exiting." -ForegroundColor Red
        Exit
    }


    #######################################################################
    #  Check if the VM is shut down
    #  Stopping the VM is a required step so that the file system is consistent when you do the copy operation.
    #  Azure does not support live migration at this time..
    #######################################################################

    if (($sourceVM = Get-AzureVM –ServiceName $SourceServiceName –Name $SourceVMName) -eq $null)
    {
        Write-Host "[ERROR] - The source VM doesn't exist in the current subscription. Exiting." -ForegroundColor Red
        Exit
    }

    # check if VM is shut down
    if ( $sourceVM.Status -notmatch "Stopped" )
    {
        Write-Host "[Warning] - Stopping the VM is a required step so that the file system is consistent when you do the copy operation. Azure does not support live migration at this time. If you’d like to create a VM from a generalized image, sys-prep the Virtual Machine before stopping it." -ForegroundColor Yellow
        $ContinueAnswer = Read-Host "`n`tDo you wish to stop $SourceVMName now? Input 'N' if you want to shut down the VM manually and come back later.(Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $sourceVM | Stop-AzureVM

        # wait until the VM is shut down
        $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        while ($VMStatus -notmatch "Stopped")
        {
            Write-Host "`n[Status] - Waiting VM $vmName to shut down" -ForegroundColor Green
            Sleep -Seconds 5
            $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        }
    }

    # exporting the sourve vm to a configuration file, you can restore the original VM by importing this config file
    # see more information for Import-AzureVM
    $workingDir = (Get-Location).Path
    $vmConfigurationPath = $env:HOMEPATH + "\VM-" + $SourceVMName + ".xml"
    Write-Host "`n[WORKITEM] - Exporting VM configuration to $vmConfigurationPath" -ForegroundColor Yellow
    $exportRe = $sourceVM | Export-AzureVM -Path $vmConfigurationPath


    #######################################################################
    #  Copy the vhds of the source vm
    #  You can choose to copy all disks including os and data disks by specifying the
    #  parameter -DataDiskOnly to be $false. The default is to copy only data disk vhds
    #  and the new VM will boot from the original os disk.
    #######################################################################

    $sourceOSDisk = $sourceVM.VM.OSVirtualHardDisk
    $sourceDataDisks = $sourceVM.VM.DataVirtualHardDisks

    # Get source storage account information, not considering the data disks and os disks are in different accounts
    $sourceStorageAccountName = $sourceOSDisk.MediaLink.Host -split "\." | select -First 1
    $sourceStorageKey = (Get-AzureStorageKey -StorageAccountName $sourceStorageAccountName).Primary
    $sourceContext = New-AzureStorageContext –StorageAccountName $sourceStorageAccountName -StorageAccountKey $sourceStorageKey

    # Create destination context
    $destStorageKey = (Get-AzureStorageKey -StorageAccountName $DestStorageAccount).Primary
    $destContext = New-AzureStorageContext –StorageAccountName $DestStorageAccount -StorageAccountKey $destStorageKey

    # Create a container of vhds if it doesn't exist
    if ((Get-AzureStorageContainer -Context $destContext -Name vhds -ErrorAction SilentlyContinue) -eq $null)
    {
        Write-Host "`n[WORKITEM] - Creating a container vhds in the destination storage account." -ForegroundColor Yellow
        New-AzureStorageContainer -Context $destContext -Name vhds
    }


    $allDisksToCopy = $sourceDataDisks
    # check if need to copy os disk
    $sourceOSVHD = $sourceOSDisk.MediaLink.Segments[2]
    if ($DataDiskOnly)
    {
        # copy data disks only, this option requires deleting the source VM so that dest VM can boot
        # from the same vhd blob.
        $ContinueAnswer = Read-Host "`n`t[Warning] You chose to copy data disks only. Moving VM requires removing the original VM (the disks and backing vhd files will NOT be deleted) so that the new VM can boot from the same vhd. This is an irreversible action. Do you wish to proceed right now? (Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $destOSVHD = Get-AzureStorageBlob -Blob $sourceOSVHD -Container vhds -Context $sourceContext
        Write-Host "`n[WORKITEM] - Removing the original VM (the vhd files are NOT deleted)." -ForegroundColor Yellow
        Remove-AzureVM -Name $SourceVMName -ServiceName $SourceServiceName

        Write-Host "`n[WORKITEM] - Waiting utill the OS disk is released by source VM. This may take up to several minutes."
        $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        while ($diskAttachedTo -ne $null)
        {
            Start-Sleep -Seconds 10
            $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        }

    }
    else
    {
        # copy the os disk vhd
        Write-Host "`n[WORKITEM] - Starting copying os disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $allDisksToCopy += @($sourceOSDisk)
        $targetBlob = Start-AzureStorageBlobCopy -SrcContainer vhds -SrcBlob $sourceOSVHD -DestContainer vhds -DestBlob $sourceOSVHD -Context $sourceContext -DestContext $destContext -Force
        $destOSVHD = $targetBlob
    }


    # Copy all data disk vhds
    # Start all async copy requests in parallel.
    foreach($disk in $sourceDataDisks)
    {
        $blobName = $disk.MediaLink.Segments[2]
        # copy all data disks
        Write-Host "`n[WORKITEM] - Starting copying data disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $targetBlob = Start-AzureStorageBlobCopy -SrcContainer vhds -SrcBlob $blobName -DestContainer vhds -DestBlob $blobName -Context $sourceContext -DestContext $destContext -Force
        # update the media link to point to the target blob link
        $disk.MediaLink = $targetBlob.ICloudBlob.Uri.AbsoluteUri
    }

    # Wait until all vhd files are copied.
    $diskComplete = @()
    do
    {
        Write-Host "`n[WORKITEM] - Waiting for all disk copy to complete. Checking status every $CopyStatusReportInterval seconds." -ForegroundColor Yellow
        # check status every 30 seconds
        Sleep -Seconds $CopyStatusReportInterval
        foreach ( $disk in $allDisksToCopy)
        {
            if ($diskComplete -contains $disk)
            {
                Continue
            }
            $blobName = $disk.MediaLink.Segments[2]
            $copyState = Get-AzureStorageBlobCopyState -Blob $blobName -Container vhds -Context $destContext
            if ($copyState.Status -eq "Success")
            {
                Write-Host "`n[Status] - Success for disk copy $($disk.DiskName) at $($copyState.CompletionTime)" -ForegroundColor Green
                $diskComplete += $disk
            }
            else
            {
                if ($copyState.TotalBytes -gt 0)
                {
                    $percent = ($copyState.BytesCopied / $copyState.TotalBytes) * 100
                    Write-Host "`n[Status] - $('{0:N2}' -f $percent)% Complete for disk copy $($disk.DiskName)" -ForegroundColor Green
                }
            }
        }
    }
    while($diskComplete.Count -lt $allDisksToCopy.Count)

    #######################################################################
    #  Create a new vm
    #  the new VM can be created from the copied disks or the original os disk.
    #  You can ddd your own logic here to satisfy your specific requirements of the vm.
    #######################################################################

    # Create a VM from the existing os disk
    if ($DataDiskOnly)
    {
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $sourceOSDisk.DiskName
    }
    else
    {
        $newOSDisk = Add-AzureDisk -OS $sourceOSDisk.OS -DiskName ($sourceOSDisk.DiskName + $DiskNameSuffix) -MediaLocation $destOSVHD.ICloudBlob.Uri.AbsoluteUri
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $newOSDisk.DiskName
    }
    # Attached the copied data disks to the new VM
    foreach ($dataDisk in $sourceDataDisks)
    {
        # add -DiskLabel $dataDisk.DiskLabel if there are labels for disks of the source vm
        $diskLabel = "drive" + $dataDisk.Lun
        $vm | Add-AzureDataDisk -ImportFrom -DiskLabel $diskLabel -LUN $dataDisk.Lun -MediaLocation $dataDisk.MediaLink
    }

    # Edit this if you want to add more custimization to the new VM
    # $vm | Add-AzureEndpoint -Protocol tcp -LocalPort 443 -PublicPort 443 -Name 'HTTPs'
    # $vm | Set-AzureSubnet "PubSubnet","PrivSubnet"

    New-AzureVM -ServiceName $DestServiceName -VMs $vm -Location $Location

#### <a name="optimization"></a>Otimização

Configuração atual do VM pode ser personalizada especificamente para trabalhar bem com discos padrão. Por exemplo, para melhorar o desempenho utilizando muitas discos num volume às riscas. Por exemplo, em vez de utilizar 4 discos separadamente armazenamento Premium, poderá conseguir otimizar o custo por ter um único disco. Otimizações como esta necessidade para ser resolvido no caso a caso e requerem passos personalizados após a migração. Além disso, repare que este processo poderá não funcionar bem para bases de dados e aplicações que dependem do esquema de disco definido na configuração.

##### <a name="preparation"></a>Preparação

1.  Conclua a migração simples, tal como descrito na secção anterior. A nova VM serão executadas otimizações após a migração.
2.  Defina os tamanhos de disco novo necessários para a configuração otimizada.
3.  Determine o mapeamento de discos/volumes atuais para os novo especificações de disco.

##### <a name="execution-steps"></a>Passos de execução

1.  Crie novos discos com os tamanhos direita na VM de armazenamento Premium.
2.  Início de sessão para o VM e copiar os dados a partir do volume actual no novo disco mapas a esse volume. Faça o seguinte para todos os volumes atuais que tem de mapear para um novo disco.
3.  Em seguida, altere as definições da aplicação para mudar para os novos discos e desanexar os volumes antigos.

Para otimizar a aplicação para um melhor desempenho do disco, consulte [Otimizar o desempenho de aplicações](storage-premium-storage-performance.md#optimizing-application-performance).

### <a name="application-migrations"></a>Migrações de aplicação

Bases de dados e outras aplicações complexas precisem de passos especiais conforme definido pelo fornecedor de aplicação para a migração. Consulte a documentação da respetiva aplicação. Por exemplo Normalmente, podem ser migrados bases de dados através de cópia de segurança e restaurar.

## <a name="next-steps"></a>Próximos passos

Consulte os seguintes recursos para cenários específicos para migrar máquinas virtuais:

- [Migrar Azure máquinas virtuais entre contas de armazenamento](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
- [Criar e carregar um VHD de servidor do Windows para o Azure.](../virtual-machines/virtual-machines-windows-classic-createupload-vhd.md)
- [Criar e carregar um disco rígido Virtual que contém o sistema operativo Linux](../virtual-machines/virtual-machines-linux-classic-create-upload-vhd.md)
- [Migrar máquinas virtuais do Amazon AWS ao Microsoft Azure](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Além disso, consulte os seguintes recursos para saber mais sobre o armazenamento do Azure e máquinas virtuais do Azure:

- [Armazenamento Azure](https://azure.microsoft.com/documentation/services/storage/)
- [Azure máquinas virtuais](https://azure.microsoft.com/documentation/services/virtual-machines/)
- [Armazenamento de Premium: Armazenamento de alto desempenho para Máquina Virtual Azure cargas de trabalho](storage-premium-storage.md)

[1]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png
[4]: http://technet.microsoft.com/library/hh831739.aspx
