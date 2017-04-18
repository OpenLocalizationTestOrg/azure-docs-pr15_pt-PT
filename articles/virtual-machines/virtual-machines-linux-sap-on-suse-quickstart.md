<properties
   pageTitle="Testes SAP NetWeaver no Microsoft Azure SUSE Linux VMs | Microsoft Azure"
   description="Testes SAP NetWeaver no Microsoft Azure SUSE Linux VMs"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="hermanndms"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/15/2016"
   ms.author="hermannd"/>

# <a name="running-sap-netweaver-on-microsoft-azure-suse-linux-vms"></a>Em execução SAP NetWeaver no Microsoft Azure SUSE Linux VMs


Este artigo descreve vários elementos a considerar quando estiver a executar o SAP NetWeaver em máquinas virtuais do Microsoft Azure SUSE Linux (VMs). Partir de 2016 19 de Maio SAP NetWeaver oficialmente é suportada no SUSE Linux VMs no Azure. Todos os detalhes sobre Linux versões, versões de kernel SAP e assim sucessivamente podem ser encontrados no SAP nota 1928533 "as aplicações SAP no Azure: produtos suportados e os tipos Azure VM".
Documentação suplementar sobre SAP no Linux VMs pode ser encontrada aqui: [Utilizar SAP em máquinas virtuais do Linux (VMs)](virtual-machines-linux-sap-get-started.md).


As seguintes informações deverão ajudar a evitar algumas potenciais falhas.

## <a name="suse-images-on-azure-for-running-sap"></a>Imagens SUSE no Azure para a execução do SAP

Para executar o SAP NetWeaver no Azure, utilize apenas SUSE Linux Enterprise Server SLES 12 (SPx) - ver também SAP nota 1928533. Uma imagem SUSE especial é no mercado Azure ("SLES 11 SP3 para SAP CAL"), mas isto não se destina a utilização geral. Não utilize esta imagem porque está reservado para a solução [SAP nuvem aparelho biblioteca](https://cal.sap.com/) .  

Deve utilizar o Gestor de recursos do Azure para todos os novos testes e instalações no Azure. Para procurar imagens SUSE SLES e versões utilizando Azure PowerShell ou a interface de comandos Azure (CLI), utilize os comandos seguintes. Em seguida, pode utilizar a saída, por exemplo, para definir a imagem do sistema operativo num modelo de JSON para implementar uma nova Linux VM da SUSE.
Estes comandos do PowerShell são válidos para a versão do Azure PowerShell 1.0.1 e versões posteriores.

* Procure fabricantes existentes, incluindo SUSE:

   ```
   PS  : Get-AzureRmVMImagePublisher -Location "West Europe"  | where-object { $_.publishername -like "*US*"  }
   CLI : azure vm image list-publishers westeurope | grep "US"
   ```

* Procure ofertas existentes a partir do SUSE:

   ```
   PS  : Get-AzureRmVMImageOffer -Location "West Europe" -Publisher "SUSE"
   CLI : azure vm image list-offers westeurope SUSE
   ```

* Procure ofertas SUSE SLES:

   ```
   PS  : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES"
   CLI : azure vm image list-skus westeurope SUSE SLES
   ```

* Procure uma versão específica de um SKU SLES:

   ```
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES" -skus "12"
   CLI : azure vm image list westeurope SUSE SLES 12
   ```

## <a name="installing-walinuxagent-in-a-suse-vm"></a>Instalar o WALinuxAgent num VM SUSE

O agente denominado WALinuxAgent faz parte das imagens SLES no Azure Marketplace. Para obter informações sobre como instalá-lo manualmente (por exemplo, quando carregar um SO SLES virtual no disco rígido (VHD) a partir no local), consulte:

- [OpenSUSE] (http://software.opensuse.org/package/WALinuxAgent)

- [Azure] (virtual-máquinas-linux-menção-distros.md)

- [SUSE] (https://www.suse.com/communities/blog/suse-linux-enterprise-server-configuration-for-windows-azure/)

## <a name="sap-enhanced-monitoring"></a>SAP "melhorado monitorização"

SAP "avançada monitorização" é um pré-requisito obrigatório para executar SAP no Azure. Verifique se existem detalhes no SAP tenha em atenção 2191498 "SAP no Linux com Azure: avançada monitorização".

## <a name="attaching-azure-data-disks-to-an-azure-linux-vm"></a>Como anexar discos Azure dados a uma VM Linux do Azure

Nunca deve discos de dados do Azure montagem para uma VM de Linux Azure utilizando o ID do dispositivo. Em alternativa, utilize o identificador exclusivo universal (UUID). Tenha cuidado quando utilizar ferramentas gráficas para discos de dados do Azure montagem, por exemplo. Verifique se as entradas /etc/fstab.

É o problema com o ID do dispositivo que podem ser alterados e, em seguida, a VM Azure poderá pendurar no processo de arranque. Para mitigar o problema, é possível adicionar o parâmetro nofail no /etc/fstab. Mas, tenha cuidado com nofail uma vez que poderão utilizar o ponto de montagem como antes de aplicações e poderão escrever o sistema de ficheiros de raiz, no caso de um disco dados Azure externos não era instalado durante o arranque.

A única exceção montagem através do UUID é anexar um disco SO para resolução de problemas de efeitos, conforme descrito na secção seguinte.

## <a name="troubleshooting-a-suse-vm-that-isnt-accessible-anymore"></a>Uma VM SUSE que não esteja acessível deixem de resolução de problemas

Poderá haver situações em que uma VM SUSE no Azure bloqueia no processo de arranque (por exemplo, com um erro relacionado com montagem discos). Pode verificar este problema, utilizando a funcionalidade de diagnóstico de arranque para máquinas virtuais do Azure v2 no portal do Azure. Para obter mais informações, consulte o artigo [diagnósticos de arranque] (https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).

Uma forma de resolver o problema é anexar o disco de SO a partir da VM danificada para outra SUSE VM no Azure. Em seguida, faça as alterações adequadas, como /etc/fstab a editar ou remover regras de udev de rede, conforme descrito na secção seguinte.

Existe um dado importante ter em consideração. Implementar várias SUSE VMs a partir da mesma imagem Azure Marketplace (por exemplo, SLES 11 SP4) faz com que o disco SO a sempre instalados pelo mesmo UUID. Por conseguinte, utilizar o UUID para anexar um disco SO a partir de uma VM diferentes que foi implementada utilizando a mesma imagem Azure Marketplace irá resultar em dois UUIDs idênticos. Isto faz com que problemas e poderia significa que a VM destinam a resolução de problemas na verdade irá arrancar partir do disco SO anexado e danificado em vez de original.

Existem duas formas para evitar esta situação:

* Utilize uma imagem de Azure Marketplace diferente para a resolução de problemas VM (por exemplo, SLES 11 SPx em vez de SLES 12).
* Não anexar o disco SO danificado a partir de outro VM utilizando UUID – utilizar algo diferente.

## <a name="uploading-a-suse-vm-from-on-premises-to-azure"></a>Carregar uma VM SUSE nos locais para Azure

Para obter uma descrição dos passos para carregar uma VM SUSE nos locais para Azure, consulte o artigo [preparar uma máquina de virtual SLES ou openSUSE para Azure] (virtual-machines-linux-suse-create-upload-vhd.md).

Se pretende carregar uma VM sem o passo deprovision no final (por exemplo, para manter uma instalação existente do SAP, bem como o nome do anfitrião), verifique os seguintes itens:

* Certifique-se de que o disco SO é instalado utilizando UUID e não o ID do dispositivo. Alterar para UUID apenas nos /etc/fstab não é suficiente para o disco SO. Além disso, não se esqueça de adaptar carregador de arranque através de YaST ou ao editar /boot/grub/menu.lst.
* Se utilizar o formato VHDX para o disco SUSE SO e convertê-la para VHD para carregar para Azure, é muito provável que o dispositivo de rede será alterado do eth0 para eth1. Para evitar problemas quando está a ser arrancar no Azure mais tarde, altere para eth0 conforme descrito [corrigir eth0 no clonado SLES 11 VMware] (https://dartron.wordpress.com/2013/09/27/fixing-eth1-in-cloned-sles-11-vmware/).

Para além do que é descrito no artigo, recomendamos que o remova isto:

   /lib/udev/Rules.d/75-persistent-NET-Generator.Rules

Também pode instalar o agente de Linux Azure (waagent) para o ajudar a evitar potenciais problemas, desde que não existem vários NIC.

## <a name="deploying-a-suse-vm-on-azure"></a>Implementar uma VM SUSE no Azure

Deve criar novo SUSE VMs utilizando os ficheiros de modelo JSON no modelo de Gestor de recursos do Azure novo. Quando o ficheiro de modelo JSON estiver criado, pode implementar a VM utilizando o seguinte comando clip como alternativa à PowerShell:

   ```
   azure group deployment create "<deployment name>" -g "<resource group name>" --template-file "<../../filename.json>"

   ```
Para obter mais detalhes sobre os ficheiros de modelo JSON, consulte [modelos de criação de Gestor de recursos do Azure] (recurso-grupo-criação-templates.md) e [modelos de guia de introdução Azure] (https://azure.microsoft.com/documentation/templates/).

Para obter mais detalhes sobre o clip e Gestor de recursos do Azure, consulte o artigo [utilizar o clip do Azure para Mac, Linux e Windows com o Gestor de recursos do Azure] (xplat-clip-azure-recurso-manager.md).

## <a name="sap-license-and-hardware-key"></a>Tecla de licença e hardware do SAP

Para certificação de SAP Azure oficial, um novo mecanismo foi introduzido para calcular a chave de hardware do SAP que é utilizada para a licença do SAP. Kernel do SAP tinha que ser adaptados para tornar uso desta. Versões de kernel SAP antigos para Linux não incluiu esta alteração de código. Por conseguinte, em determinadas situações (por exemplo, Azure VM redimensionar), a chave de hardware do SAP alterada e a licença SAP foi já não ser válidos. Isto é resolvido em miolo SAP Linux mais recente. Para obter mais detalhes, verifique nota SAP 1928533.

## <a name="suse-sapconf-package--tuned-adm"></a>Pacote de sapconf SUSE / adm optimizada

SUSE fornece um pacote denominado "sapconf" que gere a um conjunto de definições específicas do SAP. Para obter mais detalhes sobre o que significa este pacote e como instalar e utilizá-lo, consulte o artigo [utilizar sapconf para preparar uma SUSE Linux Enterprise Server para executar sistemas SAP] (https://www.suse.com/communities/blog/using-sapconf-to-prepare-suse-linux-enterprise-server-to-run-sap-systems/) e [o que é sapconf ou como preparar um SUSE Linux Enterprise Server para executar o sistemas SAP?] (http://scn.sap.com/community/linux/blog/2014/03/31/what-is-sapconf-or-how-to-prepare-a-suse-linux-enterprise-server-for-running-sap-systems).

Entretanto, existe uma nova ferramenta que substitui sapconf - adm. optimizada Um pode encontrar mais detalhes sobre esta ferramenta seguir as duas ligações em baixo.

Documentação SLES sobre adm optimizado perfil sap-hana pode ser encontrada [aqui](https://www.suse.com/documentation/sles-for-sap-12/book_s4s/data/sec_s4s_configure_sapconf.html) 

Sistemas de sintonização para das cargas de trabalho do SAP com adm optimizada - podem ser encontradas [aqui](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/book_s4s/book_s4s.pdf) na capítulo 6.2


## <a name="nfs-share-in-distributed-sap-installations"></a>Partilhar o NFS em instalações distribuídas do SAP

Se tiver uma instalação distribuída – por exemplo, onde pretende instalar a base de dados e os servidores de aplicações SAP no separado VMs – pode partilhar o diretório de /sapmnt através do sistema de ficheiros de rede (NFS). Se tiver problemas com os passos de instalação depois de criar a partilha de NFS para /sapmnt, verifique se "no_root_squash" é definido para a partilha.

## <a name="logical-volumes"></a>Volumes lógicos

No passado caso um seja necessário um grande volume de lógico por vários discos Azure dados (por exemplo, para SAP base de dados), foi recomendada para utilizar mdadm como lvm não foi totalmente validado ainda no Azure. Para saber como configurar o Linux RAID no Azure utilizando mdadm, consulte o artigo [configurar software RAID no Linux](virtual-machines-linux-configure-raid.md). Entretanto a partir de início de Maio de 2016 também lvm é totalmente suportada no Azure e pode ser utilizado como uma alternativa a mdadm. Para obter informações adicionais relativas ao lvm no Azure consulte o artigo [Configurar LVM numa VM Linux no Azure](virtual-machines-linux-configure-lvm.md).


## <a name="azure-suse-repository"></a>Repositório SUSE Azure

Se tiver um problema com o access para o repositório Azure SUSE padrão, pode utilizar um comando simple para repô-la. Isto pode acontecer se criar uma imagem do sistema operativo privada numa região Azure e, em seguida, copie a imagem para uma região diferente em que pretende implementar VMs novas que sejam baseiam esta imagem SO privada. Basta execute o seguinte comando dentro da VM:

   ```
   service guestregister restart
   ```

## <a name="gnome-desktop"></a>Ambiente de trabalho Gnomo

Se pretender utilizar o ambiente de trabalho Gnomo para instalar um sistema de demonstração SAP concluído dentro de uma única VM – incluindo uma interface gráfica SAP, que o browser e consola de gestão do SAP – Utilize esta sugestão para o instalar as imagens Azure SLES:

   Para SLES 11:

   ```
   zypper in -t pattern gnome
   ```

   Para SLES 12:

   ```
   zypper in -t pattern gnome-basic
   ```

## <a name="sap-support-for-oracle-on-linux-in-the-cloud"></a>Suporte SAP para Oracle no Linux na nuvem

Existe uma restrição de suporte do sistema Oracle no Linux nos ambientes virtualizados. Apesar de não se trata de um tópico específico Azure, é importante compreender. SAP não suporta Oracle no SUSE ou chapéu vermelho numa pública nuvem como o Azure. Para discutir neste tópico, contacte o Oracle diretamente.
