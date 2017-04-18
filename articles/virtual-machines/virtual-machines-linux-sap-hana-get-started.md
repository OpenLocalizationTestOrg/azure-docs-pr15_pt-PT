<properties
   pageTitle="Guia de guia de introdução da instalação manual do SAP HANA no Azure VMs | Microsoft Azure"
   description="Guia de guia de introdução da instalação manual do SAP HANA no Azure VMs"
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

# <a name="quickstart-guide-for-manual-installation-of-single-instance-sap-hana-on-azure-vms"></a>Guia de guia de introdução para a instalação manual de ocorrência única SAP HANA no Azure VMs

## <a name="introduction"></a>Introdução

Este guia de guia de introdução irá ajudar a configurar um único instância SAP HANA protótipo/demonstração sistema no Azure VMs por uma instalação manual de SAP NetWeaver 7.5 e SAP HANA SP12.
O guia pressupõe que o leitor está familiarizado com noções básicas do Azure IaaS como como implementar máquinas virtuais ou redes virtuais quer através do Azure portal ou Powershell/clip, incluindo a opção para utilizar modelos de json. Além disso é esperado que o leitor está familiarizado com SAP HANA, SAP NetWeaver e como instalá-lo no local.

É esperado que o leitor está em mente a documentação do SAP Azure geral mencionada na secção informações gerais no final deste artigo.

Devido a restrição para sistemas de que não sejam produção, este guia não será abrangem tópicos como HA, fazer cópia de segurança, DR, alto desempenho ou considerações de segurança especial.

A configuração de exemplo foi concluída com duas máquinas virtuais para realizar uma instalação SAP NetWeaver distribuída através do modelo de Gestor de recursos do Azure como SAP-Linux-Azure só é suportada no Gestor de recursos do Azure e não o modelo clássico. Hiperligações para mais informações sobre o Gestor de recursos do Azure podem ser encontradas na secção informações gerais no final deste artigo.

Estes foram VMs o duas teste utilizadas para a instalação de exemplo:

* Hana-appsrv (tipo DS3) para alojar a instância NW 7.5 ASCS + PAS
* Hana dbsrv (tipo GS4) para o anfitrião HANA SP12
* ambas as VMs pertenceram a uma rede virtual Azure (azure-hana-teste-vnet)
* SO em ambos os casos foi SLES 12 SP1


Tenha em atenção o facto de apesar que partir de Julho de 2016 SAP HANA totalmente só é suportada para sistemas de produção p/OLAP (b) num tipo de Azure VM GS5. Para fins onde um não é esperado apoio SAP oficial de teste é fino utilizar algo mais pequeno, como, por exemplo, GS4.
O que devem ser sempre utilizado para HANA SAP no Azure é o Azure premium armazenamento para ficheiros de dados e de registo HANA - Consulte mais abaixo da secção "Configuração disco". Em relação à mais detalhes sobre que SAP produtos são suportadas pelo Azure verificar a secção informações gerais no final deste artigo.


O guia descreve duas formas diferentes para instalar manualmente SAP HANA no Azure VMs:

* instalar o SAP HANA através do SAP Software aprovisionamento Gestor (SWPM) como parte de uma instalação NetWeaver distribuída no passo "instância de base de dados"
* instalar o HANA SAP utilizando o hdblcm de ferramenta HANA ciclo de vida Manager e, em seguida, instalar NetWeaver posteriormente

Também é possível utilizar SWPM e instalar todos os componentes (SAP HANA application server do SAP, instância ASCS, interface gráfica do SAP) numa VM único. Esta opção não é descrita neste artigo, mas os itens que devem ser consideradas são os mesmos.

Antes de iniciar a instalação de uma secção a seguir as listas de verificação abaixo sobre como configurar os teste Azure VMs deve ser lido para evitar erros básicos várias que irão acontecer ao utilizar apenas uma configuração de Azure VM predefinida.


## <a name="checklist-sap-hana-installation-via-sap-swpm"></a>Instalação de SAP HANA da lista de verificação através do SAP SWPM

Esta é uma lista de verificação simple da chave de itens relacionados com uma instalação de SAP HANA ocorrência única manual para demonstração ou protótipos pursposes através do SAP SWPM efetuar uma distribuído NW 7.5 da SAP instalar. Os itens individuais são explicados e mostrados no formulário de capturas de ecrã mais detalhadamente em todo o artigo:

* criar uma rede virtual Azure que irá incluir os duas teste VMs 
* implementar duas Azure VMs com o SO SLES 12 SP1 através do modelo de Gestor de recursos do Azure 
* anexar dois discos armazenamento padrão para o servidor de aplicação VM (por exemplo, 75GB e 500GB)
* anexar quatro discos para o servidor de HANA DB VM - discos de armazenamento padrão 2 como para o servidor de aplicação VM + 2 discos de armazenamento de prémio (por exemplo, 2x512GB)
* Dependendo do tamanho e/ou débito requisitos anexar vários discos e criar volumes às riscas quer utilizar lvm ou mdadm no nível de SO dentro da VM
* criar sistemas de ficheiros XFS discos anexados / lógicos volumes
* montagem os sistemas de ficheiro XFS novos no nível do SO. Utilize um sistema de ficheiros para manter a todo o software do SAP e o outro por exemplo, para o directório de sapmnt e talvez cópias de segurança. Na base de dados SAP HANA montagem do servidor a XFS sistemas de ficheiros nos discos premium armazenamento como /hana e /usr/sap este é todas as necessárias para evitar que o sistema de ficheiros de raiz que não é demasiado grande no Linux Azure VMs preenche para cima
* Introduza os endereços de local ip do teste VMs no/etc/anfitriões
* Introduza o parâmetro nofail na /etc/fstab
* Definir parâmetros de kernel de acordo com a nota SAP HANA-SLES-12 (ver detalhes mais abaixo na secção de parâmetros não kernel)
* Adicionar espaço de trocar
* Se pretendia - instale um ambiente de trabalho de gráfico no teste VMs. Caso contrário, utiliza uma instalação remota sapinst
* Transferir o software do SAP do marketplace de serviço do SAP
* Instale a instância do SAP ASCS no servidor aplicação VM
* Partilhe o directório sapmnt através do NFS entre o teste VMs (servidor de aplicação VM é o servidor NFS)
* Instale a instância de base de dados, incluindo HANA através do SWPM no servidor DB VM
* Instale o PAS no servidor de aplicação VM
* Inicie o SAP MC e ligue-se, por exemplo, através da interface gráfica do SAP / HANA Studio 



## <a name="checklist-sap-hana-installation-via-hdblcm"></a>Instalação de SAP HANA da lista de verificação através de hdblcm

Esta é uma lista de verificação simple da chave de itens relacionados com uma instalação de SAP HANA ocorrência única manual para demonstração ou protótipos pursposes através do SAP SWPM efetuar uma distribuído NW 7.5 da SAP instalar. Os itens individuais são explicados e mostrados no formulário de capturas de ecrã mais detalhadamente em todo o artigo:

* criar uma rede virtual Azure que irá incluir os duas teste VMs 
* implementar duas Azure VMs com o SO SLES 12 SP1 através do modelo de Gestor de recursos do Azure 
* anexar dois discos armazenamento padrão para o servidor de aplicação VM (por exemplo, 75GB e 500GB)
* anexar quatro discos para o servidor de HANA DB VM - 2 armazenamento padrão, como para o servidor de aplicação VM + 2 discos de armazenamento de prémio (por exemplo, 2x512GB)
* Dependendo do tamanho e/ou débito requisitos anexar vários discos e criar volumes às riscas quer utilizar lvm ou mdadm no nível de SO dentro da VM
* criar sistemas de ficheiros XFS discos anexados / lógicos volumes
* montagem os sistemas de ficheiro XFS novos no nível do SO. Utilize um sistema de ficheiros para manter a todo o software do SAP e o outro por exemplo, para o directório de sapmnt e talvez cópias de segurança. Na base de dados SAP HANA montagem do servidor a XFS sistemas de ficheiros nos discos premium armazenamento como /hana e /usr/sap este é todas as necessárias para evitar que o sistema de ficheiros de raiz que não é demasiado grande no Linux Azure VMs preenche para cima
* Introduza os endereços de local ip do teste VMs no/etc/anfitriões
* Introduza o parâmetro nofail na /etc/fstab
* Definir parâmetros de kernel de acordo com a nota SAP HANA-SLES-12 (ver detalhes mais abaixo na secção de parâmetros não kernel)
* Adicionar espaço de trocar
* Se pretendia - instale um ambiente de trabalho de gráfico no teste VMs. Caso contrário, utiliza uma instalação remota sapinst
* Transferir o software do SAP do marketplace de serviço do SAP
* Criar grupo "sapsys" com o id de grupo 1001 na VM de servidor de DB HANA
* instalar SAP HANA no servidor DB VM utilizando hdblcm
* Instale a instância do SAP ASCS no servidor aplicação VM
* Partilhe o directório sapmnt através do NFS entre o teste VMs (servidor de aplicação VM é o servidor NFS)
* Instale a instância de base de dados, incluindo HANA através do SWPM no servidor DB VM
* Instale o PAS no servidor de aplicação VM
* Inicie o SAP MC e ligue-se, por exemplo, através da interface gráfica do SAP / HANA Studio 




## <a name="prepare-azure-vms-for-manual-installation-of-sap-hana"></a>Preparar a instalação manual do SAP HANA Azure VMs

Este capítulo sobre como preparar o Azure VMs para instalação manual do SAP HANA é constituído por cinco secções que abrangem os seguintes tópicos:

* Programa de configuração do disco
* Parâmetros de kernel
* FILESYSTEMS
* anfitriões/etc /
* / etc/fstab


### <a name="disk-setup"></a>Programa de configuração do disco

O sistema de ficheiros de raiz num VM Linux no Azure é limitado tamanho. Por isso, é necessário anexar espaço em disco adicional para uma VM para executar o SAP. No caso de um servidor de aplicação do SAP que VM utilizado num ambiente puro protótipo/demonstração é fino a utilização de discos Azure armazenamento padrão. Considerando que para os ficheiros de dados e de registo de SAP HANA DB - discos de armazenamento do Azure Premium devem ser utilizadas mesmo na horizontal não sejam de produção.

Ver alguns detalhes sobre como anexar discos a uma VM Linux [aqui](virtual-machines-linux-add-disk.md)

Quando chegar a cache de disco Azure - um tem de utilizar "Nenhum" para discos que serão utilizados para armazenar os registos de transações HANA. Para HANA é ok para utilizar os ficheiros de dados leia colocação em cache. Como HANA é uma base de dados na memória depende o padrão de utilização geral quanto a cache de leitura no nível de disco Azure irá melhorar o desempenho (por exemplo, começando HANA e ler dados a partir do disco na memória).

Ver detalhes sobre o armazenamento do Windows Azure Premium [aqui](../storage/storage-premium-storage.md)

[Aqui](https://github.com/Azure/azure-quickstart-templates) encontrar modelos de json de exemplo para criar VMs.
O "101-vm-simples-linux" mostra como um modelo básico do aspeto incluindo a secção de armazenamento que adiciona um disco de dados de 100GB.

[Este artigo](virtual-machines-linux-sap-on-suse-quickstart.md) inclui algumas informações sobre como localizar uma imagem de SUSE através do Powershell ou clip e a importância para anexar um disco através do UUID.


Dependendo do tamanho dos requisitos de sistema e débito poderá ser necessário anexar vários discos em vez de um e posteriormente ao criar uma faixa definido ao longo nesses discos no nível do SO. Estes são os dois aspectos por que motivo um pretende criar uma faixa definir por vários Azure discos:

* aumentar o débito
* precisa de um sistema de ficheiros simples > 1TB como o limite de tamanho atual do disco Azure é 1TB (a partir de Julho de 2016)


Mais informações sobre as duas ferramentas principais para configurar repartição podem ser encontradas aqui:

[Artigo sobre como utilizar mdadm para configurar Linux raid de software numa VM Azure](virtual-machines-linux-configure-raid.md)

[Artigo sobre como configurar o Gestor de Volume lógicos numa VM de Azure Linux](virtual-machines-linux-configure-lvm.md)



![](./media/virtual-machines-linux-sap-hana-get-started/image003.jpg)

Durante o ensaio dois discos de armazenamento padrão Azure ambiente foram anexados para o servidor de aplicação do SAP VM. Um foi utilizado para armazenar todo o software SAP para a instalação (por exemplo, NetWeaver 7.5, interface gráfica do SAP, SAP HANA... ) e o outro para ter espaço suficiente para tudo o que podem ser necessário (por exemplo, fazer cópia de segurança, dados de teste) bem como o directório de sapmnt (por exemplo, perfis SAP) a ser partilhado entre todos os VMs que pertencem à mesma paisagem SAP.

![](./media/virtual-machines-linux-sap-hana-get-started/image004.jpg)

Contrariamente ao servidor de aplicação discos VM quatro foram anexados para o servidor do SAP HANA VM. Como antes de dois discos foram utilizados para manter o software SAP (um poderia também partilhar o disco de software do SAP através do NFS) e ter espaço suficiente, por exemplo, para cópia de segurança. Dois discos adicionais foram discos de armazenamento do Azure Premium para manter os ficheiros de dados e de registo do SAP HANA bem como o diretório /usr/sap.


### <a name="kernel-parameters"></a>Parâmetros de kernel


SAP HANA requer específicas Linux kernel as definições que não fazem parte das imagens Galeria Azure padrão e tem de ser definida manualmente. Existe uma nota SAP específica que descreve as definições. 


Nota SAP SAP HANA DB: SO definições recomendadas para SLES 12 / SLES para SAP aplicações 12: [2205917 de nota do SAP](https://launchpad.support.sap.com/#/notes/2205917)

Uma cache de páginas do reagrding tópico adicionais relacionados com a execução SAP HANA no SLES pode ser encontrada [aqui](https://www.suse.com/documentation/sles_for_sap/singlehtml/sles_for_sap_guide/sles_for_sap_guide.html#sec.s4s.configure.page-cache) na capítulo 6.1 Kernel: limite da Cache de página

Também existe uma nota SAP sobre o limite de página cache [1557506 de nota do SAP](https://service.sap.com/sap/support/notes/1557506)

SLES 12 tem uma nova ferramenta que substitui o utilitário sapconf antigo. É "adm optimizada" e existir um perfil SAP HANA especial disponível. Um pode encontrar mais detalhes sobre esta ferramenta seguir as duas ligações em baixo.

Documentação SLES sobre adm optimizado perfil sap-hana pode ser encontrada [aqui](https://www.suse.com/documentation/sles-for-sap-12/book_s4s/data/sec_s4s_configure_sapconf.html)

Documentação SLES sobre adm optimizado perfil sap hana - pode ser encontrado sistemas de otimização do capítulo 6.2 das cargas de trabalho do SAP com adm optimizada - [aqui](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/book_s4s/book_s4s.pdf)


![](./media/virtual-machines-linux-sap-hana-get-started/image005.jpg)

Aqui um pode ver como "adm optimizada" mudaram o transparent_hugepage bem como os valores de numa_balancing de acordo com as definições de SAP HANA necessárias.


Para tornar as definições de kernel SAP HANA permanentes um tem de utilizar grub2 no SLES 12. Mais informações sobre grub2 podem ser encontradas [aqui](https://www.suse.com/documentation/sled-12/book_sle_admin/data/sec_grub2_file_structure.html)


![](./media/virtual-machines-linux-sap-hana-get-started/image006.jpg)

Esta captura de ecrã mostra como as definições de kernel foram alteradas no ficheiro de configuração e, em seguida, "compiladas" através do grub2 mkconfig


![](./media/virtual-machines-linux-sap-hana-get-started/image007.jpg)

Outra opção seria alterar as definições através do Yast e as definições do parâmetro kernel carregador de arranque.


### <a name="filesystems"></a>FILESYSTEMS 

![](./media/virtual-machines-linux-sap-hana-get-started/image008.jpg)

Aqui um pode ver os sistemas de duas ficheiro que foram criados no servidor de aplicação SAP VM na parte superior de dois discos anexados armazenamento padrão Azure. Ambas as filesystems são do tipo XFS e instalados para /sapdata e /sapsoftware.

Não é obrigatório fazê-lo desta forma. Existem diferentes opções como estrutura do espaço de disco.
É o aspecto mais importante evitar que o sistema de ficheiros de raiz é executada espaço. 


![](./media/virtual-machines-linux-sap-hana-get-started/image009.jpg)

Em relação à VM do SAP HANA DB é importante saber que durante uma instalação de base de dados através do sapinst (swpm) e utilizar a opção de instalação "típica" simples apenas-instalará conteúdos por predefinição em /hana e /usr/sap. A predefinição para cópia de segurança do SAP HANA registo, por exemplo, está em /usr/sap.
Preferir antes de se trata chave para evitar que o sistema de ficheiros de raiz é executado espaço. Por conseguinte, um deve certificar-se de que existe espaço suficiente em /hana e /usr/sap antes de instalar SAP HANA através do swpm.

[Este artigo](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm) a partir do SAP descreve o esquema do sistema de ficheiros padrão do SAP HANA 


![](./media/virtual-machines-linux-sap-hana-get-started/image010.jpg)

Ao instalar o SAP NetWeaver numa imagem da Galeria SLES 12 Azure padrão haverá uma mensagem que não existe espaço trocar. Para eliminar esta mensagem um poderia, por exemplo, adicionar manualmente um ficheiro de trocar conforme descrito neste documento através de dd, mkswap e swapon. Basta procurar "Adicionar um trocar ficheiro manualmente" [neste](https://www.suse.com/documentation/sled-12/book_sle_deployment/data/sec_yast2_i_y2_part_expert.html) artigo

Outra opção é configurar trocar espaço através do agente de Linux VM. Mais informações podem ser encontradas [aqui](virtual-machines-linux-agent-user-guide.md)


### <a name="etchosts"></a>anfitriões/etc /

![](./media/virtual-machines-linux-sap-hana-get-started/image011.jpg)

Outro aspecto importante antes de começar a instalar SAP é incluir nomes de anfitriões e endereços IP da VMs SAP no ficheiro de /etc/hosts. Um deve implementar todos os VMs de SAP dentro de uma rede virtual Azure e, em seguida, utilize os endereços IP internos.

### <a name="etcfstab"></a>/ etc/fstab

![](./media/virtual-machines-linux-sap-hana-get-started/image000c.jpg)

Durante a fase de teste que instalado para ser uma boa ideia para adicionar o parâmetro nofail fstab. Se algum problema com os discos a VM seria ainda surgirem e não pendurar no processo de arranque. Mas um tem de prestar vistos neste caso, o espaço em disco adicionais poderão não estar disponível e processos podem preencher o sistema de ficheiros de raiz. No caso de /hana seria em falta SAP HANA não seria iniciar apesar de todo.


## <a name="install-graphical-gnome-desktop-on-sles-12"></a>Instalar o ambiente de trabalho Gnomo gráfica no SLES 12

Este capítulo consiste em duas secitions que abrangem os seguintes tópicos:

* Instalação do ambiente de trabalho Gnomo e xrdp no SLES 12
* Executar com base em Java SAP MC com o Firefox SLES 12

Um também pode utilizar alternativas como Xterminal, VNC mas partir Set. 2016 este artigo descreve apenas xrdp.

### <a name="installation-of-gnome-desktop-and-xrdp-on-sles-12"></a>Instalação do ambiente de trabalho Gnomo e xrdp no SLES 12

Especialmente para aqueles que tem o fundo do Microsoft Windows e optar por utilizar um ambiente de trabalho gráfica diretamente na VMs Linux SAP para executar o Firefox, Sapinst, interface gráfica do SAP, SAP MC ou HANA Studio e talvez ligar-se para a VM através do RDP a partir de um computador com o Microsoft Windows existe uma forma simple para realizar esta. Apesar de esta poderão não ser apropriada por exemplo, para um servidor de base de dados de produção é ok para um ambiente do protótipo/demonstração puro. Eis os passos para instalar o ambiente de trabalho Gnomo numa VM do Azure SLES 12:

Instale o ambiente de trabalho Gnomo ao seguinte comando (por exemplo, numa janela para):

   zypper no padrão -t Gnomo basic

em seguida, instale xrdp para permitir a ligação para a VM através do RDP:

   zypper no xrdp

Editar /etc/sysconfig/windowmanager e configurar o gestor predefinido para windows para Gnomo:

   DEFAULT_WM = "Gnomo"

executar chkconfig para se certificar de que xrdp é iniciado automaticamente após reiniciar o computador: 

  chkconfig-xrdp de nível 3 no

no caso de deverá haver um problema com a ligação RDP tente reiniciar (talvez fora de uma janela para):

  reiniciar /etc/xrdp/xrdp.SH

caso o reinício xrdp como mencionados acima, não funcionam verificação se existe um ficheiro de .pid e removê-lo:

  Verifique /var/run e procure xrdp.pid   
  Remova-o e, em seguida, tente novamente o reinício



### <a name="sap-mc"></a>SAP MC


Para iniciar o MC do SAP gráficas de ' com base em Java ' para fora do Firefox a ser executada numa VM do Azure SLES 12 depois de instalar o Gnomo de ambiente de trabalho, tal como descrito na secção anterior um irão receber um erro devido em falta Plug-in de Java browser.

O URL para iniciar o MC SAP é <server>: 5 < instance_number > 13

Obter mais detalhes podem ser encontradas [aqui](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm)


![](./media/virtual-machines-linux-sap-hana-get-started/image013.jpg)

A captura de ecrã acima um pode ver como a mensagem de erro pode ter aspeto quando o plug-in de Java Browser está em falta. 

![](./media/virtual-machines-linux-sap-hana-get-started/image014.jpg)

Uma das opções para resolver o problema é simplesmente instalar o plug-in em falta através do Yast.

![](./media/virtual-machines-linux-sap-hana-get-started/image015.jpg)

O URL do SAP MC de repetição moldados um pouco tempo de caixa de diálogo da primeira pedir para ativar o plug-in.


Um problema adicional que poderá pop-up é uma mensagem de erro em relação a um ficheiro em falta: javafx.properties é muito provável que esta relacionados com a instalação de Java 1.8 que é necessário para SAP interface gráfica 7.4

A versão do IBM Java vista através do Yast não inclui este ficheiro. A solução é uma transferência Java a partir do Oracle.
Um artigo que fala sobre este problema específico que pode ser encontrado [aqui](https://scn.sap.com/thread/3908306)



## <a name="manual-sap-hana-installation-via-swpm-as-part-of-a-netweaver-75-installation"></a>Instalação manual do SAP HANA através do SWPM como parte de uma instalação NetWeaver 7.5


A seguinte lista de capturas de ecrã mostra os passos principais de instalação do SAP NetWeaver 7.5 e SAP HANA SP12 através do SWPM (sapinst). Como parte de um 7.5 NW instalação SWPM tem as capacidades de instalar também a base de dados HANA como uma única ocorrência.


![](./media/virtual-machines-linux-sap-hana-get-started/image012.jpg)

Para o teste de exemplo servidor do ambiente apenas uma ABAP app foi instalado. A opção "Sistema distribuído" foi utilizada para instalar a instância ASCS e a instância de servidor de aplicação principal num Azure VM e SAP HANA como o sistema de base de dados numa VM Azure outra.


![](./media/virtual-machines-linux-sap-hana-get-started/image016.jpg)

Assim que a instância ASCS é instalada no servidor aplicação VM e está definida para "verde" no MC o SAP diretório sapmnt que inclui, por exemplo, do directório de perfil do SAP tem de ser partilhado com o servidor do SAP HANA DB VM.
O passo de instalação DB precisa de aceder a estas informações. A melhor forma é utilizar NFS que podem ser configuradas utilizando Yast.


![](./media/virtual-machines-linux-sap-hana-get-started/image017b.jpg)

Aplicação do servidor VM diretório sapmnt deve ser partilhada através do NFS utilizando o opções "rw" assim como "no_root_squash". A predefinição é "ro" e "root_squash" que pode levar a problemas ao instalar a instância de base de dados.


![](./media/virtual-machines-linux-sap-hana-get-started/image018b.jpg)

No servidor SAP HANA DB VM a sapmnt partilhar a partir do servidor aplicação VM tem de ser configurado através de "Cliente NFS" (por exemplo, com a ajuda de Yast)


![](./media/virtual-machines-linux-sap-hana-get-started/image019.jpg)

Em seguida, um tem de iniciar sessão no servidor do SAP HANA DB VM e comece a SWPM para concluir o passo seguinte da instalação NW 7.5 distribuída - "Instância de base de dados".


![](./media/virtual-machines-linux-sap-hana-get-started/image035b.jpg)

Relacionados com a instalação de SAP HANA aí não é de facto demasiado para introduzir após selecionar uma instalação "típica". Além disso, o caminho para o suporte de installatiom um tem que introduzir um SID DB, o nome de anfitrião, o número da instância e a palavra-passe de administrador de sobre DB.

 
![](./media/virtual-machines-linux-sap-hana-get-started/image036b.jpg)

Passo seguinte é introduzir a palavra-passe para o esquema DBACOCKPIT.



![](./media/virtual-machines-linux-sap-hana-get-started/image037b.jpg)

Em seguida, vem a pergunta para a palavra-passe do SAPABAP1 esquema.


![](./media/virtual-machines-linux-sap-hana-get-started/image023.jpg)

No final deverá haver apenas verdes marcas de verificação à frente de cada fase do processo de instalação DB e a caixa de mensagem pequeno que é apresentado deverá aparecer "execução de... Base de dados que foi concluída instância".

 
![](./media/virtual-machines-linux-sap-hana-get-started/image024.jpg)

Após a instalação efetuada com êxito, o MC SAP também deve ser a instância DB como "verde" e a lista completa dos processos SAP HANA (por exemplo, hdbindexserver, hdbcompileserver)


![](./media/virtual-machines-linux-sap-hana-get-started/image025.jpg)

Esta captura de ecrã mostra partes da estrutura do ficheiro em /hana/shared que SWPM criado durante a instalação HANA. Não tiver nenhuma opção para especificar um caminho diferente. Por conseguinte, é tão importante para montagem espaço em disco adicional em /hana antes da instalação do SAP HANA através do SWPM para evitar que o sistema de ficheiros de raiz é executada espaço livre.


![](./media/virtual-machines-linux-sap-hana-get-started/image026.jpg)

Aqui um pode ver a mesma coisa conforme descrito em antes do diretório /usr/sap.


![](./media/virtual-machines-linux-sap-hana-get-started/image027b.jpg)

O último passo da instalação do ABAP distribuído é "Primária instância da aplicação servidor"


![](./media/virtual-machines-linux-sap-hana-get-started/image028b.jpg)

Quando o PAS, bem como a interface gráfica do SAP tem instalada uma pode verificar através da transação "dbacockpit" que tudo tem o aspeto para a direita com a instalação de SAP HANA.

 
![](./media/virtual-machines-linux-sap-hana-get-started/image038b.jpg)

Como um último passo um poderia instalar SAP HANA Studio no servidor de aplicação SAP VM e ligar-se para a instância de SAP HANA em execução no servidor DB VM.




## <a name="manual-sap-hana-installation-via-hana-life-cycle-manager-tool-hdblcm"></a>Instalação manual de SAP HANA através do ciclo de vida HANA Gestor hdblcm de ferramenta


Para além de instalação do SAP HANA como parte de uma instalação distribuída através do SWPM também é realista quanto pela primeira vez instalar autónomo HANA utilizando hdblcm e em seguida, instalar, por exemplo, SAP NetWeaver 7.5 posteriormente. A lista de capturas de ecrã abaixo mostra como isto funciona.

Seguem-se três fontes de informações sobre a ferramenta de hdblcm HANA:

[Escolher o HDBLCM HANA SAP correta para a sua tarefa](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)

[Ferramentas de gestão de ciclo de vida do SAP HANA](http://saphanatutorial.com/sap-hana-lifecycle-management-tools/)

[Guia de atualização e de instalação do SAP HANA Server](http://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)



![](./media/virtual-machines-linux-sap-hana-get-started/image030.jpg)

Para evitar que problemas mais tarde com uma definição de id de grupo predefinida para o \<HANA SID\>utilizador adm (criado pela ferramenta hdblcm) um deve definir um novo grupo designado "sapsys" com o id de grupo 1001 antes de instalar HANA SAP utilizando hdblcm.




![](./media/virtual-machines-linux-sap-hana-get-started/image031.jpg)

Iniciar hdblcm a primeira vez haverá um menu início simples onde uma contém para selecionar o item "Instalar novo sistema" 1



![](./media/virtual-machines-linux-sap-hana-get-started/image032.jpg)

Esta captura de ecrã um pode ver todas as opções de chaves que foram introduzidas antes. Importante - directórios que foram com o nome para volumes de dados e registo HANA, bem como o caminho de instalação (/ hana/partilhado neste exemplo) e/usr/sap não deve fazer parte do sistema de ficheiros raiz mas pertence discos de dados Azure que foram anexados para a VM conforme descrito na secção Configuração Azure VM. Isto irá evitar o risco de que o sistema de ficheiros de raiz poderá ser executado espaço.
Um também pode ver que o utilizador de administração do HANA tem id de utilizador 1005 e faz parte do grupo sapsys (id 1001) que tenha sido definido antes da instalação.



![](./media/virtual-machines-linux-sap-hana-get-started/image033.jpg)

Um pode verificar a HANA \<HANA SID\>detalhes do utilizador adm (azdadm neste exemplo) no/etc/palavra-passe



![](./media/virtual-machines-linux-sap-hana-get-started/image034.jpg)

Depois de instalar o SAP HANA utilizando hdblcm podem ser visto no SAP HANA Studio. Esquema de SAPABAP1 que inclui, por exemplo, todas as SAP NetWeaver tabelas ainda não está disponível.



![](./media/virtual-machines-linux-sap-hana-get-started/image035b.jpg)

Depois de instalar SAP HANA um pode instalar SAP NetWeaver sobre a mesma. Neste exemplo que foi feito utilizando "instalação distribuída" através do SWPM conforme descrito na secção correspondente acima.
Quando instalar apenas a instância de base de dados através do SWPM um tem de introduzir os mesmos dados como antes com hdblcm (por exemplo, nome do anfitrião, HANA SID, número de instância). SWPM, em seguida, irá utilizar a instalação HANA existente e adicione esquemas adicionais.



![](./media/virtual-machines-linux-sap-hana-get-started/image036b.jpg)

Esta é a imagem do passo de instalação SWPM onde um tem de introduzir dados relativamente ao esquema de DBACOCKPIT.


![](./media/virtual-machines-linux-sap-hana-get-started/image037b.jpg)

Em seguida, SWPM espera introduzir dados acerca do esquema de SAPABAP1.


![](./media/virtual-machines-linux-sap-hana-get-started/image038b.jpg)

Assim que a instalação de instância da base de dados SWPM estar concluída uma pode ver o esquema de SAPABAP1 no HANA Studio.



![](./media/virtual-machines-linux-sap-hana-get-started/image039b.jpg)

E finalmente após a instalação do servidor de aplicação do SAP e interface gráfica do SAP um deverá poder verificar a instância HANA DB da transação "dbacockpit".




## <a name="general-information-related-to-sap-azure-certifications-running-sap-hana-on-azure-and-sap-software-download"></a>Informações gerais relacionadas com certificações SAP Azure, executar SAP HANA no Azure e pela SAP, transferência de software

* geral SAP Azure docu sobre como executar SAP no Azure com sistema operativo Windows no modo clássico: [Utilizar SAP em máquinas virtuais de Windows no Azure](virtual-machines-windows-classic-sap-get-started.md)

* informações sobre modelos SAP existentes para uma utilização pelos clientes: [Modelos de guia de introdução do Azure para SAP](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/)

* geral SAP Azure docu sobre como executar SAP no Azure com Linux SO no modelo de Gestor de recursos do Azure: [Utilizar SAP em máquinas virtuais do Linux (VMs)](virtual-machines-linux-sap-get-started.md)

* diretório de hardware SAP HANA certificado, que enumera os tipos de Azure VM são suportados para produção: [Certificação HANA SAP® Hardware diretório](https://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html)

* informações sobre tamanhos de máquina virtual especialmente para Linux cargas de trabalho: [tamanhos para máquinas virtuais no Azure](virtual-machines-linux-sizes.md)

* Nota SAP, que lista todos os suportadas produtos SAP no Azure e tipos de Azure VM suportados para SAP: [1928533 de nota do SAP](https://launchpad.support.sap.com/#/notes/1928533/E)

* Nota SAP sobre SAP "melhorado monitorização" com Linux VMs no Azure: [2191498 de nota do SAP](https://launchpad.support.sap.com/#/notes/2191498/E)

* SAP HANA oferecer no Azure "Grandes instâncias". É importante compreender que não se trata sobre como executar SAP HANA no Azure VMs mas híbrida do ambiente onde os servidores de aplicação do SAP executam no Azure VMs mas SAP HANA é executado em servidores de base: [2316233 de nota do SAP](https://launchpad.support.sap.com/#/notes/2316233/E)

* Nota do SAP com informações sobre SAPOSCOL no Linux: [1102124 de nota do SAP](https://launchpad.support.sap.com/#/notes/1102124/E)

* Métricas de monitorização para SAP no Microsoft Azure de chave: [SAP nota 2178632](https://launchpad.support.sap.com/#/notes/2178632/E)

* Informações sobre o Gestor de recursos do Azure: [Descrição geral do Gestor de recursos do Azure](../azure-resource-manager/resource-group-overview.md)

* Informações sobre como implementar Linux VMs através de modelos: [Implementar e gerir máquinas virtuais através da utilização de modelos de Gestor de recursos do Azure e o clip do Azure](virtual-machines-linux-cli-deploy-templates.md)

* Modelos de comparação de implementação entre o Gestor de recursos do Azure e clássica: [Gestor de recursos do Azure vs. implementação clássica: compreender os modelos de implementação e o estado dos seus recursos](../resource-manager-deployment-model.md)

* Transfira NetWeaver 7.5 para Linux/HANA de mercado de serviço do SAP:![](./media/virtual-machines-linux-sap-hana-get-started/image001.jpg)

* Transferir HANA SP12 plataforma Edition do Marketplace de serviço do SAP:![](./media/virtual-machines-linux-sap-hana-get-started/image002.jpg)

