<properties
    pageTitle="Recuperação de falhas para SQL Server e elevada disponibilidade | Microsoft Azure"
    description="Um debate dos vários tipos de estratégias HADR para SQL Server em máquinas virtuais do Azure de executar."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/20/2016"
    ms.author="MikeRayMSFT" />

# <a name="high-availability-and-disaster-recovery-for-sql-server-in-azure-virtual-machines"></a>Recuperação de disponibilidade e falhas alta para SQL Server em máquinas virtuais do Azure

## <a name="overview"></a>Descrição geral

Máquinas de virtuais do Microsoft Azure (VMs) com o SQL Server pode ajudar a reduzir o custo de uma alta disponibilidade e falhas (HADR) da base de dados solução de recuperação. A maioria das soluções do SQL Server HADR são suportadas no Azure máquinas virtuais, como só de Azure e como soluções híbrido. Uma solução só Azure, todo o sistema HADR é executado no Azure. Numa configuração híbrida, parte da solução é executado no Azure e o outro peça será executado no local na sua organização. A flexibilidade do Azure ambiente permite-lhe mover parcialmente ou completamente para Azure de cumprir o orçamento e requisitos de HADR da sua sistemas de bases de dados do SQL Server.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## <a name="understanding-the-need-for-an-hadr-solution"></a>Noções sobre a necessidade de uma solução HADR

É até que para se certificar de que o seu sistema de base de dados possui as capacidades HADR requer o contrato de nível de serviço (SLA). O facto de Azure fornece mecanismos de elevada disponibilidade, como o serviço de reparação para serviços em nuvem e deteção de recuperação de falha para máquinas virtuais, não propriamente dito garante que pode cumprir a SLA pretendida. Estes mecanismos protegem a disponibilidade de alta dos VMs mas não a disponibilidade do SQL Server a ser executado dentro de VMs alta. É possível para a instância do SQL Server falha enquanto a VM está online e saudável. Além disso, até mesmo a disponibilidade de alta mecanismos fornecidos pela Azure permitem para tempo de inatividade da VMs devido a eventos tal como a recuperação de software ou falhas de hardware e atualiza os do sistema operativo.

Além disso, Geo redundantes armazenamento (GRS) no Azure, que é implementado com uma funcionalidade denominada geo replicação, podem não ser uma solução de recuperação de falhas adequada para as bases de dados. Uma vez que geo replicação envia dados modo assíncrono, as atualizações recentes podem perder eventualidade. Mais informações sobre limitações de replicação geo estejam tapados na secção [Geo replicação não suportada para dados e ficheiros de registo em discos separados](#geo-replication-support) .

## <a name="hadr-deployment-architectures"></a>Arquitecturas de implementação de HADR

Tecnologias do SQL Server HADR que são suportadas no Azure incluem:

- [Sempre em grupos de disponibilidade](https://technet.microsoft.com/library/hh510230.aspx)
- [Espelhando as bases de dados](https://technet.microsoft.com/library/ms189852.aspx)
- [Registo de envio](https://technet.microsoft.com/library/ms187103.aspx)
- [Cópia de segurança e restauro com o serviço de armazenamento de Blobs do Azure](https://msdn.microsoft.com/library/jj919148.aspx)
- [Sempre em instâncias de Cluster de activação pós-falha](https://technet.microsoft.com/library/ms189134.aspx)

É possível combinar as tecnologias em conjunto para implementar uma solução do SQL Server que tenha elevada disponibilidade e capacidades de recuperação de falhas. Consoante a tecnologia de que utilizar uma versão híbrida poderá necessitar de um túnel VPN com a rede virtual Azure. As secções abaixo mostram-lhe algumas das arquitecturas de implementação de exemplo.

## <a name="azure-only-high-availability-solutions"></a>Só Azure: soluções de elevada disponibilidade

Pode ter uma solução de elevada disponibilidade para as bases de dados do SQL Server no Azure utilizar sempre no disponibilidade grupos ou espelhando as de base de dados.

| Tecnologia de apoio                               | Arquitecturas de exemplo                    |
| ---------------------------------------- | ---------------------------------------- |
| **Sempre em grupos de disponibilidade**        | Todas as réplicas de disponibilidade em execução no Azure VMs para elevada disponibilidade dentro da mesma região. Tem de configurar um controlador de domínio VM, porque o Windows Server activação pós-falha clusters (WSFC) requer um domínio do Active Directory.<br/> ![Sempre em grupos de disponibilidade](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_ha_always_on.gif)<br/>Para mais informações, consulte o artigo [Configurar sempre no grupos de disponibilidade no Azure (interface gráfica do utilizador)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md). |
| **Sempre em instâncias de Cluster de activação pós-falha** | Activação pós-falha Cluster instâncias (FCI), que requerem armazenamento partilhado, podem ser criados 2 formas diferentes.<br/><br/>1. um FCI no WSFC nó de dois a ser executada em Azure VMs com armazenamento suportado por uma solução clusters de terceiros. Para um exemplo específico que utiliza SIOS DataKeeper, consulte o artigo [elevada disponibilidade para uma partilha de ficheiros utilizando WSFC e software de terceiros 3º SIOS Datakeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).<br/><br/>2. uma FCI no WSFC nó de dois a ser executada em Azure VMs com iSCSI remoto destino partilhada armazenamento de bloqueio através do ExpressRoute. Por exemplo, NetApp privado armazenamento NPS () expõe um destino iSCSI através do ExpressRoute com Equinix para Azure VMs.<br/><br/>Para terceiros de armazenamento partilhado e soluções de replicação de dados, deverá contactar o fornecedor quaisquer problemas relacionados com o acesso a dados no activação pós-falha.<br/><br/>Tenha em atenção que utilizar FCI na parte superior de [armazenamento de ficheiros do Azure](https://azure.microsoft.com/services/storage/files/) não é suportada ainda, dado que esta solução não utilizam o armazenamento de Premium. Estamos a trabalhar para suportar esta funcionalidade mais rapidamente. |

## <a name="azure-only-disaster-recovery-solutions"></a>Só Azure: soluções de recuperação de falhas

Pode ter uma solução de recuperação de falhas para a sua bases de dados do SQL Server no Azure utilizar sempre no grupos de disponibilidade, base de dados espelhando as ou cópia de segurança e restaurar com blobs de armazenamento.

| Tecnologia de apoio                               | Arquitecturas de exemplo                    |
| ---------------------------------------- | ---------------------------------------- |
| **Sempre em grupos de disponibilidade**        | Réplicas disponibilidade executar através de vários centros de dados no Azure VMs para recuperação de falhas. Esta solução de publicação em região protege contra a indisponibilidade do site completo. <br/> ![Sempre em grupos de disponibilidade](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_alwayson.png)<br/>Dentro de uma região, todas as réplicas devem ser dentro de serviço em nuvem mesmo e o mesmo VNet. Uma vez que cada uma das regiões, terá uma VNet separada, estas soluções necessitam VNet VNet conectividade. Para mais informações, consulte o artigo [configurar um Site para o Site VPN no portal do Azure clássico](../vpn-gateway/vpn-gateway-site-to-site-create.md). |
| **Espelhando as bases de dados**                   | Capital e simétricas e servidores a executar o centros de dados diferentes para recuperação de falhas. Tem de implementar utilizando certificados de servidor, uma vez que um domínio do active directory não pode abranger vários centros de dados.<br/>![Espelhando as bases de dados](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_dbmirroring.gif) |
| **Cópia de segurança e restauro com o serviço de armazenamento de Blobs do Azure** | Bases de dados de produção cópia de segurança diretamente ao armazenamento blob num centro de dados diferentes para recuperação de falhas.<br/>![Cópia de segurança e restauro](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_backup_restore.gif)<br/>Para mais informações, consulte o artigo [cópia de segurança e restauro para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-backup-recovery.md). |

## <a name="hybrid-it-disaster-recovery-solutions"></a>Híbrido IT: Soluções de recuperação de falhas

Pode ter uma solução de recuperação de falhas para as bases de dados do SQL Server num ambiente híbrido-IT utilizando sempre no grupos de disponibilidade, espelhando as bases de dados, envio do registo e cópia de segurança e restaurar com o armazenamento de blogue Azure.

| Tecnologia de apoio                               | Arquiteturas de exemplo                    |
| ---------------------------------------- | ---------------------------------------- |
| **Sempre em grupos de disponibilidade**        | Algumas réplicas disponibilidade em execução no Azure VMs e outras réplicas em execução no local para recuperação de falhas de publicação em vários sites. O site de produção pode ser quer no local ou num centro de dados Azure.<br/>![Sempre em grupos de disponibilidade](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_alwayson.gif)<br/>Uma vez que todas as réplicas disponibilidade tem de estar no mesmo cluster WSFC, tem de WSFC cluster abranger ambas as redes (um cluster WSFC sub-rede com várias). Esta configuração requer uma ligação VPN entre Azure e à rede no local.<br/><br/>Recuperação de falhas bem sucedida das bases de dados, também deve instalar uma réplica do controlador de domínio no site de recuperação falhas.<br/><br/>É possível utilizar o Assistente de réplica adicionar no SSMS para adicionar uma réplica Azure a um sempre no disponibilidade grupo existente. Para obter mais informações, consulte o artigo Tutorial: estender o seu sempre no grupo de disponibilidade para Azure. |
| **Espelhando as bases de dados**                   | Um parceiro a ser executada numa VM Azure e a outros está em execução no local para recuperação de falhas de publicação em vários sites utilizando os certificados de servidor. Parceiros não necessitam de ser o mesmo domínio do Active Directory e, sem ligação VPN é necessária.<br/>![Espelhando as bases de dados](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_dbmirroring.gif)<br/>Outra base de dados espelhando as cenário envolve um parceiro a ser executada numa VM Azure e as outros em execução no local no mesmo domínio do Active Directory para recuperação de falhas de publicação em vários sites. É necessária uma [ligação VPN entre a rede virtual Azure e à rede no local](../vpn-gateway/vpn-gateway-site-to-site-create.md) .<br/><br/>Recuperação de falhas bem sucedida das bases de dados, também deve instalar uma réplica do controlador de domínio no site de recuperação falhas. |
| **Registo de envio**                         | Um servidor a ser executada numa VM Azure e a outros está em execução no local para recuperação de falhas de publicação em vários sites. Registo envio depende Windows Partilha de ficheiros, pelo que é necessária uma ligação VPN entre a rede virtual Azure e à rede no local.<br/>![Registo de envio](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_log_shipping.gif)<br/>Recuperação de falhas bem sucedida das bases de dados, também deve instalar uma réplica do controlador de domínio no site de recuperação falhas. |
| **Cópia de segurança e restauro com o serviço de armazenamento de Blobs do Azure** | No local cópia de segurança diretamente para o armazenamento de Blobs do Azure para recuperação de falhas bases de dados de produção.<br/>![Cópia de segurança e restauro](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_backup_restore.gif)<br/>Para mais informações, consulte o artigo [cópia de segurança e restauro para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-backup-recovery.md). |

## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Considerações importantes para SQL Server HADR no Azure

Azure VMs, armazenamento e o funcionamento em rede têm características operacionais diferentes de no local, não foi virtualizado infraestrutura de TI. Uma implementação com êxito de uma solução HADR SQL Server no Azure requer que compreender estes diferenças e estruturar a solução para acomodá-los.

### <a name="high-availability-nodes-in-an-availability-set"></a>Nós de elevada disponibilidade de um conjunto de disponibilidade

Conjuntos de disponibilidade no Azure permitem-lhe colocar os nós de elevada disponibilidade separada falhas domínios (FDs) e atualizar (UDs). Para Azure VMs seja colocado no mesmo conjunto de disponibilidade, tem de ser implementar no serviço de nuvem do mesmo. Apenas os nós no serviço na nuvem mesmo podem participar no mesmo conjunto de disponibilidade. Para mais informações, consulte o artigo [Gerir a disponibilidade de máquinas virtuais](virtual-machines-windows-manage-availability.md).

### <a name="wsfc-cluster-behavior-in-azure-networking"></a>Comportamento de cluster WSFC no Azure redes

O serviço DHCP não compatível com RFC no Azure pode causar a criação de determinadas configurações de cluster WSFC para falhar devido o nome de rede de cluster a ser atribuído um endereço IP duplicado, como o mesmo endereço IP como um de nós do cluster. Este é um problema quando implementar sempre no grupos de disponibilidade, que depende a funcionalidade WSFC.

Considere o cenário quando um cluster dois nós é criado e colocado online:

1. Cluster fica online e, em seguida, Nó1 os pedidos de um endereço IP atribuído dinamicamente para o nome da rede cluster.

2. Sem endereço IP que não seja o endereço IP do Nó1 é fornecido pelo serviço DHCP, uma vez que o serviço DHCP reconhece que o pedido provém Nó1 própria.

3. Windows Deteta que um endereço duplicado é atribuído para o Nó1 e para o nome de rede cluster e o grupo de cluster predefinido não consegue ficar online.

4. O grupo de cluster predefinido move o cursor para o Nó2, que trata o endereço IP do Nó1 como endereço IP do cluster e traz cluster grupo predefinido online.

5. Quando tenta Nó2 estabelecer a conectividade com Nó1, pacotes direccionadas Nó1 nunca deixe Nó2 porque-resolve o endereço IP do Nó1 a própria. Em seguida, Nó2 não é possível estabelecer a conectividade com Nó1 perder o quórum e encerra o cluster.

6. Entretanto, Nó1 pode enviar pacotes para Nó2, mas não pode responder Nó2. Nó1 perder o quórum e encerra o cluster.

Este cenário pode evitar-se ao atribuir um não utilizado endereço IP estático, tal como um endereço IP local de ligação como 169.254.1.1, para o nome de rede de cluster para trazer o nome da rede cluster online. Para simplificar a este processo, consulte o artigo [Configurar Cluster de activação pós-falha do Windows no Azure para sempre no disponibilidade grupos](http://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx).

Para mais informações, consulte o artigo [Configurar sempre no grupos de disponibilidade no Azure (interface gráfica do utilizador)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md).

### <a name="availability-group-listener-support"></a>Suporte de escuta do grupo de disponibilidade

Listeners de grupo de disponibilidade são suportadas no Azure VMs a executar o Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 e Windows Server 2016. Este suporte é tornado possível através da utilização de balanceamento de carga os pontos finais ativado no VMs Azure que sejam nós de grupo de disponibilidade. Tem de seguir passos de configuração especial para listeners trabalhar para ambas as aplicações de cliente que estão a executar no Azure, assim como as em execução no local.

Existem duas opções principais para configurar o seu escuta: externo (público) ou internos. A externa escuta (pública) utiliza um opostas Balanceador de carga da internet e está associada com um público Virtual IP (VIP) que esteja acessível através da internet. Uma escuta interna utiliza um balanceador de carga interno e suporta apenas a clientes dentro da mesma rede Virtual. Para um carregar o tipo de Balanceador, tem de ativar a devolver directo de servidor. 

Se o grupo de disponibilidade abrange várias sub-redes Azure (tal como uma implementação que cruza regiões Azure), a cadeia de ligação de cliente tem de incluir "**MultisubnetFailover = True**". Isto resulta em tentativas de ligação paralelas para as réplicas diferentes sub-redes. Para obter instruções sobre como configurar uma escuta, consulte o artigo

- [Configurar uma escuta ILB para sempre no grupos de disponibilidade no Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md).
- [Configurar uma escuta externa para sempre no grupos de disponibilidade no Azure](virtual-machines-windows-classic-ps-sql-ext-listener.md).

Pode ainda ligar para cada réplica disponibilidade separadamente ligando diretamente para a instância do serviço. Além disso, uma vez que sempre no grupos de disponibilidade são compatíveis com versões anteriores com espelhando as clientes da base de dados, pode ligar para as réplicas de disponibilidade como base de dados espelhando as parceiros, desde que as réplicas estão configuradas semelhante a base de dados espelhando as:

- Uma réplica principal e uma réplica secundária

- A réplica secundária está configurada como não legíveis (**Legíveis secundário** opção definida para **não**)

Uma cadeia de ligação de cliente de exemplo que corresponde a esta configuração de gosto espelhando as de base de dados utilizando o ADO.NET ou SQL Server Native Client é abaixo:

    Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

Para mais informações sobre a conectividade de cliente, consulte:

- [Utilizar palavras-chave cadeia de ligação com o SQL Server Native Client](https://msdn.microsoft.com/library/ms130822.aspx)
- [Ligar clientes a uma base de dados espelhando as sessões (SQL Server)](https://technet.microsoft.com/library/ms175484.aspx)
- [Ligar-se ao grupo de disponibilidade de escuta no híbrido IT](http://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
- [Grupo de disponibilidade Listeners, conectividade de cliente e mudança de recurso de aplicação (SQL Server)](https://technet.microsoft.com/library/hh213417.aspx)
- [Utilizar cadeias de ligação espelhando as bases de dados com grupos de disponibilidade](https://technet.microsoft.com/library/hh213417.aspx)

### <a name="network-latency-in-hybrid-it"></a>Latência da rede no híbrido IT

Deve implementar a sua solução HADR com partem do princípio de que podem ser períodos de tempo com uma latência alta de rede entre o seu rede no local e o Azure. Quando implementar réplicas Azure, deve utilizar consolidar assíncrona em vez de consolidação síncrona para o modo de sincronização. Quando implementar espelhando as servidores de base de dados no local tanto no Azure, utilize o modo de alto desempenho em vez do modo de segurança alta.

### <a name="geo-replication-support"></a>Suporte de geo replicação

A replicação geo no Azure discos não suporta o ficheiro de dados e o ficheiro de registo da mesma base de dados seja armazenada no discos separados. GRS replica alterações em cada disco de forma independente e assíncrono. Este mecanismo garante a ordem de escrita dentro de um único disco na cópia replicada geo, mas não através de cópias de replicadas geo de vários discos. Se configurar uma base de dados para armazenar o seu ficheiro de dados e o seu ficheiro de registo em discos separados, os discos recuperados após um falhas podem conter uma cópia mais recente do ficheiro de dados que o ficheiro de registo, quebras de registo de escrita automáticas no SQL Server e as propriedades ACID das transações. Se não tiver a opção para desativar a replicação geo na conta de armazenamento, o que devo manter todos os dados e ficheiros de registo para uma determinada base de dados no mesmo disco. Se tem de utilizar mais do que um disco devido o tamanho da base de dados, terá de implementar uma das soluções de recuperação falhas listadas acima para garantir a redundância de dados.

## <a name="next-steps"></a>Próximos passos

Se precisar de criar uma máquina de virtual Azure com o SQL Server, consulte o artigo [aprovisionamento uma máquina de Virtual do SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md).

Para obter o melhor desempenho do SQL Server em execução numa VM Azure, consulte as instruções fornecidas no [Desempenho melhores práticas para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-performance.md).

Para obter outros tópicos relacionados com a execução do SQL Server no Azure VMs, consulte o artigo [SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).

### <a name="other-resources"></a>Outros recursos

- [Instalar uma nova floresta do Active Directory no Azure](../active-directory/active-directory-new-forest-virtual-machine.md)
- [Criar sempre Cluster WSFC para grupos de disponibilidade na Azure VM](http://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)
