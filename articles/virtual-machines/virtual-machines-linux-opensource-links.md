<properties
    pageTitle="Linux e abrir-origem computação no Azure | Microsoft Azure"
    description="Lista Linux e informática de abrir origem artigos no Azure, incluindo a utilização de Linux básica, alguns conceitos fundamentais sobre executar ou carregar as imagens Linux no Azure e outro conteúdo sobre tecnologias específicas e otimizações."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="squillace"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="06/27/2016"
    ms.author="rasquill"/>



# <a name="linux-and-open-source-computing-on-azure"></a>Linux e abrir-origem computação no Azure

Localize toda a documentação que tem de criar e gerir baseado em Linux máquinas virtuais no modelo de implementação clássico.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## <a name="get-started"></a>Introdução
- [Introdução para Linux no Azure](virtual-machines-linux-intro-on-azure.md)
- [Perguntas mais pergunta sobre máquinas virtuais do Azure criados com o modelo clássico de implementação](virtual-machines-linux-classic-faq.md)
- [Acerca de imagens para máquinas virtuais](virtual-machines-linux-classic-about-images.md)
- [Carregar a sua própria imagem Distro](virtual-machines-linux-classic-create-upload-vhd.md) (e também instruções utilizando uma [Distribuição Azure-Endorsed](virtual-machines-linux-endorsed-distros.md))
- [Inicie sessão num utilizando VM Linux portal clássico do Azure](virtual-machines-linux-mac-create-ssh-keys.md)

## <a name="set-up"></a>Configurar o

- [Instalar o Azure Interface de comandos (Azure CLI)](../xplat-cli-install.md)


## <a name="tutorials"></a>Tutoriais

- [Instalar a pilha de luz numa máquina virtual Linux no Azure](virtual-machines-linux-create-lamp-stack.md)
- [Rubi na aplicação Web do carris numa VM Azure](linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md)
- [Como: instalar Apache Qpid protão-C para AMQP e Bus de serviço](../service-bus-messaging/service-bus-amqp-apache.md)

### <a name="databases"></a>Bases de dados
- [Otimizar o desempenho do MySQL no Azure](virtual-machines-linux-classic-optimize-mysql.md)
- [Clusters do MySQL](virtual-machines-linux-classic-mysql-cluster.md)
- [Em execução Cassandra com Linux no Azure e aceder ao mesmo Node.js](virtual-machines-linux-classic-cassandra-nodejs.md)
- [Criar um cluster de MariaDbs vários mestre](virtual-machines-linux-classic-mariadb-mysql-cluster.md)

### <a name="hpc"></a>HPC
- [Introdução ao Linux cluster nós num cluster aos Pack no Azure](virtual-machines-linux-classic-hpcpack-cluster.md)
- [Execute NAMD com o Microsoft aos Pack em nós de cluster de Linux no Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)
- [Configurar um cluster de Linux RDMA para executar as aplicações de MPI](virtual-machines-linux-classic-rdma-cluster.md)

### <a name="docker"></a>Docker
- [Utilizando a extensão VM Docker do Azure da linha de comandos a Interface (Azure CLI)](virtual-machines-linux-classic-cli-use-docker.md)
- [Utilizando a extensão de VM Docker a partir do portal do Azure](virtual-machines-linux-classic-portal-use-docker.md)
- [Como utilizar docker máquina no Azure](virtual-machines-linux-docker-machine.md)

### <a name="ubuntu"></a>Ubuntu
- [Como: Clusters do MySQL](virtual-machines-linux-classic-mysql-cluster.md)
- [Como: Node.js e Cassandra](virtual-machines-linux-classic-cassandra-nodejs.md)

### <a name="opensuse"></a>OpenSUSE
- [Como: instalar e executar MySQL](virtual-machines-linux-classic-mysql-on-opensuse.md)

### <a name="coreos"></a>CoreOS
- [Como: utilizar CoreOS no Azure](https://coreos.com/os/docs/latest/booting-on-azure.html)


## <a name="planning"></a>Planeamento
- [Diretrizes de implementação de serviços de infraestrutura Azure](virtual-machines-linux-infrastructure-subscription-accounts-guidelines.md)
- [Selecionar Linux liste](virtual-machines-linux-usernames.md)
- [Como configurar a disponibilidade de configurar para máquinas virtuais no modelo de implementação clássico](virtual-machines-linux-classic-configure-availability.md)
- [Como agendar a manutenção planeada na Azure VMs](virtual-machines-linux-planned-maintenance-schedule.md)
- [Gerir a disponibilidade de máquinas virtuais](virtual-machines-linux-manage-availability.md)
- [Manutenção planeada para máquinas virtuais de Linux no Azure](virtual-machines-linux-planned-maintenance.md)


## <a name="deployment"></a>Implementação
- [Criar uma máquina virtual personalizada em execução Linux](virtual-machines-linux-classic-createportal.md)
- [Noções básicas: capturar uma VM Linux para criar um modelo](virtual-machines-linux-classic-capture-image.md)
- [Informações para não menção distribuições](virtual-machines-linux-create-upload-generic.md)


## <a name="management"></a>Gestão

- [SSH](virtual-machines-linux-mac-create-ssh-keys.md)
- [Como repor uma palavra-passe ou propriedades SSH do Linux](virtual-machines-linux-classic-reset-access.md)
- [Utilizar a raiz](virtual-machines-linux-use-root-privileges.md)


## <a name="azure-resources"></a>Recursos Azure

- [O agente Linux Azure](virtual-machines-linux-agent-user-guide.md)
- [Funcionalidades e extensões Azure VM](virtual-machines-windows-extensions-features.md)
- [Injectando dados personalizados para uma VM para utilizar com a inicialização da nuvem](virtual-machines-windows-classic-inject-custom-data.md)


## <a name="storage"></a>Armazenamento

- [Como anexar um disco de dados a uma VM Linux](virtual-machines-linux-classic-attach-disk.md)
- [Desligar um disco de dados a partir de uma VM Linux](virtual-machines-linux-classic-detach-disk.md)
- [RAID](virtual-machines-linux-configure-raid.md)


## <a name="networking"></a>Funcionamento em rede
- [Como configurar os pontos finais num computador virtual clássico no Azure](virtual-machines-linux-classic-setup-endpoints.md)


## <a name="troubleshooting"></a>Resolução de problemas
- [Resolver problemas de ligações de Shell seguro (SSH) para uma máquina de virtual baseado em Linux Azure](virtual-machines-linux-troubleshoot-ssh-connection.md)
- [Resolução de problemas de implementação clássica com a criação de uma nova máquina de virtual Linux no Azure](virtual-machines-linux-classic-troubleshoot-deployment-new-vm.md)  
- [Implementação clássica resolver problemas relacionados com reiniciar ou redimensionar uma máquina de Virtual Linux existente no Azure](virtual-machines-linux-classic-restart-resize-error-troubleshooting.md) 


## <a name="reference"></a>Referência

- [Azure comandos de clip no modo de gestão de serviço do Azure (asm)](../virtual-machines-command-line-tools.md)
- [Gestão de serviços Azure REST API](https://msdn.microsoft.com/library/azure/ee460799.aspx)




## <a name="general-links"></a>Ligações de geral
As ligações seguintes destinam-se a Microsoft blogues, páginas do Technet e sites externos em vez de documentação Azure.com acima indicado. Como Azure e o mundo informático abrir origem são mover fast destinos, é quase certo que as ligações seguintes estiver fora data, *Apesar* o facto de podemos deve fazê-os nossos melhores continuamente tópicos mais recentes de adicionar e remover aqueles desatualizado. Se podemos tenha esquecido um fórum diga-nos saber nos comentários ou submeter um pedido de solicitação a nossa [GitHub repo](https://github.com/Azure/azure-content/).

- [Em execução ASP.NET 5 no Linux utilizando Docker contentores](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)
- [Como implementar uma imagem a VM CentOS partir OpenLogic](https://azure.microsoft.com/blog/2013/01/11/deploying-openlogic-centos-images-on-windows-azure-virtual-machines/)
- [Infraestrutura de atualização SUSE](https://forums.suse.com/showthread.php?5622-New-Update-Infrastructure)
- [SUSE Linux Enterprise Server para SAP nuvem aparelho biblioteca](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver11sp3forsapcloudappliance/)
- [Edifício Linux altamente disponíveis no Azure nos 12 passos](http://blogs.technet.com/b/keithmayer/archive/2014/10/03/quick-start-guide-building-highly-available-linux-servers-in-the-cloud-on-microsoft-azure.aspx)
- [Automatizar Linux aprovisionamento no Azure com Azure clip, node.js, jhawk](http://blogs.technet.com/b/keithmayer/archive/2014/11/24/step-by-step-automated-provisioning-for-linux-in-the-cloud-with-microsoft-azure-xplat-cli-json-and-node-js-part-1.aspx)
- [GlusterFS no Azure](http://dastouri.azurewebsites.net/gluster-on-azure-part-1/)

### <a name="freebsd"></a>FreeBSD
- [FreeBSD em execução no Azure](https://azure.microsoft.com/blog/2014/05/22/running-freebsd-in-azure/)
- [Implementar mais facilmente FreeBSD](http://msopentech.com/blog/2014/10/24/easy-deploy-freebsd-microsoft-azure-vm-depot/)
- [Implementar uma imagem FreeBSD personalizada](http://msopentech.com/blog/2014/05/14/deploy-customize-freebsd-virtual-machine-image-microsoft-azure/)
- [Kaspersky AV para servidor de ficheiros Linux](https://azure.microsoft.com/marketplace/partners/kaspersky-lab/kav-for-lfs-kav-for-lfs/)

### <a name="nosql"></a>NoSQL

- [8 bases de dados de origem de abrir NoSql para Azure](http://openness.microsoft.com/blog/2014/11/03/open-source-nosql-databases-microsoft-azure/)
- [Slideshare (MSOpenTech): Experiências com CouchDb no Azure](http://www.slideshare.net/brianbenz/experiences-using-couchdb-inside-microsofts-azure-team)
- [Executar CouchDB-como-a-Service com node.js, CORS e roncador](http://msopentech.com/blog/2013/12/19/tutorial-building-multi-tier-windows-azure-web-application-use-cloudants-couchdb-service-node-js-cors-grunt-2/)

- [Redis no Windows no serviço de Cache Azure Redis](http://msopentech.com/blog/2014/05/12/redis-on-windows/)
- [Anunciar o fornecedor de estado de sessão ASP.NET para Redis versão de pré-visualização](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)

- [Blogue: RavenHQ agora disponível no Azure Marketplace](https://azure.microsoft.com/blog/2014/08/12/ravenhq-now-available-in-the-azure-store/)

### <a name="big-data"></a>Dados grandes
- [Instalar o Hadoop VMs Azure Linux](http://blogs.msdn.com/b/benjguin/archive/2013/04/05/how-to-install-hadoop-on-windows-azure-linux-virtual-machines.aspx)
- [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/)

### <a name="relational-database"></a>Base de dados relacional
- [Arquitetura de disponibilidade do MySQL alta no Microsoft Azure](http://download.microsoft.com/download/6/1/C/61C0E37C-F252-4B33-9557-42B90BA3E472/MySQL_HADR_solution_in_Azure.pdf)
- [Instalação Postgres com corosync, pg_bouncer utilizando ILB](https://github.com/chgeuer/postgres-azure)

### <a name="linux-high-performance-computing-hpc"></a>Linux alto desempenho computação (HPC)

- [Modelo de guia de introdução: giratório para cima um cluster SLURM](https://github.com/Azure/azure-quickstart-templates/tree/master/slurm) (e [no blogue](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx))
- [Modelo de guia de introdução: criar um cluster HPC com Linux cluster nós](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)

### <a name="devops-management-and-optimization"></a>Devops, gestão e Otimização

Como parte do mundo devops, gestão e otimização é bastante bom e alterar muito rapidamente, considere a lista por baixo de um ponto de partida.

- [Vídeo: Azure Virtual máquinas: utilizar chefe, Puppet e Docker para gerir Linux VMs](https://azure.microsoft.com/blog/2014/12/15/azure-virtual-machines-using-chef-puppet-and-docker-for-managing-linux-vms/)

- [Guia de concluída a implementação de cluster Kubernetes automatizado com CoreOS e ondulação](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)
- [Kubernetes Visualizer](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/)

- [Jenkins secundária Plug-in para Azure](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
- [GitHub repo: Jenkins armazenamento Plug-in para Azure](https://github.com/jenkinsci/windows-azure-storage-plugin)

- [Terceiros: Hudson secundária Plug-in para Azure](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
- [Terceiros: Armazenamento de Hudson Plug-in para Azure](https://github.com/hudson3-plugins/windows-azure-storage-plugin)

- [Vídeo: O que é chefe e como funciona?](https://msopentech.com/blog/2014/03/31/using-chef-to-manage-azure-resources/)

- [Vídeo: Como utilizar a automatização Azure com Linux VMs](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)

- [Blogue: Como fazer Powershell DSC para Linux](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
- [GitHub: Docker cliente DSC](https://github.com/anweiss/DockerClientDSC)

- [Plug-in do embalador para Azure](https://github.com/msopentech/packer-azure)
