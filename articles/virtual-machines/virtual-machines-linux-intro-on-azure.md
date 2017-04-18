<properties
    pageTitle="Introdução ao Linux no Azure | Microsoft Azure"
    description="Saiba como utilizar máquinas virtuais de Linux no Azure."
    services="virtual-machines-linux"
    documentationCenter="python"
    authors="szarkos"
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="szark"/>

#<a name="introduction-to-linux-on-azure"></a>Introdução ao Linux no Azure

Este tópico fornece uma descrição geral de alguns aspetos da máquinas virtuais de Linux na nuvem Azure. Implementar uma máquina de virtual Linux é um processo simples utilizar uma imagem a partir da galeria.


## <a name="authentication-usernames-passwords-and-ssh-keys"></a>Autenticação: Nomes de utilizador, palavras-passe e SSH teclas

Ao criar uma máquina de virtual Linux através do portal clássico Azure, são-lhe pedido para fornecer um nome de utilizador, palavra-passe ou uma chave pública SSH. A opção de um nome de utilizador para implementar uma máquina de virtual Linux no Azure está sujeito a restrição seguinte: nomes das contas do sistema (UID < 100) já se encontra presente na máquina virtual não são permitidos, 'de raiz' por exemplo.


 - Consulte o artigo [criar uma máquina de Virtual com Linux](virtual-machines-linux-quick-create-cli.md)
 - Veja [como utilizar SSH com Linux no Azure](virtual-machines-linux-mac-create-ssh-keys.md)


## <a name="obtaining-superuser-privileges-using-sudo"></a>Obter privilégios super utilizador utilizando`sudo`

A conta de utilizador for especificada durante a implementação de instância máquinas virtuais no Azure é uma conta com privilégios. Esta conta está configurada pelo agente Linux Azure possam elevar os privilégios para raiz (conta super utilizador) utilizando o `sudo` utility. Assim que tiver sessão iniciada no utilizando esta conta de utilizador, poderá executar comandos como raiz utilizando a sintaxe de comando

    # sudo <COMMAND>

Opcionalmente, pode obter uma shell de raiz utilizando **sudo s**.

- Consulte o artigo [utilizar privilégios de raiz em máquinas virtuais de Linux no Azure](virtual-machines-linux-use-root-privileges.md)


## <a name="firewall-configuration"></a>Configuração da firewall

Azure fornece um filtro de pacote de entrada que restringe o conectividade a portas especificadas no portal do Azure clássico. Por predefinição, a porta de permitidos apenas é SSH. Pode abrir o acesso às portas adicionais no seu computador de virtual Linux configurando os pontos finais no portal do Azure clássico:

 - Consulte o artigo: [como configurar os pontos finais para uma Máquina Virtual](virtual-machines-windows-classic-setup-endpoints.md)

As imagens Linux na Galeria de Azure não ativar a firewall *iptables* por predefinição. Caso deseje, a firewall pode ser configurada para fornecem capacidades de filtragem adicionais.


## <a name="hostname-changes"></a>Alterações de nome do anfitrião

Quando implementar uma instância de uma imagem do Linux inicialmente, são necessários para fornecer um nome de anfitrião para a máquina virtual. Assim que a máquina virtual está em execução, este nome do anfitrião é publicado para os servidores DNS de plataforma para que várias máquinas virtuais ligadas umas às outras, pode executar pesquisas de endereço IP com nomes de anfitriões.

Se as alterações de nome do anfitrião são pretendidas depois de ter sido implementada uma máquina virtual, utilize o comando

    # sudo hostname <newname>

O agente de Linux Azure inclui uma funcionalidade para detetar automaticamente esta alteração de nome e configurar corretamente máquina virtual para persistirem esta alteração e publicar esta alteração para os servidores DNS de plataforma.

 - [Guia de utilizador do agente de Linux Azure](virtual-machines-linux-agent-user-guide.md)

### <a name="cloud-init"></a>Nuvem-inicialização
Imagens **Ubuntu** e **CoreOS** utilizem-las na nuvem-inicialização no Azure, que fornece capacidades adicionais para iniciar uma máquina virtual.

 - [Como inserir dados personalizados](virtual-machines-windows-classic-inject-custom-data.md)
 - [Dados personalizados e na nuvem-inicialização no Microsoft Azure](https://azure.microsoft.com/blog/2014/04/21/custom-data-and-cloud-init-on-windows-azure/)
 - [Criar partições do Azure trocar utilizando a inicialização da nuvem](https://wiki.ubuntu.com/AzureSwapPartitions)
 - [Como utilizar CoreOS no Azure](https://coreos.com/os/docs/latest/booting-on-azure.html)


## <a name="virtual-machine-image-capture"></a>Captura de ecrã máquina virtual

Azure fornece a capacidade para capturar o estado de uma máquina virtual existente para uma imagem que subsequentemente pode ser utilizada para implementar instâncias de máquina virtual adicionais. O agente de Linux Azure podem ser utilizados para anulação algumas da personalização que foi efetuada durante o processo de aprovisionamento. Pode seguir os passos abaixo para capturar uma máquina de virtual como uma imagem:

1. Executar **waagent-deprovision** para anular a personalização de aprovisionamento. Ou **waagent-deprovision + utilizador** para opcionalmente, eliminar a conta de utilizador especificada durante a dados de aprovisionamento e todos os associado.

2. Encerrar para baixo/para desligar a máquina virtual.

3. Clique em *captura* no portal do Azure clássico ou utilize as ferramentas de Powershell ou clip para capturar a máquina virtual como uma imagem.

 - Consulte o artigo: [como capturar uma máquina de Virtual Linux para utilizar como um modelo](virtual-machines-linux-classic-capture-image.md)


## <a name="attaching-disks"></a>Anexar discos

Cada máquina virtual tem um local, temporário *disco de recurso* ligado. Uma vez que os dados num disco recurso poderão não ser resistentes ao longo de ser reiniciado, é frequentemente utilizado por aplicações e processos em execução na máquina virtual transitória e **temporário** armazenamento de dados. Também é utilizado para armazenar a página ou trocar ficheiros para o sistema operativo.

No Linux, o disco de recursos está normalmente gerido pelo agente de Linux do Azure e automaticamente instalado para **/mnt/resource** (ou **/mnt** no Ubuntu imagens).


>[AZURE.NOTE] Tenha em atenção que o disco de recurso é um disco **temporário** , poderá ser eliminado e reformatado quando a VM for reiniciada.

Em Linux o disco de dados pode ser com o nome do kernel como `/dev/sdc`, e os utilizadores terão a partições, formatar e montagem esse recurso. Isto é abrangido passo a passo no tutorial: [como anexar um disco de dados para uma Máquina Virtual](virtual-machines-linux-classic-attach-disk.md).

 - **Consulte também:** [Configurar o Software RAID em Linux](virtual-machines-linux-configure-raid.md)  &  [Configurar LVM numa VM Linux no Azure](virtual-machines-linux-configure-lvm.md)

