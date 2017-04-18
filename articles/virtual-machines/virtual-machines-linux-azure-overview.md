 <properties
   pageTitle="Azure e Linux | Microsoft Azure"
   description="Descreve os serviços de calcular Azure, armazenamento e redes com máquinas virtuais de Linux."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines-linux"
   authors="vlivech"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="09/14/2016"
   ms.author="v-livech"/>

# <a name="azure-and-linux"></a>Azure e Linux

Microsoft Azure é uma coleção de crescimento do público integrada incluindo analytics, máquinas virtuais, bases de dados móveis, funcionamento em rede, armazenamento, os serviços em nuvem e web — ideal para as soluções de alojamento.  Microsoft Azure fornece uma plataforma de informática dimensionável que permite-lhe apenas pagar que utiliza, quando pretender-- sem ter de investir no local hardware.  Azure está pronto quando são dimensionar as soluções para cima e saída para qualquer escala necessita para as necessidades dos seus clientes do serviço.

Se estiver familiarizado com as várias funcionalidades da Amazon AWS, examine Azure vs AWS [documento de definição de mapeamento](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/).


## <a name="regions"></a>Regiões
Recursos do Microsoft Azure são distribuídos ao longo de vários regiões geográficas em todo o mundo.  "Região" representa vários centros de dados numa única área geográfica.  Partir 1 de Janeiro de 2016 inclui: 8 na América do, 2 na Europa, 6 em Ásia-Pacífico, 2 na China continente e 3 na Índia.  Se pretender que uma lista completa de todas as regiões Azure, podemos manter que uma lista de existente e recentemente anunciada regiões.

- [Regiões Azure](https://azure.microsoft.com/regions/)

## <a name="availability"></a>Disponibilidade
Ordem para a sua implementação qualifique para o nosso Contrato de nível de serviço VM 99.95, terá de implementar duas ou mais VMs executar a sua carga de trabalho dentro de uma disponibilidade definir. Isto irá assegurar-se ao seu VMs são distribuídos ao longo de vários domínios falhas nos nossos centros de dados, bem como implementadas no anfitriões com o windows de manutenção diferente. O total [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/) explica a disponibilidade de garantia de Azure como um todo.

## <a name="azure-virtual-machines--instances"></a>Azure máquinas virtuais & instâncias
Microsoft Azure suporta a execução de um número de populares Linux distribuições fornecidos e manter-se por um número de parceiros.  Irá encontrar as distribuições como vermelho chapéu Enterprise, CentOS, Debian, Ubuntu, CoreOS, RancherOS, FreeBSD e muito mais no Azure Marketplace. Vamos ativamente trabalhar com vários Linux Comunidades para adicionar versões ainda mais a lista [que Azure menção Linux Distros](virtual-machines-linux-endorsed-distros.md) .

Se o seu distro Linux preferido escolha não estiver atualmente presente na galeria, pode "trazer com os seu próprios Linux" VM através da [criação e carregar um VHD Linux no Azure](virtual-machines-linux-create-upload-generic.md).

Azure máquinas virtuais permitem-lhe implementar uma vasta gama de computação soluções de uma forma ágil. Pode implementar praticamente qualquer carga de trabalho e qualquer idioma em praticamente qualquer sistema operativo - Windows, Linux, ou um personalizado criado um partir de qualquer uma das nossa lista crescente de parceiros. Mesmo assim não vê o procura?  Não se preocupe - pode também trazer com os seus próprios imagens a partir no local.

## <a name="vm-sizes"></a>VM tamanhos
Quando implementar uma VM no Azure, passar para selecionar um tamanho da memória virtual dentro de um dos nossos série de tamanhos que é adequada para a sua carga de trabalho. O tamanho também afeta a capacidade de power, memória e armazenamento de processamento de máquina virtual. São faturada com base na quantidade de tempo a VM está em execução e consumir os recursos atribuídos. Uma lista completa de [tamanhos de máquinas virtuais](virtual-machines-linux-sizes.md).

Aqui estão algumas orientações básicas para selecionar um tamanho da memória virtual a partir de um dos nossos série (A, D, DS, G e s).

* A série VMs são nossa preço VMs simplificados para simplificada das cargas de trabalho e cenários de Dev Center/teste. São amplamente disponíveis em todas as regiões e podem ligar e utilizar todos os recursos padrão disponíveis para máquinas virtuais.
* A série os tamanhos (A8 - A11) são configurações intenso cluster especial adequadas para aplicações do cluster de computação alto desempenho.
* D série VMs foram concebidos para executar as aplicações que procura mais elevado cluster power e o desempenho do disco temporário. D série VMs fornecem processadores mais rápidos, um rácio de memória-para-core mais elevado e uma unidade de estado sólido (SSD) para o disco temporário.
* Série de Dv2, é a versão mais recente da nossa série D, funcionalidades de uma CPU mais eficazes. A CPU Dv2 série é cerca 35% mais rápido do que a CPU D série. Baseia-se a última geração 2,4 GHz Intel Xeon® E5-2673 v3 processador (Haskell) e com o Intel Turbo intensidade tecnologia 2.0, pode aceder até 3.2 GHz. A série de Dv2 tem as configurações de memória e de disco mesmo como a série de D.
* G série VMs oferecem mais memória e executar no anfitriões têm processadores da família Intel Xeon E5 V3.

Nota: DS série e VMs da série s têm acesso ao armazenamento de Premium - os nossos SSD cópias armazenamento de alto desempenho, baixa latência para das cargas de trabalho intensivos e/s. Armazenamento de Premium está disponível em determinados regiões. Para obter mais detalhes, consulte:

- [Armazenamento de Premium: Armazenamento de alto desempenho para máquina virtual Azure cargas de trabalho](../storage/storage-premium-storage.md)

## <a name="automation"></a>Automatização
Para obter uma cultura de DevOps inicial, infraestrutura todos os tem de ser código.  Quando todos a infraestrutura de encontra-se no código facilmente pode ser recriada (Portalegre servidores).  Azure funciona com a automatização de principal ferramenta como Ansible, chefe, SaltStack e Puppet.  Azure também tem o seu próprio ferramentas para automatização:

- [Modelos do Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

- [Azure VMAccess](virtual-machines-linux-using-vmaccess-extension.md)

Azure é gradual saída suporte para a [nuvem-inicialização](http://cloud-init.io/) ao longo da maioria dos Linux Distros que suportam-lo.  Atualmente são implementados Ubuntu VMs do Canonical com nuvem-inicialização ativada por predefinição.  RedHats RHEL, CentOS e Fedora suportam inicialização na nuvem, no entanto as imagens Azure mantidas pelo sistema RedHat não possui nuvem-inicialização instalado.  Para utilizar na nuvem-inicialização numa família de sistema RedHat SO, terá de criar uma imagem personalizada com a nuvem-inicialização instalado.

- [Utilizar a nuvem-inicialização no Azure Linux VMs](virtual-machines-linux-using-cloud-init.md)

## <a name="quotas"></a>Quotas de
Cada subscrição Azure tem limites de quota predefinidos no local que pode afetar a implementação de um grande número de VMs para o seu projeto. O limite atual numa base por subscrição é 20 VMs por região.  Limites de quota podem ser elevados, preenchendo um bilhetes de suporte pedir um aumento de limite.  Para obter mais detalhes sobre os limites de quota:

- [Limites do serviço de subscrição Azure](../azure-subscription-service-limits.md)


## <a name="partners"></a>Parceiros

Microsoft estreitamente funciona com os nossos parceiros para garantir que as imagens disponíveis são atualizadas e otimizadas para um tempo de execução do Azure.  Para mais informações sobre os nossos parceiros Verifique as suas páginas de marketplace abaixo.

- [Linux em distribuições menção Azure](virtual-machines-linux-endorsed-distros.md)

Sistema Redhat - [Azure Marketplace - sistema RedHat Enterprise Linux 7.2](https://azure.microsoft.com/marketplace/partners/redhat/redhatenterpriselinux72/)

Canónico - [Azure Marketplace - Ubuntu servidor 16.04 LTS](https://azure.microsoft.com/marketplace/partners/canonical/ubuntuserver1604lts/)

Debian - [Azure Marketplace - Debian 8 "Jessie"](https://azure.microsoft.com/marketplace/partners/credativ/debian8/)

FreeBSD - [Azure Marketplace - FreeBSD 10.3](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/)

CoreOS - [Azure Marketplace - CoreOS (estável)](https://azure.microsoft.com/marketplace/partners/coreos/coreosstable/)

RancherOS - [Azure Marketplace - RancherOS](https://azure.microsoft.com/marketplace/partners/rancher/rancheros/)

Bitnami - [Bitnami biblioteca para Azure](https://azure.bitnami.com/)

Mesosfera - [Azure Marketplace - mesosfera CC/SO no Azure](https://azure.microsoft.com/marketplace/partners/mesosphere/dcosdcos/)

Docker - [Azure Marketplace - o serviço de contentor Azure com Docker enxame](https://azure.microsoft.com/marketplace/partners/microsoft/acsswarms/)

Jenkins - [Azure Marketplace - CloudBees Jenkins plataforma](https://azure.microsoft.com/marketplace/partners/cloudbees/jenkins-platformjenkins-platform/)


## <a name="getting-setup-on-azure"></a>Receber o programa de configuração do Azure
Para começar a utilizar o Azure tem uma conta Azure, o clip de Azure instalado e um par de chaves públicas e privadas de SSH.

## <a name="sign-up-for-an-account"></a>Inscrever-se para uma conta
O primeiro passo sessão com a nuvem do Azure é para se inscrever para uma conta do Azure.  Ir para a página de [Inscrição de conta Azure](https://azure.microsoft.com/pricing/free-trial/) para começar a utilizar.

## <a name="install-the-cli"></a>Instalar o clip
Com a sua nova conta Azure, pode começar imediatamente utilizando o portal Azure, que é um painel de administração baseada na web.  Para gerir a nuvem do Azure através da linha de comandos, instale o `azure-cli`.  Instale o [Azure clip ](../xplat-cli-install.md)no seu estação de trabalho Mac ou Linux.

## <a name="create-an-ssh-key-pair"></a>Criar um par de chaves SSH
Agora, tem uma conta Azure, o portal de Azure web e o clip do Azure.  O passo seguinte é criar um par de chaves SSH é utilizado para SSH para Linux sem utilizar uma palavra-passe.  [Criar SSH teclas na Linux e Mac](virtual-machines-linux-mac-create-ssh-keys.md) para ativar a palavra-passe menos inícios de sessão e uma melhor segurança.

## <a name="getting-started-with-linux-on-microsoft-azure"></a>Introdução ao Linux no Microsoft Azure
Com a configuração de conta Azure, o clip de Azure instalado e chaves SSH criadas está agora pronto para iniciar a criação de uma infraestrutura na nuvem Azure.  A primeira tarefa é criar algumas VMs.

## <a name="create-a-vm-using-the-cli"></a>Criar uma VM utilizando o clip
Criar uma VM Linux utilizando o clip é uma forma rápida de implementar uma VM sem deixar o terminal que está a trabalhar.  Tudo o que pode especificar no portal de web está disponível através de um sinalizador da linha de comandos ou mudar.  

- [Criar uma VM Linux utilizando o clip](virtual-machines-linux-quick-create-cli.md)

## <a name="create-a-vm-in-the-portal"></a>Criar uma VM no portal
Criar uma VM Linux no portal do Azure web é uma forma de facilmente aponte e clique nas várias opções para aceder a uma implementação.  Em vez de utilizar sinalizadores da linha de comandos ou parâmetros, são conseguem ver um esquema web totalmente de várias opções e definições.  Tudo disponíveis através da interface da linha de comandos também está disponível no portal.

- [Criar uma VM Linux através do Portal](virtual-machines-linux-quick-create-portal.md)

## <a name="login-using-ssh-without-a-password"></a>Inicie sessão utilizando o SSH sem uma palavra-passe
A VM agora está em execução no Azure e estiver pronto para iniciar sessão.  Utilizar palavras-passe para iniciar sessão através de SSH é insegura e morosa.  Utilizar teclas SSH é a forma mais segura e também a forma mais rápida de início de sessão.  Quando cria Linux VM através do portal ou o clip, tem duas opções de autenticação.  Se optar por uma palavra-passe para SSH, Azure configura VM para permitir que os inícios de sessão através de palavras-passe.  Se optar por utilizar uma chave pública SSH, Azure configura VM para permitir apenas os inícios de sessão através de teclas SSH e desativa os inícios de sessão de palavra-passe. Para proteger a sua VM Linux ao permitir apenas SSH chaves inícios de sessão, utilize a opção de chave pública do SSH durante a criação de VM no portal ou clip.

- [Desativar SSH palavras-passe a VM Linux configurando SSHD](virtual-machines-linux-mac-disable-ssh-password-usage.md)

## <a name="related-azure-components"></a>Componentes Azure relacionados

## <a name="storage"></a>Armazenamento

- [Introdução ao armazenamento do Microsoft Azure](../storage/storage-introduction.md)

- [Adicionar um disco a uma VM Linux utilizando o clip do azure](virtual-machines-linux-add-disk.md)

- [Como anexar um disco de dados a uma VM Linux no portal do Azure](virtual-machines-linux-attach-disk-portal.md)

## <a name="networking"></a>Funcionamento em rede

- [Descrição geral de rede virtual](../virtual-network/virtual-networks-overview.md)

- [Endereços IP no Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md)

- [Abertura de portas para um VM Linux no Azure](virtual-machines-linux-nsg-quickstart.md)

- [Criar um nome de domínio completamente qualificado no portal do Azure](virtual-machines-linux-portal-create-fqdn.md)


## <a name="containers"></a>Contentores

- [Máquinas virtuais e contentores no Azure](virtual-machines-linux-containers.md)

- [Introdução de serviço de contentor Azure](../container-service/container-service-intro.md)

- [Implementar um cluster de serviço de contentor do Azure](../container-service/container-service-deployment.md)

## <a name="next-steps"></a>Próximos passos

Tem agora uma descrição geral do Linux no Azure.  O passo seguinte é começar a criar VMs alguns!

- [Criar uma VM Linux no Azure através do Portal](virtual-machines-linux-quick-create-portal.md)

- [Criar uma VM Linux no Azure utilizando o clip](virtual-machines-linux-quick-create-cli.md)
