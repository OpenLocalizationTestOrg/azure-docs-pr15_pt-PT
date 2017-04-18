<properties
    pageTitle="Otimizar o seu VM Linux no Azure | Microsoft Azure"
    description="Saiba algumas sugestões de otimização para se certificar de que configurou a sua VM Linux para um desempenho ideal no Azure"
    keywords="máquina de virtual Linux, máquina virtual linux, ubuntu máquina de virtual" 
    services="virtual-machines-linux"
    documentationCenter=""
    authors="rickstercdn"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="rclaus"/>

# <a name="optimize-your-linux-vm-on-azure"></a>Otimizar o seu VM Linux no Azure

Criar uma máquina de virtual Linux (VM) é fácil fazer da linha de comandos ou a partir do portal. Este tutorial mostra-lhe como pode garantir que configurou-lo para otimizar o seu desempenho a plataforma do Microsoft Azure. Este tópico utiliza uma VM de servidor Ubuntu, mas também pode criar máquina de virtual Linux utilizando as [suas próprias imagens como modelos](virtual-machines-linux-create-upload-generic.md).  

## <a name="prerequisites"></a>Pré-requisitos

Este tópico assume que já tem uma subscrição do Azure ([inscrição de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)), [instalado o clip do Azure](../xplat-cli-install.md) a funcionar e já ter aprovisionado uma VM para a sua subscrição do Azure. Antes de ao fazê-lo qualquer com Azure - tem que autenticar à sua subscrição. Para fazer isto com Azure clip, basta escrever `azure login` para iniciar o processo interativo. 

## <a name="azure-os-disk"></a>Azure SO disco

Depois de criar uma Linux Vm no Azure,-possui dois discos associados. /dev/sda é disco SO, /dev/sdb é temporário disco.  Não utilize o disco SO principal (/ Dev Center/sda) para tudo exceto o sistema operativo tal como está optimizada para tempo de arranque do fast VM e não fornece bom desempenho para o seu das cargas de trabalho. Que pretende anexar um ou mais discos para sua VM para obter persistente e otimizado armazenamento para os seus dados. 

## <a name="adding-disks-for-size-and-performance-targets"></a>A adição de discos de destinos tamanho e desempenho 

Basear o tamanho da memória virtual, pode anexar até 16 discos adicionais de uma série, 32 discos numa série de D e 64 discos numa série de G máquinas - cada até 1 TB de tamanho. Adicionar discos extra conforme necessário pelas necessidades de IOps e espaço. Cada disco tem um alvo de desempenho de 500 IOps para o armazenamento do padrão e a até 5000 IOps por disco para armazenamento Premium.  Para mais informações sobre discos de armazenamento de Premium, consulte [armazenamento Premium: armazenamento de alto desempenho para Azure VMs](../storage/storage-premium-storage.md)

Para obter o mais alto IOps em discos Premium armazenamento onde as suas definições de cache tem sido definidas para "Só de leitura" ou "Nenhum", tem de desativar "barreiras" enquanto montagem sistema de ficheiros no Linux. Não necessita de barreiras porque o escreve para discos de armazenamento de Premium cópias é dure estas definições de cache.

- Se utiliza o **reiserFS**, desativar barreiras através da opção montagem "barreiras = nenhum" (para obstáculos de ativação, utilize "barreiras = esvaziar")
- Se utiliza o **ext3/ext4**, desativar obstáculos através da opção montagem "barreiras = 0" (para activar obstáculos, utilize "barreiras = 1")
- Se utiliza o **XFS**, desativar obstáculos através da opção montagem "nobarrier" (para ativar a obstáculos, utilize a opção "barreiras")

## <a name="storage-account-considerations"></a>Considerações de conta de armazenamento

Quando criar o seu VM Linux no Azure, deve certificar-se de que anexar discos a partir de contas de armazenamento que residem na mesma região como a VM para garantir proximidade e minimizar latência da rede.  Cada conta de armazenamento padrão tem um máximo de 20 k IOps e uma capacidade de tamanho de 500 TB.  Isto funciona aproximadamente 40 discos muito utilizados, incluindo o disco de sistema operativo e qualquer discos de dados que criar. Para contas de armazenamento de Premium, não existe limite máximo IOps mas existe um limite de tamanho de 32 TB. 

Quando lidar com alta IOps cargas de trabalho e tiver optado por armazenamento padrão para os seus discos, poderá ter de dividir os discos em várias contas de armazenamento para se certificar de que não tenham visitas o limite de IOps 20.000 para contas de armazenamento padrão. A VM pode conter uma mistura de discos a partir de através de armazenamento diferentes contas e tipos de conta de armazenamento para alcançar a sua configuração ideal. 

## <a name="your-vm-temporary-drive"></a>Sua unidade VM temporária

Por predefinição quando cria uma VM Azure fornece um disco de sistema operativo (/ Dev Center/sda) e um disco temporário (/ Dev Center/sdb).  Todos os discos adicionais que adiciona aparecem como /dev/sdc, /dev/sdd, /dev/sde e assim sucessivamente. Todos os dados no disco temporário (/ Dev Center/sdb) não está resistentes e pode perder-se eventos específicos por VM redimensionamento, nova implementação, ou manutenção força um reinício da sua VM.  O tamanho e tipo do seu disco temporário está relacionado com o tamanho da memória virtual que escolheu no momento da implementação. No caso de qualquer uma do tamanho de premium VMs (DS, G e DS_V2 série), a unidade temporária é cópias por um SSD local para obter um desempenho adicional de até 48 k IOps. 

## <a name="linux-swap-file"></a>Trocar Linux ficheiro

Se estiver a VM Azure a partir de uma imagem Ubuntu ou CoreOS, pode utilizar CustomData para enviar uma nuvem-config inicialização na nuvem. Se [carregar uma imagem Linux personalizada](virtual-machines-linux-upload-vhd.md) que utiliza na nuvem-inicialização, também de configurar partições de trocar utilizando a inicialização da nuvem.

No Ubuntu nuvem imagens, tem de utilizar a inicialização da nuvem para configurar a partição trocar. Ver a página seguinte no wiki Ubuntu para obter mais detalhes: [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).

Para imagens sem suporte de inicialização na nuvem, as imagens VM implementadas a partir do Azure Marketplace têm um agente do Linux VM integrado com o SO. Este agente permite a VM interagir com vários serviços Azure. Assumindo que tenham implementado uma imagem padrão do Azure Marketplace, que precisa para fazer o seguinte para configurar as definições do ficheiro trocar Linux correctamente:

Localizar e modificar duas entradas no ficheiro **/etc/waagent.conf** . Como controlar a existência de um ficheiro de trocar dedicada e tamanho do ficheiro trocar. Os parâmetros de que está à procura para modificar são `ResourceDisk.EnableSwap=N` e`ResourceDisk.SwapSizeMB=0` 

Precisa de alterá-las para o seguinte procedimento:

* ResourceDisk.EnableSwap=Y
* ResourceDisk.SwapSizeMB={size em MB para corresponder às suas necessidades} 

Uma vez que tenha efetuado a alteração, terá de reiniciar o waagent ou reiniciar o seu VM Linux para refletir essas alterações.  Conhecer as alterações foram implementadas e um ficheiro de trocar foi criado quando utiliza o `free` comando para ver o espaço livre. Exemplo abaixo tem um ficheiro de trocar 512MB criado como resultado de modificar o ficheiro waagent.conf.

    admin@mylinuxvm:~$ free
                total       used       free     shared    buffers     cached
    Mem:       3525156     804168    2720988        408       8428     633192
    -/+ buffers/cache:     162548    3362608
    Swap:       524284          0     524284
    admin@mylinuxvm:~$
 
## <a name="io-scheduling-algorithm-for-premium-storage"></a>Algoritmo de agendamento e/s para armazenamento Premium

Com o 2.6.18 Linux kernel, a predefinição e/s agendamento algoritmo foi alterada de prazo para CFQ (algoritmo de colocação completamente justo). Para padrões de e/s acesso aleatório, existe diferença pouco significativa no desempenho as diferenças entre CFQ e prazo.  Para SSD discos baseados no local onde o padrão do disco e/s é essencialmente sequencial, mudar novamente para o algoritmo de NOOP ou prazo possível alcançar um melhor desempenho e/s.

### <a name="view-the-current-io-scheduler"></a>Ver o agendador e/s atual

Utilize o seguinte comando:  

    admin@mylinuxvm:~# cat /sys/block/sda/queue/scheduler

Irá ver as seguintes saída, que indica o agendador atual.  

    noop [deadline] cfq

###<a name="change-the-current-device-devsda-of-io-scheduling-algorithm"></a>Alterar o dispositivo atual (/ Dev Center/sda) de e/s algoritmo de agendamento

Utilize os comandos seguintes:  

    azureuser@mylinuxvm:~$ sudo su -
    root@mylinuxvm:~# echo "noop" >/sys/block/sda/queue/scheduler
    root@mylinuxvm:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
    root@mylinuxvm:~# update-grub

>[AZURE.NOTE] Esta definição para /dev/sda sozinho não é útil. Necessita de ser definidas em todos os discos de dados onde e/s sequenciais domina o padrão e/s.  

Deverá ver o resultado seguinte, que indica que grub.cfg tenha sido recompilado com êxito e que o agendador predefinida foi atualizado para NOOP.  

    Generating grub configuration file ...
    Found linux image: /boot/vmlinuz-3.13.0-34-generic
    Found initrd image: /boot/initrd.img-3.13.0-34-generic
    Found linux image: /boot/vmlinuz-3.13.0-32-generic
    Found initrd image: /boot/initrd.img-3.13.0-32-generic
    Found memtest86+ image: /memtest86+.elf
    Found memtest86+ image: /memtest86+.bin
    done

Família de distribuição do sistema Redhat, apenas terá o seguinte comando:   

    echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local

## <a name="using-software-raid-to-achieve-higher-iops"></a>Utilizar o Software RAID para alcançar superior posso / Ops

Se o seu das cargas de trabalho requerem IOps mais do que um único disco pode fornecer, terá de utilizar uma configuração de RAID de software de vários discos. Uma vez que Azure já executa RDP disco na camada ferro local, é alcançar o nível mais alto de desempenho a partir de uma configuração de repartição RAID-0.  Tem de aprovisionamento e criar novos discos no ambiente do Azure e anexá-las ao seu VM Linux antes de criar partições, formatação e as unidades de montagem.  Podem encontrar mais detalhes sobre como configurar um programa de configuração do software RAID no seu VM Linux no azure no documento **[Configurar RAID Software no Linux](virtual-machines-linux-configure-raid.md)** .


## <a name="next-steps"></a>Próximos passos

Lembre-se, com todos os debates de otimização, necessitar de efetuar testes antes e depois de cada alteração para medir o impacto que terão a alteração.  Optimização é um processo de passo a passo que terão resultados diferentes entre computadores diferentes no seu ambiente.  O que funciona para uma configuração poderá não funcionar para outras pessoas.

Algumas hiperligações úteis para recursos adicionais: 

- [Armazenamento de Premium: Armazenamento de alto desempenho para Máquina Virtual Azure cargas de trabalho](../storage/storage-premium-storage.md)
- [Guia de utilizador do agente de Linux Azure](virtual-machines-linux-agent-user-guide.md)
- [Otimizar o desempenho do MySQL no Azure Linux VMs](virtual-machines-linux-classic-optimize-mysql.md)
- [Configurar o Software RAID em Linux](virtual-machines-linux-configure-raid.md)
